---
title: "Creare processi di Spark Streaming a disponibilità elevata in YARN - Azure HDInsight | Microsoft Docs"
description: "Come configurare Spark Streaming per uno scenario a disponibilità elevata."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: ramoha
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: ramoha
ms.openlocfilehash: f916f9939ac9683a2ee162ba4d2105f66187b111
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="create-high-availability-spark-streaming-jobs-with-yarn"></a>Creare processi di Spark Streaming a disponibilità elevata con YARN

Spark Streaming consente di implementare applicazioni scalabili, con velocità effettiva elevata e a tolleranza di errore per l'elaborazione di flussi di dati. È possibile connettere applicazioni Spark Streaming in un cluster HDInsight Spark a un'ampia gamma di origini dati, ad esempio Hub eventi di Azure, Azure IoT Hub, Kafka, Flume, Twitter, ZeroMQ, socket TCP non elaborati o monitorando il file system HDFS per le modifiche. Spark Streaming supporta la tolleranza di errore con la garanzia che un determinato evento venga elaborato una sola volta, anche in caso di errore di un nodo.

Spark Streaming crea processi con esecuzione prolungata durante i quali è possibile applicare trasformazioni ai dati e quindi esegue il push dei risultati in file system, database, dashboard e nella console. Spark Streaming elabora micro-batch di dati, raccogliendo prima un batch di eventi per un intervallo di tempo definito. Tale batch viene poi fatto proseguire per l'elaborazione e l'output. Gli intervalli di tempo dei batch sono in genere definiti in frazioni di secondo.

![Spark Streaming](./media/apache-spark-streaming-high-availability/spark-streaming.png)

## <a name="dstreams"></a>DStream

Spark Streaming rappresenta un flusso continuo di dati con un *flusso discretizzato* (DStream). È possibile creare questo flusso DStream da origini di input come Hub eventi o Kafka oppure applicando trasformazioni a un altro flusso DStream. Quando questo evento arriva all'applicazione Spark Streaming, l'evento viene archiviato in modo affidabile. Ovvero, i dati dell'evento vengono replicati in modo che più nodi ne abbiano una copia. Questo meccanismo assicura che l'errore di un singolo nodo non comporti la perdita dell'evento.

Il core di Spark usa *set di dati distribuiti resilienti* (RDD, Resilient Distributed Dataset). I set RDD distribuiscono i dati tra più nodi nel cluster, in cui ogni nodo mantiene in genere i propri dati completamente in memoria per garantire prestazioni ottimali. Ogni RDD rappresenta gli eventi raccolti in un intervallo di batch. Quando è trascorso l'intervallo di batch, Spark Streaming produce un nuovo RDD contenente tutti i dati in tale intervallo. Questo set continuo di RDD viene raccolto in un flusso DStream. Un'applicazione Spark Streaming elabora i dati archiviati nel set RDD di ogni batch.

![DStream Spark](./media/apache-spark-streaming-high-availability/DStream.png)

## <a name="spark-structured-streaming-jobs"></a>Processi di Spark Structured Streaming

Spark Structured Streaming è stato introdotto in Spark 2.0 come motore di analisi per l'uso con dati strutturati di flusso. Spark Structured Streaming usa le API del motore di invio in batch di SparkSQL. Come Spark Streaming, Spark Structured Streaming esegue i calcoli su micro-batch di dati in arrivo in modo continuo. Spark Structured Streaming rappresenta un flusso di dati come tabella di input con righe illimitate. Questo significa che la tabella di input continua a crescere con l'arrivo di nuovi dati. La tabella di input viene elaborata in modo continuo tramite una query con esecuzione prolungata e i risultati vengono scritti in una tabella di output.

![Spark Structured Streaming](./media/apache-spark-streaming-high-availability/structured-streaming.png)

Con Structured Streaming, i dai raggiungono il sistema e vengono acquisiti immediatamente nella tabella di Input. È quindi necessario scrivere query che eseguono operazioni su questa tabella di Input. L'output della query produce un'altra tabella, denominata tabella dei risultati. La tabella dei risultati contiene i risultati della query, da cui è possibile ricavare i dati da inviare a un archivio di dati esterno, come un database relazionale. L'*intervallo di trigger* definisce la tempistica per l'elaborazione dei dati dalla tabella di Input. Per impostazione predefinita,Structured Streaming elabora i dati non appena arrivano. Tuttavia, è anche possibile configurare il trigger per l'esecuzione con intervalli più lunghi, in modo che i dati del flusso vengono elaborati in batch basati sul tempo. I dati nella tabella dei risultati possono essere aggiornati completamente ogni volta che arrivano nuovi dati, in modo da includere tutti i dati di output dall'inizio della query sui flussi (*modalità completa*), o possono contenere solo i dati introdotti dopo l'ultima elaborazione della query (*modalità accodamento*).

