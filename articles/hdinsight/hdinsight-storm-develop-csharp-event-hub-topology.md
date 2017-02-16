---
title: Elaborare eventi di Hub eventi con Storm in HDInsight | Documentazione Microsoft
description: Informazioni su come elaborare i dati dell&quot;hub eventi con una topologia Storm C# creata in Visual Studio tramite HDInsight Tools per Visual Studio.
services: hdinsight,notification hubs
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 67f9d08c-eea0-401b-952b-db765655dad0
ms.service: hdinsight
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/12/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: c9a5091973395dd888939432292fbd06dcbf0680
ms.openlocfilehash: c0349b5890a75c6ffaa6b7eca93baa3101912cf6


---
# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-c"></a>Elaborare eventi dell'hub eventi di Azure con Storm in HDInsight (C#)

L'hub eventi di Azure consente di elaborare grandi quantità di dati da siti Web, app e dispositivi. Lo spout dell'hub eventi semplifica l'uso di Apache Storm in HDInsight per l'analisi di questi dati in tempo reale. È anche possibile scrivere dati nell'hub eventi da Storm usando il relativo bolt.

Questa esercitazione spiega come usare i modelli di Visual Studio installati con Strumenti HDInsight per Visual Studio per creare due topologie che funzionano con gli hub eventi di Azure.

* **EventHubWriter**: genera dati casualmente e li scrive nell'hub eventi.
* **EventHubReader**: legge i dati da Hub eventi e li registra nei log di Storm.

