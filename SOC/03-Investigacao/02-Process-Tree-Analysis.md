# 1. O que é uma Process Tree?

Uma process tree (árvore de processos) mostra:

> quem iniciou quem

Todo processo no Windows nasce de outro processo.

Exemplo:

```
explorer.exe └── chrome.exe
```

Significa:

- o Windows Explorer iniciou o Chrome
- normalmente porque o usuário clicou no navegador

---

# Mentalidade correta

Quando você olha uma árvore de processos, a pergunta NÃO é:

> “esse processo é malicioso?”

A pergunta é:

> “faz sentido esse processo ter iniciado esse outro processo?”

Isso muda tudo.

---

# 2. Parent Process vs Child Process

## Parent Process

Processo que criou outro processo.

## Child Process

Processo criado.

---

## Exemplo normal

```
explorer.exe └── notepad.exe
```

Usuário abriu o Bloco de Notas.

Totalmente normal.

---

# 3. Por que isso é tão importante?

Porque malware raramente aparece como:

```
malware.exe
```

Ele geralmente usa:

- LOLBins
- PowerShell
- cmd
- wscript
- rundll32

Ou seja:

> ferramentas legítimas do Windows

Então o SOC precisa detectar:

- contexto
- comportamento
- relação entre processos

---

# 4. Processos normais do Windows (MUITO importante)

Você precisa construir uma “baseline mental”.

---

# 4.1 services.exe

## O que é

Service Control Manager.

Responsável por iniciar serviços do Windows.

---

## Relação normal

```
services.exe └── svchost.exe
```

---

## Por quê?

O `svchost.exe` hospeda serviços DLL do Windows.

---

## Isso é extremamente comum e legítimo

Você verá isso milhares de vezes.

---

# 4.2 explorer.exe

## O que é

Shell do Windows (interface do usuário).

---

## Relações normais

```
explorer.exe ├── chrome.exe ├── notepad.exe ├── excel.exe └── cmd.exe
```

---

## Significado

Usuário abriu algo manualmente.

---

# 4.3 wininit.exe

Inicializa processos críticos do sistema.

---

## Relações normais

```
wininit.exe ├── services.exe ├── lsass.exe └── lsaiso.exe
```

---

# 4.4 lsass.exe

Local Security Authority.

Responsável por autenticação.

---

## IMPORTANTE

`lsass.exe` normalmente NÃO cria processos filhos.

Se criar:

> enorme red flag

---

# 4.5 svchost.exe

Hospeda serviços.

---

## Relações normais

```
services.exe └── svchost.exe
```

---

## Relações suspeitas

```
explorer.exe └── svchost.exe
```

Extremamente estranho.

---

# 5. O que é red flag?

Agora entra a parte mais importante.

---

# 5.1 Office → Script Engine

## Exemplo

```
winword.exe └── cmd.exe      └── powershell.exe
```

---

# Por que isso é suspeito?

Word NÃO deveria:

- abrir cmd
- abrir PowerShell

---

# O que isso geralmente significa?

Macro maliciosa.

Fluxo clássico:

1. usuário abre documento
2. macro executa cmd
3. cmd executa PowerShell
4. PowerShell baixa payload

---

# Isso aparece MUITO

Você precisa reconhecer instantaneamente.

---

# 5.2 Excel → wscript.exe

```
excel.exe └── wscript.exe
```

---

# Por que suspeito?

Excel normalmente:

- abre planilhas
- NÃO executa scripts VBScript

---

# O que malware faz?

Usa:

- VBA
- scripts
- payloads HTA/VBS

---

# 5.3 Outlook → powershell.exe

```
outlook.exe └── powershell.exe
```

Muito suspeito.

Pode indicar:

- phishing
- macro
- payload automático

---

# 5.4 Browser → cmd.exe

```
chrome.exe └── cmd.exe
```

Depende do contexto.

Pode ser:

- legítimo (download tool, dev environment)
- malware

Aqui entra:

- command line
- usuário
- horário
- argumentos

---

# 6. Command Line Analysis (ESSENCIAL)

Agora chegamos na parte que separa analista bom de iniciante.

---

# Processo sozinho NÃO basta

Você precisa olhar:

```
Command Line
```

---

# Exemplo normal

```
powershell.exe
```

Pouca informação.

---

# Exemplo suspeito

```
powershell.exe -enc SQBFAFgA...
```

---

# O que é isso?

Base64 encoded command.

MUITO usado por malware.

---

# Outros argumentos suspeitos

## PowerShell

```
-nop-w hidden-enc-exec bypass
```

---

## Significados

|Argumento|Significado|
|---|---|
|-nop|sem profile|
|-w hidden|janela oculta|
|-enc|comando codificado|
|-exec bypass|bypass de política|

---

# Isso é MUITO importante

Porque:

> PowerShell legítimo existe

Mas:

> PowerShell com esses argumentos = altamente suspeito

---

# 7. Processo + Parent + Command Line

A análise real junta tudo.

---

# Exemplo completo

```
winword.exe └── powershell.exe -enc ...
```

---

# O que um analista pensa?

- Word iniciou PowerShell
- comando encoded
- comportamento anômalo

Conclusão:

> provável macro maliciosa

---

# 8. Process Trees legítimas comuns

Agora vou te mostrar padrões IMPORTANTES.

---

# Navegação normal

```
explorer.exe └── chrome.exe
```

---

# Abrindo Office

```
explorer.exe └── winword.exe
```

---

# Serviços Windows

```
services.exe └── svchost.exe
```

---

# Abrindo terminal manualmente

```
explorer.exe └── cmd.exe
```

---

# Admin legítimo

```
mmc.exe └── powershell.exe
```

Pode ser legítimo.

Contexto importa.

---

# 9. Red flags MUITO conhecidas

## Office spawning scripting engines

```
winword.exe → powershell.exeexcel.exe → wscript.exe
```

---

## Browser spawning scripts

```
chrome.exe → powershell.exe
```

---

## LOLBins suspeitos

```
rundll32.exeregsvr32.exemshta.execertutil.exe
```

Esses aparecem MUITO em malware.

---

# 10. Como isso aparece no SOC

Você recebe alerta:

> Suspicious Child Process

---

# Seu trabalho

Perguntas mentais:

1. Esse parent faz sentido?
2. Esse child faz sentido?
3. Command line é normal?
4. Usuário faria isso?
5. Parece automação/macro/script?

---

# 11. Exemplo real de análise

---

## Alerta

```
excel.exe └── powershell.exe -enc ...
```

---

# Investigação

## Parent

Excel

## Child

PowerShell

## Command line

Encoded Base64

---

# Conclusão

Comportamento altamente suspeito compatível com execução de macro maliciosa.