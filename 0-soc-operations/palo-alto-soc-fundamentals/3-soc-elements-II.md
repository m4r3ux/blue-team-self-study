## SOC Elements II: Interfaces, Visibility & Technology

---

# 1. Interfaces Pillar

O pilar **Interfaces** define **como o SecOps interage com outras equipes da organização**.

Security Operations **não funciona isoladamente**. Para operar corretamente, precisa cooperar com diversos departamentos.

### Objetivos do Pilar Interfaces

- Definir responsabilidades entre equipes
- Reduzir conflitos organizacionais
- Criar processos claros de comunicação
- Integrar segurança aos processos da empresa

Cada interação entre equipes é chamada de **interface**.

---

## Service Level Agreements (SLA)

**SLAs** são acordos que definem:

- Responsabilidades de cada equipe
- Manutenção de ferramentas
- Licenciamento
- Atualizações
- Suporte
- Responsabilidades em ambientes cloud

---

# 2. Equipes que Interagem com SecOps

## Help Desk
Objetivo:  
- Resolver tickets rapidamente.

## IT Operations
Responsável por:
- Disponibilidade da infraestrutura
- Performance dos sistemas

## DevOps
Responsável por:
- Desenvolvimento de aplicações
- Releases rápidos
- Correção de bugs

## Operational Technology (OT)
Responsável por:
- Sistemas industriais
- Infraestrutura operacional

---

# 3. Outras Funções Estratégicas

## Enterprise Architecture

Responsável por:

- Design de redes físicas e virtuais
- Integração da segurança desde a arquitetura
- Documentação da infraestrutura

Objetivo: equilibrar **segurança e necessidades do negócio**.

---

## Governance, Risk and Compliance (GRC)

Responsável por:

- Gestão de risco
- Políticas de segurança
- Compliance regulatório

Exemplos de normas:

- PCI-DSS
- HIPAA
- GDPR

---

## Business Liaison

Função que conecta:

- SecOps
- Áreas de negócio
- Parceiros e fornecedores

Ajuda a traduzir **impactos de segurança para o negócio**.

---

# 4. Equipes de Segurança que Trabalham com SecOps

## Endpoint Security Team

Responsável por:

- Políticas de segurança de endpoints
- EPP (Endpoint Protection Platform)
- EDR (Endpoint Detection and Response)

Os dados coletados por EDR são **extremamente úteis para investigação de incidentes**.

---

## Network Security Team

Responsável por:

- Firewalls
- IDS/IPS
- Monitoramento de rede

---

## Cloud Security Team

Responsável por:

- Segurança de ambientes cloud
- Monitoramento e políticas de acesso

---

# 5. Security Automation

A equipe de automação:

- Mantém **playbooks de automação**
- Implementa novas tecnologias de automação
- Integra ferramentas com processos do SOC

Antes de implementar automação deve-se avaliar:

- Economia de tempo
- Precisão
- Retorno sobre investimento (ROI)
- Custos de manutenção

---

# 6. Forensics & Telemetry

## Forensics

Forense digital é o processo **legalmente válido de coleta e análise de evidências digitais**.

Requisitos:

- Evidência deve ser **repetível**
- Processo **não pode alterar os dados**
- Evidência deve ser **defensável em tribunal**

---

## Telemetry

Telemetry coleta **atividade em tempo real** de sistemas.

Características:

- Cobertura ampla
- Coleta contínua
- Alta velocidade de coleta

É muito usada para:

- triagem
- investigação
- threat hunting

---

# 7. Vulnerability Management

A equipe de vulnerabilidades trabalha com SecOps para:

- Identificar novas vulnerabilidades
- Aplicar controles temporários
- Implementar patches

SecOps precisa ser informado para **interpretar alertas corretamente**.

---

# 8. Visibility Pillar

O pilar **Visibility** garante que o SOC tenha **visibilidade suficiente da infraestrutura**.

Inclui:

- tráfego de rede
- atividade de endpoints
- logs de sistemas
- aplicações
- dados sensíveis

---

## Network Traffic Capture

Captura de tráfego de rede através de:

