O objetivo, é sairmos sabendo como os dados tráfegam na rede, camada por camada, o que cada camada revela em logs.

## Modelo OSI (7 camadas)

7 - Aplicação: Interface com usuário (HTTP, DNS...)
6 - Apresentação: formato, criptografia
5 - Sessão: controle de sessão
4 - Transporte (Comunicação fim a fim TCP / UDP)
3 - Rede: roteamento IP
2 - Enlace: MAC, frames
1 - Física: Bits, sinal elétrico

## Detalhadamente

### Camada 1 - Física
O que é:
- Cabos
- Sinais elétricos
- Wi-Fi (nível físico)

Em logs praticamente não vemos isso.

### Camada 2 - Enlace de dados (Data Link)
O que faz:
- Comunicação dentro da rede local
- Usa MAC Address

Exemplo:
```
Source MAC: 00:1A:2B:3C:4D:5E
Destination MAC: 00:AA:BB:CC:DD:EE
```

Em análise, podemos encontrar:
- ARP spoofing
- MAC desconhecido

### Camada 3 - Rede (IP)
O que faz:
- Identifica os hosts na rede
- Roteamento entre redes

Exemplo: 
```
Source IP: 192.168.1.10
Destination IP: 8.8.8.8
```

Em SOC:
- Origem do ataque
- Destino suspeito
- Geolocalização

## Camada 4 - Transporte (TCP/UDP)
O que faz:
- Comunicação entre aplicações
- Usa portas

#### TCP vs UDP

TCP:
- Confiável, conexão, mais lento, usa handshake
- Não confiável, sem conexão, mais rápido, não usa handshake

#### TCP Handshake

SYN -> SYN-ACK -> ACK

Interpretação:
1. Cliente: "Posso conectar?" (SYN)
2. Servidor: "Pode sim" (SYN-ACK)
3. Cliente: "Ok, vamos" (ACK)

Em log:
```
TCP Flags: SYN
TCP Flags: SYN, ACK
TCP Flags: ACK
```

SOC:
- Muitos SYN: possível scan
- SYN sem ACK: tentativa falha

## Camada 7 - Aplicação
Protocolos:
- HTTP
- DNS
- SMTP

#### HTTP:
```
GET /login HTTP/1.1
Host: example.com
```

mostra:
- url acessada
- método (GET, POST)

#### DNS:
```
Query: malicious-domain.com
```

mostra:
- domínio consultado

#### SMTP
```
MAIL FROM: attacker@mail.com
```


## Modelo TCP/IP
|TCP/IP|OSI|
|---|---|
|Application|5–7|
|Transport|4|
|Internet|3|
|Network Access|1–2|

## Fluxo de conexão em site
- DNS resolve domínio - IP
- TCP handshake
- HTTP request (dps de conexão estabelecida

Exemplo completo: 
```
DNS: google.com → 142.250.190.78
TCP: SYN → SYN-ACK → ACK
HTTP: GET /index.html
```

## Exercícios:

### ❓ Pergunta 1

Se você vê:

```Destination Port: 443```

Isso indica o quê?

R: Tráfego HTTPS, como host solicitando uma página porém como é HTTPS, diferente do HTTP os dados viajam criptografados.

---

### ❓ Pergunta 2

Se há muitos pacotes:

```TCP Flags: SYN```

Sem resposta → o que pode ser?

R: Enumeração de portas abertas ou host? Evita finalizar o handshake para evitar muito barulho.

---

### ❓ Pergunta 3

Qual a diferença prática entre:

- IP
- Porta

R: O endereço IP pode ser dois identificadores - o primeiro é o do roteador, que é nosso IP público e o outro, é o IP privado, que nos é dado via DHCP pelo roteador.

A porta é a abertura de um serviço para outros hosts, então, quando um servidor abre a porta 80, ele está permitindo tráfego web através de seu endereço IP, assim, um host pode acessar

IP_publico_host:porta

E se comunicar com o servidor.

---

### ❓ Pergunta 4

```DNS Query: random123xyz.com```

Por que isso pode ser suspeito?

R: Requisição DNS a domínio suspeito, que normalmente é usado para:
- phishing
- C2
- malware

Não me parece um domínio de um serviço / aplicação legítima.

---

### ❓ Pergunta 5 (correlação real)
```
Source IP: 10.0.0.8  
Destination IP: 45.77.12.90  
Port: 4444  
Process: powershell.exe
```

Descreva o que está acontecendo.

R: Pelo que entendo, o host 10.0.0.8 está abrindo uma conexão via powershell para o host 45.77.12.90.

