---
title: Query per avvisi del log in Monitoraggio di Azure | Microsoft Docs
description: Indicazioni sulla scrittura di query efficienti per gli avvisi del log negli aggiornamenti di Monitoraggio di Azure e su un processo per la conversione delle query esistenti.
author: yossi-y
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: bwren
ms.subservice: alerts
ms.openlocfilehash: 429770b7651a93473c03f5e386d8f7b72692c161
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2019
ms.locfileid: "59006093"
---
# <a name="log-alert-queries-in-azure-monitor"></a>Query per avvisi del log in Monitoraggio di Azure
Le [regole di avviso basate sui log di Monitoraggio di Azure](alerts-unified-log.md) vengono eseguite a intervalli regolari, pertanto è necessario assicurarsi che siano scritte in modo da ridurre al minimo il sovraccarico e la latenza. Questo articolo include consigli per la scrittura di query efficienti per gli avvisi del log e un processo per la conversione delle query esistenti. 

## <a name="types-of-log-queries"></a>Tipi di query di log
[Le query di log in Monitoraggio di Azure](../log-query/log-query-overview.md) iniziare con una tabella o una [search](/azure/kusto/query/searchoperator) oppure [union](/azure/kusto/query/unionoperator) operatore.

Ad esempio, la query seguente ha come ambito la tabella _SecurityEvent_ e cerca un ID evento specifico. Questa è l'unica tabella che deve elaborare la query.

``` Kusto
SecurityEvent | where EventID == 4624 
```

Le query che iniziano con `search` o `union` consentono di eseguire la ricerca in più colonne in una tabella o anche in più tabelle. Gli esempi seguenti illustrano diversi metodi per la ricerca del termine _Memory_:

```Kusto
search "Memory"
search * | where == "Memory"
search ObjectName: "Memory"
search ObjectName == "Memory"
union * | where ObjectName == "Memory"
```

Anche se `search` e `union` sono utili durante l'esplorazione dei dati, cercando i termini nell'intero modello di dati, risultano meno efficienti rispetto all'uso di una tabella perché devono analizzare più tabelle. Dato che le query nelle regole di avviso vengono eseguite a intervalli regolari, ciò può comportare un sovraccarico eccessivo aumentando la latenza per l'avviso. A causa di questo sovraccarico, le query per le regole di avviso di log in Azure devono sempre iniziare con una tabella per definire un ambito specifico, con conseguente miglioramento sia delle prestazioni che della pertinenza dei risultati.

## <a name="unsupported-queries"></a>Query non supportate
A partire dal 11 gennaio 2019, creare o modificare le regole di avviso di log che usano gli operatori `search` o `union` non sarà supportato nel portale di Azure. L'uso di questi operatori in una regola di avviso restituirà un messaggio di errore. Le regole di avviso esistenti e le regole di avviso create e modificate con l'API di Log Analytics non sono interessate da questa modifica. È comunque consigliabile prendere in considerazione la modifica di qualsiasi regola di avviso che usa questi tipi di query per migliorarne l'efficienza.  

Le regole di avviso del log che usano [query per ricerche in più risorse](../log-query/cross-workspace-query.md) non sono interessate da questa modifica, perché queste query usano `union`, che limita l'ambito della query a risorse specifiche. Ciò non equivale a `union *`, che non può essere usato.  L'esempio seguente sarebbe valido in una regola di avviso di log:

```Kusto
union 
app('Contoso-app1').requests, 
app('Contoso-app2').requests, 
workspace('Contoso-workspace1').Perf 
```

