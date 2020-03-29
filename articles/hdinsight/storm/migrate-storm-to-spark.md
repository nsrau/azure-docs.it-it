---
title: Eseguire la migrazione di Azure HDInsight 3.6 Apache Storm a HDInsight 4.0 Apache Spark
description: Differenze e flusso di migrazione per la migrazione dei carichi di lavoro Apache Storm a Spark Streaming o Spark Structured Streaming.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/16/2019
ms.openlocfilehash: 916c54c3739d1164e4e9c1db67aa1f4e0dbd0c6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76157792"
---
# <a name="migrate-azure-hdinsight-36-apache-storm-to-hdinsight-40-apache-spark"></a>Eseguire la migrazione di Azure HDInsight 3.6 Apache Storm a HDInsight 4.0 Apache Spark

Questo documento descrive come eseguire la migrazione dei carichi di lavoro di Apache Storm in HDInsight 3.6 a HDInsight 4.0.This document describes how to migrate Apache Storm workloads on HDInsight 3.6 to HDInsight 4.0. HDInsight 4.0 non supporta il tipo di cluster Apache Storm ed è necessario eseguire la migrazione a un'altra piattaforma di streaming dati. Due opzioni adatte sono Apache Spark Streaming e Spark Structured Streaming. Questo documento descrive le differenze tra queste piattaforme e consiglia anche un flusso di lavoro per la migrazione dei carichi di lavoro Di Apache Storm.This document describes the differences between these platforms and also recommends a workflow for migrating Apache Storm workloads.

## <a name="storm-migration-paths-in-hdinsight"></a>Percorsi di migrazione di Tempesta in HDInsight

Se si desidera eseguire la migrazione da Apache Storm su HDInsight 3.6 sono disponibili più opzioni:If you want to migrate from Apache Storm on HDInsight 3.6 you have multiple options:

* Spark Streaming su HDInsight 4.0
* Spark Structured Streaming su HDInsight 4.0
* Analisi di flusso di Azure

Questo documento fornisce una guida per la migrazione da Apache Storm a Spark Streaming e Spark Structured Streaming.

> [!div class="mx-imgBorder"]
> ![Percorso di migrazione HDInsight Storm](./media/migrate-storm-to-spark/storm-migration-path.png)

## <a name="comparison-between-apache-storm-and-spark-streaming-spark-structured-streaming"></a>Confronto tra Apache Storm e Spark Streaming, Spark Structured Streaming

Apache Storm può offrire diversi livelli di elaborazione garantita dei messaggi. Un'applicazione Storm di base può ad esempio garantire un'elaborazione at-least-once, mentre [Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html) può garantire un'elaborazione exactly-once. Spark Streaming e Spark Structured Streaming garantiscono che qualsiasi evento di input venga elaborato esattamente una volta, anche se si verifica un errore del nodo. Storm ha un modello che elabora ogni singolo evento ed è anche possibile utilizzare il modello Micro Batch con Trident. Spark Streaming e Spark Structured Streaming forniscono il modello di elaborazione Micro-Batch.

