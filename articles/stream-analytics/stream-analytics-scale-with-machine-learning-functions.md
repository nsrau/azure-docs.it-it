---
title: Ridimensionare le funzioni di Machine Learning in Analisi di flusso di Azure
description: Questo articolo descrive come ridimensionare processi di Analisi di flusso che usano funzioni di Machine Learning configurando unità di partizionamento e di flusso.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 28734e5eaa693ca4ee31603863b69605a1d92c88
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467868"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-studio-classic-functions"></a>Ridimensionare il processo di analisi di flusso con le funzioni Azure Machine Learning Studio (classiche)

Questo articolo illustra come ridimensionare in modo efficiente i processi di analisi di flusso di Azure che usano funzioni Azure Machine Learning. Per informazioni su come ridimensionare processi di Analisi di flusso in generale, vedere l'articolo relativo al [ridimensionamento dei processi](stream-analytics-scale-jobs.md).

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>Che cos'è una funzione di Azure Machine Learning in Analisi di flusso?

Una funzione di Machine Learning in Analisi di flusso può essere usata come una normale chiamata di funzione nel linguaggio di query di Analisi di flusso. Tuttavia, queste chiamate di funzione sono in realtà Azure Machine Learning richieste di servizio Web.

È possibile migliorare la velocità effettiva delle richieste del servizio Web di Machine Learning "invio in batch" di più righe nella stessa chiamata API del servizio Web. Questo raggruppamento è denominato mini-batch. Per ulteriori informazioni, vedere [Azure Machine Learning Studio (classiche) servizi Web](../machine-learning/studio/consume-web-services.md). Il supporto per Azure Machine Learning Studio (classico) in analisi di flusso è in anteprima.

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>Configurare un processo di Analisi di flusso con funzioni di Machine Learning

Per configurare la funzione Machine Learning usata dal processo di analisi di flusso, sono disponibili due parametri:

* Dimensioni batch delle chiamate di funzione Machine Learning.
* Il numero di unità di streaming (SUs) di cui è stato eseguito il provisioning per il processo di analisi di flusso.

Per determinare i valori appropriati per SUs, decidere se si vuole ottimizzare la latenza del processo di analisi di flusso o la velocità effettiva di ogni SU. È possibile aggiungere sempre SUs a un processo per aumentare la velocità effettiva di una query di analisi di flusso ben partizionata. Ulteriori SUs aumentano il costo di esecuzione del processo.

Determinare la *tolleranza* di latenza per il processo di analisi di flusso. L'aumento delle dimensioni del batch aumenterà la latenza delle richieste di Azure Machine Learning e la latenza del processo di analisi di flusso.

L'aumento delle dimensioni del batch consente al processo di analisi di flusso di elaborare **più eventi** con lo **stesso numero** di richieste di servizio Web Machine Learning. L'aumento della latenza del servizio Web Machine Learning è in genere sublineare all'aumento delle dimensioni del batch. 

È importante considerare le dimensioni di batch più convenienti per un servizio Web Machine Learning in una determinata situazione. Le dimensioni del batch predefinite per le richieste del servizio Web sono pari a 1000. È possibile modificare questa dimensione predefinita usando l' [API REST di analisi di flusso](https://docs.microsoft.com/previous-versions/azure/mt653706(v=azure.100) "API REST di Analisi di flusso") o il [client di PowerShell per analisi di flusso](stream-analytics-monitor-and-manage-jobs-use-powershell.md).

Dopo aver stabilito le dimensioni del batch, è possibile impostare il numero di unità di streaming (SUs) in base al numero di eventi che la funzione deve elaborare al secondo. Per altre informazioni sulle unità di streaming, vedere [Processi di scalabilità di Analisi di flusso](stream-analytics-scale-jobs.md).

Ogni 6 unità di streaming ottiene 20 connessioni simultanee al servizio Web Machine Learning. Tuttavia, 1 processo SU e 3 processi SU ottengono 20 connessioni simultanee.  

Se l'applicazione genera 200.000 eventi al secondo e la dimensione del batch è 1000, la latenza del servizio Web risultante è 200 ms. Questa frequenza indica che ogni connessione può effettuare cinque richieste al servizio Web Machine Learning al secondo. Con 20 connessioni, il processo di analisi di flusso può elaborare eventi 20.000 in 200 ms e 100.000 eventi in un secondo.

