# O que é um IOC?
Um IOC (indicador de comprometimento) é uma evidência de que alguém pode ter violado a rede ou um endpoint uma organização.

Esses dados não indicam uma ameaça em potencial, mas sinalizam que um ataque como malware, ransomware ou exfiltração de dados já aconteceu.

Durante um ataque, a equipe usa IOCs para eliminar a ameaça e mitigar os danos.

Após a recuperação, os IOCs ajudam uma organização a compreender melhor o que aconteceu, para que a equipe de segurança da organização possa reforçar a segurança e reduzir o risco de outro incidente semelhante.

---
## Exemplos de IOC
Na segurança baseada em **IOC (Indicators of Compromise)**, a equipe de TI monitora o ambiente em busca de sinais de que um ataque pode estar em andamento. Entre os principais indícios estão:

### Anomalias de tráfego
Organizações costumam ter padrões previsíveis de tráfego de rede. Alterações significativas, como:

- Volume incomum de dados saindo da organização  
- Atividade originada de locais atípicos na rede  

podem indicar possível exfiltração de dados ou presença de invasores.

### Tentativas de login incomuns
O comportamento de acesso dos usuários tende a ser previsível. Sinais de alerta incluem:

- Logins em horários incomuns  
- Acessos a partir de regiões geográficas onde a organização não opera  
- Múltiplas tentativas de login malsucedidas na mesma conta  

Falhas repetidas podem indicar tentativa de uso de credenciais roubadas.

### Irregularidades em contas privilegiadas
Contas administrativas são alvos frequentes. Indícios suspeitos incluem:

- Tentativas de elevação de privilégio  
- Comportamento fora do padrão associado a contas administrativas  

Esses eventos podem sinalizar comprometimento interno ou externo.

### Alterações nas configurações dos sistemas
Malwares frequentemente modificam configurações para facilitar ataques, como:

- Habilitar acesso remoto  
- Desativar softwares de segurança  

Mudanças inesperadas devem ser investigadas imediatamente.

### Instalações ou atualizações inesperadas de software
Ataques muitas vezes começam com a instalação de:

- Malware  
- Ransomware  

Monitorar softwares instalados ou atualizados sem autorização ajuda a identificar rapidamente um comprometimento.

### Múltiplas solicitações para o mesmo arquivo
Diversas tentativas de acesso a um único arquivo podem indicar tentativa de roubo ou exploração de dados sensíveis.

### Solicitações incomuns de DNS
Alguns ataques utilizam infraestrutura de comando e controle (C2), na qual o malware se comunica com um servidor externo controlado pelo atacante.  

Consultas DNS incomuns ou frequentes para domínios suspeitos podem indicar que um sistema interno está comprometido e se comunicando com um invasor.

---
## Como identificar IOCs
Os **Indicadores de Comprometimento (IOCs)** geralmente deixam rastros em arquivos de log e sistemas. As equipes de segurança monitoram continuamente esses registros em busca de atividades suspeitas.

Ferramentas modernas como **SIEM** e **XDR** utilizam IA e aprendizado de máquina para:

- Estabelecer uma linha de base do comportamento normal da organização  
- Detectar anomalias automaticamente  
- Gerar alertas para investigação  

Além da tecnologia, os funcionários também desempenham papel importante. Treinamentos de conscientização em segurança ajudam a:

- Identificar e-mails suspeitos  
- Evitar downloads maliciosos  
- Reportar atividades incomuns rapidamente  

---
## Por que os IOCs são importantes
O monitoramento de IOCs é essencial para reduzir riscos de segurança. A identificação precoce permite:

- Resposta mais rápida a incidentes  
- Redução de tempo de inatividade  
- Minimização de impactos financeiros e operacionais  
- Melhor entendimento das vulnerabilidades existentes  

---
## Como responder a um IOC
Após identificar um indicador de comprometimento, a organização deve agir rapidamente. As principais etapas incluem:

### 1. Estabelecer um plano de resposta a incidentes
Um plano formal define:

- O que caracteriza um incidente  
- Papéis e responsabilidades  
- Etapas de contenção e resolução  
- Diretrizes de comunicação interna e externa  

Isso reduz o caos durante situações críticas.

### 2. Isolar sistemas comprometidos
Aplicações ou dispositivos afetados devem ser rapidamente isolados da rede para evitar movimentação lateral do invasor.

### 3. Realizar análise forense
A investigação busca identificar:

- Origem do ataque  
- Tipo de ameaça  
- Extensão do comprometimento  
- Objetivos do invasor  

Após a recuperação, novas análises ajudam a identificar vulnerabilidades exploradas.

### 4. Eliminar a ameaça
Remoção de malware, credenciais comprometidas e, se necessário, desativação temporária de sistemas afetados.

### 5. Implementar melhorias
Após o incidente, é fundamental revisar:

- Processos e políticas  
- Controles técnicos  
- Estratégias de prevenção  

O objetivo é reduzir a probabilidade de recorrência.

---
## Soluções de IOC
A maioria das violações deixa evidências nos logs. Soluções como SIEM e XDR utilizam automação e correlação de eventos para identificar padrões suspeitos rapidamente.

Um plano estruturado de resposta a incidentes, aliado ao monitoramento contínuo de IOCs, aumenta significativamente a capacidade da organização de detectar, conter e neutralizar ataques antes que causem danos relevantes.