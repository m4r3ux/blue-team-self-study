## Introduction to Computer Forensics for Windows

**Computer Forensics** é a área da cibersegurança responsável por coletar, preservar e analisar evidências de atividades realizadas em computadores. Ela faz parte da **Digital Forensics**, que investiga qualquer dispositivo digital.

Principais aplicações:

- Investigações criminais e civis.
    
- Investigações corporativas internas.
    
- Análise de incidentes e invasões.
    

Um caso famoso foi o do serial killer BTK, identificado após investigadores recuperarem um documento apagado de um disquete e analisarem seus metadados.

### Forensic Artifacts (Artefatos Forenses)

Artefatos forenses são evidências digitais que registram atividades realizadas em um sistema.

Exemplos no Windows:

- Chaves do Registro (Registry).
    
- Arquivos de perfil do usuário.
    
- Arquivos gerados por aplicações.
    
- Configurações e preferências do sistema.
    

Esses artefatos permitem reconstruir ações realizadas por um usuário, funcionando como uma "trilha de evidências".

### O Windows está me espionando?

O Windows registra muitas atividades do usuário, mas o objetivo principal não é espionagem. Essas informações são armazenadas para personalizar e melhorar a experiência de uso, como:

- Preferências do usuário.
- Programas instalados.
- Contas utilizadas.
- Configurações do sistema.
- Histórico de uso.

Contudo, essas mesmas informações podem ser utilizadas por investigadores forenses para identificar o que aconteceu em um computador.

### Ponto principal

A análise forense em Windows se baseia na coleta e correlação de **artefatos digitais**, que permitem reconstruir com grande precisão as atividades realizadas em um sistema. O **Windows Registry** é uma das fontes mais importantes desses artefatos.

---

# Registro do Windows

## Conceito

O Registro do Windows é um banco de dados hierárquico responsável por armazenar configurações do sistema operacional, hardware, software instalado e perfis de usuários. Ele é utilizado constantemente pelo Windows para recuperar informações necessárias durante a inicialização do sistema, execução de programas e gerenciamento de dispositivos.

Do ponto de vista forense, o Registro é uma das fontes mais valiosas de evidências, pois registra informações sobre atividades de usuários, programas executados, dispositivos conectados e diversas alterações realizadas no sistema.

---

## Estrutura do Registro

O Registro é organizado em uma estrutura semelhante a um sistema de arquivos:

- **Chaves (Keys):** equivalentes a diretórios ou pastas.
    
- **Subchaves (Subkeys):** chaves localizadas dentro de outras chaves.
    
- **Valores (Values):** dados armazenados dentro das chaves.
    
- **Hive (Colmeia):** conjunto de chaves, subchaves e valores armazenados em um único arquivo físico no disco.
    

Exemplo de estrutura:

```text
HKEY_LOCAL_MACHINE
 └── Software
      └── Microsoft
           └── Windows
                └── CurrentVersion
```

Os valores armazenados dentro dessas chaves podem conter informações como caminhos de arquivos, configurações de programas, parâmetros do sistema e preferências do usuário.

---

## Editor de Registro (Regedit)

O utilitário nativo utilizado para visualizar e modificar o Registro é o **regedit.exe**.

Para acessá-lo:

1. Pressione `Windows + R`.
    
2. Digite:
    

```text
regedit.exe
```

3. Pressione Enter.
    

O painel esquerdo apresenta a estrutura hierárquica das chaves, enquanto o painel direito exibe os valores armazenados na chave selecionada.

---

# Chaves Raiz (Root Keys)

Todo sistema Windows possui cinco chaves principais que servem como ponto de partida para navegação no Registro.

## HKEY_CURRENT_USER (HKCU)

Contém as configurações do usuário atualmente autenticado no sistema.

Armazena informações como:

- Preferências do usuário.
    
- Configurações do Painel de Controle.
    
- Personalização da área de trabalho.
    
- Configurações de aplicativos.
    
- Mapeamentos de unidades de rede.
    

