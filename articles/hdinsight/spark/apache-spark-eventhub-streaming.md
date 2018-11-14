---
title: 'Esercitazione: Elaborare i dati da Hub eventi di Azure con Apache Spark in Azure HDInsight '
description: Connettere Apache Spark in Azure HDInsight a Hub eventi di Azure ed elaborare i dati di streaming.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive,mvc
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: eb2145d2e7b787bafa0b546449282454f7059999
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2018
ms.locfileid: "51283411"
---
# <a name="tutorial-process-tweets-using-azure-event-hubs-and-spark-in-hdinsight"></a>Esercitazione: Elaborare i tweet con Hub eventi di Azure e Spark in HDInsight

In questa esercitazione si apprenderà come creare un'applicazione di streaming Apache Spark per inviare tweet a un hub eventi di Azure e creare un'altra applicazione per leggere i tweet dall'hub eventi. Per una spiegazione dettagliata dello streaming Spark, vedere [Apache Spark streaming overview](http://spark.apache.org/docs/latest/streaming-programming-guide.html#overview) (Panoramica dello streaming in Apache Spark). HDInsight offre le stesse funzioni di streaming a un cluster Spark in Azure.

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Inviare messaggi a Hub eventi di Azure
> * Leggere messaggi da Hub eventi di Azure

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* **Completare l'articolo [Esercitazione: Caricare i dati ed eseguire query in un cluster Apache Spark in Azure HDInsight](./apache-spark-load-data-run-query.md)**.

## <a name="create-a-twitter-application"></a>Creare un'applicazione Twitter

Per ricevere un flusso di tweet, si crea un'applicazione in Twitter. Seguire le istruzioni per creare un'applicazione Twitter e annotare i valori necessari per completare questa esercitazione.

1. Passare alla [gestione delle applicazioni Twitter](https://apps.twitter.com/).
2. Selezionare **Crea nuova app**.
3. Specificare i valori seguenti:

    - Nome: indicare il nome dell'applicazione. Il valore usato per questa esercitazione è **HDISparkStreamApp0423**. Questo nome deve essere un nome univoco.
    - Descrizione: fornire una breve descrizione dell'applicazione. Il valore usato per questa esercitazione è **A simple HDInsight Spark streaming application** (Un'applicazione di streaming HDInsight Spark semplice).
    - Sito Web: indicare il sito Web dell'applicazione. Non è necessario inserire un sito Web valido.  Il valore usato per questa esercitazione è **http://www.contoso.com**.
    - URL callback: è possibile lasciarlo vuoto.

4. Selezionare **Sì, ho letto e accetto il contratto per gli sviluppatori di Twitter**, quindi selezionare **Creare un'applicazione Twitter**.
5. Selezionare la scheda **Keys and Access Tokens** .
6. Selezionare **Crea token di accesso** alla fine della pagina.
7. Annotare i valori seguenti dalla pagina.  Sarà necessario usare questi valori più avanti nell'esercitazione:

    - **Chiave utente di Twitter (chiave API)**    
    - **Segreto utente di Twitter (segreto API)**  
    - **Token di accesso**
    - **Segreto token di accesso**   

## <a name="create-an-azure-event-hub"></a>Creare un Hub di eventi di Azure

L'hub eventi consente di archiviare tweet.

