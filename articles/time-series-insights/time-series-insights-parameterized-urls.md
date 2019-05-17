---
title: Condividere le visualizzazioni personalizzate di Azure Time Series Insights con gli URL con parametri | Microsoft Docs
description: Questo articolo descrive come sviluppare URL con parametri in Azure Time Series Insights, per poter condividere facilmente una visualizzazione di un cliente.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.topic: conceptual
ms.workload: big-data
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: 3ddde600c2ac15c56b59051fbcd6bb0e8fbae1f6
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65787535"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>Condividere una visualizzazione personalizzata usando un URL con parametri

Per condividere una visualizzazione personalizzata in Time Series Insights Explorer, è possibile creare a livello di codice un URL con parametri della visualizzazione personalizzata.

Time Series Insights Explorer supporta parametri di query URL per specificare le visualizzazioni nell'esperienza direttamente dall'URL. Ad esempio, usando solo l'URL, è possibile specificare un ambiente di destinazione, un predicato di ricerca e l'intervallo di tempo desiderato. Quando un utente fa clic sull'URL personalizzato, l'interfaccia fornisce un collegamento diretto a tale asset nel portale di Time Series Insights. Vengono applicati i criteri di accesso ai dati.

> [!TIP]
> * Visualizzare la versione gratuita [Time Series Insights demo](https://insights.timeseries.azure.com/samples).
> * Leggere l'accompagnamento [Time Series Insights Explorer](./time-series-insights-explorer.md) documentazione.

## <a name="environment-id"></a>ID di ambiente

Il parametro `environmentId=<guid>` specifica l'ID dell'ambiente di destinazione. È un componente del nome di dominio completo di accesso ai dati ed è possibile trovarlo nell'angolo in alto a destra della panoramica dell'ambiente nel portale di Azure. che include tutto ciò che precede `env.timeseries.azure.com`.

Un parametro ID di ambiente di esempio è `?environmentId=10000000-0000-0000-0000-100000000108`.

## <a name="time"></a>Time

È possibile specificare valori assoluti o relativi con un URL con parametri.

### <a name="absolute-time-values"></a>Valori assoluti

Per i valori assoluti, usare i parametri `from=<integer>` e `to=<integer>`.

* `from=<integer>` è un valore in millisecondi JavaScript indicante l'ora di inizio dell'intervallo di ricerca.
* `to=<integer>` è un valore in millisecondi JavaScript indicante l'ora di fine dell'intervallo di ricerca.

Per identificare i millisecondi JavaScript per una data, vedere [Epoch & Unix Timestamp Converter](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html) (Convertitore di timestamp Epoch e Unix).

### <a name="relative-time-values"></a>Valori relativi

Per un valore relativo, usare `relativeMillis=<value>`, dove *value* indica in millisecondi JavaScript i dati più recenti del back-end.

`&relativeMillis=3600000` ad esempio visualizza gli ultimi 60 minuti di dati.

I valori accettati corrispondono a Time Series Insights explorer **tempi rapidi** menu e includono:

* `1800000` (Ultimi 30 minuti)
* `3600000` (Ultimi 60 minuti)
* `10800000` (Ultime 3 ore)
* `21600000` (Ultime 6 ore)
* `43200000` (Ultime 12 ore)
* `86400000` (Ultime 24 ore)
* `604800000` (Ultimi 7 giorni)
* `2592000000` (Ultime 30 ore)

### <a name="optional-parameters"></a>Parametri facoltativi

Il `timeSeriesDefinitions=<collection of term objects>` parametro specifica le condizioni di una visualizzazione di Time Series Insights:

| Parametro | Elemento URL | Descrizione |
| --- | --- | --- |
| **name** | `\<string>` | Nome del *termine*. |
| **splitBy** | `\<string>` | Nome della colonna *in base a cui dividere*. |
| **measureName** | `\<string>` | Nome della colonna della *misura*. |
| **predicate** | `\<string>` | Clausola *where* per i filtri lato server. |
| **useSum** | `true` | Un parametro facoltativo che specifica l'uso della somma per una misura. </br>  Si noti che se `Events` è la misura selezionata, conteggio è selezionato per impostazione predefinita.  </br>  Se `Events` è Media non è selezionata, viene selezionata per impostazione predefinita. |

* Il `multiChartStack=<true/false>` coppia chiave-valore consente l'impilamento nel grafico.
* Il `multiChartSameScale=<true/false>` coppia chiave-valore abilita la stessa scala dell'asse y in tutti i termini all'interno di un parametro facoltativo.  
* Il `timeBucketUnit=<Unit>&timeBucketSize=<integer>` consente di regolare il dispositivo di scorrimento di intervallo per fornire più granulare o semplice e più aggregati visualizzazione del grafico.  
* Il `timezoneOffset=<integer>` parametro consente di impostare il fuso orario per il grafico da visualizzare come differenza dall'ora UTC.

| Coppie di | Descrizione |
| --- | --- |
| `multiChartStack=false` | `true` è abilitato per impostazione predefinita passano pertanto `false` uno stack. |
| `multiChartStack=false&multiChartSameScale=true` | È necessario abilitare l'impilamento per usare la stessa scala dell'asse Y in tutti i termini.  Ha `false` per impostazione predefinita, pertanto, passando 'true' Abilita questa funzionalità. |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | Unità = giorni, ore, minuti, secondi, millisecondi.  Scrivere sempre in lettere maiuscole l'unità. </br> Definire il numero di unità, passando l'intero desiderato per timeBucketSize.  Si noti che l'arco temporale è di 7 giorni.  |
| `timezoneOffset=-<integer>` | L'intero viene sempre espresso in millisecondi. </br> Si noti che questa funzionalità sono leggermente diverso da quella abilitata in Time Series Insights explorer, in cui è permetterti di scegliere l'ora UTC o locale (ora del browser). |

### <a name="examples"></a>Esempi

Per aggiungere definizioni di serie temporali in un ambiente Time Series Insights come parametro URL, aggiungere:

```plaintext
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Usare le definizioni di serie esempio tempo per:

* L'ID ambiente
* Ultimi 60 minuti di dati
* I termini (F1PressureID, F2TempStation e F3VibrationPL) che includono i parametri facoltativi

è possibile costruire l'URL con parametri seguente per ottenere una visualizzazione:

```plaintext
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

> [!TIP]
> Visualizzare l'Explorer di live [usando l'URL](https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]).

L'URL precedente descrive e genera la visualizzazione di Time Series Insights Explorer:

[![Termini di tempo Series Insights explorer](media/parameterized-url/url1.png)](media/parameterized-url/url1.png#lightbox)

La visualizzazione completa (incluso il grafico):

[![Visualizzazione grafico](media/parameterized-url/url2.png)](media/parameterized-url/url2.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [tramite dati di query C# ](time-series-insights-query-data-csharp.md).

* Scopri le [Time Series Insights Explorer](./time-series-insights-explorer.md).
