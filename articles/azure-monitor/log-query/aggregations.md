---
title: Aggregazioni nelle query dei log di Monitoraggio di Azure | Microsoft Docs
description: Descrive le funzioni di aggregazione disponibili per le query dei log di Monitoraggio di Azure che offrono soluzioni utili per analizzare i dati.
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
ms.openlocfilehash: 864e9586082ed95bf17135414ec4b879e3034ace
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60589615"
---
# <a name="aggregations-in-azure-monitor-log-queries"></a>Aggregazioni nelle query dei log di Monitoraggio di Azure

> [!NOTE]
> Prima di seguire questa lezione, è consigliabile completare [Introduzione al portale di Analytics](get-started-portal.md) e [Introduzione alle query](get-started-queries.md).

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Questo articolo descrive le funzioni di aggregazione disponibili per le query dei log di Monitoraggio di Azure che offrono soluzioni utili per analizzare i dati. Tutte queste funzioni usano l'operatore `summarize` che genera una tabella con i risultati aggregati della tabella di input.

## <a name="counts"></a>Conteggi

### <a name="count"></a>count
Questa funzione conta il numero di righe nel set di risultati dopo l'applicazione di eventuali filtri. L'esempio seguente restituisce il numero totale di righe della tabella _Perf_ negli ultimi 30 minuti. Il risultato viene restituito in una colonna denominata *count_* a meno che non si definisca un nome specifico:


```Kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize count()
```

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize num_of_records=count() 
```

Per visualizzare una tendenza nel tempo può essere utile un diagramma temporale:

```Kusto
Perf 
| where TimeGenerated > ago(30m) 
| summarize count() by bin(TimeGenerated, 5m)
| render timechart
```

L'output di questo esempio mostra la linea di tendenza dei conteggi dei record delle prestazioni a intervalli di 5 minuti:

![Tendenza dei conteggi](media/aggregations/count-trend.png)


### <a name="dcount-dcountif"></a>dcount, dcountif
Usare `dcount` e `dcountif` per contare valori distinti in una colonna specifica. La query seguente valuta quanti computer distinti hanno inviato heartbeat nell'ultima ora:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcount(Computer)
```

Per contare solo i computer Linux che hanno inviato heartbeat, usare `dcountif`:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcountif(Computer, OSType=="Linux")
```

### <a name="evaluating-subgroups"></a>Valutazione di sottogruppi
Per eseguire un conteggio o altre aggregazioni in sottogruppi dei dati, usare la parola chiave `by`. Ad esempio, per contare il numero di computer Linux distinti che hanno inviato heartbeat in ogni paese:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry
```

|RemoteIPCountry  | distinct_computers  |
------------------|---------------------|
|Stati Uniti    | 19                  |
|Canada           | 3                   |
|Irlanda          | 0                   |
|Regno Unito   | 0                   |
|Paesi Bassi      | 2                   |


Per analizzare sottogruppi ancora più piccoli dei dati, aggiungere altri nomi di colonna nella sezione `by`. Può ad esempio essere necessario contare i computer distinti di ogni paese in base al tipo di sistema operativo:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry, OSType
```

## <a name="percentiles-and-variance"></a>Percentili e varianza
Quando si valutano i valori numerici, è pratica comune calcolarne la media usando `summarize avg(expression)`. Sulle medie influiscono valori estremi che caratterizzano solo alcuni casi. Per risolvere questo problema, è possibile usare funzioni meno sensibili, ad esempio `median` o `variance`.

### <a name="percentile"></a>Percentile
Per trovare il valore mediano, usare la funzione `percentile` con un valore per specificare il percentile:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 50) by Computer
```

È anche possibile specificare diversi percentili per ottenere un risultato aggregato per ciascuno:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 25, 50, 75, 90) by Computer
```

I risultati potrebbero mostrare che alcune CPU di computer hanno valori mediani simili, anche se alcuni computer si attestano attorno a un valore mediano, mentre altri segnalano valori di CPU molto più bassi e più alti, indicando così che si sono verificati dei picchi.

### <a name="variance"></a>Variance
Per valutare direttamente la varianza di un valore, usare i metodi di varianza e deviazione standard:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), variance(CounterValue) by Computer
```

Un buon metodo per analizzare la stabilità dell'utilizzo della CPU consiste nel combinare stdev con il calcolo mediano:

```Kusto
Perf
| where TimeGenerated > ago(130m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), percentiles(CounterValue, 50) by Computer
```

Vedere altre lezioni per l'uso del [linguaggio di query Kusto](/azure/kusto/query/) con i dati di log di Monitoraggio di Azure:

- [Operazioni con stringhe](string-operations.md)
- [Operazioni con data e ora](datetime-operations.md)
- [Aggregazioni avanzate](advanced-aggregations.md)
- [Strutture dei dati e JSON](json-data-structures.md)
- [Scrittura di query avanzate](advanced-query-writing.md)
- [Join](joins.md)
- [Grafici](charts.md)
