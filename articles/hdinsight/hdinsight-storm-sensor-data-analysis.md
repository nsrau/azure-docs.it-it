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
ms.date: 08/09/2017
ms.author: larryfr
ms.translationtype: HT
ms.sourcegitcommit: 760543dc3880cb0dbe14070055b528b94cffd36b
ms.openlocfilehash: 0d1cc959c87bd64ed728f8b56c9b9156fa492a8b
ms.contentlocale: it-it
ms.lasthandoff: 08/10/2017

---
# <a name="analyze-sensor-data-with-apache-storm-event-hub-and-hbase-in-hdinsight-hadoop"></a>Analizzare i dati del sensore con Apache Storm, hub eventi e HBase in HDInsight (Hadoop)

Informazioni su come usare Apache Storm in HDInsight per elaborare i dati del sensore dall'hub eventi di Azure. I dati vengono quindi archiviati in Apache HBase in HDInsight e visualizzati con D3.js.

Il modello di Azure Resource Manager usato in questo documento illustra come creare più risorse di Azure in un gruppo di risorse. Il modello crea una rete virtuale di Azure, due cluster HDInsight (Storm e HBase) e un'app Web di Azure. Un'implementazione di Node. js di un dashboard Web in tempo reale viene distribuita automaticamente all'app web.

