---
title: Proprietà standard nei record di log di Monitoraggio di Azure | Microsoft Docs
description: Descrive le proprietà comuni a più tipi di dati nei log di Monitoraggio di Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/01/2020
ms.openlocfilehash: b0ec666f2cfadc3a1571f3ed1d26c92bcbbca3a2
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83196241"
---
# <a name="standard-properties-in-azure-monitor-logs"></a>Proprietà standard in Log di Monitoraggio di Azure
I dati in Log di Monitoraggio di Azure vengono [archiviati come set di record in un'area di lavoro Log Analytics o in un'applicazione di Application Insights](../log-query/logs-structure.md), ognuno con un particolare tipo di dati a cui è assegnato un set di proprietà univoco. Molti tipi di dati hanno proprietà standard comuni a più tipi. Questo articolo descrive queste proprietà e contiene esempi di come usarle nelle query.

> [!IMPORTANT]
> Se si usa Application Performance Monitoring 2.1, le applicazioni di Application Insights vengono archiviate in un'area di lavoro Log Analytics con tutti gli altri dati del log. Le tabelle sono state rinominate e ristrutturate ma includono le stesse informazioni delle tabelle nell'applicazione di Application Insights. Le proprietà standard di queste nuove tabelle sono le stesse di altre tabelle nell'area di lavoro Log Analytics.

> [!NOTE]
> Alcune delle proprietà standard non verranno visualizzate nella visualizzazione schema o in IntelliSense in Log Analytics,né nei risultati della query a meno che non si specifichi in modo esplicito la proprietà nell'output.

## <a name="timegenerated-and-timestamp"></a>TimeGenerated e timestamp
Le proprietà **TimeGenerated** (area di lavoro Log Analytics) e **timestamp** (applicazione di Application Insights) contengono la data e l'ora in cui il record è stato creato dall'origine dati. Per altri dettagli, vedere [Tempo di inserimento dei dati di log in Monitoraggio di Azure](data-ingestion-time.md).

**TimeGenerated** e **timestamp** forniscono una proprietà comune da usare per filtrare o riepilogare in base all'ora. Quando si seleziona un intervallo di tempo per una visualizzazione o un dashboard nel portale di Azure, si usa TimeGenerated o timestamp per filtrare i risultati. 

### <a name="examples"></a>Esempi

La query seguente restituisce il numero di eventi di errore creati per ogni giorno della settimana precedente.

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by TimeGenerated asc 
```

La query seguente restituisce il numero di eccezioni create per ogni giorno della settimana precedente.

```Kusto
exceptions
| where timestamp between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by timestamp asc 
```

## <a name="_timereceived"></a>\_TimeReceived
La proprietà **\_TimeReceived** contiene la data e l'ora in cui il record è stato ricevuto nel cloud di Azure dal punto di inserimento di Monitoraggio di Azure. Questa proprietà può essere utile per identificare i problemi di latenza tra l'origine dati e il cloud. Un esempio può essere un problema di rete che causa un ritardo durante l'invio dei dati da un agente. Per altri dettagli, vedere [Tempo di inserimento dei dati di log in Monitoraggio di Azure](data-ingestion-time.md).

La query seguente restituisce la latenza media per ora relativa ai record di evento inviati da un agente. Sono inclusi il tempo del trasferimento dall'agente al cloud e il tempo totale in cui il record sarà disponibile per le query sui log.

```Kusto
Event
| where TimeGenerated > ago(1d) 
| project TimeGenerated, TimeReceived = _TimeReceived, IngestionTime = ingestion_time() 
| extend AgentLatency = toreal(datetime_diff('Millisecond',TimeReceived,TimeGenerated)) / 1000
| extend TotalLatency = toreal(datetime_diff('Millisecond',IngestionTime,TimeGenerated)) / 1000
| summarize avg(AgentLatency), avg(TotalLatency) by bin(TimeGenerated,1hr)
``` 

## <a name="type-and-itemtype"></a>Type e itemType
Le proprietà **Type** (area di lavoro Log Analytics) e **itemType** (applicazione di Application Insights) contengono il nome della tabella da cui è stato recuperato il record, che è possibile considerare come tipo di record. Questa proprietà è utile nelle query che consentono di combinare record da più tabelle, ad esempio quelle che usano l'operatore `search`, per distinguere tra record di diverso tipo. **$table** può essere usato al posto di **Type** in alcune posizioni.

### <a name="examples"></a>Esempi
La query seguente restituisce il numero di record in base al tipo raccolti nell'ultima ora.

```Kusto
search * 
| where TimeGenerated > ago(1h)
| summarize count() by Type

