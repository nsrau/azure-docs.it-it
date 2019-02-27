---
title: Aggregazioni avanzate nelle query dei log di Monitoraggio di Azure | Microsoft Docs
description: Descrive alcune delle opzioni di aggregazione più avanzate disponibili per le query dei log di Monitoraggio di Azure.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: bwren
ms.openlocfilehash: 37eb8ca3c25268dd7923087439a8fbf0fd1f168b
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2019
ms.locfileid: "56269910"
---
# <a name="advanced-aggregations-in-azure-monitor-log-queries"></a>Aggregazioni avanzate nelle query dei log di Monitoraggio di Azure

> [!NOTE]
> Prima di seguire questa lezione, è consigliabile completare [Aggregazioni nelle query di Monitoraggio di Azure](./aggregations.md).

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Questo articolo descrive alcune delle opzioni di aggregazione più avanzate disponibili per le query di Monitoraggio di Azure.

## <a name="generating-lists-and-sets"></a>Generazione di elenchi e set
È possibile usare `makelist` per variare la visualizzazione dei dati in base all'ordine dei valori di una determinata colonna. Se ad esempio è necessario esaminare l'ordine più comune in cui si verificano gli eventi nei computer, è possibile scegliere di visualizzare i dati in base all'ordine degli ID evento in ogni computer. 

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makelist(EventID) by Computer
```
|Computer|list_EventID|
|---|---|
| computer1 | [704,701,1501,1500,1085,704,704,701] |
| computer2 | [326,105,302,301,300,102] |
| ... | ... |

`makelist` genera un elenco nell'ordine in cui sono stati passati i dati. Per ordinare gli eventi dal meno recente al più recente, usare `asc` anziché `desc` nell'istruzione order. 

È anche utile creare un elenco di semplici valori distinti. Un elenco di questo tipo è definito _Set_ e può essere generato con `makeset`:

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makeset(EventID) by Computer
```
|Computer|list_EventID|
|---|---|
| computer1 | [704,701,1501,1500,1085] |
| computer2 | [326,105,302,301,300,102] |
| ... | ... |

Analogamente a `makelist`, anche `makeset` funziona con i dati ordinati e genera le matrici in base all'ordine delle righe che vengono passate.

## <a name="expanding-lists"></a>Espansione di elenchi
L'operazione inversa di `makelist` o `makeset` è `mvexpand`, che espande un elenco di valori in righe separate. Questa funzione può espandere i valori in un numero qualsiasi di colonne dinamiche, sia JSON che matrice. Si supponga ad esempio di controllare la tabella *Heartbeat* per visualizzare le soluzioni che hanno trasmesso dati dai computer che hanno inviato un heartbeat nell'ultima ora:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, Solutions
```

| Computer | Soluzioni | 
|--------------|----------------------|
| computer1 | "security", "updates", "changeTracking" |
| computer2 | "security", "updates" |
| computer3 | "antiMalware", "changeTracking" |
| ... | ... | ... |

Usare `mvexpand` per mostrare ogni valore in una riga separata anziché in un elenco delimitato da virgole:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
```

| Computer | Soluzioni | 
|--------------|----------------------|
| computer1 | "security" |
| computer1 | "updates" |
| computer1 | "changeTracking" |
| computer2 | "security" |
| computer2 | "updates" |
| computer3 | "antiMalware" |
| computer3 | "changeTracking" |
| ... | ... | ... |


