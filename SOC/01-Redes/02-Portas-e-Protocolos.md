O objetivo, é olharmos um log e pensar:
- Que serviço é esse, isso é normal e como isso pode ser explorado?

Uma porta é um identificador lógico de um serviço dentro de um host.

Um endereço IP pode ter vários serviços rodando:
```
192.168.1.10:22   → SSH
192.168.1.10:80   → HTTP
192.168.1.10:3389 → RDP
```

## Tipos de portas
|Tipo|Range|
|---|---|
|Well-known|0–1023|
|Registered|1024–49151|
|Ephemeral|49152–65535|

## Portas importantes

### 22 - SSH (Secure Shell)

O que faz:
- Acesso remoto seguro a servidores e hosts.

Uso normal:
`ssh user@server`

Abuso:
- Brute force
- credenciais vazadas
- acesso inicial

Em logs:
`Failed password for root from 185.x.x.x`

### 23 - Telnet (inseguro)

O que faz:
- acesso remoto (sem criptografia)
- senha em texto plano

### 25 - SMTP

O que faz: 
- envio de e-mails

Abuso:
- Spam 
- phishing
- relay aberto

## 53 - DNS

O que faz:
- Resolve domínio -> IP

Abuso:
- DNS tunneling -> comunicação com c2, exfiltração de dados, bypass de restrições de rede
- DGA malware -> hosts gerados automaticamente, pra evitar blacklists, etc.

Exemplo:
`Query: ajd92jd92jd.com`

### 80 - HTTP

O que faz:
- web sem criptografia

Abuso:
- SQL Injectionm
- XSS
- Download de malware

### 443 - HTTPS

O que faz:
- web criptografada

Abuso:
- C2 escondido
- Exfiltração criptografada

SOC:
- HTTPS é diferente de seguro, só está criptografado

### 445 - SMB 

O que faz:
- Compartilhamento de arquivos Windows

Ataques:
- EternalBlue
- Lateral movement
- Pass-the-Hash

### 3389 - RDP

O que faz:
- Acesso remoto gráfico

Abuso:
- brute force
- acesso direto ao desktop

Log:
`Event ID 4624 Logon Type 10`

|Porta|Serviço|Risco|
|---|---|---|
|22|SSH|brute force|
|23|Telnet|credenciais em claro|
|25|SMTP|phishing|
|53|DNS|tunneling|
|80|HTTP|exploração web|
|443|HTTPS|C2 oculto|
|445|SMB|lateral movement|
|3389|RDP|acesso remoto|
|8080|Web alt|serviços expostos|

---

## Exercícios:

### ❓ Pergunta 1

```
Port: 22  
Source IP: 185.x.x.x  
Múltiplas tentativas de login
```

O que está acontecendo?
R: Tentativa de brute force via  SSH 

---

### ❓ Pergunta 2

```
Port: 445  
Source IP: 10.0.0.5  
Destination IP: 10.0.0.8
```

Isso pode indicar o quê?
R: Movimentação lateral via SMB

---

### ❓ Pergunta 3

```
Port: 53  
Query: ajd92jd92jd.com
```

Por que isso é suspeito?
R: Domínio suspeito, pode ser DNS tunneling, phishing, exfiltração ou comunicação com C2.

---

### ❓ Pergunta 4

```
Port: 3389  
Login sucesso  
IP externo
```

Qual o risco?
R: Dispositivo externo acessando host interno via RDP - pode ser indicação de incidente que já aconteceu ou pode ser um acesso normal, depende do histórico e contexto do host.

---

### ❓ Pergunta 5 (correlação real)

```
Port: 80  
Download de arquivo  
Depois:  
Port: 443  
Conexão contínua
```

Descreva o ataque.

R: Especulação, uma pessoa pode ter recebido um e-mail de phishing ou simplesmente ter encontrado um produto / serviço ou mensagem suspeita na internet. Realizou o download de um arquivo malicioso que depois de executado, realizou a conexão via HTTPS (para evitar suspeitas) com o C2.