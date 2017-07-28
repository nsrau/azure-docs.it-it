---
title: Usare lo streaming di Apache Spark con 	Hub eventi in Azure HDInsight | Microsoft Docs
description: Creare un esempio di streaming di Apache Spark su come inviare un flusso di dati a Hub eventi di Azure e quindi ricevere tali eventi nel cluster HDInsight Spark usando un'applicazione Scala.
keywords: streaming apache spark, streaming spark, esempio spark, esempio streaming spark apache, esempio azure hub eventi, esempio spark
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 68894e75-3ffa-47bd-8982-96cdad38b7d0
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: 80be19618bd02895d953f80e5236d1a69d0811af
ms.openlocfilehash: 41019b4ae022602d2688399d1fc309151174e157
ms.contentlocale: it-it
ms.lasthandoff: 06/07/2017

---
# <a name="apache-spark-streaming-process-data-from-azure-event-hubs-with-spark-cluster-on-hdinsight"></a>Streaming Apache Spark: elaborare i dati dall'Hub eventi di Azure con il cluster Spark in HDInsight

In questo articolo si crea un esempio di streaming di Apache Spark che implica i passaggi seguenti:

1. Usare un'applicazione autonoma per inserire messaggi in un Hub di eventi di Azure.

2. Esistono due approcci diversi per recuperare i messaggi dall'Hub eventi in tempo reale usando un'applicazione eseguita nel cluster Spark in Azure HDInsight.

3. Compilare le pipeline analitiche sullo streaming per rendere persistenti i dati su sistemi di archiviazione diversi o ottenere informazioni dettagliate dai dati in tempo reale.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Un cluster Apache Spark in HDInsight. Per istruzioni, vedere l'articolo relativo alla [creazione di cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="spark-streaming-concepts"></a>Concetti relativi allo streaming Spark

