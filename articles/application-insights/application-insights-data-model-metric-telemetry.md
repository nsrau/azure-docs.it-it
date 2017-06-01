---
title: Modello di dati di Azure Application Insights Telemetry - Metrica dei dati di telemetria | Microsoft Docs
description: Modello di dati di Application Insights per la metrica dei dati di telemetria
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/25/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: e22bd56e0d111add6ab4c08b6cc6e51c364c7f22
ms.openlocfilehash: 069457d7ef7855749f64e299e0db579e6bb2b590
ms.contentlocale: it-it
ms.lasthandoff: 05/19/2017


---
# <a name="metric-telemetry-application-insights-data-model"></a>Metrica dei dati di telemetria: modello di dati di Application Insights

Esistono due tipi di telemetria della metrica supportata da [Application Insights](app-insights-overview.md): la metrica a misura singola e la metrica preaggregata. La misura singola è costituita semplicemente da un nome e un valore. La metrica preaggregata specifica i valori minimo e massimo della metrica nell'intervallo di aggregazione e la relativa deviazione standard.

La metrica dei dati di telemetria preaggregata presuppone che il periodo di aggregazione sia di un minuto.

Esistono diversi nomi di metrica noti supportati da Application Insights. 

Metrica che rappresenta i contatori di sistema e di processo:

| **Nome .NET**             | **Nome indipendente dalla piattaforma** | **Nome API REST** | **Descrizione**
| ------------------------- | -------------------------- | ----------------- | ---------------- 
| `\Processor(_Total)\% Processor Time` | Lavoro in corso... | [processorCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessorCpuPercentage) | CPU computer totale
| `\Memory\Available Bytes`                 | Lavoro in corso... | [memoryAvailableBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FmemoryAvailableBytes) | memoria disponibile su disco
| `\Process(??APP_WIN32_PROC??)\% Processor Time` | Lavoro in corso... | [processCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessCpuPercentage) | CPU del processo che ospita l'applicazione
| `\Process(??APP_WIN32_PROC??)\Private Bytes`      | Lavoro in corso... | [processPrivateBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessPrivateBytes) | memoria usata dal processo che ospita l'applicazione
| `\Process(??APP_WIN32_PROC??)\IO Data Bytes/sec` | Lavoro in corso... | [processIOBytesPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessIOBytesPerSecond) | frequenza delle operazioni I/O eseguite dal processo che ospita l'applicazione
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec`             | Lavoro in corso... | [requestsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsPerSecond) | frequenza delle richieste elaborate dall'applicazione 
| `\.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec`    | Lavoro in corso... | [exceptionsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FexceptionsPerSecond) | frequenza delle eccezioni generate dall'applicazione
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time`   | Lavoro in corso... | [requestExecutionTime](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestExecutionTime) | tempo di esecuzione medio delle richieste
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue` | Lavoro in corso... | [requestsInQueue](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsInQueue) | numero di richieste in attesa dell'elaborazione in una coda

## <a name="name"></a>Nome

Nome della metrica che si vuole visualizzare nel portale di Application Insights e nell'interfaccia utente. 

## <a name="value"></a>Valore

Singolo valore per la misura. Somma delle singole misure per l'aggregazione.

## <a name="count"></a>Numero

Peso della metrica aggregata. Non deve essere impostato per una misura.

## <a name="min"></a>Min

Valore minimo della metrica aggregata. Non deve essere impostato per una misura.

## <a name="max"></a>Max

Valore massimo della metrica aggregata. Non deve essere impostato per una misura.

## <a name="standard-deviation"></a>Deviazione standard

Deviazione standard della metrica aggregata. Non deve essere impostato per una misura.

## <a name="custom-properties"></a>Proprietà personalizzate

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come usare l'[API di Application Insights per metriche ed eventi personalizzati](app-insights-api-custom-events-metrics.md#trackmetric).
- Per informazioni sul modello di dati e sui tipi di Application Insights, vedere il [modello di dati](application-insights-data-model.md).
- Verificare quali [piattaforme](app-insights-platforms.md) supportano Application Insights.

