---
title: Query avanzate in Azure Log Analytics | Microsoft Docs
description: Questo articolo fornisce un'esercitazione per l'uso del portale di analisi per la scrittura di query in Log Analytics.
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
ms.openlocfilehash: 72c151fec0637822411f8cac44f4e13a8df96445
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/17/2018
ms.locfileid: "40191010"
---
# <a name="writing-advanced-queries-in-log-analytics"></a>Scrittura di query avanzate in Log Analytics

> [!NOTE]
> Prima di seguire questa lezione, è consigliabile completare [Introduzione al portale di Analytics](get-started-analytics-portal.md) e [Introduzione alle query](get-started-queries.md).

## <a name="reusing-code-with-let"></a>Riutilizzo del codice con let
Usare `let` per assegnare i risultati a una variabile e farvi riferimento successivamente nella query:

```OQL
// get all events that have level 2 (indicates warning level)
let warning_events=
Event
| where EventLevel == 2;
// count the number of warning events per computer
warning_events
| summarize count() by Computer 
```

È inoltre possibile assegnare valori costanti alle variabili. In questo modo si possono impostare parametri per i campi che è necessario modificare ogni volta che si esegue la query. Modificare tali parametri in base alle proprie esigenze. Ad esempio, per calcolare lo spazio libero su disco e la memoria libera (in percentili) in un determinato intervallo di tempo:

```OQL
let startDate = datetime(2018-08-01T12:55:02);
let endDate = datetime(2018-08-02T13:21:35);
let FreeDiskSpace =
Perf
| where TimeGenerated between (startDate .. endDate)
| where ObjectName=="Logical Disk" and CounterName=="Free Megabytes"
| summarize percentiles(CounterValue, 50, 75, 90, 99);
let FreeMemory =
Perf
| where TimeGenerated between (startDate .. endDate)
| where ObjectName=="Memory" and CounterName=="Available MBytes Memory"
| summarize percentiles(CounterValue, 50, 75, 90, 99);
union FreeDiskSpace, FreeMemory
```

In questo modo è facile modificare l'inizio dell'ora di fine alla successiva esecuzione della query.

### <a name="local-functions-and-parameters"></a>Funzioni e parametri locali
Usare le istruzioni `let` per creare funzioni che possono essere usate nella stessa query. Ad esempio, definire una funzione che accetta un campo datetime (nel formato UTC) e lo converte in un formato standard degli Stati Uniti. 

```OQL
let utc_to_us_date_format = (t:datetime)
{
  strcat(getmonth(t), "/", dayofmonth(t),"/", getyear(t), " ",
  bin((t-1h)%12h+1h,1s), iff(t%24h<12h, "AM", "PM"))
};
Event 
| where TimeGenerated > ago(1h) 
| extend USTimeGenerated = utc_to_us_date_format(TimeGenerated)
| project TimeGenerated, USTimeGenerated, Source, Computer, EventLevel, EventData 
```

## <a name="functions"></a>Funzioni
È possibile salvare una query con un alias di funzione in modo che sia possibile farvi riferimento con altre query. La query standard seguente, ad esempio, restituisce tutti gli aggiornamenti della sicurezza mancanti segnalati nell'ultimo giorno:

```OQL
Update
| where TimeGenerated > ago(1d) 
| where Classification == "Security Updates" 
| where UpdateState == "Needed"
```

È possibile salvare questa query come funzione e assegnarle un alias, ad esempio _security_updates_last_day_, che può essere usato successivamente in un'altra query per cercare gli aggiornamenti della sicurezza necessari correlati a SQL:

```OQL
security_updates_last_day | where Title contains "SQL"
```

Per salvare una query come funzione, selezionare il pulsante **Salva** nel portale e impostare **Salva con nome** su _Funzione_. L'alias della funzione può contenere lettere, cifre o caratteri di sottolineatura, ma deve iniziare con una lettera o un carattere di sottolineatura.

> [!NOTE]
> Il salvataggio di una funzione è possibile per le query di Log Analytics, ma attualmente non lo è per le query di Application Insights.


## <a name="print"></a>Print
`print` restituisce una tabella con una sola colonna e una sola riga, che mostra il risultato di un calcolo. Questa funzione viene spesso usata nei casi in cui è necessario eseguire un calcolo semplice. Ad esempio, per trovare l'ora corrente in PST e aggiungere una colonna con EST:

```OQL
print nowPst = now()-8h
| extend nowEst = nowPst+3h
```

## <a name="datatable"></a>Datatable
`datatable` consente di definire un set di dati. Si specifica uno schema e un set di valori e quindi si invia la tabella ad altri elementi di query. Ad esempio, per creare una tabella relativa all'utilizzo della RAM e calcolare il valore medio ogni ora:

```OQL
datatable (TimeGenerated: datetime, usage_percent: double)
[
  "2018-06-02T15:15:46.3418323Z", 15.5,
  "2018-06-02T15:45:43.1561235Z", 20.2,
  "2018-06-02T16:16:49.2354895Z", 17.3,
  "2018-06-02T16:46:44.9813459Z", 45.7,
  "2018-06-02T17:15:41.7895423Z", 10.9,
  "2018-06-02T17:44:23.9813459Z", 24.7,
  "2018-06-02T18:14:59.7283023Z", 22.3,
  "2018-06-02T18:45:12.1895483Z", 25.4
]
| summarize avg(usage_percent) by bin(TimeGenerated, 1h)
```

I costrutti di datatable sono molto utili anche quando si crea una tabella di ricerca. Ad esempio, per mappare gli ID evento della tabella _SecurityEvent_ a tipi di evento elencati altrove, creare una tabella di ricerca con i tipi di evento usando `datatable` e quindi creare un join tra questa datatable e i dati di _SecurityEvent_:

```OQL
let eventCodes = datatable (EventID: int, EventType:string)
[
    4625, "Account activity",
    4688, "Process action",
    4634, "Account activity",
    4672, "Access",
    4624, "Account activity",
    4799, "Access management",
    4798, "Access management",
    5059, "Key operation",
    4648, "A logon was attempted using explicit credentials",
    4768, "Access management",
    4662, "Other",
    8002, "Process action",
    4904, "Security event management",
    4905, "Security event management",
];
SecurityEvent
| where TimeGenerated > ago(1h) 
| join kind=leftouter (
  eventCodes
) on EventID
| project TimeGenerated, Account, AccountType, Computer, EventType
```

## <a name="next-steps"></a>Passaggi successivi
Vedere altre lezioni relative all'uso del linguaggio di query di Log Analytics:

- [Operazioni con stringhe](string-operations.md)
- [Operazioni con data e ora](datetime-operations.md)
- [Funzioni di aggregazione](aggregations.md)
- [Aggregazioni avanzate](advanced-aggregations.md)
- [Strutture dei dati e JSON](json-data-structures.md)
- [Join](joins.md)
- [Grafici](charts.md)