---
title: Ridimensionare le funzioni di Machine Learning in Analisi di flusso di Azure
description: Questo articolo descrive come ridimensionare processi di Analisi di flusso che usano funzioni di Machine Learning configurando unità di partizionamento e di flusso.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 5b08625d055063b3804a35a3344ff01c7edb79de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066995"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-studio-classic-functions"></a>Ridimensionare il processo di Analisi di flusso con le funzioni di Azure Machine Learning Studio (classico)Scale your Stream Analytics job with Azure Machine Learning Studio (classic) functions

> [!TIP]
> È consigliabile usare [le funzioni definite dall'utente](machine-learning-udf.md) di Azure Machine Learning anziché la funzione definita dall'utente di Azure Machine Learning Studio (classica) per migliorare le prestazioni e l'affidabilità.

Questo articolo illustra come ridimensionare in modo efficiente i processi di Analisi di flusso di Azure che usano le funzioni di Azure Machine Learning.This article discusses how to efficiently scale Azure Stream Analytics jobs that use Azure Machine Learning functions. Per informazioni su come ridimensionare processi di Analisi di flusso in generale, vedere l'articolo relativo al [ridimensionamento dei processi](stream-analytics-scale-jobs.md).

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>Che cos'è una funzione di Azure Machine Learning in Analisi di flusso?

Una funzione di Machine Learning in Analisi di flusso può essere usata come una normale chiamata di funzione nel linguaggio di query di Analisi di flusso. Dietro le quinte, tuttavia, queste chiamate di funzione sono in realtà le richieste del servizio Web di Azure Machine Learning.Behind the scenes, however, these function calls are actually Azure Machine Learning Web Service requests.

È possibile migliorare la velocità effettiva delle richieste del servizio Web di Machine Learning "in batch" più righe insieme nella stessa chiamata API del servizio Web.You can improve the throughput of Machine Learning web service requests by "batching" multiple rows together in the same web service API call. Questo raggruppamento è denominato mini-batch. Per altre informazioni, vedere Servizi Web di [Azure Machine Learning Studio (classici).](../machine-learning/studio/consume-web-services.md) Il supporto per Azure Machine Learning Studio (classico) in Analisi di flusso è in anteprima.

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>Configurare un processo di Analisi di flusso con funzioni di Machine Learning

Esistono due parametri per configurare la funzione di Machine Learning utilizzata dal processo di Analisi di flusso:There are two parameters to configure the Machine Learning function used by your Stream Analytics job:

* Dimensioni batch delle chiamate di funzione di Machine Learning.
* Numero di unità di streaming (SUs) di cui è stato eseguito il provisioning per il processo di Analisi di flusso.

Per determinare i valori appropriati per le sole di accesso utenti, decidere se si desidera ottimizzare la latenza del processo di Analisi di flusso o la velocità effettiva di ogni su. Le ssu possono sempre essere aggiunte a un processo per aumentare la velocità effettiva di una query di Analisi di flusso ben partizionata. Le cudue aggiuntive aumentano il costo di esecuzione del processo.

Determinare la *tolleranza* di latenza per il processo di Analisi di flusso. L'aumento delle dimensioni del batch aumenterà la latenza delle richieste di Azure Machine Learning e la latenza del processo di Analisi di flusso.

L'aumento delle dimensioni del batch consente al processo Analisi di flusso di elaborare **più eventi** con lo **stesso numero** di richieste del servizio Web di Machine Learning. L'aumento della latenza del servizio Web di Machine Learning è in genere sublineare all'aumento delle dimensioni del batch. 

È importante considerare le dimensioni del batch più efficienti in termini di costi per un servizio Web di Machine Learning in una determinata situazione. La dimensione batch predefinita per le richieste del servizio Web è 1000.The default batch size for web service requests is 1000. È possibile modificare questa dimensione predefinita usando [l'API REST](https://docs.microsoft.com/previous-versions/azure/mt653706(v=azure.100) "API REST di Analisi di flusso") di Analisi di flusso o il client [PowerShell per Analisi di](stream-analytics-monitor-and-manage-jobs-use-powershell.md)flusso.

Dopo aver deciso una dimensione batch, è possibile impostare il numero di unità di streaming (SU), in base al numero di eventi che la funzione deve elaborare al secondo. Per altre informazioni sulle unità di streaming, vedere [Processi di scalabilità di Analisi di flusso](stream-analytics-scale-jobs.md).

Ogni 6 di ssu ottengono 20 connessioni simultanee al servizio Web Machine Learning. Tuttavia, 1 processo SU e 3 processi SU ottengono 20 connessioni simultanee.  

Se l'applicazione genera 200.000 eventi al secondo e la dimensione del batch è 1000, la latenza del servizio Web risultante è 200 ms. Questa frequenza significa che ogni connessione può effettuare cinque richieste al servizio Web Machine Learning ogni secondo. Con 20 connessioni, il processo analisi di flusso può elaborare 20.000 eventi in 200 ms e 100.000 eventi in un secondo.

Per elaborare 200.000 eventi al secondo, il processo di Analisi di flusso richiede 40 connessioni simultanee, che provengono a 12 digestione di dominio. Il diagramma seguente illustra le richieste dal processo di Analisi di flusso all'endpoint servizio di Web Machine Learning. Ogni 6 unità di streaming sono presenti al massimo 20 connessioni simultanee al servizio Web di Machine Learning.

![Esempio di due processi con scalabilità di Analisi di flusso con funzioni di Machine LearningScale Stream Analytics with Machine Learning Functions two job example](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "Esempio di due processi con scalabilità di Analisi di flusso con funzioni di Machine LearningScale Stream Analytics with Machine Learning Functions two job example")

In generale, posto che ***B*** sta per dimensioni batch e ***L*** sta per latenza del servizio Web per le dimensioni batch B in millisecondi, la velocità effettiva di un processo di Analisi di flusso con ***N*** unità di streaming è:

![Formula per ridimensionare Analisi di flusso con funzioni di Machine Learning](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Formula per ridimensionare Analisi di flusso con funzioni di Machine Learning")

È inoltre possibile configurare il 'numero massimo di chiamate simultanee' nel servizio Web di Machine Learning.You can also configure the 'max concurrent calls' on the Machine Learning web service. Si consiglia di impostare questo parametro sul valore massimo (200 attualmente).

Per altre informazioni su questa impostazione, vedere l'articolo [Ridimensionamento di un servizio Web di Azure Machine Learning](../machine-learning/studio/scaling-webservice.md).

## <a name="example--sentiment-analysis"></a>Esempio: Analisi di valutazione
L'esempio seguente include un processo di Analisi di flusso con la funzione di Machine Learning di analisi di valutazione, come descritto nell' [esercitazione sull'integrazione tra Analisi di flusso e Machine Learning](stream-analytics-machine-learning-integration-tutorial.md).

La query è una semplice query completamente partizionata seguita dalla funzione di **sentiment**, come illustrato nell'esempio seguente:

```SQL
    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery
```

Esaminiamo la configurazione necessaria per creare un processo di Analisi di flusso, che esegue l'analisi del sentiment dei tweet a una velocità di 10.000 tweet al secondo.

Usando 1 SU, questo processo di Analisi di flusso potrebbe gestire il traffico? Il processo può tenere il passo con l'input utilizzando la dimensione batch predefinita di 1000.The job can keep up with the input using the default batch size of 1000. La latenza predefinita del servizio Web Machine Learning di analisi del sentiment (con una dimensione batch predefinita di 1000) crea non più di un secondo di latenza.

La latenza **complessiva** o end-to-end del processo di Analisi di flusso è in genere di pochi secondi. Esaminare ora più da vicino questo processo di Analisi di flusso, *in particolare* le chiamate di funzione di Machine Learning. Con una dimensione batch pari a 1000, una velocità effettiva di 10.000 eventi richiede circa 10 richieste al servizio Web. Anche con una unità di streaming, il numero di connessioni simultanee è sufficiente per gestire il traffico di input.

Se la frequenza degli eventi di input aumenta di 100 volte, il processo di Analisi di flusso deve elaborare 1.000.000 di tweet al secondo. Per sostenere l'aumentata richiesta, sono disponibili due opzioni:

1. Aumentare le dimensioni del batch.
2. Partizionare il flusso di input per elaborare gli eventi in parallelo.

Con la prima opzione aumenta la **latenza** del processo.

Con la seconda opzione, sarà necessario eseguire il provisioning di più di sUS per avere più richieste simultanee del servizio Web di Machine Learning. Questo maggior numero di SU, aumenta il **costo**del lavoro .

Esaminiamo il ridimensionamento usando le misure di latenza seguenti per ogni dimensione del batch:Let's look at the scaling using the following latency measurements for each batch size:

| Latenza | Dimensioni dei batch |
| --- | --- |
| 200 ms | Lotti di 1000 eventi o inferiori |
| 250 ms | Lotti di 5.000 eventi |
| 300 ms | Lotti di 10.000 eventi |
| 500 ms | 25.000 batch di eventi |

1. Utilizzo della prima opzione (**senza effettuare ** il provisioning di altre unità di streaming). È possibile aumentare le dimensioni del batch a **25.000**. Aumentando le dimensioni del batch in questo modo, il processo consentirà al processo di elaborare 1.000.000 di eventi con 20 connessioni simultanee al servizio Web Machine Learning (con una latenza di 500 ms per chiamata). La latenza aggiuntiva del processo di Analisi di flusso dovuta alle richieste della funzione di valutazione rispetto alle richieste del servizio Web di Machine Learning aumenterebbe da **200 ms** a **500 ms**. Tuttavia, le dimensioni del batch **non possono** essere aumentate all'infinito poiché i servizi Web di Machine Learning richiedono che la dimensione del payload di una richiesta sia di 4 MB o inferiore e il timeout delle richieste del servizio Web dopo 100 secondi di funzionamento.
1. Se si usa la seconda opzione, le dimensioni batch rimangono pari a 1000. Con una latenza del servizio Web di 200 ms, ogni 20 connessioni simultanee al servizio Web sarebbe possibile elaborare 1000 * 20 * 5 eventi = 100.000 al secondo. Per elaborare 1.000.000 di eventi al secondo, quindi, il processo richiederebbe 60 unità di streaming. Rispetto alla prima opzione, il processo di Analisi di flusso invierebbe più richieste batch al servizio Web, generando così un costo maggiore.

Di seguito è riportata una tabella della velocità effettiva del processo di Analisi di flusso per diverse unità di streaming e dimensioni batch, in numero di eventi al secondo.

| Dimensioni batch (latenza ML) | 500 (200 ms) | 1.000 (200 ms) | 5.000 (250 ms) | 10.000 (300 ms) | 25.000 (500 ms) |
| --- | --- | --- | --- | --- | --- |
| **1 unità di archiviazione** |2.500 |5.000 |20.000 |30.000 |50.000 |
| **3 unità di archiviazione** |2.500 |5.000 |20.000 |30.000 |50.000 |
| **6 unità di archiviazione** |2.500 |5.000 |20.000 |30.000 |50.000 |
| **12 unità di archiviazione** |5.000 |10,000 |40.000 |60.000 |100,000 |
| **18 unità di archiviazione** |7.500 |15.000 |60.000 |90.000 |150.000 |
| **24 unità di archiviazione** |10,000 |20.000 |80.000 |120.000 |200.000 |
| **…** |… |… |… |… |… |
| **60 unità di archiviazione** |25.000 |50.000 |200.000 |300.000 |500.000 |

A questo punto dovrebbe essere chiaro il funzionamento delle funzioni di Machine Learning in Analisi di flusso. I processi di Analisi di flusso eseguono il pull dei dati dalle origini dati e ogni pull restituisce un batch di eventi al processo di Analisi di flusso per l'elaborazione. Come influisce tale modello pull sulle richieste al servizio Web Machine Learning?

In genere, le dimensioni del batch impostate per le funzioni di Machine Learning non saranno esattamente divisibili per il numero di eventi restituiti da ogni processo di Analisi di flusso "pull". Quando ciò accade, il servizio Web di Machine Learning viene chiamato con batch parziali. L'utilizzo di batch parziali evita di incorrere in un sovraccarico aggiuntivo della latenza dei processi negli eventi coalescing da pull a pull.

## <a name="new-function-related-monitoring-metrics"></a>Nuove metriche di monitoraggio correlate alle funzioni
Nell'area di monitoraggio di un processo di Analisi di flusso sono state aggiunte altre tre metriche relative alle funzioni. Si tratta di **REQUESTS FUNCTION**, **FUNCTION EVENTS** e FAILED **FUNCTION REQUESTS**, come illustrato nel grafico seguente.

![Metriche per ridimensionare Analisi di flusso con funzioni di Machine Learning](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "Metriche per ridimensionare Analisi di flusso con funzioni di Machine Learning")

Di seguito sono riportate le rispettive definizioni.

**FUNCTION REQUESTS**: numero di richieste di funzione.

**FUNCTION EVENTS**: numero di eventi nelle richieste di funzione.

**FAILED FUNCTION REQUESTS**: numero di richieste di funzione non riuscite.

## <a name="key-takeaways"></a>Risultati principali

Per ridimensionare un processo di Analisi di flusso con le funzioni di Machine Learning, considerare i fattori seguenti:To scale a Stream Analytics job with Machine Learning functions, consider the following factors:

1. Frequenza degli eventi di input.
2. Latenza tollerata per il processo di Analisi di flusso in esecuzione (e quindi le dimensioni del batch richieste dal servizio Web di Machine Learning).
3. Le ssu di Stream Analytics di cui è stato eseguito il provisioning e il numero di richieste del servizio Web di Machine Learning (i costi aggiuntivi correlati alle funzioni).

È stata usata come esempio una query di Analisi di flusso completamente partizionata. Se è necessaria una query più complessa, è possibile usare il [forum di Analisi di flusso di Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics) per ottenere altre informazioni dal team di Analisi di flusso.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su Analisi di flusso, vedere:

* [Introduzione all'uso di Analisi di flusso di AzureGet started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Guida di riferimento al linguaggio di query di Analisi di flusso di AzureAzure Stream Analytics Query Language Reference](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
