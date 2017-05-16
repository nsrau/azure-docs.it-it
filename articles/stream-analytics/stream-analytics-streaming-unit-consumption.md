---
title: "Analisi di flusso di Azure: ottimizzare il processo per l&quot;uso efficiente delle unità di streaming | Documentazione Microsoft"
description: "Procedure consigliate di query per la scalabilità e le prestazioni in Analisi di flusso di Azure."
keywords: "unità di streaming, prestazioni delle query"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeffstok
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: 264331293b16ffb76d037a233eac564e1067f784
ms.contentlocale: it-it
ms.lasthandoff: 05/01/2017


---

# <a name="optimize-your-job-to-use-streaming-units-efficiently"></a>Ottimizzare il processo per l'uso efficiente delle unità di streaming

Analisi di flusso di Azure aggrega il "peso" delle prestazioni dell'esecuzione di un processo in unità di streaming. Le unità di streaming rappresentano le risorse di elaborazione che vengono usate per eseguire un processo. Le unità di streaming descrivono la capacità relativa di elaborazione di eventi in base a una misurazione combinata di CPU, memoria e frequenze di lettura e scrittura. Questa capacità consente di concentrarsi sulla logica della query, senza dover conoscere il funzionamento delle prestazioni del livello di archiviazione, allocare memoria per il processo manualmente e approssimare il numero di memorie centrali di CPU necessario per eseguire il processo in modo tempestivo.

## <a name="how-many-sus-are-required-for-a-job"></a>Quante unità di streaming sono necessarie per un processo?

Il numero di unità di streaming necessarie per un particolare processo dipende dalla configurazione della partizione per gli input e dalla query definita nel processo. Il pannello **Scala** consente di impostare il numero corretto di unità di streaming. È consigliabile allocare più unità di streaming di quelle necessarie. Il motore di elaborazione di Analisi di flusso è ottimizzato per la latenza e la velocità effettiva al costo di allocazione di memoria aggiuntiva.

In generale la procedura consigliata consiste nell'iniziare con 6 unità di streaming per le query che non usano *PARTITION BY*. Quindi determinare un punto di svolta usando un metodo di valutazione e correzione degli errori in cui si modifica il numero di unità di streaming dopo aver passato quantità rappresentative di dati ed esaminato la metrica di utilizzo %Utilization delle unità di streaming.

Analisi di flusso di Azure conserva gli eventi in una finestra denominata "buffer di riordino" prima di avviare qualsiasi elaborazione. Gli eventi vengono ordinati all'interno della finestra di riordino in base all'orario e le operazioni successive vengono eseguite sugli eventi ordinati in sequenza temporale. Riordinare gli eventi in base all'ora garantisce che l'operatore abbia visibilità su tutti gli eventi nell'intervallo di tempo stabilito. Consente inoltre all'operatore di eseguire l'elaborazione necessaria e produrre un output. Un effetto collaterale di questo meccanismo è che l'elaborazione viene posticipata del tempo corrispondente alla durata della finestra di riordino. Il footprint di memoria del processo (che influisce sul consumo delle unità di streaming) è una funzione le cui dimensioni corrispondono alla finestra di riordino e al numero di eventi che contiene.

> [!NOTE]
> Quando il numero di lettori cambia durante gli aggiornamenti del processo, vengono scritti avvisi temporanei nei log di controllo. I processi di Analisi di flusso vengono ripristinati automaticamente da questi problemi temporanei.

## <a name="common-high-memory-causes-for-high-su-usage-for-running-jobs"></a>Cause comuni di memoria elevata per l'utilizzo elevato delle unità di streaming per i processi in esecuzione

### <a name="high-cardinality-for-group-by"></a>Elevata cardinalità di GROUP BY

La cardinalità degli eventi in ingresso determina l'uso della memoria per il processo.

Ad esempio, in `SELECT count(*) from input group by clustered, tumblingwindow (minutes, 5)` la cardinalità della query è rappresentata dal numero associato a **cluster**.

Per limitare i problemi causati dalla cardinalità elevata, scalare orizzontalmente la query aumentando le partizioni mediante **PARTITION BY**.

```
Select count(*) from input
Partition By clusterid
GROUP BY clustered tumblingwindow (minutes, 5)
```

Il numero di *cluster* rappresenta qui la cardinalità di GROUP BY.

Dopo il partizionamento della query, viene distribuita su più nodi. Di conseguenza il numero di eventi in arrivo in ogni nodo diminuisce, riducendo così la dimensione del buffer di riordino. È inoltre consigliabile partizionare le partizioni dell'hub eventi in base all'ID partizione.

### <a name="high-unmatched-event-count-for-join"></a>Elevata quantità di eventi senza corrispondenza per JOIN

Il numero di eventi senza corrispondenza in un JOIN influisce sull'uso della memoria per la query. Ad esempio, si consideri una query che cerca il numero di impressioni di un annuncio che generano clic:

```
SELECT id from clicks INNER JOIN,
impressions on impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10
```

In questo scenario è possibile che vengano visualizzati molti annunci e siano generati pochi clic. Un risultato come questo richiede che il processo mantenga tutti gli eventi nell'intervallo di tempo. La quantità di memoria consumata è proporzionale alle dimensioni della finestra e alla frequenza degli eventi. 

Per evitare questa situazione, scalare orizzontalmente la query aumentando le partizioni tramite PARTITION BY. 

Dopo il partizionamento della query, viene distribuita su più nodi di elaborazione. Di conseguenza il numero di eventi in arrivo in ogni nodo diminuisce, riducendo così la dimensione del buffer di riordino.

### <a name="large-number-of-out-of-order-events"></a>Quantità elevata di eventi fuori sequenza 

Un numero elevato di eventi fuori sequenza per un lungo intervallo di tempo causerà l'aumento della dimensione del "buffer di riordino". Per mitigare questa situazione, scalare la query aumentando le partizioni tramite PARTITION BY. Dopo il partizionamento della query, viene distribuita su più nodi. Di conseguenza il numero di eventi in arrivo in ogni nodo diminuisce, riducendo così la dimensione del buffer di riordino. 


## <a name="get-help"></a>Ottenere aiuto
Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-get-started.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

