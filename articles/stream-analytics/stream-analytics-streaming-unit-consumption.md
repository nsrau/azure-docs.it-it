---
title: "Analisi di flusso di Azure: informazioni sulle unità di streaming e su come modificarle | Microsoft Docs"
description: Informazioni sui fattori che hanno impatto sulle prestazioni in Analisi di flusso di Azure.
keywords: "unità di streaming, prestazioni delle query"
services: stream-analytics
documentationcenter: 
author: JSeb225
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeanb
ms.openlocfilehash: e8812f10662ee7b571e8e353074c2537d1a3181b
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
# <a name="understand-and-adjust-streaming-units"></a>Informazioni sulle unità di flusso e su come modificarle

Analisi di flusso di Azure aggrega il "peso" delle prestazioni dell'esecuzione di un processo in unità di streaming. Le unità di streaming rappresentano le risorse di elaborazione che vengono usate per eseguire un processo. Le unità di streaming descrivono la capacità relativa di elaborazione di eventi in base a una misurazione combinata di CPU, memoria e frequenze di lettura e scrittura. Questa capacità consente di concentrarsi sulla logica della query, senza dover conoscere il funzionamento delle prestazioni del livello di archiviazione, allocare memoria per il processo manualmente e approssimare il numero di memorie centrali di CPU necessario per eseguire il processo in modo tempestivo.

Per ottenere l'elaborazione di flussi a bassa latenza, i processi di Analisi di flusso di Azure eseguono tutta l'elaborazione in memoria. Quando la memoria viene esaurita, il processo di streaming non riesce. Di conseguenza, per un processo di produzione è importante monitorare l'utilizzo delle risorse di un processo di streaming e assicurarsi che siano state allocate risorse sufficienti per mantenere il processo in esecuzione 24 ore su 24, 7 giorni su 7.

La metrica è un numero in percentuale compreso tra 0% e 100%. Per un processo di streaming con footprint minimo, la metrica di utilizzo in percentuale delle unità di streaming è in genere compresa tra 10% e 20%. È consigliabile mantenere la metrica al di sotto dell'80% in modo da tenere conto dei picchi occasionali.  È possibile impostare un avviso per la metrica. Vedere [qui per configurare avvisi delle metriche](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-alerts-portal).



