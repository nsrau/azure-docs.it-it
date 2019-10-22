---
title: Modello di dati di Azure Application Insights Telemetry - Metrica dei dati di telemetria | Microsoft Docs
description: Modello di dati di Application Insights per la metrica dei dati di telemetria
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 816fa37ea052b18dab80bcc0d5c1528cd3d9a014
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678139"
---
# <a name="metric-telemetry-application-insights-data-model"></a>Metrica dei dati di telemetria: modello di dati di Application Insights

Esistono due tipi di telemetria della metrica supportata da [Application Insights](../../azure-monitor/app/app-insights-overview.md): la metrica a misura singola e la metrica preaggregata. La misura singola è costituita semplicemente da un nome e un valore. La metrica preaggregata specifica i valori minimo e massimo della metrica nell'intervallo di aggregazione e la relativa deviazione standard.

La metrica dei dati di telemetria preaggregata presuppone che il periodo di aggregazione sia di un minuto.

Esistono diversi nomi di metrica noti supportati da Application Insights. Metriche inserite nella tabella performanceCounters.

Metrica che rappresenta i contatori di sistema e di processo:

| **Nome .NET**             | **Nome indipendente dalla piattaforma** | **Nome API REST** | **Descrizione**
| ------------------------- | -------------------------- | ----------------- | ---------------- 
| `\Processor(_Total)\% Processor Time` | Lavoro in corso... | [processorCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessorCpuPercentage) | CPU computer totale
| `\Memory\Available Bytes`                 | Lavoro in corso... | [memoryAvailableBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FmemoryAvailableBytes) | Mostra la quantità di memoria fisica, espressa in byte, disponibile per l'esecuzione dei processi nel computer. Viene calcolata sommando la quantità di spazio negli elenchi di memoria azzerata, libera e in standby. La memoria libera è la memoria pronta all'uso. La memoria azzerata è composta da pagine di memoria piene di zero per impedire ai processi successivi di visualizzare i dati usati da un processo precedente. La memoria in standby è la memoria rimossa dal working set di un processo (la relativa memoria fisica) durante il trasferimento su disco, ma ancora disponibile per essere richiamata. Vedere [Oggetto Memory](https://msdn.microsoft.com/library/ms804008.aspx)
| `\Process(??APP_WIN32_PROC??)\% Processor Time` | Lavoro in corso... | [processCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessCpuPercentage) | CPU del processo che ospita l'applicazione
| `\Process(??APP_WIN32_PROC??)\Private Bytes`      | Lavoro in corso... | [processPrivateBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessPrivateBytes) | memoria usata dal processo che ospita l'applicazione
| `\Process(??APP_WIN32_PROC??)\IO Data Bytes/sec` | Lavoro in corso... | [processIOBytesPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessIOBytesPerSecond) | frequenza delle operazioni I/O eseguite dal processo che ospita l'applicazione
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec`             | Lavoro in corso... | [requestsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsPerSecond) | frequenza delle richieste elaborate dall'applicazione 
| `\.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec`    | Lavoro in corso... | [exceptionsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FexceptionsPerSecond) | frequenza delle eccezioni generate dall'applicazione
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time`   | Lavoro in corso... | [requestExecutionTime](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestExecutionTime) | tempo di esecuzione medio delle richieste
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue` | Lavoro in corso... | [requestsInQueue](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsInQueue) | numero di richieste in attesa dell'elaborazione in una coda

## <a name="name"></a>name

Nome della metrica che si vuole visualizzare nel portale di Application Insights e nell'interfaccia utente. 

## <a name="value"></a>Value

Singolo valore per la misura. Somma delle singole misure per l'aggregazione.

## <a name="count"></a>Conteggio

Peso della metrica aggregata. Non deve essere impostato per una misura.

## <a name="min"></a>Min

Valore minimo della metrica aggregata. Non deve essere impostato per una misura.

## <a name="max"></a>Max

Valore massimo della metrica aggregata. Non deve essere impostato per una misura.

## <a name="standard-deviation"></a>Deviazione standard

Deviazione standard della metrica aggregata. Non deve essere impostato per una misura.

## <a name="custom-properties"></a>Proprietà personalizzate

Le metriche con la proprietà personalizzata `CustomPerfCounter` impostata su `true` indicano che rappresentano il contatore delle prestazioni di Windows. Metriche inserite nella tabella performanceCounters. Non in customMetrics. Viene analizzato anche il nome di queste metriche per estrarre i nomi di categoria, contatore e istanza.

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come usare l'[API di Application Insights per metriche ed eventi personalizzati](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric).
- Per informazioni sul modello di dati e sui tipi di Application Insights, vedere il [modello di dati](data-model.md).
- Verificare quali [piattaforme](../../azure-monitor/app/platforms.md) supportano Application Insights.
