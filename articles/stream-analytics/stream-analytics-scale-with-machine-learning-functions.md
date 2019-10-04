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
ms.openlocfilehash: 3d478c2421066c8347622f9064c479bb8255b112
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621739"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-studio-functions"></a>Ridimensionare il processo di Stream Analitica con funzioni di Azure Machine Learning Studio

Questo articolo illustra come ridimensionare in modo efficiente i processi di Analitica Stream di Azure che usano funzioni di Azure Machine Learning. Per informazioni su come ridimensionare processi di Analisi di flusso in generale, vedere l'articolo relativo al [ridimensionamento dei processi](stream-analytics-scale-jobs.md).

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>Che cos'è una funzione di Azure Machine Learning in Analisi di flusso?

Una funzione di Machine Learning in Analisi di flusso può essere usata come una normale chiamata di funzione nel linguaggio di query di Analisi di flusso. Dietro le quinte, tuttavia, queste chiamate di funzione sono effettivamente le richieste di servizio Web di Azure Machine Learning.

È possibile migliorare la velocità effettiva delle richieste di servizio web di Machine Learning "raggruppando" più righe contemporaneamente nella stessa chiamata API al servizio web. Questo raggruppamento viene chiamato un mini-batch. Per altre informazioni, vedere [servizi Web di Azure Machine Learning Studio](../machine-learning/studio/consume-web-services.md). Supporto per Azure Machine Learning Studio in Stream Analitica è in anteprima.

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>Configurare un processo di Analisi di flusso con funzioni di Machine Learning

Esistono due parametri per configurare la funzione di Machine Learning usata dal processo Analitica Stream:

* Dimensioni batch delle chiamate di funzione di Machine Learning.
* Il numero di unità di Streaming (SUs) il provisioning per il processo di Stream Analitica.

Per determinare i valori appropriati per unità di streaming, decidere se si desidera ottimizzare la latenza del processo di Stream Analitica o la velocità effettiva di ogni unità di streaming. È sempre possibile aggiungere unità di streaming a un processo per aumentare la velocità effettiva di una query Analitica Stream ben partizionata. Unità di streaming aumentano il costo di esecuzione del processo.

Determinare la latenza *tolleranza* per il processo di Stream Analitica. Aumentando le dimensioni del batch aumenterà la latenza delle richieste di servizio di Azure Machine Learning e la latenza del processo di Stream Analitica.

Aumento delle dimensioni batch consente al processo di Stream Analitica di elaborare **altri eventi** con il **stesso numero** di Machine Learning richieste al servizio web. L'aumento della latenza del servizio web Machine Learning è in genere di sublineare all'aumento delle dimensioni del batch. 

