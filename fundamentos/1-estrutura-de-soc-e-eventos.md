# SOC (L1, L2, L3)
Um SOC (Security Operations Center) é uma estrutura responsável por:
- Monitorar eventos de segurança
- Detectar ameaças
- Investigar alertas
- Responder a incidentes
- Proteger o ambiente continuamente

É o "time de defesa ativa" da organização.

## Estrutura em Camadas: L1, L2, L3

Pense como níveis de profundidade investigativa.

|Nível|Papel Principal|Perfil Cognitivo|Foco|
|---|---|---|---|
|L1|Triagem|Operacional e analítico|Identificar se é incidente|
|L2|Investigação|Investigativo|Confirmar, analisar impacto|
|L3|Especialista|Estratégico e técnico avançado|Caça ativa, melhoria de detecção|
## SOC L1 - Analista de Triagem
Faz a filtragem do sinal de ruído. O L1 recebe alertas e precisa responder se aquele alerta é um incidente real ou um falso positivo.

O que ele faz exatamente?
- Analisa alertas (ex: brute force)
- Verifica logs (Windows, Linux, firewall)
- Checa IOC
- Classifica severidade
- Decide se escala pra L2
- Documenta tudo

O L1 precisa ter a mentalidade:
- Objetiva
- Rápida
- Baseada em evidência
- Segue o playbook

O L1 não inventa, ele aplica o processo.

Por exemplo, no caso do seguinte alerta:

**100 falhas de login para usuário admin**

O L1 verifica:
- IP externo?
- Logon Type?
- Conta privilegiada?
- Tentativa bem-sucedida depois?

Se houver login sucesso -> escalar.

Problemas comuns de um L1, comumente são:
- Confundir evento com incidente
- Não documentar direito
- Não verificar contexto
- Escalonar tudo por insegurança
- Ignorar baseline

## SOC L2 - Investigador
O L2 confirma que o que foi caracterizado como um incidente pelo L1 realmente é um incidente e mede o impacto.

O L2 realiza:
- Análise aprofundada
- Correlação de eventos
- Linha do tempo
- Verifica movimentação lateral
- Identifica comprometimento real

Ele tem como mentalidade:
- Investigativa
- Questionadora
- Conecta pontos
- Pensa como atacante

Por exemplo, após um brute force confirmado, o L2 verifica:
- Movimento lateral?
- Execução de PowerShell?
- Dump de credenciais?
- Criação de novas contas?
## SOC L3 - Especialista / Threat Hunter
O L3 tem como objetivo elevar a maturidade de segurança, realizando:
- Threat Hunting
- Criação de regras no SIEM
- Ajustes de correlação
- Analise de malware
- Forense digital
- Engenharia reversa

## Como os níveis se conectam?
Fluxo simplificado:

SIEM gera alerta → L1 tria →  
Se relevante → L2 investiga →  
Se complexo → L3 aprofunda / melhora detecção