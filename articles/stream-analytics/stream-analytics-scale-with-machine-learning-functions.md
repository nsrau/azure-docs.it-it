---
title: Ridimensionare le funzioni Machine Learning Studio (classiche) in analisi di flusso di Azure
description: Questo articolo descrive come ridimensionare i processi di analisi di flusso che usano funzioni Machine Learning Studio (classiche), configurando il partizionamento e le unità di streaming.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/16/2020
ms.openlocfilehash: feeb709f67a0e75f5980ec0520b95feb7edd5960
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93124408"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-studio-classic-functions"></a>Ridimensionare il processo di Analisi di flusso con funzioni di Azure Machine Learning Studio (versione classica)

> [!TIP]
> Per migliorare le prestazioni e l'affidabilità, è consigliabile usare [funzioni definite dall'utente di Azure Machine Learning](machine-learning-udf.md) anziché le funzioni definite dall'utente di Azure Machine Learning Studio (versione classica).

Questo articolo illustra come ridimensionare in modo efficiente i processi di analisi di flusso di Azure che usano funzioni Azure Machine Learning Studio (classiche). Per informazioni su come ridimensionare processi di Analisi di flusso in generale, vedere l'articolo relativo al [ridimensionamento dei processi](stream-analytics-scale-jobs.md).

## <a name="what-is-a-studio-classic-function-in-stream-analytics"></a>Che cos'è una funzione Studio (classica) in analisi di flusso?

Una funzione Machine Learning Studio (classica) in analisi di flusso può essere usata come una normale chiamata di funzione nel linguaggio di query di analisi di flusso. Dietro le quinte, tuttavia, queste chiamate di funzione sono in realtà richieste di servizi Web in studio (classiche).

È possibile migliorare la velocità effettiva delle richieste di servizi Web di studio (classiche) per la "suddivisione in batch" di più righe nella stessa chiamata API del servizio Web. Questo raggruppamento è denominato mini-batch. Per altre informazioni, vedere [Servizi Web di Azure Machine Learning Studio (versione classica)](../machine-learning/classic/consume-web-services.md). Il supporto per studio (classico) in analisi di flusso è in anteprima.

## <a name="configure-a-stream-analytics-job-with-studio-classic-functions"></a>Configurare un processo di analisi di flusso con funzioni Studio (classiche)

Sono disponibili due parametri per configurare la funzione Studio (classica) usata dal processo di analisi di flusso:

* Dimensioni batch delle chiamate di funzione di studio (classiche).
* Il numero di unità di streaming (SU) di cui è stato eseguito il provisioning per il processo di Analisi di flusso.

Per determinare i valori appropriati per le unità di streaming, decidere se si vuole ottimizzare la latenza del processo di Analisi di flusso o la velocità effettiva di ogni SU. È sempre possibile aggiungere unità di streaming a un processo per aumentare la velocità effettiva di una query di Analisi di flusso con partizionamento efficiente. Le unità di streaming aumentano il costo di esecuzione del processo.

Determinare la *tolleranza* di latenza per il processo di analisi di flusso. L'aumento delle dimensioni del batch aumenterà la latenza delle richieste di studio (classiche) e la latenza del processo di analisi di flusso.

L'aumento delle dimensioni del batch consente al processo di analisi di flusso di elaborare **più eventi** con lo **stesso numero** di richieste di servizi Web Studio (classiche). L'aumento della latenza del servizio Web Studio (classico) è in genere sublineare all'aumento delle dimensioni del batch. 

È importante considerare le dimensioni di batch più convenienti per un servizio Web di studio (classico) in una determinata situazione. Le dimensioni del batch predefinite per le richieste del servizio Web sono pari a 1000. È possibile modificare questa dimensione predefinita usando l'[API REST di Analisi di flusso](/previous-versions/azure/mt653706(v=azure.100) "API REST di Analisi di flusso") o il [client di PowerShell per Analisi di flusso](stream-analytics-monitor-and-manage-jobs-use-powershell.md).

