---
title: Proprietà standard nei record di Log Analytics di Monitoraggio di Azure | Microsoft Docs
description: Descrive le proprietà comuni a più tipi di dati in Log Analytics di Monitoraggio di Azure.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 663f0b04c528c180e4130c1c157441cbc0ceb98b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46955868"
---
# <a name="standard-properties-in-log-analytics-records"></a>Proprietà standard nei record di Log Analytics
I dati in [Log Analytics](../log-analytics/log-analytics-queries.md) sono archiviati come set di record, ognuno con un particolare tipo di dati che ha un set di proprietà univoco. Molti tipi di dati hanno proprietà standard comuni a più tipi. Questo articolo descrive queste proprietà e contiene esempi di come usarle nelle query.

Alcune di queste proprietà sono ancora in corso di implementazione, pertanto possono essere presenti in alcuni tipi di dati ma non in altri.


## <a name="resourceid"></a>_ResourceId
La proprietà **_ResourceId** contiene un identificatore univoco per la risorsa a cui è associato il record. Si ottiene così una proprietà standard da utilizzare per definire l'ambito della query solo ai record di una determinata risorsa oppure per unire i dati correlati tra più tabelle.

Per le risorse di Azure, il valore di **_ResourceId** è l'[URL dell'ID risorsa di Azure](../azure-resource-manager/resource-group-template-functions-resource.md). La proprietà è attualmente limitata alle risorse di Azure, ma verrà estesa alle risorse esterne ad Azure, ad esempio i computer locali. 

### <a name="examples"></a>Esempi
L'esempio seguente illustra una query che crea un join dei dati delle prestazioni e degli eventi per ogni computer. Mostra tutti gli eventi con ID _101_ e un utilizzo del processore del 50%.

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

L'esempio seguente illustra una query che crea un join dei record _AzureActivity_ con i record _SecurityEvent_. Mostra tutte le operazioni di attività con utenti che hanno effettuato l'accesso a tali macchine.

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

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulla modalità di [archiviazione dei dati di Log Analytics](../log-analytics/log-analytics-queries.md).
- Accedere a una lezione sulla [scrittura di query in Log Analytics](../log-analytics/query-language/get-started-queries.md).
- Accedere a una lezione sul [join di tabelle nelle query di Log Analytics](../log-analytics/query-language/joins.md).