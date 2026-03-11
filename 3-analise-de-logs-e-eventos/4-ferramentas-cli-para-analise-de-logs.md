# Analise de Dados e Parsing de Logs via CLI

Este guia aborda o uso de ferramentas de processamento de texto para analise massiva de logs em investigacoes de seguranca.

## 1. grep (Global Regular Expression Print)
Utilizado para filtragem de padroes.
- Filtrar por termo: grep "critico" arquivo.log
- Contar ocorrencias: grep -c "Failed" arquivo.log
- Excluir termo: grep -v "127.0.0.1" arquivo.log

## 2. cut (Corte de Campos)
Utilizado para isolar colunas especificas quando o delimitador e fixo.
- Isolar IPs (assumindo coluna 3 e delimitador ponto-e-virgula):
cut -d';' -f3 arquivo.log

## 3. awk (Processamento de Dados)
Mais robusto que o cut, ideal para logs com espacamento irregular.
- Imprimir colunas especificas: awk '{print $1, $4}' arquivo.log
- Filtragem condicional (ex: imprimir se a coluna 5 for maior que 100):
awk '$5 > 100 {print $0}' arquivo.log

## 4. sort e uniq (Ordenacao e Contagem)
Fundamentais para gerar estatisticas de ataque e identificar anomalias.
- Gerar lista de IPs unicos que acessaram o sistema:
cat acesso.log | awk '{print $1}' | sort | uniq

- Ranking de tentativas de login (Top 10 atacantes):
grep "Failed" auth.log | awk '{print $11}' | sort | uniq -c | sort -nr | head -n 10

## 5. sed (Stream Editor)
Utilizado para substituicao e limpeza de strings em massa.
- Substituir "DEBUG" por "INFO": sed 's/DEBUG/INFO/g' sistema.log
- Deletar linhas vazias: sed '/^$/d' arquivo.log

## 6. Pipeline de Investigacao (Caso Real)
Para identificar qual usuario foi alvo do maior numero de tentativas de forca bruta:
grep "Failed password" /var/log/auth.log | awk '{print $9}' | sort | uniq -c | sort -nr

- Passo 1: Busca falhas.
- Passo 2: Extrai o nome do usuario.
- Passo 3: Agrupa e conta.
- Passo 4: Ordena do mais visado para o menos visado.