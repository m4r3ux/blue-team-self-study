# Linux - Análise de Logs em /var/log/

## 1. Estrutura de Logs no Linux

No Linux, a maioria dos eventos relevantes para SOC está em:

- /var/log/auth.log  → Autenticação (SSH, sudo, login)
- /var/log/syslog    → Eventos gerais do sistema
- /var/log/messages  → Similar ao syslog (dependendo da distro)
- /var/log/secure    → Autenticação (em distros como CentOS/RHEL)

Esses arquivos são texto puro.
A análise é feita via terminal com comandos de processamento textual.

Diferente do PowerShell, aqui você trabalha com texto bruto.

---

## 2. tail — Monitoramento em Tempo Real

### Visualizar últimas linhas

tail /var/log/auth.log

Por padrão, mostra as últimas 10 linhas.

### Monitorar em tempo real

tail -f /var/log/auth.log

Uso em SOC:
- Ver tentativas de SSH acontecendo ao vivo
- Acompanhar brute force em tempo real
- Monitorar sudo sendo utilizado

Ctrl + C para sair.

---

## 3. less — Navegação Controlada

less /var/log/auth.log

Permite:
- Navegar com setas
- Buscar usando /palavra
- Ir para o final com Shift + G
- Sair com q

Uso em SOC:
- Investigar manualmente eventos passados
- Procurar padrões específicos
- Navegar grandes arquivos sem travar o terminal

---

## 4. grep — Filtro de Texto

Ferramenta essencial.

### Buscar por falhas de login SSH

grep "Failed password" /var/log/auth.log

### Buscar por IP específico

grep "192.168.1.10" /var/log/auth.log

### Ignorar maiúsculas/minúsculas

grep -i "failed" /var/log/auth.log

### Contar ocorrências

grep "Failed password" /var/log/auth.log | wc -l

Uso em SOC:
- Detectar brute force
- Buscar atividade de um IP
- Identificar uso de sudo
- Filtrar eventos suspeitos

---

## 5. grep + tail — Investigação Recente

Ver apenas eventos recentes e filtrar:

tail -n 1000 /var/log/auth.log | grep "Failed password"

Isso evita processar o arquivo inteiro.
Boa prática em arquivos grandes.

---

## 6. awk — Extração de Campos

awk trabalha por colunas (separadas por espaço).

Exemplo de linha de auth.log:

Jan 10 10:22:31 server sshd[1234]: Failed password for root from 192.168.1.50 port 53422 ssh2

Para extrair IP (normalmente campo 11):

grep "Failed password" /var/log/auth.log | awk '{print $11}'

Para contar IPs únicos:

grep "Failed password" /var/log/auth.log | awk '{print $11}' | sort | uniq -c | sort -nr

Explicação:
- awk '{print $11}' → extrai coluna do IP
- sort → organiza
- uniq -c → conta ocorrências
- sort -nr → ordena do maior para o menor

Uso em SOC:
Detectar IPs com múltiplas tentativas de login.

---

## 7. sed — Manipulação de Texto

sed altera ou extrai padrões.

Exemplo: mostrar apenas linhas com "Failed password":

sed -n '/Failed password/p' /var/log/auth.log

Exemplo: remover datas do início da linha:

sed 's/^.*sshd/sshd/' /var/log/auth.log

Uso em SOC:
- Limpar saída para relatório
- Ajustar logs para análise
- Remover partes irrelevantes

---

## 8. Análise de Brute Force SSH (Fluxo Completo)

Passo 1: Identificar falhas

grep "Failed password" /var/log/auth.log

Passo 2: Extrair IPs

grep "Failed password" /var/log/auth.log | awk '{print $11}'

Passo 3: Contar tentativas por IP

grep "Failed password" /var/log/auth.log | awk '{print $11}' | sort | uniq -c | sort -nr

Passo 4: Verificar se houve login bem-sucedido

grep "Accepted password" /var/log/auth.log

Isso responde:
- Quem tentou?
- Quantas vezes?
- Conseguiu entrar?

---

## 9. Investigando Uso de sudo

Buscar comandos executados com privilégio:

grep "sudo" /var/log/auth.log

Identificar qual usuário executou:

grep "sudo" /var/log/auth.log | awk '{print $1, $2, $3, $9, $10}'

Uso em SOC:
- Detectar possível privilege escalation
- Identificar abuso de privilégio

---

## 10. Conceitos Fundamentais para Consolidar

- Logs Linux são texto puro
- grep filtra
- tail monitora
- less navega
- awk extrai colunas
- sed manipula padrões
- sort + uniq contam ocorrências
- Combinar comandos via pipe é essencial

Fluxo mental padrão:

Identificar padrão → Filtrar → Extrair campo relevante → Agrupar → Analisar comportamento

Se você domina esse fluxo, consegue investigar 80% dos incidentes básicos em servidores Linux.