### Importância Forense

Permite identificar:

- Preferências do usuário.
    
- Programas utilizados.
    
- Configurações específicas da conta.
    
- Evidências de atividades recentes.
    

---

## HKEY_USERS (HKU)

Armazena os perfis de todos os usuários carregados no sistema.

Cada usuário possui uma subchave identificada por seu SID (Security Identifier).

O HKCU é, na prática, um atalho para o perfil atualmente carregado dentro do HKU.

### Importância Forense

Permite:

- Analisar múltiplos perfis de usuários.
    
- Investigar atividades realizadas por diferentes contas.
    
- Recuperar configurações mesmo quando o usuário não está logado.
    

---

## HKEY_LOCAL_MACHINE (HKLM)

Contém configurações globais do computador, independentemente do usuário conectado.

Armazena informações sobre:

- Hardware instalado.
    
- Drivers.
    
- Serviços do Windows.
    
- Softwares instalados.
    
- Configurações de segurança.
    
- Sistema operacional.
    

### Importância Forense

É uma das hives mais importantes para investigações, pois permite identificar:

- Programas instalados.
    
- Serviços executados.
    
- Dispositivos conectados.
    
- Configurações do sistema.
    
- Persistência de malware.
    

---

## HKEY_CLASSES_ROOT (HKCR)

Responsável pelo gerenciamento das associações entre arquivos e aplicações.

Determina qual programa será executado ao abrir um determinado tipo de arquivo.

Exemplos:

- `.pdf → Adobe Reader`
    
- `.docx → Microsoft Word`
    
- `.jpg → Visualizador de Fotos`
    

Na realidade, essa chave representa uma visão combinada de:

```text
HKEY_LOCAL_MACHINE\Software\Classes
```

e

```text
HKEY_CURRENT_USER\Software\Classes
```

As configurações definidas pelo usuário possuem prioridade sobre as configurações padrão do sistema.

### Importância Forense

Pode revelar:

- Associações alteradas por malware.
    
- Programas configurados para abrir determinados arquivos.
    
- Manipulações realizadas para execução maliciosa.
    

---

## HKEY_CURRENT_CONFIG (HKCC)

Contém informações sobre o perfil de hardware atualmente utilizado durante a inicialização do sistema.

Armazena configurações relacionadas a:

- Monitores.
    
- Impressoras.
    
- Dispositivos de entrada.
    
- Perfis de hardware ativos.
    

Essas informações são geradas dinamicamente a partir dos dados armazenados em outras partes do Registro.

### Importância Forense

Pode auxiliar na identificação de:

- Hardware utilizado no momento da inicialização.
    
- Configurações de dispositivos ativos.
    
- Alterações em perfis de hardware.
    

---

# Relevância do Registro em Computação Forense

O Registro do Windows é uma das principais fontes de evidências em investigações digitais devido à grande quantidade de informações históricas armazenadas.

Por meio dele, um analista pode identificar:

- Usuários que utilizaram o sistema.
    
- Programas executados recentemente.
    
- Dispositivos USB conectados.
    
- Configurações de rede.
    
- Softwares instalados.
    
- Mecanismos de persistência.
    
- Evidências de atividade maliciosa.
    

A análise dessas informações permite reconstruir eventos e criar uma linha do tempo das ações realizadas no sistema investigado.

---

## Resumo

- O Registro do Windows é um banco de dados hierárquico que armazena configurações do sistema, usuários e aplicações.
    
- É composto por **Keys (chaves)**, **Subkeys (subchaves)**, **Values (valores)** e **Hives (colmeias)**.
    
- O utilitário **regedit.exe** permite visualizar e editar o Registro.
    
- As cinco chaves raiz são:
    
    - HKCU → Usuário atual.
        
    - HKU → Todos os usuários carregados.
        
    - HKLM → Configurações globais do sistema.
        
    - HKCR → Associações de arquivos e aplicações.
        
    - HKCC → Perfil de hardware ativo.
        