>[!NOTE]
>Le [query su più risorse](../log-query/cross-workspace-query.md) negli avvisi dei log sono supportate nella nuova [API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Per impostazione predefinita, Monitoraggio di Azure usa l'[API legacy degli avvisi di Log Analytics](api-alerts.md) per la creazione di nuove regole di avviso relative ai log dal portale di Azure, a meno che non si esegua la commutazione dall'[API legacy degli avvisi relativi ai log](alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api). Dopo la commutazione, la nuova API diventa quella predefinita per le nuove regole di avviso nel portale di Azure e consente di creare regole di avviso dei log basate su query su più risorse. È possibile creare regole di avviso dei log basate su [query su più risorse](../log-query/cross-workspace-query.md) senza effettuare la commutazione usando il [modello ARM per l'API scheduledQueryRules](alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template), ma questa regola di avviso è gestibile tramite l'[API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) e non dal portale di Azure.

## <a name="examples"></a>Esempi
Gli esempi seguenti includono query di log che usano `search` e `union` con indicazione delle procedure che è possibile usare per modificare queste query per l'uso con le regole di avviso.

### <a name="example-1"></a>Esempio 1
Si vuole creare una regola di avviso di log usando la query seguente che recupera informazioni sulle prestazioni tramite `search`: 

``` Kusto
search * | where Type == 'Perf' and CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```
  

Per modificare questa query, iniziare usando la query seguente per identificare la tabella a cui appartengono le proprietà:

``` Kusto
search * | where CounterName == '% Free Space'
| summarize by $table
```
 

Il risultato di questa query indicherebbe che la proprietà _CounterName_ proviene dalla tabella _Perf_. 

È possibile usare questo risultato per creare la query seguente che verrebbe usata per la regola di avviso:

``` Kusto
Perf 
| where CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```


### <a name="example-2"></a>Esempio 2
Si vuole creare una regola di avviso di log usando la query seguente che recupera informazioni sulle prestazioni tramite `search`: 

``` Kusto
search ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"  
| summarize Avg_Memory_Usage =avg(CounterValue) by Computer 
| where Avg_Memory_Usage between(90 .. 95)  
| count 
```
  

Per modificare questa query, iniziare usando la query seguente per identificare la tabella a cui appartengono le proprietà:

``` Kusto
search ObjectName=="Memory" and CounterName=="% Committed Bytes In Use" 
| summarize by $table 
```
 

Il risultato di questa query indicherebbe che le proprietà _ObjectName_ e _CounterName_ provengono dalla tabella _Perf_. 

È possibile usare questo risultato per creare la query seguente che verrebbe usata per la regola di avviso:

``` Kusto
Perf 
| where ObjectName =="Memory" and CounterName=="% Committed Bytes In Use" 
| summarize Avg_Memory_Usage=avg(CounterValue) by Computer 
| where Avg_Memory_Usage between(90 .. 95)  
| count 
```
 

### <a name="example-3"></a>Esempio 3

Si vuole creare una regola di avviso di log usando la query seguente che usa sia `search` che `union` per recuperare informazioni sulle prestazioni: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| where Computer !in ((union * | where CounterName == "% Processor Utility" | summarize by Computer))
| summarize Avg_Idle_Time = avg(CounterValue) by Computer|  count  
```
 

Per modificare questa query, iniziare usando la query seguente per identificare la tabella a cui appartengono le proprietà nella prima parte della query: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| summarize by $table 
```

Il risultato di questa query indicherebbe che tutte queste proprietà provengono dalla tabella _Perf_. 

A questo punto usare `union` con il comando `withsource` per identificare la tabella di origine da cui proviene ogni riga.

``` Kusto
union withsource=table * | where CounterName == "% Processor Utility" 
| summarize by table 
```
 

Il risultato di questa query indicherebbe che anche queste proprietà provengono dalla tabella _Perf_. 

È possibile usare questi risultati per creare la query seguente che verrebbe usata per la regola di avviso: 

``` Kusto
Perf 
| where ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total" 
| where Computer !in ( 
    (Perf 
    | where CounterName == "% Processor Utility" 
    | summarize by Computer)) 
| summarize Avg_Idle_Time = avg(CounterValue) by Computer 
| count 
``` 

### <a name="example-4"></a>Esempio 4
Si vuole creare una regola di avviso di log usando la query seguente che unisce in join i risultati di due query `search`:

```Kusto
search Type == 'SecurityEvent' and EventID == '4625' 
| summarize by Computer, Hour = bin(TimeGenerated, 1h) 
| join kind = leftouter ( 
    search in (Heartbeat) OSType == 'Windows' 
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h) 
    | project Hour , Computer  
)  
on Hour 
| count 
```
 

Per modificare questa query, iniziare usando la query seguente per identificare la tabella che contiene le proprietà nel lato sinistro del join: 

``` Kusto
search Type == 'SecurityEvent' and EventID == '4625' 
| summarize by $table 
```
 

Il risultato indica che le proprietà nel lato sinistro del join appartengono alla tabella _SecurityEvent_. 

Ora è possibile usare la query seguente per identificare la tabella che contiene le proprietà sul lato destro del join: 

 
``` Kusto
search in (Heartbeat) OSType == 'Windows' 
| summarize by $table 
```

 
Il risultato indica che le proprietà sul lato destro del join appartengono alla tabella Heartbeat. 

È possibile usare questi risultati per creare la query seguente che verrebbe usata per la regola di avviso: 


``` Kusto
SecurityEvent
| where EventID == '4625'
| summarize by Computer, Hour = bin(TimeGenerated, 1h)
| join kind = leftouter (
    Heartbeat  
    | where OSType == 'Windows' 
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h) 
    | project Hour , Computer  
)  
on Hour 
| count 
```

## <a name="next-steps"></a>Passaggi successivi
- Informazioni sugli [avvisi del log](alerts-log.md) in Monitoraggio di Azure.
- Informazioni sulle [query nei log](../log-query/log-query-overview.md).

