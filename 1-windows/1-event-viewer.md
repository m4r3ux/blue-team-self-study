O **Windows Event Viewer** é uma ferramenta nativa do Windows usada para **visualizar e analisar logs de eventos do sistema**.

Esses logs registram atividades importantes do computador, como:

- eventos de **aplicações**

- eventos de **segurança**

- eventos do **sistema**

- eventos de **instalação (setup)**


Eles são essenciais para **monitoramento, troubleshooting e investigação de incidentes**.

---

# Como acessar

Existem duas formas principais:

**Menu Iniciar**

Pesquisar por:

Event Viewer

**Atalho**

Win + R  
eventvwr.msc

---

# Estrutura do Event Viewer

Ao abrir o programa, veremos quatro seções principais no painel esquerdo:

- **Custom Views (Modos de Exibição Personalizados)**

- **Windows Logs (Logs do Windows)**

- **Applications and Services Logs (Logs de Aplicativos e Serviços)**

- **Subscriptions (Assinaturas)**


---

# Principais tipos de logs

Dentro de **Windows Logs**, os principais são:

**Application (Aplicativo)**  
Eventos gerados por programas instalados no sistema.

**Security (Segurança)**  
Eventos relacionados à segurança, como:

- logins

- falhas de autenticação

- alterações de contas

- privilégios


Esse é um dos logs mais importantes para **análise de segurança**.

**System (Sistema)**  
Eventos gerados pelo próprio sistema operacional.

**Setup (Instalação)**  
Eventos relacionados à instalação ou atualização do sistema.

---

# Analisando eventos

Os eventos aparecem em forma de **lista/tabela**.

Ao clicar em um evento, duas abas principais aparecem:

**General (Geral)**  
Mostra um resumo do evento, incluindo:

- nome do evento

- ID do evento

- nível

- data e hora

- descrição


**Details (Detalhes)**  
Exibe informações técnicas mais completas sobre o evento.

---

# Níveis de eventos

Os eventos possuem níveis que indicam sua importância:

**Information (Informação)**  
Indica operações normais ou bem-sucedidas.

**Warning (Aviso)**  
Aponta possíveis problemas que **ainda não afetam diretamente o sistema**.

**Error (Erro)**  
Indica falhas significativas que podem exigir atenção.

---

# Aplicando filtros

No painel direito é possível aplicar filtros para facilitar a análise dos logs.

Os filtros podem incluir:

- tipo de evento

- intervalo de datas

- nível de criticidade

- ID do evento

- fonte do evento


Isso ajuda a **encontrar eventos específicos rapidamente**.

---

# Criando filtros personalizados

Para criar um modo de visualização personalizado:

1. Clique com o botão direito em **Custom Views**

2. Selecione **Create Custom View**

3. Defina os critérios desejados, como:


- Event ID

- nível do evento

- fonte do log

- intervalo de tempo


Isso permite monitorar **eventos específicos com mais facilidade**.

---

# Exportando logs

Para salvar logs do sistema:

1. Clique com o botão direito em um log

2. Selecione **Save All Events As**

3. Escolha o formato desejado


Os logs podem ser exportados para **análise posterior ou investigação forense**.

---

## Se aprofundando nos eventos

### Event ID 4624
Significa que um login foi realizado com sucesso, pode ser:
- login no computador
- login via RDP
- login via reed
- execução de serviços

Campos importantes a se observar:
- Account Name: usuário que fez login
- Logon Type: tipo de login
- Source Network Address: IP de origem
- Logon ID: sessão

### Event ID 4625
Evento de tentativa de login falho

É importante pra detectar:
- brute force
- credenciais incorretas
- ataques de senha

É bom olhar:
- Account Name: usuário alvo
- Source Network Address: ip do atacante
- Failure Reason: motivo da falha

### Event ID 4688
Indica a criação de um processo

Muito usado para detectar:
- execução de malware
- PowerShell suspeito
- ferramentas ofensivas

Campos importantes:
- New Process Name: processo iniciado
- Creator Process: processo pai
- Command Line: comando executado
- Account Name: usuario	

Exemplo suspeito:
**powershell.exe -enc <base66**

Isso pode indicar ataque fileless

### Event ID 4672
Indica que um usuário recebeu privilégios especiais.

Normalmente aparece quando: 
- administrador faz login
- SYSTEM inicia processos

Campos importantes:
- Account Name: usuário
- Privileges: privilégios concedidos

Pode indicar:
- login administrativo
- escalada de privilégio

### Event ID 4720
Indica que uma conta foi criada.

Muito importante pra detectar persistência

Campos importantes:
- New Account Name: usuário criado
- Creator Account: quem criou

Exemplo suspeito - um invasor cria um usuário chamado:
**backup_admin**

## Tipos de Logon (LogonType)
| LogonType | Tipo                 |
| --------- | -------------------- |
| 2         | login local          |
| 3         | login via rede       |
| 4         | batch                |
| 5         | serviço              |
| 7         | unlock workstation   |
| 8         | network cleartext    |
| 10        | Remote Desktop (RDP) |
| 11        | cached login         |
Alguns dos tipos de logon muito investigados:

### LogonType 10
Login via RDP

Pode indicar: 
- administração remota
- invasão remota

### LogonType 3
Login via rede

Exemplo:
- acesso a compartilhamentos
- lateral movement

---

## Campos importantes nos eventos

### SID
Security Identifier

Identifica **unicamente um usuário**.

### Source IP
IP que originou a conexão

Muito usado para detectar:
- ataques externos
- lateral movement

### Account Name
Usuário que realizou a ação

### Logon ID
Identifica sessões específicas

Permite correlacionar eventos

4624 login
4688 processo

Ambos podem ter o mesmo Logon ID

Isso mostra o que o usuário fez após logar.