- O Registro é uma fonte crítica de evidências para análises forenses e investigações de incidentes de segurança.

---

# Localização das Colmeias do Registro (Registry Hives)

## Conceito

Durante uma análise forense, nem sempre o investigador terá acesso a um sistema em execução. Em muitos casos, a única evidência disponível será uma imagem de disco. Nesses cenários, é fundamental conhecer a localização física das colmeias (hives) do Registro do Windows para realizar sua extração e análise offline.

As colmeias são arquivos que armazenam as estruturas do Registro e podem ser carregadas manualmente em ferramentas forenses ou no próprio Editor de Registro.

---

# Colmeias Principais do Sistema

A maioria das colmeias de sistema está localizada em:

```text
C:\Windows\System32\Config
```

As principais colmeias encontradas nesse diretório são:

|Arquivo Hive|Montado em|
|---|---|
|DEFAULT|HKEY_USERS\DEFAULT|
|SAM|HKEY_LOCAL_MACHINE\SAM|
|SECURITY|HKEY_LOCAL_MACHINE\Security|
|SOFTWARE|HKEY_LOCAL_MACHINE\Software|
|SYSTEM|HKEY_LOCAL_MACHINE\System|

---

## DEFAULT

Armazena o perfil padrão utilizado pelo sistema antes que um usuário realize login.

### Utilidade Forense

Pode fornecer informações sobre:

- Configurações padrão do sistema.
    
- Ambiente de logon.
    
- Configurações aplicadas a novos usuários.
    

---

## SAM (Security Account Manager)

Contém informações relacionadas às contas locais do Windows.

### Informações armazenadas

- Usuários locais.
    
- Grupos locais.
    
- Identificadores de segurança (SIDs).
    
- Hashes de senhas (protegidos pelo sistema).
    

### Utilidade Forense

Permite identificar:

- Contas existentes.
    
- Contas desabilitadas.
    
- Privilégios atribuídos.
    
- Possíveis alvos de extração de credenciais.
    

---

## SECURITY

Armazena políticas de segurança locais do sistema.

### Informações armazenadas

- Direitos de usuários.
    
- Políticas de auditoria.
    
- Segredos LSA (Local Security Authority Secrets).
    

### Utilidade Forense

Pode revelar:

- Credenciais armazenadas.
    
- Configurações de autenticação.
    
- Políticas de segurança implementadas.
    

---

## SOFTWARE

Contém informações sobre softwares instalados e configurações do sistema operacional.

### Informações armazenadas

- Programas instalados.
    
- Configurações de aplicações.
    
- Informações de desinstalação.
    
- Componentes do Windows.
    

### Utilidade Forense

Permite identificar:

- Softwares presentes no sistema.
    
- Ferramentas utilizadas pelo usuário.
    
- Possíveis aplicações maliciosas.
    

---

## SYSTEM

Armazena informações críticas sobre o sistema operacional e hardware.

### Informações armazenadas

- Serviços.
    
- Drivers.
    
- Perfis de hardware.
    
- Configurações de inicialização.
    

### Utilidade Forense

É uma das hives mais importantes para:

- Identificação do nome do computador.
    
- Análise de serviços persistentes.
    
- Investigação de drivers maliciosos.
    
- Reconstrução da configuração do sistema.
    

---

# Colmeias de Usuário

Além das colmeias do sistema, cada usuário possui colmeias individuais armazenadas em seu perfil.

Diretório padrão:

```text
C:\Users\<username>\
```

---

## NTUSER.DAT

Localização:

```text
C:\Users\<username>\NTUSER.DAT
```

Quando o usuário realiza login, essa hive é carregada como:

```text
HKEY_CURRENT_USER (HKCU)
```

### Informações armazenadas

- Preferências do usuário.
    
- Configurações pessoais.
    
- Histórico de atividades.
    
- Aplicações utilizadas.
    

### Utilidade Forense

É uma das fontes mais valiosas para identificar:

- Atividades do usuário.
    
