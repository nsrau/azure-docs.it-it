---
title: Proprietà standard nei record di log di Monitoraggio di Azure | Microsoft Docs
description: Descrive le proprietà comuni a più tipi di dati nei log di Monitoraggio di Azure.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/20/2019
ms.author: bwren
ms.openlocfilehash: 4d7c1d9b59e802343f6d8fe258e8e4ac961bb2df
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67061017"
---
# <a name="standard-properties-in-azure-monitor-log-records"></a>Proprietà standard nei record di log di Monitoraggio di Azure
I dati di log in Monitoraggio di Azure sono [archiviati come set di record](../log-query/log-query-overview.md), ognuno con un particolare tipo di dati che ha un set di proprietà univoco. Molti tipi di dati hanno proprietà standard comuni a più tipi. Questo articolo descrive queste proprietà e contiene esempi di come usarle nelle query.

Alcune di queste proprietà sono ancora in corso di implementazione, pertanto possono essere presenti in alcuni tipi di dati ma non in altri.

## <a name="timegenerated"></a>TimeGenerated
La proprietà **TimeGenerated** contiene la data e l'ora in cui è stato creato il record. Fornisce una proprietà comune da usare per il filtro o il riepilogo in base all'ora. Quando si seleziona un intervallo di tempo per una visualizzazione o un dashboard nel portale di Azure, viene usato TimeGenerated per filtrare i risultati.

### <a name="examples"></a>Esempi

La query seguente restituisce il numero di eventi di errore creati per ogni giorno della settimana precedente.

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by TimeGenerated asc 
```

## <a name="type"></a>Type
La proprietà **Type** contiene il nome della tabella da cui è stato recuperato il record, che può essere considerato anche il tipo di record. Questa proprietà è utile nelle query che consentono di combinare record da più tabelle, ad esempio quelle che usano l'operatore `search`, per distinguere tra record di diverso tipo. **$table** può essere usato al posto di **Type** in alcune posizioni.

### <a name="examples"></a>Esempi
La query seguente restituisce il numero di record in base al tipo raccolti nell'ultima ora.

```Kusto
search * 
| where TimeGenerated > ago(1h) 
| summarize count() by Type 
```

## <a name="resourceid"></a>\_ResourceId
La proprietà **\_ResourceId** contiene un identificatore univoco per la risorsa a cui è associato il record. Si ottiene così una proprietà standard da utilizzare per definire l'ambito della query solo ai record di una determinata risorsa oppure per unire i dati correlati tra più tabelle.

Per le risorse di Azure, il valore di **_ResourceId** è l'[URL dell'ID risorsa di Azure](../../azure-resource-manager/resource-group-template-functions-resource.md). La proprietà è attualmente limitata alle risorse di Azure, ma verrà estesa alle risorse esterne ad Azure, ad esempio i computer locali.

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

La query seguente Analizza **_ResourceId** e aggregazioni fatturati i volumi di dati per ogni sottoscrizione di Azure.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last 
```

Usare queste query `union withsource = tt *` solo se necessario, poiché le analisi tra tipi di dati sono costose.

## <a name="isbillable"></a>\_IsBillable
La proprietà **\_IsBillable** specifica se i dati inseriti sono fatturabili. I dati con **\_IsBillable** uguali a _false_ vengono raccolti gratuitamente e non fatturati nell'account Azure.

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

## <a name="billedsize"></a>\_BilledSize
La proprietà **\_BilledSize** specifica la dimensione in byte dei dati che verranno fatturati all'Azure se la proprietà **\_IsBillable** è true.

### <a name="examples"></a>Esempi
Per vedere le dimensioni degli eventi fatturabili inseriti per computer, usare la proprietà `_BilledSize` che fornisce la dimensione in byte:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last 
```

Per visualizzare le dimensioni di eventi fatturabili inseriti per ogni sottoscrizione, usare la query seguente:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/" *
| summarize Bytes=sum(_BilledSize) by  SubscriptionId | sort by Bytes nulls last 
```

Per visualizzare le dimensioni di eventi fatturabili inseriti al gruppo di risorse, usare la query seguente:

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

Per visualizzare il numero di tipi di dati fatturabili da un computer specifico, usare la query seguente:

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
