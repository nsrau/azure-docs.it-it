---
title: Condividere visualizzazioni personalizzate con URL con parametri-Azure Time Series Insights | Microsoft Docs
description: Informazioni su come creare URL con parametri per condividere facilmente visualizzazioni personalizzate di Esplora risorse in Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: conceptual
ms.workload: big-data
ms.date: 04/15/2020
ms.custom: seodec18
ms.openlocfilehash: 10616c8003d9bbbe42cb70bd1bac4193044907c0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81417002"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>Condividere una visualizzazione personalizzata usando un URL con parametri

Per condividere una visualizzazione personalizzata in Time Series Insights Explorer, è possibile creare a livello di codice un URL con parametri della visualizzazione personalizzata.

Esplora Time Series Insights supporta i parametri di query URL per specificare le viste nell'esperienza direttamente dall'URL. Ad esempio, usando solo l'URL, è possibile specificare un ambiente di destinazione, un predicato di ricerca e l'intervallo di tempo desiderato. Quando un utente seleziona l'URL personalizzato, l'interfaccia fornisce un collegamento direttamente a tale risorsa nel portale di Time Series Insights. Vengono applicati i criteri di accesso ai dati.

> [!TIP]
> * Visualizza la [demo Time Series Insights](https://insights.timeseries.azure.com/samples)gratuita.
> * Leggere la documentazione di [Time Series Insights Explorer](./time-series-insights-explorer.md) associata.

## <a name="environment-id"></a>ID di ambiente

Il parametro `environmentId=<guid>` specifica l'ID dell'ambiente di destinazione. Si tratta di un componente del FQDN di accesso ai dati, che è possibile trovare nell'angolo superiore destro della panoramica dell'ambiente nella portale di Azure. È tutto ciò che precede `env.timeseries.azure.com`.

Un parametro ID di ambiente di esempio è `?environmentId=10000000-0000-0000-0000-100000000108`.

## <a name="time"></a>Tempo

È possibile specificare valori assoluti o relativi con un URL con parametri.

### <a name="absolute-time-values"></a>Valori assoluti

Per i valori assoluti, usare i parametri `from=<integer>` e `to=<integer>`.

* `from=<integer>` è un valore in millisecondi JavaScript indicante l'ora di inizio dell'intervallo di ricerca.
* `to=<integer>` è un valore in millisecondi JavaScript indicante l'ora di fine dell'intervallo di ricerca.

> [!TIP]
> Per convertire facilmente le date in millisecondi JavaScript, provare il [convertitore di timestamp di Epoch & UNIX](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html).

### <a name="relative-time-values"></a>Valori relativi

Per un valore di ora relativa, `relativeMillis=<value>`usare, dove *value* è in millisecondi JavaScript dal timestamp più recente ricevuto dall'API.

`&relativeMillis=3600000` ad esempio visualizza gli ultimi 60 minuti di dati.

I valori accettati corrispondono al menu **ora rapido** Esplora Time Series Insights e includono:

* `1800000`(Ultimi 30 minuti)
* `3600000`(Ultimi 60 minuti)
* `10800000`(Ultime 3 ore)
* `21600000`(Ultime 6 ore)
* `43200000`(Ultime 12 ore)
* `86400000`(Ultime 24 ore)
* `604800000`(Ultimi 7 giorni)
* `2592000000`(Ultime 30 ore)

### <a name="optional-parameters"></a>Parametri facoltativi

Il `timeSeriesDefinitions=<collection of term objects>` parametro specifica i termini del predicato che verranno visualizzati in una visualizzazione Time Series Insights:

| Parametro | Elemento URL | Descrizione |
| --- | --- | --- |
| **nome** | `\<string>` | Nome del *termine*. |
| **splitBy** | `\<string>` | Nome della colonna *in base a cui dividere*. |
| **measureName** | `\<string>` | Nome della colonna della *misura*. |
| **predicato** | `\<string>` | Clausola *where* per i filtri lato server. |
| **USA** | `true` | Parametro facoltativo che specifica l'utilizzo di Sum per la misura. |

> [!NOTE]
> Se `Events` è la misura di **utilizzo** selezionata, il conteggio è selezionato per impostazione predefinita.  
> Se `Events` non è selezionata, per impostazione predefinita viene selezionata l'opzione media. |

* La `multiChartStack=<true/false>` coppia chiave-valore consente lo stacking nel grafico.
* La `multiChartSameScale=<true/false>` coppia chiave-valore Abilita la stessa scala dell'asse Y in tutti i termini all'interno di un parametro facoltativo.  
* `timeBucketUnit=<Unit>&timeBucketSize=<integer>` Consente di regolare il dispositivo di scorrimento intervallo per fornire una visualizzazione più granulare o più uniforme del grafico.  
* Il `timezoneOffset=<integer>` parametro consente di impostare il fuso orario affinché il grafico venga visualizzato in come offset per l'ora UTC.

| Coppie | Descrizione |
| --- | --- |
| `multiChartStack=false` | `true`è abilitata per impostazione predefinita `false` , quindi passa allo stack. |
| `multiChartStack=false&multiChartSameScale=true` | È necessario abilitare l'impilamento per usare la stessa scala dell'asse Y in tutti i termini.  `false` Per impostazione predefinita, il passaggio `true` Abilita questa funzionalità. |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | Unità = `days`, `hours`, `minutes`, `seconds`, `milliseconds`.  Scrivere sempre in lettere maiuscole l'unità. </br> Definire il numero di unità passando l'intero desiderato per **timeBucketSize**.  |
| `timezoneOffset=-<integer>` | L'intero viene sempre espresso in millisecondi. |

> [!NOTE]
> i valori **timeBucketUnit** possono essere smussati fino a 7 giorni.
> i valori di **timezoneOffset** non sono UTC né l'ora locale.

### <a name="examples"></a>Esempi

Per aggiungere definizioni di serie temporali a un ambiente Time Series Insights come parametro URL, aggiungere:

```URL parameter
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Usare le definizioni di esempio della serie temporale per:

* ID ambiente
* Ultimi 60 minuti di dati
* Termini (**F1PressureID**, **F2TempStation**e **F3VibrationPL**) che comprendono i parametri facoltativi

Per una vista è possibile costruire l'URL con parametri seguente:

```URL
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

[![URL con parametri di Time Series Insights Explorer](media/parameterized-url/share-parameterized-url.png)](media/parameterized-url/share-parameterized-url.png#lightbox)

> [!TIP]
> Vedere la finestra di esplorazione in tempo reale [usando l'URL](https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]) riportato sopra.

L'URL precedente descrive e visualizza la vista con parametri Time Series Insights Explorer. 

* Predicati con parametri.

  [![Predicati con parametri di Time Series Insights Explorer.](media/parameterized-url/share-parameterized-url-predicates.png)](media/parameterized-url/share-parameterized-url-predicates.png#lightbox)

* Visualizzazione completa del grafico condivisa.

  [![Visualizzazione completa del grafico condivisa.](media/parameterized-url/share-parameterized-url-full-chart.png)](media/parameterized-url/share-parameterized-url-full-chart.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [eseguire query sui dati con C#](time-series-insights-query-data-csharp.md).

* Informazioni sul [Time Series Insights Explorer](./time-series-insights-explorer.md).
