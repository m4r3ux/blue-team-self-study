# Identificação de Anomalias em Logs  
  
## Objetivo  
  
Aprender a reconhecer:  
  
- Padrões normais (baseline)  
- Comportamentos suspeitos  
- Outliers (eventos fora da curva)  
- Sequências indicativas de ataque  
  
---  
  
# 1. O que é Baseline?  
  
Baseline é o padrão normal de comportamento de um ambiente.  
  
Sem baseline, tudo parece suspeito.  
Com baseline, você identifica o que realmente foge do esperado.  
  
## Exemplos de Baseline  
  
### Usuário  
- Horário comum de login: 08:00–18:00  
- Localização comum: Brasil  
- IP frequente: 200.x.x.x  
- Dispositivo padrão: mesmo host  
  
### Servidor  
- Portas abertas: 80 e 443  
- Comunicação apenas com rede interna  
- Volume médio de tráfego: 2 GB/dia  
  
### Rede  
- Média de 100 conexões SSH por dia  
- DNS interno resolvendo apenas domínios corporativos  
  
Tudo isso forma o padrão normal.  
  
---  
  
# 2. O que é Anomalia?  
  
Anomalia é qualquer comportamento que foge significativamente do baseline.  
  
Ela pode ser:  
  
- Técnica  
- Comportamental  
- Temporal  
- Geográfica  
- Volumétrica  
  
Importante: anomalia não é sinônimo de ataque.    
É apenas algo fora do padrão que merece investigação.  
  
---  
  
# 3. Tipos de Anomalias  
  
## 3.1 Temporal  
  
Evento fora do horário habitual.  
  
Exemplo:  

User: maria  
Login: 03:12 AM

  
Se Maria nunca acessa nesse horário, isso é uma anomalia temporal.  
  
---  
  
## 3.2 Geográfica  
  
Login ou tráfego vindo de local incomum.  
  
Exemplo:  

User: joao  
Country: Russia

  
Se o ambiente opera apenas no Brasil, isso é anomalia geográfica.  
  
---  
  
## 3.3 Volume  
  
Explosão ou queda abrupta na quantidade de eventos.  
  
Exemplo normal:  

20 tentativas de login por hora

  
Exemplo anômalo:  

500 tentativas de login em 2 minutos

  
Pode indicar brute force.  
  
---  
  
## 3.4 Comportamental  
  
Mudança no padrão de ação de um usuário ou sistema.  
  
Exemplo:  
  
- Usuário de RH executando PowerShell com parâmetros obscuros.  
- Servidor interno iniciando conexões externas inesperadas.  
  
---  
  
## 3.5 Técnica  
  
Uso incomum de portas, protocolos ou processos.  
  
Exemplo:  
  
- DNS sendo usado para tráfego de alto volume.  
- HTTP com payload binário inesperado.  
- Processo `powershell.exe` abrindo conexão para IP externo.  
  
---  
  
# 4. O que é Outlier?  
  
Outlier é um evento extremamente fora da curva estatística.  
  
Pode ser:  
  
- Muito raro  
- Muito intenso  
- Muito diferente do comportamento médio  
  
Exemplo:  

Upload: 15GB  
Usuário comum

  
Se a média diária é 100MB, isso é um outlier claro.  
  
---  
  
# 5. Como Pensar Como Analista  
  
Sempre faça estas perguntas:  
  
## 5.1 Quem?  
  
- Quem gerou o evento?  
- Esse comportamento é comum para essa entidade?  
  
## 5.2 Quando?  
  
- O horário é habitual?  
- O dia é comum (fim de semana, feriado)?  
  
## 5.3 Para onde?  
  
- O destino é comum?  
- Já houve comunicação anterior com esse IP/domínio?  
  
## 5.4 Com que frequência?  
  
- Isso ocorre diariamente?  
- Foi um evento isolado?  
  
## 5.5 Qual o contexto?  
  
- Houve tentativas falhas antes?  
- Houve download antes?  
- Houve execução de processo suspeito depois?  
  
Análise isolada é fraca.  
Análise contextual é forte.  
  
---  
  
# 6. Sequência de Eventos (Análise em Cadeia)  
  
Ataques raramente são um único evento.  
  
Exemplo de cadeia suspeita:  

20 logins falhos  
1 login bem-sucedido  
Download de arquivo  
Execução de PowerShell  
Conexão HTTP para IP externo

  
Cada evento isolado pode parecer pouco grave.  
Em sequência, forma um padrão claro de comprometimento.  
  
---  
  
# 7. Diferença Entre Evento Suspeito e Evento Anômalo  
  
Evento suspeito:  
- Já é conhecido como malicioso.  
  
Evento anômalo:  
- Apenas foge do padrão.  
- Precisa ser investigado.  
  
Toda atividade maliciosa começa como uma anomalia.  
Nem toda anomalia é maliciosa.  
  
---  
  
# 8. Como Treinar Identificação de Anomalias  
  
## Passo 1 — Observe logs normais  
  
Antes de procurar ataques, entenda:  
  
- O que é comum?  
- Qual o volume médio?  
- Quais horários são normais?  
  
## Passo 2 — Compare  
  
Sempre compare o evento atual com o padrão histórico.  
  
## Passo 3 — Procure extremos  
  
- Horários incomuns  
- Países incomuns  
- Portas incomuns  
- Processos incomuns  
- Picos abruptos  
  
## Passo 4 — Analise encadeamento  
  
Pergunte:  
  
- O que aconteceu antes?  
- O que aconteceu depois?  
  
---  
  
# 9. Aplicação Prática (Exercício Mental)  
  
Dado o log:  

User: carlos  
Login: 02:47 AM  
IP: 185.234.1.9  
Country: Russia  
Result: Success

  
Perguntas para análise:  
  
- Carlos costuma acessar nesse horário?  - possivelmente não.
- Já houve acesso da Rússia?   - se a empresa é Brasileira, não.
- Houve falhas antes?  - sim.
- O que ele fez após o login? - possivelmente exfiltrou.  
  
Se isso foge do padrão, é anomalia.  
Se houver ações suspeitas depois, pode ser comprometimento.  
  
---  
  
# Conclusão  
  
Identificação de anomalias não é decorar regras.  
  
É:  
  
1. Conhecer o padrão.  
2. Detectar desvios.  
3. Avaliar contexto.  
4. Correlacionar eventos.  
5. Investigar antes de concluir.  
  
A habilidade real está em entender o comportamento normal primeiro.