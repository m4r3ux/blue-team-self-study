# Caça de ameaças com MITRE ATT&CK — Guia prático para SOC

Leitura baseada no artigo:  
https://medium.com/@rehmanwaraich107/threat-hunting-with-mitre-att-ck-a-practical-guide-for-soc-analysts-61abe5cb02c1  
Por Misbah Wariach

---

## Objetivo do conteúdo

Este material tem como objetivo explicar:

- O que é o framework MITRE ATT&CK
- Por que ele é fundamental na caça de ameaças (threat hunting)
- Como aplicá-lo de forma estruturada em um fluxo de análise
- Como transformar hipóteses em investigações orientadas por técnicas reais
- Exemplos práticos aplicáveis em ambiente SOC

---

## O que é o MITRE ATT&CK

O MITRE ATT&CK (Adversarial Tactics, Techniques, and Common Knowledge) é um framework baseado em comportamentos reais de atacantes observados em incidentes do mundo real.

Ele organiza o comportamento do adversário em três níveis principais:

- Táticas: o objetivo do atacante (por que ele está fazendo algo)
- Técnicas: a forma como o objetivo é alcançado (como ele executa a ação)
- Procedimentos: a implementação real e específica usada por atacantes reais

Diferente de taxonomias genéricas, o MITRE ATT&CK é construído com base em evidências de ataques reais, não em teoria.

---

## Por que o MITRE ATT&CK é importante

O MITRE ATT&CK fornece uma linguagem comum para descrever comportamento adversário e permite que analistas de segurança:

- Mapeiem alertas para técnicas reais de ataque
- Entendam em que fase do ataque o adversário está
- Priorizem investigações com base em comportamento esperado
- Desenvolvam regras de detecção mais precisas
- Melhorem a comunicação entre analistas e times de segurança

Em vez de descrever um evento como “atividade suspeita no PowerShell”, é possível contextualizar como:

“Possível execução de código via T1059 (Command and Scripting Interpreter)”

Isso transforma observações isoladas em inteligência estruturada.

---

## Como usar MITRE ATT&CK na caça de ameaças

A caça de ameaças não deve ser baseada em exploração aleatória de logs, mas sim em um processo estruturado baseado em hipóteses.

O fluxo básico pode ser dividido em etapas.

---

## Passo 1 — Construir uma hipótese

Uma hipótese define o que será investigado e por qual motivo.

Exemplo:

“Endpoints na sub-rede financeira podem apresentar sinais de credential dumping ou força bruta nas últimas 72 horas.”

Uma boa hipótese deve conter:

- Período de tempo definido
- Escopo (rede, hosts, usuários ou segmento específico)
- Comportamento esperado ou suspeito

Esse passo reduz a aleatoriedade e direciona a investigação.

---

## Passo 2 — Mapear a hipótese para MITRE ATT&CK

Após definir a hipótese, ela deve ser traduzida para comportamentos de atacante descritos no MITRE ATT&CK.

Exemplo de mapeamento:

- Credential Access → Credential Dumping (T1003)
- Lateral Movement → Remote Services (T1021)
- Execution → Command and Scripting Interpreter (T1059)

Esse mapeamento serve para transformar intenções de ataque em técnicas específicas que podem ser investigadas nos logs.

---

## Como esse mapeamento funciona na prática

O processo não é automático. Ele segue uma lógica de interpretação:

- A hipótese descreve comportamento suspeito
- O analista identifica o que um atacante faria para causar esse comportamento
- Esse comportamento é traduzido para uma técnica do MITRE ATT&CK

Exemplo:

- “Uso suspeito de PowerShell”
  → execução de comandos
  → T1059 (Execution)

---

## Passo 3 — Identificar fontes de dados relevantes

Com as técnicas definidas, é necessário identificar quais dados podem confirmar ou refutar a hipótese.

Fontes comuns incluem:

- Logs de autenticação (login e falhas de login)
- Logs de processos (PowerShell, criação de processos)
- Telemetria de EDR (linha de comando e comportamento de processos)
- Logs de rede (conexões suspeitas)
- Logs de nuvem, quando aplicável

Essa etapa também ajuda a identificar lacunas de visibilidade na infraestrutura.

---

## Passo 4 — Escrever e executar consultas de caça

Nesta etapa, o foco é investigar comportamentos específicos, não padrões genéricos.

Exemplo em SIEM:

- Analisar eventos de falha de login (login_failure)
- Agrupar por usuário e IP de origem
- Identificar tentativas acima do comportamento normal

Exemplo de análise de PowerShell:

- Filtrar execuções de powershell.exe
- Agrupar por host e usuário
- Identificar volumes anormais de execução

O uso do MITRE ATT&CK aqui ajuda a direcionar consultas para comportamentos específicos de ataque.

---

## Passo 5 — Investigar e documentar

Quando uma consulta retorna resultados relevantes:

- Validar se o comportamento é malicioso ou legítimo
- Mapear os achados para técnicas do MITRE ATT&CK
- Documentar evidências e contexto da investigação

Esse processo transforma uma análise isolada em inteligência reutilizável para o time de segurança.

---

## Exemplo prático — Credential Dumping

### Hipótese

Um servidor pode ter sido comprometido e ferramentas de credential dumping podem ter sido utilizadas.

### Mapeamento MITRE ATT&CK

- Tática: Credential Access
- Técnica: T1003 (Credential Dumping)

### Fontes de dados

- Logs de processos (EDR)
- Acesso à memória do processo LSASS
- Logs de linha de comando

### Investigação

- Identificar acessos suspeitos ao processo LSASS
- Verificar uso de ferramentas conhecidas como Mimikatz
- Correlacionar com atividade de login e possíveis movimentos laterais

---

## Conclusão

O MITRE ATT&CK funciona como um modelo mental para análise de comportamento adversário.

Quando logs são interpretados através de técnicas reais de ataque, em vez de observações isoladas, a caça de ameaças se torna:

- mais precisa
- mais eficiente
- mais orientada a impacto real

Esse modelo permite que o analista saia da investigação reativa e passe a operar de forma estruturada e orientada a comportamento de atacante.