> [!NOTE] 
> Per la versione Java di questo progetto, vedere [Elaborare eventi dell'hub eventi di Azure con Storm in HDInsight (Java)](hdinsight-storm-develop-java-event-hub-topology.md).

## <a name="scpnet"></a>SCP.NET

Questi progetti utilizzano SCP.NET, un pacchetto NuGet che semplifica la creazione di topologie e componenti C# per l'uso con Storm in HDInsight.

> [!IMPORTANT]
> Anche se i passaggi illustrati in questo documento si basano su un ambiente di sviluppo Windows con Visual Studio, il progetto compilato può essere inviato a Storm in un cluster HDInsight che usa Linux. __Solo i cluster basati su Linux creati dopo il 28/10/2016 supportano le topologie SCP.NET.__

### <a name="cluster-versioning"></a>Controllo delle versioni del cluster

Il pacchetto NuGet Microsoft.SCP.Net.SDK usato dal progetto deve corrispondere alla versione principale di Storm installata in HDInsight. Storm in HDInsight versioni 3.3 e 3.4 usa Storm versione 0.10.x, pertanto è necessario usare SCP.NET versione 0.10.x.x con questi cluster. HDInsight 3.5 usa Storm 1.0.x, pertanto è necessario usare la versione 1.0.x.x di SCP.NET con questa versione del cluster.

> [!IMPORTANT]
> Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere [HDInsight deprecato in Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

HDInsight versione 3.4 o successiva usano Mono per eseguire le topologie C#. Anche se non dovrebbero verificarsi problemi con Mono, è consigliabile vedere il documento sulla [compatibilità di Mono](http://www.mono-project.com/docs/about-mono/compatibility/) per le potenziali incompatibilità.

Le topologie C# devono inoltre puntare a .NET 4.5.

## <a name="how-to-work-with-event-hubs"></a>Come lavorare con gli Hub eventi

Microsoft fornisce un set di componenti Java da usare per comunicare con l'Hub eventi di Azure da una topologia Storm. È possibile trovare il file .JAR contenente la versione più recente di questi componenti alla pagina [https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/).

> [!IMPORTANT]
> I componenti sono scritti in Java ma è comunque possibile usarli da una topologia C#.

I componenti che verranno usati principalmente sono:

* __EventHubSpout__: legge i dati dagli Hub eventi.
* __EventHubBolt__: scrive i dati negli Hub eventi.
* __EventHubSpoutConfig__: usato per configurare EventHubSpout.
* __EventHubBoltConfig__: usato per configurare EventHubBolt.
* __UnicodeEventDataScheme__: usato per configurare lo spout da usare con la codifica UTF-8 durante la lettura dall'Hub eventi. Se non viene utilizzato, per impostazione predefinita lo spout usa la codifica String.

### <a name="example-spout-usage"></a>Esempio di uso di uno spout

SCP.NET offre metodi specifici per aggiungere EventHubSpout alla topologia. Questi metodi facilitano l'aggiunta di uno spout rispetto ai metodi generici di aggiunta di un componente Java. Nell'esempio seguente viene illustrato come creare un nuovo spout usando i metodi __SetEventHubSpout__ ed EventHubSpoutConfig forniti da SCP.NET:

```csharp
topologyBuilder.SetEventHubSpout(
    "EventHubSpout",
    new EventHubSpoutConfig(
        // the shared access signature name and key used to read the data
        ConfigurationManager.AppSettings["EventHubSharedAccessKeyName"],
        ConfigurationManager.AppSettings["EventHubSharedAccessKey"],
        // The namespace that contains the Event Hub to read from
        ConfigurationManager.AppSettings["EventHubNamespace"],
        // The Event Hub name to read from
        ConfigurationManager.AppSettings["EventHubEntityPath"],
        // The number of partitions in the Event Hub
        eventHubPartitions),
    // Parallelism hint for this component. Should be set to the partition count.
    eventHubPartitions);
```

Nell'esempio precedente viene creato un nuovo componente spout denominato __EventHubSpout__, che viene poi configurato per comunicare con un Hub eventi. Si noti che per il componente viene anche impostato l'hint di parallelismo sul numero di partizioni nell'Hub eventi. In questo modo Storm può creare un'istanza del componente per ogni partizione.

> [!WARNING]
> A partire dal 1° gennaio 2017, usando i metodi SetEventHubSpout ed EventHubSpoutConfig viene creato uno spout che usa la codifica String durante la lettura dei dati dagli Hub eventi. Se è necessario usare la codifica UTF-8, vedere l'esempio seguente.

È inoltre possibile usare il metodo JavaCompoentConstructor generico durante la creazione di uno spout. Nell'esempio seguente viene illustrato come creare un nuovo spout utilizzando il metodo JavaComponentConstructor. Viene inoltre illustrato come configurare lo spout per leggere i dati tramite una codifica UTF-8 invece di String:

```csharp
// Create an instance of UnicodeEventDataScheme
var schemeConstructor = new JavaComponentConstructor("com.microsoft.eventhubs.spout.UnicodeEventDataScheme");
// Create an instance of EventHubSpoutConfig
var eventHubSpoutConfig = new JavaComponentConstructor(
    "com.microsoft.eventhubs.spout.EventHubSpoutConfig",
    new List<Tuple<string, object>>()
    {
        // the shared access signature name and key used to read the data
        Tuple.Create<string, object>(JavaComponentConstructor.JAVA_LANG_STRING, ConfigurationManager.AppSettings["EventHubSharedAccessKeyName"]),
        Tuple.Create<string, object>(JavaComponentConstructor.JAVA_LANG_STRING, ConfigurationManager.AppSettings["EventHubSharedAccessKey"]),
        // The namespace that contains the Event Hub to read from
        Tuple.Create<string, object>(JavaComponentConstructor.JAVA_LANG_STRING, ConfigurationManager.AppSettings["EventHubNamespace"]),
        // The Event Hub name to read from
        Tuple.Create<string, object>(JavaComponentConstructor.JAVA_LANG_STRING, ConfigurationManager.AppSettings["EventHubEntityPath"]),
        // The number of partitions in the Event Hub
        Tuple.Create<string, object>("int", eventHubPartitions),
        // The encoding scheme to use when reading
        Tuple.Create<string, object>("com.microsoft.eventhubs.spout.IEventDataScheme", schemeConstructor)
    }
    );
// Create an instance of the spout
var eventHubSpout = new JavaComponentConstructor(
    "com.microsoft.eventhubs.spout.EventHubSpout",
    new List<Tuple<string, object>>()
    {
        Tuple.Create<string, object>("com.microsoft.eventhubs.spout.EventHubSpoutConfig", eventHubSpoutConfig)
    }
    );
// Set the spout in the topology
topologyBuilder.SetJavaSpout("EventHubSpout", eventHubSpout, eventHubPartitions);
```

> [!IMPORTANT]
> UnicodeEventDataScheme è disponibile solo nella versione 9.5 dei componenti di Hub eventi, disponibile all'indirizzo [https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/).

### <a name="example-bolt-usage"></a>Esempio di uso di bolt

È necessario usare il metodo JavaComponmentConstructor per creare un'istanza del bolt. Nell'esempio seguente viene illustrato come creare e configurare una nuova istanza di EventHubBolt:

```csharp
//Create constructor for the Java bolt
JavaComponentConstructor constructor =
    // Use a Clojure expression to create the EventHubBoltCOnfig
    JavaComponentConstructor.CreateFromClojureExpr(
        String.Format(@"(org.apache.storm.eventhubs.bolt.EventHubBolt. (org.apache.storm.eventhubs.bolt.EventHubBoltConfig. " +
        @"""{0}"" ""{1}"" ""{2}"" ""{3}"" ""{4}"" {5}))",
    // The policy name and key used to read from Event Hubs
    ConfigurationManager.AppSettings["EventHubPolicyName"],
    ConfigurationManager.AppSettings["EventHubPolicyKey"],
    // The namespace that contains the Event Hub
    ConfigurationManager.AppSettings["EventHubNamespace"],
    "servicebus.windows.net", //suffix for the namespace fqdn
    // The Evetn Hub Name)
    ConfigurationManager.AppSettings["EventHubName"],
    "true"));

//Set the bolt
topologyBuilder.SetJavaBolt(
        "EventHubBolt",
        constructor,
        partitionCount). //Parallelism hint uses partition count
    shuffleGrouping("Spout"); //Consume data from spout
```

> [!NOTE]
> In questo esempio viene usata un'espressione Clojure passata come stringa, anziché usare JavaComponentConstructor per creare un EventHubBoltConfig separato come nell'esempio Spout. Entrambi i metodi sono validi, l'utente può pertanto scegliere il metodo preferito.

## <a name="download-the-completed-project"></a>Scaricare il progetto completo

È possibile scaricare la versione completa del progetto creato in questa esercitazione all'indirizzo GitHub: [eventhub-storm-hybrid](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub). Sarà tuttavia necessario fornire le impostazioni di configurazione seguendo la procedura riportata in questa esercitazione.

### <a name="prerequisites"></a>Prerequisiti

* Un [cluster Apache Storm in HDInsight versione 3.5](hdinsight-apache-storm-tutorial-get-started.md)

    > [!WARNING]
    > Per l'esempio usato in questo documento è necessario Storm su HDInsight versione 3.5. Esistono differenze nei nomi delle classi usati per i componenti principali di Storm tra le versioni in cluster meno recenti e la versione di Storm inclusa in HDInsight 3.5. Per una versione di questo esempio utilizzabile in cluster meno recenti, vedere [https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub/releases](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub/releases).

* Un [Hub eventi di Azure](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

* L’ [Azure .NET SDK](http://azure.microsoft.com/downloads/)

* L’ [HDInsight Tools per Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md)

* Java JDK 1.7 o versione successiva nell'ambiente di sviluppo. I download di JDK sono disponibili alla pagina [http://www.oracle.com/technetwork/java/javase/downloads/index.html](http://www.oracle.com/technetwork/java/javase/downloads/index.html).

  * La variabile di ambiente **JAVA_HOME** deve puntare alla directory contenente Java.
  * La directory **%JAVA_HOME%/bin** deve essere inclusa nel percorso.

## <a name="download-the-event-hub-components"></a>Scaricare i componenti di Hub eventi

Lo spout e il bolt vengono distribuiti come un singolo file di archivio Java (con estensione jar) denominato **eventhubs-storm-spout-#.#-jar-with-dependencies.jar**, dove #.# è la versione del file.

È possibile scaricare una versione del file jar compatibile con Storm in HDInsight versione 3.5 alla pagina [https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/).

Creare una nuova directory denominata `eventhubspout` e salvare il file in tale directory.

## <a name="configure-event-hubs"></a>Configurare gli hub eventi

L'hub eventi è l'origine dati per questo esempio. Usare le informazioni contenute nella sezione **Creare un hub eventi** del documento [Introduzione all'Hub eventi](../event-hubs/event-hubs-csharp-ephcs-getstarted.md).

1. Dopo avere creato l'hub eventi, visualizzare il pannello Hub eventi nel portale di Azure e selezionare **Criteri di accesso condivisi**. Usare la voce **+ Aggiungi** per aggiungere i criteri seguenti:
   
   | Nome | Autorizzazioni |
   | --- | --- |
   | writer |Invio |
   | reader |Attesa |
   
    ![criteri](./media/hdinsight-storm-develop-csharp-event-hub-topology/sas.png)

2. Selezionare i criteri **reader** and **writer**. Copiare e salvare il valore **CHIAVE PRIMARIA** per entrambi i criteri, perché verrà usato in un secondo momento.

## <a name="configure-the-eventhubwriter"></a>Configurare EventHubWriter

1. Se la versione più recente di HDInsight Tools per Visual Studio non è ancora installata, vedere [Introduzione all'uso di HDInsight Tools per Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).

2. Scaricare la soluzione da [eventhub-storm-hybrid](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub). Aprire la soluzione e cercare nel codice il progetto **EventHubWriter**.

3. Nel progetto **EventHubWriter** aprire il file **App.config**. Usare le informazioni dell'hub eventi configurato prima per inserire il valore per le chiavi seguenti:
   
   | Chiave | Valore |
   | --- | --- |
   | EventHubPolicyName |writer (se è stato usato un altro nome per il criterio con l'autorizzazione *Send*, usare l'altro nome) |
   | EventHubPolicyKey |Chiave per il criterio writer |
   | EventHubNamespace |Spazio dei nomi contenente l'hub eventi |
   | EventHubName |Nome dell'hub eventi |
   | EventHubPartitionCount |Numero di partizioni nell'hub eventi |

4. Salvare e chiudere il file **App.config**.

## <a name="configure-the-eventhubreader"></a>Configurare EventHubReader

1. Aprire il progetto **EventHubReader** ed esaminare il codice.

2. Aprire **App.config** per **EventHubWriter**. Usare le informazioni dell'hub eventi configurato prima per inserire il valore per le chiavi seguenti:
   
   | Chiave | Valore |
   | --- | --- |
   | EventHubPolicyName |reader (se è stato usato un altro nome per il criterio con l'autorizzazione *listen*, usare l'altro nome) |
   | EventHubPolicyKey |Chiave per il criterio reader |
   | EventHubNamespace |Spazio dei nomi contenente l'hub eventi |
   | EventHubName |Nome dell'hub eventi |
   | EventHubPartitionCount |Numero di partizioni nell'hub eventi |

3. Salvare e chiudere il file **App.config**.

## <a name="deploy-the-topologies"></a>Distribuire le topologie

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **EventHubReader** e scegliere Submit to Storm on HDInsight **(Invia a Storm in HDInsight)**.
   
    ![invio a storm](./media/hdinsight-storm-develop-csharp-event-hub-topology/submittostorm.png)

2. Nella schermata **Submit Topology** (Invia topologia) selezionare il **cluster Storm**. Espandere **Additional Configurations** (Configurazioni aggiuntive), selezionare **Java File Paths** (Percorsi file Java), selezionare **...** e quindi selezionare la directory contenente il file JAR scaricato in precedenza. Infine fare clic su **Submit**.
   
    ![immagine della finestra di dialogo di invio](./media/hdinsight-storm-develop-csharp-event-hub-topology/submit.png)

3. Dopo l'invio della topologia, verrà aperto **Storm Topologies Viewer** . Selezionare la topologia **EventHubReader** a sinistra della finestra di dialogo per visualizzare le relative statistiche. Non dovrebbe essere in corso alcuna operazione, perché nell'hub eventi non sono ancora stati scritti eventi.
   
    ![visualizzazione di archiviazione di esempio](./media/hdinsight-storm-develop-csharp-event-hub-topology/topologyviewer.png)

4. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **EventHubWriter** e scegliere **Submit to Storm on HDInsight** (Invia a Storm in HDInsight).

5. Nella schermata **Submit Topology** (Invia topologia) selezionare il **cluster Storm**. Espandere **Additional Configurations** (Configurazioni aggiuntive), selezionare **Java File Paths** (Percorsi file Java), selezionare **...** e quindi selezionare la directory contenente il file JAR scaricato in precedenza. Infine fare clic su **Submit**.

6. Dopo l'invio della topologia, aggiornare l'elenco delle topologie in **Storm Topologies Viewer** per verificare che siano entrambe in esecuzione nel cluster.

7. In **Storm Topologies Viewer** (Visualizzatore topologie Storm), selezionare la topologia **EventHubReader**.

8. Nella visualizzazione grafico fare doppio clic sul componente **LogBolt**. Verrà aperta la pagina **Riepilogo componenti** per il bolt.

9. Nella sezione **Executors** (Esecutori) selezionare uno dei collegamenti nella colonna **Porta**. Verranno visualizzate le informazioni registrate dal componente. Le informazioni registrate sono simili alle seguenti:
   
        2016-10-20 13:26:44.186 m.s.s.b.ScpNetBolt [INFO] Processing tuple: source: com.microsoft.eventhubs.spout.EventHubSpout:7, stream: default, id: {5769732396213255808=520853934697489134}, [{"deviceId":3,"deviceValue":1379915540}]
        2016-10-20 13:26:44.234 m.s.s.b.ScpNetBolt [INFO] Processing tuple: source: com.microsoft.eventhubs.spout.EventHubSpout:7, stream: default, id: {7154038361491319965=4543766486572976404}, [{"deviceId":3,"deviceValue":459399321}]
        2016-10-20 13:26:44.335 m.s.s.b.ScpNetBolt [INFO] Processing tuple: source: com.microsoft.eventhubs.spout.EventHubSpout:6, stream: default, id: {513308780877039680=-7571211415704099042}, [{"deviceId":5,"deviceValue":845561159}]
        2016-10-20 13:26:44.445 m.s.s.b.ScpNetBolt [INFO] Processing tuple: source: com.microsoft.eventhubs.spout.EventHubSpout:7, stream: default, id: {-2409895457033895206=5479027861202203517}, [{"deviceId":8,"deviceValue":2105860655}]

## <a name="stop-the-topologies"></a>Arrestare le topologie

Per arrestare le topologie, selezionarle singolarmente in **Storm Topology Viewer** (Visualizzatore topologie Storm) e quindi fare clic su **Termina**.

![immagine dell'arresto di una topologia](./media/hdinsight-storm-develop-csharp-event-hub-topology/killtopology.png)

## <a name="delete-your-cluster"></a>Eliminare il cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come usare lo spout e il bolt dell'hub eventi Java da una topologia C# per utilizzare i dati nell'hub eventi di Azure. Per altre informazioni sulla creazione di topologie C#, vedere gli articoli seguenti.

* [Sviluppare topologie C# per Apache Storm in HDInsight tramite Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)
* [Guida alla programmazione SCP](hdinsight-storm-scp-programming-guide.md)
* [Topologie di esempio per Storm in HDInsight](hdinsight-storm-example-topology.md)




<!--HONumber=Jan17_HO3-->


