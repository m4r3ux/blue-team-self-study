Os Windows Event IDs são registros gerados pelo sistema operacional para descrever ações que aconteceram.

Mas o erro, é achar que:

- 4624 = login OK - acabou

Um analista de SOC pensa assim:

- 4624 aconteceu... mas quem logou, de onde, como e por quê?

---

## Estrutura mental de qualquer log do Windows

Antes de decorar eventos, precisamos entender que todo log responde 5 perguntas:
- Quem -> usuário / conta
- De onde -> IP / hsotname / máquina
- Em qual sistema -> host alvo
- O que aconteceu -> ação (login, criação, execução)
- Quando -> timestamp

Se soubermos extrair isso, não dependemos de tabela.

## Eventos comuns

### Event ID 4624 - login bem-sucedido

Significa que um login foi aceito pelo sistema, mas nem todo 4624 é igual.

Campo mais importante: 
- Logon Type

Principais:
- 2 -> login interativo: teclado
- 3 -> login via rede (SMB, acesso remoto interno)
- 10 -> RDP (remote desktop)
- 5 -> serviço

A mentalidade alvo, é ver esse tipo de log ID 4624 e pensar:
- Esse login faz sentido?
- Esse tipo de logon condiz com o comportamento esperado? - é um host comumente acessado via remote desktop ou via login interativo?
- O IP é interno ou externo?
- O usuário costuma logar aqui?

Exemplo real:
```
Event ID: 4624
User: admin
Logon Type: 10
Source IP: 185.x.x.x
```

Isso aqui grita: RDP vindo da internet -> possível invasão.

![[Pasted image 20260403090834.png]]

---

### Event ID 4625 - logon falho
Isso significa que houve uma tentativa de login que falhou .

Campo muito importante é o 
- Failure Reason / Status Code

Exemplos:
- `0xC000006A` → senha errada
- `0xC0000064` → usuário não existe

A mentalidade pra esse tipo de log, é:
- Muitos 4625 seguidos -> brute force
- Vários usuários diferentes -> password spraying
- Origem externa -> ataque

```
4625
User: administrator
Source IP: 203.x.x.x
Tentativas: 50 em 2 minutos
```

Ataque claro de força bruta.

---

### Event ID 4688 - processo criado
Significa que um processo foi executado, campos mais importantes:
- New Process Name -> o que foi executado
- Parent Process -> quem chamou
- Command Line -> COMO foi executado

Aqui, você não olha só o processo, você pensa:
- Esse processo faz sentido vindo desse pai?

Exemplo:
```
Process: powershell.exe
Parent: winword.exe
```

Isso é muito suspeito, um word não deveria abrir um powershell.

---

### ID 4698 - nova tarefa agendada criada
Aqui, significa que alguém ou algo criou uma tarefa automática no sistema, que é executada automaticamente em X tempo.

Uso comum disso por atacantes, é
- Persistência

Nesse caso, temos que tomar cuidado com:
- Quem criou?
- Qual comando roda?
- Frequência?

Exemplo: 
```
Task: UpdateCheck
Action: powershell -enc <base64>
```

Clássico de malware mantendo a persistência e ofuscando comandos.

Para ver na prática, aqui, eu agendei uma tarefa com o nome de Teste 123 que executaria o cmd.exe  

![[Pasted image 20260403091726.png]]
![[Pasted image 20260403092403.png]]

No log podemos ver quem originou essa tarefa agendada, data de execução, o que a pessoa que executou usou para logar (LogonType), algumas configurações personalizadas e o pricipal: comando a ser executado.

Também fui observar o id de processo criado, para observar como comporta, e vêmos que quem origina o processo agendado executado, é o svchost.exe - pesquisei e percebi que o Task Scheduler, roda dentro do svchost.exe, então atenção.

![[Pasted image 20260403091946.png]]

---

### ID 4720 - usuário criado
Significa que um novo usuário foi criado dentro do host, a mentalidade aqui é saber:
- Quem criou?
- É padrão da empresa?
- Foi fora de horário?

Exemplo:
```
User created: backup_admin
Created by: SYSTEM
```

Possível persistência / escalonamento.

---

### Event ID 4732 - usuário adicionado a grupo privilegiado
Significa que um usuário foi adicionado a um grupo que tem privilégios, como o grupo Administrators.

Aqui, devemos prestar atenção em:
- Qual grupo?
- Quem adicionou?
- Isso deveria acontecer?

Exemplo:
```
User: john
Added to: Domain Admins
```

Isso é crítico.

---

O core de analisar eventos do windows, é não ler eventos isolados apenas, é conectar:

```
4625 (falhas)
→ 4624 (sucesso)
→ 4688 (powershell)
→ 4698 (persistência)
→ 4732 (privilégio)
```

Isso é cadeia de ataque real.

Resumo, pra gravar:
- 4624 - é login ok (analisar tipo + origem)
- 4625 - login falho (volume = ataque)
- 4688 - execução (pai + comando = chave)
- 4698 - persistência
- 4720 - novo usuário
- 4732 - privilégio

Exercício:
```
4625 (20x)
4624 (Logon Type 3)
4688 (cmd.exe)
4688 (net user hacker /add)
4732 (hacker → Administrators)
```

Basicamente houve uma tentativa de brute-force (4625) seguida de um login bem-sucedido (4624) - após conseguir acesso, um processo cmd foi criado, que executou outro processo com o comando `net user hacker /add` adicionando persistência ao ambiente ou host (4688), após isso, ele faz o escalonamento de privilégios se adicionando ao grupo Administrators (4732), assim, um ataque que começou com um brute force termina em um hacker com acesso administrativo ao ambiente.

