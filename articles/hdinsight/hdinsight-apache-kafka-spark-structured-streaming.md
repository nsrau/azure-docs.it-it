---
title: 'Esercitazione: streaming strutturato di Apache Spark con Apache Kafka - Azure HDInsight'
description: Informazioni su come usare lo streaming Apache Spark per ottenere o trasmettere dati da o verso Apache Kafka. In questa esercitazione, si esegue lo streaming dei dati usando Jupyter Notebook da Spark in HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,seodec18
ms.topic: tutorial
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: 4ac341d780a3c348f9ba9f8fd0241c351bd5fdc5
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53162434"
---
# <a name="tutorial-use-apache-spark-structured-streaming-with-apache-kafka-on-hdinsight"></a>Esercitazione: usare lo streaming strutturato di Apache Spark con Apache Kafka in HDInsight

Questa esercitazione illustra come usare lo [streaming strutturato di Apache Spark](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html) per leggere e scrivere dati con [Apache Kafka](https://kafka.apache.org/) in Azure HDInsight.

Lo streaming strutturato Spark è un motore di elaborazione del flusso basato su Spark SQL. Consente di esprimere i calcoli di streaming come il calcolo di batch in dati statici. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Streaming strutturato con Kafka
> * Creare cluster Kafka e Spark
> * Caricare il notebook in Spark
> * Usare il notebook
> * Pulire le risorse

Al termine della procedura descritta in questo documento, eliminare i cluster per evitare costi supplementari.

## <a name="prerequisites"></a>Prerequisiti

* Familiarità nell'uso di [Jupyter Notebook](https://jupyter.org/) con Spark in HDInsight. Per altre informazioni, vedere il documento [Esercitazione: Caricare i dati ed eseguire query in un cluster Apache Spark in Azure HDInsight](spark/apache-spark-load-data-run-query.md).

* Familiarità con il linguaggio di programmazione [Scala](https://www.scala-lang.org/). Il codice usato in questa esercitazione è scritto in Scala.

* Familiarità con la creazione di argomenti Kafka. Per altre informazioni, vedere il documento [Creare un cluster Apache Kafka in HDInsight](kafka/apache-kafka-get-started.md).

> [!IMPORTANT]
> La procedura descritta in questo documento richiede che venga creato un gruppo di risorse di Azure che contenga sia un cluster Spark in HDInsight che un cluster Kafka in HDInsight. Entrambi questi cluster si trovano all'interno di una rete virtuale di Azure, che consente al cluster Spark di comunicare direttamente con il cluster Kafka.
> 
> Per comodità, questo documento si collega a un modello in grado di creare tutte le risorse di Azure necessarie. 
>
> Per altre informazioni sull'uso di HDInsight in una rete virtuale, vedere il documento [Estendere Azure HDInsight usando Rete virtuale di Azure](hdinsight-extend-hadoop-virtual-network.md).

## <a name="structured-streaming-with-apache-kafka"></a>Streaming strutturato con Apache Kafka

Lo streaming strutturato Spark è un motore di elaborazione del flusso basato sul motore SQL di Spark. Quando si usa lo streaming strutturato, è possibile scrivere query di streaming nello stesso modo in cui si scrivono le query batch.

I frammenti di codice seguenti illustrano la lettura da Kafka e l'archiviazione in file. La prima è un'operazione batch, mentre la seconda è un'operazione di flusso:

```scala
// Read a batch from Kafka
val kafkaDF = spark.read.format("kafka")
                .option("kafka.bootstrap.servers", kafkaBrokers)
                .option("subscribe", kafkaTopic)
                .option("startingOffsets", "earliest")
                .load()
// Select data and write to file
kafkaDF.select(from_json(col("value").cast("string"), schema) as "trip")
                .write
                .format("parquet")
                .option("path","/example/batchtripdata")
                .option("checkpointLocation", "/batchcheckpoint")
                .save()
```

```scala
// Stream from Kafka
val kafkaStreamDF = spark.readStream.format("kafka")
                .option("kafka.bootstrap.servers", kafkaBrokers)
                .option("subscribe", kafkaTopic)
                .option("startingOffsets", "earliest")
                .load()
// Select data from the stream and write to file
kafkaStreamDF.select(from_json(col("value").cast("string"), schema) as "trip")
                .writeStream
                .format("parquet")
                .option("path","/example/streamingtripdata")
                .option("checkpointLocation", "/streamcheckpoint")
                .start.awaitTermination(30000)
```

In entrambi i frammenti di codice, i dati vengono letti da Kafka e scritti nel file. Le differenze tra gli esempi sono:

| Batch | Streaming |
| --- | --- |
| `read` | `readStream` |
| `write` | `writeStream` |
| `save` | `start` |

L'operazione di streaming usa anche `awaitTermination(30000)`, che arresta il flusso dopo 30000 ms. 

Per usare lo streaming strutturato con Kafka, il progetto deve avere una dipendenza sul pacchetto `org.apache.spark : spark-sql-kafka-0-10_2.11`. La versione di questo pacchetto deve corrispondere alla versione di Spark in HDInsight. Per Spark 2.2.0 (disponibile in HDInsight 3.6), è possibile trovare le informazioni sulle dipendenze per diversi tipi di progetto in [https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar](https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar).

Per Jupyter Notebook incluso con questa esercitazione, la cella seguente carica questa dipendenza dal pacchetto:

```
%%configure -f
{
    "conf": {
        "spark.jars.packages": "org.apache.spark:spark-sql-kafka-0-10_2.11:2.2.0",
        "spark.jars.excludes": "org.scala-lang:scala-reflect,org.apache.spark:spark-tags_2.11"
    }
}
```

## <a name="create-the-clusters"></a>Creare i cluster

Apache Kafka in HDInsight non fornisce l'accesso ai broker Kafka tramite Internet pubblico. Qualunque elemento che usa Kafka deve trovarsi nella stessa rete virtuale di Azure. In questa esercitazione i cluster Kafka e Spark si trovano nella stessa rete virtuale di Azure. 

Il diagramma seguente illustra il flusso delle comunicazioni tra Spark e Kafka:

![Diagramma dei cluster Spark e Kafka in una rete virtuale di Azure](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> Il servizio Kafka è limitato alle comunicazioni all'interno della rete virtuale. Altri servizi nel cluster, ad esempio SSH e Ambari, sono accessibili tramite Internet. Per altre informazioni sulle porte pubbliche disponibili con HDInsight, vedere [Porte e URI usati da HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Per creare una Rete virtuale di Microsoft Azure e quindi crearvi i cluster Kafka e Spark, seguire questa procedura:

1. Usare il pulsante seguente per accedere ad Azure e aprire il modello nel portale di Azure.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-kafka-structured-streaming%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Il modello di Azure Resource Manager è disponibile in **https://raw.githubusercontent.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming/master/azuredeploy.json**.

    Questo modello crea le risorse seguenti:

    * Kafka nel cluster HDInsight 3.6.
    * Spark 2.2.0 nel cluster HDInsight 3.6.
    * Una rete virtuale Azure contenente i cluster HDInsight.

    > [!IMPORTANT]
    > Il notebook di streaming strutturato che è stato usato in questa esercitazione richiede Spark 2.2.0 in HDInsight 3.6. Se si usa una versione precedente di Spark in HDInsight, si ricevono errori durante l'uso del notebook.

2. Usare le informazioni seguenti per popolare le voci nella sezione **Modello personalizzato**:

    | Impostazione | Valore |
    | --- | --- |
    | Sottoscrizione | Sottoscrizione di Azure |
    | Gruppo di risorse | Gruppo di risorse che contiene le risorse. |
    | Località | Area di Azure in cui vengono create le risorse. |
    | Nome del cluster Spark | Nome del cluster Spark. I primi sei caratteri devono essere diversi dal nome di cluster Kafka. |
    | Nome del cluster Kafka | Nome del cluster Kafka. I primi sei caratteri devono essere diversi dal nome di cluster Spark. |
    | Nome utente dell'account di accesso del cluster | Nome utente dell'amministratore per i cluster. |
    | Password di accesso al cluster | Password dell'utente amministratore per i cluster. |
    | Nome utente SSH | Utente SSH da creare per i cluster. |
    | Password SSH | Password per l'utente SSH. |
   
    ![Screenshot del modello personalizzato](./media/hdinsight-apache-kafka-spark-structured-streaming/spark-kafka-template.png)

3. Leggere le **Condizioni** e quindi selezionare **Accetto le condizioni riportate sopra**.

4. Selezionare infine **Aggiungi al dashboard** e quindi **Acquista**. 

> [!NOTE]
> La creazione dei cluster può richiedere fino a 20 minuti.

## <a name="upload-the-notebook"></a>Caricare il notebook

Per caricare il notebook dal progetto nel cluster Spark su HDInsight, seguire questa procedura:

1. Scaricare il progetto da [https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming](https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming).

1. Nel Web browser connettersi al notebook Jupyter nel cluster Spark. Nell'URL seguente sostituire `CLUSTERNAME` con il nome del cluster __Spark__:

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    Quando richiesto, immettere l'account di accesso (amministratore) e la password usati durante la creazione del cluster.

2. Usare il pulsante __Carica__ in alto a destra nella pagina per caricare il file __spark-structured-streaming-kafka.ipynb__ nel cluster. Selezionare __Apri__ per avviare il caricamento.

    ![Usare il pulsante di caricamento per selezionare e caricare un notebook](./media/hdinsight-apache-kafka-spark-structured-streaming/upload-button.png)

    ![Selezionare il file KafkaStreaming.ipynb](./media/hdinsight-apache-kafka-spark-structured-streaming/select-notebook.png)

3. Trovare la voce __spark-structured-streaming-kafka.ipynb__ nell'elenco dei notebook e selezionare il pulsante __Carica__ accanto.

    ![Per caricare Notebook, usare il pulsante di caricamento accanto alla voce KafkaStreaming.ipynb](./media/hdinsight-apache-kafka-spark-structured-streaming/upload-notebook.png)


## <a name="use-the-notebook"></a>Usare il notebook

Dopo aver caricato i file, selezionare la voce __spark-structured-streaming-kafka.ipynb__ per aprire il notebook. Per informazioni sull'uso dello streaming strutturato di Spark con Kafka su HDInsight, seguire le istruzioni disponibili nel notebook.

## <a name="clean-up-resources"></a>Pulire le risorse

Per pulire le risorse create da questa esercitazione, eliminare il gruppo di risorse. Se si elimina il gruppo di risorse, vengono eliminati anche il cluster HDInsight associato e tutte le altre risorse correlate al gruppo di risorse.

Per rimuovere il gruppo di risorse usando il portale di Azure:

1. Nel portale di Azure espandere il menu a sinistra per aprire il menu dei servizi e quindi scegliere __Gruppi di risorse__ per visualizzare l'elenco dei gruppi di risorse.
2. Individuare il gruppo di risorse da eliminare e quindi fare clic con il pulsante destro del mouse su __Altro__ (...) a destra dell'elenco.
3. Scegliere __Elimina gruppo di risorse__ e quindi confermare.

> [!WARNING]
> La fatturazione del cluster HDInsight inizia dopo la creazione del cluster e si interrompe solo quando questo viene eliminato. La fatturazione avviene con tariffa oraria, perciò si deve sempre eliminare il cluster in uso quando non lo si usa più.
> 
> Se si elimina un cluster Kafka su HDInsight vengono eliminati anche eventuali dati archiviati in Kafka.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato descritto come usare lo [streaming strutturato di Apache Spark](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html) per scrivere e leggere i dati da [Apache Kafka](https://kafka.apache.org/) in HDInsight. Usare il collegamento seguente per informazioni su come usare [Apache Storm](https://storm.apache.org/) con Kafka.

> [!div class="nextstepaction"]
> [Usare Apache Storm con Apache Kafka](hdinsight-apache-storm-with-kafka.md)
