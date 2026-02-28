# Linux - Linha de Comando Prática para SOC

## 1. Navegação no Sistema de Arquivos

### pwd — Onde estou?

pwd

Mostra o diretório atual.

Importante para evitar executar comandos no local errado durante uma investigação.

### ls — Listar arquivos

ls
ls -l
ls -la

- -l → mostra permissões, dono, tamanho, data
- -a → mostra arquivos ocultos (iniciados com .)

Uso em SOC:
- Identificar arquivos suspeitos
- Ver permissões incorretas
- Localizar scripts maliciosos

### cd — Mudar de diretório

cd /var/log
cd ..
cd ~

- .. → volta um diretório
- ~ → diretório home

Saber navegar rápido é fundamental para investigação eficiente.

---

## 2. Estrutura Importante do Sistema

Diretórios críticos para segurança:

- /var/log      → Logs
- /etc          → Configurações do sistema
- /home         → Diretórios de usuários
- /root         → Diretório do root
- /tmp          → Arquivos temporários (muito usado por malware)
- /bin /usr/bin → Binários executáveis

Em SOC, sempre suspeite de:
- Scripts estranhos em /tmp
- Binários desconhecidos em /usr/bin
- Alterações suspeitas em /etc

---

## 3. Permissões de Arquivo

### ls -l

Exemplo:

-rwxr-xr-- 1 root root 1234 Jan 10 script.sh

Estrutura:

- Primeiro caractere: tipo (- arquivo, d diretório)
- Próximos 3: permissões do dono
- Próximos 3: permissões do grupo
- Últimos 3: permissões de outros

r = read  
w = write  
x = execute  

Exemplo:
rwxr-xr-- significa:

- Dono: leitura, escrita, execução
- Grupo: leitura, execução
- Outros: leitura

Permissão 777 é extremamente perigosa.

### chmod — Alterar permissões

chmod 755 script.sh
chmod +x script.sh

Uso em SOC:
- Detectar arquivos executáveis suspeitos
- Verificar permissões excessivas
- Identificar possíveis backdoors

---

## 4. Processos

### ps — Listar processos

ps aux

Mostra:
- Usuário
- PID
- Uso de CPU
- Uso de memória
- Comando executado

Uso em SOC:
- Identificar processos desconhecidos
- Ver malware rodando como root
- Ver comandos suspeitos

### Filtrar processo específico

ps aux | grep ssh
ps aux | grep python

---

## 5. top — Monitoramento em Tempo Real

top

Mostra:
- Processos em execução
- Consumo de CPU
- Consumo de memória
- Load average

Uso em SOC:
- Detectar mineração de criptomoeda
- Identificar uso anormal de CPU
- Ver processos suspeitos ativos

Para sair: q

---

## 6. netstat e ss — Conexões de Rede

### netstat (legado, mas comum)

netstat -tulnp

Mostra:
- Portas abertas
- Protocolos
- Processo associado
- PID

Flags:
- t → TCP
- u → UDP
- l → Listening
- n → Não resolver DNS
- p → Mostrar PID

Uso em SOC:
- Identificar portas abertas inesperadas
- Detectar backdoors escutando
- Ver conexões externas suspeitas

### ss (moderno e recomendado)

ss -tulnp
ss -antp

Mais rápido que netstat.
Substituto moderno.

---

## 7. Identificando Processo por Porta

Descobrir quem está usando a porta 4444:

ss -tulnp | grep 4444

Ou:

lsof -i :4444

Uso em SOC:
- Detectar reverse shells
- Identificar serviços não autorizados
- Ver conexões externas ativas

---

## 8. Fluxo de Investigação Básico

Se suspeitar de comprometimento:

1. Ver processos ativos:
   ps aux

2. Monitorar consumo:
   top

3. Ver conexões de rede:
   ss -antp

4. Ver portas escutando:
   ss -tulnp

5. Ver arquivos suspeitos em /tmp:
   ls -la /tmp

Esse fluxo cobre investigação inicial em servidor Linux.

---

## 9. Conceitos que Devem Estar Consolidados

- Navegar rapidamente pelo sistema
- Interpretar permissões corretamente
- Identificar processos suspeitos
- Entender relação PID ↔ Porta
- Diferenciar conexão ativa de porta em listening
- Reconhecer uso anormal de CPU/memória

Linha de comando em Linux não é sobre decorar comandos.
É sobre entender o que observar quando algo parece anormal.