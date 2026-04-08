No Windows, existem várias maneiras pelas quais um logon pode ocorrer localmente e remotamente. Os administradores do sistema precisam acompanhar os tipos de logon para estar a par de quaisquer vulnerabilidades de segurança na rede da organização.

Lista de tipo de logons encontrados no Windows:

## Logon Type 2 - Interactive
Ocorre quando o usuário faz logon no computador, com o teclado, por exemplo.

![[Pasted image 20260403114026.png]]

---

## Logon Type 3 - Network
Esse tipo de logon ocorre quando um usuário ou computador faz logon no computador a partir da rede.

---

## Logon Type 4 - Batch
Esse tipo de logon é usado por servidores batch (ou em lote) - tarefas agendadas são executadas sem nenhuma intervenção humana

---

## Logon Type 5 - Service
Esse tipo de logon é usado por serviços e contas de serviço para rodar um serviço.

---

## Logon Type 7 - Unlock
Esse tipo de logon ocorre quando o usuário desbloqueia sua máquina, por exemplo, quando da um Ctrl + L e bloqueia, e depois volta a máquina e a desbloqueia.

---

## Logon Type 8 - Network Cleartext
Esse tipo de logon acontece quando um usuário ou computador faz loga a partir da rede, e a senha é enviada em texto limpo.

---

## Logon Type 9 - NewCredentials
Esse tipo de logon acontece quando um usuário roda um "RunAs" no terminal para rodar uma aplicação

---

## Logon Type 10 - RemoInteractive
Esse tipo de logon acontece quando um usuário acessa a máquina remotamente a partir do protocolo RDP, usando por exemplo o Remote Desktop, Remote Assistance ou serviços de terminal.

---

## Logon Type 11 - CachedInteractive
Esse tipo de logon acontece quando o usuário faz login sem ter que ter contato com o controlador do domínio, visando que ele tem as credenciais salvas localmente em seu computador.

É como um "lembrar destas credenciais".

