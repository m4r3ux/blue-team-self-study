# Triagem de Alertas em um SOC (L1)

Imagine-se como um estagiário, olhando para o monitor do seu mentor (Analista L1 ou L2) e vendo centenas de alertas aparecendo, mudando de status e eventualmente desaparecendo de algum painel.

Você percebe alertas como:
- "Email Marked as..."
- "Incomum Gmail Login Location"
- "Uso de Mimikatz não aprovado"

Quais são esses alertas, como eles são formados e o que devemos fazer com eles? Vamos entender.

![Painel de alerta](https://tryhackme-images.s3.amazonaws.com/user-uploads/678ecc92c80aa206339f0f23/room-content/678ecc92c80aa206339f0f23-1741715853256.gif)

---

## De Eventos a Alertas

Primeiro, um evento ocorre:
- Login de usuário
- Execução de processo
- Download de arquivos

Depois:
1. O sistema (endpoint, servidor ou nuvem) registra o evento
2. Logs são enviados para soluções de segurança (SIEM/XDR)
3. A equipe pode receber milhões de logs por dia

A maioria dos eventos é normal, mas alguns exigem atenção.

**Alerta**:
> Notificação gerada quando um evento ou sequência de eventos suspeitos ocorre.

Sem alertas:
- Analistas revisariam milhões de logs

Com alertas:
- Analistas focam apenas em eventos relevantes

---

## Plataformas de Gestão de Alertas

| Tipo | Exemplos | Descrição |
|------|----------|----------|
| SIEM | Elastic, Splunk | Gerenciamento centralizado de logs e alertas |
| EDR/NDR | Microsoft Defender, CrowdStrike | Detectam atividades suspeitas em endpoints/rede |
| SOAR | Cortex, Splunk SOAR | Automação e orquestração de alertas |
| Ticketing | Jira, TheHive, Trello | Gestão de fluxo e acompanhamento de alertas |

---

## Papel do L1 na Triagem

Analistas L1 são a primeira linha de defesa.

Eles podem receber:
- De 0 a 100 alertas por dia

### Responsabilidades

- **L1**
  - Revisar alertas
  - Identificar se é ameaça ou não
  - Escalar quando necessário

- **L2**
  - Investigação aprofundada
  - Resposta ao incidente

- **Engenheiros de Detecção**
  - Criar regras e melhorar alertas

- **Gerentes**
  - Monitorar qualidade e velocidade

---

## Propriedades de um Alerta

| # | Propriedade | Descrição | Exemplos |
|---|------------|----------|----------|
| 1 | Tempo | Quando o alerta foi gerado | Evento: 15:32 / Alerta: 15:35 |
| 2 | Nome | Resumo da detecção | Login incomum, força bruta |
| 3 | Severidade | Nível de risco | Baixo, Médio, Alto, Crítico |
| 4 | Status | Estado do alerta | Novo, Em progresso, Fechado |
| 5 | Veredicto | Classificação final | Verdadeiro positivo / Falso positivo |
| 6 | Atribuição | Responsável pelo alerta | Analista designado |
| 7 | Descrição | Explicação do alerta | Lógica + contexto |
| 8 | Campos | Dados técnicos | Host, usuário, command line |

---

## Priorização de Alertas

Nem todos os alertas são iguais. É necessário priorizar.

### Processo:

1. **Filtrar**
   - Ignorar alertas já analisados
   - Focar apenas nos novos

2. **Ordenar por severidade**
   - Crítico
   - Alto
   - Médio
   - Baixo

3. **Ordenar por tempo**
   - Começar pelos mais antigos

---

## Fluxo de Triagem

![Fluxo de triagem](https://tryhackme-images.s3.amazonaws.com/user-uploads/678ecc92c80aa206339f0f23/room-content/678ecc92c80aa206339f0f23-1773962582664.svg)

---

## Ações Iniciais

Objetivo:
- Assumir responsabilidade pelo alerta
- Evitar conflitos com outros analistas

Passos:
- Atribuir o alerta a si mesmo
- Mudar status para "In Progress"
- Ler nome, descrição e dados principais

---

## Investigação

Etapa mais importante.

O analista deve:
- Entender o alvo (usuário, host, sistema)
- Analisar a atividade (login, execução, etc.)
- Correlacionar eventos próximos
- Buscar evidências adicionais

### Apoio

- Playbooks / Runbooks
- Threat Intelligence
- Ferramentas do SOC

---

## Ações Finais

Decisão final do analista:

- **Verdadeiro Positivo**
  - Existe ameaça real
  - Escalar para L2

- **Falso Positivo**
  - Atividade legítima

### Finalização:

- Documentar análise
- Registrar evidências
- Atualizar status para "Closed"