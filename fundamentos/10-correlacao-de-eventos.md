# Correlação de Eventos  
  
## Objetivo  
  
Aprender a:  
  
- Conectar eventos aparentemente isolados  
- Identificar padrões de ataque  
- Construir uma timeline coerente  
- Transformar logs brutos em narrativa de incidente  
  
Correlação é o que separa leitura de log de investigação real.  
  
---  
  
# 1. O que é Correlação de Eventos?  
  
Correlação é o processo de conectar múltiplos eventos relacionados para identificar:  
  
- Um ataque em andamento  
- Um comprometimento passado  
- Uma cadeia completa de ações maliciosas  
  
Evento isolado:

Login falhou

  
Evento correlacionado:

Login falhou 20x  
Login bem-sucedido  
Download de arquivo  
Execução de PowerShell  
Conexão externa

  
Isso já é um incidente.  
  
---  
  
# 2. Por que Correlação é Necessária?  
  
Ataques modernos raramente aparecem como um único evento crítico.  
  
Eles se manifestam como:  
  
- Pequenas anomalias  
- Eventos legítimos fora de contexto  
- Ações distribuídas ao longo do tempo  
  
Sem correlação, você vê ruído.  
Com correlação, você vê o ataque.  
  
---  
  
# 3. Tipos de Correlação  
  
## 3.1 Temporal  
  
Eventos próximos no tempo.  
  
Exemplo:  

10:01 - 50 logins falhos  
10:02 - Login bem-sucedido

  
Alta probabilidade de brute force.  
  
---  
  
## 3.2 Por Entidade  
  
Relacionar eventos pelo mesmo:  
  
- Usuário  
- IP  
- Host  
- Processo  
  
Exemplo:  

User: maria  
Login incomum  
Download suspeito  
Upload de grande volume

  
Todos vinculados à mesma identidade.  
  
---  
  
## 3.3 Por Cadeia Técnica  
  
Relacionar eventos pelo fluxo técnico:  
  
- Processo cria outro processo  
- Processo abre conexão  
- Conexão envia dados  
  
Exemplo:  

explorer.exe  
└─ powershell.exe  
└─ conexão HTTP externa

  
Isso é correlação por encadeamento de execução.  
  
---  
  
## 3.4 Por Indicador (IOC)  
  
Relacionar eventos com:  
  
- Mesmo IP malicioso  
- Mesmo domínio  
- Mesmo hash  
- Mesmo User-Agent  
  
Exemplo:  

Host A → conecta para 5.252.x.x  
Host B → conecta para 5.252.x.x  
Host C → conecta para 5.252.x.x

  
Isso sugere comprometimento múltiplo.  
  
---  
  
# 4. Construindo uma Timeline de Incidente  
  
Timeline é a reconstrução cronológica do ataque.  
  
## Passo 1 – Ordenar por Tempo  
  
Organize eventos por timestamp.  

08:41 - Usuário acessa site suspeito  
08:42 - Download de arquivo  
08:43 - Execução de PowerShell  
08:44 - Conexão para IP externo  
08:46 - Transferência de dados

  
---  
  
## Passo 2 – Identificar Fases do Ataque  
  
Mapeie mentalmente para fases:  
  
- Acesso inicial  
- Execução  
- Persistência  
- Comunicação C2  
- Exfiltração  
  
---  
  
## Passo 3 – Conectar Causa e Efeito  
  
Pergunte:  
  
- Esse evento causou o próximo?  
- Ou são independentes?  
  
Exemplo:  

Download → Execução → Conexão externa

  
Sequência lógica.  
  
---  
  
# 5. Exemplo Prático de Correlação  
  
Imagine os seguintes logs separados:  
  
### Log 1

User: joao  
Login failed x30

  
### Log 2

User: joao  
Login success

  
### Log 3

Process: powershell.exe

  
### Log 4

Outbound connection to 185.x.x.x

  
Isoladamente:  
Nada conclusivo.  
  
Correlacionados:  
Possível comprometimento via brute force seguido de execução e callback.  
  
---  
  
# 6. Erros Comuns na Correlação  
  
## 6.1 Analisar evento isolado  
  
Sem contexto temporal e comportamental.  
  
## 6.2 Ignorar baseline  
  
Nem todo login fora de horário é ataque.  
  
## 6.3 Correlacionar apenas por proximidade  
  
Eventos próximos no tempo nem sempre estão relacionados.  
  
---  
  
# 7. Mentalidade de Analista  
  
Sempre pergunte:  
  
- O que aconteceu antes?  
- O que aconteceu depois?  
- Quem mais fez algo parecido?  
- Isso já ocorreu antes?  
- Existe padrão repetitivo?  
  
---  
  
# 8. Modelo Mental de Investigação  
  
Use este framework:  
  
1. Identifique a anomalia.  
2. Busque eventos relacionados à mesma entidade.  
3. Amplie a janela de tempo.  
4. Construa sequência lógica.  
5. Classifique como:  
   - Falso positivo  
   - Atividade legítima incomum  
   - Incidente real  
  
---  
  
# 9. Aplicando ao seu contexto (PCAP anterior)  
  
Exemplo baseado no que você analisou:  

Usuário baixa software fake  
PowerShell é executado  
Conexão HTTP para IP externo  
Requisições periódicas para mesmo IP

  
Correlacionando:  
  
- Acesso inicial  
- Execução  
- Comunicação C2  
  
Isso deixa de ser tráfego estranho.  
Vira uma timeline de comprometimento.  
  
---  
  
# 10. Conclusão  
  
Correlação de eventos é a habilidade de:  
  
- Transformar logs em história  
- Conectar pontos técnicos  
- Identificar sequência de ataque  
- Construir evidência estruturada  
  
Sem correlação, você enxerga eventos.  
Com correlação, você enxerga incidentes.