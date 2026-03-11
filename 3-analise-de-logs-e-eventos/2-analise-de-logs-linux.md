# Analise de Logs de Autenticacao e Auditoria em Sistemas Linux

Este documento descreve procedimentos operacionais para analise de seguranca (Blue Team) focada em logs de autenticacao e utilizacao de privilegios.

## 1. Localizacao dos Arquivos de Log
Dependendo da distribuicao Linux, os registros de autenticacao sao armazenados em caminhos distintos:
- Familias Debian/Ubuntu: /var/log/auth.log
- Familias RHEL/CentOS/Fedora: /var/log/secure

## 2. Monitoramento em Tempo Real (Live Monitoring)
A visualizacao continua permite identificar picos de atividade anomala no exato momento da ocorrencia.

Comando:
sudo tail -f /var/log/auth.log

Uso do parametro -n para visualizar historico imediato:
sudo tail -n 50 /var/log/auth.log

## 3. Filtragem de Tentativas de Invasao (Brute Force)
O filtro por "Failed password" isola tentativas de autenticacao que nao obtiveram sucesso, permitindo identificar o alvo (usuario) e a origem (IP).

Comando basico:
grep "Failed password" /var/log/auth.log

Comando para extracao de estatisticas de IPs atacantes (Top Offenders):
grep "Failed password" /var/log/auth.log | awk '{print $(NF-3)}' | sort | uniq -c | sort -nr

Comando para identificar usuarios inexistentes (Invalid User):
grep "Invalid user" /var/log/auth.log

## 4. Auditoria de Elevacao de Privilegios (Sudo Usage)
A analise do binario sudo e critica para detectar movimentacao lateral ou abuso de privilegios por usuarios internos ou contas de servico comprometidas.

Filtragem de comandos executados via sudo:
grep "COMMAND=" /var/log/auth.log

Filtragem de tentativas de uso do sudo por usuarios nao autorizados:
grep "not in sudoers" /var/log/auth.log

## 5. Analise de Logins Bem-Sucedidos
Apos um ataque de forca bruta, e necessario verificar se houve sucesso em algum momento (Accepted password).

Comando:
grep "Accepted password" /var/log/auth.log

Verificacao de sessoes abertas por TTY (Terminal):
grep "session opened" /var/log/auth.log

## 6. Analise de Logs Compactados (Log Rotation)
Sistemas Linux rotacionam logs para economizar espaco. Arquivos antigos sao terminados em .gz. Para analisa-los sem descompactar, utiliza-se a suite ztools.

Comando:
sudo zgrep "Failed password" /var/log/auth.log.2.gz

## 7. Padroes de Identificacao (IOCs - Indicators of Compromise)
- Multiplas falhas seguidas de um "Accepted password" para o mesmo usuario.
- Logins em horarios nao comerciais para contas administrativas.
- Uso do comando sudo por contas de servico (ex: www-data, apache, mysql).
- Tentativas de login para usuarios comuns de sistema (bin, daemon, sync).