È quindi possibile usare nuovamente `makelist` per raggruppare gli elementi e questa volta visualizzare l'elenco dei computer per ogni soluzione:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
| summarize makelist(Computer) by tostring(Solutions) 
```
|Soluzioni | list_Computer |
|--------------|----------------------|
| "security" | ["computer1", "computer2"] |
| "updates" | ["computer1", "computer2"] |
| "changeTracking" | ["computer1", "computer3"] |
| "antiMalware" | ["computer3"] |
| ... | ... |

## <a name="handling-missing-bins"></a>Gestione di bin mancanti
Un'applicazione utile di `mvexpand` è quella di inserire i valori predefiniti per i bin mancanti. Si supponga, ad esempio, di voler conoscere il tempo di attività di un determinato computer esaminandone l'heartbeat e di voler visualizzare l'origine dell'heartbeat che è riportata nella colonna _category_. In genere, si usa una semplice istruzione summarize, come indicato di seguito:

```Kusto
Heartbeat
| where TimeGenerated > ago(12h)
| summarize count() by Category, bin(TimeGenerated, 1h)
```
| Categoria | TimeGenerated | count_ |
|--------------|----------------------|--------|
| Agente diretto | 2017-06-06T17:00:00Z | 15 |
| Agente diretto | 2017-06-06T18:00:00Z | 60 |
| Agente diretto | 2017-06-06T20:00:00Z | 55 |
| Agente diretto | 2017-06-06T21:00:00Z | 57 |
| Agente diretto | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |

In questi risultati, tuttavia, l'intervallo di tempo associato a "2017-06-06T19:00:00Z" non è elencato perché non sono presenti dati relativi ad heartbeat per tale ora. Per assegnare un valore predefinito agli intervalli di tempo vuoti usare la funzione `make-series`. In questo modo viene generata una riga per ogni categoria con due colonne matrice supplementari, una per i valori e l'altra per gli intervalli di tempo corrispondenti:

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
```

| Categoria | count_ | TimeGenerated |
|---|---|---|
| Agente diretto | [15,60,0,55,60,57,60,...] | ["2017-06-06T17:00:00.0000000Z","2017-06-06T18:00:00.0000000Z","2017-06-06T19:00:00.0000000Z","2017-06-06T20:00:00.0000000Z","2017-06-06T21:00:00.0000000Z",...] |
| ... | ... | ... |

Il terzo elemento della matrice *count_* è pari a 0 come previsto e nella matrice _TimeGenerated_ è presente un timestamp "2017-06-06T19:00:00.0000000Z" corrispondente. Questo formato di matrice è tuttavia difficile da leggere. Usare `mvexpand` per espandere le matrici e generare l'output nello stesso formato di quello generato da `summarize`:

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
| mvexpand TimeGenerated, count_
| project Category, TimeGenerated, count_
```
| Categoria | TimeGenerated | count_ |
|--------------|----------------------|--------|
| Agente diretto | 2017-06-06T17:00:00Z | 15 |
| Agente diretto | 2017-06-06T18:00:00Z | 60 |
| Agente diretto | 2017-06-06T19:00:00Z | 0 |
| Agente diretto | 2017-06-06T20:00:00Z | 55 |
| Agente diretto | 2017-06-06T21:00:00Z | 57 |
| Agente diretto | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |



## <a name="narrowing-results-to-a-set-of-elements-let-makeset-toscalar-in"></a>Limitazione dei risultati a un set di elementi: `let`, `makeset`, `toscalar`, `in`
Uno scenario comune consiste nel selezionare i nomi di alcune entità specifiche in base a un set di criteri e quindi filtrare un altro set di dati fino a tale set di entità. Può ad esempio essere necessario trovare i computer a cui non risultano applicati aggiornamenti e identificare gli indirizzi IP a cui questi computer hanno inviato chiamate:


```Kusto
let ComputersNeedingUpdate = toscalar(
    Update
    | summarize makeset(Computer)
    | project set_Computer
);
WindowsFirewall
| where Computer in (ComputersNeedingUpdate)
```

## <a name="next-steps"></a>Passaggi successivi

Vedere altre lezioni per l'uso del [linguaggio di query Kusto](/azure/kusto/query/) con i dati di log di Monitoraggio di Azure:

- [Operazioni con stringhe](string-operations.md)
- [Operazioni con data e ora](datetime-operations.md)
- [Funzioni di aggregazione](aggregations.md)
- [Aggregazioni avanzate](advanced-aggregations.md)
- [Strutture dei dati e JSON](json-data-structures.md)
- [Join](joins.md)
- [Grafici](charts.md)