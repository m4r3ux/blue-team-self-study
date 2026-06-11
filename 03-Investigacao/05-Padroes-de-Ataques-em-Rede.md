# Padrões de Ataque em Rede

## Como um Analista SOC Reconhece Comportamento Malicioso em PCAPs

Até agora você estudou bastante endpoint:

- Processos

- PowerShell

- LOLBins

- Persistência

- Phishing


Agora começamos a entrar em outro mundo:

> comportamento de rede.

Muitas vezes você não terá:

- hash

- malware

- alerta de antivírus


Você terá apenas:

```text
IPs
Portas
Pacotes
Conexões
```

E precisará responder:

> "Isso é comportamento normal ou atividade de atacante?"

Esse é exatamente o objetivo desta aula.

---

# O que você deve aprender

Ao terminar este tema você deve conseguir:

- Reconhecer port scanning

- Reconhecer beaconing

- Reconhecer C2 (Command & Control)

- Reconhecer DNS tunneling

- Abrir um PCAP e formular hipóteses sozinho

- Explicar tecnicamente por que um comportamento é suspeito


---

# Antes de tudo: Mentalidade de Análise

O erro mais comum de iniciantes é procurar:

```text
IP malicioso
Hash malicioso
Domínio malicioso
```

Analistas mais experientes procuram:

```text
Comportamento
```

Porque:

- IP muda

- domínio muda

- malware muda


Mas comportamento geralmente permanece parecido.

---

# O que é um PCAP?

PCAP significa:

```text
Packet Capture
```

É uma captura de tráfego de rede.

Pense nele como:

> um arquivo contendo tudo que trafegou pela rede durante determinado período.

---

No Wireshark você verá:

```text
Origem
Destino
Protocolo
Porta
Payload
Tamanho
Tempo
```

---

# O que estamos procurando?

Existem padrões clássicos.

Os mais importantes para SOC L1 são:

1. Port Scan

2. Beaconing

3. C2 HTTP

4. DNS Tunneling


---

# 1. Port Scan

## O que é?

Antes de invadir um sistema, o atacante precisa descobrir:

- quais portas estão abertas

- quais serviços existem

- onde vale a pena atacar


Isso é reconhecimento.

MITRE ATT&CK:

```text
T1046
Network Service Discovery
```

---

# Como funciona?

Imagine um atacante testando:

```text
22
80
443
445
3389
8080
```

na mesma máquina.

---

Visualmente:

```text
10.10.10.50 → 192.168.1.20:22
10.10.10.50 → 192.168.1.20:80
10.10.10.50 → 192.168.1.20:443
10.10.10.50 → 192.168.1.20:445
10.10.10.50 → 192.168.1.20:3389
```

---

# O padrão que interessa

Não é apenas:

```text
Muitas conexões
```

É:

```text
Muitas conexões
+
muitas portas diferentes
+
curto intervalo de tempo
```

---

# Como aparece no Wireshark?

Você pode notar:

```text
SYN
SYN
SYN
SYN
SYN
SYN
```

para dezenas de portas.

---

Exemplo:

```text
10.0.0.5 → 172.16.1.10:21
10.0.0.5 → 172.16.1.10:22
10.0.0.5 → 172.16.1.10:23
10.0.0.5 → 172.16.1.10:25
10.0.0.5 → 172.16.1.10:80
```

em poucos segundos.

---

# O que pensar?

Pergunta mental:

> Um usuário normal acessaria 30 portas diferentes em 5 segundos?

Normalmente não.

---

# Evidência de Port Scan

Exemplo:

> Host 10.0.0.5 realizou conexões SYN para múltiplas portas (21, 22, 23, 25, 80, 443 e outras) do mesmo destino em curto intervalo de tempo, comportamento compatível com atividade de reconhecimento de serviços.

---

# 2. Beaconing

Agora chegamos em um dos conceitos mais importantes do SOC.

---

# O que é Beaconing?

Beaconing ocorre quando um malware "liga para casa".

Ou seja:

```text
Host infectado
↓
Servidor atacante
↓
Host infectado
↓
Servidor atacante
↓
Host infectado
```

em intervalos regulares.

---

# Por que fazer isso?

O malware quer:

- receber comandos

- enviar status

- informar que está ativo


---

# Exemplo

A cada 60 segundos:

```text
10:00:00
10:01:00
10:02:00
10:03:00
10:04:00
```

o mesmo host conversa com o mesmo IP.

---

# O que chama atenção?

A regularidade.

Humanos não funcionam assim.

---

Usuário acessando um site:

```text
10:00
10:01
10:05
10:08
10:20
```

Padrão irregular.

---

Malware:

```text
60s
60s
60s
60s
60s
```

Padrão extremamente previsível.

---

# O que procurar?

Mesmo:

- IP

- URI

- tamanho


repetidamente.

---

