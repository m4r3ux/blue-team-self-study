# Análise de Tráfego Criptografado

Analisar tráfego criptografado não significa descriptografar conteúdo.  
Na maioria dos ambientes de SOC, você não verá o que foi enviado, mas **como foi enviado**.

O foco está em metadados e comportamento.

---

# 1. Metadados TLS

Mesmo utilizando HTTPS, várias informações continuam visíveis durante o handshake TLS.

Quando um cliente inicia uma conexão, ele envia um **Client Hello**. Nesse momento é possível observar:

- Versão do TLS
- Cipher suites suportadas
- Extensões TLS
- SNI (Server Name Indication)

## 1.1 SNI (Server Name Indication)

O SNI revela o domínio que o cliente deseja acessar.

Exemplo:  
Se um host interno inicia conexão TLS para `update-security-checker.xyz`, você conseguirá ver esse nome no handshake, mesmo sem acesso ao conteúdo criptografado.

Isso permite:

- Identificar domínios suspeitos
- Correlacionar com logs de DNS
- Detectar comunicação com domínios recém-criados

---

## 1.2 Certificado do Servidor

Durante o handshake, o servidor envia seu certificado digital. É possível analisar:

- Subject (para qual domínio foi emitido)
- Issuer (autoridade certificadora)
- Período de validade
- Se é autoassinado

Indicadores de atenção:

- Certificados autoassinados inesperados
- Validade muito curta
- Domínio diferente do SNI
- Autoridade emissora incomum

Importante: certificado válido não significa tráfego legítimo. Atacantes também utilizam certificados confiáveis.

---

# 2. Tamanho de Pacotes

Sem ver o conteúdo, o tamanho dos pacotes e o volume de dados tornam-se indicadores fundamentais.

Observe:

- Quantidade total de bytes transferidos
- Relação upload vs download
- Tamanho médio dos pacotes
- Número de pacotes por sessão

## 2.1 Comportamento Normal

Exemplo: usuário assistindo vídeo.

- Sessão longa
- Alto volume de download
- Pacotes grandes
- Fluxo contínuo

## 2.2 Comportamento Suspeito

Exemplo típico de beaconing:

- Conexão curta
- 200–300 bytes enviados
- 200–400 bytes recebidos
- Sessão encerrada rapidamente
- Repetição em intervalos regulares

Pequenos pacotes + repetição constante indicam automação.

---

## 2.3 Relação Upload vs Download

Em navegação comum:

Download > Upload

Possível exfiltração:

Upload muito superior ao download

Se um servidor interno envia grandes volumes para a internet sem justificativa operacional, isso deve ser investigado.

---

# 3. Padrões de Fluxo

É aqui que o analista realmente constrói hipóteses.

Você deve observar:

## 3.1 Frequência

Conexões ocorrendo a cada 30, 45 ou 60 segundos de forma constante indicam comportamento automatizado.

## 3.2 Duração

Sessões muito curtas e repetitivas podem indicar comunicação com servidor de comando e controle (C2).

## 3.3 Horário

Tráfego constante durante madrugada, fins de semana ou fora do horário operacional pode indicar atividade automatizada.

## 3.4 Consistência

Beaconing costuma manter:

- Intervalo fixo
- Tamanhos semelhantes
- Duração parecida entre conexões

---

# 4. Estrutura Mental para Análise

Ao analisar tráfego TLS, responda:

- Para onde está indo?
- Com que frequência ocorre?
- Quanto está sendo transferido?
- O comportamento faz sentido para o tipo de máquina?

Exemplo de possível comunicação C2:

- Conexão TLS a cada 30 segundos
- 250 bytes enviados
- 300 bytes recebidos
- Duração de 1 segundo
- Execução contínua 24h por dia

Mesmo sem descriptografar o conteúdo, o padrão comportamental indica anomalia.

---

# Conclusão

Análise de tráfego criptografado é análise de comportamento.

Você não depende de descriptografia para:

- Identificar beaconing
- Detectar exfiltração
- Reconhecer automação maliciosa
- Correlacionar eventos com DNS e logs internos

A habilidade central está em interpretar padrões, não em visualizar conteúdo.