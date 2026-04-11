O nosso objetivo aqui, é conseguir:
- Ler um log DNS - entender o que foi resolvido
- Ler HTTP - entender o que foi feito
- Ler e-mail - identificar fraude

---

## DNS (Domain Name System)

O que é 
- Traduz domínio - IP

Fluxo básico:
`google.com → 142.250.190.78`

|Tipo|Função|
|---|---|
|A|domínio → IPv4|
|AAAA|domínio → IPv6|
|MX|servidor de e-mail|
|TXT|validações (SPF, etc.)|
|CNAME|alias de domínio|
Exemplo real:
```
Query: example.com
Type: A
Response: 93.184.216.34
TTL: 3600
```

O que cada campo diz:
- Query - o que foi pedido
- Type - tipo de dado
- Response - resposta
- TTL - tempo de cache

### Detecção (SOC)

Domínio suspeito:
`ajd92jd92jd.com`

Pode ser:
- DGA (malware)
- C2

DNS tunneling:
`asdasd.asdasd.asdasd.malicious.com`

Dados escondidos no domínio.

---

## HTTP / HTTPS

O que é:
- Protocolo da web

Métodos principais:

|Método|Função|
|---|---|
|GET|buscar dados|
|POST|enviar dados|
|PUT|atualizar|
|DELETE|deletar|

Exemplo:
```
GET /login HTTP/1.1
Host: site.com
```

Status codes:

| Código | Significado    |
| ------ | -------------- |
| 200    | OK             |
| 403    | proibido       |
| 404    | não encontrado |
| 500    | erro servidor  |

---

## Headers

Exemplo:
```
User-Agent: Mozilla/5.0
Host: example.com
```

### Detecção (SOC)

Exfiltração:

`POST /upload`

- envio de dados

Download de malware:

`GET /payload.exe`

User-agent suspeito:

`User-Agent: curl/7.68.0`

- script automatizado

---

## SMTP (Email)

O que é:
- protocolo de envio de e-mails

### Campos importantes

Exemplo simplificado:

```
From: banco@seguro.com
Reply-To: hacker@gmail.com
```

É red flag por que o "from" é diferente do "reply-to"

Clássico de phishing.

### Autenticação

- SPF (Sender Policy Framework) - verifica se o servidor pode enviar email pelo domínio
- DKIM (DomainKeys Identified Mail) - assinatura criptográfica do email
- DMARC - política que valida SPF + DKIM

Exemplo:
```
SPF: pass
DKIM: pass
DMARC: fail
```

Algo não bate - possível spoofing

Exemplo de phishing:
```
From: banco@seguro.com
Reply-To: hacker@gmail.com
SPF: fail
```

Resumo SOC

|Protocolo|O que revela|
|---|---|
|DNS|para onde quer ir|
|HTTP|o que está fazendo|
|SMTP|quem enviou e se é legítimo|
```
DNS → resolve malware.com
HTTP → GET payload.exe
SMTP → phishing inicial
```

---

## Exercícios:

### ❓ Pergunta 1

```
Query: google.com  
Type: A
```

O que está sendo pedido?
R: Ele está solicitando pelo endereço IP do domínio google.com, a fim de realizar uma comunicação TCP/IP e consequentemente realizar uma requisição HTTP para interagir com a web via navegador.

---

### ❓ Pergunta 2

```
POST /upload
```

O que isso indica?
R: Pode indicar várias coisas - dependendo do host que está recebendo essa requisição.

Se for um host legítimo, como facebook, pode ser um simples upload de imagem ou coisa do tipo

Se for um host com domínio e/ou IP suspeito, pode ser tentativa de exfiltração de dados, comunicação com C2 e outros.

Depende do caso e da situação.

---

### ❓ Pergunta 3

```
Status: 404
```

O que significa?
R: Página não encontrada, o usuário tentou acessar uma página que foi movida ou não existe.

Pode ser várias coisas também, um usuário tentando acessar uma página que não existe, link quebrado, ou até mesmo enumeração de páginas.

---

### ❓ Pergunta 4

```
From: suporte@banco.com  
Reply-To: hacker@gmail.com
```

Isso é suspeito? Por quê?
R: Mesmo o e-mail indicando que é "From: suporte@banco.com", o "Reply-to: hacker@gmail.com" diferente do From, indica que provavelmente essa pessoa não tem acesso ao @banco.com, pode ter falsificado header. É suspeito mas para nos aprofundar, poderiamos consultar registros SPF, DKIM, DMARC afim de tirar demais conclusões.

---

### ❓ Pergunta 5 (correlação)

```
DNS: malicious.com  
HTTP: GET /file.exe
```

Descreva o que está acontecendo.
R: Request DNS seguida de download de arquivo. Basicamente o que aconteceu exatamente foi:

- pessoa acessou domínio malicious.com, que fez uma request do endereço de IP
- conexão TCP/IP
- http request
- download do malware

Pode ser:
- Pessoa baixando malware não intencionalmente via phishing ou web
- Pode ser computador invadido criando persistência ou infecção do host ou rede
- Entre outros.
