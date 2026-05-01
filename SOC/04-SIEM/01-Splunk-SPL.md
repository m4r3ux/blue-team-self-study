# 1. Entendendo o ambiente: Search & Reporting App

O **Search & Reporting** é onde a investigação acontece no Splunk.

Pensa assim:

> SIEM sem busca eficiente = você está cego.

### Componentes principais

- **Search Head**  
    → Onde você escreve queries  
    → É literalmente seu “terminal de investigação”
- **Time Picker**  
    → Um dos pontos mais críticos  
    → Define _quando_ você está investigando

⚠️ Impacto real:

- Errar o tempo = perder incidente
- Muito amplo = ruído absurdo
- Muito curto = perde contexto

---

- **Search History**  
    → Histórico de queries  
    → Útil para:
    - Repetir investigações
    - Aprender padrões
    - Criar playbooks

---

- **Data Summary**  
    → Visão geral dos dados:
    - hosts
    - sources
    - sourcetypes

💡 Mentalidade SOC:

> Antes de investigar → entenda o dataset

---

# 📦 2. Index = Onde os logs vivem

```
index=windowslogs
```

- `index` = container de dados
- Equivalente a um “banco de logs”

💡 Na prática:

- Cada tipo de log fica separado
- Você SEMPRE começa por um index

---

# 🔎 3. Primeira busca: Free Text

```
index=windowslogs alice
```

Isso busca qualquer evento contendo “alice”.

### Quando usar:

- Threat hunting inicial
- Quando você não sabe os campos
- IOC simples

⚠️ Limitação:

- Muito ruído
- Pouco preciso

---

# 🧩 4. Fields Sidebar (subestimado por iniciantes)

Aqui está uma skill MUITO importante:

Você aprende **como o log está estruturado**.

### Tipos:

- **Selected Fields**  
    → Campos já extraídos
- **Interesting Fields**  
    → Splunk sugere campos relevantes
- `#` → numérico
- `α` → texto

💡 Mentalidade:

> Um bom analista entende os campos antes de buscar

---

# ⚙️ 5. SPL (Search Processing Language)

SPL é o coração do Splunk.

Ele permite:

- Filtrar
- Correlacionar
- Transformar
- Detectar

---

# 🔧 6. Operadores — Base da investigação

## Relacionais

```
AccountName=MarkAccountName!=SYSTEM
```

💡 Caso real:

- Remover SYSTEM = remover ruído

```
index=windowslogs AccountName!=SYSTEM
```

---

## Lógicos

```
UserName=David AND IP=10.10.10.10UserName=David OR UserName=John
```

💡 Dica prática:

- `AND` geralmente é implícito

---

## Wildcards

```
status=*fail*DestinationIp=172.*
```

💡 Muito usado para:

- brute force detection
- erro de autenticação
- padrões

---

## CIDR (muito importante)

```
DestinationIp=172.18.0.0/16
```

💡 Isso é vida real:

- redes internas
- ranges corporativos

---

# 🧠 7. Ordem de avaliação (erro comum em SOC)

## Problema clássico:

```
alice AND bob OR charlie
```

Splunk interpreta como:

```
alice AND (bob OR charlie)
```

## Correto:

```
(alice AND bob) OR charlie
```

⚠️ Impacto real:

- Resultado errado
- Investigação comprometida

---

# 🔗 8. Pipes (|) — Pipeline de investigação

```
index=windowslogs | fields host User SourceIp
```

Cada `|`:

> pega o resultado anterior e transforma

💡 Pense como:

- pipeline de análise
- etapas de investigação

---

# 🧹 9. Comandos essenciais (nível SOC)

## fields

```
| fields host User SourceIp
```

→ reduz ruído  
→ melhora leitura

---

## dedup

```
| dedup SourceIp
```

→ remove duplicados

💡 Caso real:

- evitar flood de logs repetidos

---

## rename

```
| rename User as Employee
```

→ melhora relatório

💡 importante para:

- documentação SOC
- apresentação

---

## regex

```
| regex Image="\.exe$"
```

→ filtra padrões complexos

💡 muito usado em:

- malware hunting
- logs bagunçados

---

# 📊 10. Organização dos dados

## table

```
| table _time EventID Hostname SourceName
```

→ cria visual limpo

💡 essencial para:

- timeline
- investigação

---

## head / tail

```
| head 20
```

→ acelera análise

---

## sort / reverse

```
| sort User| reverse
```

→ ordena eventos

---

# 🕵️ 11. Timeline (isso é SOC real)

```
index=windowslogs Hostname=Salena.Adam| table _time Hostname EventID Category| reverse
```

💡 Aqui você:

- reconstrói o ataque
- entende sequência

> Isso é investigação de verdade.

---

# 🔗 12. Correlação (nível intermediário)

## Subsearch + join

Você correlacionou:

- Sysmon (processo)
- Security (logon)

```
| join LogonId [...]
```

💡 Tradução SOC:

> “esse processo veio de qual login?”

⚠️ Problema:

- pesado
- lento

---

# 📈 13. Transformação de dados

## stats (muito importante)

```
| stats count by EventID
```

→ agrega dados

💡 uso real:

- volume de eventos
- detectar anomalias

---

## top / rare

```
| top User| rare User
```

💡 SOC:

- top → comportamento normal
- rare → possível ameaça

---

## chart / timechart

```
| timechart count by Image
```

→ comportamento ao longo do tempo

💡 perfeito para:

- detectar picos
- atividade suspeita

---

# 🌍 14. Enriquecimento de dados

## iplocation

```
| iplocation SourceIp
```

→ adiciona país, cidade

💡 base para:

- geolocalização
- fraude
- acesso suspeito

---

## lookup

```
| lookup user_roles Hostname OUTPUT UserRole
```

→ adiciona contexto externo

💡 exemplo:

- função do usuário
- criticidade

---

## eval (canivete suíço)

```
| eval LogonTypeDesc = case(...)
```

→ cria novos campos

💡 essencial para:

- normalização
- legibilidade

---

# 🚨 15. Detecção de anomalias (alto nível)

Aqui começa o **pensamento de analista de verdade**.

---

## Outlier por país

```
| where country_freq < 0.1
```

→ comportamento raro

💡 interpretação:

> login único de país diferente = suspeito

---

## Outlier por horário

Você usou conceito de estatística:

- média
- desvio padrão
- z-score

Isso é basicamente:

z=∣x−μ∣σz = \frac{|x - \mu|}{\sigma}z=σ∣x−μ∣​

Onde:

- `x` = evento atual
- `μ` = comportamento normal
- `σ` = variação

💡 SOC mindset:

> “isso foge do padrão desse usuário?”