## <a name="create-fault-tolerant-spark-streaming-jobs"></a>Creare processi di Spark Streaming a tolleranza di errore

Per creare un ambiente a disponibilità elevata per i processi di Spark Streaming, iniziare codificando i singoli processi per il ripristino in caso di errore. Questi processi con ripristino automatico sono a tolleranza di errore.

I set RDD offrono varie proprietà utili per i processi di Spark Streaming a disponibilità elevata e a tolleranza di errore:

* I batch di dati di input archiviati in set RDD come DStream vengono replicati automaticamente in memoria per la tolleranza di errore.
* I dati perduti a causa di un errore del nodo di lavoro possono essere ricalcolati dai dati di input replicati in nodi di lavoro diversi, a condizione che siano disponibili.
* Il recupero rapido da errori può essere completato entro un secondo, perché il recupero da errori/ritardi avviene tramite calcolo in memoria.

### <a name="exactly-once-semantics-with-spark-streaming"></a>Semantica exactly-once in Spark Streaming

Per creare un'applicazione che elabora ogni evento una sola volta, valutare la modalità di riavvio per tutti i punti di errore del sistema e come è possibile evitare perdite di dati. La semantica exactly-once richiede che non si verifichi alcuna perdita di dati in alcun punto e che l'elaborazione dei messaggi sia riavviabile, indipendentemente da dove si verifica l'errore. Vedere [Creare processi di Spark Streaming con elaborazione di eventi di tipo exactly-once](apache-spark-streaming-exactly-once.md).

## <a name="spark-streaming-and-yarn"></a>Spark Streaming e YARN

In HDInsight, il lavoro del cluster è coordinato da *Yet Another Resource Negotiator* (YARN). La progettazione della disponibilità elevata per Spark Streaming include tecniche per Spark Streaming e anche per i componenti YARN.  Di seguito è illustrata una configurazione di esempio con YARN. 

![Architettura YARN](./media/apache-spark-streaming-high-availability/yarn-arch.png)

Nelle sezioni seguenti vengono descritte alcune considerazioni sulla progettazione di questa configurazione.

### <a name="plan-for-failures"></a>Piano per gli errori

Per creare una configurazione di YARN per la disponibilità elevata, è necessario definire un piano per affrontare possibili errori di executor o driver. Alcuni processi di Spark Streaming prevedono anche requisiti di garanzia per i dati che richiedono interventi di installazione e configurazione aggiuntivi. Ad esempio, un'applicazione per l'uso dei flussi potrebbe avere un requisito aziendale di garanzia della totale assenza di perdita di dati, indipendentemente dagli eventuali errori che possono verificarsi nel sistema di streaming host o nel cluster HDInsight.

In caso di errore per un **executor**, le attività e i ricevitori corrispondenti vengono riavviati automaticamente da Spark, pertanto non è necessaria alcuna modifica di configurazione.

Tuttavia, se l'errore si verifica per un **driver**, anche tutti gli executor associati saranno in errore con conseguente perdita di tutti i blocchi e i risultati di calcolo ricevuti. Per eseguire il ripristino da un errore di driver, usare *checkpoint DStream* come descritto in [Creare processi di Spark Streaming con elaborazione di eventi di tipo exactly-once](apache-spark-streaming-exactly-once.md#use-checkpoints-for-drivers). La creazione di checkpoint DStream salva periodicamente il *grafico aciclico diretto* di DStream in una risorsa di archiviazione a tolleranza di errore, come Archiviazione di Azure.  La creazione di checkpoint consente a Spark Structured Streaming di riavviare il driver in errore dalle informazioni di checkpoint.  Questo riavvio del driver avvia nuovi esecutori e riavvia anche i ricevitori.

Per eseguire il ripristino dei driver con checkpoint DStream:

