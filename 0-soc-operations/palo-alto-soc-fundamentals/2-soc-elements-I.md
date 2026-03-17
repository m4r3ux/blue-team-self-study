## SOC Elements I: Business, People, & Processes

## 1. SOC Elements (Pilares do SOC)

O funcionamento de um **Security Operations Center (SOC)** é dividido em **6 pilares principais**, permitindo associar atividades de segurança a diferentes áreas e stakeholders da organização.

### Os 6 pilares do SOC
1. **Business**
2. **People**
3. **Processes**
4. **Interfaces**
5. **Visibility**
6. **Technology**

Todos os pilares **se conectam ao pilar de Business**, pois o objetivo do SecOps é **suportar as necessidades de segurança do negócio**.

---

# 2. Business Pillar

Define **o propósito do SOC para a empresa** e **como ele será gerenciado**.

### Elementos do Business Pillar

- **Mission** — O que estamos fazendo
- **Governance** — Como vamos gerenciar
- **Planning** — Como vamos executar
- **Budget** — Quanto custará
- **Staffing** — Quem fará o trabalho
- **Facility** — Onde o SOC operará
- **Metrics** — Como medir eficiência
- **Reporting** — Como reportar resultados
- **Collaboration** — Como interagir com outras áreas

### Elementos Fundamentais
Os três mais importantes são:

- **Mission**
- **Governance**
- **Planning**

### Mission
Define:

- Objetivos do SOC
- Razão da existência
- Resultados esperados para o negócio
- Como as ações serão executadas

---

# 3. Metrics (Métricas do SOC)

Métricas devem **gerar melhorias e decisões**, não apenas números.

### Exemplos de métricas ruins

Podem incentivar comportamentos errados:

- **MTTR (Mean Time to Respond)**
- Número de incidentes tratados
- Número de regras/firewalls criados
- Quantidade de feeds no SIEM

### Boas métricas

Devem gerar **confiança na segurança da empresa**.

Existem dois tipos principais:

---

## Configuration Confidence

Garante que **as tecnologias estão configuradas corretamente para prevenir ataques**.

Perguntas importantes:

- Os controles de segurança estão funcionando?
- Existem mudanças fora do controle de mudanças?
- As tecnologias seguem **best practices**?
- Qual porcentagem das funcionalidades das ferramentas está sendo utilizada?

---

## Operational Confidence

Garante que **pessoas e processos estão preparados para responder a incidentes**.

Perguntas importantes:

- Quantos eventos os analistas tratam por hora?
- Existem incidentes repetidos?
- O SOC está lidando com ameaças conhecidas?
- Há desvios nos procedimentos do SOC?

---

# 4. Reporting (Relatórios)

Relatórios mostram **o valor que o SOC entrega ao negócio**.

Eles documentam:

- Atividades realizadas
- Incidentes analisados
- Resultados de segurança

### Tipos de relatórios

**Daily Reports**
- Incidentes abertos
- Atividades do dia

**Weekly Reports**
- Tendências de ameaças
- Métricas operacionais

**Monthly Reports**
- Análise estratégica
- Avaliação da eficiência do SOC

---

# 5. People Pillar

Define **os papéis e gestão das pessoas dentro do SOC**.

### Importância das pessoas

Analistas SOC precisam de:

- Conhecimento técnico
- Boa comunicação
- Tomada rápida de decisões
- Capacidade de responder a ameaças complexas

### Pontos importantes de gestão

- Práticas de contratação e treinamento
- Redução de fadiga dos analistas
- Validação do trabalho da equipe
- Retenção de talentos

---

## SOC Tradicional vs SOC Moderno

### SOC Tradicional
Problemas comuns:

- Sobrecarga de consoles
- Excesso de alertas
- Burnout de analistas

### SOC Moderno

Utiliza:

- **Automação**
- **AI e Machine Learning**
- **Threat Detection Automation**

Benefícios:

- Menos fadiga
- Melhor resposta a incidentes
- Analistas focam em tarefas estratégicas

---

# 6. SOC Training

Treinamento estruturado é essencial para **consistência e eficiência**.

Shadowing (acompanhar analistas) **não deve ser o único método de treinamento**.

Deve existir **documentação formal** sobre:

- Ferramentas
- Processos
- Comunicação
- Procedimentos

### Estrutura recomendada de treinamento

1. Segurança e privacidade da empresa
2. Uso de ferramentas
3. Documentação de processos
4. Planos de comunicação
5. Educação contínua

---

# 7. Processes Pillar

Os processos do SOC seguem **4 funções principais**:

1. **Identification**
2. **Investigation**
3. **Mitigation**
4. **Continuous Improvement**

---

## Identification

Primeira etapa: detectar atividades suspeitas.

Elementos principais:

- **Alerting** — geração de alertas
- **Initial Research** — análise inicial
- **Severity Triage** — classificação de severidade
- **Escalation Process** — escalonamento

---

## Investigation

Processo de investigação digital e forense.

Etapas:

1. **Identify** — identificar evidências
2. **Preserve** — preservar evidências
3. **Analyze** — analisar dados
4. **Document** — documentar análise
5. **Report** — reportar resultados

---

## Mitigation

Ações para **conter e resolver incidentes**.

Inclui:

- Resposta a violação (Breach Response)
- Estratégias de mitigação
- Cenários de mitigação
- Interface agreements entre equipes
- Change control

---

## Continuous Improvement

Processo de melhoria contínua do SOC.

Elementos principais:

- **Tuning**
- **Process Improvement**
- **Capability Improvement**
- **Quality Review**

---

### Tuning

Ajuste de alertas e regras baseado em investigações anteriores.

Objetivos:

- Reduzir falsos positivos
- Melhorar visibilidade no SIEM
- Detectar incidentes mais rapidamente

### Boas práticas de tuning

- Definir quem inicia o tuning
- Criar thresholds de acionamento
- Revisar alertas existentes
- Solicitar melhorias nas regras

Recomendações:

- Revisar métricas de alertas **mensalmente**
- Revisar regras **trimestralmente**