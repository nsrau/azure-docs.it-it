---
title: Ridimensionare le funzioni di Machine Learning in Analisi di flusso di Azure
description: Questo articolo descrive come ridimensionare processi di Analisi di flusso che usano funzioni di Machine Learning configurando unità di partizionamento e di flusso.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/16/2020
ms.openlocfilehash: a0cc70f5bf994e03088511a0d10796746a434bd7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91300310"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-studio-classic-functions"></a>Ridimensionare il processo di Analisi di flusso con funzioni di Azure Machine Learning Studio (versione classica)

> [!TIP]
> Per migliorare le prestazioni e l'affidabilità, è consigliabile usare [funzioni definite dall'utente di Azure Machine Learning](machine-learning-udf.md) anziché le funzioni definite dall'utente di Azure Machine Learning Studio (versione classica).

Questo articolo illustra come ridimensionare in modo efficiente i processi di analisi di flusso di Azure che usano le funzioni di Azure Machine Learning. Per informazioni su come ridimensionare processi di Analisi di flusso in generale, vedere l'articolo relativo al [ridimensionamento dei processi](stream-analytics-scale-jobs.md).

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>Che cos'è una funzione di Azure Machine Learning in Analisi di flusso?

Una funzione di Machine Learning in Analisi di flusso può essere usata come una normale chiamata di funzione nel linguaggio di query di Analisi di flusso. Tuttavia, queste chiamate di funzione sono in realtà richieste del servizio Web Azure Machine Learning.

È possibile migliorare la velocità effettiva delle richieste al servizio Web Machine Learning tramite "invio in batch" di più righe nella stessa chiamata all'API del servizio Web. Questo raggruppamento è denominato mini-batch. Per altre informazioni, vedere [Servizi Web di Azure Machine Learning Studio (versione classica)](../machine-learning/classic/consume-web-services.md). Il supporto per Azure Machine Learning Studio (versione classica) in Analisi di flusso è in anteprima.

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>Configurare un processo di Analisi di flusso con funzioni di Machine Learning

Per configurare la funzione Machine Learning usata dal processo di analisi di flusso, sono disponibili due parametri:

* Dimensioni di batch delle chiamate di funzione di Machine Learning.
* Il numero di unità di streaming (SU) di cui è stato eseguito il provisioning per il processo di Analisi di flusso.

Per determinare i valori appropriati per le unità di streaming, decidere se si vuole ottimizzare la latenza del processo di Analisi di flusso o la velocità effettiva di ogni SU. È sempre possibile aggiungere unità di streaming a un processo per aumentare la velocità effettiva di una query di Analisi di flusso con partizionamento efficiente. Le unità di streaming aumentano il costo di esecuzione del processo.

Determinare la *tolleranza* di latenza per il processo di analisi di flusso. L'aumento delle dimensioni del batch aumenterà la latenza delle richieste di Azure Machine Learning e la latenza del processo di Analisi di flusso.

L'aumento delle dimensioni batch consente al processo di Analisi di flusso di elaborare **più eventi** con lo **stesso numero** di richieste del servizio Web di Machine Learning. L'aumento della latenza del servizio Web di Machine Learning è in genere sublineare all'aumento delle dimensioni del batch. 

È importante considerare le dimensioni del batch più convenienti per un servizio Web di Machine Learning in una determinata situazione. Le dimensioni del batch predefinite per le richieste del servizio Web sono pari a 1000. È possibile modificare questa dimensione predefinita usando l'[API REST di Analisi di flusso](https://docs.microsoft.com/previous-versions/azure/mt653706(v=azure.100) "API REST di Analisi di flusso") o il [client di PowerShell per Analisi di flusso](stream-analytics-monitor-and-manage-jobs-use-powershell.md).

Dopo aver stabilito le dimensioni del batch, è possibile impostare il numero di unità di streaming (SU) in base al numero di eventi che la funzione deve elaborare al secondo. Per altre informazioni sulle unità di streaming, vedere [Processi di scalabilità di Analisi di flusso](stream-analytics-scale-jobs.md).

Ogni 6 unità di streaming si ottengono 20 connessioni simultanee al servizio Web di Machine Learning. Tuttavia, 1 processo di SU e 3 processi di SU ottengono 20 connessioni simultanee.  

Se l'applicazione genera 200.000 eventi al secondo e la dimensione del batch è 1000, la latenza del servizio Web risultante è 200 ms. Questa velocità significa che ogni connessione può inviare cinque richieste al servizio Web di Machine Learning in ciascun secondo. Con 20 connessioni, il processo di Analisi di flusso può elaborare 20.000 eventi in 200 ms e 100.000 eventi al secondo.

Per elaborare 200.000 eventi al secondo, il processo di Analisi di flusso necessita di 40 connessioni simultanee, pari a 12 unità di streaming. Il diagramma seguente illustra le richieste dal processo di Analisi di flusso all'endpoint servizio di Web Machine Learning. Ogni 6 unità di streaming sono presenti al massimo 20 connessioni simultanee al servizio Web di Machine Learning.

![Due esempi di processo per ridimensionare Analisi di flusso con funzioni di Machine Learning](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "Due esempi di processo per ridimensionare Analisi di flusso con funzioni di Machine Learning")

In generale, posto che ***B*** sta per dimensioni batch e ***L*** sta per latenza del servizio Web per le dimensioni batch B in millisecondi, la velocità effettiva di un processo di Analisi di flusso con ***N*** unità di streaming è:

