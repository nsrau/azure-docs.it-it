---
title: Analizzare i dati dei sensori con Apache Storm e HBase | Documentazione Microsoft
description: Informazioni su come connettersi ad Apache Storm con una rete virtuale. Usare Storm con HBase per elaborare i dati del sensore da un hub eventi e visualizzarli con D3.js.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: a9a1ac8e-5708-4833-b965-e453815e671f
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/21/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 34445eff89ff7ff513893d5b36b937075b09f599
ms.lasthandoff: 03/22/2017


---
# <a name="analyze-sensor-data-with-apache-storm-event-hub-and-hbase-in-hdinsight-hadoop"></a>Analizzare i dati del sensore con Apache Storm, hub eventi e HBase in HDInsight (Hadoop)

Informazioni su come usare Apache Storm in HDInsight per elaborare i dati del sensore dall'hub eventi di Azure. I dati vengono quindi archiviati in Apache HBase in HDInsight e visualizzati con D3.js.

Il modello di Azure Resource Manager usato in questo documento illustra come creare più risorse di Azure in un gruppo di risorse. Il modello crea una rete virtuale di Azure, due cluster HDInsight (Storm e HBase) e un'app Web di Azure. Un'implementazione di Node. js di un dashboard Web in tempo reale viene distribuita automaticamente all'app web.