- Firewalls
- IDS/IPS
- Proxies
- Roteadores
- Switches
- Ferramentas de captura

Isso permite:

- análises detalhadas
- investigações avançadas

---

## Endpoint Data Capture

Dados coletados de dispositivos como:

- Windows
- macOS
- Chrome OS

Tipos de dados coletados:

### Logs
Registram eventos específicos.

### Telemetry
Coleta contínua de atividades.

### Forensics
Dados detalhados usados em investigações.

---

# 9. Cloud Security Visibility

Para ambientes cloud é necessário:

### Policy Enforcement

Aplicação de políticas como:

- Single Sign-On (SSO)
- Autenticação
- Autorização
- Device profiling
- Step-up authentication

---

### Log Collection

Logs são usados para:

- análise de incidentes
- correlação de eventos
- investigações

Devem ser integrados ao **SIEM**.

---

# 10. Application Monitoring & URL Filtering

Permitem:

- monitorar aplicações
- controlar acesso a sites
- detectar comportamento suspeito

---

# 11. Asset, Knowledge & Case Management

## Asset Management
Controle de ativos de TI.

## Knowledge Management
Base de conhecimento para analistas.

## Case Management
Gerenciamento de incidentes e investigações.

---

# 12. Data Loss Prevention (DLP)

DLP previne **vazamento de dados sensíveis**.

Detecta:

- exfiltração de dados
- envio indevido de informações
- vazamento interno

Alertas de DLP são enviados ao **SecOps para investigação**.

---

# 13. Technology Pillar

O pilar **Technology** reúne as ferramentas usadas pelo SOC para detectar e responder a ataques.

---

# 14. Tecnologias Principais

## Firewall
Controla o tráfego de rede baseado em regras.

---

## IDS / IPS

### IDS (Intrusion Detection System)
Detecta ataques.

### IPS (Intrusion Prevention System)
Detecta e bloqueia ataques.

---

## Malware Sandbox

Ambiente isolado para analisar malware de forma segura.

---

# 15. Endpoint Security & Behavioral Analytics

## Endpoint Security

Protege dispositivos como:

- servidores
- laptops
- desktops
- celulares
- tablets

Ferramentas comuns:

- Antivirus
- EDR
- Device control

---

## Behavioral Analytics

Detecta ataques analisando **comportamentos anormais**.

Baseia-se em:

- baseline de comportamento
- comparação com comportamento passado
- machine learning

Pode detectar:

- ransomware
- malware
- movimento lateral
- exfiltração
- insider threats

---

# 16. Email Security & WAF

## Email Security

Protege contra:

- phishing
- malware
- spoofing

Utiliza tecnologias como:

- criptografia
- autenticação de remetente
- DMARC

---

## Web Application Firewall (WAF)

Protege aplicações web contra ataques HTTP como:

- SQL Injection
- XSS
- exploits web

---

# 17. Acesso Seguro

## VPN (Virtual Private Network)

Permite acesso remoto seguro à rede corporativa.

SecOps precisa monitorar:

- tráfego VPN
- comportamento de usuários remotos

---

## Mobile Device Management (MDM)

Gerencia e protege dispositivos móveis.

---

## Network Access Control (NAC)

Controla quais dispositivos podem acessar a rede.

---

## Identity & Access Management (IAM)

Gerencia:

- identidades
- permissões
- autenticação

---

# 18. SIEM

**Security Information and Event Management**

Função:

- coletar logs
- correlacionar eventos
- detectar incidentes

Coleta dados de:

- sistemas
- redes
- aplicações
- ferramentas de segurança

---

# 19. XSIAM

**Extended Security Intelligence and Automation Management**

Evolução do SIEM que adiciona:

- automação
- inteligência de ameaças
- IA
- investigação automatizada

---

# 20. SOAR

**Security Orchestration, Automation and Response**

SOAR permite:

- automatizar resposta a incidentes
- executar playbooks
- integrar ferramentas de segurança

---

# 21. XSOAR

Versão mais avançada de SOAR que inclui:

- automação avançada
- gerenciamento de casos
- visão unificada da segurança
- integração profunda com ferramentas