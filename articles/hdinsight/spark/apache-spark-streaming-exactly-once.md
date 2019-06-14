---
title: Creare processi di Spark Streaming con elaborazione di eventi di tipo exactly-once - Azure HDInsight
description: Come configurare Spark Streaming per elaborare un evento esattamente una sola volta.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: 388723624fde73899809b95ff8ae4ee23cf49a9d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64705095"
---
# <a name="create-apache-spark-streaming-jobs-with-exactly-once-event-processing"></a>Creare processi di Apache Spark Streaming con elaborazione di eventi di tipo exactly-once

Le applicazioni di elaborazione di flussi usano approcci diversi per la gestione della rielaborazione dei messaggi dopo un errore nel sistema:

* At-least-once (almeno una volta): viene garantita l'elaborazione di ogni messaggio, che può avvenire più volte.
* At-most-once (al massimo una volta): ogni messaggio può venire o meno elaborato. Se un messaggio viene elaborato, ciò avviene una sola volta.
* Exactly-once (esattamente una volta): viene garantita l'elaborazione di ogni messaggio una sola volta.

Questo articolo illustra come configurare Spark Streaming per ottenere l'elaborazione di tipo exactly-once.

## <a name="exactly-once-semantics-with-apache-spark-streaming"></a>Semantica exactly-once in Apache Spark Streaming

Valutare prima di tutto la modalità di riavvio per tutti i punti di errore del sistema dopo un problema e come è possibile evitare perdite di dati. Un'applicazione Spark Streaming ha:

* Un'origine di input
* Uno o più processi ricevitore che eseguono il pull dei dati dall'origine di input
* Attività di elaborazione dei dati
* Un sink di output
* Un processo driver che gestisce il processo con esecuzione prolungata

La semantica exactly-once richiede che non si verifichi alcuna perdita di dati in alcun punto e che l'elaborazione dei messaggi sia riavviabile, indipendentemente da dove si verifica l'errore.

### <a name="replayable-sources"></a>Origini riproducibili

L'origine da cui l'applicazione Spark Streaming legge gli eventi deve essere *riproducibile*. Ciò significa che nei casi in cui il messaggio è stato recuperato ma si è verificato un errore nel sistema prima che il messaggio potesse essere salvato in modo permanente o elaborato, l'origine deve fornire di nuovo lo stesso messaggio.

