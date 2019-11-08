---
title: 'Esercitazione: Dati di Hub eventi di Azure & Apache Spark - HDInsight'
description: Esercitazione - Connettere Apache Spark in Azure HDInsight a Hub eventi di Azure ed elaborare i dati di streaming.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 10/17/2019
ms.openlocfilehash: 0b24d1b0215564fb9f6063d4a2d091bb7a9a1c3e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494625"
---
# <a name="tutorial-process-tweets-using-azure-event-hubs-and-apache-spark-in-hdinsight"></a>Esercitazione: Elaborare i tweet usando Hub eventi di Azure e Apache Spark in HDInsight

In questa esercitazione si apprenderà come creare un'applicazione di streaming [Apache Spark](https://spark.apache.org/) per inviare tweet a un hub eventi di Azure e creare un'altra applicazione per leggere i tweet dall'hub eventi. Per una spiegazione dettagliata dello streaming Spark, vedere [Apache Spark streaming overview](https://spark.apache.org/docs/latest/streaming-programming-guide.html#overview) (Panoramica dello streaming in Apache Spark). HDInsight offre le stesse funzioni di streaming a un cluster Spark in Azure.

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Inviare messaggi a Hub eventi di Azure
> * Leggere messaggi da Hub eventi di Azure

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* Un cluster Apache Spark in HDInsight. Vedere [Creare un cluster Apache Spark](./apache-spark-jupyter-spark-sql-use-portal.md).

* Familiarità nell'uso di Jupyter Notebook con Spark in HDInsight. Per altre informazioni, vedere l'articolo su come [caricare i dati ed eseguire query con Apache Spark in HDInsight](./apache-spark-load-data-run-query.md).