## <a name="configure-stream-analytics-streaming-units-sus"></a>Configurare le unità di streaming di Analisi di flusso
1. Accedere al [portale di Azure](http://portal.azure.com/).
2. Nell'elenco delle risorse trovare il processo di Analisi di flusso da ridimensionare e aprirlo. 
3. Nella pagina del processo fare clic su **Ridimensiona** in Configura. 

    ![Configurazione del processo di Analisi di flusso nel portale di Azure][img.stream.analytics.preview.portal.settings.scale]
    
4. Usare il dispositivo di scorrimento per impostare le unità di streaming per il processo. Si noti che è possibile definire solo impostazioni specifiche delle unità di streaming. 


## <a name="monitor-job-performance"></a>Monitorare le prestazioni del processo
Nel portale di Azure è possibile rilevare la velocità effettiva di un processo:

![Processi di monitoraggio di Analisi dei flussi di Azure][img.stream.analytics.monitor.job]

Calcolare la velocità effettiva prevista del carico di lavoro. Se la velocità effettiva è inferiore al previsto, ottimizzare la partizione di input e la query, quindi aggiungere unità di streaming al processo.


## <a name="how-many-sus-are-required-for-a-job"></a>Quante unità di streaming sono necessarie per un processo?

Il numero di unità di streaming necessarie per un particolare processo dipende dalla configurazione della partizione per gli input e dalla query definita nel processo. La pagina **Ridimensiona** consente di impostare il numero corretto di unità di streaming. È consigliabile allocare più unità di streaming di quelle necessarie. Il motore di elaborazione di Analisi di flusso è ottimizzato per la latenza e la velocità effettiva al costo di allocazione di memoria aggiuntiva.

In generale la procedura consigliata consiste nell'iniziare con 6 unità di streaming per le query che non usano **PARTITION BY**. Determinare quindi il punto critico usando un metodo di valutazione e correzione degli errori con cui modificare il numero di unità di streaming dopo aver passato quantità rappresentative di dati e aver esaminato la metrica di utilizzo in percentuale delle unità di streaming.

Per altre informazioni sulla scelta del numero corretto di unità di streaming, vedere questa pagina: [Ridimensionare i processi di Analisi di flusso di Azure per aumentare la velocità effettiva dell'elaborazione dei flussi di dati](stream-analytics-scale-jobs.md)

> [!Note]
> Il numero di unità di streaming necessarie per un particolare processo dipende dalla configurazione delle partizioni per gli input e dalla query definita per il processo. È prevista una quota massima di unità di streaming che è possibile selezionare per un processo. Per impostazione predefinita, ogni sottoscrizione di Azure ha una quota massima di 200 unità di streaming per tutti i processi di analisi in un'area specifica. Per superare la quota massima di unità di streaming per le sottoscrizioni, contattare il [supporto tecnico Microsoft](http://support.microsoft.com). I valori validi di unità di streaming per processo sono 1, 3, 6 e poi a salire, con incrementi di 6.



## <a name="factors-increasing-su-utilization"></a>Fattori che aumentano l'utilizzo in percentuale delle unità di streaming 
### <a name="stateful-query-logic"></a>Logica di query con stato 
Una delle esclusive funzionalità dei processi di Analisi di flusso di Azure è l'esecuzione dell'elaborazione con stato, ad esempio per funzioni di aggregazione finestra, join temporali e funzioni di analisi temporali. Ognuno di questi operatori mantiene alcuni stati. 
#### <a name="windowed-aggregates"></a>Funzioni di aggregazione finestra
Le dimensioni dello stato di una funzione di aggregazione finestra è proporzionale al numero di gruppi (cardinalità) nell'operatore GROUP BY. Ad esempio, nella query seguente il numero associato a clusterid è la cardinalità della query. 

    SELECT count(*)
    FROM input 
    GROUP BY  clusterid, tumblingwindow (minutes, 5)


Per risolvere i problemi causati dalla cardinalità elevata nella query precedente, è possibile inviare eventi partizionati in base a ''clusterid'' a Hub eventi e scalare orizzontalmente la query, consentendo al sistema di elaborare ogni partizione di input separatamente usando **PARTITION BY**, come mostrato nell'esempio seguente:


    SELECT count(*) 
    FROM PARTITION BY PartitionId
    GROUP BY PartitionId, clusterid, tumblingwindow (minutes, 5)

In seguito al partizionamento, la query viene distribuita su più nodi. Di conseguenza, il numero di elementi clusterid in arrivo in ogni nodo diminuisce, riducendo a sua volta la cardinalità dell'operatore GROUP BY. 

Le partizioni di Hub eventi devono essere create in base alla chiave di raggruppamento per evitare la necessità di un passaggio di riduzione. Altri dettagli sono disponibili [qui](https://docs.microsoft.com/azure/event-hubs/event-hubs-overview). 
#### <a name="temporal-join"></a>Join temporale
Le dimensioni dello stato di un join temporale sono proporzionali al numero di eventi all'interno del margine di manovra temporale del join, che corrisponde alla frequenza di input degli eventi moltiplicata per le dimensioni del margine di manovra. 

Il numero di eventi senza corrispondenza nel join influisce sull'utilizzo della memoria per la query. La query seguente cerca le impressioni di annunci che generano clic:

    SELECT id
    FROM clicks 
    INNER JOIN, impressions ON impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10.

In questo esempio è possibile che vengano mostrati molti annunci e che pochi utenti facciano clic sugli annunci. È anche necessario mantenere tutti gli eventi nell'intervallo di tempo. La memoria consumata è proporzionale alle dimensioni della finestra e alla frequenza degli eventi. 

Per risolvere questo problema, inviare gli eventi a Hub eventi partizionandoli in base alle chiavi di join (l'ID in questo caso) e scalare orizzontalmente la query consentendo al sistema di elaborare ogni partizione di input separatamente usando **PARTITION BY**, come mostrato di seguito:

    SELECT id
    FROM clicks PARTITION BY PartitionId
    INNER JOIN impressions PARTITION BY PartitionId 
    ON impression.PartitionId = clocks.PartitionId AND impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10 
</code>

In seguito al partizionamento, la query viene distribuita su più nodi. Di conseguenza, il numero di eventi in arrivo in ogni nodo diminuisce, riducendo a sua volta le dimensioni dello stato mantenuto nell'intervallo di join. 
#### <a name="temporal-analytic-function"></a>Funzione di analisi temporale
Le dimensioni dello stato di una funzione di analisi temporale sono proporzionali alla frequenza degli eventi moltiplicata per la durata. La correzione è simile a quella per il join temporale. È possibile scalare orizzontalmente la query usando **PARTITION BY**. 

### <a name="out-of-order-buffer"></a>Buffer non in ordine 
L'utente può configurare le dimensioni del buffer non in ordine nel riquadro di configurazione Ordinamento eventi. Il buffer viene usato per contenere gli input per la durata dell'intervallo e per riordinarli. Le dimensioni del buffer sono proporzionali alla frequenza di input degli eventi moltiplicata per la dimensione dell'intervallo per l'ordine non corretto. La dimensione predefinita dell'intervallo è 0. 

Per correggere questo problema, ridimensionare la query usando **PARTITION BY**. In seguito al partizionamento, la query viene distribuita su più nodi. Di conseguenza, il numero di eventi in arrivo in ogni nodo diminuisce, riducendo a sua volta il numero di eventi in ogni buffer di riordinamento. 

### <a name="input-partition-count"></a>Conteggio delle partizioni di input 
Ogni partizione di input di un processo ha un buffer. Maggiore è il numero di partizioni di input, maggiore è il numero di risorse utilizzate dal processo. Poiché per ogni unità di streaming Analisi di flusso di Azure può elaborare all'incirca 1 MB/s di input, potrebbe essere necessario che il numero di unità di streaming di Analisi di flusso di Azure corrisponda al numero di partizioni di Hub eventi. In genere, un processo con una unità di streaming è sufficiente per un'istanza di Hub eventi con 2 partizioni (valore minimo per Hub eventi). Se l'istanza di Hub eventi ha più partizioni, il processo di Analisi di flusso utilizza più risorse, ma non usa necessariamente la velocità effettiva aggiuntiva fornita da Hub eventi. Per un processo con 6 unità di streaming, possono essere necessarie 4 o 8 partizioni in Hub eventi. L'uso di Hub eventi con 16 o più partizioni in un processo con una unità di streaming contribuisce spesso a un utilizzo di risorse eccessivo e deve essere evitato. 

### <a name="reference-data"></a>Dati di riferimento 
I dati di riferimento in Analisi di flusso di Azure vengono caricati in memoria per consentire la ricerca rapida. Con l'implementazione corrente ogni operazione di join con dati di riferimento mantiene una copia dei dati di riferimento in memoria, anche se il join viene eseguito con gli stessi dati di riferimento più volte. Per le query con **PARTITION BY**, ogni partizione include una copia dei dati di riferimento, in modo che le partizioni siano completamente separate. Con l'effetto moltiplicatore l'utilizzo della memoria può aumentare rapidamente se si esegue il join con i dati di riferimento più volte con più partizioni.  

#### <a name="use-of-udf-functions"></a>Uso di funzioni definite dall'utente
Quando si aggiunge una funzione definita dall'utente, Analisi di flusso di Azure carica il runtime JavaScript in memoria. Questo comportamento influisce sulla percentuale di unità di streaming.


## <a name="get-help"></a>Ottenere aiuto
Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passaggi successivi
* [Create parallelizable queries in Azure Stream Analytics](stream-analytics-parallelization.md) (Creare query eseguibili in parallelo in Analisi di flusso di Azure)
* [Ridimensionare i processi di Analisi di flusso di Azure per aumentare la velocità effettiva dell'elaborazione dei flussi di dati](stream-analytics-scale-jobs.md)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   
