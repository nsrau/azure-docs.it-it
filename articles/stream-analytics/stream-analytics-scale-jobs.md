---
title: "Ridimensionare i processi di analisi di flusso per aumentare la velocità effettiva | Microsoft Docs"
description: "Informazioni su come ridimensionare i processi di Analisi di flusso configurando partizioni di input, ottimizzando la definizione di query e impostando le unità di streaming del processo."
keywords: flusso di dati, elaborazione del flusso di dati, ottimizzare analisi
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 7e857ddb-71dd-4537-b7ab-4524335d7b35
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 06/22/2017
ms.author: jeffstok
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 9a2b16fc6dff687e2a1fa03c9194d50711f53476
ms.contentlocale: it-it
ms.lasthandoff: 07/04/2017


---
# <a name="scale-azure-stream-analytics-jobs-to-increase-stream-data-processing-throughput"></a>Ridimensionare i processi di Analisi di flusso di Azure per aumentare la velocità effettiva dell'elaborazione dei flussi di dati
Questo articolo illustra come ottimizzare una query per aumentare la velocità effettiva per i processi di Analisi di flusso. Si apprenderà come ridimensionare tali processi configurando partizioni di input, ottimizzando la definizione di query, calcolando e impostando le *unità di streaming* dei processi. 

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>Quali sono le parti di un processo di Analisi di flusso?
Una definizione del processo di Analisi di flusso include input, query e output. Gli input sono le origini da cui il processo legge il flusso di dati, la query viene usata per trasformare il flusso di input dei dati e l'output è la destinazione a cui il processo invia i risultati.  

Un processo richiede almeno un'origine di input per il flusso dei dati. L'origine dell'input del flusso dei dati può essere archiviata in un hub eventi di Azure o in una risorsa di archiviazione BLOB di Azure. Per altre informazioni, vedere [Introduzione all'analisi di flusso di Azure](stream-analytics-introduction.md) e [Introduzione all'uso dell'analisi di flusso di Azure](stream-analytics-real-time-fraud-detection.md).

## <a name="partitions-in-event-hubs-and-azure-storage"></a>Partizioni negli hub eventi e nell'archiviazione di Azure
Il ridimensionamento di un processo di Analisi di flusso sfrutta i vantaggi offerti dall'uso di partizioni nell'input o nell'output. Il partizionamento consente di suddividere i dati in subset in base a una chiave di partizione. Un processo che utilizza i dati, come avviene per i processi di Analisi di flusso, può utilizzare diverse partizioni e scrivervi in parallelo, aumentando così la velocità effettiva. Quando si usa Analisi di flusso, è possibile sfruttare il partizionamento negli hub eventi e nell'archiviazione BLOB. 

Per altre informazioni sulle partizioni, vedere gli articoli seguenti:

* [Panoramica delle funzionalità di Hub eventi](../event-hubs/event-hubs-features.md#partitions)
* [Partizionamento dei dati](https://docs.microsoft.com/azure/architecture/best-practices/data-partitioning#partitioning-azure-blob-storage)


## <a name="streaming-units-sus"></a>Unità di streaming
Le unità di streaming rappresentano le risorse e la potenza di elaborazione necessarie per eseguire un processo di Analisi di flusso di Azure. Le unità di streaming descrivono la capacità relativa di elaborazione di eventi in base a una misurazione combinata di CPU, memoria e frequenze di lettura e scrittura. Ogni unità di streaming corrisponde a circa 1 MB al secondo di velocità effettiva. 

Il numero di unità di streaming necessarie per un particolare processo dipende dalla configurazione delle partizioni per gli input e dalla query definita per il processo. È prevista una quota massima di unità di streaming che è possibile selezionare per un processo. Per impostazione predefinita, ogni sottoscrizione di Azure ha una quota massima di 50 unità di streaming per tutti i processi di analisi in un'area specifica. Per superare la quota massima di unità di streaming per le sottoscrizioni, contattare il [supporto tecnico Microsoft](http://support.microsoft.com). I valori validi di unità di streaming per processo sono 1, 3, 6 e poi a salire, con incrementi di 6.

## <a name="embarrassingly-parallel-jobs"></a>Processi perfettamente paralleli
Un processo *perfettamente parallelo* è lo scenario più scalabile che può presentarsi in Analisi di flusso di Azure. Connette una partizione dell'input inviato a un'istanza della query a una partizione dell'output. Questo parallelismo presenta i requisiti seguenti:

1. Se la logica di query richiede che la stessa chiave venga elaborata dalla stessa istanza di query, è necessario verificare che gli eventi siano diretti alla stessa partizione dell'input. Per gli hub eventi, questo significa che per i dati di evento deve essere impostata la proprietà **PartitionKey**. In alternativa, è possibile usare mittenti partizionati. Per l'archiviazione BLOB, questo significa che gli eventi vengono inviati alla stessa cartella di partizione. Se la logica di query non richiede che la stessa chiave venga elaborata dalla stessa istanza di query, è possibile ignorare questo requisito. Un esempio di questa logica è offerto da una query semplice select-project-filter.  

2. Quando i dati sono disposti a livello di input, si deve verificare che la query sia partizionata. A questo scopo, è necessario usare la clausola **Partition By** in tutti i passaggi. È possibile eseguire più passaggi, ma tutti devono essere partizionati con la stessa chiave. Per ottenere un processo completamente parallelo, è attualmente necessario impostare la chiave di partizionamento su **PartitionId**.  

3. Solo gli hub eventi e l'archiviazione BLOB supportano attualmente l'output partizionato. Per l'output degli hub eventi, è necessario configurare la chiave di partizione come **PartitionId**. Per l'output dell'archiviazione BLOB, non è necessario eseguire operazioni.  

4. Il numero delle partizioni di input deve essere uguale a quello delle partizioni di output. L'output dell'archiviazione BLOB attualmente non supporta le partizioni, ma questo non è un problema perché lo schema di partizionamento viene ereditato dalla query upstream. Ecco alcuni esempi di valori di partizioni che consentono un processo perfettamente parallelo:  

   * 8 partizioni di input di hub eventi e 8 partizioni di output di hub eventi
   * 8 partizioni di input di hub eventi e output di archiviazione BLOB  
   * 8 partizioni di input di archiviazione BLOB e output di archiviazione BLOB  
   * 8 partizioni di input di archiviazione BLOB e 8 partizioni di output di hub eventi  

Le sezioni seguenti illustrano alcuni esempi di scenari perfettamente paralleli.

### <a name="simple-query"></a>Query semplice

* Input: hub eventi con 8 partizioni
* Output: hub eventi con 8 partizioni

Query:

    SELECT TollBoothId
    FROM Input1 Partition By PartitionId
    WHERE TollBoothId > 100

Questa query è un filtro semplice. Non è pertanto necessario preoccuparsi del partizionamento dell'input inviato all'hub eventi. Si noti che la query include la clausola **Partition By PartitionId** e quindi il requisito 2 illustrato in precedenza è pienamente soddisfatto. A livello di output, è necessario configurare l'output dell'hub eventi nel processo in modo che la chiave di partizione sia impostata su **PartitionId**. È infine necessario verificare che il numero delle partizioni di input sia uguale a quello delle partizioni di output.

### <a name="query-with-a-grouping-key"></a>Query con chiave di raggruppamento

* Input: hub eventi con 8 partizioni
* Output: archiviazione BLOB

Query:

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Questa query include una chiave di raggruppamento. La stessa chiave deve pertanto essere elaborata dalla stessa istanza di query. Ciò significa che gli eventi devono essere inviati all'hub eventi in modo partizionato. Ma quale chiave è necessario usare? **PartitionId** corrisponde a un concetto della logica di processo. La chiave effettiva di cui occuparsi è **TollBoothId** e quindi il valore di **PartitionKey** dei dati di evento deve essere **TollBoothId**. A questo scopo è necessario impostare **Partition By** su **PartitionId** nella query. Poiché l'output è costituito dall'archiviazione BLOB, non occorre preoccuparsi di configurare un valore di chiave di partizione, come definito dal requisito 4.

### <a name="multi-step-query-with-a-grouping-key"></a>Query a più passaggi con chiave di raggruppamento
* Input: hub eventi con 8 partizioni
* Output: istanza di hub eventi con 8 partizioni

Query:

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Questa query include una chiave di raggruppamento ed è quindi necessario che la stessa chiave venga elaborata dalla stessa istanza di query. È possibile adottare la stessa strategia usata nell'esempio precedente. In questo caso, la query è articolata in più passaggi. Per ogni passaggio è presente la clausola **Partition By PartitionID**? Sì, quindi la query soddisfa il requisito 3. A livello di output, è necessario impostare la chiave di partizione su **PartitionId**, come descritto in precedenza. Si può anche osservare che il numero di partizioni dell'output è identico a quello dell'input.

## <a name="example-scenarios-that-are-not-embarrassingly-parallel"></a>Esempi di scenari *non* perfettamente paralleli

Nella sezione precedente sono stati presentati alcuni scenari perfettamente paralleli. In questa sezione si illustreranno scenari che non soddisfano tutti i requisiti di parallelismo perfetto. 

### <a name="mismatched-partition-count"></a>Numero di partizioni non corrispondente
* Input: hub eventi con 8 partizioni
* Output: hub eventi con 32 partizioni

In questo caso, la query non è rilevante. Se il numero delle partizioni di input non corrisponde a quello delle partizioni di output, la topologia non è perfettamente parallela.

### <a name="not-using-event-hubs-or-blob-storage-as-output"></a>Uso di un output diverso da hub eventi o archiviazione BLOB
* Input: hub eventi con 8 partizioni
* Output: Power BI

L'output Power BI attualmente non supporta il partizionamento. Pertanto, questo scenario non è perfettamente parallelo.

### <a name="multi-step-query-with-different-partition-by-values"></a>Query a più passaggi con valori diversi per Partition By
* Input: hub eventi con 8 partizioni
* Output: hub eventi con 8 partizioni

Query:

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By TollBoothId
    GROUP BY TumblingWindow(minute, 3), TollBoothId

Come è possibile osservare, il secondo passaggio usa **TollBoothId** come chiave di partizionamento, a differenza del primo passaggio. È quindi necessario eseguire una riproduzione in ordine casuale. 

Gli esempi precedenti illustrano alcuni processi di Analisi di flusso che sono o non sono conformi a una topologia perfettamente parallela. Se sono conformi, possono raggiungere il livello massimo di scalabilità. Per i processi che non rientrano in nessuno di questi profili, in futuro saranno disponibili aggiornamenti con le linee guida per il ridimensionamento. Per il momento, seguire le indicazioni generali riportate nelle sezioni seguenti.

## <a name="calculate-the-maximum-streaming-units-of-a-job"></a>Calcolare il numero massimo di unità di streaming di un processo
Il numero totale di unità di streaming che possono essere usate da un processo di Analisi dei flussi dipende dal numero di passaggi nella query definita per il processo e dal numero di partizioni per ogni passaggio.

### <a name="steps-in-a-query"></a>Passaggi in una query
Una query può includere uno o più passaggi. Ogni passaggio è una sottoquery definita mediante la parola chiave **WITH**. Anche la query esterna alla parola chiave **WITH** (una sola query) viene contata come passaggio. Ad esempio, l'istruzione **SELECT** nella query seguente:

    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute,3), TollBoothId

Questa query include due passaggi.

> [!NOTE]
> Questa query viene illustrata in dettaglio più avanti nell'articolo.
>  

### <a name="partition-a-step"></a>Partizionamento di un passaggio
Il partizionamento di un passaggio richiede le condizioni seguenti:

* L'origine di input deve essere partizionata. 
* L'istruzione **SELECT** della query deve leggere da un'origine di input partizionata.
* La query all'interno del passaggio deve includere la parola chiave **Partition By**.

Quando una query è partizionata, gli eventi di input vengono elaborati e aggregati in gruppi separati di partizioni e per ogni gruppo vengono generati eventi di output. Se si vuole un aggregato combinato, è necessario creare un secondo passaggio non partizionato per l'aggregazione.

### <a name="calculate-the-max-streaming-units-for-a-job"></a>Calcolare il numero massimo di unità di streaming per un processo
Per un processo di Analisi di flusso, l'insieme di tutti i passaggi non partizionati può raggiungere un massimo di sei unità di streaming. Per aggiungere altre unità, è necessario partizionare un passaggio. Ogni partizione può includere sei unità di streaming.

<table border="1">
<tr><th>Query</th><th>Numero massimo di unità di streaming per il processo</th></td>

<tr><td>
<ul>
<li>La query contiene un unico passaggio.</li>
<li>Il passaggio non è partizionato.</li>
</ul>
</td>
<td>6</td></tr>

<tr><td>
<ul>
<li>Il flusso di dati di input è suddiviso in 3 partizioni.</li>
<li>La query contiene un unico passaggio.</li>
<li>Il passaggio è partizionato.</li>
</ul>
</td>
<td>18</td></tr>

<tr><td>
<ul>
<li>La query contiene due passaggi.</li>
<li>Nessuno dei passaggi è partizionato.</li>
</ul>
</td>
<td>6</td></tr>

<tr><td>
<ul>
<li>Il flusso di dati di input è suddiviso in 3 partizioni.</li>
<li>La query contiene due passaggi. Il passaggio di input viene partizionato, al contrario del secondo passaggio.</li>
<li>L'istruzione <strong>SELECT</strong> legge dall'input partizionato.</li>
</ul>
</td>
<td>24 (18 per i passaggi partizionati + 6 per i passaggi non partizionati)</td></tr>
</table>

### <a name="examples-of-scaling"></a>Esempi di ridimensionamento

La query seguente calcola il numero di automobili che passano per una stazione di pedaggio con tre caselli in un intervallo di tre minuti. Il numero di unità di streaming di questa query può essere aumentato fino a sei.

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Per usare più unità di streaming per la query, è necessario che il flusso di dati di input e la query siano entrambi partizionati. Se il partizionamento del flusso di dati è impostato su 3, la query modificata seguente può essere ridimensionata fino a un massimo di 18 unità di streaming:

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Quando una query è partizionata, gli eventi di input vengono elaborati e aggregati in gruppi di partizioni separati e vengono anche generati eventi di output per ognuno dei gruppi. Quando il campo **GROUP BY** non corrisponde alla chiave di partizione nel flusso di dati di input, il partizionamento può causare risultati imprevisti. Ad esempio, il campo **TollBoothId** nella query precedente non corrisponde alla chiave di partizione **Input1**. I dati provenienti dal casello 1 possono pertanto essere distribuiti in più partizioni.

Le singole partizioni di **Input1** verranno elaborate separatamente da Analisi di flusso. Verranno pertanto creati più record del conteggio relativo al passaggio di automobili dallo stesso casello nella stessa finestra a cascata. Se la chiave di partizione di input non può essere modificata, è possibile risolvere questo problema aggiungendo un altro passaggio non di partizione, come nell'esempio seguente:

    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId

Per questa query è possibile aumentare il numero di unità di streaming fino a 24.

> [!NOTE]
> Se si uniscono due flussi, verificare che tali flussi siano partizionati in base alla chiave di partizione della colonna usata per le unioni. Controllare inoltre che in entrambi i flussi sia presente lo stesso numero di partizioni.
> 
> 

## <a name="configure-stream-analytics-streaming-units"></a>Configurare le unità di streaming di Analisi di flusso

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nell'elenco delle risorse trovare il processo di Analisi di flusso da ridimensionare e aprirlo.
3. Nel pannello del processo, in **Configura**, fare clic su **Scale** (Ridimensiona).

    ![Configurazione del processo di Analisi di flusso nel portale di Azure][img.stream.analytics.preview.portal.settings.scale]

4. Usare il dispositivo di scorrimento per impostare le unità di streaming per il processo. Si noti che è possibile definire solo impostazioni specifiche delle unità di streaming.


## <a name="monitor-job-performance"></a>Monitorare le prestazioni del processo
Nel portale di Azure è possibile rilevare la velocità effettiva di un processo:

![Processi di monitoraggio di Analisi dei flussi di Azure][img.stream.analytics.monitor.job]

Calcolare la velocità effettiva prevista del carico di lavoro. Se la velocità effettiva è inferiore al previsto, ottimizzare la partizione di input e la query, quindi aggiungere unità di streaming al processo.


## <a name="visualize-stream-analytics-throughput-at-scale-the-raspberry-pi-scenario"></a>Visualizzare la velocità effettiva di Analisi di flusso su larga scala: scenario Raspberry Pi
Per illustrare la scalabilità dei processi di Analisi di flusso, è stato eseguito un esperimento in base all'input di un dispositivo Raspberry Pi. L'esperimento ha consentito di osservare l'effetto prodotto da più unità di streaming e partizioni sulla velocità effettiva.

In questo scenario il dispositivo invia i dati dei sensori (client) a un hub eventi. Analisi di flusso elabora i dati e invia come output un avviso o dati statistici a un altro hub eventi. 

Il client invia i dati dei sensori in formato JSON e anche l'output dei dati è in formato JSON. L'aspetto dei dati sarà simile al seguente:

    {"devicetime":"2014-12-11T02:24:56.8850110Z","hmdt":42.7,"temp":72.6,"prss":98187.75,"lght":0.38,"dspl":"R-PI Olivier's Office"}

La query seguente consente di inviare un avviso quando una luce si spegne:

    SELECT AVG(lght),
     "LightOff" as AlertText
    FROM input TIMESTAMP
    BY devicetime
     WHERE
        lght< 0.05 GROUP BY TumblingWindow(second, 1)

### <a name="measure-throughput"></a>Misurare la velocità effettiva

In questo contesto la velocità effettiva è la quantità di dati di input elaborata da Analisi di flusso in un determinato intervallo di tempo (10 minuti). Per ottenere la velocità effettiva di elaborazione ottimale per i dati di input, l'input del flusso dei dati e la query sono stati partizionati. Nella query è stato incluso **COUNT()** per misurare il numero di eventi di input che sono stati elaborati. Per essere certi che il processo non rimanesse semplicemente in attesa dell'arrivo di eventi di input, ogni partizione dell'hub eventi di input è stata precaricata con circa 300 MB di dati di input.

La tabella seguente mostra i risultati ottenuti aumentando il numero di unità di streaming e il numero di partizioni corrispondenti negli hub eventi.  

<table border="1">
<tr><th>Partizioni di input</th><th>Partizioni di output</th><th>Unità di streaming</th><th>Velocità effettiva sostenuta
</th></td>

<tr><td>12</td>
<td>12</td>
<td>6</td>
<td>4,06 MB/s</td>
</tr>

<tr><td>12</td>
<td>12</td>
<td>12</td>
<td>8,06 MB/s</td>
</tr>

<tr><td>48</td>
<td>48</td>
<td>48</td>
<td>38,32 MB/s</td>
</tr>

<tr><td>192</td>
<td>192</td>
<td>192</td>
<td>172,67 MB/s</td>
</tr>

<tr><td>480</td>
<td>480</td>
<td>480</td>
<td>454,27 MB/s</td>
</tr>

<tr><td>720</td>
<td>720</td>
<td>720</td>
<td>609,69 MB/s</td>
</tr>
</table>

Il grafico seguente illustra la relazione tra unità di streaming e velocità effettiva.

![img.stream.analytics.perfgraph][img.stream.analytics.perfgraph]

## <a name="get-help"></a>Ottenere aiuto
Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-real-time-fraud-detection.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   

<!--Link references-->

[microsoft.support]: http://support.microsoft.com
[azure.management.portal]: http://manage.windowsazure.com
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301


