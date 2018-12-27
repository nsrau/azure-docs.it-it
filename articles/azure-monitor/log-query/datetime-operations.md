---
title: Uso dei valori di data e ora nelle query di Azure Log Analytics | Microsoft Docs
description: Descrive come usare i valori di data e ora nelle query di Log Analytics.
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
ms.openlocfilehash: e922e2c18ebb87783bf0ed229ce7107c4577c994
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52884806"
---
# <a name="working-with-date-time-values-in-log-analytics-queries"></a>Uso dei valori di data e ora nelle query di Azure Log Analytics

> [!NOTE]
> Prima di seguire questa lezione, è consigliabile completare [Introduzione al portale di Analytics](get-started-portal.md) e [Introduzione alle query](get-started-queries.md).

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Questo articolo descrive come usare i valori di data e ora nelle query di Log Analytics.


## <a name="date-time-basics"></a>Informazioni di base su data e ora
Nel linguaggio di query di Log Analytics sono presenti due tipi di dati principali associati ai valori di data e ora: datetime e timespan. Tutte le date sono espresse in UTC. Benché siano supportati più formati di valori datetime, è preferibile il formato ISO8601. 

I valori timespan sono espressi come numero decimale seguito da un'unità di tempo:

|sintassi abbreviata   | unità di tempo    |
|:---|:---|
|d           | day          |
|h           | hour         |
|m           | minuto       |
|s           | secondo       |
|ms          | millisecondo  |
|microsecondo | microsecondo  |
|tick        | nanosecondo   |

È possibile creare valori datetime eseguendo il cast di una stringa tramite l'operatore `todatetime`. Ad esempio, per esaminare gli heartbeat di una macchina virtuale inviati in un intervallo di tempo specifico, è possibile usare l'[operatore between](/azure/kusto/query/betweenoperator), utile per specificare un intervallo di tempo.

```Kusto
Heartbeat
| where TimeGenerated between(datetime("2018-06-30 22:46:42") .. datetime("2018-07-01 00:57:27"))
```

Un altro scenario comune consiste nel confronto di un valore datetime con quello attuale. Ad esempio, per visualizzare tutti gli heartbeat negli ultimi due minuti, è possibile usare l'operatore `now` insieme a un valore timespan che rappresenta due minuti:

```Kusto
Heartbeat
| where TimeGenerated > now() - 2m
```

Per questa funzione è disponibile anche una soluzione più rapida:
```Kusto
Heartbeat
| where TimeGenerated > now(-2m)
```

La soluzione più veloce e leggibile consiste tuttavia nell'uso dell'operatore `ago`:
```Kusto
Heartbeat
| where TimeGenerated > ago(2m)
```

Si supponga di conoscere l'ora di inizio e la durata anziché l'ora di inizio e di fine. È possibile riscrivere la query come segue:

```Kusto
let startDatetime = todatetime("2018-06-30 20:12:42.9");
let duration = totimespan(25m);
Heartbeat
| where TimeGenerated between(startDatetime .. (startDatetime+duration) )
| extend timeFromStart = TimeGenerated - startDatetime
```

## <a name="converting-time-units"></a>Conversione di unità di tempo
Può essere utile esprimere un valore datetime o timespan in un'unità di tempo diversa da quella predefinita. Si supponga ad esempio di esaminare gli eventi di errore degli ultimi 30 minuti e di dover aggiungere una colonna calcolata che mostra quanto tempo prima si è verificato l'evento:

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated 
```

È possibile vedere la colonna _timeAgo_ contenente valori come: "00:09:31.5118992", vale a dire che sono formattati come: hh:mm:ss. fffffff. Se si vogliono formattare questi valori con il valore _numver_ di minuti dall'ora di inizio, è sufficiente dividere tale valore per "1 minuto":

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated
| extend timeAgoMinutes = timeAgo/1m 
```