|  |Storm |Streaming di Spark | Streaming strutturato Spark|
|---|---|---|---|
|**Garanzia di elaborazione degli eventi**|Almeno una volta <br> Esattamente una volta (Trident) |[Esattamente una volta](https://spark.apache.org/docs/latest/streaming-programming-guide.html)|[Esattamente una volta](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**Modello di elaborazione**|Tempo reale <br> Micro Batch (Trident) |Micro Batch |Micro Batch |
|**Supporto per il tempo dell'evento**|[Sì](https://storm.apache.org/releases/2.0.0/Windowing.html)|No|[Sì](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**Languages**|Java, ecc.|Scala, Java, Python|Python, R, Scala, Java, SQL|

### <a name="spark-streaming-vs-spark-structured-streaming"></a>Streaming Spark vs Spark in streaming strutturato

Spark Structured Streaming sta sostituendo Spark Streaming (DStreams). Lo streaming strutturato continuerà a ricevere miglioramenti e manutenzione, mentre DStreams sarà solo in modalità di manutenzione. **Nota: sono necessari collegamenti per enfatizzare questo punto**. Lo streaming strutturato non dispone di tante funzionalità come DStream per le origini e i sink supportati, quindi valuta i requisiti per scegliere l'opzione di elaborazione del flusso Spark appropriata.

## <a name="streaming-single-event-processing-vs-micro-batch-processing"></a>Elaborazione di streaming (evento singolo) e elaborazione Micro-Batch

Storm fornisce un modello che elabora ogni singolo evento. Ciò significa che tutti i record in entrata verranno elaborati non appena arrivano. Le applicazioni Spark Streaming devono attendere una frazione di secondo per raccogliere ogni micro batch di eventi prima di inviare ogni batch per l'elaborazione. Al contrario, un'applicazione guidata dagli eventi elabora ogni evento immediatamente. La latenza di Spark Streaming è in genere inferiore a pochi secondi. I vantaggi dell'approccio basato su micro batch consistono in un'elaborazione dati più efficiente e in calcoli di aggregazione più semplici.

> [!div class="mx-imgBorder"]
> ![streaming ed elaborazione di micro-batch](./media/migrate-storm-to-spark/streaming-and-micro-batch-processing.png)

## <a name="storm-architecture-and-components"></a>Architettura e componenti Di Storm

Le topologie Storm sono costituite da più componenti disposti in un grafo aciclico diretto (DAG). I componenti del grafo scambiano flussi di dati. Ogni componente utilizza uno o più flussi di dati e, facoltativamente, trasmette uno o più flussi.

|Componente |Descrizione |
|---|---|
|Beccuccio|Porta i dati in una topologia. trasmettendo uno o più flussi nella topologia stessa.|
|Bullone|Utilizza flussi emessi da beccucci o altri bulloni. Facoltativamente, i bolt possono trasmettere flussi nella topologia. I bolt sono anche responsabili della scrittura dei dati in un archivio o servizio esterno, ad esempio HDFS, Kafka o HBase.|

> [!div class="mx-imgBorder"]
> ![interazione dei componenti delle tempeste](./media/migrate-storm-to-spark/apache-storm-components.png)

Tempesta è costituito dai seguenti tre daemons, che mantengono l'ammasso Storm funzionante.

|Daemon |Descrizione |
|---|---|
|Nimbus|Analogamente ad Hadoop JobTracker, è responsabile della distribuzione del codice intorno al cluster e dell'assegnazione di attività ai computer e del monitoraggio degli errori.|
|Zookeeper|Utilizzato per il coordinamento del cluster.|
|Supervisore|Ascolta il lavoro assegnato alla propria macchina e avvia e arresta i processi di lavoro in base alle direttive di Nimbus. Ogni processo di lavoro esegue un sottoinsieme di una topologia. La logica dell'applicazione dell'utente (Spouts e Bolt) viene eseguita qui.|

> [!div class="mx-imgBorder"]
> ![nimbus, zookeeper e daemons supervisore](./media/migrate-storm-to-spark/nimbus-zookeeper-supervisor.png)

## <a name="spark-streaming-architecture-and-components"></a>Architettura e componenti di Spark Streaming

I passaggi seguenti riepilogano il modo in cui i componenti funzionano insieme in Spark Streaming (DStream) e Spark Structured Streaming:

* Quando Spark Streaming viene avviato, il driver avvia l'attività nell'esecutore.
* L'esecutore riceve un flusso da un'origine dati in streaming.
* Quando l'esecutore riceve flussi di dati, suddivide il flusso in blocchi e li mantiene in memoria.
* Blocchi di dati vengono replicati ad altri esecutori.
* I dati elaborati vengono quindi archiviati nell'archivio dati di destinazione.

> [!div class="mx-imgBorder"]
> ![spark percorso di streaming all'output](./media/migrate-storm-to-spark/spark-streaming-to-output.png)

## <a name="spark-streaming-dstream-workflow"></a>Flusso di lavoro Spark Streaming (DStream)

Allo scadere di ogni intervallo di invio in batch, viene prodotto un nuovo RDD che contiene tutti i dati dell'intervallo. I set continui di RDD vengono raccolti in un DStream. Ad esempio, se l'intervallo di invio in batch è lungo un secondo, il flusso DStream genera ogni secondo un batch che contiene un RDD, che a sua volta contiene tutti i dati inseriti durante il secondo. Durante l'elaborazione del flusso DStream, l'evento di temperatura appare in uno di questi batch. Un'applicazione Spark Streaming elabora i batch che contengono gli eventi e infine agisce sui dati archiviati in ogni RDD.

> [!div class="mx-imgBorder"]
> ![spark streaming batch di elaborazione](./media/migrate-storm-to-spark/spark-streaming-batches.png)

Per informazioni dettagliate sulle diverse trasformazioni disponibili con Spark Streaming, vedere [Trasformazioni su DStream](https://spark.apache.org/docs/latest/streaming-programming-guide.html#transformations-on-dstreams).

## <a name="spark-structured-streaming"></a>Spark Structured Streaming

Spark Structured Streaming rappresenta un flusso di dati come una tabella che non è delimitata in profondità. La tabella continua a crescere con l'arrivo di nuovi dati. Questa tabella di input viene elaborata continuamente da una query a esecuzione prolungata e i risultati vengono inviati a una tabella di output.

In Structured Streaming i dati arrivano nel sistema e vengono immediatamente inseriti in una tabella di input. Si scriveranno query (usando le API DataFrame e Dataset) che eseguono operazioni su questa tabella di input.

L'output della query produce una *tabella dei risultati*che contiene i risultati della query. È possibile estrarre dati dalla tabella dei risultati per un datastore esterno, ad esempio un database relazionale.

Il periodo in cui i dati vengono elaborati dalla tabella di input viene controllato dall'intervallo di trigger. Per impostazione predefinita, l'intervallo di trigger è zero e di conseguenza Structured Streaming tenta di elaborare i dati non appena arrivano. In pratica, questo significa che non appena Structured Streaming ha completato l'elaborazione dell'esecuzione della query precedente, avvia un'altra elaborazione, eseguita su eventuali nuovi dati ricevuti. È possibile configurare il trigger per l'esecuzione in base a un intervallo specifico, in modo che i dati di streaming vengano elaborati in batch basati sul tempo.

> [!div class="mx-imgBorder"]
> ![elaborazione dei dati in streaming strutturato](./media/migrate-storm-to-spark/structured-streaming-data-processing.png)

> [!div class="mx-imgBorder"]
> ![modello di programmazione per lo streaming strutturato](./media/migrate-storm-to-spark/structured-streaming-model.png)

## <a name="general-migration-flow"></a>Flusso di migrazione generale

Il flusso di migrazione consigliato da Storm a Spark presuppone l'architettura iniziale seguente:The recommended migration flow from Storm to Spark assumes the following initial architecture:

* Kafka viene utilizzato come origine dati in streaming
* Kafka e Storm sono distribuiti sulla stessa rete virtuale
* I dati elaborati da Storm vengono scritti in un sink di dati, ad esempio Archiviazione di Azure o Azure Data Lake Storage Gen2.The data processed by Storm is written to a data sink, such as Azure Storage or Azure Data Lake Storage Gen2.

    > [!div class="mx-imgBorder"]
    > ![diagramma dell'ambiente corrente presunto](./media/migrate-storm-to-spark/presumed-current-environment.png)

Per eseguire la migrazione dell'applicazione da Storm a una delle API di streaming Spark, eseguire le operazioni seguenti:

1. **Distribuire un nuovo cluster.** Distribuire un nuovo cluster HDInsight 4.0 Spark nella stessa rete virtuale e distribuire l'applicazione Spark Streaming o Spark Structured Streaming su di esso e testarla accuratamente.

    > [!div class="mx-imgBorder"]
    > ![nuova distribuzione di spark in HDInsight](./media/migrate-storm-to-spark/new-spark-deployment.png)

1. **Smetti di consumare sul vecchio cluster Storm.** In Storm esistente, interrompere l'utilizzo dei dati dall'origine dati in streaming e attendere che i dati completino la scrittura nel sink di destinazione.

    > [!div class="mx-imgBorder"]
    > ![interrompere l'utilizzo del cluster corrente](./media/migrate-storm-to-spark/stop-consuming-current-cluster.png)

1. **Iniziare a utilizzare il nuovo cluster Spark.Start consuming on the new Spark cluster.** Avviare lo streaming dei dati da un cluster Spark HDInsight 4.0 appena distribuito. In questo momento, il processo viene ripreso consumando l'ultimo offset Kafka.

    > [!div class="mx-imgBorder"]
    > ![iniziare a consumare sul nuovo cluster](./media/migrate-storm-to-spark/start-consuming-new-cluster.png)

1. **Rimuovere il cluster precedente in base alle esigenze.** Una volta che lo switch è completo e funziona correttamente, rimuovere il vecchio cluster HDInsight 3.6 Storm in base alle esigenze.

    > [!div class="mx-imgBorder"]
    > ![rimuovere i cluster HDInsight precedenti in base alle esigenzeRemove old HDInsight clusters as needed](./media/migrate-storm-to-spark/remove-old-clusters1.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Storm, Spark Streaming e Spark Structured Streaming, vedere i documenti seguenti:For more information about Storm, Spark Streaming, and Spark Structured Streaming, see the following documents:

* [Panoramica di Apache Spark Streaming](../spark/apache-spark-streaming-overview.md)
* [Panoramica di Apache Spark Structured Streaming](../spark/apache-spark-structured-streaming-overview.md)