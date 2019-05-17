---
title: 'Esercitazione: Rilevamento anomalie sui dati in streaming con Azure Databricks'
description: Usare l'API rilevatore di anomalie e Azure Databricks per monitorare le anomalie nei dati.
titlesuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 05/08/2019
ms.author: aahi
ms.openlocfilehash: a00ad2523c215fa54d7d19d8c9e923b621f3081a
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65791695"
---
# <a name="tutorial-anomaly-detection-on-streaming-data-using-azure-databricks"></a>Esercitazione: Rilevamento anomalie sui dati in streaming con Azure Databricks

Microsoft Power BI Desktop è un'applicazione gratuita che consente di connettersi ai dati e di trasformarli e visualizzarli. L'API rilevatore di anomalie, parte di servizi cognitivi di Azure, offre un modo per il monitoraggio di dati delle serie temporali. Usare questa esercitazione per eseguire il rilevamento delle anomalie in un flusso di dati in tempo quasi reale con Azure Databricks. Si sarà inserire i dati di twitter usando hub eventi di Azure e importarli in Azure Databricks tramite il connettore di hub eventi per Spark. Successivamente, si userà l'API per rilevare le anomalie sui dati trasmessi. 

L'illustrazione seguente mostra il flusso dell'applicazione:

![Azure Databricks con Hub eventi e Servizi cognitivi](../media/tutorials/databricks-cognitive-services-tutorial.png "Azure Databricks con Hub eventi e Servizi cognitivi")

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Creare un'area di lavoro di Azure Databricks
> * Creare un cluster Spark in Azure Databricks
> * Creare un'app Twitter per accedere a dati in streaming
> * Creare notebook in Azure Databricks
> * Collegare librerie per Hub eventi e API Twitter
> * Creare una risorsa di rilevatore di anomalie e recuperare la chiave di accesso
> * Inviare tweet a Hub eventi
> * Leggere tweet da Hub eventi
> * Eseguire il rilevamento anomalie sui TWEET

> [!Note]
> Questa esercitazione introduce un approccio all'implementazione consigliata [architettura della soluzione](https://azure.microsoft.com/solutions/architecture/anomaly-detector-process/) per l'API rilevatore di anomalie.

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

> [!Note]
> Questa esercitazione non è possibile completare con una chiave di valutazione gratuita per l'API rilevatore di anomalie. Per usare un account gratuito per creare il cluster Azure Databricks, prima di creare il cluster, passare al profilo personale e impostare la sottoscrizione per il **pagamento in base al consumo**. Per altre informazioni, vedere [Account gratuito di Azure](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Prerequisiti

- Un' [dello spazio dei nomi di hub eventi di Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-create) e hub eventi.

- Il [stringa di connessione](../../../event-hubs/event-hubs-get-connection-string.md) per lo spazio dei nomi di hub eventi di accesso. La stringa di connessione deve avere un formato simile a:

    `Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<key name>;SharedAccessKey=<key value>`. 

- Il nome dei criteri di accesso condiviso e criteri di chiave per gli hub eventi.

Vedere l'hub eventi di Azure [Guida introduttiva](../../../event-hubs/event-hubs-create.md) per informazioni sulla creazione di un hub eventi e lo spazio dei nomi.

## <a name="create-an-azure-databricks-workspace"></a>Creare un'area di lavoro di Azure Databricks

In questa sezione si crea un'area di lavoro Azure Databricks usando il [portale di Azure](https://portal.azure.com/).

1. Nel portale di Azure selezionare **Crea una risorsa** > **Analisi** > **Azure Databricks**.

    ![Databricks nel portale di Azure](../media/tutorials/azure-databricks-on-portal.png "Databricks nel portale di Azure")