È importante prendere in considerazione le dimensioni batch più convenienti per un servizio web di Machine Learning in una determinata situazione. Le dimensioni batch predefinite per le richieste di servizio web sono 1000. È possibile modificare questa dimensione predefinita utilizzando il [API REST di Stream Analitica](https://docs.microsoft.com/previous-versions/azure/mt653706(v=azure.100) "Stream Analitica REST API") o la [client di PowerShell per Stream Analitica](stream-analytics-monitor-and-manage-jobs-use-powershell.md).

Dopo aver deciso per una dimensione del batch, è possibile impostare il numero di unità di streaming (SUs), in base al numero di eventi che la funzione deve elaborare al secondo. Per altre informazioni sulle unità di streaming, vedere [Processi di scalabilità di Analisi di flusso](stream-analytics-scale-jobs.md).

Ogni 6 unità di streaming ricevono 20 connessioni simultanee al servizio web Machine Learning. Tuttavia, processo di 1 unità di streaming e per 3 unità di streaming ricevono 20 connessioni simultanee.  

Se l'applicazione genera 200.000 eventi al secondo e la dimensione del batch è 1000, la latenza del servizio web risultante è 200 ms. Questa velocità significa che ogni connessione può inviare cinque richieste al servizio web Machine Learning ogni secondo. Con 20 connessioni, il processo di Stream Analitica può elaborare 20.000 eventi in 200 ms, ovvero 100.000 eventi in un secondo.

Per elaborare 200.000 eventi al secondo, il processo di Stream Analitica necessita di 40 connessioni simultanee, pari a 12 unità di streaming. Il diagramma seguente illustra le richieste dal processo di Analisi di flusso all'endpoint servizio di Web Machine Learning. Ogni 6 unità di streaming sono presenti al massimo 20 connessioni simultanee al servizio Web di Machine Learning.

![Esempio di processo per ridimensionare Analisi di flusso con funzioni di Machine Learning due](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "Esempio di processo per ridimensionare Analisi di flusso con funzioni di Machine Learning due")

In generale, posto che ***B*** sta per dimensioni batch e ***L*** sta per latenza del servizio Web per le dimensioni batch B in millisecondi, la velocità effettiva di un processo di Analisi di flusso con ***N*** unità di streaming è:

![Formula per ridimensionare Analisi di flusso con funzioni di Machine Learning](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Formula per ridimensionare Analisi di flusso con funzioni di Machine Learning")

È anche possibile configurare il "numero massimo di chiamate simultanee' nel servizio web Machine Learning. È consigliabile impostare questo parametro sul valore massimo (attualmente è 200).

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

È possibile esaminare la configurazione necessaria creare un processo di Stream Analitica, che esegue l'analisi del sentiment di tweet con una frequenza di 10.000 TWEET al secondo.

Con 1 unità di streaming, è stato possibile questo processo di Stream Analitica gestire il traffico? Il processo di grado di gestirla con l'input usando le dimensioni batch predefinite pari a 1000. La latenza predefinita del servizio web Machine Learning di analisi del sentiment (con dimensioni batch predefinite pari a 1000) consente di creare non più di un secondo di latenza.

La latenza **totale** o end-to-end del processo di Analisi di flusso sarebbe normalmente di pochi secondi. Esaminare ora più da vicino questo processo di Analisi di flusso, *in particolare* le chiamate di funzione di Machine Learning. Con una dimensione di batch di 1000, una velocità effettiva di 10.000 eventi richiede circa 10 richieste al servizio web. Anche con una unità di streaming, il numero di connessioni simultanee è sufficiente per gestire il traffico di input.

Se la frequenza degli eventi di input aumenta di 100 volte, il processo di Analisi di flusso deve elaborare 1.000.000 di tweet al secondo. Per sostenere l'aumentata richiesta, sono disponibili due opzioni:

1. Aumentare le dimensioni del batch.
2. Partizionare il flusso di input per elaborare gli eventi in parallelo.

Con la prima opzione aumenta la **latenza** del processo.

Con la seconda opzione, è necessario effettuare il provisioning di altre unità di streaming affinché simultanea di più richieste di servizio web di Machine Learning. Questo numero maggiore di unità di streaming, aumenta il processo **costo**.

Si osservi il ridimensionamento delle misurazioni di latenza seguenti usando per ogni dimensione di batch:

| Latenza | Dimensioni dei batch |
| --- | --- |
| 200 ms | batch di 1000 eventi o versione precedente |
| 250 ms | batch di 5.000 eventi |
| 300 ms | batch di 10.000 eventi |
| 500 ms | batch di 25.000 eventi |

1. Utilizzo della prima opzione (**senza effettuare**  il provisioning di altre unità di streaming). È possibile aumentare le dimensioni del batch a **25.000**. Aumentando le dimensioni del batch in questo modo si consentirà al processo di elaborare 1.000.000 di eventi con 20 connessioni simultanee al servizio web Machine Learning (con una latenza di 500 ms per ogni chiamata). La latenza aggiuntiva del processo di Analisi di flusso dovuta alle richieste della funzione di valutazione rispetto alle richieste del servizio Web di Machine Learning aumenterebbe da **200 ms** a **500 ms**. Dimensioni batch, tuttavia **non è possibile** aumentate all'infinito, perché i servizi web Machine Learning richiedono le dimensioni del payload della richiesta pari a 4 MB o più piccoli e web timeout delle richieste di servizio dopo 100 secondi di attività.
1. Se si usa la seconda opzione, le dimensioni batch rimangono pari a 1000. Con una latenza del servizio Web di 200 ms, ogni 20 connessioni simultanee al servizio Web sarebbe possibile elaborare 1000 * 20 * 5 eventi = 100.000 al secondo. Per elaborare 1.000.000 di eventi al secondo, quindi, il processo richiederebbe 60 unità di streaming. Rispetto alla prima opzione, il processo di Analisi di flusso invierebbe più richieste batch al servizio Web, generando così un costo maggiore.

Di seguito è riportata una tabella della velocità effettiva del processo di Analisi di flusso per diverse unità di streaming e dimensioni batch, in numero di eventi al secondo.

| Dimensioni batch (latenza ML) | 500 (200 ms) | 1\.000 (200 ms) | 5\.000 (250 ms) | 10.000 (300 ms) | 25.000 (500 ms) |
| --- | --- | --- | --- | --- | --- |
| **1 unità di archiviazione** |2\.500 |5\.000 |20.000 |30.000 |50,000 |
| **3 unità di archiviazione** |2\.500 |5\.000 |20.000 |30.000 |50,000 |
| **6 unità di archiviazione** |2\.500 |5\.000 |20.000 |30.000 |50,000 |
| **12 unità di archiviazione** |5\.000 |10,000 |40.000 |60.000 |100,000 |
| **18 unità di archiviazione** |7\.500 |15.000 |60.000 |90.000 |150.000 |
| **24 unità di archiviazione** |10,000 |20.000 |80.000 |120.000 |200.000 |
| **…** |… |… |… |… |… |
| **60 unità di archiviazione** |25.000 |50,000 |200,000 |300.000 |500,000 |

A questo punto dovrebbe essere chiaro il funzionamento delle funzioni di Machine Learning in Analisi di flusso. I processi di Analisi di flusso eseguono il pull dei dati dalle origini dati e ogni pull restituisce un batch di eventi al processo di Analisi di flusso per l'elaborazione. Come influisce tale modello pull sulle richieste al servizio Web Machine Learning?

In genere, le dimensioni batch impostate per le funzioni di Machine Learning non sono esattamente divisibili per il numero di eventi restituiti da ogni pull del processo di Analisi di flusso. Quando ciò accade, il servizio Web di Machine Learning viene chiamato con batch parziali. Utilizzo dei batch parziale è possibile evitare un sovraccarico di latenza processo aggiuntive nell'unione di eventi da pull e l'altro.

## <a name="new-function-related-monitoring-metrics"></a>Nuove metriche di monitoraggio correlate alle funzioni
Nell'area di monitoraggio di un processo di Analisi di flusso sono state aggiunte altre tre metriche relative alle funzioni. Si trovano **FUNCTION REQUESTS**, **FUNCTION EVENTS** e **FAILED FUNCTION REQUESTS**, come illustrato nella figura seguente.

![Metriche per ridimensionare Analisi di flusso con funzioni di Machine Learning](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "Metriche per ridimensionare Analisi di flusso con funzioni di Machine Learning")

Di seguito sono riportate le rispettive definizioni.

**FUNCTION REQUESTS**: numero di richieste di funzione.

**FUNCTION EVENTS**: numero di eventi nelle richieste di funzione.

**FAILED FUNCTION REQUESTS**: numero di richieste di funzione non riuscite.

## <a name="key-takeaways"></a>Risultati principali

Per ridimensionare un processo di Stream Analitica con funzioni di Machine Learning, considerare quanto segue:

1. La frequenza degli eventi di input.
2. Latenza consentita per il processo di Stream Analitica in esecuzione (e pertanto le dimensioni del batch di richieste di servizio web di Machine Learning).
3. Le unità di streaming di Analitica Stream con provisioning e il numero di richieste di servizio web di Machine Learning (correlate alla funzione costi aggiuntivi).

È stata usata come esempio una query di Analisi di flusso completamente partizionata. Se è necessaria una query più complessa, è possibile usare il [forum di Analisi di flusso di Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics) per ottenere altre informazioni dal team di Analisi di flusso.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su Analisi di flusso, vedere:

* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
