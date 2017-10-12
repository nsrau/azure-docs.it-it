---
title: Elaborare eventi da Hub eventi con Storm - Azure HDInsight | Microsoft Docs
description: Informazioni su come elaborare i dati degli Hub eventi di Azure con una topologia Storm C# creata in Visual Studio tramite HDInsight Tools per Visual Studio.
services: hdinsight,notification hubs
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 67f9d08c-eea0-401b-952b-db765655dad0
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/03/2017
ms.author: larryfr
ms.openlocfilehash: 4b6fd87b057d93175d3ef284238d77be3bdde61d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-c"></a>Elaborare eventi dell'hub eventi di Azure con Storm in HDInsight (C#)

Informazioni su come usare Hub eventi di Azure da Apache Storm in HDInsight. In questo documento viene usata una topologia Storm con C# per leggere e scrivere dati da Hub eventi

> [!NOTE]
> Per la versione Java di questo progetto, vedere [Elaborare eventi dell'hub eventi di Azure con Storm in HDInsight (Java)](hdinsight-storm-develop-java-event-hub-topology.md).

## <a name="scpnet"></a>SCP.NET

Nella procedura in questo documento viene usato SCP.NET, un pacchetto NuGet che semplifica la creazione di topologie e componenti C# per l'uso con Storm in HDInsight.

> [!IMPORTANT]
> Anche se i passaggi illustrati in questo documento si basano su un ambiente di sviluppo Windows con Visual Studio, il progetto compilato può essere inviato a Storm in un cluster HDInsight che usa Linux. Solo i cluster basati su Linux creati dopo il 28 ottobre 2016 supportano le topologie SCP.NET.

HDInsight versione 3.4 o successiva usano Mono per eseguire le topologie C#. L'esempio in questo documento funziona con HDInsight 3.6. Se si prevede di creare soluzioni .NET personalizzate per HDInsight, leggere il documento [Mono Compatibility](http://www.mono-project.com/docs/about-mono/compatibility/) (Compatibilità di Mono) per individuare potenziali incompatibilità.

### <a name="cluster-versioning"></a>Controllo delle versioni del cluster

Il pacchetto NuGet Microsoft.SCP.Net.SDK usato per il progetto deve corrispondere alla versione principale di Storm installata in HDInsight. HDInsight versioni 3.5 e 3.6 usa Storm versione 1.x, pertanto è necessario usare SCP.NET versione 1.0.x.x con questi cluster.

> [!IMPORTANT]
> L'esempio in questo documento presuppone un cluster HDInsight 3.5 o 3.6.
>
> Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere la sezione relativa al [ritiro di HDInsight in Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

Le topologie C# devono inoltre puntare a .NET 4.5.

## <a name="how-to-work-with-event-hubs"></a>Come lavorare con gli Hub eventi

Microsoft fornisce un set di componenti Java da usare per comunicare con gli Hub eventi da una topologia Storm. Il file di archivio Java (JAR) che contiene una versione compatibile di HDInsight 3.6 di questi componenti è disponibile in [https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar).

> [!IMPORTANT]
> I componenti sono scritti in Java ma è comunque possibile usarli da una topologia C#.

Nell'esempio vengono usati i componenti seguenti:

* __EventHubSpout__: legge i dati dagli Hub eventi.
* __EventHubBolt__: scrive i dati negli Hub eventi.
* __EventHubSpoutConfig__: usato per configurare EventHubSpout.
* __EventHubBoltConfig__: usato per configurare EventHubBolt.

### <a name="example-spout-usage"></a>Esempio di uso di uno spout

SCP.NET offre metodi per l'aggiunta di EventHubSpout alla topologia. Questi metodi facilitano l'aggiunta di uno spout rispetto ai metodi generici di aggiunta di un componente Java. L'esempio seguente illustra come creare uno spout usando i metodi __SetEventHubSpout__ ed **EventHubSpoutConfig** forniti da SCP.NET:

```csharp
 topologyBuilder.SetEventHubSpout(
    "EventHubSpout",
    new EventHubSpoutConfig(
        ConfigurationManager.AppSettings["EventHubSharedAccessKeyName"],
        ConfigurationManager.AppSettings["EventHubSharedAccessKey"],
        ConfigurationManager.AppSettings["EventHubNamespace"],
        ConfigurationManager.AppSettings["EventHubEntityPath"],
        eventHubPartitions),
    eventHubPartitions);
```

Nell'esempio precedente viene creato un nuovo componente spout denominato __EventHubSpout__, che viene poi configurato per comunicare con un Hub eventi. Per il componente viene impostato anche l'hint di parallelismo sul numero di partizioni nell'Hub eventi. Questa impostazione consente a Storm di creare un'istanza del componente per ogni partizione.

### <a name="example-bolt-usage"></a>Esempio di utilizzo di bolt

Usare il metodo **JavaComponmentConstructor** per creare un'istanza del bolt. L'esempio seguente illustra come creare e configurare una nuova istanza di **EventHubBolt**:

```csharp
// Java construcvtor for the Event Hub Bolt
JavaComponentConstructor constructor = JavaComponentConstructor.CreateFromClojureExpr(
    String.Format(@"(org.apache.storm.eventhubs.bolt.EventHubBolt. (org.apache.storm.eventhubs.bolt.EventHubBoltConfig. " +
        @"""{0}"" ""{1}"" ""{2}"" ""{3}"" ""{4}"" {5}))",
        ConfigurationManager.AppSettings["EventHubPolicyName"],
        ConfigurationManager.AppSettings["EventHubPolicyKey"],
        ConfigurationManager.AppSettings["EventHubNamespace"],
        "servicebus.windows.net",
        ConfigurationManager.AppSettings["EventHubName"],
        "true"));

// Set the bolt to subscribe to data from the spout
topologyBuilder.SetJavaBolt(
    "eventhubbolt",
    constructor,
    partitionCount)
        .shuffleGrouping("Spout");
```

> [!NOTE]
> In questo esempio, per creare un **EventHubBoltConfig** viene usata un'espressione Clojure passata come stringa e non **JavaComponentConstructor**, come nell'esempio dello spout. Entrambi i metodi sono validi ed è possibile scegliere quello ritenuto migliore.

## <a name="download-the-completed-project"></a>Scaricare il progetto completo

È possibile scaricare la versione completa del progetto creato in questa esercitazione da [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub). Sarà tuttavia necessario fornire le impostazioni di configurazione seguendo la procedura riportata in questa esercitazione.

### <a name="prerequisites"></a>Prerequisiti

* Un [cluster Apache Storm in HDInsight versione 3.5 o 3.6](hdinsight-apache-storm-tutorial-get-started.md).

    > [!WARNING]
    > Per l'esempio usato in questo documento è necessario Storm in HDInsight versione 3.5 o 3.6. Questo approccio non funzionerà con le versioni precedenti di HDInsight a causa di modifiche importanti apportate al nome della classe. Per una versione di questo esempio funziona con i cluster precedenti, vedere [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub/releases).

* Un [Hub eventi di Azure](../event-hubs/event-hubs-csharp-ephcs-getstarted.md).

* [Azure .NET SDK](http://azure.microsoft.com/downloads/).

* [HDInsight Tools per Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).

* Java JDK 1.8 o versione successiva nell'ambiente di sviluppo. Sono disponibili download di JDK da [Oracle](http://www.oracle.com/technetwork/java/javase/downloads/index.html).

  * La variabile di ambiente **JAVA_HOME** deve puntare alla directory contenente Java.
  * La directory **%JAVA_HOME%/bin** deve essere inclusa nel percorso.

## <a name="download-the-event-hubs-components"></a>Scaricare i componenti di Hub eventi

Scaricare il componente spout e bolt di Hub eventi da [https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar).

Creare una directory denominata `eventhubspout` e salvare il file in tale directory.

## <a name="configure-event-hubs"></a>Configurare gli hub eventi

L'hub eventi è l'origine dati per questo esempio. Usare le informazioni contenute nella sezione "Creare un hub eventi" di [Introduzione all'Hub eventi](../event-hubs/event-hubs-csharp-ephcs-getstarted.md).

1. Dopo avere creato l'hub eventi, visualizzare il pannello **Hub eventi** nel portale di Azure e selezionare **Criteri di accesso condivisi**. Selezionare **+ Aggiungi** per aggiungere i criteri seguenti:

   | Nome | Autorizzazioni |
   | --- | --- |
   | writer |Invio |
   | reader |Attesa |

    ![Schermata della finestra Criteri di accesso condivisi](./media/hdinsight-storm-develop-csharp-event-hub-topology/sas.png)

2. Selezionare i criteri **reader** and **writer**. Copiare e salvare i valori di chiave primaria per entrambi i criteri, perché verranno usati in un secondo momento.

## <a name="configure-the-eventhubwriter"></a>Configurare EventHubWriter

1. Se la versione più recente di HDInsight Tools per Visual Studio non è ancora installata, vedere [Introduzione all'uso di HDInsight Tools per Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).

2. Scaricare la soluzione da [eventhub-storm-hybrid](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

3. Nel progetto **EventHubWriter** aprire il file **App.config**. Usare le informazioni dell'hub eventi configurato prima per inserire il valore per le chiavi seguenti:

   | Chiave | Valore |
   | --- | --- |
   | EventHubPolicyName |writer (se è stato usato un altro nome per il criterio con l'autorizzazione *Send*, usare l'altro nome) |
   | EventHubPolicyKey |Chiave per il criterio writer. |
   | EventHubNamespace |Spazio dei nomi contenente l'hub eventi. |
   | EventHubName |Nome dell'hub eventi. |
   | EventHubPartitionCount |Numero di partizioni nell'hub eventi. |

4. Salvare e chiudere il file **App.config**.

## <a name="configure-the-eventhubreader"></a>Configurare EventHubReader

1. Aprire il progetto **EventHubReader**.

2. Aprire il file **App.config** per **EventHubReader**. Usare le informazioni dell'hub eventi configurato prima per inserire il valore per le chiavi seguenti:

   | Chiave | Valore |
   | --- | --- |
   | EventHubPolicyName |reader (se è stato usato un altro nome per il criterio con l'autorizzazione *listen*, usare l'altro nome) |
   | EventHubPolicyKey |Chiave per il criterio reader. |
   | EventHubNamespace |Spazio dei nomi contenente l'hub eventi. |
   | EventHubName |Nome dell'hub eventi. |
   | EventHubPartitionCount |Numero di partizioni nell'hub eventi. |

3. Salvare e chiudere il file **App.config**.

## <a name="deploy-the-topologies"></a>Distribuire le topologie

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **EventHubReader** e scegliere Submit to Storm on HDInsight **(Invia a Storm in HDInsight)**.

    ![Schermata di Esplora soluzioni con Submit to Storm on HDInsight (Invia a Storm in HDInsight) evidenziato](./media/hdinsight-storm-develop-csharp-event-hub-topology/submittostorm.png)

2. Nella finestra di dialogo **Submit Topology** (Invia topologia) selezionare il **cluster Storm**. Espandere **Additional Configurations** (Configurazioni aggiuntive), selezionare **Java File Paths** (Percorsi file Java), selezionare **...** e quindi selezionare la directory contenente il file JAR scaricato in precedenza. Infine fare clic su **Submit**.

    ![Schermata della finestra di dialogo Submit Topology (Invia topologia)](./media/hdinsight-storm-develop-csharp-event-hub-topology/submit.png)

3. Dopo l'invio della topologia, verrà visualizzato **Storm Topologies Viewer** (Visualizzatore topologie Storm). Selezionare la topologia **EventHubReader** nel riquadro a sinistra per visualizzare le relative informazioni.

    ![Schermata Storm Topologies Viewer (Visualizzatore topologie Storm)](./media/hdinsight-storm-develop-csharp-event-hub-topology/topologyviewer.png)

4. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **EventHubWriter** e scegliere **Submit to Storm on HDInsight** (Invia a Storm in HDInsight).

5. Nella finestra di dialogo **Submit Topology** (Invia topologia) selezionare il **cluster Storm**. Espandere **Additional Configurations** (Configurazioni aggiuntive), selezionare **Java File Paths** (Percorsi file Java), selezionare **...** e quindi selezionare la directory contenente il file JAR scaricato in precedenza. Infine fare clic su **Submit**.

6. Dopo l'invio della topologia, aggiornare l'elenco delle topologie in **Storm Topologies Viewer** per verificare che siano entrambe in esecuzione nel cluster.

7. In **Storm Topologies Viewer** (Visualizzatore topologie Storm) selezionare la topologia **EventHubReader**.

8. Per aprire il riepilogo componenti per il bolt, fare doppio clic sul componente **LogBolt** nel diagramma.

9. Nella sezione **Executors** (Esecutori) selezionare uno dei collegamenti nella colonna **Porta**. Verranno visualizzate le informazioni registrate dal componente. Le informazioni registrate sono simili al testo seguente:

        2017-03-02 14:51:29.255 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,255 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1830978598,"deviceId":"8566ccbc-034d-45db-883d-d8a31f34068e"}
        2017-03-02 14:51:29.283 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,283 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1756413275,"deviceId":"647a5eff-823d-482f-a8b4-b95b35ae570b"}
        2017-03-02 14:51:29.313 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,312 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1108478910,"deviceId":"206a68fa-8264-4d61-9100-bfdb68ee8f0a"}

## <a name="stop-the-topologies"></a>Arrestare le topologie

Per arrestare le topologie, selezionarle singolarmente in **Storm Topology Viewer** (Visualizzatore topologie Storm) e quindi fare clic su **Termina**.

![Schermata Storm Topology Viewer (Visualizzatore topologie Storm), con il pulsante Kill (Termina) evidenziato](./media/hdinsight-storm-develop-csharp-event-hub-topology/killtopology.png)

## <a name="delete-your-cluster"></a>Eliminare il cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come usare lo spout e il bolt degli Hub eventi Java da una topologia C# per utilizzare i dati nell'hub eventi di Azure. Per altre informazioni sulla creazione di topologie C#, vedere gli articoli seguenti:

* [Sviluppare topologie C# per Apache Storm in HDInsight tramite Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)
* [Guida alla programmazione SCP](hdinsight-storm-scp-programming-guide.md)
* [Topologie di esempio per Storm in HDInsight](hdinsight-storm-example-topology.md)
