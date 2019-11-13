---
title: Condividere visualizzazioni personalizzate con URL con parametri-Azure Time Series Insights | Microsoft Docs
description: Informazioni su come sviluppare URL con parametri in Azure Time Series Insights per condividere facilmente le visualizzazioni personalizzate.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: conceptual
ms.workload: big-data
ms.date: 10/18/2019
ms.custom: seodec18
ms.openlocfilehash: 145af35f8c36d7f4659c3937209cb0d4d5b221a3
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74006381"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>Condividere una visualizzazione personalizzata usando un URL con parametri

Per condividere una visualizzazione personalizzata in Time Series Insights Explorer, è possibile creare a livello di codice un URL con parametri della visualizzazione personalizzata.

Esplora Time Series Insights supporta i parametri di query URL per specificare le viste nell'esperienza direttamente dall'URL. Ad esempio, usando solo l'URL, è possibile specificare un ambiente di destinazione, un predicato di ricerca e l'intervallo di tempo desiderato. Quando un utente seleziona l'URL personalizzato, l'interfaccia fornisce un collegamento direttamente a tale risorsa nel portale di Time Series Insights. Vengono applicati i criteri di accesso ai dati.

> [!TIP]
> * Visualizza la [demo Time Series Insights](https://insights.timeseries.azure.com/samples)gratuita.
> * Leggere la documentazione di [Time Series Insights Explorer](./time-series-insights-explorer.md) associata.

## <a name="environment-id"></a>ID di ambiente

Il parametro `environmentId=<guid>` specifica l'ID dell'ambiente di destinazione. Si tratta di un componente del FQDN di accesso ai dati, che è possibile trovare nell'angolo superiore destro della panoramica dell'ambiente nella portale di Azure. che include tutto ciò che precede `env.timeseries.azure.com`.

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

I valori accettati corrispondono al menu **ora rapido** Esplora Time Series Insights e includono:

* `1800000` (ultimi 30 minuti)
* `3600000` (ultimi 60 minuti)
* `10800000` (ultime 3 ore)
* `21600000` (ultime 6 ore)
* `43200000` (ultime 12 ore)
* `86400000` (ultime 24 ore)
* `604800000` (ultimi 7 giorni)
* `2592000000` (ultime 30 ore)

### <a name="optional-parameters"></a>Parametri facoltativi

Il parametro `timeSeriesDefinitions=<collection of term objects>` specifica i termini di una visualizzazione Time Series Insights:

| . | Elemento URL | DESCRIZIONE |
| --- | --- | --- |
| **nome** | `\<string>` | Nome del *termine*. |
| **splitBy** | `\<string>` | Nome della colonna *in base a cui dividere*. |
| **measureName** | `\<string>` | Nome della colonna della *misura*. |
| **predicate** | `\<string>` | Clausola *where* per i filtri lato server. |
| **useSum** | `true` | Parametro facoltativo che specifica l'utilizzo di Sum per la misura. </br>  Si noti che se `Events` è la misura selezionata, il conteggio è selezionato per impostazione predefinita.  </br>  Se `Events` non è selezionata, per impostazione predefinita viene selezionata l'opzione media. |

* Il `multiChartStack=<true/false>` coppia chiave-valore consente lo stacking nel grafico.
* La coppia chiave-valore `multiChartSameScale=<true/false>` Abilita la stessa scala dell'asse Y in tutti i termini all'interno di un parametro facoltativo.  
* Il `timeBucketUnit=<Unit>&timeBucketSize=<integer>` consente di regolare il dispositivo di scorrimento intervallo per fornire una visualizzazione più granulare o più uniforme del grafico.  
* Il `timezoneOffset=<integer>` parametro consente di impostare il fuso orario affinché il grafico venga visualizzato in come offset per l'ora UTC.

| Coppie | DESCRIZIONE |
| --- | --- |
| `multiChartStack=false` | `true` è abilitato per impostazione predefinita, quindi passare `false` allo stack. |
| `multiChartStack=false&multiChartSameScale=true` | È necessario abilitare l'impilamento per usare la stessa scala dell'asse Y in tutti i termini.  È `false` per impostazione predefinita, quindi il passaggio di ' true ' Abilita questa funzionalità. |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | Unità = giorni, ore, minuti, secondi, millisecondi.  Scrivere sempre in lettere maiuscole l'unità. </br> Definire il numero di unità, passando l'intero desiderato per timeBucketSize.  Si noti che l'arco temporale è di 7 giorni.  |
| `timezoneOffset=-<integer>` | L'intero viene sempre espresso in millisecondi. </br> Si noti che questa funzionalità è leggermente diversa rispetto a quella consentita in Esplora Time Series Insights, in cui è possibile scegliere locale (tempo del browser) o ora UTC. |

### <a name="examples"></a>esempi

Per aggiungere definizioni di serie temporali a un ambiente Time Series Insights come parametro URL, aggiungere:

```plaintext
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Usare le definizioni di esempio della serie temporale per:

* ID ambiente
* Ultimi 60 minuti di dati
* Termini (F1PressureID, F2TempStation e F3VibrationPL) che comprendono i parametri facoltativi

Per una vista è possibile costruire l'URL con parametri seguente:

```plaintext
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

> [!TIP]
> Vedere Esplora risorse Live [utilizzando l'URL](https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]).

L'URL sopra riportato descrive e compila la visualizzazione Time Series Insights Explorer:

[Termini di ![Time Series Insights Explorer](media/parameterized-url/url1.png)](media/parameterized-url/url1.png#lightbox)

Visualizzazione completa (incluso il grafico):

[visualizzazione grafico ![](media/parameterized-url/url2.png)](media/parameterized-url/url2.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [eseguire query sui C#dati usando ](time-series-insights-query-data-csharp.md).

* Informazioni sul [Time Series Insights Explorer](./time-series-insights-explorer.md).
