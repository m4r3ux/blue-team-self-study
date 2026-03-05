# Hardening Linux Básico

Hardening é o processo de reduzir a superfície de ataque de um sistema mantendo sua funcionalidade.  
Em um servidor Linux, isso significa controlar acesso, restringir privilégios, limitar comunicação de rede e proteger arquivos críticos.

---

# 1. Controle de Acesso – SSH Hardening

O SSH é o principal ponto de entrada em servidores Linux. Se ele estiver mal configurado, todo o restante perde força.

Arquivo de configuração:

/etc/ssh/sshd_config

## 1.1 Desabilitar login como root

Permitir login direto como root facilita ataques de força bruta e exploração.

Configuração:

PermitRootLogin no

Reiniciar serviço:

systemctl restart sshd

---

## 1.2 Desabilitar autenticação por senha

Autenticação por senha permite ataques automatizados. O ideal é utilizar chave pública/privada.

Configuração:

PasswordAuthentication no

Antes disso, configure chave SSH no cliente:

ssh-keygen  
ssh-copy-id usuario@servidor

---

## 1.3 Alterar porta (opcional)

Não é uma medida de segurança forte, mas reduz ruído de scanners automáticos.

Port 2222

---

# 2. Controle de Privilégios – Sudo

O princípio do menor privilégio deve ser aplicado sempre.

Editar regras com:

visudo

## Exemplo incorreto (acesso total):

usuario ALL=(ALL) ALL

## Exemplo mais seguro (acesso restrito):

usuario ALL=(ALL) /usr/bin/systemctl restart nginx

Nesse modelo, o usuário só pode executar um comando específico.

---

# 3. Firewall – iptables e firewalld

O firewall controla o tráfego de entrada e saída do servidor.

Conceito essencial: política padrão.

Se a política padrão for ACCEPT, tudo é permitido.
Se for DROP, nada é permitido até que você libere explicitamente.

Hardening adequado começa com política restritiva.

---

## 3.1 Exemplo com iptables

Definir políticas padrão:

iptables -P INPUT DROP  
iptables -P FORWARD DROP  
iptables -P OUTPUT ACCEPT  

Permitir conexões já estabelecidas:

iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT  

Permitir SSH:

iptables -A INPUT -p tcp --dport 22 -j ACCEPT  

Permitir HTTP:

iptables -A INPUT -p tcp --dport 80 -j ACCEPT  

Tudo o restante será bloqueado.

---

## 3.2 Exemplo com firewalld

Permitir serviços:

firewall-cmd --permanent --add-service=ssh  
firewall-cmd --permanent --add-service=http  
firewall-cmd --reload  

A lógica é a mesma do iptables, apenas com outra interface.

---

# 4. Permissões de Arquivos

No Linux, tudo é arquivo. Segurança depende da configuração correta de dono e permissões.

Exemplo de permissão:

-rwxr-x---

Significado:
- Dono: leitura, escrita e execução
- Grupo: leitura e execução
- Outros: sem acesso

---

## 4.1 Erro comum

chmod 777 arquivo

Isso permite que qualquer usuário modifique o arquivo, o que pode levar a escalonamento de privilégio.

---

## 4.2 Configurações recomendadas

Arquivos executáveis:

chmod 750 arquivo

Diretórios sensíveis:

chmod 700 diretorio

Alterar dono:

chown usuario:grupo arquivo

---

# 5. SUID (Set User ID)

Arquivos com SUID executam com o privilégio do dono, normalmente root.

Verificar arquivos com SUID:

find / -perm -4000 -type f 2>/dev/null

Arquivos desnecessários com SUID podem ser vetores de privilege escalation e devem ser revisados.

---

# Estrutura Mental do Hardening

Firewall → controla quem pode se conectar  
SSH → controla como alguém entra  
Sudo → controla o que pode fazer  
Permissões → controlam o que pode ser alterado  

Aplicando corretamente esses quatro pilares, o nível de segurança do servidor aumenta significativamente.