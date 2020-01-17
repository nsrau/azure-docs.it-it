---
title: Eseguire la migrazione di Azure HDInsight 3,6 Apache Storm a HDInsight 4,0 Apache Spark
description: Le differenze e il flusso di migrazione per la migrazione di carichi di lavoro Apache Storm a Spark streaming o Spark Structured streaming.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/16/2019
ms.openlocfilehash: 916c54c3739d1164e4e9c1db67aa1f4e0dbd0c6c
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76157792"
---
# <a name="migrate-azure-hdinsight-36-apache-storm-to-hdinsight-40-apache-spark"></a>Eseguire la migrazione di Azure HDInsight 3,6 Apache Storm a HDInsight 4,0 Apache Spark

Questo documento illustra come eseguire la migrazione di carichi di lavoro Apache Storm in HDInsight 3,6 a HDInsight 4,0. HDInsight 4,0 non supporta il tipo di cluster Apache Storm ed è necessario eseguire la migrazione a un'altra piattaforma dati di streaming. Due opzioni adatte sono Apache Spark streaming e il flusso strutturato Spark. Questo documento descrive le differenze tra queste piattaforme e suggerisce anche un flusso di lavoro per la migrazione di carichi di lavoro Apache Storm.

## <a name="storm-migration-paths-in-hdinsight"></a>Percorsi di migrazione di Storm in HDInsight

Se si vuole eseguire la migrazione da Apache Storm in HDInsight 3,6 sono disponibili più opzioni:

* Streaming Spark in HDInsight 4,0
* Streaming strutturato Spark in HDInsight 4,0
* Analisi di flusso di Azure

Questo documento fornisce una guida per la migrazione da Apache Storm a Spark streaming e Spark Structured streaming.

> [!div class="mx-imgBorder"]
> ![percorso di migrazione di HDInsight Storm](./media/migrate-storm-to-spark/storm-migration-path.png)

## <a name="comparison-between-apache-storm-and-spark-streaming-spark-structured-streaming"></a>Confronto tra Apache Storm e Spark streaming, Spark Structured streaming

Apache Storm può offrire diversi livelli di elaborazione garantita dei messaggi. Un'applicazione Storm di base può ad esempio garantire un'elaborazione at-least-once, mentre [Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html) può garantire un'elaborazione exactly-once. Spark streaming e Spark Structured streaming garantiscono che qualsiasi evento di input venga elaborato una sola volta, anche se si verifica un errore del nodo. Storm include un modello che elabora ogni singolo evento ed è anche possibile usare il modello micro batch con Trident. Spark streaming e Spark Structured streaming forniscono il modello di elaborazione micro-batch.