Per elaborare gli eventi 200.000 al secondo, il processo di analisi di flusso necessita di 40 connessioni simultanee, che arrivano a 12 unità di streaming. Il diagramma seguente illustra le richieste dal processo di Analisi di flusso all'endpoint servizio di Web Machine Learning. Ogni 6 unità di streaming sono presenti al massimo 20 connessioni simultanee al servizio Web di Machine Learning.

![Ridimensionare analisi di flusso con funzioni Machine Learning due esempi di processo](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "Ridimensionare analisi di flusso con funzioni Machine Learning due esempi di processo")

In generale, posto che ***B*** sta per dimensioni batch e ***L*** sta per latenza del servizio Web per le dimensioni batch B in millisecondi, la velocità effettiva di un processo di Analisi di flusso con ***N*** unità di streaming è:

![Formula scalabilità di analisi di flusso con funzioni Machine Learning](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Formula per ridimensionare Analisi di flusso con funzioni di Machine Learning")

È anche possibile configurare il numero massimo di chiamate simultanee nel servizio Web Machine Learning. È consigliabile impostare questo parametro sul valore massimo (200 attualmente).

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

Esaminiamo la configurazione necessaria per creare un processo di analisi di flusso, che esegue l'analisi dei sentimenti dei tweet a una velocità di 10.000 Tweet al secondo.

Con 1 unità di streaming, questo processo di analisi di flusso gestisce il traffico? Il processo può essere aggiornato con l'input usando le dimensioni batch predefinite 1000. La latenza predefinita dell'analisi dei sentimenti Machine Learning servizio Web (con dimensioni batch predefinite 1000) crea non più di un secondo di latenza.

La latenza **totale** o end-to-end del processo di Analisi di flusso sarebbe normalmente di pochi secondi. Esaminare ora più da vicino questo processo di Analisi di flusso, *in particolare* le chiamate di funzione di Machine Learning. Con una dimensione di batch di 1000, una velocità effettiva di 10.000 eventi richiede circa 10 richieste al servizio Web. Anche con una unità di streaming, il numero di connessioni simultanee è sufficiente per gestire il traffico di input.

Se la frequenza degli eventi di input aumenta di 100 volte, il processo di Analisi di flusso deve elaborare 1.000.000 di tweet al secondo. Per sostenere l'aumentata richiesta, sono disponibili due opzioni:

1. Aumentare le dimensioni del batch.
2. Partizionare il flusso di input per elaborare gli eventi in parallelo.

Con la prima opzione aumenta la **latenza** del processo.

Con la seconda opzione, sarà necessario eseguire il provisioning di più unità di streaming per ottenere più richieste simultanee di Machine Learning servizio Web. Questo numero maggiore di SUs aumenta il **costo**del processo.

Si osservi il ridimensionamento usando le misure di latenza seguenti per ogni dimensione del batch:

| Latency | Dimensioni dei batch |
| --- | --- |
| 200 ms | 1000-batch di eventi o di seguito |
| 250 ms | 5\.000-batch di eventi |
| 300 ms | 10.000-batch di eventi |
| 500 ms | 25.000-batch di eventi |

1. Utilizzo della prima opzione (**senza effettuare**  il provisioning di altre unità di streaming). È possibile aumentare le dimensioni del batch a **25.000**. L'aumento delle dimensioni del batch in questo modo consentirà al processo di elaborare 1 milione eventi con 20 connessioni simultanee al servizio Web di Machine Learning (con una latenza di 500 ms per chiamata). La latenza aggiuntiva del processo di Analisi di flusso dovuta alle richieste della funzione di valutazione rispetto alle richieste del servizio Web di Machine Learning aumenterebbe da **200 ms** a **500 ms**. Tuttavia, le dimensioni del batch **non possono** essere aumentate in modo infinito perché i servizi web di Machine Learning richiedono che le dimensioni del payload di una richiesta siano pari a 4 MB o inferiori e il timeout delle richieste del servizio web dopo 100 secondi di funzionamento.
1. Se si usa la seconda opzione, le dimensioni batch rimangono pari a 1000. Con una latenza del servizio Web di 200 ms, ogni 20 connessioni simultanee al servizio Web sarebbe possibile elaborare 1000 * 20 * 5 eventi = 100.000 al secondo. Per elaborare 1.000.000 di eventi al secondo, quindi, il processo richiederebbe 60 unità di streaming. Rispetto alla prima opzione, il processo di Analisi di flusso invierebbe più richieste batch al servizio Web, generando così un costo maggiore.

Di seguito è riportata una tabella della velocità effettiva del processo di Analisi di flusso per diverse unità di streaming e dimensioni batch, in numero di eventi al secondo.

| Dimensioni batch (latenza ML) | 500 (200 ms) | 1\.000 (200 ms) | 5\.000 (250 ms) | 10.000 (300 ms) | 25.000 (500 ms) |
| --- | --- | --- | --- | --- | --- |
| **1 unità di archiviazione** |2\.500 |5\.000 |20.000 |30.000 |50.000 |
| **3 unità di archiviazione** |2\.500 |5\.000 |20.000 |30.000 |50.000 |
| **6 unità di archiviazione** |2\.500 |5\.000 |20.000 |30.000 |50.000 |
| **12 unità di archiviazione** |5\.000 |10,000 |40.000 |60.000 |100,000 |
| **18 unità di archiviazione** |7\.500 |15.000 |60.000 |90.000 |150.000 |
| **24 unità di archiviazione** |10,000 |20.000 |80.000 |120.000 |200.000 |
| **…** |… |… |… |… |… |
| **60 unità di archiviazione** |25.000 |50.000 |200.000 |300.000 |500.000 |

A questo punto dovrebbe essere chiaro il funzionamento delle funzioni di Machine Learning in Analisi di flusso. I processi di Analisi di flusso eseguono il pull dei dati dalle origini dati e ogni pull restituisce un batch di eventi al processo di Analisi di flusso per l'elaborazione. Come influisce tale modello pull sulle richieste al servizio Web Machine Learning?

In genere, le dimensioni batch impostate per le funzioni di Machine Learning non sono esattamente divisibili per il numero di eventi restituiti da ogni pull del processo di Analisi di flusso. Quando ciò accade, il servizio Web di Machine Learning viene chiamato con batch parziali. L'uso di batch parziali evita l'overhead della latenza di processi aggiuntiva negli eventi di Unione da pull a pull.

## <a name="new-function-related-monitoring-metrics"></a>Nuove metriche di monitoraggio correlate alle funzioni
Nell'area di monitoraggio di un processo di Analisi di flusso sono state aggiunte altre tre metriche relative alle funzioni. Si tratta di **richieste di funzione**, **eventi** di funzione e **richieste di funzioni non riuscite**, come illustrato nell'immagine seguente.

![Ridimensionare analisi di flusso con le metriche di funzioni Machine Learning](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "Metriche per ridimensionare Analisi di flusso con funzioni di Machine Learning")

Di seguito sono riportate le rispettive definizioni.

**FUNCTION REQUESTS**: numero di richieste di funzione.

**FUNCTION EVENTS**: numero di eventi nelle richieste di funzione.

**FAILED FUNCTION REQUESTS**: numero di richieste di funzione non riuscite.

## <a name="key-takeaways"></a>Risultati principali

Per ridimensionare un processo di analisi di flusso con funzioni Machine Learning, considerare i fattori seguenti:

1. Frequenza degli eventi di input.
2. Latenza tollerata per il processo di analisi di flusso in esecuzione (e di conseguenza le dimensioni del batch delle richieste del servizio Web Machine Learning).
3. Il servizio di analisi di flusso con provisioning e il numero di richieste di servizio Web Machine Learning (i costi aggiuntivi correlati alla funzione).

È stata usata come esempio una query di Analisi di flusso completamente partizionata. Se è necessaria una query più complessa, è possibile usare il [forum di Analisi di flusso di Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics) per ottenere altre informazioni dal team di Analisi di flusso.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su Analisi di flusso, vedere:

* [Introduzione all’uso di Analisi di flusso di Azure](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
