---
title: Funzioni definite dall'utente in analisi di flusso di Azure
description: Questo articolo è una panoramica delle funzioni definite dall'utente in analisi di flusso di Azure.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: d167c603ada885a1a4917c66bab110e4ce38cab4
ms.sourcegitcommit: acc558d79d665c8d6a5f9e1689211da623ded90a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82598369"
---
# <a name="user-defined-functions-in-azure-stream-analytics"></a>Funzioni definite dall'utente in analisi di flusso di Azure

Il linguaggio di query simile a SQL in analisi di flusso di Azure consente di implementare in modo semplice la logica di analisi in tempo reale sui dati di streaming. Analisi di flusso offre maggiore flessibilità tramite funzioni personalizzate richiamate nella query. L'esempio di codice seguente è una funzione `sampleFunction` definita dall'utente denominata che accetta un parametro, ogni record di input ricevuto dal processo e il risultato viene scritto `sampleResult`nell'output come.

```sql
SELECT 
    UDF.sampleFunction(InputStream) AS sampleResult 
INTO 
    output 
FROM 
    InputStream 
```

## <a name="types-of-functions"></a>Tipi di funzioni

Analisi di flusso di Azure supporta i quattro tipi di funzioni seguenti: 

* Funzioni JavaScript definite dall'utente 
* aggregazioni JavaScript definite dall'utente 
* Funzioni definite dall'utente C# (con Visual Studio) 
* Azure Machine Learning 

È possibile usare queste funzioni per scenari come il punteggio in tempo reale usando modelli di apprendimento automatico, modifiche di stringhe, calcoli matematici complessi, codifica e decodifica dei dati. 

## <a name="limitations"></a>Limitazioni

Le funzioni definite dall'utente sono senza stato e il valore restituito può essere solo un valore scalare. Non è possibile chiamare gli endpoint REST esterni da queste funzioni definite dall'utente, perché probabilmente influirà sulle prestazioni del processo. 

Analisi di flusso di Azure non conserva un record di tutte le chiamate di funzioni e i risultati restituiti. Per garantire la ripetibilità. ad esempio, la riesecuzione del processo dal timestamp precedente produce gli stessi risultati. non usare funzioni come `Date.GetData()` o `Math.random()`, perché queste funzioni non restituiscono lo stesso risultato per ogni chiamata.  

## <a name="resource-logs"></a>Log risorse

Tutti gli errori di runtime sono considerati irreversibili e vengono esposti tramite log attività e risorse. È consigliabile che la funzione gestisca tutte le eccezioni e gli errori e restituisca un risultato valido alla query. In questo modo si impedisce al processo di passare a uno [stato di errore](job-states.md).  

## <a name="exception-handling"></a>Gestione delle eccezioni

Qualsiasi eccezione durante l'elaborazione dei dati viene considerata un errore irreversibile quando si utilizzano i dati in analisi di flusso di Azure. Le funzioni definite dall'utente hanno un potenziale maggiore per generare eccezioni e causare l'interruzione dell'elaborazione. Per evitare questo problema, usare un blocco *try-catch* in JavaScript o C# per rilevare le eccezioni durante l'esecuzione del codice. Le eccezioni rilevate possono essere registrate e gestite senza causare un errore di sistema. Si consiglia di eseguire sempre il wrapping del codice personalizzato in un blocco *try-catch* per evitare la generazione di eccezioni impreviste nel motore di elaborazione.

## <a name="next-steps"></a>Passaggi successivi

* [Funzioni JavaScript definite dall'utente in Analisi di flusso di Azure](stream-analytics-javascript-user-defined-functions.md)
* [Funzioni di aggregazione JavaScript definite dall'utente in analisi di flusso di Azure](stream-analytics-javascript-user-defined-aggregates.md)
* [Sviluppare .NET Standard funzioni definite dall'utente per i processi di analisi di flusso di Azure](stream-analytics-edge-csharp-udf-methods.md)
* [Integrare analisi di flusso di Azure con Azure Machine Learning](machine-learning-udf.md)