1. Accedere al [portale di Azure](https://ms.portal.azure.com).
2. Selezionare **Crea una risorsa** nella parte superiore sinistra dello schermo.
3. Selezionare **Internet delle cose**, quindi **Hub eventi**.

    ![Creare un hub eventi per l'esempio di streaming Spark](./media/apache-spark-eventhub-streaming/hdinsight-create-event-hub-for-spark-streaming.png "Creare un hub eventi per l'esempio di streaming Spark")
4. Immettere i valori seguenti per il nuovo spazio dei nomi dell'hub eventi:

    - **Nome**: Inserire un nome per l'hob eventi.  Il valore usato per questa esercitazione è **myeventhubns20180403**.
    - **Piano tariffario**: selezionare **Standard**.
    - **Gruppo di risorse**: è possibile selezionare il gruppo di risorse per il cluster Spark oppure crearne uno nuovo. 
    - **Percorso**: è necessario selezionare lo stesso **Percorso** del cluster Apache Spark in HDInsight per ridurre i costi e la latenza.

    ![Fornire un nome di hub eventi per l'esempio di streaming Spark](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-name-for-spark-streaming.png "Fornire un nome di hub eventi per l'esempio di streaming Spark")
5. Selezionare **Crea** per creare lo spazio dei nomi.

7. Aprire lo spazio dei nomi dell'hub eventi tramite le istruzioni seguenti:

    1. Nel portale selezionare **Tutti i servizi**.
    2. Nella casella del filtro immettere **hub eventi**.
    3. Selezionare lo spazio dei nomi appena creato.
    4. Selezionare **+ Hub eventi**.

8. Immettere i valori seguenti:

    - Nome: assegnare un nome all'hub eventi.
    - Conteggio partizioni: 10
    - Conservazione dei messaggi: 1. 
   
    ![Fornire i dettagli dell'hub eventi per l'esempio di streaming Spark](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-details-for-spark-streaming-example.png "Fornire i dettagli dell'hub eventi per l'esempio di streaming Spark")

9. Selezionare **Create**.
10. Selezionare **Criteri di accesso condiviso** per lo spazio dei nomi (non sono i criteri di accesso condiviso dell'hub eventi), quindi selezionare **RootManageSharedAccessKey**.
    
     ![Impostare i criteri dell'hub eventi per l'esempio di streaming Spark](./media/apache-spark-eventhub-streaming/hdinsight-set-event-hub-policies-for-spark-streaming-example.png "Impostare i criteri dell'hub eventi per l'esempio di streaming Spark")

11. Salvare i valori di **Chiave primaria** e **Chiave primaria stringa di connessione** da usare in un secondo momento nell'esercitazione.

     ![Visualizzare le chiavi dei criteri dell'hub eventi per l'esempio di streaming Spark](./media/apache-spark-eventhub-streaming/hdinsight-view-event-hub-policy-keys.png "Visualizzare le chiavi dei criteri dell'hub eventi per l'esempio di streaming Spark")


## <a name="send-tweets-to-the-event-hub"></a>Inviare tweet all'hub eventi

È necessario creare un notebook Jupyter e denominarlo **SendTweetsToEventHub**. 

1. Eseguire il codice seguente per aggiungere le librerie Maven esterne:

    ```
    %%configure
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.2.0,org.twitter4j:twitter4j-core:4.0.6"}}
    ```

2. Eseguire il codice seguente per inviare tweet all'hub eventi:

    ```
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

3. Aprire l'hub eventi nel portale di Azure.  In **Panoramica** verranno visualizzati alcuni grafici che mostrano i messaggi inviati all'hub eventi.

## <a name="read-tweets-from-the-event-hub"></a>Leggere tweet dall'hub eventi

È necessario creare un altro notebook Jupyter e denominarlo **ReadTweetsFromEventHub**. 

1. Eseguire il codice seguente per aggiungere una libreria Maven esterna:

    ```
    %%configure -f
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.2.0"}}
    ```
2. Eseguire il codice seguente per leggere tweet dall'hub eventi:

    ```
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

Con HDInsight i dati vengono salvati in Archiviazione di Azure o in Azure Data Lake Store in modo che sia possibile eliminare tranquillamente un cluster quando non viene usato. Vengono addebitati i costi anche per i cluster HDInsight che non sono in uso. Se si prevede di lavorare immediatamente nell'esercitazione successiva, si potrebbe voler mantenere il cluster, altrimenti procedere ed eliminare il cluster.

Aprire il cluster nel portale di Azure e selezionare **Elimina**.

![Eliminare cluster HDInsight](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "Eliminare cluster HDInsight")

È anche possibile selezionare il nome del gruppo di risorse per aprire la pagina del gruppo di risorse e quindi selezionare **Elimina gruppo di risorse**. Eliminando il gruppo di risorse, si elimina sia il cluster HDInsight Spark che l'account di archiviazione predefinito.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

* Leggere messaggi da un hub eventi.
Passare al prossimo articolo per vedere che è possibile creare un'applicazione di apprendimento automatico. 

> [!div class="nextstepaction"]
> [Creare un'applicazione di apprendimento automatico](./apache-spark-ipython-notebook-machine-learning.md)