* Configurare il riavvio automatico del driver su YARN con l'impostazione di configurazione `yarn.resourcemanager.am.max-attempts`.
* Impostare una directory di checkpoint in un file system compatibile con HDFS con `streamingContext.checkpoint(hdfsDirectory)`.
* Ristrutturare il codice sorgente per l'uso di checkpoint per il ripristino, ad esempio:

    ```scala
        def creatingFunc() : StreamingContext = {
            val context = new StreamingContext(...)
            val lines = KafkaUtils.createStream(...)
            val words = lines.flatMap(...)
            ...
            context.checkpoint(hdfsDir)
        }

        val context = StreamingContext.getOrCreate(hdfsDir, creatingFunc)
        context.start()
    ```

* Configurare il ripristino dei dati persi abilitando il log write-ahead (WAL) con `sparkConf.set("spark.streaming.receiver.writeAheadLog.enable","true")` e disabilitare la replica in memoria per i flussi DStream di input con `StorageLevel.MEMORY_AND_DISK_SER`.

Per riepilogare, l'uso di checkpoint, log write-ahead e ricevitori affidabili consentirà di fornire funzionalità di ripristino dei dati "at-least-once":

* Exactly-once, purché i dati ricevuti non vadano persi e gli output siano idempotenti o transazionali.
* Exactly-once, con il nuovo approccio Kafka Direct che usa Kafka come log replicato, invece di usare ricevitori o log write-ahead.

### <a name="typical-concerns-for-high-availability"></a>Problematiche tipiche per la disponibilità elevata

* È più difficile monitorare i processi di streaming rispetto ai processi batch. I processi di Spark Streaming hanno in genere un'esecuzione prolungata e YARN non aggrega i log fino al completamento di un processo.  I checkpoint Spark vanno perduti durante gli aggiornamenti di applicazioni e Spark e sarà necessario cancellare la directory dei checkpoint durante l'aggiornamento.

* Configurare la modalità del cluster YARN per l'esecuzione dei driver anche in caso di errore di un client. Per configurare il riavvio automatico per i driver:

    ```
    spark.yarn.maxAppAttempts = 2
    spark.yarn.am.attemptFailuresValidityInterval=1h
    ```

* L'interfaccia utente di Spark e Spark Streaming include un sistema di metriche configurabile. È anche possibile usare librerie aggiuntive, come Graphite/Grafana per scaricare metriche del dashboard come 'num records processed' (numero di record elaborati), 'memory/GC usage on driver & executors' (utilizzo di memoria/GC per driver ed executor), 'total delay' (ritardo totale), 'utilization of the cluster' (utilizzo del cluster) e così via. In Structured Streaming versione 2.1 o successiva, è possibile usare `StreamingQueryListener` per raccogliere metriche aggiuntive.

* È consigliabile segmentare i processi con esecuzione prolungata.  Quando un'applicazione Spark Streaming viene inviata al cluster, è necessario definire la coda YARN in cui viene eseguito il processo. È possibile usare un'[utilità di pianificazione della capacità YARN](https://hadoop.apache.org/docs/stable/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html) per inviare i processi con esecuzione prolungata a code separate.

* Arrestare l'applicazione per l'uso dei flussi normalmente. Se gli offset sono noti e tutto lo stato dell'applicazione è archiviato esternamente, è possibile arrestare l'applicazione per l'uso dei flussi a livello di programmazione nella posizione appropriata. Una tecnica consiste nell'usare "thread di hook" in Spark, controllando la presenza di un flag esterno ogni *n* secondi. È anche possibile usare un *file marcatore* che viene creato in HDFS all'avvio dell'applicazione e poi rimosso quando si vuole arrestarla. Per l'approccio con file marcatore, usare un thread separato nell'applicazione Spark che chiama codice simile al seguente:

    ```scala
    streamingContext.stop(stopSparkContext = true, stopGracefully = true)
    // to be able to recover on restart, store all offsets in an external database
    ```

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica di Spark Streaming](apache-spark-streaming-overview.md)
* [Creare processi di Spark Streaming con elaborazione di eventi di tipo exactly-once](apache-spark-streaming-exactly-once.md)
* [Long-running Spark Streaming Jobs on YARN](http://mkuthan.github.io/blog/2016/09/30/spark-streaming-on-yarn/) (Processi di Spark Streaming con esecuzione prolungata in YARN) 
* [Structured Streaming: Fault Tolerant Semantics](http://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html#fault-tolerance-semantics) (Flusso strutturato: semantica con tolleranza di errore)
* [Discretized Streams: A Fault-Tolerant Model for Scalable Stream Processing](https://www2.eecs.berkeley.edu/Pubs/TechRpts/2012/EECS-2012-259.pdf) (Flussi discretizzati: un modello a tolleranza di errore per l'elaborazione di flussi scalabile)
