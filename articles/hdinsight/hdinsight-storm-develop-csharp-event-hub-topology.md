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
ms.date: 10/27/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 77d0dfe7e09baab9b923b3c49af0cb7c28cd8625


---
# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-c"></a>Elaborare eventi dell'hub eventi di Azure con Storm in HDInsight (C#)
L'hub eventi di Azure consente di elaborare grandi quantità di dati da siti Web, app e dispositivi. Lo spout dell'hub eventi semplifica l'uso di Apache Storm in HDInsight per l'analisi di questi dati in tempo reale. È anche possibile scrivere dati nell'hub eventi da Storm usando il relativo bolt.

Questa esercitazione spiega come usare i modelli di Visual Studio installati con Strumenti HDInsight per Visual Studio per creare due topologie che funzionano con gli hub eventi di Azure.

* **EventHubWriter**: genera dati casualmente e li scrive nell'hub eventi.
* **EventHubReader**: legge i dati da Hub eventi e li registra nei log di Storm.

> [!NOTE]
> Anche se i passaggi illustrati in questo documento si basano su un ambiente di sviluppo Windows con Visual Studio, il progetto compilato può essere inviato a un cluster HDInsight basato su Linux o su Windows. Solo i cluster basati su Linux creati dopo il 28/10/2016 supportano le topologie SCP.NET.
> 
> Per usare una topologia C# con un cluster basato su Linux, è necessario aggiornare il pacchetto NuGet Microsoft.SCP.Net.SDK usato dal progetto alla versione 0.10.0.6 o successiva. La versione del pacchetto deve anche corrispondere alla versione principale di Storm installata in HDInsight. Ad esempio, le versioni 3.3 e 3.4 di Storm in HDInsight usano Storm versione 0.10.x, mentre HDInsight 3.5 usa Storm 1.0.x.
> 
> Le topologie C# nei cluster basati su Linux devono usare .NET 4.5 e devono usare Mono per l'esecuzione nel cluster HDInsight. Anche se non dovrebbero verificarsi problemi, è consigliabile vedere il documento sulla [compatibilità di Mono](http://www.mono-project.com/docs/about-mono/compatibility/) per le potenziali incompatibilità.
> 
> Per una versione Java di questo progetto, che funzionerà anche in un cluster basato su Windows o Linux, vedere [Elaborare gli eventi da hub eventi di Azure con Storm in HDInsight (Java)](hdinsight-storm-develop-java-event-hub-topology.md).
> 
> 