Dopo aver stabilito le dimensioni del batch, è possibile impostare il numero di unità di streaming (SU) in base al numero di eventi che la funzione deve elaborare al secondo. Per altre informazioni sulle unità di streaming, vedere [Processi di scalabilità di Analisi di flusso](stream-analytics-scale-jobs.md).

Ogni 6 unità di streaming ottengono 20 connessioni simultanee al servizio Web Studio (classico). Tuttavia, 1 processo di SU e 3 processi di SU ottengono 20 connessioni simultanee.  

Se l'applicazione genera 200.000 eventi al secondo e la dimensione del batch è 1000, la latenza del servizio Web risultante è 200 ms. Questa frequenza indica che ogni connessione può effettuare cinque richieste al servizio Web Studio (classico) ogni secondo. Con 20 connessioni, il processo di Analisi di flusso può elaborare 20.000 eventi in 200 ms e 100.000 eventi al secondo.

Per elaborare 200.000 eventi al secondo, il processo di Analisi di flusso necessita di 40 connessioni simultanee, pari a 12 unità di streaming. Il diagramma seguente illustra le richieste dal processo di analisi di flusso all'endpoint del servizio Web Studio (classico). ogni 6 unità di streaming dispone di 20 connessioni simultanee al servizio Web Studio (classico) al massimo.