Per una spiegazione dettagliata dello streaming Spark, vedere [Apache Spark streaming overview](http://spark.apache.org/docs/latest/streaming-programming-guide.html#overview) (Panoramica dello streaming in Apache Spark). HDInsight offre le stesse funzioni di streaming a un cluster Spark in Azure.  

## <a name="what-does-this-solution-do"></a>Scopo di questa soluzione

In questo articolo per creare un esempio di streaming Spark è necessario procedere come segue:

1. Creare un Hub eventi di Azure che riceve un flusso di eventi.

2. Eseguire un'applicazione autonoma locale che generi eventi e ne effettui il push all'Hub eventi di Azure. L'applicazione di esempio che esegue questa operazione è pubblicata all'indirizzo [https://github.com/hdinsight/spark-streaming-data-persistence-examples](https://github.com/hdinsight/spark-streaming-data-persistence-examples).

3. Eseguire un'applicazione di streaming in modalità remota in un cluster Spark che legge eventi di streaming provenienti dall'Hub di eventi di Azure ed eseguire varie analisi/elaborazioni di dati.

## <a name="create-an-azure-event-hub"></a>Creare un Hub di eventi di Azure

1. Accedere al [portale di Azure](https://ms.portal.azure.com) e fare clic su **Nuovo** nella parte superiore sinistra della schermata.

2. Fare clic su **Internet delle cose** e quindi su **Hub eventi**.

    ![Creare un hub eventi per l'esempio di streaming Spark](./media/hdinsight-apache-spark-eventhub-streaming/hdinsight-create-event-hub-for-spark-streaming.png "Creare un hub eventi per l'esempio di streaming Spark")

3. Nel pannello **Crea spazio dei nomi** immettere un nome per lo spazio dei nomi. scegliere il piano tariffario (Base o Standard). Scegliere anche una sottoscrizione, un gruppo di risorse e una località di Azure in cui creare la risorsa. Fare clic su **Crea** per creare lo spazio dei nomi.

      ![Fornire un nome di hub eventi per l'esempio di streaming Spark](./media/hdinsight-apache-spark-eventhub-streaming/hdinsight-provide-event-hub-name-for-spark-streaming.png "Fornire un nome di hub eventi per l'esempio di streaming Spark")

    > [!NOTE]
    > È necessario selezionare lo stesso **Percorso** del cluster Apache Spark in HDInsight per ridurre i costi e la latenza.
    >
    >

4. Nell'elenco degli spazi dei nomi di Hub eventi fare clic sullo spazio dei nomi appena creato.      


5. Nel pannello dello spazio dei nomi, fare clic su **Hub eventi**, quindi fare clic su **+ Event Hub** (+ Hub eventi) per creare un nuovo Hub eventi.
   
    ![Creare un hub eventi per l'esempio di streaming Spark](./media/hdinsight-apache-spark-eventhub-streaming/hdinsight-open-event-hubs-blade-for-spark-streaming-example.png "Creare un hub eventi per l'esempio di streaming Spark")

6. Digitare un nome per l'Hub eventi, impostare il numero di partizioni su 10 e la conservazione dei messaggi su 1. In questa soluzione non vengono archiviati i messaggi, pertanto è consigliabile non modificare le impostazioni predefinite e quindi fare clic su **Crea**.
   
    ![Fornire i dettagli dell'hub eventi per l'esempio di streaming Spark](./media/hdinsight-apache-spark-eventhub-streaming/hdinsight-provide-event-hub-details-for-spark-streaming-example.png "Fornire i dettagli dell'hub eventi per l'esempio di streaming Spark")

7. L'Hub eventi appena creato viene elencato nel pannello dell'Hub eventi.
    
     ![Visualizzare un hub eventi per l'esempio di streaming Spark](./media/hdinsight-apache-spark-eventhub-streaming/hdinsight-view-event-hub-for-spark-streaming-example.png "Visualizzare un hub eventi per l'esempio di streaming Spark")

8. Nel pannello dello spazio dei nomi (non in quello dello specifico hub eventi) fare clic su **Criteri di accesso condivisi** e quindi su **RootManageSharedAccessKey**.
    
     ![Impostare i criteri dell'hub eventi per l'esempio di streaming Spark](./media/hdinsight-apache-spark-eventhub-streaming/hdinsight-set-event-hub-policies-for-spark-streaming-example.png "Impostare i criteri dell'hub eventi per l'esempio di streaming Spark")

9. Fare clic sul pulsante di copia per copiare la chiave primaria **RootManageSharedAccessKey** e la stringa di connessione negli Appunti. Salvarle per usarle più avanti nell'esercitazione.
    
     ![Visualizzare le chiavi dei criteri dell'hub eventi per l'esempio di streaming Spark](./media/hdinsight-apache-spark-eventhub-streaming/hdinsight-view-event-hub-policy-keys.png "Visualizzare le chiavi dei criteri dell'hub eventi per l'esempio di streaming Spark")

## <a name="send-messages-to-azure-event-hub-using-a-sample-scala-application"></a>Usare un'applicazione Scala di esempio per inviare messaggi all'Hub eventi di Azure

In questa sezione si userà un'applicazione Scala locale autonoma che genera un flusso di eventi e lo invia all'Hub eventi di Azure creato in precedenza. Questa applicazione è disponibile in GitHub all'indirizzo [https://github.com/hdinsight/eventhubs-sample-event-producer](https://github.com/hdinsight/eventhubs-sample-event-producer). Questi passaggi presuppongono che sia già stato eseguito il fork di questo repository GitHub.

1. Assicurarsi che nel computer in cui viene eseguita l'applicazione siano installati i prodotti seguenti.

    * Oracle Java Development Kit. Per installarlo, fare clic [qui](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
    * Apache Maven. È possibile scaricarlo [qui](https://maven.apache.org/download.cgi). Le istruzioni per scaricare Maven sono disponibili [qui](https://maven.apache.org/install.html).

2. Aprire un prompt dei comandi e accedere alla posizione in cui è stato clonato il repository GitHub per l'applicazione di esempio Scala, quindi eseguire il comando seguente per compilare l'applicazione.

        mvn package

3. Il file di output con estensione jar per l'applicazione, **com-microsoft-azure-eventhubs-client-example-0.2.0.jar**, viene creato in **/target** directory. Il file con estensione jar viene usato più avanti in questo articolo per testare la soluzione completa.

## <a name="create-application-to-receive-messages-from-event-hub-into-a-spark-cluster"></a>Creare l'applicazione per ricevere messaggi dall'Hub eventi in un cluster Spark 

Esistono due approcci per connettere lo streaming Spark e gli Hub eventi di Azure: la connessione basata su ricevitore e la connessione basata su DStream diretto. Quest'ultima è stata introdotta a gennaio 2017 con la versione 2.0.3. Dal momento che è più efficiente e usa le risorse in modo più efficace, è destinata a sostituire la connessione originale basata su ricevitore. Altre informazioni sono disponibili su [https://github.com/hdinsight/spark-eventhubs](https://github.com/hdinsight/spark-eventhubs). DStream diretto supporta solo Spark 2.0 e versioni successive.

### <a name="build-applications-with-the-dependency-to-spark-eventhubs-connector"></a>Compilazione di applicazioni con la dipendenza al connettore Spark-EventHubs

Verrà pubblicata anche la versione di gestione temporanea di Spark-EventHubs in GitHub. Il primo passaggio per usare la versione di gestione temporanea di Spark-EventHubs consiste nell'indicare GitHub come repository di origine aggiungendo la voce seguente a pom.xml:

```xml
<repository>
      <id>spark-eventhubs</id>
      <url>https://raw.github.com/hdinsight/spark-eventhubs/maven-repo/</url>
      <snapshots>
        <enabled>true</enabled>
        <updatePolicy>always</updatePolicy>
      </snapshots>
</repository>
```

È quindi possibile aggiungere la dipendenza seguente al progetto per selezionare la versione pre-rilascio.

Dipendenza Maven

```xml
<!-- https://mvnrepository.com/artifact/com.microsoft.azure/spark-streaming-eventhubs_2.11 -->
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spark-streaming-eventhubs_2.11</artifactId>
    <version>2.0.4</version>
</dependency>
```

Dipendenza SBT

```
// https://mvnrepository.com/artifact/com.microsoft.azure/spark-streaming-eventhubs_2.11
libraryDependencies += "com.microsoft.azure" % "spark-streaming-eventhubs_2.11" % "2.0.4"
```

### <a name="direct-dstream-connection"></a>Connessione DStream diretto

È possibile scaricare da [http://central.maven.org/maven2/com/microsoft/azure/spark-streaming-eventhubs_2.11/2.0.4/spark-streaming-eventhubs_2.11-2.0.4.jar](http://central.maven.org/maven2/com/microsoft/azure/spark-streaming-eventhubs_2.11/2.0.4/spark-streaming-eventhubs_2.11-2.0.4.jar) un file con estensione jar preesistente contenente esempi di uso di DStream diretto.

Il file con estensione jar contiene tre esempi i cui codici sorgente sono disponibili su [https://github.com/hdinsight/spark-eventhubs/tree/master/examples/src/main/scala/com/microsoft/spark/streaming/examples/directdstream](https://github.com/hdinsight/spark-eventhubs/tree/master/examples/src/main/scala/com/microsoft/spark/streaming/examples/directdstream).

Prendere come esempio [WindowingWordCount](https://github.com/hdinsight/spark-eventhubs/blob/master/examples/src/main/scala/com/microsoft/spark/streaming/examples/directdstream/WindowingWordCount.scala):

```scala
private def createStreamingContext(
  sparkCheckpointDir: String,
  batchDuration: Int,
  namespace: String,
  eventHunName: String,
  eventhubParams: Map[String, String],
  progressDir: String) = {
val ssc = new StreamingContext(new SparkContext(), Seconds(batchDuration))
ssc.checkpoint(sparkCheckpointDir)
val inputDirectStream = EventHubsUtils.createDirectStreams(
  ssc,
  namespace,
  progressDir,
  Map(eventHunName -> eventhubParams))

inputDirectStream.map(receivedRecord => (new String(receivedRecord.getBody), 1)).
  reduceByKeyAndWindow((v1, v2) => v1 + v2, (v1, v2) => v1 - v2, Seconds(batchDuration * 3),
    Seconds(batchDuration)).print()

ssc
}

def main(args: Array[String]): Unit = {

if (args.length != 8) {
  println("Usage: program progressDir PolicyName PolicyKey EventHubNamespace EventHubName" +
    " BatchDuration(seconds) Spark_Checkpoint_Directory maxRate")
  sys.exit(1)
}

val progressDir = args(0)
val policyName = args(1)
val policykey = args(2)
val namespace = args(3)
val name = args(4)
val batchDuration = args(5).toInt
val sparkCheckpointDir = args(6)
val maxRate = args(7)

val eventhubParameters = Map[String, String] (
  "eventhubs.policyname" -> policyName,
  "eventhubs.policykey" -> policykey,
  "eventhubs.namespace" -> namespace,
  "eventhubs.name" -> name,
  "eventhubs.partition.count" -> "32",
  "eventhubs.consumergroup" -> "$Default",
  "eventhubs.maxRate" -> s"$maxRate"
)

val ssc = StreamingContext.getOrCreate(sparkCheckpointDir,
  () => createStreamingContext(sparkCheckpointDir, batchDuration, namespace, name,
    eventhubParameters, progressDir))

ssc.start()
ssc.awaitTermination()
}
```

Nell'esempio precedente, `eventhubParameters` si riferisce ai parametri specifici di una singola istanza EventHubs e deve essere trasferito all'API `createDirectStreams` che costruisce un mapping degli oggetti DStream diretto a uno spazio dei nomi dell'Hub eventi. Nell'oggetto DStream diretto, è possibile chiamare qualsiasi API DStream fornita dal framework dell'API di streaming Spark. In questo esempio viene calcolata la frequenza di ogni parola negli ultimi 3 intervalli di micro batch.

### <a name="receiver-based-connection"></a>Connessioni basate su ricevitore

Un'applicazione scritta in Scala di esempio di streaming Spark, che riceve gli eventi e li instrada a destinazioni diverse, è disponibile all'indirizzo [https://github.com/hdinsight/spark-streaming-data-persistence-examples](https://github.com/hdinsight/spark-streaming-data-persistence-examples). Seguire questa procedura per aggiornare l'applicazione per la configurazione dell'Hub eventi e creare il file con estensione jar di output.

1. Avviare IntelliJ IDEA e dalla schermata di avvio selezionare **Check out from Version Control** (Estrai da controllo della versione) e quindi fare clic su **Git**.
   
    ![Esempio di streaming Apache Spark - Ottenere origini da Git](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-get-source-from-git.png "Esempio di streaming Apache Spark - Ottenere origini da Git")

2. Nella finestra di dialogo **Clone Repository** (Clona repository) immettere l'URL del repository Git da cui eseguire la clonazione, specificare la directory da clonare e quindi fare clic su **Clone** (Clona).
   
    ![Esempio di streaming Apache Spark - Clonare da Git](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-clone-from-git.png "Esempio di streaming Apache Spark - Clonare da Git")
3. Seguire le istruzioni finché la clonazione del progetto non sarà completa. Premere **ALT+1** per aprire la **visualizzazione progetto**, che dovrebbe essere simile alla seguente.
   
    ![Esempio di streaming Apache Spark - Project View](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-project-view.png "Esempio di streaming Apache Spark - Project View")
4. Verificare che il codice dell'applicazione venga compilato con Java8. Per esserne certi, fare clic su **File** (File), quindi su **Project Structure** (Struttura progetto) e nella scheda **Project** (Progetto) verificare che Project language level (Livello del linguaggio di progetto) sia impostato su **8 - Lambdas, type annotations, etc.** (8 - Lambda, annotazioni tipo e così via).
   
    ![Esempio di streaming Apache Spark - Impostare il compilatore](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-java-8-compiler.png "Esempio di streaming Apache Spark - Impostare il compilatore")
5. Aprire il file **pom.xml** e verificare che la versione di Spark sia corretta. Nel nodo `<properties>` cercare il frammento seguente e verificare la versione di Spark.

        <scala.version>2.11.8</scala.version>
        <scala.compat.version>2.11.8</scala.compat.version>
        <scala.binary.version>2.11</scala.binary.version>
        <spark.version>2.0.0</spark.version>

6. L'applicazione richiede un file con estensione JAR di dipendenza denominato **JDBC driver jar** (Driver JDBC jar). È necessario per scrivere i messaggi ricevuti dall'Hub eventi in un database SQL di Azure. È possibile scaricare la versione 4.1 o successiva di questo file con estensione jar [qui](https://msdn.microsoft.com/sqlserver/aa937724.aspx). Aggiungere riferimenti a questo file con estensione jar nella libreria del progetto. Eseguire la procedura seguente:
     
     1. Nella finestra di IntelliJ IDEA in cui è aperta l'applicazione fare clic su **File** (File), su **Project Structure** (Struttura progetto) e quindi su **Libraries** (Librerie). 
     2. Fare clic sull'icona di aggiunta (![icona per l'aggiunta](./media/hdinsight-apache-spark-eventhub-streaming/add-icon.png)), fare clic su **Java**e quindi passare al percorso in cui è stato scaricato il file con estensione jar del driver JDBC. Seguire le istruzioni per aggiungere il file con estensione jar alla libreria del progetto.

         ![aggiungere dipendenze mancanti](./media/hdinsight-apache-spark-eventhub-streaming/add-missing-dependency-jars.png "Aggiungere file JAR di dipendenza mancanti")
     3. Fare clic su **Apply**.

7. Creare il file con estensione jar di output. Eseguire i passaggi seguenti.

   1. Nella finestra di dialogo **Project Structure** (Struttura progetto) fare clic su **Artifacts** (Elementi) e quindi sul segno più. Nella finestra di dialogo popup fare clic su **JAR**, quindi fare clic su **From modules with dependencies** (Da moduli con dipendenze).      
       
       ![Esempio di streaming Apache Spark - Creazione di un file con estensione jar](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-create-jar.png "Esempio di streaming Apache Spark - Creazione di un file con estensione jar")
   2. Nella finestra di dialogo **Create JAR from Modules** (Crea JAR da moduli) fare clic sui puntini di sospensione (![puntini di sospensione](./media/hdinsight-apache-spark-eventhub-streaming/ellipsis.png)) relativi a **Main Class** (Classe principale).
   3. Nella finestra di dialogo **Select Main Class** (Seleziona classe principale) selezionare una delle classi disponibili e quindi fare clic su **OK**.
      
       ![Esempio di streaming Apache Spark - Selezionare la classe per un file con estensione jar](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-select-class-for-jar.png "Esempio di streaming Apache Spark - Selezionare la classe per un file con estensione jar")
   4. Nella finestra di dialogo **Create JAR from Modules** (Crea JAR da moduli) verificare che l'opzione per **estrarre nel file JAR di destinazione** sia selezionata e quindi fare clic su **OK**. Verrà creato un singolo file con estensione jar con tutte le dipendenze.
      
       ![Esempio di streaming Apache Spark - Creare un file con estensione jar dai moduli](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-create-jar-from-modules.png "Esempio di streaming Apache Spark - Creare un file con estensione jar dai modul")
   5. Nella scheda **Output Layout** sono elencati tutti i file JAR inclusi nel progetto Maven. È possibile selezionare ed eliminare quelli in cui l'applicazione Scala non ha dipendenze dirette. Per l'applicazione che si sta creando è possibile rimuovere tutti i file tranne l'ultimo,**spark-streaming-data-persistence-examples compile output**. Selezionare i file JAR da eliminare e quindi fare clic sull'icona **Delete** (Elimina) (![icona di eliminazione](./media/hdinsight-apache-spark-eventhub-streaming/delete-icon.png)).
      
       ![Esempio di streaming Apache Spark - Eliminare i file con estensione jar estratti](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-delete-output-jars.png "Esempio di streaming Apache Spark - Eliminare i file con estensione jar estratti")
      
       Assicurarsi che la casella **Build on make** sia selezionata per garantire che il file jar venga creato ogni volta che il progetto viene creato o aggiornato. Fare clic su **Apply**.
   6. Nella scheda **Output Layout** (Layout output) in fondo alla casella **Available Elements** (Elementi disponibili) sono disponibili i file JAR SQL JDBC aggiunti in precedenza alla libreria del progetto. È necessario aggiungerli alla scheda **Output Layout** . Fare clic con il pulsante destro del mouse sul file con estensione jar, quindi scegliere **Extract Into Output Root**.
      
       ![Esempio di streaming Apache Spark - Estrarre file con estensione jar di dipendenza](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-extract-dependency-jar.png "Esempio di streaming Apache Spark - Estrarre file con estensione jar di dipendenza")  
      
       La scheda **Output Layout** dovrebbe essere simile alla seguente.
      
       ![Esempio di streaming Apache Spark - Scheda output finale](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-final-output-tab.png "Esempio di streaming Apache Spark - Scheda output finale")        
      
       Nella finestra di dialogo **Project Structure** (Struttura progetto) fare clic su **Apply** (Applica) e quindi su **OK**.    
   7. Sulla barra dei menu fare clic su **Build** (Compila) e quindi su **Make Project** (Crea progetto). È anche possibile fare clic su **Build Artifacts** per creare il file JAR. Il file JAR di output viene creato in **\classes\artifacts**.
      
       ![Esempio di streaming Apache Spark - Output dei file con estensione jar](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-output-jar.png "Esempio di streaming Apache Spark - Output dei file con estensione jar")

## <a name="run-the-application-remotely-on-a-spark-cluster-using-livy"></a>Eseguire l'applicazione in modalità remota in un cluster Spark usando Livy

In questo articolo si usa Livy per eseguire l'applicazione di streaming Apache Spark in modalità remota in un cluster Spark. Per informazioni dettagliate sull'uso di Livy il cluster Spark HDInsight, vedere [Inviare processi in modalità remota a un cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-livy-rest-interface.md). Prima di avviare l'esecuzione dell'applicazione di streaming Spark, eseguire queste operazioni:

1. Avviare l'applicazione autonoma locale per generare eventi e inviarli all'Hub eventi. A questo scopo, usare il comando seguente:

        java -cp com-microsoft-azure-eventhubs-client-example-0.2.0.jar com.microsoft.eventhubs.client.example.EventhubsClientDriver --eventhubs-namespace "mysbnamespace" --eventhubs-name "myeventhub" --policy-name "mysendpolicy" --policy-key "<policy key>" --message-length 32 --thread-count 32 --message-count -1

2. Copiare il file con estensione JAR di streaming (**spark-streaming-data-persistence-examples.jar**) nell'archivio BLOB di Azure associato al cluster. Questa operazione rende il file con estensione jar accessibile a Livy. A tale scopo è possibile usare [**AzCopy**](../storage/storage-use-azcopy.md), un'utilità della riga di comando. Sono disponibili molti altri client da usare per caricare i dati. Altre informazioni su questi client sono disponibili in [Caricare dati per processi Hadoop in HDInsight](hdinsight-upload-data.md).
3. Installare CURL nel computer in cui si eseguono tali applicazioni. CURL viene usato per richiamare gli endpoint Livy per eseguire i processi in modalità remota.

### <a name="run-the-spark-streaming-application-to-receive-the-events-into-an-azure-storage-blob-as-text"></a>Eseguire l'applicazione di streaming Spark per ricevere gli eventi in un BLOB del servizio di archiviazione di Azure come testo

Aprire un prompt dei comandi, passare alla directory in cui è installato CURL ed eseguire il comando seguente (sostituire nome utente/password e nome cluster):

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputBlob.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

I parametri nel file **inputBlob.txt** sono definiti come segue:

    { "file":"wasb:///example/jars/spark-streaming-data-persistence-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsEventCount", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Ecco una descrizione dei parametri nel file di input:

* **file** è il percorso del file JAR dell'applicazione è già stato copiato nell'account di archiviazione di Azure associato al cluster.
* **className** è il nome della classe nel file JAR.
* **args** è l'elenco di argomenti richiesti dalla classe.
* **numExecutors** è il numero di core usati da Spark per eseguire l'applicazione di streaming. Deve essere sempre almeno il doppio del numero di partizioni dell'Hub eventi.
* **executorMemory**, **executorCores**, **driverMemory** sono i parametri usati per assegnare le risorse richieste per l'applicazione di streaming.

> [!NOTE]
> Non è necessario creare le cartelle di output (EventCheckpoint, EventCount/EventCount10) usate come parametri, perché vengono create automaticamente dall'applicazione di streaming.
>
>

Quando si esegue il comando, viene visualizzato un output simile al seguente:

    < HTTP/1.1 201 Created
    < Content-Type: application/json; charset=UTF-8
    < Location: /18
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Tue, 01 Dec 2015 05:39:10 GMT
    < Content-Length: 37
    <
    {"id":1,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

Prendere nota dell'ID batch nell'ultima riga dell'output. In questo esempio è "1". Per verificare che l'applicazione venga eseguita correttamente, è possibile esaminare l'account di archiviazione di Azure associato al cluster, dove sarà visualizzata la cartella **/EventCount/EventCount10** creata. Questa cartella contiene i BLOB che acquisiscono il numero di eventi elaborati durante il periodo di tempo specificato per il parametro **batch-interval-in-seconds**.

L'esecuzione dell'applicazione di streaming Spark continuerà fino a quando non viene terminata. A questo scopo, usare il comando seguente:

    curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/1"

### <a name="run-the-applications-to-receive-the-events-into-an-azure-storage-blob-as-json"></a>Eseguire le applicazioni per ricevere gli eventi in un BLOB di archiviazione di Azure come JSON
Aprire un prompt dei comandi, passare alla directory in cui è installato CURL ed eseguire il comando seguente (sostituire nome utente/password e nome cluster):

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputJSON.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

I parametri nel file **inputJSON.txt** sono definiti come segue:

    { "file":"wasb:///example/jars/spark-streaming-data-persistence-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToAzureBlobAsJSON", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--event-store-folder", "/EventStore10"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

I parametri sono simili a quanto specificato per l'output di testo nel passaggio precedente. Anche in questo caso non è necessario creare le cartelle di output (EventCheckpoint, EventCount/EventCount10) usate come parametri, perché vengono create automaticamente dall'applicazione di streaming.

 Dopo l'esecuzione del comando, è possibile esaminare l'account di archiviazione di Azure associato al cluster, dove sarà visualizzata la cartella **/EventStore10** creata. Aprire un file qualsiasi con prefisso **part-** dove saranno visualizzati gli eventi elaborati in un formato JSON.

### <a name="run-the-applications-to-receive-the-events-into-a-hive-table"></a>Eseguire le applicazioni per ricevere gli eventi in una tabella Hive
Per eseguire l'applicazione di streaming Spark che trasmette eventi a una tabella Hive, sono necessari alcuni componenti aggiuntivi. Si tratta di:

* datanucleus-api-jdo-3.2.6.jar
* datanucleus-rdbms-3.2.9.jar
* datanucleus-core-3.2.10.jar
* hive-site.xml

I file con estensione **jar** sono disponibili nel cluster HDInsight Spark in `/usr/hdp/current/spark-client/lib`. Il file **hive-site.xml** è disponibile in `/usr/hdp/current/spark-client/conf`.

È possibile usare [WinScp](http://winscp.net/eng/download.php) per copiare i file dal cluster nel computer locale. È quindi possibile usare strumenti per copiare questi file nell'account di archiviazione associato al cluster. Per altre informazioni su come caricare i file nell'account di archiviazione, vedere [Caricare dati per processi Hadoop in HDInsight](hdinsight-upload-data.md).

Dopo avere copiato i file nell'account di archiviazione di Azure, aprire un prompt dei comandi, passare alla directory in cui è installato CURL ed eseguire il comando seguente (sostituire nome utente/password e nome cluster):

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputHive.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

I parametri nel file **inputHive.txt** sono definiti come segue:

    { "file":"wasb:///example/jars/spark-streaming-data-persistence-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToHiveTable", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--event-hive-table", "EventHiveTable10" ], "jars":["wasb:///example/jars/datanucleus-api-jdo-3.2.6.jar", "wasb:///example/jars/datanucleus-rdbms-3.2.9.jar", "wasb:///example/jars/datanucleus-core-3.2.10.jar"], "files":["wasb:///example/jars/hive-site.xml"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

I parametri sono simili a quanto specificato per l'output di testo nei passaggi precedenti. Anche in questo caso non è necessario creare la tabella Hive di output (EventHiveTable10) o le cartelle di output (EventCheckpoint, EventCount/EventCount10) usate come parametri, perché vengono create automaticamente dall'applicazione di streaming. Si noti che le opzioni **jars** e **files** includono i percorsi per i file JAR e il file hive-site.xml copiato nell'account di archiviazione.

Per verificare che la tabella Hive sia stata creata correttamente, è possibile usare SSH nel cluster ed eseguire query Hive. Per istruzioni, vedere [Usare Hive con Hadoop in HDInsight tramite SSH](hdinsight-hadoop-use-hive-ssh.md). Una volta connessi tramite SSH, è possibile eseguire il comando seguente per verificare che la tabella Hive, **EventHiveTable10**, venga creata.

    show tables;

L'output dovrebbe essere simile al seguente:

    OK
    eventhivetable10
    hivesampletable

È anche possibile eseguire una query SELECT per visualizzare il contenuto della tabella.

    SELECT * FROM eventhivetable10 LIMIT 10;

Verrà visualizzato un output simile al seguente:

    ZN90apUSQODDTx7n6Toh6jDbuPngqT4c
    sor2M7xsFwmaRW8W8NDwMneFNMrOVkW1
    o2HcsU735ejSi2bGEcbUSB4btCFmI1lW
    TLuibq4rbj0T9st9eEzIWJwNGtMWYoYS
    HKCpPlWFWAJILwR69MAq863nCWYzDEw6
    Mvx0GQOPYvPR7ezBEpIHYKTKiEhYammQ
    85dRppSBSbZgThLr1s0GMgKqynDUqudr
    5LAWkNqorLj3ZN9a2mfWr9rZqeXKN4pF
    ulf9wSFNjD7BZXCyunozecov9QpEIYmJ
    vWzM3nvOja8DhYcwn0n5eTfOItZ966pa
    Time taken: 4.434 seconds, Fetched: 10 row(s)


### <a name="run-the-applications-to-receive-the-events-into-an-azure-sql-database-table"></a>Eseguire le applicazioni per ricevere gli eventi in una tabella di database SQL di Azure
Prima di eseguire questo passaggio, assicurarsi che sia stato creato un database SQL di Azure. Per istruzioni, vedere [Creare un database SQL in pochi minuti](../sql-database/sql-database-get-started.md). Per completare questa sezione saranno necessari i valori per il nome del database, il nome del server di database e le credenziali di amministratore del database come parametri. Non è però necessario creare la tabella di database, perché viene creata automaticamente dall'applicazione di streaming Spark.

Aprire un prompt dei comandi, passare alla directory in cui è installato CURL ed eseguire il comando seguente:

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputSQL.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

I parametri nel file **inputSQL.txt** sono definiti come segue:

    { "file":"wasb:///example/jars/spark-streaming-data-persistence-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToAzureSQLTable", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--sql-server-fqdn", "<database-server-name>.database.windows.net", "--sql-database-name", "mysparkdatabase", "--database-username", "sparkdbadmin", "--database-password", "<put-password-here>", "--event-sql-table", "EventContent" ], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Per verificare che l'applicazione venga eseguita correttamente, è possibile connettersi al database SQL di Azure con SQL Server Management Studio. Per istruzioni su come eseguire questa operazione, vedere [Connettersi al database SQL con SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md). Una volta connessi al database, è possibile passare alla tabella il **EventContent** creata dall'applicazione di streaming. È possibile eseguire una query rapida per ottenere i dati dalla tabella. Eseguire questa query:

    SELECT * FROM EventCount

L'output dovrebbe essere simile al seguente:

    00046b0f-2552-4980-9c3f-8bba5647c8ee
    000b7530-12f9-4081-8e19-90acd26f9c0c
    000bc521-9c1b-4a42-ab08-dc1893b83f3b
    00123a2a-e00d-496a-9104-108920955718
    0017c68f-7a4e-452d-97ad-5cb1fe5ba81b
    001KsmqL2gfu5ZcuQuTqTxQvVyGCqPp9
    001vIZgOStka4DXtud0e3tX7XbfMnZrN
    00220586-3e1a-4d2d-a89b-05c5892e541a
    0029e309-9e54-4e1b-84be-cd04e6fce5ec
    003333cf-874f-4045-9da3-9f98c2b4ea49
    0043c07e-8d73-420a-9af7-1fcb94575356
    004a11a9-0c2c-4bc0-a7d5-2e0ebd947ab9


## <a name="seealso"></a>Vedere anche
* [Panoramica: Apache Spark su Azure HDInsight](hdinsight-apache-spark-overview.md)
* [Design of Receiver-based Connection and Direct DStream](https://www.slideshare.net/NanZhu/seattle-sparkmeetup032317)

### <a name="scenarios"></a>Scenari
* [Spark con Business Intelligence: eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](hdinsight-apache-spark-use-bi-tools.md)
* [Spark con Machine Learning: utilizzare Spark in HDInsight per l'analisi della temperatura di compilazione utilizzando dati HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark con Machine Learning: usare Spark in HDInsight per prevedere i risultati del controllo degli alimenti](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Analisi dei log del sito Web con Spark in HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Creare ed eseguire applicazioni
* [Creare un'applicazione autonoma con Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Eseguire processi in modalità remota in un cluster Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Strumenti ed estensioni
* [Usare il plug-in degli strumenti HDInsight per IntelliJ IDEA per creare e inviare applicazioni Spark in Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely (Usare il plug-in Strumenti HDInsight per IntelliJ IDEA per eseguire il debug di applicazioni Spark in remoto)](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usare i notebook di Zeppelin con un cluster Spark in HDInsight](hdinsight-apache-spark-zeppelin-notebook.md)
* [Kernel disponibili per notebook di Jupyter nel cluster Spark per HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Usare pacchetti esterni con i notebook Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Installare Jupyter Notebook nel computer e connetterlo a un cluster HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestire risorse
* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Tenere traccia ed eseguire il debug di processi in esecuzione nel cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]: ../storage-create-storage-account/

