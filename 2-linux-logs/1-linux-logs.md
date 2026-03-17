Objetivos - aprender a:
- ler logs de sistema linux
- investigar tentativas de login
- detectar uso de sudo
- analisar ssh logs
- identificar ataques de brute force

---

## Principais logs Linux
Os principais e mais importantes logs ficam em
**/var/log**

Os dois que vamos focar são:

**/var/log/auth.log** - logs de autenticação
**/var/log/syslog** - eventos gerais do sistema

---

## Estrutura de um log Linux
**Mar 16 20:14:02 ubuntu sshd[1042]: Failed password for root from 192.168.1.22 port 52144 ssh2**

|Campo|Significado|
|---|---|
|Mar 16 20:14:02|data e hora|
|ubuntu|hostname|
|sshd|serviço|
|1042|PID do processo|
|Failed password...|evento|

---

## Logs de autenticação
Arquivo: **/var/log/auth.log**

Contém eventos de:
- login
- ssh
- sudo
- falhas de autenticação
- mudança de usuários

## Eventos de login

### Login bem sucedido

Por exemplo: 
**Accepted password for luciano from 192.168.1.50 port 55122 ssh2**

| Campo             | Explicação         |
| ----------------- | ------------------ |
| Accepted password | login bem sucedido |
| luciano           | usuário            |
| 192.168.1.50      | IP origem          |
| port 55122        | porta origem       |
| ssh2              | protocolo          |
### Login falhado
Por exemplo:
**Failed password for root from 192.168.1.100 port 42112 ssh2**

Significa que:
- alguém tentou acessar
- senha errada

Bem comum em ataques automatizados

## Detectando brute force
Exemplo de logs:
**Failed password for root from 185.234.218.22**
**Failed password for root from 185.234.218.22**
**Failed password for root from 185.234.218.22**
**Failed password for root from 185.234.218.22**

Isso indica:
- ataque de força bruta via SSH

---

## Logs de sudo
Quando um usuário usa sudo:

Exemplo:
**luciano : TTY=pts/0 ; PWD=/home/luciano ; USER=root ; COMMAND=/usr/bin/apt update**

Campos importantes:

|Campo|Significado|
|---|---|
|luciano|usuário|
|TTY|terminal|
|PWD|diretório|
|USER=root|privilégio|
|COMMAND|comando executado|
Isso mostra **escalada de privilégio**.

---
## Logs de SSH

Os logs de SSH também aparecem em 
**/var/log/auth.log**

Eventos comuns:

conexão iniciada 
**Connection from 192.168.1.55 port 52231**

autenticação bem sucedida
**Accepted password for user**

autenticação falhou
**Failed password**

---

## Logs do sistema
Arquivo:
**/var/log/syslog**

Contém eventos de:
- serviços
- kernel
- erros
- inicialização de programas

Exemplo:
**systemd[1]: Started OpenSSH server**

---

## Comandos essenciais para análise

Ver log inteiro:
**cat /var/log/auth.log**

---

Navegar melhor:
**less /var/log/auth.log**

Teclas úteis:
q → sair
/ → pesquisar
n → próximo resultado

---

### Filtrar eventos

Logins SSH:
**grep ssh /var/log/auth.log**

Falhas de login:
**grep "Failed password" /var/log/auth.log**

logins bem sucedidos:
**grep "Accepted" /var/log/auth.log**

uso de sudo:
**grep sudo /var/log/auth.log**



