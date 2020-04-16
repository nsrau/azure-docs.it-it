---
title: Condividere visualizzazioni personalizzate con URL con parametri - Azure Time Series Insights Documenti Microsoft
description: Informazioni su come creare URL con parametri per condividere facilmente visualizzazioni esploratorie personalizzate in Azure Time Series Insights.Learn how to create parameterized URLs to easily share customized explorer views in Azure Time Series Insights.
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417002"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>Condividere una visualizzazione personalizzata usando un URL con parametri

Per condividere una visualizzazione personalizzata in Time Series Insights Explorer, è possibile creare a livello di codice un URL con parametri della visualizzazione personalizzata.

Time Series Insights Explorer supporta i parametri di query URL per specificare le visualizzazioni nell'esperienza direttamente dall'URL. Ad esempio, usando solo l'URL, è possibile specificare un ambiente di destinazione, un predicato di ricerca e l'intervallo di tempo desiderato. Quando un utente seleziona l'URL personalizzato, l'interfaccia fornisce un collegamento direttamente a tale asset nel portale Time Series Insights. Vengono applicati i criteri di accesso ai dati.

> [!TIP]
> * Visualizza la demo gratuita [di Time Series Insights](https://insights.timeseries.azure.com/samples).
> * Leggere la documentazione di [Time Series Insights Explorer.](./time-series-insights-explorer.md)

## <a name="environment-id"></a>ID di ambiente

Il parametro `environmentId=<guid>` specifica l'ID dell'ambiente di destinazione. È un componente dell'FQDN di accesso ai dati ed è possibile trovarlo nell'angolo superiore destro della panoramica dell'ambiente nel portale di Azure.It's a component of the data access FQDN, and you can find it in the top-right corner of the environment overview in the Azure portal. È tutto ciò che `env.timeseries.azure.com`precede .

Un parametro ID di ambiente di esempio è `?environmentId=10000000-0000-0000-0000-100000000108`.

## <a name="time"></a>Tempo

È possibile specificare valori assoluti o relativi con un URL con parametri.

### <a name="absolute-time-values"></a>Valori assoluti

Per i valori assoluti, usare i parametri `from=<integer>` e `to=<integer>`.

* `from=<integer>` è un valore in millisecondi JavaScript indicante l'ora di inizio dell'intervallo di ricerca.
* `to=<integer>` è un valore in millisecondi JavaScript indicante l'ora di fine dell'intervallo di ricerca.

> [!TIP]
> Per tradurre facilmente le date in millisecondi JavaScript, prova te [epoc & Unix Timestamp Converter](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html).

### <a name="relative-time-values"></a>Valori relativi

Per un valore di `relativeMillis=<value>`ora relativo, utilizzare , dove *value* è in JavaScript millisecondi dal timestamp più recente ricevuto dall'API.

`&relativeMillis=3600000` ad esempio visualizza gli ultimi 60 minuti di dati.

I valori accettati corrispondono al menu **rapido dell'esplorazione** di Time Series Insights e includono:

* `1800000`(Ultimi 30 min)
* `3600000`(Ultimi 60 min)
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
| **name** | `\<string>` | Nome del *termine*. |
| **splitBy** | `\<string>` | Nome della colonna *in base a cui dividere*. |
| **measureName (nome misura)** | `\<string>` | Nome della colonna della *misura*. |
| **Predicato** | `\<string>` | Clausola *where* per i filtri lato server. |
| **useSum** | `true` | Parametro facoltativo che specifica l'utilizzo della somma per la misura. |

> [!NOTE]
> Se `Events` è la misura **useSum** selezionata, conteggio è selezionato per impostazione predefinita.  
> Se `Events` l'opzione non è selezionata, l'opzione Media è selezionata per impostazione predefinita. |

* La `multiChartStack=<true/false>` coppia chiave-valore consente l'impilamento nel grafico.
* La `multiChartSameScale=<true/false>` coppia chiave-valore consente la stessa scala dell'asse Y tra i termini all'interno di un parametro facoltativo.  
* Consente `timeBucketUnit=<Unit>&timeBucketSize=<integer>` di regolare il dispositivo di scorrimento dell'intervallo per fornire una visualizzazione più granulare o più uniforme e più aggregata del grafico.  
* Il `timezoneOffset=<integer>` parametro consente di impostare il fuso orario in cui visualizzare il grafico come offset per UTC.

| Coppie(e) | Descrizione |
| --- | --- |
| `multiChartStack=false` | `true`è abilitato per `false` impostazione predefinita in modo da passare a stack. |
| `multiChartStack=false&multiChartSameScale=true` | È necessario abilitare l'impilamento per usare la stessa scala dell'asse Y in tutti i termini.  È `false` per impostazione predefinita, quindi il passaggio `true` abilita questa funzionalità. |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | Unità `days`di `hours` `minutes`misura `seconds` `milliseconds`, , , , , .  Scrivere sempre in lettere maiuscole l'unità. </br> Definire il numero di unità passando l'intero desiderato per **timeBucketSize**.  |
| `timezoneOffset=-<integer>` | L'intero viene sempre espresso in millisecondi. |

> [!NOTE]
> **Valori timeBucketUnit** possono essere smussati fino a 7 giorni.
> i valori **timezoneOffset** non sono né UTC né ora locale.

### <a name="examples"></a>Esempi

Per aggiungere definizioni di serie temporali a un ambiente Time Series Insights come parametro URL, aggiungere:

```URL parameter
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Utilizzare le definizioni di serie temporali di esempio per:Use the example time series definitions for:

* ID ambiente
* Ultimi 60 minuti di dati
* I termini (**F1PressureID**, **F2TempStation**e **F3VibrationPL**) che comprendono i parametri facoltativi

È possibile costruire il seguente URL con parametri per una vista:

```URL
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

[![URL con parametri di Time Series Insights Explorer](media/parameterized-url/share-parameterized-url.png)](media/parameterized-url/share-parameterized-url.png#lightbox)

> [!TIP]
> Guarda l'Explorer in diretta [usando l'esempio di URL](https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]) precedente.

L'URL precedente descrive e visualizza la visualizzazione di esplora risorse Time Series Insights con parametri. 

* Predicati con parametri.

  [![Predicati con parametri di Esplora serie time.](media/parameterized-url/share-parameterized-url-predicates.png)](media/parameterized-url/share-parameterized-url-predicates.png#lightbox)

* Visualizzazione completa del grafico condivisa.

  [![Visualizzazione completa del grafico condivisa.](media/parameterized-url/share-parameterized-url-full-chart.png)](media/parameterized-url/share-parameterized-url-full-chart.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come eseguire query sui [dati utilizzando C.](time-series-insights-query-data-csharp.md)

* Informazioni su [Time Series Insights Explorer](./time-series-insights-explorer.md).
