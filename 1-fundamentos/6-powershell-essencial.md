# PowerShell Essencial para SOC

## 1. Mentalidade Correta

PowerShell é uma shell orientada a objetos.

Isso significa que os comandos retornam objetos estruturados, não texto simples.

Exemplo conceitual:
Um evento 4625 (falha de login) possui propriedades como:

- TimeCreated  
- Id  
- ProviderName  
- Message  
- Properties (lista interna com IP, usuário, Logon Type, etc.)

Você não filtra texto.
Você filtra propriedades de objetos.

Isso é fundamental para análise eficiente em SOC.

## 2. Get-EventLog vs Get-WinEvent

### Get-EventLog

Comando legado.  
Mais simples.  
Mais limitado.  
Não suporta logs modernos como Sysmon.  
Performance inferior em ambientes grandes.

Exemplo:

Get-EventLog -LogName Security -Newest 5

Uso recomendado apenas em ambientes antigos.

### Get-WinEvent

Comando moderno e recomendado.  
Suporta logs clássicos e avançados.  
Permite filtros eficientes.  
Melhor performance.

Exemplo básico:

Get-WinEvent -LogName Security -MaxEvents 5

Este é o padrão profissional.

## 3. Filtros

### Forma ineficiente

Get-WinEvent -LogName Security | Where-Object {$_.Id -eq 4625}

Problema:
- Carrega todos os eventos
- Filtra depois
- Consome mais memória e CPU

### Forma eficiente: FilterHashtable

Get-WinEvent -FilterHashtable @{
    LogName='Security'
    Id=4625
}

O filtro ocorre antes da consulta completa.
Mais rápido e escalável.
Recomendado para ambientes reais.

## 4. Filtro por Tempo

Investigações sempre têm contexto temporal.

Exemplo: falhas de login nas últimas 2 horas.

Get-WinEvent -FilterHashtable @{
    LogName='Security'
    Id=4625
    StartTime=(Get-Date).AddHours(-2)
}

Explicação:
- Get-Date obtém a data atual
- AddHours(-2) retorna duas horas no passado
- StartTime limita os eventos ao intervalo desejado

## 5. Pipeline

O símbolo | envia o resultado de um comando para outro.

Exemplo:

Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4625} |
Select-Object TimeCreated, Id, Message

Fluxo:
1. Busca eventos  
2. Seleciona apenas campos relevantes

## 6. Comandos Essenciais

### Where-Object

Filtra objetos com base em condição.

Exemplo:

Where-Object {$_.Id -eq 4625}

### Select-Object

Seleciona propriedades específicas.

Exemplo:

Select-Object TimeCreated, Id, Message

### Group-Object

Agrupa objetos por propriedade.

Exemplo:

Group-Object MachineName

Muito útil para identificar padrões, como múltiplas falhas por IP.

## 7. Detectando Possível Brute Force

Exemplo: falhas de login nos últimos 30 minutos agrupadas por IP.

$events = Get-WinEvent -FilterHashtable @{
    LogName='Security'
    Id=4625
    StartTime=(Get-Date).AddMinutes(-30)
}

$events |
Select-Object @{Name="IP";Expression={$_.Properties[19].Value}} |
Group-Object IP |
Sort-Object Count -Descending

Lógica:
1. Buscar eventos recentes  
2. Extrair IP da estrutura interna  
3. Agrupar por IP  
4. Ordenar por quantidade  

## 8. Explorando Estrutura do Evento

Para entender um evento profundamente:

$evento = Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4625} -MaxEvents 1
$evento | Format-List *

Isso revela todas as propriedades disponíveis.

Importante para:
- Descobrir onde está o IP  
- Descobrir onde está o usuário  
- Descobrir o tipo de logon  

Sem explorar a estrutura, você depende de scripts prontos.
Com exploração, você entende o funcionamento interno.

## 9. Fluxo Mental de Investigação

Estrutura padrão:

Get-WinEvent → Filtrar (Id, tempo) → Selecionar campos → Agrupar ou analisar padrão

Esse modelo mental é mais importante do que decorar sintaxe completa.

## 10. Conceitos que Devem Estar Consolidados

- PowerShell trabalha com objetos  
- Get-WinEvent é preferível ao Get-EventLog  
- FilterHashtable é mais eficiente que Where-Object  
- Pipeline permite encadeamento de análise  
- Eventos possuem propriedades internas exploráveis  
- Investigação sempre envolve contexto temporal  