* Un [account Twitter](https://twitter.com/i/flow/signup) e familiarità con Twitter.

## <a name="create-a-twitter-application"></a>Creare un'applicazione Twitter

Per ricevere un flusso di tweet, si crea un'applicazione in Twitter. Seguire le istruzioni per creare un'applicazione Twitter e annotare i valori necessari per completare questa esercitazione.

1. Passare alla [gestione delle applicazioni Twitter](https://apps.twitter.com/).

1. Selezionare **Create an app** (Crea un'app).

1. Specificare i valori obbligatori seguenti:

    |Proprietà |Valore |
    |---|---|
    |Nome app|Specificare il nome dell'applicazione. Il valore usato per questa esercitazione è **HDISparkStreamApp0423**. Questo nome deve essere un nome univoco.|
    |Descrizione applicazione|Fornire una breve descrizione dell'applicazione. Il valore usato per questa esercitazione è **A simple HDInsight Spark streaming application** (Un'applicazione di streaming HDInsight Spark semplice).|
    |URL del sito Web|Specificare il sito Web dell'applicazione. Non è necessario inserire un sito Web valido.  Il valore usato per questa esercitazione è `http://www.contoso.com`.|
    |Indicare come verrà usata questa app|Solo a scopo di test. Creazione di un'applicazione di streaming Apache Spark per inviare tweet a un hub eventi di Azure.|

1. Selezionare **Create** (Crea).

1. Dalla finestra popup **Review our Developer Terms** (Verifica le condizioni per gli sviluppatori) selezionare **Create** (Crea).

1. Selezionare la scheda **Keys an tokens** (Chiavi e token).

1. In **Access token & access token secret** (Token di accesso e segreto del token di accesso) selezionare **Create** (Crea).

1. Prendere nota dei quattro valori seguenti visualizzati nella pagina per poterli usare in un secondo momento:

    - **Chiave utente (chiave API)**
    - **Segreto utente (chiave segreto API)**
    - **Token di accesso**
    - **Segreto token di accesso**

## <a name="create-an-azure-event-hubs-namespace"></a>Creare uno spazio dei nomi di Hub eventi di Azure

L'hub eventi consente di archiviare tweet.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Nel menu a sinistra passare a **Tutti i servizi** > **Internet of Things** > **Hub eventi**.  

    ![Creare un hub eventi per l'esempio di streaming Spark](./media/apache-spark-eventhub-streaming/hdinsight-create-event-hub-for-spark-streaming.png "Creare un hub eventi per l'esempio di streaming Spark")

1. Selezionare **+ Aggiungi**.

1. Immettere i valori seguenti per il nuovo spazio dei nomi dell'hub eventi:

    |Proprietà |Valore |
    |---|---|
    |NOME|immettere un nome per l'hub eventi.  Il valore usato per questa esercitazione è **myeventhubns20180403**.|
    |Piano tariffario|Selezionare **Standard**.|
    |Subscription|Selezionare la sottoscrizione appropriata.|
    |Resource group|selezionare un gruppo di risorse esistente dall'elenco a discesa oppure selezionare **Crea nuovo** per creare un nuovo gruppo di risorse.|
    |Location|selezionare la stessa **Località** del cluster Apache Spark in HDInsight per ridurre i costi e la latenza.|
    |Abilita aumento automatico (facoltativo) |La funzionalità di aumento automatico ridimensiona automaticamente il numero di unità elaborate assegnato allo spazio dei nomi dell'hub eventi quando il traffico supera la capacità di unità elaborate assegnata allo spazio dei nomi stesso.  |
    |Numero massimo di unità elaborate per l'aumento automatico (facoltativo)|Questo dispositivo di scorrimento viene visualizzato solo se l'opzione **Abilita aumento automatico** è selezionata.  |

    ![Specificare il nome di un hub eventi per l'esempio di streaming Spark](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-name-for-spark-streaming.png "Specificare il nome di un hub eventi per l'esempio di streaming Spark")

1. Selezionare **Crea** per creare lo spazio dei nomi.  La distribuzione verrà completata in pochi minuti.

## <a name="create-an-azure-event-hub"></a>Creare un hub eventi di Azure

Creare un hub eventi dopo la distribuzione dello spazio dei nomi dell'hub eventi.  Dal portale:

1. Nel menu a sinistra passare a **Tutti i servizi** > **Internet of Things** > **Hub eventi**.

1. Selezionare lo spazio dei nomi degli hub eventi dall'elenco.

1. Nella pagina **Spazio dei nomi degli hub eventi** selezionare **+ Hub eventi**.  

1. Nella pagina **Crea hub eventi** immettere i valori seguenti:

    - **Nome**: assegnare un nome all'hub eventi.

    - **Conteggio partizioni**: 10.  

    - **Conservazione messaggi**: 1.

      ![Specificare i dettagli dell'hub eventi per l'esempio di streaming Spark](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-details-for-spark-streaming-example.png "Specificare i dettagli dell'hub eventi per l'esempio di streaming Spark")

1. Selezionare **Create** (Crea).  La distribuzione verrà completata in pochi secondi e si tornerà alla pagina Spazio dei nomi degli hub eventi.

1. In **Impostazioni** selezionare **Criteri di accesso condivisi**.

1. Selezionare **RootManageSharedAccessKey**.

     ![Impostare i criteri dell'hub eventi per l'esempio di streaming Spark](./media/apache-spark-eventhub-streaming/hdinsight-set-event-hub-policies-for-spark-streaming-example.png "Impostare i criteri dell'hub eventi per l'esempio di streaming Spark")

1. Salvare i valori di **Chiave primaria** e **Chiave primaria stringa di connessione** da usare in un secondo momento nell'esercitazione.

     ![Visualizzare le chiavi dei criteri dell'hub eventi per l'esempio di streaming Spark](./media/apache-spark-eventhub-streaming/hdinsight-view-event-hub-policy-keys.png "Visualizzare le chiavi dei criteri dell'hub eventi per l'esempio di streaming Spark")

## <a name="send-tweets-to-the-event-hub"></a>Inviare tweet all'hub eventi

1. Passare a `https://CLUSTERNAME.azurehdinsight.net/jupyter` dove `CLUSTERNAME` è il nome del cluster di Apache Spark. Creare un notebook Jupyter e denominarlo **SendTweetsToEventHub**.

1. Eseguire il codice seguente per aggiungere le librerie Apache Maven esterne:

    ```
    %%configure
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.13,org.twitter4j:twitter4j-core:4.0.6"}}
    ```

1. Modificare il codice seguente sostituendo `<Event hub name>`, `<Event hub namespace connection string>`, `<CONSUMER KEY>`, `<CONSUMER SECRET>`, `<ACCESS TOKEN>` e `<TOKEN SECRET>` con i valori appropriati. Eseguire il codice modificato per inviare tweet all'hub eventi:

    ```scala
    import java.util._
    import scala.collection.JavaConverters._
    import java.util.concurrent._

    import org.apache.spark._
    import org.apache.spark.streaming._
    import org.apache.spark.eventhubs.ConnectionStringBuilder

    // Event hub configurations
    // Replace values below with yours
    val eventHubName = "<Event hub name>"
    val eventHubNSConnStr = "<Event hub namespace connection string>"
    val connStr = ConnectionStringBuilder(eventHubNSConnStr).setEventHubName(eventHubName).build

    import com.microsoft.azure.eventhubs._
    val pool = Executors.newFixedThreadPool(1)
    val eventHubClient = EventHubClient.create(connStr.toString(), pool)

    def sendEvent(message: String) = {
          val messageData = EventData.create(message.getBytes("UTF-8"))
          eventHubClient.get().send(messageData)
          println("Sent event: " + message + "\n")
    }

    import twitter4j._
    import twitter4j.TwitterFactory
    import twitter4j.Twitter
    import twitter4j.conf.ConfigurationBuilder

    // Twitter application configurations
    // Replace values below with yours
    val twitterConsumerKey = "<CONSUMER KEY>"
    val twitterConsumerSecret = "<CONSUMER SECRET>"
    val twitterOauthAccessToken = "<ACCESS TOKEN>"
    val twitterOauthTokenSecret = "<TOKEN SECRET>"

    val cb = new ConfigurationBuilder()
    cb.setDebugEnabled(true).setOAuthConsumerKey(twitterConsumerKey).setOAuthConsumerSecret(twitterConsumerSecret).setOAuthAccessToken(twitterOauthAccessToken).setOAuthAccessTokenSecret(twitterOauthTokenSecret)

    val twitterFactory = new TwitterFactory(cb.build())
    val twitter = twitterFactory.getInstance()

    // Getting tweets with keyword "Azure" and sending them to the Event Hub in realtime!

    val query = new Query(" #Azure ")
    query.setCount(100)
    query.lang("en")
    var finished = false
    while (!finished) {
      val result = twitter.search(query)
      val statuses = result.getTweets()
      var lowestStatusId = Long.MaxValue
      for (status <- statuses.asScala) {
        if(!status.isRetweet()){
          sendEvent(status.getText())
        }
        lowestStatusId = Math.min(status.getId(), lowestStatusId)
        Thread.sleep(2000)
      }
      query.setMaxId(lowestStatusId - 1)
    }

    // Closing connection to the Event Hub
    eventHubClient.get().close()
    ```

1. Aprire l'hub eventi nel portale di Azure.  In **Panoramica** verranno visualizzati alcuni grafici che mostrano i messaggi inviati all'hub eventi.

## <a name="read-tweets-from-the-event-hub"></a>Leggere tweet dall'hub eventi

Creare un altro notebook Jupyter e denominarlo **ReadTweetsFromEventHub**.

1. Eseguire il codice seguente per aggiungere una libreria Apache Maven esterna:

    ```
    %%configure -f
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.13"}}
    ```

2. Modificare il codice seguente sostituendo `<Event hub name>` e `<Event hub namespace connection string>` con i valori appropriati. Eseguire il codice modificato per leggere tweet dall'hub eventi:

    ```scala
    import org.apache.spark.eventhubs._
    // Event hub configurations
    // Replace values below with yours
    val eventHubName = "<Event hub name>"
    val eventHubNSConnStr = "<Event hub namespace connection string>"
    val connStr = ConnectionStringBuilder(eventHubNSConnStr).setEventHubName(eventHubName).build

    val customEventhubParameters = EventHubsConf(connStr).setMaxEventsPerTrigger(5)
    val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()
    //incomingStream.printSchema

    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._

    // Event Hub message format is JSON and contains "body" field
    // Body is binary, so you cast it to string to see the actual content of the message
    val messages = incomingStream.withColumn("Offset", $"offset".cast(LongType)).withColumn("Time (readable)", $"enqueuedTime".cast(TimestampType)).withColumn("Timestamp", $"enqueuedTime".cast(LongType)).withColumn("Body", $"body".cast(StringType)).select("Offset", "Time (readable)", "Timestamp", "Body")

    messages.printSchema

    messages.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()
    ```

## <a name="clean-up-resources"></a>Pulire le risorse

Con HDInsight i dati vengono salvati in Archiviazione di Azure o in Azure Data Lake Storage. È quindi possibile eliminare senza problemi un cluster quando non viene usato. Vengono addebitati i costi anche per i cluster HDInsight che non sono in uso. Se si prevede di passare immediatamente all'esercitazione successiva, è consigliabile mantenere il cluster, altrimenti eliminarlo.

Aprire il cluster nel portale di Azure e selezionare **Elimina**.

![Eliminazione del cluster HDInsight nel portale di Azure](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "Eliminare un cluster HDInsight")

È anche possibile selezionare il nome del gruppo di risorse per aprire la pagina del gruppo di risorse e quindi selezionare **Elimina gruppo di risorse**. Eliminando il gruppo di risorse, si elimina sia il cluster HDInsight Spark che l'account di archiviazione predefinito.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come creare un'applicazione di streaming Apache Spark per inviare tweet a un hub eventi di Azure e creare un'altra applicazione per leggere i tweet dall'hub eventi.  Passare al prossimo articolo per vedere che è possibile creare un'applicazione di apprendimento automatico.

> [!div class="nextstepaction"]
> [Creare un'applicazione di apprendimento automatico](./apache-spark-ipython-notebook-machine-learning.md)
