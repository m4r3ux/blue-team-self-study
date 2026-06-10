# 1. Contexto real (SOC)

Você recebe um alerta:

> “Malware removido com sucesso”

Júnior pensa:

> acabou

Analista pensa:

> **ele deixou persistência?**

Se deixou:

> o atacante volta depois → incidente continua

---

# 2. O que é Persistência (mentalidade correta)

Persistência =

> mecanismo para o atacante voltar ou manter acesso após reboot / logout

---

# 3. Principais técnicas (as que você precisa dominar)

Vou focar nas que você listou — essas aparecem MUITO no dia a dia.

---

## 3.1 Registry Run Keys (MITRE T1547)

Caminhos clássicos:

```
HKCU\Software\Microsoft\Windows\CurrentVersion\RunHKLM\Software\Microsoft\Windows\CurrentVersion\Run
```

### O que fazem:

Executam programas automaticamente no login.

---

### Como atacante usa:

Adiciona algo tipo:

```
malware.exe → executa sempre que usuário loga
```

---

### Como detectar:

- entradas suspeitas
- caminhos estranhos (AppData, Temp)
- nomes que imitam sistema

---

## 3.2 Scheduled Tasks (MITRE T1053)

Criadas com:

```
schtasks
```

---

### O que fazem:

Executam algo em horários ou eventos.

---

### Uso malicioso:

- rodar malware a cada X minutos
- rodar no boot

---

### Event ID importante:

- **4698 → tarefa criada**

---

## 3.3 Services (sc.exe)

Criados com:

```
sc create
```

---

### O que fazem:

Executam como serviço do Windows (alto privilégio).

---

### Event ID:

- **7045 → serviço instalado**

---

## 3.4 Startup Folder

Caminho:

```
C:\Users\...\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup
```

---

### Simples, mas eficaz:

Qualquer coisa ali executa no login.

---

## 3.5 WMI Persistence (avançado)

- menos visível
- baseado em eventos

Você não precisa dominar agora, mas saiba:

> é stealth e difícil de detectar sem ferramenta

---

# 4. Como detectar (o que você realmente precisa saber)

Agora entra o diferencial: **não é saber o que é, é saber encontrar**.

---

## 4.1 Autoruns (ferramenta principal)

Ferramenta da Microsoft Sysinternals.

---

### O que ela faz:

Lista **tudo que executa automaticamente no sistema**:

- Run Keys
- Scheduled Tasks
- Services
- Startup
- muito mais

---

### Como usar (prático)

1. Baixa Autoruns
2. Executa como admin
3. Filtra:

- “Logon”
- “Scheduled Tasks”
- “Services”

---

### O que procurar:

- caminhos suspeitos (AppData, Temp)
- executáveis desconhecidos
- nomes estranhos ou imitando sistema
- entradas sem assinatura

---

## 4.2 Event Viewer (correlação)

Agora você valida com logs.

---

### Event IDs importantes

- **4698 → criação de tarefa**
- **7045 → criação de serviço**
- **4657 → modificação de registry**

---

### O que isso te dá:

- **quando foi criado**
- **quem criou**
- **como foi criado**

---

# 5. LAB (o mais importante)

Agora vamos fazer você realmente aprender.

---

## Objetivo

Você vai:

- criar persistência
- detectar com Autoruns
- confirmar com Event Logs

---

## Passo 1 — Criar Scheduled Task

No cmd (admin):

```
schtasks /create /sc minute /mo 5 /tn "Updater" /tr "notepad.exe"
```

---

## Passo 2 — Criar Run Key

No cmd:

```
reg add HKCU\Software\Microsoft\Windows\CurrentVersion\Run /v Updater /t REG_SZ /d "notepad.exe"
```

---

## Passo 3 — Criar Service

```
sc create UpdaterService binPath= "notepad.exe"
```

---

# 6. Agora você vira o analista

---

## Passo 4 — Abrir Autoruns

Procure:

- “Updater”
- “UpdaterService”

Pergunta:

> Onde cada um aparece?

---

## Passo 5 — Event Viewer

Procure:

- Event ID 4698
- Event ID 7045
- Event ID 4657

Pergunta:

> Você consegue correlacionar com o que você criou?

---

# 7. Como isso aparece no SOC

Você não vai “criar”, você vai ver algo assim:

---

## Cenário real

Alerta EDR:

> “Suspicious persistence mechanism detected”

---

## Você investiga:

- vê uma task suspeita
- vê Run Key estranha
- vê serviço novo

---

## Sua decisão:

- persistência confirmada
- máquina comprometida
- escalar incidente

---

# 8. Erros comuns

- só olhar malware e ignorar persistência
- não correlacionar com logs
- não validar com Autoruns
- assumir que “remover arquivo resolveu”

---

# 9. Resultado que você precisa atingir

Você deve conseguir:

- identificar persistência no host
- usar Autoruns para encontrar
- confirmar com Event IDs
- explicar o que está acontecendo

---

# 10. Exercício (nível SOC)

Agora você como analista:

---

## Cenário

Você abre Autoruns e encontra:

- Entry: `WindowsUpdater`
- Path: `C:\Users\user\AppData\Roaming\updater.exe`
- Tipo: Logon

Event Viewer:

- Event ID 4657 (registry modificado)
- usuário comum

---

## Responda:

1. Isso é suspeito ou normal? 
2. Qual técnica de persistência está sendo usada?
3. Qual o próximo passo como analista?