![Formula per ridimensionare Analisi di flusso con funzioni di Machine Learning](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Formula per ridimensionare Analisi di flusso con funzioni di Machine Learning")

È anche possibile configurare il numero massimo di chiamate simultanee nel servizio Web di Machine Learning. È consigliabile impostare questo parametro sul valore massimo (200 attualmente).

Per altre informazioni su questa impostazione, vedere l'articolo [Ridimensionamento di un servizio Web di Azure Machine Learning](../machine-learning/classic/create-endpoint.md).

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

Esaminiamo la configurazione necessaria per creare un processo di Analisi di flusso, che esegue l'analisi della valutazione dei tweet a una velocità di 10.000 tweet al secondo.

Usando 1 unità di streaming, questo processo di Analisi di flusso può gestire il traffico? Mantenendo le dimensioni batch predefinite, pari a 1000, il processo deve riuscire a gestire l'input. La latenza predefinita del servizio Web di Machine Learning di analisi di valutazione (con dimensioni batch predefinite pari a 1000) crea non più di un secondo di latenza.

La latenza **totale** o end-to-end del processo di Analisi di flusso sarebbe normalmente di pochi secondi. Esaminare ora più da vicino questo processo di Analisi di flusso, *in particolare* le chiamate di funzione di Machine Learning. Con una dimensione batch pari a 1000, una velocità effettiva di 10.000 eventi richiede circa 10 richieste al servizio Web. Anche con una unità di streaming, il numero di connessioni simultanee è sufficiente per gestire il traffico di input.

Se la frequenza degli eventi di input aumenta di 100 volte, il processo di Analisi di flusso deve elaborare 1.000.000 di tweet al secondo. Per sostenere l'aumentata richiesta, sono disponibili due opzioni:

1. Aumentare le dimensioni batch.
2. Partizionare il flusso di input per elaborare gli eventi in parallelo.

Con la prima opzione aumenta la **latenza** del processo.

Con la seconda opzione, sarà necessario eseguire il provisioning di più unità di streaming per ottenere più richieste simultanee al servizio Web di Machine Learning. Questo numero maggiore di unità di streaming aumenta il **costo** del processo.

Esaminiamo il ridimensionamento usando le misure di latenza seguenti per ogni dimensione batch:

| Latenza | Dimensioni dei batch |
| --- | --- |
| 200 ms | Batch da 1000 eventi o meno |
| 250 ms | Batch da 5.000 eventi |
| 300 ms | Batch da 10.000 eventi |
| 500 ms | Batch da 25.000 eventi |

1. Utilizzo della prima opzione (**senza effettuare**  il provisioning di altre unità di streaming). È possibile aumentare le dimensioni del batch a **25.000**. Aumentare le dimensioni del batch in questo modo consentirà al processo di elaborare 1.000.000 di eventi con 20 connessioni simultanee al servizio Web di Machine Learning, con una latenza di 500 ms per ogni chiamata. La latenza aggiuntiva del processo di Analisi di flusso dovuta alle richieste della funzione di valutazione rispetto alle richieste del servizio Web di Machine Learning aumenterebbe da **200 ms** a **500 ms**. Tuttavia **non è possibile** aumentare le dimensioni batch all'infinito, perché i servizi Web di Machine Learning richiedono dimensioni del payload della richiesta pari a 4 MB. Le richieste di servizio Web di dimensioni inferiori scadono dopo 100 secondi di attività.
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

In genere, le dimensioni batch impostate per le funzioni di Machine Learning non sono esattamente divisibili per il numero di eventi restituiti da ogni pull del processo di Analisi di flusso. Quando ciò accade, il servizio Web di Machine Learning viene chiamato con batch parziali. L'uso di batch parziali evita il sovraccarico della latenza dei processi nell'unione di eventi tra un pull e l'altro.

## <a name="new-function-related-monitoring-metrics"></a>Nuove metriche di monitoraggio correlate alle funzioni
Nell'area di monitoraggio di un processo di Analisi di flusso sono state aggiunte altre tre metriche relative alle funzioni. Le metriche sono **FUNCTION REQUESTS**, **FUNCTION EVENTS** e **FAILED FUNCTION REQUESTS** e sono illustrate nella figura seguente.

![Metriche per ridimensionare Analisi di flusso con funzioni di Machine Learning](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "Metriche per ridimensionare Analisi di flusso con funzioni di Machine Learning")

Di seguito sono riportate le rispettive definizioni.

**FUNCTION REQUESTS**: numero di richieste di funzione.

**FUNCTION EVENTS**: numero di eventi nelle richieste di funzione.

**FAILED FUNCTION REQUESTS**: numero di richieste di funzione non riuscite.

## <a name="key-takeaways"></a>Risultati principali

Per ridimensionare un processo di Analisi di flusso con funzioni di Machine Learning, considerare i fattori seguenti:

1. Frequenza degli eventi di input.
2. Latenza consentita per il processo di Analisi di flusso in esecuzione e dimensioni batch delle richieste al servizio Web Machine Learning.
3. Unità di streaming di Analisi di flusso di cui è stato eseguito il provisioning e numero di richieste al servizio Web Machine Learning, oltre ai costi aggiuntivi correlati alle funzioni.

È stata usata come esempio una query di Analisi di flusso completamente partizionata. Se è necessaria una query più complessa, è possibile usare la [Pagina delle domande di Domande e risposte Microsoft per Analisi di flusso di Azure](https://docs.microsoft.com/answers/topics/azure-stream-analytics.html) per ottenere altre informazioni dal team di Analisi di flusso.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su Analisi di flusso, vedere:

* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
