---
title: 'Esercitazione: Elaborare i dati da Hub eventi di Azure con Apache Spark in Azure HDInsight '
description: Connettere Apache Spark in Azure HDInsight a Hub eventi di Azure ed elaborare i dati di streaming.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 05/24/2019
ms.openlocfilehash: bf6a728f66f5eeaf5983ecd50d65326a59cc3267
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67066205"
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

* Un [account Twitter](https://twitter.com/i/flow/signup).

## <a name="create-a-twitter-application"></a>Creare un'applicazione Twitter

Per ricevere un flusso di tweet, si crea un'applicazione in Twitter. Seguire le istruzioni per creare un'applicazione Twitter e annotare i valori necessari per completare questa esercitazione.

1. Passare alla [gestione delle applicazioni Twitter](https://apps.twitter.com/).

1. Selezionare **Crea nuova app**.

1. Specificare i valori seguenti:

    |Proprietà |Valore |
    |---|---|
    |NOME|Specificare il nome dell'applicazione. Il valore usato per questa esercitazione è **HDISparkStreamApp0423**. Questo nome deve essere un nome univoco.|
    |DESCRIZIONE|Fornire una breve descrizione dell'applicazione. Il valore usato per questa esercitazione è **A simple HDInsight Spark streaming application** (Un'applicazione di streaming HDInsight Spark semplice).|
    |Website|Specificare il sito Web dell'applicazione. Non è necessario inserire un sito Web valido.  Il valore usato per questa esercitazione è **http://www.contoso.com** .|
    |Callback URL (URL callback)|È possibile lasciarlo vuoto.|

1. Selezionare **Sì, ho letto e accetto il contratto per gli sviluppatori di Twitter**, quindi selezionare **Creare un'applicazione Twitter**.

1. Selezionare la scheda **Keys and Access Tokens** .

1. Selezionare **Crea token di accesso** alla fine della pagina.

1. Annotare i valori seguenti dalla pagina.  Sarà necessario usare questi valori più avanti nell'esercitazione:

    - **Chiave utente di Twitter (chiave API)**    
    - **Segreto utente di Twitter (segreto API)**  
    - **Token di accesso**
    - **Segreto token di accesso**   

## <a name="create-an-azure-event-hubs-namespace"></a>Creare uno spazio dei nomi di Hub eventi di Azure

L'hub eventi consente di archiviare tweet.

1. Accedere al [portale di Azure](https://portal.azure.com). 

2. Nel menu a sinistra selezionare **Tutti i servizi**.  

3. In **INTERNET DELLE COSE** selezionare **Hub eventi**. 

    ![Creare un hub eventi per l'esempio di streaming Spark](./media/apache-spark-eventhub-streaming/hdinsight-create-event-hub-for-spark-streaming.png "Creare un hub eventi per l'esempio di streaming Spark")

4. Selezionare **+ Aggiungi**.

5. Immettere i valori seguenti per il nuovo spazio dei nomi dell'hub eventi:

    |Proprietà |Valore |
    |---|---|
    |NOME|immettere un nome per l'hub eventi.  Il valore usato per questa esercitazione è **myeventhubns20180403**.|
    |Piano tariffario|Selezionare **Standard**.|
    |Sottoscrizione|Selezionare la sottoscrizione appropriata.|
    |Gruppo di risorse|selezionare un gruppo di risorse esistente dall'elenco a discesa oppure selezionare **Crea nuovo** per creare un nuovo gruppo di risorse.|
    |Località|selezionare la stessa **Località** del cluster Apache Spark in HDInsight per ridurre i costi e la latenza.|
    |Abilita aumento automatico (facoltativo) |La funzionalità di aumento automatico ridimensiona automaticamente il numero di unità elaborate assegnato allo spazio dei nomi dell'hub eventi quando il traffico supera la capacità di unità elaborate assegnata allo spazio dei nomi stesso.  |
    |Numero massimo di unità elaborate per l'aumento automatico (facoltativo)|Questo dispositivo di scorrimento viene visualizzato solo se l'opzione **Abilita aumento automatico** è selezionata.  |

    ![Fornire un nome di hub eventi per l'esempio di streaming Spark](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-name-for-spark-streaming.png "Fornire un nome di hub eventi per l'esempio di streaming Spark")

6. Selezionare **Crea** per creare lo spazio dei nomi.  La distribuzione verrà completata in pochi minuti.

## <a name="create-an-azure-event-hub"></a>Creare un hub eventi di Azure
Creare un hub eventi dopo la distribuzione dello spazio dei nomi dell'hub eventi.  Dal portale:

1. Nel menu a sinistra selezionare **Tutti i servizi**.  

1. In **INTERNET DELLE COSE** selezionare **Hub eventi**.  

1. Selezionare lo spazio dei nomi degli hub eventi dall'elenco.  

1. Nella pagina **Spazio dei nomi degli hub eventi** selezionare **+ Hub eventi**.  
1. Nella pagina **Crea hub eventi** immettere i valori seguenti:

    - **Nome**: assegnare un nome all'hub eventi. 
 
    - **Conteggio partizioni**: 10.  

    - **Conservazione messaggi**: 1.   
   
      ![Fornire i dettagli dell'hub eventi per l'esempio di streaming Spark](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-details-for-spark-streaming-example.png "Fornire i dettagli dell'hub eventi per l'esempio di streaming Spark")

1. Selezionare **Create** (Crea).  La distribuzione verrà completata in pochi secondi e si tornerà alla pagina Spazio dei nomi degli hub eventi.

1. In **Impostazioni** selezionare **Criteri di accesso condivisi**.

1. Selezionare **RootManageSharedAccessKey**.
    
     ![Impostare i criteri dell'hub eventi per l'esempio di streaming Spark](./media/apache-spark-eventhub-streaming/hdinsight-set-event-hub-policies-for-spark-streaming-example.png "Impostare i criteri dell'hub eventi per l'esempio di streaming Spark")

1. Salvare i valori di **Chiave primaria** e **Chiave primaria stringa di connessione** da usare in un secondo momento nell'esercitazione.

     ![Visualizzare le chiavi dei criteri dell'hub eventi per l'esempio di streaming Spark](./media/apache-spark-eventhub-streaming/hdinsight-view-event-hub-policy-keys.png "Visualizzare le chiavi dei criteri dell'hub eventi per l'esempio di streaming Spark")


## <a name="send-tweets-to-the-event-hub"></a>Inviare tweet all'hub eventi

Creare un notebook Jupyter e denominarlo **SendTweetsToEventHub**. 

1. Eseguire il codice seguente per aggiungere le librerie Apache Maven esterne:

    ```
    %%configure
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.2.0,org.twitter4j:twitter4j-core:4.0.6"}}
    ```

2. Modificare il codice seguente sostituendo `<Event hub name>`, `<Event hub namespace connection string>`, `<CONSUMER KEY>`, `<CONSUMER SECRET>`, `<ACCESS TOKEN>` e `<TOKEN SECRET>` con i valori appropriati. Eseguire il codice modificato per inviare tweet all'hub eventi:

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

3. Aprire l'hub eventi nel portale di Azure.  In **Panoramica** verranno visualizzati alcuni grafici che mostrano i messaggi inviati all'hub eventi.

## <a name="read-tweets-from-the-event-hub"></a>Leggere tweet dall'hub eventi

Creare un altro notebook Jupyter e denominarlo **ReadTweetsFromEventHub**. 

1. Eseguire il codice seguente per aggiungere una libreria Apache Maven esterna:

    ```
    %%configure -f
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.2.0"}}
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

![Eliminare cluster HDInsight](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "Eliminare cluster HDInsight")

È anche possibile selezionare il nome del gruppo di risorse per aprire la pagina del gruppo di risorse e quindi selezionare **Elimina gruppo di risorse**. Eliminando il gruppo di risorse, si elimina sia il cluster HDInsight Spark che l'account di archiviazione predefinito.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come creare un'applicazione di streaming Apache Spark per inviare tweet a un hub eventi di Azure e creare un'altra applicazione per leggere i tweet dall'hub eventi.  Passare al prossimo articolo per vedere che è possibile creare un'applicazione di apprendimento automatico.

> [!div class="nextstepaction"]
> [Creare un'applicazione di apprendimento automatico](./apache-spark-ipython-notebook-machine-learning.md)