In Azure, sia Hub eventi di Azure che [Apache Kafka](https://kafka.apache.org/) in HDInsight forniscono origini riproducibili. Un altro esempio di origine riproducibile è un file system a tolleranza di errore come [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html), BLOB del servizio di archiviazione di Azure o Azure Data Lake Storage, in cui tutti i dati vengono mantenuti per sempre e in qualsiasi momento è possibile leggere nuovamente i dati nella loro interezza.

### <a name="reliable-receivers"></a>Ricevitori affidabili

In Spark Streaming le origini come Hub eventi e Kafka hanno *ricevitori affidabili*, ognuno dei quali tiene traccia dello stato di lettura dell'origine. Un ricevitore affidabile salva in modo permanente il proprio stato in un archivio a tolleranza di errore, all'interno di [Apache ZooKeeper](https://zookeeper.apache.org/) oppure in checkpoint di Spark Streaming scritti in Hadoop Distributed File System. In caso di errore e successivo riavvio, un ricevitore di questo tipo può continuare da dove si è interrotto.

### <a name="use-the-write-ahead-log"></a>Usare il log write-ahead

Spark Streaming supporta l'uso di un log write-ahead, dove ogni evento ricevuto viene prima di tutto scritto nella directory di checkpoint di Spark in un archivio a tolleranza di errore e quindi archiviato in oggetto RDD (Resilient Distributed Dataset). In Azure l'archivio a tolleranza di errore è supportato da HDFS tramite Archiviazione di Azure o Azure Data Lake Storage. Nell'applicazione Spark Streaming, il log write-ahead viene abilitato per tutti i ricevitori impostando l'opzione di configurazione `spark.streaming.receiver.writeAheadLog.enable` su `true`. Il log write-ahead fornisce tolleranza di errore per i casi in cui si verificano errori sia del driver che degli executor.

Per i ruoli di lavoro che eseguono attività sui dati degli eventi, ogni oggetto RDD per definizione viene sia replicato che distribuito in più ruoli di lavoro. Se si verifica un errore di un'attività a causa di un arresto anomalo del ruolo di lavoro che la sta eseguendo, l'attività viene riavviata in un altro ruolo di lavoro che ha una replica dei dati dell'evento, in modo che l'evento non vada perso.

### <a name="use-checkpoints-for-drivers"></a>Usare i checkpoint per i driver

I driver di processo devono essere riavviabili. Se si verifica un arresto anomalo del driver che esegue l'applicazione Spark Streaming, si arrestano anche tutti i ricevitori in esecuzione, le attività e gli oggetti RDD in cui sono archiviati i dati dell'evento. In questo caso, è necessario poter salvare lo stato del processo in modo che sia possibile riprenderlo in seguito. A tale scopo, è possibile creare periodicamente checkpoint nel grafo aciclico diretto di DStream nell'archivio a tolleranza di errore. I metadati del grafo aciclico diretto includono la configurazione usata per creare l'applicazione di streaming, le operazioni che definiscono l'applicazione e qualsiasi batch accodato ma non ancora completato. Questi metadati consentono di riavviare un driver in cui si è verificato un errore usando le informazioni di checkpoint. Quando il driver viene riavviato, avvia nuovi ricevitori che recuperano i dati dell'evento reinserendoli negli oggetti RDD dal log write-ahead.

I checkpoint vengono abilitati in Spark Streaming Spark in due passaggi. 

1. Nell'oggetto StreamingContext, configurare il percorso di archiviazione per i checkpoint:

    ```Scala
    val ssc = new StreamingContext(spark, Seconds(1))
    ssc.checkpoint("/path/to/checkpoints")
    ```

    In HDInsight questi checkpoint devono essere salvati nell'archivio predefinito collegato al cluster, ovvero Archiviazione di Azure oppure Azure Data Lake Storage.

2. Specificare quindi un intervallo di checkpoint (in secondi) in DStream. A ogni intervallo, i dati relativi allo stato derivati dall'evento di input vengono salvati in modo permanente nell'archivio. I dati relativi allo stato salvati in modo permanente possono ridurre le attività di calcolo necessarie quando si ricrea lo stato dall'evento di origine.

    ```Scala
    val lines = ssc.socketTextStream("hostname", 9999)
    lines.checkpoint(30)
    ssc.start()
    ssc.awaitTermination()
    ```

### <a name="use-idempotent-sinks"></a>Usare sink idempotenti

Il sink di destinazione in cui il processo scrive i risultati deve essere in grado di gestire i casi in cui viene passato lo stesso risultato più volte. Il sink deve essere in grado di rilevare tali risultati duplicati e ignorarli. Un sink *idempotente* può essere chiamato più volte con gli stessi dati senza alcuna modifica dello stato.

È possibile creare sink idempotenti implementando la logica che controlla innanzitutto l'esistenza del risultato in ingresso nell'archivio dati. Se il risultato esiste già, la scrittura deve risultare riuscita dal punto di vista del processo di Spark, mentre in realtà l'archivio dati ha ignorato i dati duplicati. Se il risultato non esiste, il sink deve inserire questo nuovo risultato nell'archivio. 

Si potrebbe ad esempio usare una stored procedure con il database SQL di Azure che inserisce gli eventi in una tabella. Questa stored procedure cerca prima di tutto l'evento in base ai campi chiave e, solo se non viene trovato alcun evento corrispondente, il record viene inserito nella tabella.

Un altro esempio consiste nell'usare un file system partizionato, come BLOB del servizio di archiviazione di Azure e Azure Data Lake Storage. In questo caso, non è necessario che la logica del sink controlli l'esistenza di un file. Se il file che rappresenta l'evento esiste, viene semplicemente sovrascritto con gli stessi dati. In caso contrario, viene creato un nuovo file nel percorso calcolato.

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica di Apache Spark Streaming](apache-spark-streaming-overview.md)
* [Creare processi di Apache Spark Streaming a disponibilità elevata in Apache Hadoop YARN](apache-spark-streaming-high-availability.md)