> [!NOTE]
> Le informazioni e l'esempio riportati in questo documento richiedono HDInsight versione 3.5.
>
> Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere [HDInsight deprecato in Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Vedere [Ottenere una versione di valutazione gratuita di Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
  
  > [!IMPORTANT]
  > Non è necessario un cluster HDInsight esistente. La procedura descritta in questo documento consente di creare le risorse seguenti:
  > 
  > * una rete virtuale di Azure
  > * Un cluster Storm in HDInsight (basato su Linux con due nodi del ruolo di lavoro)
  > * Un cluster HBase in HDInsight (basato su Linux con due nodi del ruolo di lavoro)
  > * Un'app Web di Azure che ospita il dashboard Web

* [Node.js](http://nodejs.org/): viene usato per visualizzare in anteprima il dashboard Web in locale nell'ambiente di sviluppo.
* [Java e JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/index.html): usati per sviluppare la topologia Storm.
* [Maven](http://maven.apache.org/what-is-maven.html): usato per generare e compilare il progetto.
* [Git](http://git-scm.com/): usato per scaricare il progetto da GitHub.
* Client **SSH** : usato per connettersi al cluster HDInsight basato su Linux. Per altre informazioni sull'uso di SSH con HDInsight, vedere i documenti seguenti:
  
  * [Usare SSH (PuTTY) con HDInsight da un client Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
  * [Usare SSH con HDInsight da un client Linux, Unix, OS X o Bash in Windows 10](hdinsight-hadoop-linux-use-ssh-unix.md)
    
    > [!NOTE]
    > È anche necessario avere accesso al comando `scp` che viene usato per copiare i file tra l'ambiente di sviluppo locale e il cluster HDInsight tramite SSH.


## <a name="architecture"></a>Architettura

![diagramma dell'architettura](./media/hdinsight-storm-sensor-data-analysis/devicesarchitecture.png)

Questo esempio è costituito dai componenti seguenti:

* **Hub eventi di Azure**: include i dati raccolti dai sensori.
* **Storm in HDInsight**: assicura l'elaborazione in tempo reale dei dati provenienti dall'hub eventi.
* **HBase in HDInsight**: fornisce un archivio dati NoSQL persistente per i dati, dopo l'elaborazione tramite Storm.
* **Servizio Rete virtuale di Azure**: abilita la comunicazione sicura tra i cluster Storm in HDInsight e HBase in HDInsight.
  
  > [!NOTE]
  > Quando si usa l'API del client Java HBase, è necessaria una rete virtuale non esposta nel gateway pubblico per i cluster HBase. L'installazione di cluster HBase e Storm nella stessa rete virtuale consente al cluster Storm, o a qualsiasi altro sistema nella rete virtuale, di accedere direttamente a HBase tramite l'API del client.

* **Sito Web del dashboard**: dashboard di esempio che rappresenta graficamente i dati in tempo reale.
  
  * Il sito Web viene implementato in Node.js ed è quindi possibile eseguirlo a scopo di test su qualsiasi sistema operativo client. In alternativa, è possibile distribuirlo in Siti Web di Azure.
  * [Socket.io](http://socket.io/) viene usato per le comunicazioni in tempo reale tra la topologia Storm e il sito Web.
    
    > [!NOTE]
    > L'uso di Socket.io per la comunicazione è un dettaglio di implementazione. È possibile usare qualsiasi framework di comunicazione, ad esempio WebSocket o SignalR non elaborato.

  * [D3.js](http://d3js.org/) viene usato per creare un grafico dei dati inviati al sito Web.

> [!IMPORTANT]
> Sono necessari due cluster perché non è supportato alcun metodo per la creazione di un unico cluster HDInsight per Storm e HBase.

La topologia legge i dati dall'hub eventi tramite la classe [org.apache.storm.eventhubs.spout.EventHubSpout](http://storm.apache.org/releases/0.10.1/javadocs/org/apache/storm/eventhubs/spout/class-use/EventHubSpout.html) e scrive i dati in HBase usando la classe [org.apache.storm.hbase.bolt.HBaseBolt](https://storm.apache.org/javadoc/apidocs/org/apache/storm/hbase/bolt/class-use/HBaseBolt.html). Per la comunicazione con il sito Web viene usato [socket.io-client.java](https://github.com/nkzawa/socket.io-client.java).

Nel diagramma seguente viene illustrato il layout della topologia:

![diagramma della topologia](./media/hdinsight-storm-sensor-data-analysis/sensoranalysis.png)

> [!NOTE]
> Si tratta di una visualizzazione semplificata della topologia. In fase di esecuzione, per ciascuna partizione dell'hub eventi in fase di lettura viene creata un'istanza di ogni componente. Queste istanze vengono distribuite tra i nodi del cluster e i dati vengono instradati tra di essi, come indicato di seguito:
> 
> * I dati trasmessi dallo spout al parser sono sottoposti a bilanciamento del carico.
> * I dati dal parser al dashboard e ad HBase vengono raggruppati per ID dispositivo, in modo che il flusso di messaggi provenienti dallo stesso dispositivo siano indirizzati sempre allo stesso componente.

### <a name="topology-components"></a>Componenti della topologia

* **EventHub Spout**: lo spout viene fornito come parte di Apache Storm 0.10.0 e versioni successive.
  
  > [!NOTE]
  > Lo spout dell'hub eventi usato in questo esempio richiede un cluster Storm in HDInsight versione 3.3 o 3.4. Per informazioni sull'uso di eventi con una versione precedente di Storm in HDInsight, vedere [Elaborare eventi dell'hub eventi di Azure con Storm in HDInsight (Java)](hdinsight-storm-develop-java-event-hub-topology.md).

* **ParserBolt.java**: i dati generati dallo spout sono dati JSON non elaborati. In alcuni casi vengono generati più eventi alla volta. Questo bolt mostra come leggere i dati generati dallo spout e indirizzarli a un nuovo flusso sotto forma di una tupla contenente più campi.
* **DashboardBolt.java**: questo componente mostra come usare la libreria client Socket.io per Java per inviare dati in tempo reale al dashboard Web.
* **Temperature.java**: definisce la topologia e carica i dati di configurazione dal file **Config.properties**.

## <a name="prepare-your-environment"></a>Preparare l'ambiente

Prima di usare questo esempio, è necessario creare un hub eventi di Azure, che viene letto dalla topologia Storm.

### <a name="configure-event-hub"></a>Configurare l'hub eventi

L'hub eventi è l'origine dati per questo esempio. Per creare un nuovo hub eventi, seguire questa procedura.

1. Dal [portale di Azure](https://portal.azure.com) selezionare **+ Nuovo** -> **Internet delle cose** -> **Hub eventi**.
2. Nel pannello **Crea spazio dei nomi** , eseguire le operazioni seguenti:
   
   1. Immettere un **nome** per lo spazio dei nomi.
   2. Selezione di un piano tariffario. **Basic** è sufficiente per questo esempio.
   3. Selezionare la **sottoscrizione** di Azure da usare.
   4. Selezionare un gruppo di risorse esistente o crearne uno nuovo.
   5. Selezionare il **percorso** per l'hub eventi.
   6. Selezionare **Aggiungi al dashboard** e quindi fare clic su **Crea**.

3. Al termine del processo di creazione, viene visualizzato il pannello Hub eventi per lo spazio dei nomi. Da qui, scegliere **+ Aggiungi hub eventi**. Nel pannello **Crea hub eventi** immettere un nome di **sensordata** e selezionare **Crea**. Mantenere i valori predefiniti per gli altri campi.
4. Dal pannello Hub eventi per lo spazio dei nomi, selezionare **Hub eventi**. Selezionare la voce **sensordata** .
5. Nel pannello Hub eventi per sensordata, selezionare **Criteri di accesso condiviso**. Utilizzare il collegamento **+ Aggiungi** per aggiungere i seguenti criteri:

    | Nome criterio | Claims |
    | ----- | ----- |
    | devices | Invio |
    | storm | Attesa |

1. Selezionare entrambi i criteri e annotare il valore **PRIMARY KEY** . Il valore di entrambi i criteri servirà nei passaggi successivi.

## <a name="download-and-configure-the-project"></a>Scaricare e configurare il progetto

Usare il comando seguente per scaricare il progetto da GitHub.

    git clone https://github.com/Blackmist/hdinsight-eventhub-example

Dopo aver eseguito il comando, si otterrà la struttura di directory seguente:

    hdinsight-eventhub-example/
        TemperatureMonitor/ - this contains the topology
            resources/
                log4j2.xml - set logging to minimal.
                hbase-site.xml - connection information for the HBase cluster.
                Config.properties - configuration information for the topology. How to read from Event Hub and the URI to the dashboard.
            src/ - the Java bolts and topology definition.
        dashboard/nodejs/ - this is the node.js web dashboard.
        SendEvents/ - utilities to send fake sensor data.

> [!NOTE]
> Questo documento non specifica i dettagli completi del codice incluso nell'esempio. Il codice, tuttavia, è completamente commentato.

Aprire il file **hdinsight-eventhub-example/TemperatureMonitor/resources/Config.propertiess** e aggiungere le informazioni dell'hub eventi alle righe seguenti:

    eventhubspout.username = <shared access policy name that can read from Event Hub>
    eventhubspout.password = <shared access policy key>
    eventhubspout.namespace = <namespace of your Event Hub
    eventhubspout.entitypath = <name of your event hub>
    eventhubspout.partitions.count = 2

Salvare il file dopo aver aggiunto tali informazioni.

## <a name="compile-and-test-locally"></a>Compilare ed eseguire il test in locale

Prima di eseguire il test, è necessario avviare il dashboard per visualizzare l'output della topologia e generare i dati da archiviare nell'hub eventi.

> [!IMPORTANT]
> Il componente HBase di questa topologia non è attivo durante il test locale, perché l'API Java per il cluster HBase non è accessibile dall'esterno della rete virtuale di Azure che contiene i cluster.

### <a name="start-the-web-application"></a>Avviare l'applicazione Web

1. Aprire un nuovo terminal o prompt dei comandi e passare alla directory **hdinsight-eventhub-example/dashboard**. Usare il comando seguente per installare le dipendenze richieste dall'applicazione Web:
   
        npm install

2. Usare il comando seguente per avviare l'applicazione Web:
   
        node server.js
   
    Verrà visualizzato un messaggio simile al testo seguente:
   
        Server listening at port 3000

3. Aprire un Web browser e immettere **http://localhost:3000/** come indirizzo. Verrà visualizzata una pagina simile all'immagine seguente:
   
    ![dashboard Web](./media/hdinsight-storm-sensor-data-analysis/emptydashboard.png)
   
    Lasciare il prompt dei comandi o il terminal aperto. Al termine del test, usare CTRL+C per arrestare il server Web.

### <a name="start-generating-data"></a>Avviare la generazione dei dati

> [!NOTE]
> I passaggi descritti in questa sezione usano Node.js, così da poter essere usati su qualsiasi piattaforma. Per esempi in altri linguaggi, vedere la directory **SendEvents** .

1. Aprire un nuovo terminal, prompt o shell e modificare le directory in **hdinsight-eventhub-example/SendEvents/nodejs**. Usare il comando seguente per installare le dipendenze richieste dall'applicazione:
   
        npm install

2. Aprire il file **app.js** in un editor di testo e aggiungere le informazioni relative all'hub eventi ottenute in precedenza:
   
        // ServiceBus Namespace
        var namespace = 'YourNamespace';
        // Event Hub Name
        var hubname ='sensordata';
        // Shared access Policy name and key (from Event Hub configuration)
        var my_key_name = 'devices';
        var my_key = 'YourKey';
   
   > [!NOTE]
   > Questo esempio presuppone che sia stato usato **sensordata** come nome dell'hub eventi e **dispositivi** come nome del criterio dotato di un'attestazione **Invia**.

3. Usare il comando seguente per inserire nuove voci nell'hub eventi:
   
        node app.js
   
    Verranno visualizzate diverse righe di output contenenti i dati inviati all'hub eventi:
   
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"0","Temperature":7}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"1","Temperature":39}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"2","Temperature":86}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"3","Temperature":29}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"4","Temperature":30}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"5","Temperature":5}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"6","Temperature":24}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"7","Temperature":40}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"8","Temperature":43}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"9","Temperature":84}

### <a name="start-the-topology"></a>Avviare la topologia

1. Aprire un nuovo prompt dei comandi, shell o terminal e modificare le directory su **hdinsight-eventhub-example/TemperatureMonitor**, quindi usare il comando seguente per avviare la topologia:
   
        mvn compile exec:java
   
    Questo comando avvia la topologia in modalità locale. I valori contenuti nel file **Config.properties** assicurano le informazioni di connessione per gli hub eventi e il sito Web del dashboard locale. Una volta avviata, la topologia legge le voci dall'hub eventi e le invia al dashboard in esecuzione sul computer locale. Nel dashboard Web dovrebbero comparire linee simili a quelle nell'immagine seguente:
   
    ![dashboard con dati](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

2. Mentre il dashboard è in esecuzione, usare il comando `node app.js` dai passaggi precedenti per inviare nuovi dati agli hub eventi. Poiché i valori di temperatura vengono generati in modo casuale, il grafico deve aggiornarsi per visualizzare le modifiche estese della temperatura.
   
   > [!NOTE]
   > Per usare il comando `node app.js` è necessario trovarsi nella directory **hdinsight-eventhub-example/SendEvents/Nodejs**.

3. Dopo aver verificato il corretto aggiornamento del dashboard, arrestare la topologia usando CTRL+C. È possibile usare CTRL+C anche per arrestare il server Web locale.

## <a name="create-a-storm-and-hbase-cluster"></a>Creare un cluster Storm e HBase

Per i passaggi in questa sezione si usa un [modello di Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md) per creare una rete virtuale di Azure e un cluster Storm e HBase nella rete virtuale. Il modello crea anche un'app Web di Azure e consente di distribuire una copia del dashboard al suo interno.

> [!NOTE]
> Viene usata una rete virtuale in modo che la topologia in esecuzione nel cluster Storm possa comunicare direttamente con il cluster HBase tramite l'API Java HBase.

Il modello di Resource Manager usato in questo documento si trova in un contenitore BLOB pubblico, **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hbase-storm-cluster-in-vnet.json**.

1. Fare clic sul pulsante seguente per accedere ad Azure e aprire il modello di Resource Manager nel portale di Azure.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-storm-cluster-in-vnet-3.5.json" target="_blank"><img src="./media/hdinsight-storm-sensor-data-analysis/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Compilare i campi seguenti del pannello **Distribuzione personalizzata**:
   
    ![Parametri di HDInsight](./media/hdinsight-storm-sensor-data-analysis/parameters.png)
   
   * **Base Cluster Name** (Nome di base del cluster): questo valore viene usato come nome di base per i cluster Storm e HBase. Ad esempio, se si immette **abc** verranno creati un cluster Storm denominato **storm-abc** e un cluster HBase denominato **hbase-abc**.
   * **Cluster Login User Name** (Nome utente di accesso del cluster): il nome utente amministratore per i cluster Storm e HBase.
   * **Password dell'account di accesso del cluster**: la password amministratore per i cluster Storm e HBase.
   * **Nome utente SSH**: l'utente SSH da creare per i cluster Storm e HBase.
   * **Password SSH**: la password dell'utente SSH per i cluster Storm e HBase.
   * **Location** (Località): area in cui vengono creati i cluster.
     
     Fare clic su **OK** per salvare i parametri.

3. Usare la sezione **Generale** per creare un gruppo di risorse o selezionarne uno esistente.
4. Nel menu a discesa **Località del gruppo di risorse** selezionare la stessa località selezionata per il parametro **Località** nella sezione **Impostazioni**.
5. Leggere le condizioni, quindi selezionare **Accetto le condizioni riportate sopra**.
6. Selezionare infine **Aggiungi al dashboard** e quindi **Acquista**. La creazione dei cluster richiede circa 20 minuti.

Dopo aver creato le risorse, si viene reindirizzati a un pannello del gruppo di risorse che contiene i cluster e il dashboard Web.

![Pannello Gruppo di risorse per la rete virtuale e i cluster](./media/hdinsight-storm-sensor-data-analysis/groupblade.png)

> [!IMPORTANT]
> Si noti che i nomi dei cluster HDInsight sono **storm-BASENAME** e **hbase-BASENAME**, dove BASENAME è il nome specificato per il modello. Questi nomi verranno usati in un passaggio successivo per la connessione ai cluster. Si noti inoltre che il nome del sito dashboard è **basename-dashboard**. Questo valore viene usato più avanti in questo documento.

## <a name="configure-the-dashboard-bolt"></a>Configurare il bolt Dashboard

Per inviare dati al dashboard distribuito come un'app Web, è necessario modificare la riga seguente nel file **Config.properties**:

    dashboard.uri: http://localhost:3000

Modificare `http://localhost:3000` in `http://BASENAME-dashboard.azurewebsites.net` e salvare il file. Sostituire **BASENAME** con il nome di base fornito nel passaggio precedente. È anche possibile usare il gruppo di risorse creato in precedenza per selezionare il dashboard e visualizzare l'URL.

## <a name="create-the-hbase-table"></a>Creare una tabella HBase

Per archiviare i dati in HBase, è necessario creare prima di tutto una tabella. Creare prima le risorse in cui Storm dovrà scrivere, perché creare risorse dall'interno di una topologia Storm può comportare la creazione di più istanze che tentano di creare la stessa risorsa. Creare le risorse all'esterno della topologia e usare Storm per operazioni di lettura/scrittura e analisi.

1. Usare SSH per connettersi al cluster HBase usando il nome utente e la password SSH forniti nel modello durante la creazione del cluster. Ad esempio, se la connessione viene stabilita tramite il comando `ssh` , usare la sintassi seguente:
   
        ssh USERNAME@hbase-BASENAME-ssh.azurehdinsight.net
   
    In questo comando sostituire **USERNAME** con il nome utente SSH specificato durante la creazione del cluster e **BASENAME** con il nome di base specificato. Quando richiesto, immettere la password per l'utente SSH.

2. Dalla sessione SSH avviare la shell di HBase.
   
        hbase shell
   
    Dopo il caricamento della shell, verrà visualizzato un prompt `hbase(main):001:0>`.

3. Dalla shell HBase immettere il comando seguente per creare una tabella in cui archiviare i dati del sensore:
   
        create 'SensorData', 'cf'

4. Verificare la corretta creazione della tabella con il comando seguente:
   
        scan 'SensorData'
   
    Tale comando restituisce informazioni simili alle seguenti, che indicano che nella tabella ci sono 0 righe disponibili.
   
        ROW                   COLUMN+CELL                                       0 row(s) in 0.1900 seconds

5. Immettere `exit` per uscire dalla shell HBase:

## <a name="configure-the-hbase-bolt"></a>Configurare il bolt HBase

Per scrivere in HBase dal cluster Storm, è necessario fornire al bolt HBase i dettagli di configurazione del cluster HBase. Questo esempio usa il file **hbase-site.xml** del cluster HBase.

### <a name="download-the-hbase-sitexml"></a>Salvare il file hbase-site.xml

A un prompt dei comandi usare SCP per scaricare il file **hbase-site.xml** dal cluster. Nell'esempio seguente sostituire **USERNAME** con il nome utente SSH specificato durante la creazione del cluster e **BASENAME** con il nome di base specificato in precedenza. Quando richiesto, immettere la password per l'utente SSH. Sostituire `/path/to/TemperatureMonitor/resources/hbase-site.xml` con il percorso del file nel progetto TemperatureMonitor.

    scp USERNAME@hbase-BASENAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml /path/to/TemperatureMonitor/resources/hbase-site.xml

Questo comando scarica **hbase-site.xml** nel percorso specificato.

### <a name="enable-the-hbase-bolt"></a>Abilitare il bolt HBase

Per abilitare il componente bolt HBase aprire il file **TemperatureMonitor/src/main/java/com/microsoft/examples/Temperature.java** e rimuovere i commenti dalla righe seguenti:

    // topologyBuilder.setBolt("HBase", new HBaseBolt("SensorData", mapper).withConfigKey("hbase.conf"), spoutConfig.getPartitionCount())
    //  .fieldsGrouping("Parser", "hbasestream", new Fields("deviceid")).setNumTasks(spoutConfig.getPartitionCount());

Dopo aver rimosso le righe, salvare il file.

## <a name="build-package-and-deploy-the-solution-to-hdinsight"></a>Compilare la soluzione, creare il pacchetto e distribuirla in HDInsight

Nell'ambiente di sviluppo seguire questa procedura per distribuire la topologia Temperature nel cluster Storm.

1. Dalla directory **TemperatureMonitor** , eseguire una nuova compilazione e creare un pacchetto JAR dal progetto utilizzando il comando seguente:
   
        mvn clean compile package
   
    Questo comando crea un file denominato **TemperatureMonitor-1.0-SNAPSHOT.jar** nella directory di **destinazione** del progetto.

2. Usare scp per caricare il file **TemperatureMonitor-1.0-SNAPSHOT.jar** nel cluster Storm. Nell'esempio seguente sostituire **USERNAME** con il nome utente SSH specificato durante la creazione del cluster e **BASENAME** con il nome di base specificato in precedenza. Quando richiesto, immettere la password per l'utente SSH.
   
        scp target/TemperatureMonitor-1.0-SNAPSHOT.jar USERNAME@storm-BASENAME-ssh.azurehdinsight.net:TemperatureMonitor-1.0-SNAPSHOT.jar

   > [!NOTE]
   > Il caricamento del file può richiedere alcuni minuti.

3. Una volta completato il caricamento, connettersi al cluster tramite SSH.
   
        ssh USERNAME@storm-BASENAME-ssh.azurehdinsight.net

4. Nella sessione SSH usare il comando seguente per avviare la topologia:
   
        storm jar TemperatureMonitor-1.0-SNAPSHOT.jar com.microsoft.examples.Temperature temperature

5. Dopo aver avviato la topologia, aprire un browser al sito Web pubblicato in Azure e quindi usare il comando `node app.js` per inviare dati all'hub eventi. Verrà visualizzato il dashboard Web aggiornato per mostrare le informazioni.
   
    ![dashboard](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

## <a name="view-hbase-data"></a>Visualizzare i dati di HBase

Usare la procedura seguente per connettersi a HBase e verificare che i dati siano stati scritti nella tabella:

1. Usare SSH per connettersi al cluster HBase.
   
        ssh USERNAME@hbase-BASENAME-ssh.azurehdinsight.net

2. Dalla sessione SSH avviare la shell di HBase.
   
        hbase shell
   
    Dopo il caricamento della shell, verrà visualizzato un prompt `hbase(main):001:0>`.

3. Visualizzare le righe dalla tabella:
   
        scan 'SensorData'
   
    Questo comando restituisce informazioni simili al testo seguente, che indicano che nella tabella sono presenti dati.
   
        hbase(main):002:0> scan 'SensorData'
        ROW                             COLUMN+CELL
        \x00\x00\x00\x00               column=cf:temperature, timestamp=1467290788277, value=\x00\x00\x00\x04
        \x00\x00\x00\x00               column=cf:timestamp, timestamp=1467290788277, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x01               column=cf:temperature, timestamp=1467290788348, value=\x00\x00\x00M
        \x00\x00\x00\x01               column=cf:timestamp, timestamp=1467290788348, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x02               column=cf:temperature, timestamp=1467290788268, value=\x00\x00\x00R
        \x00\x00\x00\x02               column=cf:timestamp, timestamp=1467290788268, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x03               column=cf:temperature, timestamp=1467290788269, value=\x00\x00\x00#
        \x00\x00\x00\x03               column=cf:timestamp, timestamp=1467290788269, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x04               column=cf:temperature, timestamp=1467290788356, value=\x00\x00\x00>
        \x00\x00\x00\x04               column=cf:timestamp, timestamp=1467290788356, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x05               column=cf:temperature, timestamp=1467290788326, value=\x00\x00\x00\x0D
        \x00\x00\x00\x05               column=cf:timestamp, timestamp=1467290788326, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x06               column=cf:temperature, timestamp=1467290788253, value=\x00\x00\x009
        \x00\x00\x00\x06               column=cf:timestamp, timestamp=1467290788253, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x07               column=cf:temperature, timestamp=1467290788229, value=\x00\x00\x00\x12
        \x00\x00\x00\x07               column=cf:timestamp, timestamp=1467290788229, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x08               column=cf:temperature, timestamp=1467290788336, value=\x00\x00\x00\x16
        \x00\x00\x00\x08               column=cf:timestamp, timestamp=1467290788336, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x09               column=cf:temperature, timestamp=1467290788246, value=\x00\x00\x001
        \x00\x00\x00\x09               column=cf:timestamp, timestamp=1467290788246, value=2015-02-10T14:43.05.00320Z
        10 row(s) in 0.1800 seconds
   
   > [!NOTE]
   > Questa operazione di analisi restituisce un massimo di 10 righe dalla tabella.

## <a name="delete-your-clusters"></a>Eliminare i cluster
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Per eliminare il cluster, la risorsa di archiviazione e l'app Web contemporaneamente, eliminare il gruppo di risorse che li contiene.

## <a name="next-steps"></a>Passaggi successivi

Per altri esempi di topologie Storm con HDInsight, vedere [Topologie di esempio per Storm in HDInsight](hdinsight-storm-example-topology.md).

Per altre informazioni su Apache Storm, visitare il sito Web [Apache Storm](https://storm.incubator.apache.org/) .

Per altre informazioni su HBase in HDInsight, vedere la [panoramica su HBase con HDInsight](hdinsight-hbase-overview.md)

Per altre informazioni su Socket.io, visitare il sito Web [socket.io](http://socket.io/) .

Per altre informazioni su D3.js, vedere [D3.js - Data Driven Documents](http://d3js.org/).

Per altre informazioni sulla creazione di topologie in Java, vedere [Sviluppo di topologie basate su Java per Apache Storm in HDInsight](hdinsight-storm-develop-java-topology.md).

Per altre informazioni sulla creazione di topologie in .NET, vedere [Sviluppare topologie C# per Apache Storm in HDInsight tramite Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

[azure-portal]: https://portal.azure.com

