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
ms.devlang: na
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 833548a4bfca83a8ee6971f05a4f308cc54d5b5d
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/17/2018
ms.locfileid: "40191112"
---
# <a name="working-with-date-time-values-in-log-analytics-queries"></a>Uso dei valori di data e ora nelle query di Azure Log Analytics

> [!NOTE]
> Prima di seguire questa lezione, è consigliabile completare [Introduzione al portale di Analytics](get-started-analytics-portal.md) e [Introduzione alle query](get-started-queries.md).

Questo articolo descrive come usare i valori di data e ora nelle query di Log Analytics.


## <a name="date-time-basics"></a>Informazioni di base su data e ora
Nel linguaggio di query di Log Analytics sono presenti due tipi di dati principali associati ai valori di data e ora: datetime e timespan. Tutte le date sono espresse in UTC. Benché siano supportati più formati di valori datetime, è preferibile il formato ISO8601. 

I valori timespan sono espressi come numero decimale seguito da un'unità di tempo:

|sintassi abbreviata   | unità di tempo    |
|:---|:---|
|d           | giorno          |
|h           | ora         |
|m           | minuto       |
|s           | secondo       |
|ms          | millisecondo  |
|microsecondo | microsecondo  |
|tick        | nanosecondo   |

È possibile creare valori datetime eseguendo il cast di una stringa tramite l'operatore `todatetime`. Ad esempio, per esaminare gli heartbeat di una macchina virtuale inviati in un intervallo di tempo specifico, è possibile usare l'[operatore between](https://docs.loganalytics.io/docs/Language-Reference/Scalar-operators/between-operator), utile per specificare un intervallo di tempo.

```OQL
Heartbeat
| where TimeGenerated between(datetime("2018-06-30 22:46:42") .. datetime("2018-07-01 00:57:27"))
```

Un altro scenario comune consiste nel confronto di un valore datetime con quello attuale. Ad esempio, per visualizzare tutti gli heartbeat negli ultimi due minuti, è possibile usare l'operatore `now` insieme a un valore timespan che rappresenta due minuti:

```OQL
Heartbeat
| where TimeGenerated > now() - 2m
```

Per questa funzione è disponibile anche una soluzione più rapida:
```OQL
Heartbeat
| where TimeGenerated > now(-2m)
```

La soluzione più veloce e leggibile consiste tuttavia nell'uso dell'operatore `ago`:
```OQL
Heartbeat
| where TimeGenerated > ago(2m)
```

Si supponga di conoscere l'ora di inizio e la durata anziché l'ora di inizio e di fine. È possibile riscrivere la query come segue:

```OQL
let startDatetime = todatetime("2018-06-30 20:12:42.9");
let duration = totimespan(25m);
Heartbeat
| where TimeGenerated between(startDatetime .. (startDatetime+duration) )
| extend timeFromStart = TimeGenerated - startDatetime
```

## <a name="converting-time-units"></a>Conversione di unità di tempo
Può essere utile esprimere un valore datetime o timespan in un'unità di tempo diversa da quella predefinita. Si supponga ad esempio di esaminare gli eventi di errore degli ultimi 30 minuti e di dover aggiungere una colonna calcolata che mostra quanto tempo prima si è verificato l'evento:

```OQL
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated 
```

Come si può notare, la colonna _timeAgo_ contiene valori come "00:09:31.5118992", ovvero nel formato hh:mm:ss.fffffff. Se si vogliono formattare questi valori con il valore _numver_ di minuti dall'ora di inizio, è sufficiente dividere tale valore per "1 minuto":

```OQL
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated
| extend timeAgoMinutes = timeAgo/1m 
```


## <a name="aggregations-and-bucketing-by-time-intervals"></a>Aggregazioni e bucket in base all'intervallo di tempo
Un altro scenario molto comune è rappresentato dalla necessità di ottenere statistiche relative a un determinato periodo di tempo con uno specifico livello di dettaglio. A tale scopo è possibile usare un operatore `bin` come parte di una clausola di riepilogo.

Usare la query seguente per ottenere il numero di eventi che si sono verificati ogni 5 minuti durante l'ultima mezz'ora:

```OQL
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

```OQL
Event
| where TimeGenerated > ago(4d)
| summarize events_count=count() by startofday(TimeGenerated) 
```

Il risultato è illustrato nella tabella seguente:

|timestamp|count_|
|--|--|
|2018-07-28T00:00:00.000|7.136|
|2018-07-29T00:00:00.000|12.315|
|2018-07-30T00:00:00.000|16.847|
|2018-07-31T00:00:00.000|12.616|
|2018-08-01T00:00:00.000|5.416  |


## <a name="time-zones"></a>Fusi orari
Poiché tutti i valori datetime sono espressi in UTC, spesso è utile convertirli nel fuso orario locale. Eseguire ad esempio questo calcolo per convertire le ore UTC in PST:

```OQL
Event
| extend localTimestamp = TimeGenerated - 8h
```

## <a name="related-functions"></a>Funzioni correlate

| Categoria | Funzione |
|:---|:---|
| Convertire tipi di dati | [todatetime](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/todatetime())  [totimespan](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/totimespan())  |
| Arrotondare il valore alle dimensioni del contenitore | [bin](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/bin()) |
| Ottenere una data o un'ora specifica | [ago](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/ago()) [now](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/now())   |
| Ottenere parte di un valore | [datetime_part](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/datetime_part()) [getmonth](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/getmonth()) [monthofyear](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/monthofyear()) [getyear](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/getyear()) [dayofmonth](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/dayofmonth()) [dayofweek](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/dayofweek()) [dayofyear](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/dayofyear()) [weekofyear](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/weekofyear()) |
| Ottenere una data relativa a un valore  | [endofday](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/endofday()) [endofweek](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/endofweek()) [endofmonth](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/endofmonth()) [endofyear](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/endofyear()) [startofday](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/startofday()) [startofweek](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/startofweek()) [startofmonth](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/startofmonth()) [startofyear](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/startofyear()) |

## <a name="next-steps"></a>Passaggi successivi
Vedere altre lezioni relative all'uso del linguaggio di query di Log Analytics:

- [Operazioni con stringhe](string-operations.md)
- [Funzioni di aggregazione](aggregations.md)
- [Aggregazioni avanzate](advanced-aggregations.md)
- [Strutture dei dati e JSON](json-data-structures.md)
- [Scrittura di query avanzate](advanced-query-writing.md)
- [Join](joins.md)
- [Grafici](charts.md)