![Ridimensionare l'analisi di flusso con funzioni di studio (classiche) a due processi](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "Ridimensionare l'analisi di flusso con funzioni di studio (classiche) a due processi")

In generale, * *_B_* _ per le dimensioni del batch, _*_L_*_ per la latenza del servizio Web in batch di dimensioni B in millisecondi, la velocità effettiva di un processo di analisi di flusso con _*_N_*_ unità di streaming è:

![Formula per ridimensionare analisi di flusso con funzioni di studio (classiche)](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Formula per ridimensionare analisi di flusso con funzioni di studio (classiche)")

È anche possibile configurare il numero massimo di chiamate simultanee nel servizio Web Studio (classico). È consigliabile impostare questo parametro sul valore massimo (200 attualmente).

Per ulteriori informazioni su questa impostazione, vedere l' [articolo relativo alla scalabilità per i servizi Web di Machine Learning Studio (classico)](../machine-learning/classic/create-endpoint.md).

## <a name="example--sentiment-analysis"></a>Esempio: Analisi di valutazione
L'esempio seguente include un processo di analisi di flusso con la funzione valutazione di Analysis Studio (classica), come descritto nell'esercitazione sull'integrazione di analisi di [flusso Machine Learning Studio (classica)](stream-analytics-machine-learning-integration-tutorial.md).

La query è una semplice query completamente partizionata seguita dalla funzione _ *sentimento* *, come illustrato nell'esempio seguente:

```SQL
    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery
```

Esaminiamo la configurazione necessaria per creare un processo di Analisi di flusso, che esegue l'analisi della valutazione dei tweet a una velocità di 10.000 tweet al secondo.

Usando 1 unità di streaming, questo processo di Analisi di flusso può gestire il traffico? Mantenendo le dimensioni batch predefinite, pari a 1000, il processo deve riuscire a gestire l'input. La latenza predefinita del servizio Web di valutazione di Analysis Studio (classico) (con dimensioni batch predefinite 1000) non crea più di un secondo di latenza.

La latenza **totale** o end-to-end del processo di Analisi di flusso sarebbe normalmente di pochi secondi. Esaminare in modo più dettagliato questo processo di analisi di flusso, in *particolare* le chiamate di funzione di studio (classiche). Con una dimensione batch pari a 1000, una velocità effettiva di 10.000 eventi richiede circa 10 richieste al servizio Web. Anche con una unità di streaming, il numero di connessioni simultanee è sufficiente per gestire il traffico di input.

Se la frequenza degli eventi di input aumenta di 100 volte, il processo di Analisi di flusso deve elaborare 1.000.000 di tweet al secondo. Per sostenere l'aumentata richiesta, sono disponibili due opzioni:

1. Aumentare le dimensioni batch.
2. Partizionare il flusso di input per elaborare gli eventi in parallelo.

Con la prima opzione aumenta la **latenza** del processo.

Con la seconda opzione, è necessario eseguire il provisioning di più unità di streaming per ottenere più richieste di servizio Web simultanee in studio (classiche). Questo numero maggiore di unità di streaming aumenta il **costo** del processo.

Esaminiamo il ridimensionamento usando le misure di latenza seguenti per ogni dimensione batch:

| Latenza | Dimensioni dei batch |
| --- | --- |
| 200 ms | Batch da 1000 eventi o meno |
| 250 ms | Batch da 5.000 eventi |
| 300 ms | Batch da 10.000 eventi |
| 500 ms | Batch da 25.000 eventi |

1. Utilizzo della prima opzione ( **senza effettuare**  il provisioning di altre unità di streaming). È possibile aumentare le dimensioni del batch a **25.000** . L'aumento delle dimensioni del batch in questo modo consentirà al processo di elaborare 1 milione eventi con 20 connessioni simultanee al servizio Web Studio (classico) (con una latenza di 500 ms per chiamata). Quindi, la latenza aggiuntiva del processo di analisi di flusso dovuta alle richieste della funzione di valutazione rispetto alle richieste del servizio Web Studio (classica) viene aumentata da **200 MS** a **500 ms** . Tuttavia, le dimensioni del batch **non possono** essere aumentate in modo infinito perché i servizi Web di studio (classico) richiedono che le dimensioni del payload di una richiesta siano pari a 4 MB o inferiori e il timeout delle richieste del servizio web dopo 100 secondi di operatività.
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

A questo punto, è necessario avere già una conoscenza approfondita del funzionamento delle funzioni di studio (classiche) in analisi di flusso. I processi di Analisi di flusso eseguono il pull dei dati dalle origini dati e ogni pull restituisce un batch di eventi al processo di Analisi di flusso per l'elaborazione. In che modo questo modello pull influisca sulle richieste del servizio Web di studio (classiche)?

Normalmente, le dimensioni del batch impostate per le funzioni Studio (classiche) non saranno esattamente divisibile per il numero di eventi restituiti da ogni processo di analisi di flusso "pull". In questo caso, il servizio Web Studio (classico) viene chiamato con batch parziali. L'uso di batch parziali evita il sovraccarico della latenza dei processi nell'unione di eventi tra un pull e l'altro.

## <a name="new-function-related-monitoring-metrics"></a>Nuove metriche di monitoraggio correlate alle funzioni
Nell'area di monitoraggio di un processo di Analisi di flusso sono state aggiunte altre tre metriche relative alle funzioni. Le metriche sono **FUNCTION REQUESTS** , **FUNCTION EVENTS** e **FAILED FUNCTION REQUESTS** e sono illustrate nella figura seguente.

![Ridimensionare analisi di flusso con le metriche delle funzioni di studio (classiche)](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "Ridimensionare analisi di flusso con le metriche delle funzioni di studio (classiche)")

Di seguito sono riportate le rispettive definizioni.

**FUNCTION REQUESTS** : numero di richieste di funzione.

**FUNCTION EVENTS** : numero di eventi nelle richieste di funzione.

**FAILED FUNCTION REQUESTS** : numero di richieste di funzione non riuscite.

## <a name="key-takeaways"></a>Risultati principali

Per ridimensionare un processo di analisi di flusso con funzioni Studio (classiche), considerare i fattori seguenti:

1. Frequenza degli eventi di input.
2. Latenza tollerata per il processo di analisi di flusso in esecuzione (e quindi le dimensioni del batch delle richieste del servizio Web di studio (classico)).
3. Il servizio di analisi di flusso di cui è stato effettuato il provisioning e il numero di richieste di servizi Web Studio (classiche), ovvero i costi aggiuntivi relativi alla funzione.

È stata usata come esempio una query di Analisi di flusso completamente partizionata. Se è necessaria una query più complessa, è possibile usare la [Pagina delle domande di Domande e risposte Microsoft per Analisi di flusso di Azure](/answers/topics/azure-stream-analytics.html) per ottenere altre informazioni dal team di Analisi di flusso.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su Analisi di flusso, vedere:

* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](/stream-analytics-query/stream-analytics-query-language-reference)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](/rest/api/streamanalytics/)