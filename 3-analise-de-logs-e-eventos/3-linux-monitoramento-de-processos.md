# Analise de Processos e Identificacao de Anomalias em Linux

Este guia descreve o uso de ferramentas nativas e interativas para deteccao de processos maliciosos, vazamento de recursos e execucao de codigos nao autorizados.

## 1. Utilitario ps (Process Status)
O comando ps aux e a base para auditoria estatica de processos em execucao.

Comando para listagem completa:
ps aux

Principais colunas para analise de seguranca:
- USER: Identifica se processos criticos estao rodando com privilegios indevidos.
- %CPU / %MEM: Indica consumo anormal de recursos (possivel DoS ou Mineracao).
- COMMAND: Mostra o caminho do binario e argumentos utilizados.

Busca por processos ocultos ou em diretorios temporarios:
ps aux | grep -E "/tmp/|/dev/shm/|/var/tmp/"

## 2. Monitoramento em Tempo Real com top
O top e utilizado para diagnostico rapido de performance e sequestro de processamento.

Interacoes em tempo real:
- P: Ordenar por utilizacao de processador.
- M: Ordenar por utilizacao de memoria residente.
- u: Filtrar processos de um usuario especifico (ex: u www-data).

Indicadores de Compromisso (IOC):
- Processos com nomes aleatorios (ex: jh73ks9).
- Processos que utilizam 100% da CPU de forma constante.

## 3. Analise Estrutural com htop
O htop oferece uma interface ncurses que facilita a visualizacao da hierarquia de execucao.

Funcionalidades de Auditoria:
- F5 (Tree View): Essencial para identificar a origem de um processo. Um processo bash originado de um servidor Apache/Nginx e um indicador critico de intrusao.
- F6 (Sort): Permite alternar rapidamente entre criterios de prioridade.
- F9 (Kill): Permite encerrar processos suspeitos diretamente da interface.

## 4. Verificacao de Processos sem Arquivos (Fileless)
Atacantes podem deletar o binario original apos a execucao para evitar deteccao por scanners de disco.

Comando para identificar binarios deletados ainda em execucao:
ls -al /proc/*/exe | grep "deleted"

## 5. Checklist de Investigacao para o Blue Team
1. O usuario que executa o processo tem permissao para tal?
2. O binario esta localizado em um diretorio padrao (/usr/bin, /usr/sbin)?
3. O processo pai (PPID) e legitimo?
4. Existem conexoes de rede associadas a este processo? (Uso complementar de netstat/ss).