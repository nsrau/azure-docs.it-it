---
title: Colonne standard nei record di log di monitoraggio di Azure | Microsoft Docs
description: Descrive le colonne comuni a più tipi di dati nei log di monitoraggio di Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/09/2020
ms.openlocfilehash: 2370f76bacb8645f1b343da4f056c8bcf06a26dd
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95796729"
---
# <a name="standard-columns-in-azure-monitor-logs"></a>Colonne standard nei log di monitoraggio di Azure
I dati nei log di monitoraggio di Azure vengono [archiviati come set di record in un'area di lavoro log Analytics o in un'applicazione Application Insights](./data-platform-logs.md), ognuno con un particolare tipo di dati che dispone di un set univoco di colonne. Molti tipi di dati avranno colonne standard comuni tra più tipi. Questo articolo descrive queste colonne e fornisce esempi di come è possibile usarle nelle query.

Le applicazioni basate su aree di lavoro in Application Insights archiviano i dati in un'area di lavoro Log Analytics e utilizzano le stesse colonne standard di altre tabelle nell'area di lavoro. Le applicazioni classiche archiviano separatamente i dati e hanno colonne standard diverse, come specificato in questo articolo.

> [!NOTE]
> Alcune colonne standard non verranno visualizzate nella visualizzazione schema o in IntelliSense in Log Analytics e non verranno visualizzate nei risultati della query a meno che non si specifichi in modo esplicito la colonna nell'output.

## <a name="timegenerated-and-timestamp"></a>TimeGenerated e timestamp
Le colonne **TimeGenerated** (area di lavoro log Analytics) e **timestamp** (Application Insights applicazione) contengono la data e l'ora in cui il record è stato creato dall'origine dati. Per altri dettagli, vedere [Tempo di inserimento dei dati di log in Monitoraggio di Azure](data-ingestion-time.md).

**TimeGenerated** e **timestamp** forniscono una colonna comune da usare per il filtro o il riepilogo in base al tempo. Quando si seleziona un intervallo di tempo per una visualizzazione o un dashboard nel portale di Azure, si usa TimeGenerated o timestamp per filtrare i risultati. 

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
La colonna **\_ TimeReceived** contiene la data e l'ora in cui il record è stato ricevuto dal punto di inserimento di monitoraggio di Azure nel cloud di Azure. Questa proprietà può essere utile per identificare i problemi di latenza tra l'origine dati e il cloud. Un esempio può essere un problema di rete che causa un ritardo durante l'invio dei dati da un agente. Per altri dettagli, vedere [Tempo di inserimento dei dati di log in Monitoraggio di Azure](data-ingestion-time.md).

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
Il **tipo** (area di lavoro log Analytics) e le colonne **ItemType** (Application Insights applicazione) contengono il nome della tabella da cui il record è stato recuperato, che può anche essere considerato come il tipo di record. Questa colonna è utile nelle query che combinano i record di più tabelle, ad esempio quelli che usano l' `search` operatore, per distinguere i record di tipi diversi. **$table** può essere usato al posto di **Type** in alcune posizioni.

### <a name="examples"></a>Esempi
La query seguente restituisce il numero di record in base al tipo raccolti nell'ultima ora.

```Kusto
search * 
| where TimeGenerated > ago(1h)
| summarize count() by Type

```
## <a name="_itemid"></a>\_ItemId
La colonna **\_ ItemId** include un identificatore univoco per il record.


## <a name="_resourceid"></a>\_ResourceId
La colonna **\_ resourceId** include un identificatore univoco per la risorsa a cui è associato il record. In questo modo si ottiene una colonna standard da usare per definire l'ambito della query solo per i record di una determinata risorsa o per unire dati correlati tra più tabelle.

Per le risorse di Azure, il valore di **_ResourceId** è l'[URL dell'ID risorsa di Azure](../../azure-resource-manager/templates/template-functions-resource.md). La colonna è limitata alle risorse di Azure, incluse le risorse di [Azure Arc](../../azure-arc/overview.md) , oppure ai log personalizzati che indicano l'ID risorsa durante l'inserimento.

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

La query seguente analizza **_ResourceId** e aggrega i volumi di dati fatturati per ogni gruppo di risorse di Azure.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by resourceGroup | sort by Bytes nulls last 
```

Usare queste query `union withsource = tt *` solo se necessario, poiché le analisi tra tipi di dati sono costose.

È sempre più efficiente usare la \_ colonna SubscriptionId anziché estrarla analizzando la \_ colonna ResourceId.

## <a name="_substriptionid"></a>\_SubstriptionId
La colonna **\_ SubscriptionId** include l'ID sottoscrizione della risorsa a cui è associato il record. In questo modo si ottiene una colonna standard da usare per definire l'ambito della query solo per i record di una sottoscrizione specifica o per confrontare sottoscrizioni diverse.

Per le risorse di Azure, il valore di **__SubscriptionId** è la parte relativa alla sottoscrizione dell' [URL dell'ID risorsa di Azure](../../azure-resource-manager/templates/template-functions-resource.md). La colonna è limitata alle risorse di Azure, incluse le risorse di [Azure Arc](../../azure-arc/overview.md) , oppure ai log personalizzati che indicano l'ID risorsa durante l'inserimento.

> [!NOTE]
> Per alcuni tipi di dati sono già presenti campi che contengono l'ID sottoscrizione di Azure. Sebbene questi campi siano conservati per la compatibilità con le versioni precedenti, è consigliabile usare la \_ colonna SubscriptionId per eseguire la correlazione incrociata poiché sarà più coerente.
### <a name="examples"></a>Esempi
Nella query seguente vengono esaminati i dati sulle prestazioni per i computer di una sottoscrizione specifica. 

```Kusto
Perf 
| where TimeGenerated > ago(24h) and CounterName == "memoryAllocatableBytes"
| where _SubscriptionId == "57366bcb3-7fde-4caf-8629-41dc15e3b352"
| summarize avgMemoryAllocatableBytes = avg(CounterValue) by Computer
```

La query seguente analizza **_ResourceId** e aggrega i volumi di dati fatturati per ogni sottoscrizione di Azure.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by _SubscriptionId | sort by Bytes nulls last 
```

Usare queste query `union withsource = tt *` solo se necessario, poiché le analisi tra tipi di dati sono costose.


## <a name="_isbillable"></a>\_IsBillable
La colonna **\_ fatturabile** specifica se i dati inseriti sono fatturabili. I dati con **\_IsBillable** uguali a `false` vengono raccolti gratuitamente e non fatturati nell'account Azure.

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
La colonna **\_ BilledSize** specifica la dimensione in byte dei dati che verranno addebitati all'account di Azure se la **\_ fatturazione** è vera.


### <a name="examples"></a>Esempi
Per visualizzare le dimensioni degli eventi fatturabili inseriti per computer, utilizzare la `_BilledSize` colonna che fornisce le dimensioni in byte:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last 
```

Per visualizzare la quantità di eventi fatturabili inseriti per sottoscrizione, usare la query seguente:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  _SubscriptionId | sort by Bytes nulls last 
```

Per visualizzare la quantità di eventi fatturabili inseriti per gruppo di risorse, usare la query seguente:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/resourcegroups/" ResourceGroupName "/" *
| summarize Bytes=sum(_BilledSize) by  _SubscriptionId, ResourceGroupName | sort by Bytes nulls last 

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
- Seguire una lezione sulla [scrittura di query di log](../log-query/get-started-queries.md).
- Seguire una lezione sul [join di tabelle nelle query di log](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#joins).