> [!NOTE]
> Le informazioni e l'esempio riportati in questo documento richiedono HDInsight versione 3.6.
>
> Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere la sezione relativa al [ritiro di HDInsight in Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure.
* [Node.js](http://nodejs.org/): viene usato per visualizzare in anteprima il dashboard Web in locale nell'ambiente di sviluppo.
* [Java e JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/index.html): usati per sviluppare la topologia Storm.
* [Maven](http://maven.apache.org/what-is-maven.html): usato per generare e compilare il progetto.
* [Git](http://git-scm.com/): usato per scaricare il progetto da GitHub.
* Client **SSH** : usato per connettersi al cluster HDInsight basato su Linux. Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).


> [!IMPORTANT]
> Non è necessario un cluster HDInsight esistente. La procedura descritta in questo documento consente di creare le risorse seguenti:
> 
> * Una rete virtuale di Azure
> * Un cluster Storm in HDInsight (basato su Linux con due nodi del ruolo di lavoro)
> * Un cluster HBase in HDInsight (basato su Linux con due nodi del ruolo di lavoro)
> * Un'app Web di Azure che ospita il dashboard Web

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
  
  * Il sito Web viene implementato in Node.js.
  * [Socket.io](http://socket.io/) viene usato per le comunicazioni in tempo reale tra la topologia Storm e il sito Web.
    
    > [!NOTE]
    > L'uso di Socket.io per la comunicazione è un dettaglio di implementazione. È possibile usare qualsiasi framework di comunicazione, ad esempio WebSocket o SignalR non elaborato.

  * [D3.js](http://d3js.org/) viene usato per creare un grafico dei dati inviati al sito Web.

> [!IMPORTANT]
> Sono necessari due cluster perché non è supportato alcun metodo per la creazione di un unico cluster HDInsight per Storm e HBase.

La topologia legge i dati dall'hub eventi tramite la classe [org.apache.storm.eventhubs.spout.EventHubSpout](http://storm.apache.org/releases/0.10.1/javadocs/org/apache/storm/eventhubs/spout/class-use/EventHubSpout.html) e scrive i dati in HBase usando la classe [org.apache.storm.hbase.bolt.HBaseBolt](https://storm.apache.org/releases/1.0.1/javadocs/org/apache/storm/hbase/bolt/HBaseBolt.html). Per la comunicazione con il sito Web viene usato [socket.io-client.java](https://github.com/nkzawa/socket.io-client.java).

Nel diagramma seguente viene illustrato il layout della topologia:

![diagramma della topologia](./media/hdinsight-storm-sensor-data-analysis/sensoranalysis.png)

> [!NOTE]
> Questo diagramma è una visualizzazione semplificata della topologia. Per ogni partizione dell'hub eventi viene creata un'istanza di ogni componente. Queste istanze vengono distribuite tra i nodi del cluster e i dati vengono instradati tra di essi, come indicato di seguito:
> 
> * I dati trasmessi dallo spout al parser sono sottoposti a bilanciamento del carico.
> * I dati dal parser al dashboard e ad HBase vengono raggruppati per ID dispositivo, in modo che il flusso di messaggi provenienti dallo stesso dispositivo siano indirizzati sempre allo stesso componente.

### <a name="topology-components"></a>Componenti della topologia

* **EventHub Spout**: lo spout viene fornito come parte di Apache Storm 0.10.0 e versioni successive.
  
  > [!NOTE]
  > Lo spout dell'hub eventi usato in questo esempio richiede un cluster Storm in HDInsight versione 3.5 o 3.6.

* **ParserBolt.java**: i dati generati dallo spout sono dati JSON non elaborati. In alcuni casi vengono generati più eventi alla volta. Il bolt legge i dati generati dallo spout e analizza il messaggio JSON. Il bolt genera quindi i dati come tupla contenente più campi.
* **DashboardBolt.java**: questo componente mostra come usare la libreria client Socket.io per Java per inviare dati in tempo reale al dashboard Web.
* **no-hbase.yaml**: definizione della topologia usata per l'esecuzione in modalità locale. Non usa componenti HBase.
* **with-hbase.yaml**: definizione della topologia usata per l'esecuzione della topologia nel cluster. Usa componenti HBase.
* **dev.properties**: informazioni di configurazione per lo spout dell'hub eventi, il bolt HBase e i componenti del dashboard.

## <a name="prepare-your-environment"></a>Preparare l'ambiente

Prima di usare questo esempio, è necessario creare un hub eventi di Azure, che viene letto dalla topologia Storm.

### <a name="configure-event-hub"></a>Configurare l'hub eventi

L'hub eventi è l'origine dati per questo esempio. Per creare un nuovo hub eventi, seguire questa procedura.

1. Dal [portale di Azure](https://portal.azure.com) selezionare **+ Nuovo** -> **Internet delle cose** -> **Hub eventi**.
2. Nella sezione **Crea spazio dei nomi** eseguire le operazioni seguenti:
   
   1. Immettere un **nome** per lo spazio dei nomi.
   2. Selezione di un piano tariffario. **Basic** è sufficiente per questo esempio.
   3. Selezionare la **sottoscrizione** di Azure da usare.
   4. Selezionare un gruppo di risorse esistente o crearne uno nuovo.
   5. Selezionare il **percorso** per l'hub eventi.
   6. Selezionare **Aggiungi al dashboard** e quindi fare clic su **Crea**.

3. Al termine del processo di creazione, vengono visualizzate le informazioni relative agli hub eventi per lo spazio dei nomi. Da qui, scegliere **+ Aggiungi hub eventi**. Nella sezione **Crea hub eventi** immettere un nome per **sensordata** e quindi selezionare **Crea**. Mantenere i valori predefiniti per gli altri campi.
4. Dalla visualizzazione Hub eventi per lo spazio dei nomi selezionare **Hub eventi**. Selezionare la voce **sensordata** .
5. Dall'hub eventi per sensordata selezionare **Criteri di accesso condiviso**. Utilizzare il collegamento **+ Aggiungi** per aggiungere i seguenti criteri:

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
                no-hbase.yaml - topology definition without hbase components.
                with-hbase.yaml - topology definition with hbase components.
            src/main/java/com/microsoft/examples/bolts/
                ParserBolt.java - parses JSON data into tuples
                DashboardBolt.java - sends data over Socket.IO to the web dashboard.
        dashboard/nodejs/ - this is the node.js web dashboard.
        SendEvents/ - utilities to send fake sensor data.

> [!NOTE]
> Questo documento non fornisce i dettagli completi del codice incluso nell'esempio. Il codice, tuttavia, è completamente commentato.

Per configurare il progetto da leggere dall'hub eventi, aprire il file `hdinsight-eventhub-example/TemperatureMonitor/dev.properties` e aggiungere le informazioni relative all'hub eventi alle righe seguenti:

```bash
eventhub.read.policy.name: your_read_policy_name
eventhub.read.policy.key: your_key_here
eventhub.namespace: your_namespace_here
eventhub.name: your_event_hub_name
eventhub.partitions: 2
```

## <a name="compile-and-test-locally"></a>Compilare ed eseguire il test in locale

> [!IMPORTANT]
> L'uso della topologia in locale richiede un ambiente di sviluppo Storm funzionante. Per altre informazioni, vedere [Setting up a development environment](http://storm.apache.org/releases/1.1.0/Setting-up-development-environment.html) (Impostazione di un ambiente di sviluppo) in Apache.org.

> [!WARNING]
> Se si usa un ambiente di sviluppo Windows, si potrebbe ricevere un'eccezione `java.io.IOException` quando si esegue la topologia in locale. In questo caso, eseguire la topologia in HDInsight.

Prima di eseguire il test, è necessario avviare il dashboard per visualizzare l'output della topologia e generare i dati da archiviare nell'hub eventi.

> [!IMPORTANT]
> Il componente HBase di questa topologia non è attivo durante il test locale. L'API Java per il cluster HBase non è accessibile dall'esterno della rete virtuale di Azure che contiene i cluster.

### <a name="start-the-web-application"></a>Avviare l'applicazione Web

1. Aprire un prompt dei comandi e passare alla directory `hdinsight-eventhub-example/dashboard`. Usare il comando seguente per installare le dipendenze richieste dall'applicazione Web:
   
    ```bash
    npm install
    ```

2. Usare il comando seguente per avviare l'applicazione Web:
   
    ```bash
    node server.js
    ```
   
    Verrà visualizzato un messaggio simile al testo seguente:
   
        Server listening at port 3000

3. Aprire un Web browser e immettere `http://localhost:3000/` come indirizzo. Viene visualizzata una pagina simile all'immagine seguente:
   
    ![dashboard Web](./media/hdinsight-storm-sensor-data-analysis/emptydashboard.png)
   
    Lasciare il prompt dei comandi aperto. Al termine del test, usare CTRL+C per arrestare il server Web.

### <a name="generate-data"></a>Generare i dati

> [!NOTE]
> I passaggi descritti in questa sezione usano Node.js, così da poter essere usati su qualsiasi piattaforma. Per esempi in altri linguaggi, vedere la directory `SendEvents`.

1. Aprire un nuovo prompt, shell o terminale e passare alla directory `hdinsight-eventhub-example/SendEvents/nodejs`. Usare il comando seguente per installare le dipendenze richieste dall'applicazione:

    ```bash
    npm install
    ```

2. Aprire il file `app.js` in un editor di testo e aggiungere le informazioni relative all'hub eventi ottenute in precedenza:
   
    ```javascript
    // ServiceBus Namespace
    var namespace = 'YourNamespace';
    // Event Hub Name
    var hubname ='sensordata';
    // Shared access Policy name and key (from Event Hub configuration)
    var my_key_name = 'devices';
    var my_key = 'YourKey';
    ```
   
   > [!NOTE]
   > Questo esempio presuppone che sia stato usato `sensordata` come nome dell'hub eventi e `devices` come nome del criterio che include un'attestazione `Send`.

3. Usare il comando seguente per inserire nuove voci nell'hub eventi:
   
    ```bash
    node app.js
    ```
   
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

### <a name="build-and-start-the-topology"></a>Creare e avviare la topologia

1. Aprire un nuovo prompt dei comandi e passare alla directory `hdinsight-eventhub-example/TemperatureMonitor`. Per compilare e creare la topologia, usare il seguente comando: 

    ```bash
    mvn clean package
    ```

2. Per avviare la topologia in modalità locale, usare il comando seguente:

    ```bash
    storm jar target/TemperatureMonitor-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local --filter dev.properties resources/no-hbase.yaml
    ```

    * `--local` avvia la topologia in modalità locale.
    * `--filter` usa il file `dev.properties` per immettere i parametri nella definizione della topologia.
    * `resources/no-hbase.yaml` usa la definizione di topologia `no-hbase.yaml`.
 
   Una volta avviata, la topologia legge le voci dall'hub eventi e le invia al dashboard in esecuzione sul computer locale. Nel dashboard Web dovrebbero comparire linee simili a quelle nell'immagine seguente:
   
    ![dashboard con dati](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

2. Mentre il dashboard è in esecuzione, usare il comando `node app.js` dai passaggi precedenti per inviare nuovi dati agli hub eventi. Poiché i valori di temperatura vengono generati in modo casuale, il grafico deve aggiornarsi per visualizzare le modifiche estese della temperatura.
   
   > [!NOTE]
   > Per usare il comando `node app.js` è necessario trovarsi nella directory **hdinsight-eventhub-example/SendEvents/Nodejs**.

3. Dopo aver verificato il corretto aggiornamento del dashboard, arrestare la topologia usando CTRL+C. È possibile usare CTRL+C anche per arrestare il server Web locale.

## <a name="create-a-storm-and-hbase-cluster"></a>Creare un cluster Storm e HBase

Per i passaggi in questa sezione, usare un [modello di Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md) per creare una rete virtuale di Azure e un cluster Storm e HBase nella rete virtuale. Il modello crea anche un'app Web di Azure e consente di distribuire una copia del dashboard al suo interno.

> [!NOTE]
> Viene usata una rete virtuale in modo che la topologia in esecuzione nel cluster Storm possa comunicare direttamente con il cluster HBase tramite l'API Java HBase.

Il modello di Resource Manager usato in questo documento si trova in un contenitore BLOB pubblico all'indirizzo **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hbase-storm-cluster-in-vnet-3.6.json**.

1. Fare clic sul pulsante seguente per accedere ad Azure e aprire il modello di Resource Manager nel portale di Azure.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-storm-cluster-in-vnet-3.6.json" target="_blank"><img src="./media/hdinsight-storm-sensor-data-analysis/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Immettere i valori seguenti dalla sezione **Distribuzione personalizzata**:
   
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

Dopo avere creato le risorse, vengono visualizzate le informazioni sul gruppo di risorse.

![Gruppo di risorse per la rete virtuale e i cluster](./media/hdinsight-storm-sensor-data-analysis/groupblade.png)

> [!IMPORTANT]
> Si noti che i nomi dei cluster HDInsight sono **storm-BASENAME** e **hbase-BASENAME**, dove BASENAME è il nome specificato per il modello. Questi nomi verranno usati in un passaggio successivo per la connessione ai cluster. Si noti inoltre che il nome del sito dashboard è **basename-dashboard**. Questo valore viene usato più avanti in questo documento.

## <a name="configure-the-dashboard-bolt"></a>Configurare il bolt Dashboard

Per inviare dati al dashboard distribuito come app Web, è necessario modificare la riga seguente nel file `dev.properties`:

```yaml
dashboard.uri: http://localhost:3000
```

Modificare `http://localhost:3000` in `http://BASENAME-dashboard.azurewebsites.net` e salvare il file. Sostituire **BASENAME** con il nome di base fornito nel passaggio precedente. È anche possibile usare il gruppo di risorse creato in precedenza per selezionare il dashboard e visualizzare l'URL.

## <a name="create-the-hbase-table"></a>Creare una tabella HBase

Per archiviare i dati in HBase, è necessario creare prima di tutto una tabella. Creare prima le risorse in cui Storm dovrà scrivere, perché creare risorse dall'interno di una topologia Storm può comportare la creazione di più istanze che tentano di creare la stessa risorsa. Creare le risorse all'esterno della topologia e usare Storm per operazioni di lettura/scrittura e analisi.

1. Usare SSH per connettersi al cluster HBase usando il nome utente e la password SSH forniti nel modello durante la creazione del cluster. Ad esempio, se la connessione viene stabilita tramite il comando `ssh` , usare la sintassi seguente:
   
    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```
   
    Sostituire `sshuser` con il nome utente SSH specificato durante la creazione del cluster. Sostituire `clustername` con il nome del cluster HBase.

2. Dalla sessione SSH avviare la shell di HBase.
   
    ```bash
    hbase shell
    ```
   
    Dopo il caricamento della shell, verrà visualizzato un prompt `hbase(main):001:0>`.

3. Dalla shell HBase immettere il comando seguente per creare una tabella in cui archiviare i dati del sensore:
   
    ```hbase
    create 'SensorData', 'cf'
    ```

4. Verificare la corretta creazione della tabella con il comando seguente:
   
    ```hbase
    scan 'SensorData'
    ```
   
    Tale comando restituisce informazioni simili alle seguenti, che indicano che nella tabella ci sono 0 righe disponibili.
   
        ROW                   COLUMN+CELL                                       0 row(s) in 0.1900 seconds

5. Immettere `exit` per uscire dalla shell HBase:

## <a name="configure-the-hbase-bolt"></a>Configurare il bolt HBase

Per scrivere in HBase dal cluster Storm, è necessario fornire al bolt HBase i dettagli di configurazione del cluster HBase.

1. Usare uno degli esempi seguenti per recuperare il quorum di Zookeeper per il cluster HBase:

    ```bash
    CLUSTERNAME='your_HDInsight_cluster_name'
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/HBASE/components/HBASE_MASTER" | jq '.metrics.hbase.master.ZookeeperQuorum'
    ```

    > [!NOTE]
    > Sostituire `your_HDInsight_cluster_name` con il nome del cluster HDInsight. Per altre informazioni sull'installazione dell'utilità `jq`, vedere [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).
    >
    > Quando richiesto, immettere la password per l'account di accesso amministratore di HDInsight.

    ```powershell
    $clusterName = 'your_HDInsight_cluster_name'
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HBASE/components/HBASE_MASTER" -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.metrics.hbase.master.ZookeeperQuorum
    ```

    > [!NOTE]
    > Sostituire 'your_HDInsight_cluster_name' con il nome del cluster HDInsight. Quando richiesto, immettere la password per l'account di accesso amministratore di HDInsight.
    >
    > Questo esempio richiede Azure PowerShell. Per altre informazioni sull'uso di Azure PowerShell, vedere [Getting started with Azure PowerShell](https://docs.microsoft.com/en-us/powershell/scripting/Getting-Started-with-Windows-PowerShell?view=powershell-6) (Introduzione ad Azure PowerShell)

    Le informazioni restituite da questi esempi sono simili al testo seguente:

    `zk2-hbase.mf0yeg255m4ubit1auvj1tutvh.ex.internal.cloudapp.net:2181,zk0-hbase.mf0yeg255m4ubit1auvj1tutvh.ex.internal.cloudapp.net:2181,zk3-hbase.mf0yeg255m4ubit1auvj1tutvh.ex.internal.cloudapp.net:2181`

    Queste informazioni vengono usate da Storm per comunicare con il cluster HBase.

2. Modificare il file `dev.properties` e aggiungere le informazioni sul quorum di Zookeeper alla riga seguente:

    ```yaml
    hbase.zookeeper.quorum: your_hbase_quorum
    ```

## <a name="build-package-and-deploy-the-solution-to-hdinsight"></a>Compilare la soluzione, creare il pacchetto e distribuirla in HDInsight

Nell'ambiente di sviluppo seguire questa procedura per distribuire la topologia Temperature nel cluster Storm.

1. Dalla directory `TemperatureMonitor` eseguire una nuova compilazione e creare un pacchetto JAR dal progetto usando il comando seguente:
   
        mvn clean package
   
    Questo comando crea un file denominato `TemperatureMonitor-1.0-SNAPSHOT.jar in the ` nella directory "target" del progetto.

2. Usare scp per caricare i file `TemperatureMonitor-1.0-SNAPSHOT.jar` e `dev.properties` nel cluster Storm. Nell'esempio seguente sostituire `sshuser` con l'utente SSH specificato durante la creazione del cluster e `clustername` con il nome del cluster Storm. Quando richiesto, immettere la password per l'utente SSH.
   
    ```bash
    scp target/TemperatureMonitor-1.0-SNAPSHOT.jar dev.properties sshuser@clustername-ssh.azurehdinsight.net:
    ```

   > [!NOTE]
   > Il caricamento dei file può richiedere alcuni minuti.

    Per altre informazioni sull'uso dei comandi `scp` e `ssh` con HDInsight, vedere [Usare SSH con HDInsight](./hdinsight-hadoop-linux-use-ssh-unix.md)

3. Al termine del caricamento del file, connettersi al cluster Storm tramite SSH.
   
    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

    Sostituire `sshuser` con il nome utente SSH. Sostituire `clustername` con il nome del cluster Storm.

4. Nella sessione SSH usare il comando seguente per avviare la topologia:
   
    ```bash
    storm jar TemperatureMonitor-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote --filter dev.properties -R /with-hbase.yaml
    ```

    * `--remote` invia la topologia al servizio Nimbus, che lo distribuisce ai nodi supervisore nel cluster.
    * `--filter` usa il file `dev.properties` per immettere i parametri nella definizione della topologia.
    * `-R /with-hbase.yaml` usa la topologia `with-hbase.yaml` inclusa nel pacchetto.

5. Dopo aver avviato la topologia, aprire un browser al sito Web pubblicato in Azure e quindi usare il comando `node app.js` per inviare dati all'hub eventi. Verrà visualizzato il dashboard Web aggiornato per mostrare le informazioni.
   
    ![dashboard](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

## <a name="view-hbase-data"></a>Visualizzare i dati di HBase

Usare la procedura seguente per connettersi a HBase e verificare che i dati siano stati scritti nella tabella:

1. Usare SSH per connettersi al cluster HBase.
   
    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

    Sostituire `sshuser` con il nome utente SSH. Sostituire `clustername` con il nome del cluster HBase.

2. Dalla sessione SSH avviare la shell di HBase.
   
    ```bash
    hbase shell
    ```
   
    Dopo il caricamento della shell, verrà visualizzato un prompt `hbase(main):001:0>`.

3. Visualizzare le righe dalla tabella:
   
    ```hbase
    scan 'SensorData'
    ```
   
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