```
## <a name="_itemid"></a>\_ItemId
La proprietà **\_ItemId** contiene un identificatore univoco per il record.


## <a name="_resourceid"></a>\_ResourceId
La proprietà **\_ResourceId** contiene un identificatore univoco per la risorsa a cui è associato il record. Si ottiene così una proprietà standard da utilizzare per definire l'ambito della query solo ai record di una determinata risorsa oppure per unire i dati correlati tra più tabelle.

Per le risorse di Azure, il valore di **_ResourceId** è l'[URL dell'ID risorsa di Azure](../../azure-resource-manager/templates/template-functions-resource.md). La proprietà è attualmente limitata alle risorse di Azure, ma verrà estesa alle risorse esterne ad Azure, ad esempio i computer locali.

> [!NOTE]
> Alcuni tipi di dati dispongono già di campi che contengono l'ID risorsa di Azure o almeno una parte di esso, ad esempio l'ID sottoscrizione. Mentre questi campi vengono mantenuti per motivi di compatibilità con le versioni precedenti, è consigliabile usare _ResourceId per eseguire la correlazione incrociata dal momento che sarà più coerente.

### <a name="examples"></a>Esempi
La query seguente crea un join dei dati delle prestazioni e degli eventi per ogni computer. Mostra tutti gli eventi con ID _101_ e un utilizzo del processore del 50%.

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

La query seguente crea un join dei record _AzureActivity_ con i record _SecurityEvent_. Mostra tutte le operazioni di attività con utenti che hanno effettuato l'accesso a tali macchine.

```Kusto
AzureActivity 
| where  
    OperationName in ("Restart Virtual Machine", "Create or Update Virtual Machine", "Delete Virtual Machine")  
    and ActivityStatus == "Succeeded"  
| join kind= leftouter (    
   SecurityEvent 
   | where EventID == 4624  
   | summarize LoggedOnAccounts = makeset(Account) by _ResourceId 
) on _ResourceId  
```

La query seguente analizza **_ResourceId** e aggrega i volumi di dati fatturati per ogni sottoscrizione di Azure.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last 
```

Usare queste query `union withsource = tt *` solo se necessario, poiché le analisi tra tipi di dati sono costose.

## <a name="_isbillable"></a>\_IsBillable
La proprietà **\_IsBillable** specifica se i dati inseriti sono fatturabili. I dati con **\_IsBillable** uguali a `false` vengono raccolti gratuitamente e non fatturati nell'account Azure.

### <a name="examples"></a>Esempi
Per ottenere un elenco di computer che inviano i tipi di dati fatturati, usare la query seguente:

> [!NOTE]
> Usare le query con `union withsource = tt *` solo se necessario, poiché le analisi tra tipi di dati costituiscono un processo costoso. 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

Questo processo può essere esteso per restituire il conteggio orario dei computer che inviano tipi di dati fatturati:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="_billedsize"></a>\_BilledSize
La proprietà **\_BilledSize** specifica la dimensione in byte dei dati che verranno fatturati all'Azure se la proprietà **\_IsBillable** è true.


### <a name="examples"></a>Esempi
Per vedere le dimensioni degli eventi fatturabili inseriti per computer, usare la proprietà `_BilledSize` che fornisce la dimensione in byte:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last 
```

Per visualizzare la quantità di eventi fatturabili inseriti per sottoscrizione, usare la query seguente:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/" *
| summarize Bytes=sum(_BilledSize) by  SubscriptionId | sort by Bytes nulls last 
```

Per visualizzare la quantità di eventi fatturabili inseriti per gruppo di risorse, usare la query seguente:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/resourcegroups/" ResourceGroupName "/" *
| summarize Bytes=sum(_BilledSize) by  SubscriptionId, ResourceGroupName | sort by Bytes nulls last 

```


Per visualizzare il numero di eventi inseriti per computer, usare la query seguente:

```Kusto
union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last
```

Per visualizzare il numero di eventi fatturabili inseriti per computer, usare la query seguente: 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize count() by Computer  | sort by count_ nulls last
```

Per visualizzare il numero di tipi di dati fatturabili inseriti da un computer specifico, usare la query seguente:

```Kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last 
```

## <a name="next-steps"></a>Passaggi successivi

- Leggere altre informazioni su come [vengono archiviati i dati di log di Monitoraggio di Azure](../log-query/log-query-overview.md).
- Seguire una lezione sulla [scrittura di query di log](../../azure-monitor/log-query/get-started-queries.md).
- Seguire una lezione sul [join di tabelle nelle query di log](../../azure-monitor/log-query/joins.md).