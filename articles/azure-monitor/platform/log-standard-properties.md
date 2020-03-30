---
title: Proprietà standard nei record di log di Monitoraggio di Azure | Microsoft Docs
description: Descrive le proprietà comuni a più tipi di dati nei log di Monitoraggio di Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/18/2019
ms.openlocfilehash: 252ddeb372744986df0b8ba9b742d0462a4e8202
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274476"
---
# <a name="standard-properties-in-azure-monitor-logs"></a>Standard properties in Azure Monitor Logs
I dati nei log di Monitoraggio di Azure [vengono archiviati come set di record in un'area](../log-query/logs-structure.md)di lavoro di Log Analytics o in un'applicazione Di Application Insights, ognuno con un particolare tipo di dati con un set univoco di proprietà. Molti tipi di dati hanno proprietà standard comuni a più tipi. Questo articolo descrive queste proprietà e contiene esempi di come usarle nelle query.

> [!NOTE]
> Alcune delle proprietà standard non verranno visualizzate nella visualizzazione schema o intellisense in Log Analytics e non verranno visualizzate nei risultati della query a meno che non si specifichi in modo esplicito la proprietà nell'output.

## <a name="timegenerated-and-timestamp"></a>TimeGenerated e timestamp
Le proprietà **TimeGenerated** (area di lavoro di Log Analytics) e **timestamp** (applicazione Application Insights) contengono la data e l'ora di creazione del record dall'origine dati. Per altre informazioni, vedere Registrare il tempo di inserimento dei [dati in Monitoraggio di Azure.See Log data ingestion time in Azure Monitor](data-ingestion-time.md) for more details.

**TimeGenerated** e **timestamp** forniscono una proprietà comune da utilizzare per filtrare o riepilogare in base al tempo. Quando si seleziona un intervallo di tempo per una visualizzazione o un dashboard nel portale di Azure, viene utilizzato TimeGenerated o timestamp per filtrare i risultati. 

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
La proprietà TimeReceived contiene la data e l'ora in cui il record è stato ricevuto dal punto di inserimento di Monitoraggio di Azure nel cloud di Azure.The ** \_TimeReceived** property contains the date and time that the record was received by the Azure Monitor ingestion point in the Azure cloud. Ciò può essere utile per identificare i problemi di latenza tra l'origine dati e il cloud. Un esempio potrebbe essere un problema di rete che causa un ritardo con i dati inviati da un agente. Per altre informazioni, vedere Registrare il tempo di inserimento dei [dati in Monitoraggio di Azure.See Log data ingestion time in Azure Monitor](data-ingestion-time.md) for more details.

La query seguente fornisce la latenza media per ora per i record di evento di un agente. Ciò include il tempo dall'agente al cloud e il tempo totale per il record sia disponibile per le query di log.

```Kusto
Event
| where TimeGenerated > ago(1d) 
| project TimeGenerated, TimeReceived = _TimeReceived, IngestionTime = ingestion_time() 
| extend AgentLatency = toreal(datetime_diff('Millisecond',TimeReceived,TimeGenerated)) / 1000
| extend TotalLatency = toreal(datetime_diff('Millisecond',IngestionTime,TimeGenerated)) / 1000
| summarize avg(AgentLatency), avg(TotalLatency) by bin(TimeGenerated,1hr)
``` 

## <a name="type-and-itemtype"></a>Tipo e itemType
Le proprietà **Type** (Area di lavoro log Analytics) e **itemType** (applicazione Application Insights) contengono il nome della tabella da cui è stato recuperato il record, che può essere considerato anche come tipo di record. Questa proprietà è utile nelle query che consentono di combinare record da più tabelle, ad esempio quelle che usano l'operatore `search`, per distinguere tra record di diverso tipo. **$table** può essere usato al posto di **Type** in alcune posizioni.

### <a name="examples"></a>Esempi
La query seguente restituisce il numero di record in base al tipo raccolti nell'ultima ora.

```Kusto
search * 
| where TimeGenerated > ago(1h)
| summarize count() by Type

```
## <a name="_itemid"></a>\_Itemid
La proprietà ** \_ItemId** contiene un identificatore univoco per il record.


## <a name="_resourceid"></a>\_ResourceId
La proprietà ** \_ResourceId** contiene un identificatore univoco per la risorsa a cui è associato il record. Si ottiene così una proprietà standard da utilizzare per definire l'ambito della query solo ai record di una determinata risorsa oppure per unire i dati correlati tra più tabelle.

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

La query seguente analizza **_ResourceId** e aggrega i volumi di dati fatturati per ogni sottoscrizione di Azure.The following query parses the import and aggregates billed data volumes per Azure subscription.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last 
```

Usare queste query `union withsource = tt *` solo se necessario, poiché le analisi tra tipi di dati sono costose.

## <a name="_isbillable"></a>\_IsBillable
La proprietà ** \_IsBillable** specifica se i dati ingeriti sono fatturabili. I dati con ** \_IsBillable** uguale a false vengono raccolti gratuitamente e non fatturati all'account Azure.Data with IsBillable equal to _false_ are collected for free and not billed to your Azure account.

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
La ** \_proprietà BilledSize** specifica la dimensione in byte di dati che verranno fatturati all'account Azure se ** \_IsBillable** è true.


### <a name="examples"></a>Esempi
Per vedere le dimensioni degli eventi fatturabili inseriti per computer, usare la proprietà `_BilledSize` che fornisce la dimensione in byte:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last 
```

Per visualizzare la dimensione degli eventi fatturabili ingeriti per ogni sottoscrizione, utilizzare la query seguente:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/" *
| summarize Bytes=sum(_BilledSize) by  SubscriptionId | sort by Bytes nulls last 
```

Per visualizzare la dimensione degli eventi fatturabili ingeriti per gruppo di risorse, usare la query seguente:To see the size of billable events ingested per resource group, use the following query:

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

Per visualizzare il conteggio dei tipi di dati fatturabili da un computer specifico, utilizzare la query seguente:

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