|  |Storm |Streaming di Spark | Streaming strutturato Spark|
|---|---|---|---|
|**Garanzia di elaborazione degli eventi**|Almeno una volta <br> Esattamente una volta (Trident) |[Esattamente una volta](https://spark.apache.org/docs/latest/streaming-programming-guide.html)|[Esattamente una volta](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**Modello di elaborazione**|Tempo reale <br> Micro batch (Trident) |Batch micro |Batch micro |
|**Supporto dell'ora dell'evento**|[Sì](https://storm.apache.org/releases/2.0.0/Windowing.html)|No|[Sì](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**Lingue**|Java e così via.|Scala, Java, Python|Python, R, scala, Java, SQL|

### <a name="spark-streaming-vs-spark-structured-streaming"></a>Streaming strutturato Spark streaming vs Spark

Spark Structured streaming sostituisce Spark streaming (DStreams). Il flusso strutturato continuerà a ricevere miglioramenti e manutenzione, mentre DStreams sarà in modalità di manutenzione. **Nota: sono necessari collegamenti per evidenziare questo punto**. Il flusso strutturato non dispone di tutte le funzionalità di DStreams per le origini e i sink supportati in modo predefinito, quindi valutare i requisiti per scegliere l'opzione di elaborazione del flusso Spark appropriata.

## <a name="streaming-single-event-processing-vs-micro-batch-processing"></a>Elaborazione del flusso (evento singolo) rispetto all'elaborazione micro-batch

Storm fornisce un modello che elabora ogni singolo evento. Ciò significa che tutti i record in ingresso verranno elaborati non appena arrivano. Le applicazioni Spark streaming devono attendere una frazione di secondo per raccogliere ogni micro batch di eventi prima di inviare il batch per l'elaborazione. Al contrario, un'applicazione guidata dagli eventi elabora ogni evento immediatamente. La latenza di Spark Streaming è in genere inferiore a pochi secondi. I vantaggi dell'approccio basato su micro batch consistono in un'elaborazione dati più efficiente e in calcoli di aggregazione più semplici.

> [!div class="mx-imgBorder"]
> ![lo streaming e l'elaborazione di micro batch](./media/migrate-storm-to-spark/streaming-and-micro-batch-processing.png)

## <a name="storm-architecture-and-components"></a>Architettura e componenti di Storm

Le topologie Storm sono costituite da più componenti disposti in un grafo aciclico diretto (DAG). I componenti del grafo scambiano flussi di dati. Ogni componente utilizza uno o più flussi di dati e, facoltativamente, trasmette uno o più flussi.

|Componente |Description |
|---|---|
|Beccuccio|Consente di portare i dati in una topologia. trasmettendo uno o più flussi nella topologia stessa.|
|Bullone|Utilizza flussi emessi da beccucci o da altri Bolt. Facoltativamente, i bolt possono trasmettere flussi nella topologia. I bolt sono anche responsabili della scrittura dei dati in un archivio o servizio esterno, ad esempio HDFS, Kafka o HBase.|

> [!div class="mx-imgBorder"]
> ![interazione dei componenti Storm](./media/migrate-storm-to-spark/apache-storm-components.png)

Storm è costituito dai tre daemon seguenti, che mantengono il funzionamento del cluster Storm.

|Daemon |Description |
|---|---|
|Nimbus|Analogamente a Hadoop JobTracker, è responsabile della distribuzione del codice nel cluster e dell'assegnazione delle attività ai computer e del monitoraggio degli errori.|
|Zookeeper|Utilizzato per il coordinamento del cluster.|
|Supervisor|Ascolta il lavoro assegnato al computer e avvia e arresta i processi di lavoro in base alle direttive di Nimbus. Ogni processo di lavoro esegue un subset di una topologia. La logica dell'applicazione dell'utente (beccucci e Bolt) viene eseguita qui.|

> [!div class="mx-imgBorder"]
> daemon ![Nimbus, Zookeeper e Supervisor](./media/migrate-storm-to-spark/nimbus-zookeeper-supervisor.png)

## <a name="spark-streaming-architecture-and-components"></a>Architettura e componenti di Spark streaming

I passaggi seguenti riepilogano il modo in cui i componenti interagiscono in Spark streaming (DStreams) e Spark Structured streaming:

* Quando Spark streaming viene avviato, il driver avvia l'attività nell'Executor.
* L'Executor riceve un flusso da un'origine dati di flusso.
* Quando l'executor riceve i flussi di dati, suddivide il flusso in blocchi e li mantiene in memoria.
* I blocchi di dati vengono replicati in altri esecutori.
* I dati elaborati vengono quindi archiviati nell'archivio dati di destinazione.

> [!div class="mx-imgBorder"]
> ![il percorso di streaming Spark per l'output](./media/migrate-storm-to-spark/spark-streaming-to-output.png)

## <a name="spark-streaming-dstream-workflow"></a>Flusso di lavoro Spark streaming (DStream)

Allo scadere di ogni intervallo di invio in batch, viene prodotto un nuovo RDD che contiene tutti i dati dell'intervallo. I set di RDD continui vengono raccolti in un DStream. Ad esempio, se l'intervallo di invio in batch è lungo un secondo, il flusso DStream genera ogni secondo un batch che contiene un RDD, che a sua volta contiene tutti i dati inseriti durante il secondo. Durante l'elaborazione del flusso DStream, l'evento di temperatura appare in uno di questi batch. Un'applicazione Spark Streaming elabora i batch che contengono gli eventi e infine agisce sui dati archiviati in ogni RDD.

> [!div class="mx-imgBorder"]
> ![batch di elaborazione dello streaming Spark](./media/migrate-storm-to-spark/spark-streaming-batches.png)

Per informazioni dettagliate sulle diverse trasformazioni disponibili con Spark streaming, vedere [trasformazioni in DStreams](https://spark.apache.org/docs/latest/streaming-programming-guide.html#transformations-on-dstreams).

## <a name="spark-structured-streaming"></a>Spark Structured Streaming

Spark Structured streaming rappresenta un flusso di dati sotto forma di tabella senza limiti di profondità. La tabella continua a crescere man mano che arrivano nuovi dati. Questa tabella di input viene elaborata continuamente da una query con esecuzione prolungata e i risultati vengono inviati a una tabella di output.

In Structured Streaming i dati arrivano nel sistema e vengono immediatamente inseriti in una tabella di input. Si scriveranno query (usando le API DataFrame e Dataset) che eseguono operazioni su questa tabella di input.

L'output della query produce una *tabella dei risultati*che contiene i risultati della query. È possibile creare dati dalla tabella dei risultati per un archivio dati esterno, ad esempio un database relazionale.

Il momento in cui i dati vengono elaborati dalla tabella di input è controllato dall'intervallo di trigger. Per impostazione predefinita, l'intervallo di trigger è zero e di conseguenza Structured Streaming tenta di elaborare i dati non appena arrivano. In pratica, questo significa che non appena Structured Streaming ha completato l'elaborazione dell'esecuzione della query precedente, avvia un'altra elaborazione, eseguita su eventuali nuovi dati ricevuti. È possibile configurare il trigger per l'esecuzione in base a un intervallo specifico, in modo che i dati di streaming vengano elaborati in batch basati sul tempo.

> [!div class="mx-imgBorder"]
> ![l'elaborazione dei dati in un flusso strutturato](./media/migrate-storm-to-spark/structured-streaming-data-processing.png)

> [!div class="mx-imgBorder"]
> modello di programmazione ![per lo streaming strutturato](./media/migrate-storm-to-spark/structured-streaming-model.png)

## <a name="general-migration-flow"></a>Flusso generale della migrazione

Il flusso di migrazione consigliato da Storm a Spark presuppone la seguente architettura iniziale:

* Kafka viene usato come origine dati di flusso
* Kafka e Storm vengono distribuiti nella stessa rete virtuale
* I dati elaborati da Storm vengono scritti in un sink di dati, ad esempio archiviazione di Azure o Azure Data Lake Storage Gen2.

    > [!div class="mx-imgBorder"]
    > ![diagramma dell'ambiente corrente presunto](./media/migrate-storm-to-spark/presumed-current-environment.png)

Per eseguire la migrazione dell'applicazione da Storm a una delle API di Spark streaming, seguire questa procedura:

1. **Distribuire un nuovo cluster.** Distribuire un nuovo cluster HDInsight 4,0 Spark nella stessa rete virtuale e distribuire l'applicazione Spark streaming o Spark Structured streaming su di essa e testarla in modo approfondito.

    > [!div class="mx-imgBorder"]
    > ![nuova distribuzione Spark in HDInsight](./media/migrate-storm-to-spark/new-spark-deployment.png)

1. **Interrompere l'utilizzo del vecchio cluster Storm.** Nel Storm esistente arrestare l'utilizzo dei dati dall'origine dati di streaming e attendere che i dati completino la scrittura nel sink di destinazione.

    > [!div class="mx-imgBorder"]
    > ![interrompere l'utilizzo del cluster corrente](./media/migrate-storm-to-spark/stop-consuming-current-cluster.png)

1. **Avviare l'utilizzo del nuovo cluster Spark.** Avviare il flusso dei dati da un cluster HDInsight 4,0 Spark appena distribuito. A questo punto, il processo viene preso dall'utilizzo dell'offset più recente di Kafka.

    > [!div class="mx-imgBorder"]
    > ![iniziare a utilizzarlo in un nuovo cluster](./media/migrate-storm-to-spark/start-consuming-new-cluster.png)

1. **Rimuovere il vecchio cluster in base alle esigenze.** Una volta completata l'opzione e funzionante correttamente, rimuovere il vecchio cluster HDInsight 3,6 Storm in base alle esigenze.

    > [!div class="mx-imgBorder"]
    > ![rimuovere i cluster HDInsight precedenti in base alle esigenze](./media/migrate-storm-to-spark/remove-old-clusters1.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Storm, Spark streaming e Spark Structured streaming, vedere i documenti seguenti:

* [Panoramica dello streaming Apache Spark](../spark/apache-spark-streaming-overview.md)
* [Panoramica dello streaming strutturato Apache Spark](../spark/apache-spark-structured-streaming-overview.md)