Exemplo:

```text
GET /checkin
```

a cada minuto.

---

# Evidência

> Host interno realizou comunicação periódica com o IP 45.x.x.x em intervalos aproximados de 60 segundos, apresentando padrão consistente de beaconing compatível com comunicação C2.

---

# 3. C2 HTTP

Agora vamos aprofundar.

Beaconing normalmente faz parte de algo maior:

```text
Command & Control
```

(C2)

---

# O que é C2?

Depois de comprometer a máquina:

o atacante precisa controlá-la.

---

Fluxo:

```text
Malware
↓
Servidor atacante
↓
Recebe comando
↓
Executa
↓
Retorna resultado
```

---

# Como esconder isso?

Usando HTTP.

Porque HTTP existe em toda empresa.

---

# Características comuns

## User-Agent estranho

Normal:

```text
Mozilla/5.0
Chrome
Edge
Firefox
```

---

Suspeito:

```text
abc123
WindowsUpdate
Client
Agent
```

---

# URI aleatória

Normal:

```text
/login
/images
/products
```

---

Suspeito:

```text
/asd89asd/
/k23ja8s/
/x91bb2/
```

---

# Tráfego pequeno

Muitos C2 enviam:

```text
50 bytes
80 bytes
100 bytes
```

porque só estão trocando comandos.

---

# O padrão

```text
Poucos bytes
+
Regularidade
+
URI estranha
+
User-Agent estranho
```

---

# Evidência

> Comunicação HTTP recorrente observada para IP externo utilizando URI aleatória e User-Agent não associado a navegadores comuns, com troca reduzida de dados, compatível com atividade de Command and Control.

---

# 4. DNS Tunneling

Esse costuma ser mais difícil.

---

# Primeiro:

O que DNS deveria fazer?

Normalmente:

```text
google.com
```

↓

```text
142.250.x.x
```

---

Consulta rápida.

Resposta rápida.

Fim.

---

# O que o atacante faz?

Usa DNS como canal de comunicação.

---

# Ideia

Em vez de enviar:

```text
senha=123
```

via HTTP

ele envia dentro da consulta DNS.

---

Exemplo

Normal:

```text
google.com
```

---

Suspeito:

```text
ajd89asjd98asj98dasj98d.example.com
```

---

# O que chama atenção?

Subdomínios enormes.

---

Normal:

```text
mail.google.com
```

---

Suspeito:

```text
ajshd8723hd72hd72hd72hd72hd72h.example.com
```

---

# Outro sinal

Alta frequência.

---

Exemplo:

```text
100
200
300
500
```

consultas por minuto.

---

# O mesmo domínio

```text
*.evil.com
```

sendo consultado sem parar.

---

# Como identificar?

Perguntas:

1. As consultas são muito longas?

2. Há muitas consultas?

3. Todas vão para o mesmo domínio?

4. Os nomes parecem aleatórios?


---

# Evidência

> Observadas consultas DNS frequentes para múltiplos subdomínios aleatórios e extensos associados ao domínio example.com, comportamento compatível com DNS tunneling para exfiltração ou comunicação C2.

---

# Processo Mental para Analisar um PCAP

Quando abrir um PCAP desconhecido:

---

## Passo 1

Olhar estatísticas gerais.

No Wireshark:

```text
Statistics → Conversations
```

---

Pergunta:

```text
Quem fala mais?
```

---

## Passo 2

Identificar hosts internos.

Pergunta:

```text
Quem iniciou as conexões?
```

---

## Passo 3

Ver protocolos.

Muito:

- HTTP?

- DNS?

- SMB?

- RDP?


---

## Passo 4

Buscar repetição.

Pergunta:

```text
Existe periodicidade?
```

---

## Passo 5

Buscar anomalias.

Pergunta:

```text
O comportamento parece humano?
```

---

# Checklist Mental de SOC

Ao abrir um PCAP:

### Port Scan

- Muitas portas?

- Mesmo destino?

- Muitos SYN?

- Curto período?


---

### Beaconing

- Intervalos regulares?

- Mesmo IP?

- Mesmo tamanho?


---

### C2

- User-Agent estranho?

- URI aleatória?

- Poucos bytes?

- Comunicação recorrente?


---

### DNS Tunneling

- Queries longas?

- Alta frequência?

- Subdomínios aleatórios?

- Mesmo domínio repetidamente?


---

# Resultado que você deve atingir

Ao receber um PCAP desconhecido, você deve conseguir responder:

1. Existe reconhecimento (port scan)?

2. Existe comunicação periódica (beaconing)?

3. Existe possível C2?

4. Existe abuso de DNS?

5. Quais evidências sustentam minha conclusão?


Quando você chega nesse nível, para de olhar pacotes isolados e começa a enxergar o comportamento completo do atacante na rede — exatamente o que um analista SOC faz durante uma investigação real.