3. Nella pagina **Servizio Azure Databricks** specificare i valori per creare un'area di lavoro di Databricks:


    |Proprietà  |Descrizione  |
    |---------|---------|
    |**Nome area di lavoro**     | Specificare un nome per l'area di lavoro di Databricks        |
    |**Sottoscrizione**     | Selezionare la sottoscrizione di Azure nell'elenco a discesa.        |
    |**Gruppo di risorse**     | Specificare se si vuole creare un nuovo gruppo di risorse o usarne uno esistente. Un gruppo di risorse è un contenitore con risorse correlate per una soluzione Azure. Per altre informazioni, vedere [Panoramica di Gestione risorse di Microsoft Azure](../../../azure-resource-manager/resource-group-overview.md). |
    |**Posizione**     | Selezionare **Stati Uniti orientali 2** o una delle eventuali altre aree disponibili. Visualizzare [servizi di Azure disponibili per area](https://azure.microsoft.com/regions/services/) per aree di disponibilità.        |
    |**Piano tariffario**     |  Scegliere tra **Standard** e **Premium**. NON si sceglie **versione di valutazione**. Per altre informazioni su questi piani tariffari, vedere la [pagina dei prezzi di Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

    Selezionare **Create**.

4. La creazione dell'account richiede alcuni minuti, 

## <a name="create-a-spark-cluster-in-databricks"></a>Creare un cluster Spark in Databricks

1. Nel portale di Azure passare all'area di lavoro di Databricks creata e quindi selezionare **Avvia area di lavoro**.

2. Si verrà reindirizzati al portale di Azure Databricks. Dal portale, selezionare **nuovo Cluster**.

    ![Databricks in Azure](../media/tutorials/databricks-on-azure.png "Databricks in Azure")

3. Nel **nuovo Cluster** fornire i valori per creare un cluster.

    ![Creare il cluster Databricks Spark in Azure](../media/tutorials/create-databricks-spark-cluster.png "Creare il cluster Databricks Spark in Azure")

    Accettare tutti gli altri valori predefiniti tranne i seguenti:

   * Immettere un nome per il cluster.
   * Per questo articolo, creare un cluster con **5.2** runtime. NON si seleziona **5.3** runtime.
   * Assicurarsi che il **termina dopo \_ \_ minuti di inattività** casella di controllo è selezionata. Specificare una durata (in minuti) per terminare il cluster, se il cluster non sia in uso.

     Selezionare **Crea cluster**. Quando il cluster è in esecuzione, è possibile collegare blocchi appunti al cluster ed eseguire processi Spark.

## <a name="create-a-twitter-application"></a>Creare un'applicazione Twitter

Per ricevere un flusso di tweet, è necessario creare un'applicazione in Twitter. Seguire le istruzioni per creare un'applicazione Twitter e registrare i valori necessari per completare questa esercitazione.

1. In un Web browser passare alla [pagina di gestione delle applicazioni Twitter](https://apps.twitter.com/) e selezionare **Create New App** (Crea nuova app).

    ![Creare un'applicazione Twitter](../media/tutorials/databricks-create-twitter-app.png "Creare un'applicazione Twitter")

2. Nella pagina **Create an application** (Crea applicazione) specificare i dettagli per la nuova app e quindi selezionare **Create your Twitter application** (Crea applicazione Twitter).

    ![Dettagli dell'applicazione Twitter](../media/tutorials/databricks-provide-twitter-app-details.png "Dettagli dell'applicazione Twitter")

3. Nella pagina dell'applicazione selezionare **Keys and Access Tokens** (Chiavi e token di accesso) e copiare i valori di **Consumer Key** (Chiave utente) e **Consumer Secret** (Segreto utente). Selezionare anche **Create my access token** (Crea il mio token di accesso) per generare i token di accesso. Copiare i valori di **Access Token** (Token di accesso) e **Access Token Secret** (Segreto del token di accesso).

    ![Dettagli dell'applicazione Twitter](../media/tutorials/twitter-app-key-secret.png "Dettagli dell'applicazione Twitter")

Salvare i valori recuperati per l'applicazione Twitter. Sarà necessario usare questi valori più avanti nell'esercitazione.

## <a name="attach-libraries-to-spark-cluster"></a>Collegare librerie a un cluster Spark

In questa esercitazione verranno usate le API Twitter per inviare tweet a Hub eventi. Verrà anche usato il [connettore di Hub eventi per Apache Spark](https://github.com/Azure/azure-event-hubs-spark) per leggere e scrivere dati in Hub eventi di Azure. Per usare queste API nell'ambito del cluster, aggiungerle come librerie ad Azure Databricks e quindi associarle al cluster Spark. Le istruzioni seguenti illustrano come aggiungere le librerie per il **condiviso** cartella nell'area di lavoro.

1. Nell'area di lavoro di Azure Databricks selezionare **Workspace** (Area di lavoro) e quindi fare clic con il pulsante destro del mouse su **Shared** (Condivisa). Nel menu di scelta rapida scegliere **Create (Crea)** > **Library (Raccolta)**.

   ![Finestra di dialogo di aggiunta della libreria](../media/tutorials/databricks-add-library-option.png "Finestra di dialogo di aggiunta della libreria")

2. Nella pagina New Library (Nuova libreria) selezionare **Maven Coordinate** (Coordinata Maven) in **Source** (Origine). Per **Coordinate** (Coordinata) immettere la coordinata del pacchetto da aggiungere. Le coordinate di Maven per le librerie usate in questa esercitazione sono le seguenti:

   * Connettore di Hub eventi per Spark - `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`
   * API Twitter - `org.twitter4j:twitter4j-core:4.0.7`

     ![Specificare le coordinate Maven](../media/tutorials/databricks-eventhub-specify-maven-coordinate.png "Specificare le coordinate Maven ")

3. Selezionare **Create**.

4. Passare alla cartella in cui è stata aggiunta la libreria e selezionare il nome della libreria.

    ![Selezionare la libreria da aggiungere](../media/tutorials/select-library.png "Selezionare la libreria da aggiungere")

5. Se nella pagina della raccolta non è presente alcun cluster, selezionare **cluster** ed eseguire il cluster creato. Attendere che lo stato visualizzato 'Running' e quindi tornare alla pagina della raccolta.
Nella pagina libreria, selezionare il cluster in cui si desidera usare la libreria e quindi selezionare **installare**. Una volta che la libreria è correttamente associata al cluster, lo stato diventa immediatamente **Installed**.

    ![Installare libreria al cluster](../media/tutorials/databricks-library-attached.png "installare la libreria al cluster")

6. Ripetere questi passaggi per il pacchetto Twitter, `twitter4j-core:4.0.7`.

## <a name="get-a-cognitive-services-access-key"></a>Ottenere una chiave di accesso per Servizi cognitivi

In questa esercitazione, Usa la [Azure API servizi cognitivi anomalie Rilevatore](../overview.md) per eseguire il rilevamento delle anomalie in un flusso di tweet quasi in tempo reale. Prima di usare le API, è necessario creare una risorsa di rilevatore di anomalie in Azure e recuperare una chiave di accesso per usare le API di rilevamento delle anomalie.

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Selezionare **+ Crea una risorsa**.

3. In Azure Marketplace, selezionare **intelligenza artificiale e Machine Learning** > **Vedi tutto** > **di servizi cognitivi - più**  >  **Rilevatore di anomalie**. È inoltre possibile utilizzare [questo collegamento](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) per passare alle **crea** direttamente nella finestra di dialogo.

    ![Crea risorsa rilevatore di anomalie](../media/tutorials/databricks-cognitive-services-anomaly-detector.png "risorse creare rilevatore di anomalie")

4. Nella finestra di dialogo **Crea** specificare i valori seguenti:

    |Value |Descrizione  |
    |---------|---------|
    |NOME     | Un nome per la risorsa di rilevatore di anomalie.        |
    |Sottoscrizione     | La sottoscrizione di Azure, la risorsa verrà associata.        |
    |Località     | Una località di Azure.        |
    |Piano tariffario     | Un piano tariffario per il servizio. Per altre informazioni sui prezzi di rilevatore di anomalie, vedere [pagina dei prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/anomaly-detector/).        |
    |Gruppo di risorse     | Specificare se si vuole creare un nuovo gruppo di risorse o selezionarne uno esistente.        |


     Selezionare **Create**.

5. Dopo aver creata la risorsa, dal **Overview** scheda, seleziona **Mostra chiavi di accesso**.

    ![Mostra chiavi di accesso](../media/tutorials/cognitive-services-get-access-keys.png "Mostra chiavi di accesso")

    Copiare una parte dell'URL endpoint, come illustrato nello screenshot. Questo URL sarà necessario nell'esercitazione.

6. Sotto **chiavi**, selezionare l'icona di copia per la chiave da usare.

    ![Copiare le chiavi di accesso](../media/tutorials/cognitive-services-copy-access-keys.png "Copiare le chiavi di accesso")

7. Salvare i valori per l'URL endpoint e la chiave di accesso recuperati in questo passaggio perché saranno necessari più avanti in questa esercitazione.

## <a name="create-notebooks-in-databricks"></a>Creare notebook in Databricks

In questa sezione vengono creati due notebook nell'area di lavoro di Databricks con i nomi seguenti:

- **SendTweetsToEventHub**: notebook producer usato per ottenere tweet da Twitter e trasmetterli a Hub eventi.
- **AnalyzeTweetsFromEventHub** -un notebook consumer usato per leggere TWEET da hub eventi ed eseguire il rilevamento delle anomalie.

1. Nel riquadro a sinistra selezionare **Workspace** (Area di lavoro). Nell'elenco a discesa **Workspace** (Area di lavoro) selezionare **Create** (Crea) e quindi **Notebook**.

    ![Creare un blocco appunti in Databricks](../media/tutorials/databricks-create-notebook.png "Creare un blocco appunti in Databricks")

2. Nel **Notebook di creazione** finestra di dialogo immettere **SendTweetsToEventHub** come nome, selezionare **Scala** come linguaggio e selezionare il cluster Spark creato in precedenza.

    ![Creare un blocco appunti in Databricks](../media/tutorials/databricks-notebook-details.png "Creare un blocco appunti in Databricks")

    Selezionare **Create**.

3. Ripetere la procedura per creare il notebook **AnalyzeTweetsFromEventHub**.

## <a name="send-tweets-to-event-hubs"></a>Inviare tweet a Hub eventi

Nel notebook **SendTweetsToEventHub** incollare il codice seguente e sostituire il segnaposto con i valori per lo spazio dei nomi di Hub eventi e l'applicazione Twitter creata in precedenza. Questo notebook trasmette i tweet con la parola chiave "Azure" a Hub eventi in tempo reale.

```scala
//
// Send Data to Eventhub
//

import scala.collection.JavaConverters._
import com.microsoft.azure.eventhubs._
import java.util.concurrent._
import com.google.gson.{Gson, GsonBuilder, JsonParser}
import java.util.Date
import scala.util.control._
import twitter4j._
import twitter4j.TwitterFactory
import twitter4j.Twitter
import twitter4j.conf.ConfigurationBuilder

// Event Hub Config
val namespaceName = "[Placeholder: EventHub namespace]"
val eventHubName = "[Placeholder: EventHub name]"
val sasKeyName = "[Placeholder: EventHub access key name]"
val sasKey = "[Placeholder: EventHub access key key]"
val connStr = new ConnectionStringBuilder()
  .setNamespaceName(namespaceName)
  .setEventHubName(eventHubName)
  .setSasKeyName(sasKeyName)
  .setSasKey(sasKey)

// Connect to the Event Hub
val pool = Executors.newScheduledThreadPool(1)
val eventHubClient = EventHubClient.create(connStr.toString(), pool)

def sendEvent(message: String) = {
  val messageData = EventData.create(message.getBytes("UTF-8"))
  eventHubClient.get().send(messageData)
  System.out.println("Sent event: " + message + "\n")
}

case class MessageBody(var timestamp: Date, var favorite: Int)
val gson: Gson = new GsonBuilder().setDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSS'Z'").create()

val twitterConsumerKey = "[Placeholder: Twitter consumer key]"
val twitterConsumerSecret = "[Placeholder: Twitter consumer seceret]"
val twitterOauthAccessToken = "[Placeholder: Twitter oauth access token]"
val twitterOauthTokenSecret = "[Placeholder: Twitter oauth token secret]"

val cb = new ConfigurationBuilder()
cb.setDebugEnabled(true)
  .setOAuthConsumerKey(twitterConsumerKey)
  .setOAuthConsumerSecret(twitterConsumerSecret)
  .setOAuthAccessToken(twitterOauthAccessToken)
  .setOAuthAccessTokenSecret(twitterOauthTokenSecret)

val twitterFactory = new TwitterFactory(cb.build())
val twitter = twitterFactory.getInstance()

// Getting tweets with keyword "Azure" and sending them to the Event Hub in realtime!

val query = new Query(" #Azure ")
query.setCount(100)
query.lang("en")

var finished = false
var maxStatusId = Long.MinValue
var preMaxStatusId = Long.MinValue
val innerLoop = new Breaks
while (!finished) {
  val result = twitter.search(query)
  val statuses = result.getTweets()
  var lowestStatusId = Long.MaxValue
  innerLoop.breakable {
    for (status <- statuses.asScala) {
      if (status.getId() <= preMaxStatusId) {
        preMaxStatusId = maxStatusId
        innerLoop.break
      }
      if(!status.isRetweet()) {
        sendEvent(gson.toJson(new MessageBody(status.getCreatedAt(), status.getFavoriteCount())))
      }
      lowestStatusId = Math.min(status.getId(), lowestStatusId)
      maxStatusId = Math.max(status.getId(), maxStatusId)
    }
  }
  
  if (lowestStatusId == Long.MaxValue) {
    preMaxStatusId = maxStatusId
  }
  Thread.sleep(10000)
  query.setMaxId(lowestStatusId - 1)
}

// Close connection to the Event Hub
eventHubClient.get().close()
pool.shutdown()
```

Premere **MAIUSC+INVIO** per eseguire il notebook. L'output visualizzato sarà simile al frammento di codice seguente. Ogni evento nell'output è un tweet inserito in Hub eventi.

    Sent event: {"timestamp":"2019-04-24T09:39:40.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:38:48.000Z","favorite":1}

    Sent event: {"timestamp":"2019-04-24T09:38:36.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:37:27.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:37:00.000Z","favorite":2}

    Sent event: {"timestamp":"2019-04-24T09:31:11.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:30:15.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:30:02.000Z","favorite":1}

    ...
    ...

## <a name="read-tweets-from-event-hubs"></a>Leggere tweet da Hub eventi

Nel notebook **AnalyzeTweetsFromEventHub** incollare il codice seguente e sostituire il segnaposto con i valori dell'istanza di Hub eventi di Azure creata in precedenza. Questo notebook legge i tweet trasmessi in precedenza a Hub eventi tramite il notebook **SendTweetsToEventHub**.

In primo luogo, scrivere un client per chiamare rilevatore di anomalie. 
```scala

//
// Anomaly Detection Client
//

import java.io.{BufferedReader, DataOutputStream, InputStreamReader}
import java.net.URL
import java.sql.Timestamp

import com.google.gson.{Gson, GsonBuilder, JsonParser}
import javax.net.ssl.HttpsURLConnection

case class Point(var timestamp: Timestamp, var value: Double)
case class Series(var series: Array[Point], var maxAnomalyRatio: Double, var sensitivity: Int, var granularity: String)
case class AnomalySingleResponse(var isAnomaly: Boolean, var isPositiveAnomaly: Boolean, var isNegativeAnomaly: Boolean, var period: Int, var expectedValue: Double, var upperMargin: Double, var lowerMargin: Double, var suggestedWindow: Int)
case class AnomalyBatchResponse(var expectedValues: Array[Double], var upperMargins: Array[Double], var lowerMargins: Array[Double], var isAnomaly: Array[Boolean], var isPositiveAnomaly: Array[Boolean], var isNegativeAnomaly: Array[Boolean], var period: Int)

object AnomalyDetector extends Serializable {

  // Cognitive Services API connection settings
  val subscriptionKey = "[Placeholder: Your Anomaly Detector resource access key]"
  val endpoint = "[Placeholder: Your Anomaly Detector resource endpoint]"
  val latestPointDetectionPath = "/anomalydetector/v1.0/timeseries/last/detect"
  val batchDetectionPath = "/anomalydetector/v1.0/timeseries/entire/detect";
  val latestPointDetectionUrl = new URL(endpoint + latestPointDetectionPath)
  val batchDetectionUrl = new URL(endpoint + batchDetectionPath)
  val gson: Gson = new GsonBuilder().setDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSS'Z'").setPrettyPrinting().create()

  def getConnection(path: URL): HttpsURLConnection = {
    val connection = path.openConnection().asInstanceOf[HttpsURLConnection]
    connection.setRequestMethod("POST")
    connection.setRequestProperty("Content-Type", "text/json")
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey)
    connection.setDoOutput(true)
    return connection
  }

  // Handles the call to Cognitive Services API.
  def processUsingApi(request: String, path: URL): String = {
    println(request)
    val encoded_text = request.getBytes("UTF-8")
    val connection = getConnection(path)
    val wr = new DataOutputStream(connection.getOutputStream())
    wr.write(encoded_text, 0, encoded_text.length)
    wr.flush()
    wr.close()

    val response = new StringBuilder()
    val in = new BufferedReader(new InputStreamReader(connection.getInputStream()))
    var line = in.readLine()
    while (line != null) {
      response.append(line)
      line = in.readLine()
    }
    in.close()
    return response.toString()
  }

  // Calls the Latest Point Detection API for timeserie.
  def detectLatestPoint(series: Series): Option[AnomalySingleResponse] = {
    try {
      println("Process Timestamp: " + series.series.apply(series.series.length-1).timestamp.toString + ", size: " + series.series.length)
      val response = processUsingApi(gson.toJson(series), latestPointDetectionUrl)
      println(response)
      // Deserializing the JSON response from the API into Scala types
      val anomaly = gson.fromJson(response, classOf[AnomalySingleResponse])
      Thread.sleep(5000)
      return Some(anomaly)
    } catch {
      case e: Exception => {
        println(e)
        e.printStackTrace()
        return None
      }
    }
  }

  // Calls the Batch Detection API for timeserie.
  def detectBatch(series: Series): Option[AnomalyBatchResponse] = {
    try {
      val response = processUsingApi(gson.toJson(series), batchDetectionUrl)
      println(response)
      // Deserializing the JSON response from the API into Scala types
      val anomaly = gson.fromJson(response, classOf[AnomalyBatchResponse])
      Thread.sleep(5000)
      return Some(anomaly)
    } catch {
      case e: Exception => {
        println(e)
        return None
      }
    }
  }
}
```

Premere **MAIUSC+INVIO** per eseguire il notebook. L'output visualizzato sarà simile al frammento di codice seguente. :

    import java.io.{BufferedReader, DataOutputStream, InputStreamReader}
    import java.net.URL
    import java.sql.Timestamp
    import com.google.gson.{Gson, GsonBuilder, JsonParser}
    import javax.net.ssl.HttpsURLConnection
    defined class Point
    defined class Series
    defined class AnomalySingleResponse
    defined class AnomalyBatchResponse
    defined object AnomalyDetector

Preparare quindi una funzione di aggregazione per l'utilizzo futuro.
```scala
//
// User Defined Aggregation Function for Anomaly Detection
//

import org.apache.spark.sql.Row
import org.apache.spark.sql.expressions.{MutableAggregationBuffer, UserDefinedAggregateFunction}
import org.apache.spark.sql.types.{StructType, TimestampType, FloatType, MapType, BooleanType, DataType}
//import org.apache.spark.sql.functions._
import scala.collection.immutable.ListMap

class AnomalyDetectorAggregationFunction_Hourly extends UserDefinedAggregateFunction {
  override def inputSchema: StructType = new StructType().add("timestamp", TimestampType).add("value", FloatType)
  
  override def bufferSchema: StructType = new StructType().add("point", MapType(TimestampType, FloatType))
  
  override def dataType: DataType = BooleanType
  
  override def deterministic: Boolean = false
  
  override def initialize(buffer: MutableAggregationBuffer): Unit = {
    buffer(0) = Map()
  }
  
  override def update(buffer: MutableAggregationBuffer, input: Row): Unit = {
    buffer(0) = buffer.getAs[Map[java.sql.Timestamp, Float]](0) + (input.getTimestamp(0) -> input.getFloat(1))
  }
  
  override def merge(buffer1: MutableAggregationBuffer, buffer2: Row): Unit = {
    buffer1(0) = buffer1.getAs[Map[java.sql.Timestamp, Float]](0) ++ buffer2.getAs[Map[java.sql.Timestamp, Float]](0)
  }
  
  override def evaluate(buffer: Row): Any = {
    val points = buffer.getAs[Map[java.sql.Timestamp, Float]](0)
    if (points.size > 12) {
      val sorted_points = ListMap(points.toSeq.sortBy(_._1.getTime):_*)
      var detect_points: List[Point] = List()
      sorted_points.keys.foreach {
        key => detect_points = detect_points :+ new Point(key, sorted_points(key))
      }
      
      
      // 0.25 is maxAnomalyRatio. It represents 25%, max anomaly ratio in a time series.
      // 95 is the sensitivity of the algorithms. 
      // Check Anomaly detector API reference (https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-last-detect)
      
      val series: Series = new Series(detect_points.toArray, 0.25, 95, "hourly")
      val response: Option[AnomalySingleResponse] = AnomalyDetector.detectLatestPoint(series)
      if (!response.isEmpty) {
        return response.get.isAnomaly
      }
    }
    
    return None
  }
}

```

Premere **MAIUSC+INVIO** per eseguire il notebook. L'output visualizzato sarà simile al frammento di codice seguente. 

    import org.apache.spark.sql.Row
    import org.apache.spark.sql.expressions.{MutableAggregationBuffer, UserDefinedAggregateFunction}
    import org.apache.spark.sql.types.{StructType, TimestampType, FloatType, MapType, BooleanType, DataType}
    import scala.collection.immutable.ListMap
    defined class AnomalyDetectorAggregationFunction

Caricare quindi i dati da hub eventi per il rilevamento anomalie.

```scala
//
// Load Data from Eventhub
//

import org.apache.spark.eventhubs._
import org.apache.spark.sql.types._
import org.apache.spark.sql.functions._

val connectionString = ConnectionStringBuilder("[Placeholder: EventHub namespace connection string]")
  .setEventHubName("[Placeholder: EventHub name]")
  .build

val customEventhubParameters =
  EventHubsConf(connectionString)
  .setConsumerGroup("$Default")
  .setMaxEventsPerTrigger(100)

val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()

val messages =
  incomingStream
  .withColumn("enqueuedTime", $"enqueuedTime".cast(TimestampType))
  .withColumn("body", $"body".cast(StringType))
  .select("enqueuedTime", "body")

val bodySchema = new StructType().add("timestamp", TimestampType).add("favorite", IntegerType)

val msgStream = messages.select(from_json('body, bodySchema) as 'fields).select("fields.*")

msgStream.printSchema

display(msgStream)

```

L'output sarà ora simile al seguente. Pagare attenzione a che la data nella tabella potrebbe essere diversa dalla data in questa esercitazione come i dati sono in tempo reale.
![Hub di dati da eventi di caricamento](../media/tutorials/load-data-from-eventhub.png "carica i dati da Hub eventi")

È stato eseguito lo streaming di dati near real time da Hub eventi di Azure ad Azure Databricks usando il connettore di Hub eventi per Apache Spark. Per altre informazioni su come usare il connettore di Hub eventi per Spark, vedere la [documentazione del connettore](https://github.com/Azure/azure-event-hubs-spark/tree/master/docs).



## <a name="run-anomaly-detection-on-tweets"></a>Eseguire il rilevamento anomalie sui TWEET

In questa sezione si esegue il rilevamento anomalie sui TWEET ricevuti usando l'API rilevatore di anomalie. Per questa sezione, aggiungere i frammenti di codice allo stesso notebook **AnalyzeTweetsFromEventHub**.

A scopo di rilevamento delle anomalie, in primo luogo, è necessario aggregare il conteggio delle metriche per ora.
```scala
//
// Aggregate Metric Count by Hour
//

// If you want to change granularity, change the groupBy window. 
val groupStream = msgStream.groupBy(window($"timestamp", "1 hour"))
  .agg(avg("favorite").alias("average"))
  .withColumn("groupTime", $"window.start")
  .select("groupTime", "average")

groupStream.printSchema

display(groupStream)
```
L'output sarà simile a questo punto i frammenti di codice seguente.
```
groupTime                       average
2019-04-23T04:00:00.000+0000    24
2019-04-26T19:00:00.000+0000    47.888888888888886
2019-04-25T12:00:00.000+0000    32.25
2019-04-26T09:00:00.000+0000    63.4
...
...

```

Ottenere quindi il risultato dell'output aggregato a Delta. Poiché il rilevamento delle anomalie richiede una finestra della cronologia più lunga, utilizziamo Delta per mantenere i dati di cronologia per il punto in cui che si desidera rilevare. 


```scala
//
// Output Aggregation Result to Delta
//

groupStream.writeStream
  .format("delta")
  .outputMode("complete")
  .option("checkpointLocation", "/delta/[Placeholder: table name]/_checkpoints/[Placeholder: folder name for checkpoints]")
  .table("[Placeholder: table name]")

```

```scala
//
// Show Aggregate Result
//

val twitterCount = spark.sql("SELECT COUNT(*) FROM [Placeholder: table name]")
twitterCount.show()

val twitterData = spark.sql("SELECT * FROM [Placeholder: table name] ORDER BY groupTime")
twitterData.show(200, false)

display(twitterData)
```
L'output come indicato di seguito: 
```
groupTime                       average
2019-04-08T01:00:00.000+0000    25.6
2019-04-08T02:00:00.000+0000    6857
2019-04-08T03:00:00.000+0000    71
2019-04-08T04:00:00.000+0000    55.111111111111114
2019-04-08T05:00:00.000+0000    2203.8
...
...

```

A questo punto i dati delle serie temporali aggregati in modo continuo vengono inseriti nel Delta. È quindi possibile pianificare un processo ogni ora per rilevare le anomalie del punto più recente. 

```scala
//
// Anomaly Detection with Batch query
//

import java.time.Instant
import java.time.temporal.ChronoUnit

val detectData = spark.read.format("delta").table("[Placeholder: table name]")

// How long history you want to use in anomaly detection. It is hourly time series in this tutorial, so 72 means 72 hours. 
val batchSize = 72

// Change the endTime to where you want to detect. You could use Databricks to schedule a job and change it to the latest hour. 
val endTime = Instant.parse("2019-04-16T00:00:00Z")
val startTime = endTime.minus(batchSize, ChronoUnit.HOURS)

val series = detectData.filter($"groupTime" < endTime.toString && $"groupTime" >= startTime.toString).sort($"groupTime")

series.createOrReplaceTempView("series")

//series.show()

// Register the function to access it
spark.udf.register("anomalydetect", new AnomalyDetectorAggregationFunction)

val adResult = spark.sql("SELECT '" + endTime.toString + "' as timestamp, anomalydetect(groupTime, average) as anomaly FROM series")
adResult.show()
```
Risultato come indicato di seguito: 

```
+--------------------+-------+
|           timestamp|anomaly|
+--------------------+-------+
|2019-04-16T00:00:00Z|  false|
+--------------------+-------+

```
Il risultato del rilevamento delle anomalie al Delta di output. 
```scala
//
// Output Batch AD Result to delta
//

adResult.writeStream
  .format("delta")
  .outputMode("complete")
  .option("checkpointLocation", "/delta/[Placeholder: table name]/_checkpoints/[Placeholder: folder name for checkpoints]")
  .table("[Placeholder: table name]")
  
```


L'operazione è terminata. Usando Azure Databricks sono stati trasmessi dati in hub eventi di Azure usato i dati di flusso usando il connettore di hub eventi e quindi è stato eseguito il rilevamento delle anomalie nel flusso di dati quasi in tempo reale.
Sebbene in questa esercitazione, la granularità è su base oraria, è sempre possibile modificare la granularità in base alle proprie esigenze. 

## <a name="clean-up-resources"></a>Pulire le risorse

Dopo aver concluso l'esecuzione per l'esercitazione è possibile terminare il cluster. A questo scopo, nel riquadro sinistro dell'area di lavoro di Azure Databricks fare clic su **Clusters** (Cluster). Per il cluster che si vuole terminare, posizionare il cursore sui puntini di sospensione sotto la colonna **Actions** (Azioni) e fare clic sull'icona **Terminate** (Termina).

![Arrestare un cluster Databricks](../media/tutorials/terminate-databricks-cluster.png "Arrestare un cluster Databricks")

Se si non terminare manualmente il cluster si arresterà automaticamente, fornito è stata selezionata la **termina dopo \_ \_ minuti di inattività** casella di controllo durante la creazione del cluster. In tal caso, il cluster verrà automaticamente arrestato se è rimasto inattivo per il tempo specificato.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come usare Azure Databricks per trasmettere dati a Hub eventi di Azure e quindi leggere i dati trasmessi da Hub eventi in tempo reale. Passare all'esercitazione successiva per informazioni su come chiamare l'API rilevatore di anomalie e rappresentare graficamente le anomalie usando Power BI desktop. 

> [!div class="nextstepaction"]
>[Rilevamento delle anomalie di batch con Power BI desktop](batch-anomaly-detection-powerbi.md)
