Você recebe:

- um alerta do usuário (“email estranho”)
- ou detecção automática (email gateway)

Sua missão não é “ler o email”.

É responder:

> Isso é phishing real (TP) ou falso positivo (FP)?  
> Existe risco ou comprometimento?

---

# Visão geral do fluxo de análise

Sempre pense assim:

1. **Analisar o cabeçalho (origem real)**
2. **Analisar o conteúdo (engenharia social)**
3. **Analisar URL (destino real)**
4. **Analisar anexo (se houver)**
5. **Correlacionar tudo → decisão**

---

# 1. Análise de Header (onde a verdade está)

O corpo do email mente.  
O header quase nunca.

---

## Campos críticos que você precisa dominar

### 1. From vs Reply-To

- **From**: quem “aparentemente” enviou
- **Reply-To**: para onde a resposta vai

### Como analisar

Se você vê:

- From: suporte@empresa.com
- Reply-To: atacante@gmail.com

Isso é um forte indicador de phishing.

---

## 2. X-Originating-IP

Mostra o IP de origem real.

### O que você faz:

- pega o IP
- consulta reputação (VirusTotal, AbuseIPDB)

### Interpretação:

- IP de datacenter estranho → suspeito
- IP fora do país esperado → suspeito

---

## 3. Received (cadeia de entrega)

Esse é o mais poderoso — e ignorado por iniciantes.

Ele mostra o caminho do email.

Você lê de baixo para cima.

### O que procurar:

- servidores estranhos
- origens inconsistentes
- países inesperados

---

## 4. SPF (Sender Policy Framework)

Diz se o servidor está autorizado a enviar email por aquele domínio.

- **pass** → ok (mas não garante legitimidade)
- **fail** → forte suspeita

---

## 5. DKIM

Assinatura digital do email.

- **pass** → conteúdo não foi alterado
- **fail** → possível spoofing

---

## 6. DMARC

Política do domínio baseada em SPF/DKIM.

- **pass** → alinhamento correto
- **fail** → problema sério

---

## Interpretação real (muito importante)

|SPF|DKIM|DMARC|Significado|
|---|---|---|---|
|fail|fail|fail|quase certeza de phishing|
|pass|fail|fail|suspeito|
|pass|pass|pass|pode ser legítimo (mas ainda analise conteúdo)|

---

# 2. Análise de URL (onde acontece o ataque)

Nunca confie no que está visível.

---

## Técnica 1: Hover (ver destino real)

Você olha o link real:

Exemplo:

- Texto: microsoft.com
- Link real: micros0ft-login.ru

Isso é **typosquatting**.

---

## Técnica 2: sinais clássicos

- domínios estranhos (.ru, .xyz, etc.)
- subdomínios enganosos:
    - microsoft.login.security.com
- encurtadores:
    - bit.ly, tinyurl

---

## Técnica 3: Ferramentas

Você NÃO clica direto.

Você usa:

- URLscan → ver comportamento
- sandbox → ver redirecionamento
- reputação (VirusTotal)

---

# 3. Análise de Anexo

Se tiver anexo, risco sobe muito.

---

## Passo 1: gerar hash

- SHA256 do arquivo

---

## Passo 2: consultar no VirusTotal

Resultados possíveis:

- detectado → malware confirmado
- limpo → ainda pode ser zero-day

---

## Tipos suspeitos

- .exe, .js, .scr
- .docm (macro)
- .zip com executável dentro

---

# 4. Ferramentas práticas

Você mencionou algumas — vou organizar como usar:

---

## URLscan

- mostra:
    - redirecionamentos
    - IP final
    - screenshots

---

## VirusTotal

- IP
- domínio
- hash

---

## PhishTool

- análise estruturada de phishing
- útil para documentação

---

# 5. Como concluir (TP vs FP)

Agora vem o que importa: decisão.

---

## Phishing verdadeiro (TP)

Evidências:

- SPF/DKIM/DMARC falhando
- domínio suspeito
- link malicioso
- engenharia social clara

---

## Falso positivo (FP)

Evidências:

- autenticações OK
- domínio legítimo
- conteúdo esperado
- sem comportamento malicioso

---

# 6. Exemplo real (como você deve pensar)

Você recebe:

- From: suporte@microsoft.com
- Reply-To: suporte-login@gmail.com
- SPF: fail
- Link: micros0ft-security.ru

---

## Sua conclusão:

- spoofing claro
- domínio malicioso
- falha de autenticação

> Classificação: phishing (TP)

---

# 7. Erros comuns (isso derruba iniciante)

- confiar só no “From”
- não analisar header
- clicar direto no link
- ignorar SPF/DKIM/DMARC
- parar na primeira evidência

---

# 8. Resultado que você precisa alcançar

Dado um email, você consegue:

- analisar header
- validar URL
- verificar anexo
- juntar evidências
- decidir TP ou FP

---

# Agora vamos testar (nível SOC)

## Cenário

Você recebeu:

**From:** suporte@microsoft.com  
**Reply-To:** suporte@micr0soft-help.ru

**SPF:** fail  
**DKIM:** fail  
**DMARC:** fail

**X-Originating-IP:** 45.77.XX.XX (VPS Holanda)

**Link visível:** [https://microsoft.com/security](https://microsoft.com/security)  
**Link real:** http://micr0soft-login.ru/auth

---

## Pergunta

Responda como analista:

1. Classificação (TP ou FP)
2. Liste 3 evidências técnicas
3. Qual ação imediata

Resposta:

1. True Positive
2. 
	1. From diferente do Reply-to indica spoofing que é ainda mais suspeito quando analisamos o Reply-to e confirmamos que utiliza a técnica typosquatting.
	2. Todas as verificações falharam - o SPF (confirma que o servidor pode enviar e-mails pelo domínio), DKIM (assinatura digital do e-mail) e DMARC (política de alinhamento entre SPF e DKIM) - todos falharam, forte indício de envio de e-mail não legítimo.
	3. Não pudemos verificar o endereço IP, mas a localização pode ser suspeita.
	4. Botão utilizando técnica de typosquatting novamente, exibindo URL legítima mas redirecionando para domínio suspeito com typosquatting.
3. Como próximas ações, eu as faria na seguinte ordem:
	1. Verificar se houve clique no link e posteriormente se houve exposição de credenciais pelo usuário
	2. Caso sim, revogar permissões do usuário, alterar credenciais de acesso e aplicar MFA forçado, além de forçar o de-auth.
	3. Caso sim e caso não também: bloquear domínio suspeito, endereço de IP da VPS.