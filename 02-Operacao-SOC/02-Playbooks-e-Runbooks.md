## Playbooks e Runbooks — Operação SOC (nível prático)  
  
### Contexto real  
  
Você está no SOC. Alertas chegam o tempo todo.  
  
Sem processo, você:  
- demora    
- erra    
- toma decisão fraca    
  
Com processo (playbook), você:  
- investiga rápido    
- mantém consistência    
- documenta corretamente    
  
---  
  
### Playbook vs Runbook (sem confusão)  
  
#### Playbook (investigação e decisão)  
  
É o roteiro mental de análise.  
  
Define:  
- o que verificar    
- quais perguntas fazer    
- como classificar    
  
**Exemplo:**  
> “Se login veio de IP novo + fora do horário → investigar comprometimento”  
  
---  
  
#### Runbook (execução técnica)  
  
É como você executa.  
  
Inclui:  
- queries    
- comandos    
- uso de ferramentas    
  
**Exemplo:**  
- buscar logs no SIEM    
- consultar IP em threat intel    
- extrair timeline    
  
---  
  
### Diferença direta  
  
- **Playbook = processo de decisão**    
- **Runbook = execução técnica**  
  
---  
  
### Como seguir um playbook (do jeito certo)  
  
Você não segue como checklist mecânico.  
  
Você segue como lógica:  
- validar contexto    
- identificar anomalia    
- medir impacto    
- decidir    
  
A pergunta constante é:  
  
> Isso é comportamento normal ou não?  
  
---  
  
## Playbook SOC — Brute Force  
  
### Alerta  
  
Multiple failed logins + success  
  
---  
  
### 1. Validar contexto  
  
- IP é interno ou externo?    
- geolocalização faz sentido?    
- horário é comum para o usuário?    
  
Se algo for anômalo, aumenta prioridade.  
  
---  
  
### 2. Analisar o IP  
  
- tentou outros usuários?    
- volume de tentativas?    
- velocidade (rápido = automação)    
  
Se múltiplos usuários ou alta frequência, indica brute force.  
  
---  
  
### 3. Analisar o usuário  
  
- já logou desse IP antes?    
- padrão histórico de login    
- já teve falhas semelhantes?    
  
Se for comportamento novo, é suspeito.  
  
---  
  
### 4. Verificar o que aconteceu depois  
  
**Ponto crítico que muita gente ignora.**  
  
- houve acesso a sistemas sensíveis?    
- download de dados?    
- alteração de senha?    
- criação de novos acessos?    
  
Isso define se houve impacto real.  
  
---  
  
### 5. Classificar  
  
- falso positivo    
- atividade suspeita    
- incidente confirmado    
  
---  
  
### 6. Ação  
  
Depende da classificação:  
  
- reset de senha    
- bloqueio de IP    
- desativar conta    
- escalar incidente    
  
---  
  
## Runbook (exemplo técnico simplificado)  
  
**Buscar falhas:**  
```sql  
event_type=login_failed user="user"
```
**Buscar sucesso:**
```sql  
event_type=login_success user="user"
```
**Correlacionar IP:**
```sql  
src_ip="X.X.X.X"
```
**Construir timeline:**
```sql  
sort by timestamp
```
---

## Playbook — Phishing

### Fluxo

- usuário interagiu com o email?
- inseriu credenciais?
- houve login suspeito depois?
- domínio do remetente é confiável?

### Ação

- reset de senha
- revogar sessões
- bloquear domínio
- awareness do usuário

---

## Playbook — Malware suspeito

### Fluxo

- arquivo foi executado?
- hash é conhecido?
- houve conexão externa?
- existe persistência?

### Ação

- isolar máquina
- remover artefatos
- investigar movimento lateral

---

## Erros comuns

- assumir explicação simples sem validar
- ignorar comportamento histórico
- não verificar o que aconteceu após o evento
- tratar alerta isolado

---

## Resultado esperado (se você fizer certo)

Dado um alerta de brute force, você deve:

- validar contexto rapidamente
- identificar anomalias claras
- confirmar ou descartar impacto
- tomar decisão
- documentar

---

# Análise de Playbook de Exemplo Genérico - Phishing

https://github.com/counteractive/incident-response-plan-template/blob/master/playbooks/playbook-phishing.md#TODO-link-to-actual-resource

## Ajuste mental inicial (muito importante)

Esse playbook **não é para seguir linha por linha**.

Logo no começo ele já indica:

> “trabalhar em paralelo”

Ou seja:

- investigação acontecendo  
- contenção acontecendo  
- comunicação acontecendo  

Isso é o mundo real.

Se você tentar seguir de forma sequencial → **perde tempo e o ataque avança**.

---

## Estrutura real do playbook (simplificada)

Apesar de parecer grande, ele se resume a 4 blocos:

- **Investigate** → entender o que aconteceu  
- **Remediate** → parar o ataque  
- **Communicate** → avisar e coordenar  
- **Recover** → voltar ao normal  

Isso é, na prática, um fluxo de **Incident Response**.

---

## Investigate (tradução prática para SOC)

### 3.1 Scope the attack (escopo)

Você precisa responder:

- quantos usuários foram afetados?  
- alguém caiu no golpe?  
- houve ação (clique, login, download)?  

Classificação rápida:

- só tentativa → baixo impacto  
- credenciais inseridas → crítico  

---

### 3.2 Analyze the message (analisar o email)

Você quer identificar:

- remetente é legítimo?  
- domínio é parecido (typosquatting)?  
- link aponta para onde?  
- header bate com o “From”?  

Aqui você extrai IOC:

- domínio malicioso  
- IP  
- hash (se houver anexo)  

---

### 3.3 Analyze links and attachments

Aqui é execução técnica (runbook):

- verificar domínio (whois, nslookup)  
- consultar no VirusTotal  
- usar sandbox (se necessário)  

Objetivo:

- confirmar se é malicioso ou não  

---

### 3.4 Categorize the attack

Muita gente ignora, mas é essencial:

- credential harvesting  
- malware delivery  
- BEC (Business Email Compromise)  

A resposta muda dependendo do tipo.

---

### 3.5 Determine severity

Definição de prioridade:

- usuário só recebeu → baixo  
- clicou → médio  
- digitou senha → alto  
- houve acesso depois → incidente  

---

## Remediate (o que realmente fazer)

### Contain (conter)

Ações imediatas:

- reset de senha (se credencial exposta)  
- bloquear domínio malicioso  
- remover emails das caixas  
- bloquear IP/domínio no gateway  

Objetivo:

- impedir que o ataque continue  

---

### Monitoramento (ponto crítico)

Pouco aplicado na prática, mas essencial:

- observar logins do usuário  
- buscar uso das credenciais  
- caçar comportamento anômalo  

---

## Communicate (parte ignorada por iniciantes)

Parece burocracia, mas é crítico.

Você deve:

- documentar o que aconteceu  
- avisar liderança (se houver impacto)  
- orientar usuários  

**Exemplo:**

> “Se você recebeu esse email, não clique. Reporte imediatamente.”

---

## Recover (pós-incidente)

Aqui entra maturidade operacional:

- treinar usuários  
- ajustar filtros  
- melhorar detecção  

Se você não fizer isso:

- o ataque se repete  

---

## O que o playbook NÃO te dá

Você vai notar vários **TODOs**.

Isso é proposital.

Playbook genérico = incompleto.

Cada empresa precisa adaptar:

- ferramentas (SIEM, EDR, email gateway)  
- queries específicas  
- contatos internos  
- processos de escalonamento  

---

## Como um analista experiente usa isso

Ele não lê tudo.

Ele pensa em forma de **mapa mental**:

Quando chega alerta de phishing:

- alguém clicou?  
- alguém digitou credencial?  
- houve login depois?  
- qual o impacto?  

E em paralelo:

- extrai IOC  
- bloqueia domínio  
- remove email  
- monitora contas  

---

## Erros clássicos

- tentar seguir tudo sequencialmente  
- focar só no email e ignorar o usuário  
- não verificar comprometimento real  
- demorar para conter  

---

# LAB 1 — Phishing com possível comprometimento

## Contexto

Você está no turno (nível SOC L1/L2).

Um ticket chega do time de Help Desk:

> “Usuário reportou email suspeito e comportamento estranho na conta.”

---

## Informações iniciais (o que você recebeu)

**Usuário:** `ana.souza@empresa.com`  
**Departamento:** Financeiro

**Relato do usuário:**

> “Recebi um email da Microsoft pedindo para revalidar minha senha. Cliquei no link e fiz login. Depois disso minha caixa de entrada ficou estranha.”

---

## Alertas no SIEM

- Login bem-sucedido:
    - IP: `185.234.217.55`
    - Localização: Alemanha
    - Horário: 10:14
- Usuário normalmente loga do Brasil (SP)

---

## Log adicional (email activity)

- Criação de regra de inbox:
    - “Move emails com palavra ‘invoice’ para pasta RSS”
- Envio de emails:
    - 12 emails enviados para contatos internos
    - Assunto: “Urgente: pagamento pendente”

---

# Sua missão

Você está com isso na mão agora.

Responda como analista.

---

## Fase 1 — Classificação

1. Isso é:
    - falso positivo
    - suspeito
    - incidente confirmado
2. Qual evidência **fecha o diagnóstico**?

R: Incidente confirmado - comprometimento de credencial via phishing seguido por acesso não autorizado e campanha de phishing na rede interna, possível exfiltração de dados e tentativa de escalação de privilégios.

Evidências:
- Login em localização suspeita e IP possívelmente externo.
- Usuário clicou em e-mail de phishing e expôs credenciais de conta.
- E-mails enviados para contatos internos com tema de phishing.

---

## Fase 2 — Contenção (prioridade máxima)

Liste **exatamente o que você faz primeiro** (ordem importa)

1- Bloqueio o endereço de IP do atacante.
2-Bloqueio a conta de e-mail comprometida, reseto sua senha e aplico MFA.
3- Revogação de privilégios da conta.
4- Realizo ou escalo a ação de exclusão de e-mails enviados, seguido de investigação de possíveis cliques posteriores que podem levar a comprometimento de outros usuários.

---

## Fase 3 — Investigação

Quero ver profundidade aqui.

Liste pelo menos **5 coisas específicas** que você vai investigar agora.

(Nada genérico tipo “ver logs” — seja técnico)

1 - Investigar endereço IP no VirusTotal e ver possíveis relações com o endereço, buscando IOCs que possam haver também no incidente.
2- Depois de conter o ataque com bloqueio de endereço IP de atacante, reset de senha, MFA , revogação de privilégios, realizo a comunicação com a equipe.

---

## Fase 4 — Escopo

O problema pode ser maior.

O que você faz para descobrir se:

- outros usuários foram afetados?
- o ataque está se espalhando?

---

## Fase 5 — Decisão final

Resuma em 3 linhas:

- o que aconteceu
- impacto
- ação tomada

---

# Regras

- responda como se estivesse no SOC
- seja direto e técnico
- nada de resposta genérica