- Programas executados.
    
- Arquivos acessados.
    
- Configurações individuais.
    

---

## USRCLASS.DAT

Localização:

```text
C:\Users\<username>\AppData\Local\Microsoft\Windows\USRCLASS.DAT
```

Montada em:

```text
HKEY_CURRENT_USER\Software\Classes
```

### Informações armazenadas

- Associações de arquivos.
    
- Configurações do Windows Explorer.
    
- Dados relacionados à interface do usuário.
    

### Utilidade Forense

Pode revelar:

- Arquivos recentemente acessados.
    
- Personalizações do Explorer.
    
- Comportamentos específicos do usuário.
    

---

### Observação

Tanto o **NTUSER.DAT** quanto o **USRCLASS.DAT** são arquivos ocultos por padrão.

---

# Amcache Hive

## Conceito

A hive **Amcache** é uma das fontes mais importantes para investigações forenses modernas.

Localização:

```text
C:\Windows\AppCompat\Programs\Amcache.hve
```

O Windows utiliza essa hive para registrar informações sobre programas executados no sistema.

---

## Informações Armazenadas

A Amcache pode conter:

- Programas executados.
    
- Caminhos dos executáveis.
    
- Tamanho dos arquivos.
    
- Hashes.
    
- Timestamps.
    
- Informações de instalação.
    

---

## Utilidade Forense

Extremamente útil para identificar:

- Execução de ferramentas maliciosas.
    
- Softwares removidos posteriormente.
    
- Artefatos de execução de programas.
    

Mesmo que um executável tenha sido apagado, registros dele podem permanecer na Amcache.

---

# Logs de Transação do Registro

## Conceito

Os logs de transação funcionam como um "diário de alterações" das colmeias do Registro.

Quando o Windows grava informações em uma hive, as mudanças podem ser registradas primeiro nos arquivos de log antes de serem efetivamente consolidadas na hive principal.

---

## Localização

Os logs ficam no mesmo diretório da hive correspondente.

Exemplo:

```text
C:\Windows\System32\Config\SAM.LOG
```

Também podem existir:

```text
SAM.LOG1
SAM.LOG2
```

---

## Utilidade Forense

Os logs de transação podem conter:

- Alterações recentes.
    
- Dados ainda não gravados na hive principal.
    
- Chaves modificadas pouco antes de um desligamento inesperado.
    

Por isso, uma análise forense completa deve incluir tanto a hive quanto seus logs associados.

---

# Backups do Registro (RegBack)

## Conceito

O Windows mantém cópias de segurança das colmeias do Registro para auxiliar na recuperação do sistema.

Esses backups ficam armazenados em:

```text
C:\Windows\System32\Config\RegBack
```

---

## Utilidade Forense

Os backups podem ser extremamente úteis quando:

- Chaves foram apagadas.
    
- Configurações foram alteradas recentemente.
    
- Malware modificou o Registro.
    
- É necessário comparar estados antigos e atuais da hive.
    

A comparação entre a hive atual e sua versão armazenada em RegBack pode revelar modificações importantes realizadas no sistema.

---

# Resumo

- As principais hives do sistema ficam em `C:\Windows\System32\Config`.
    
- **SAM** contém contas locais e informações de autenticação.
    
- **SECURITY** armazena políticas e segredos de segurança.
    
- **SOFTWARE** contém dados sobre aplicações instaladas.
    
- **SYSTEM** registra informações sobre hardware, drivers e serviços.
    
- **NTUSER.DAT** contém a maior parte das atividades e configurações do usuário.
    
- **USRCLASS.DAT** armazena associações de arquivos e configurações do Explorer.
    
- **Amcache.hve** registra informações sobre programas executados no sistema.
    
- Arquivos `.LOG`, `.LOG1` e `.LOG2` armazenam transações recentes do Registro.
    
- O diretório **RegBack** pode conter versões anteriores das hives e auxiliar na recuperação de evidências apagadas ou modificadas.