## <a name="aggregations-and-bucketing-by-time-intervals"></a>Aggregazioni e bucket in base all'intervallo di tempo
Un altro scenario molto comune è rappresentato dalla necessità di ottenere statistiche relative a un determinato periodo di tempo con uno specifico livello di dettaglio. A tale scopo è possibile usare un operatore `bin` come parte di una clausola di riepilogo.

Usare la query seguente per ottenere il numero di eventi che si sono verificati ogni 5 minuti durante l'ultima mezz'ora:

```Kusto
Event
| where TimeGenerated > ago(30m)
| summarize events_count=count() by bin(TimeGenerated, 5m) 
```

Il risultato è illustrato nella tabella seguente:  
|TimeGenerated(UTC)|events_count|
|--|--|
|2018-08-01T09:30:00.000|54|
|2018-08-01T09:35:00.000|41|
|2018-08-01T09:40:00.000|42|
|2018-08-01T09:45:00.000|41|
|2018-08-01T09:50:00.000|41|
|2018-08-01T09:55:00.000|16|

In alternativa, è possibile creare bucket di risultati tramite funzioni, ad esempio `startofday`:

```Kusto
Event
| where TimeGenerated > ago(4d)
| summarize events_count=count() by startofday(TimeGenerated) 
```

Il risultato è illustrato nella tabella seguente:

| timestamp|count_|
|--|--|
|2018-07-28T00:00:00.000|7.136|
|2018-07-29T00:00:00.000|12.315|
|2018-07-30T00:00:00.000|16.847|
|2018-07-31T00:00:00.000|12.616|
|2018-08-01T00:00:00.000|5.416  |


## <a name="time-zones"></a>Fusi orari
Poiché tutti i valori datetime sono espressi in UTC, spesso è utile convertirli nel fuso orario locale. Eseguire ad esempio questo calcolo per convertire le ore UTC in PST:

```Kusto
Event
| extend localTimestamp = TimeGenerated - 8h
```

## <a name="related-functions"></a>Funzioni correlate

| Categoria | Funzione |
|:---|:---|
| Convertire tipi di dati | [todatetime](/azure/kusto/query/todatetimefunction)  [totimespan](/azure/kusto/query/totimespanfunction)  |
| Arrotondare il valore alle dimensioni del contenitore | [bin](/azure/kusto/query/binfunction) |
| Ottenere una data o un'ora specifica | [ago](/azure/kusto/query/agofunction) [now](/azure/kusto/query/nowfunction)   |
| Ottenere parte di un valore | [datetime_part](/azure/kusto/query/datetime-partfunction) [getmonth](/azure/kusto/query/getmonthfunction) [monthofyear](/azure/kusto/query/monthofyearfunction) [getyear](/azure/kusto/query/getyearfunction) [dayofmonth](/azure/kusto/query/dayofmonthfunction) [dayofweek](/azure/kusto/query/dayofweek) [dayofyear](/azure/kusto/query/dayofyearfunction) [weekofyear](/azure/kusto/query/weekofyearfunction) |
| Ottenere una data relativa a un valore  | [endofday](/azure/kusto/query/endofdayfunction) [endofweek](/azure/kusto/query/endofweekfunction) [endofmonth](/azure/kusto/query/endofmonthfunction) [endofyear](/azure/kusto/query/endofyearfunction) [startofday](/azure/kusto/query/startofdayfunction) [startofweek](/azure/kusto/query/startofweekfunction) [startofmonth](/azure/kusto/query/startofmonthfunction) [startofyear](/azure/kusto/query/startofyearfunction) |

## <a name="next-steps"></a>Passaggi successivi
Vedere altre lezioni relative all'uso del linguaggio di query di Log Analytics:

- [Operazioni con stringhe](string-operations.md)
- [Funzioni di aggregazione](aggregations.md)
- [Aggregazioni avanzate](advanced-aggregations.md)
- [Strutture dei dati e JSON](json-data-structures.md)
- [Scrittura di query avanzate](advanced-query-writing.md)
- [Join](joins.md)
- [Grafici](charts.md)