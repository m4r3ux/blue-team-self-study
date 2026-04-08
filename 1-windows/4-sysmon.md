## Sysmon 
O Windows loga eventos (Security, System..), mas

Ele tem algumas limitações, por exemplo:
- 4688 - muitas vezes sem command line
- não mostra conexões de rede por processo
- não mostra criação de arquivos detalhada
- registry quase invisível
- sem correlação rica

Você vê que algo aconteceu, mas não entende o ataque.

## Solução: Sysmon (System Monitor)
O sysmon instala um driver + serviço que gera logs muito mais ricos.

Logs ficam em:
`Applications and Services Logs → Microsoft → Windows → Sysmon → Operational`

Podemos, como padrão, ao instalar o Sysmon, usar uma config padrão, que é a SwiftOnSecurity Sysmon Config - muito recomendada, pois:
- filtra ruído
- foca em comportamento malicioso
- pronto para SOC

## Eventos importantes

## Event ID 1 - Criação de processo

Campos-chave:

Image - executável
CommandLine - comando completo
ParentImage - processo pai
User
ProcessGuid - correlação

Exemplo:
```
Event ID: 1
Image: C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe
CommandLine: powershell -enc SQBFAFgA...
ParentImage: C:\Windows\explorer.exe
User: LAB\user
```

PowerShell com comando codificado - forte indicador de ataque.

Aqui o Windows muitas vezes não mostra isso.

---

### Event ID 3 - Conexão de rede

Campos importantes:
- Image
- DestinationIp
- DestinationPort

Exemplo:

```
Event ID: 3
Image: powershell.exe
DestinationIp: 45.77.12.90
DestinationPort: 443
```

Interpretação:
- PowerShell abrindo conexão externa -> C2 possível.

---

### Event ID 8 - CreateRemoteThread

Indica: um processo injetando código em outro

Exemplo:
```
Event ID: 8
SourceImage: powershell.exe
TargetImage: explorer.exe
```

Indica:
- PowerShell injetando no explorer - process injection

Técnica comum de malware

---

### Event ID 11 - File Creation

Campos:
- TargetFilename
- Image (quem criou)

```
Event ID: 11
Image: powershell.exe
TargetFilename: C:\Users\user\AppData\Local\temp\payload.exe
```

PowerShell baixando arquivo .exe na pasta temp - possível malware.

---

### Event ID 13 - Registry Modification

Persistência clássica

Exemplo:

```
Event ID: 13
TargetObject: HKCU\Software\Microsoft\Windows\CurrentVersion\Run\evil
Details: powershell.exe -enc ...
```

Persistência via registry (run key)

Resumo:
1	O que foi executado
3	Pra onde conectou
8	Injeção de código
11	Arquivo criado
13	Persistência