## <a name="prerequisites"></a>Prerequisiti
* Un [cluster Apache Storm in HDInsight](hdinsight-apache-storm-tutorial-get-started.md)
* Un [Hub eventi di Azure](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
* L’ [Azure .NET SDK](http://azure.microsoft.com/downloads/)
* L’ [HDInsight Tools per Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md)

## <a name="completed-project"></a>Progetto completato
È possibile scaricare la versione completa del progetto creato in questa esercitazione all'indirizzo GitHub: [eventhub-storm-hybrid](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub). Sarà tuttavia necessario fornire le impostazioni di configurazione seguendo la procedura riportata in questa esercitazione.

## <a name="event-hubs-spout-and-bolt"></a>Spout e bolt dell'hub eventi
Lo spout e il bolt dell'hub eventi sono componenti Java che facilitano l'uso dell'hub da Apache Storm. Anche se questi componenti sono scritti in Java, con HDInsight Tools per Visual Studio è possibile creare topologie ibride che combinano componenti C# e Java.

Lo spout e il bolt vengono distribuiti come un singolo file di archivio Java (con estensione jar) denominato **eventhubs-storm-spout-#.#-jar-with-dependencies.jar**, dove #.# è la versione del file.

### <a name="download-the-jar-file"></a>Scaricare il file con estensione jar
La versione più recente del file JAR è disponibile nella cartella **lib/eventhubs** del progetto con gli [esempi per Storm in HDInsight](https://github.com/hdinsight/hdinsight-storm-examples). Per scaricare il file, usare uno dei metodi seguenti.

> [!NOTE]
> Per lo spout e il bolt è stata inviata la richiesta di inclusione nel progetto Apache Storm. Per altre informazioni, vedere l'articolo relativo a [STORM-583: archiviazione iniziale per hub eventi basati su Storm](https://github.com/apache/storm/pull/336/files) in GitHub.
> 
> 

* **Scaricare un file ZIP**: nella pagina degli [esempi per Storm in HDInsight](https://github.com/hdinsight/hdinsight-storm-examples) selezionare **Download ZIP (Scarica ZIP)** nel riquadro destro e scaricare un file con estensione zip contenente il progetto.
  
    ![pulsante download zip](./media/hdinsight-storm-develop-csharp-event-hub-topology/download.png)
  
    Dopo aver completato il download, è possibile estrarre l'archivio. Il file sarà disponibile nella directory **lib**.
* **Clonare il progetto**: se [Git](http://git-scm.com/) è installato, usare il comando seguente per clonare il repository in locale, quindi trovare il file nella directory **lib**.
  
        git clone https://github.com/hdinsight/hdinsight-storm-examples

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
2. Nella schermata **Submit Topology** (Invia topologia) selezionare il **cluster Storm**. Espandere **Configurazioni aggiuntive**, selezionare **Java File Paths** (Percorsi file Java), fare clic su **...** e quindi selezionare la directory contenente il file **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** scaricato prima. Infine fare clic su **Submit**.
   
    ![immagine della finestra di dialogo di invio](./media/hdinsight-storm-develop-csharp-event-hub-topology/submit.png)
3. Dopo l'invio della topologia, verrà aperto **Storm Topologies Viewer** . Selezionare la topologia **EventHubReader** a sinistra della finestra di dialogo per visualizzare le relative statistiche. Non dovrebbe essere in corso alcuna operazione, perché nell'hub eventi non sono ancora stati scritti eventi.
   
    ![visualizzazione di archiviazione di esempio](./media/hdinsight-storm-develop-csharp-event-hub-topology/topologyviewer.png)
4. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **EventHubWriter** e scegliere **Submit to Storm on HDInsight** (Invia a Storm in HDInsight).
5. Nella schermata **Submit Topology** (Invia topologia) selezionare il **cluster Storm**. Espandere **Configurazioni aggiuntive**, selezionare **Java File Paths** (Percorsi file Java), fare clic su **...** e selezionare la directory contenente il file **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** scaricato prima. Infine fare clic su **Submit**.
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

## <a name="notes"></a>Note
### <a name="checkpointing"></a>Checkpoint
EventHubSpout controlla periodicamente il proprio stato nel nodo Zookeeper, operazione che salva l'offset corrente per i messaggi letti dalla coda. Questo consente al componente di avviare la ricezione di messaggi in corrispondenza dell'offset salvato negli scenari seguenti:

* L'istanza del componente ha esito negativo e viene riavviata.
* Il cluster viene aumentato o ridotto mediante l'aggiunta o la rimozione di nodi.
* La topologia viene arrestata e riavviata **con lo stesso nome**.

È inoltre possibile esportare e importare i checkpoint persistenti in WASB (servizio Archiviazione di Azure usato dal cluster HDInsight.) Gli script per eseguire questa operazione si trovano in Storm, nel cluster HDInsight, in **c:\apps\dist\storm-0.9.3.2.2.1.0-2340\zkdatatool-1.0\bin**.

> [!NOTE]
> Il numero di versione indicato nel percorso può essere diverso, poiché la versione di Storm installata nel cluster può cambiare.
> 
> 

Gli script presenti in questa directory sono:

* **stormmeta_import.cmd**: importa tutti i metadati Storm dal contenitore di archiviazione predefinito del cluster in Zookeeper.
* **stormmeta_export.cmd**: esporta tutti i metadati Storm da Zookeeper al contenitore di archiviazione predefinito del cluster.
* **stormmeta_delete.cmd**: elimina tutti i metadati Storm da Zookeeper.

L'esportazione di un'importazione consente di rendere persistenti i dati del punto di controllo quando è necessario eliminare il cluster ma si desidera riprendere l'elaborazione dall'offset corrente nell'hub quando si riporta un nuovo cluster online.

> [!NOTE]
> Poiché i dati vengono mantenuti nel contenitore di archiviazione predefinito, il nuovo cluster **deve** usare gli stessi account e contenitore di archiviazione del cluster precedente.
> 
> 

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come usare lo spout e il bolt dell'hub eventi Java da una topologia C# per utilizzare i dati nell'hub eventi di Azure. Per altre informazioni sulla creazione di topologie C#, vedere gli articoli seguenti.

* [Sviluppare topologie C# per Apache Storm in HDInsight tramite Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)
* [Guida alla programmazione SCP](hdinsight-storm-scp-programming-guide.md)
* [Topologie di esempio per Storm in HDInsight](hdinsight-storm-example-topology.md)




<!--HONumber=Nov16_HO3-->


