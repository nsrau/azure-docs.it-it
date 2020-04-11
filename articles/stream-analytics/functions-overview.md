---
title: Funzioni definite dall'utente in Analisi di flusso di AzureUser-defined functions in Azure Stream Analytics
description: Questo articolo è una panoramica delle funzioni definite dall'utente in Analisi di flusso di Azure.This article is an overview of user-defined functions in Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: b29d66e8bb213fbbb162c3249f022e0783f9f62f
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115586"
---
# <a name="user-defined-functions-in-azure-stream-analytics"></a>Funzioni definite dall'utente in Analisi di flusso di AzureUser-defined functions in Azure Stream Analytics

Il linguaggio di query di tipo SQL in Analisi di flusso di Azure semplifica l'implementazione della logica di analisi in tempo reale nei dati di streaming. Analisi di flusso offre maggiore flessibilità tramite funzioni personalizzate richiamate nella query. L'esempio di codice seguente `sampleFunction` è una funzione definita dall'utente chiamata che accetta un parametro, ogni record di input ricevuto dal processo e il risultato viene scritto nell'output come `sampleResult`.

```sql
SELECT 
    UDF.sampleFunction(InputStream) AS sampleResult 
INTO 
    output 
FROM 
    InputStream 
```

## <a name="types-of-functions"></a>Tipi di funzioni

Analisi di flusso di Azure supporta i quattro tipi di funzione seguenti:Azure Stream Analytics supports the following four function types: 

* Funzioni JavaScript definite dall'utente 
* aggregazioni JavaScript definite dall'utente 
* Funzioni definite dall'utente di C '(tramite Visual Studio)C's user-defined functions (using Visual Studio) 
* Azure Machine Learning 

È possibile usare queste funzioni per scenari come il punteggio in tempo reale usando modelli di apprendimento automatico, manipolazioni di stringhe, calcoli matematici complessi, codifica e decodifica dei dati. 

## <a name="limitations"></a>Limitazioni

Le funzioni definite dall'utente sono senza stato e il valore restituito può essere solo un valore scalare. Non è possibile chiamare endpoint REST esterni da queste funzioni definite dall'utente, in quanto probabilmente influirà sulle prestazioni del processo. 

Analisi di flusso di Azure non mantiene un record di tutte le chiamate di funzioni e dei risultati restituiti. Per garantire la ripetibilità, ad esempio la riesecuzione del processo da timestamp precedente, `Date.GetData()` `Math.random()`non utilizzare nuovamente funzioni come o , poiché queste funzioni non restituiscono lo stesso risultato per ogni chiamata.  

## <a name="diagnostic-logs"></a>Log di diagnostica

Eventuali errori di runtime vengono considerati irreversibili e vengono rilevati tramite log di attività e diagnostica. È consigliabile che la funzione gestisca tutte le eccezioni e gli errori e restituisca un risultato valido alla query. Ciò impedirà al processo di passare allo [stato Non riuscito.](job-states.md)  


## <a name="next-steps"></a>Passaggi successivi

* [Funzioni JavaScript definite dall'utente in Analisi di flusso di Azure](stream-analytics-javascript-user-defined-functions.md)
* [Aggregazioni definite dall'utente JavaScript di Azure Stream AnalyticsAzure Stream Analytics JavaScript user-defined aggregates](stream-analytics-javascript-user-defined-aggregates.md)
* [Sviluppare funzioni definite dall'utente .NET Standard per i processi di Analisi di flusso di AzureDevelop .NET Standard user-defined functions for Azure Stream Analytics jobs](stream-analytics-edge-csharp-udf-methods.md)
* [Integrare Analisi di flusso di Azure con Azure Machine Learning](machine-learning-udf.md)

