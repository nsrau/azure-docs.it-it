<properties
   pageTitle="Analisi dei dati del sensore con Apache Storm e HBase | Microsoft Azure"
   description="Informazioni su come connettersi ad Apache Storm con una rete virtuale. Usare Storm con HBase per elaborare i dati del sensore da un hub eventi e visualizzarli con D3.js."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"/>  

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/05/2016"
   ms.author="larryfr"/>

# Analizzare i dati del sensore con Apache Storm, hub eventi e HBase in HDInsight (Hadoop) 

Informazioni su come usare Apache Storm in HDInsight per elaborare i dati del sensore dall'hub eventi di Azure, archiviarli in Apache HBase in HDInsight e visualizzarli con D3.j in esecuzione come app Web di Azure.

Il modello di Azure Resource Manager usato in questo documento illustra come creare più risorse di Azure in un gruppo di risorse. In particolare, crea una rete virtuale di Azure, due cluster HDInsight (Storm e HBase) e un'app Web di Azure. Un'implementazione di Node. js di un dashboard Web in tempo reale viene distribuita automaticamente all'app web.

> [AZURE.NOTE] Le informazioni contenute in questo documento e l'esempio fornito sono stati testati con le versioni cluster di HDInsight 3.3 e 3.4 basate su Linux .

## Prerequisiti

* Una sottoscrizione di Azure. Vedere [Ottenere una versione di valutazione gratuita di Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

    > [AZURE.IMPORTANT] Non è necessario un cluster HDInsight esistente. La procedura in questo documento consente di creare le risorse seguenti:
    >
    > * Una rete virtuale di Azure
    > * Un cluster Storm in HDInsight, basato su Linux con 2 nodi del ruolo di lavoro
    > * Un cluster HBase in HDInsight, basato su Linux con 2 nodi del ruolo di lavoro
    > * Un'app Web di Azure che ospita il dashboard Web

* [Node. js](http://nodejs.org/): viene usato per visualizzare in anteprima il dashboard Web in locale nell'ambiente di sviluppo.

* [Java e JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/index.html): usati per sviluppare la topologia Storm.

* [Maven](http://maven.apache.org/what-is-maven.html): usato per generare e compilare il progetto.

* [Git](http://git-scm.com/): usato per scaricare il progetto da GitHub.

* Client __SSH__: usato per connettersi al cluster HDInsight basato su Linux. Per altre informazioni sull'uso di SSH con HDInsight, vedere i documenti seguenti.

    * [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

    * [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix oppure OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    > [AZURE.NOTE] È anche necessario avere accesso al comando `scp` che viene usato per copiare i file tra l'ambiente di sviluppo locale e il cluster HDInsight tramite SSH.

## Architettura

![diagramma dell'architettura](./media/hdinsight-storm-sensor-data-analysis/devicesarchitecture.png)

Questo esempio è costituito dai componenti seguenti:

* **Hub eventi di Azure**: include i dati raccolti dai sensori. Ai fini di questo esempio viene fornita un'applicazione che genera i dati.

* **Storm in HDInsight**: assicura l'elaborazione in tempo reale dei dati provenienti dall'hub eventi.

* **HBase in HDInsight**: fornisce un archivio dati NoSQL persistente per i dati, dopo l'elaborazione tramite Storm.

* **Servizio Rete virtuale di Azure**: abilita la comunicazione sicura tra i cluster Storm in HDInsight e HBase in HDInsight.

    > [AZURE.NOTE] Una rete virtuale è necessaria per usare l'API del client HBase Java, perché non viene esposta tramite il gateway pubblico per i cluster HBase. L'installazione di cluster HBase e Storm nella stessa rete virtuale consente al cluster Storm, o qualsiasi altro sistema sulla rete virtuale, di accedere direttamente a HBase tramite l'API del client.

* **Sito Web del dashboard**: dashboard di esempio che rappresenta graficamente i dati in tempo reale.

	* Il sito Web viene implementato in Node.js ed è quindi possibile eseguirlo a scopo di test su qualsiasi sistema operativo client. In alternativa, è possibile distribuirlo in Siti Web di Azure.

	* [Socket.io](http://socket.io/) viene usato per le comunicazioni in tempo reale tra la topologia Storm e il sito Web.

		> [AZURE.NOTE] Questo è un dettaglio di implementazione. È possibile usare qualsiasi framework di comunicazione, ad esempio WebSocket o SignalR non elaborato.

	* [D3.js](http://d3js.org/) viene usato per creare un grafico dei dati inviati al sito Web.

La topologia legge i dati dall'hub eventi tramite la classe [org.apache.storm.eventhubs.spout.EventHubSpout](http://storm.apache.org/releases/0.10.1/javadocs/org/apache/storm/eventhubs/spout/class-use/EventHubSpout.html) e scrive i dati in HBase usando la classe [org.apache.storm.hbase.bolt.HBaseBolt](https://storm.apache.org/javadoc/apidocs/org/apache/storm/hbase/bolt/class-use/HBaseBolt.html). Per la comunicazione con il sito Web viene usato [socket.io-client.java](https://github.com/nkzawa/socket.io-client.java).

Di seguito è riportato un diagramma della topologia:

![diagramma della topologia](./media/hdinsight-storm-sensor-data-analysis/sensoranalysis.png)

> [AZURE.NOTE] Si tratta di una visualizzazione molto semplificata della topologia. In fase di esecuzione, per ciascuna partizione dell'hub eventi in fase di lettura viene creata un'istanza di ogni componente. Queste istanze vengono distribuite tra i nodi del cluster e i dati vengono instradati tra di essi, come indicato di seguito:
>
> * I dati trasmessi dallo spout al parser sono sottoposti a bilanciamento del carico.
> * I dati dal parser al dashboard e ad HBase vengono raggruppati per ID dispositivo, in modo che il flusso di messaggi provenienti dallo stesso dispositivo siano indirizzati sempre allo stesso componente.

### Componenti della topologia

* **EventHub Spout**: lo spout viene fornito come parte di Apache Storm 0.10.0 e versioni successive.

    > [AZURE.NOTE] Lo spout dell'hub eventi usato in questo esempio richiede un cluster Storm in HDInsight versione 3.3 o 3.4. Per informazioni sull'uso di eventi con una versione precedente di Storm in HDInsight, vedere [Elaborare eventi dell'hub eventi di Azure con Storm in HDInsight (Java)](hdinsight-storm-develop-java-event-hub-topology.md).

* **ParserBolt.java**: i dati generati dallo spout sono dati JSON non elaborati. In alcuni casi vengono generati più eventi alla volta. Questo bolt mostra come leggere i dati generati dallo spout e indirizzarli a un nuovo flusso sotto forma di una tupla contenente più campi.

* **DashboardBolt.java**: mostra come usare la libreria client Socket.io per Java per inviare dati in tempo reale al dashboard Web.

## Preparare l'ambiente

Prima di usare questo esempio, è necessario creare un hub eventi di Azure, che viene letto dalla topologia Storm.

### Configurare l'hub eventi

L'hub eventi è l'origine dati per questo esempio. Per creare un nuovo hub eventi, seguire questa procedura.

1. Nel [portale di Azure classico](https://manage.windowsazure.com) selezionare **NUOVO| Service Bus | Event Hub | Custom Create**.

2. Nella finestra di dialogo **Crea un nuovo hub eventi** immettere un nome in **Nome hub eventi**, selezionare l'area in cui creare l'hub in **Area geografica** e quindi creare un nuovo spazio dei nomi o selezionarne uno esistente. Infine fare clic sulla freccia per continuare.

2. Nella finestra di dialogo **Configura hub eventi** immettere i valori per **Conteggio partizioni** e **Conservazione messaggi**. Per questo esempio usare un numero di partizioni pari a 10 e un valore di conservazione dei messaggi pari a 1.

3. Dopo la creazione dell'hub eventi, selezionare lo spazio dei nomi e quindi **Hub eventi**. Infine selezionare l'hub eventi creato in precedenza.

4. Selezionare **Configura** e quindi creare due nuovi criteri di accesso usando le informazioni seguenti.

	| Name | Autorizzazioni |
    | ----- | ----- |
	| devices | Invio |
	| storm | Attesa |

	Dopo avere creato le autorizzazioni, selezionare l'icona **Salva** nella parte inferiore della pagina. Verranno creati i criteri di accesso condivisi che saranno usati per inviare messaggi all'hub e per leggere i messaggi provenienti dall'hub.

5. Dopo avere salvato i criteri, usare **Generatore di chiavi di accesso condivise** nella parte inferiore della pagina per recuperare la chiave per i criteri **devices** e **storm**. Salvare le chiavi perché verranno usate in un secondo momento.

## Scaricare e configurare il progetto

Usare il comando seguente per scaricare il progetto da GitHub.

	git clone https://github.com/Blackmist/hdinsight-eventhub-example

Dopo aver eseguito il comando, si otterrà la struttura di directory seguente:

	hdinsight-eventhub-example/
		TemperatureMonitor/ - this is the Java topology
			conf/
				Config.properties
				hbase-site.xml
			src/
			test/
			dashboard/ - this is the node.js web dashboard
			SendEvents/ - utilities to send fake sensor data

> [AZURE.NOTE] Questo documento non fornisce i dettagli completi del codice incluso nell'esempio. Il codice, tuttavia, è completamente commentato.

Aprire il file **Config.properties** e aggiungere le informazioni usate in precedenza durante la creazione dell'hub eventi per le voci elencate di seguito. Salvare il file dopo aver aggiunto tali informazioni.

	eventhubspout.username = storm

	eventhubspout.password = <the key of the 'storm' policy>

	eventhubspout.namespace = <the event hub namespace>

	eventhubspout.entitypath = <the event hub name>

	eventhubspout.partitions.count = <the number of partitions for the event hub>

## Compilare ed eseguire il test in locale

Prima di eseguire il test, è necessario avviare il dashboard per visualizzare l'output della topologia e generare i dati da archiviare nell'hub eventi.

> [AZURE.IMPORTANT] Il componente HBase di questa topologia non è attivo quando il test viene eseguito in locale, perché l'API Java per il cluster HBase non è accessibile dall'esterno della rete virtuale Azure che contiene i cluster.

### Avviare l'applicazione Web

1. Aprire un nuovo prompt dei comandi o terminal e passare alla directory **hdinsight-eventhub-example/dashboard**. Usare quindi il comando seguente per installare le dipendenze necessarie per l'applicazione Web:

		npm install

2. Usare il comando seguente per avviare l'applicazione Web:

		node server.js

	Verrà visualizzato un messaggio simile al seguente:

		Server listening at port 3000

2. Aprire un Web browser e immettere **http://localhost:3000/** come indirizzo. Verrà visualizzata una pagina simile alla seguente:

	![dashboard Web](./media/hdinsight-storm-sensor-data-analysis/emptydashboard.png)

	Lasciare il prompt dei comandi o il terminal aperto. Al termine del test, usare CTRL+C per arrestare il server Web.

### Avviare la generazione dei dati

> [AZURE.NOTE] I passaggi descritti in questa sezione usano Node.js, così da poter essere usati su qualsiasi piattaforma. Per esempi in altri linguaggi, vedere la directory **SendEvents**.

1. Aprire un nuovo prompt dei comandi o terminal e passare alla directory **hdinsight-eventhub-example/SendEvents/nodejs**. Usare quindi il comando seguente per installare le dipendenze necessarie per l'applicazione:

		npm install

2. Aprire il file **app.js** in un editor di testo e aggiungere le informazioni relative all'hub eventi ottenute in precedenza:

		// ServiceBus Namespace
		var namespace = 'servicebusnamespace';
		// Event Hub Name
		var hubname ='eventhubname';
		// Shared access Policy name and key (from Event Hub configuration)
		var my_key_name = 'devices';
		var my_key = 'key';

2. Usare il comando seguente per inserire nuove voci nell'hub eventi:

		node app.js

	Verranno visualizzate diverse righe di output contenenti i dati inviati all'hub eventi. Le righe saranno simili a quelle riportate di seguito:

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

### Avviare la topologia

2. Avviare la topologia in locale usando il comando seguente:

        mvn compile exec:java -Dstorm.topology=com.microsoft.examples.Temperature

	Questo comando avvia la topologia, legge i file dall'hub eventi e li invia al dashboard in esecuzione in Siti Web di Azure. Nel dashboard Web dovrebbero comparire linee simili alle seguenti:

	![dashboard con dati](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

    > [AZURE.NOTE] Se si esegue questo comando da un prompt dei comandi di PowerShell, è necessario usate le virgolette doppie per racchiudere il parametro `-Dstorm.topology=com.microsoft.examples.Temperature`. Ad esempio: `mvn compile exec:java "-Dstorm.topology=com.microsoft.examples.Temperature"`.

3. Mentre il dashboard è in esecuzione, usare il comando `node app.js` dai passaggi precedenti per inviare nuovi dati agli hub eventi. Poiché i valori di temperatura vengono generati in modo casuale, il grafico deve aggiornarsi per visualizzare le modifiche estese della temperatura.

3. Dopo aver verificato il corretto funzionamento di questo meccanismo, arrestare la topologia usando CTRL+C. Per arrestare l'app SendEvent, selezionare la finestra e premere un tasto qualsiasi. È possibile usare CTRL+C anche per arrestare il server Web.

## Creare un cluster Storm e HBase

Per eseguire la topologia in HDInsight e per abilitare il bolt HBase, è necessario creare nuovi cluster Storm e HBase. Per i passaggi in questa sezione si usa un [modello di Azure Resource Manager](../resource-group-template-deploy.md) per creare una nuova rete virtuale di Azure e un cluster Storm e HBase nella rete virtuale. Il modello crea anche un'app Web di Azure e consente di distribuire una copia del dashboard al suo interno.

> [AZURE.NOTE] Viene usata una rete virtuale in modo che la topologia in esecuzione nel cluster Storm possa comunicare direttamente con il cluster HBase tramite l'API Java HBase.

Il modello di Resource Manager usato in questo documento è disponibile in un contenitore BLOB pubblico in \_\_https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hbase-storm-cluster-in-vnet.json__.

1. Fare clic sul pulsante seguente per accedere ad Azure e aprire il modello di Resource Manager nel portale di Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-storm-cluster-in-vnet.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/it-IT/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Nel pannello **Parametri** immettere le informazioni seguenti:

    ![Parametri di HDInsight](./media/hdinsight-storm-sensor-data-analysis/parameters.png)
    
    * **BASECLUSTERNAME**: questo valore verrà usato come nome di base per i cluster Storm e HBase. Ad esempio, se si immette __hdi__ verrà creato un cluster Storm denominato __storm-hdi__ e un cluster HBase denominato __hbase-hdi__.
    * __CLUSTERLOGINUSERNAME__: il nome utente amministratore per i cluster Storm e HBase.
    * __CLUSTERLOGINPASSWORD__: la password utente dell'amministratore per i cluster Storm e HBase.
    * __SSHUSERNAME__: l'utente SSH per creare per i cluster Storm e HBase.
    * __SSHPASSWORD__: la password dell'utente SSH per i cluster Storm e HBase.
    * __LOCATION__: l'area in cui verranno creati i cluster.
    
    Fare clic su __OK__ per salvare i parametri.
    
3. Usare la sezione __Gruppo di risorse__ per creare un nuovo gruppo di risorse o selezionarne uno esistente.

4. Nel menu a discesa __Località del gruppo di risorse__ selezionare la stessa località selezionata per il parametro __LOCATION__.

5. Selezionare __Note legali__ e quindi __Crea__.

6. Infine, selezionare __Aggiungi al dashboard__ e quindi __Crea__. Occorreranno circa 20 minuti per creare il cluster.

Dopo avere create le risorse, si verrà reindirizzati a un pannello per il gruppo di risorse che contiene i cluster e il dashboard Web.

![Pannello Gruppo di risorse per la rete virtuale e i cluster](./media/hdinsight-storm-sensor-data-analysis/groupblade.png)

> [AZURE.IMPORTANT] Si noti che i nomi dei cluster HDInsight sono __storm-BASENAME__ e __hbase-BASENAME__, dove BASENAME è il nome specificato per il modello. Questi nomi verranno usati nei passaggi successivi per la connessione al cluster. Si noti inoltre che il nome del sito dashboard è __basename-dashboard__. Lo si userà in un secondo momento quando si visualizza il dashboard.

## Configurare il bolt Dashboard

Per inviare dati al dashboard distribuito come un'app Web, è necessario modificare la riga seguente nel file __DashboardBolt.java__:

    socket = IO.socket("http://localhost:3000");

Modificare `http://localhost:3000` in `http://BASENAME-dashboard.azurewebsites.net` e salvare il file. Sostituire __BASENAME__ con il nome di base fornito nel passaggio precedente. È anche possibile usare il gruppo di risorse creato in precedenza per selezionare il dashboard e visualizzare l'URL.

## Creare una tabella HBase

Per archiviare i dati in HBase, è necessario creare prima di tutto una tabella. In genere è consigliabile creare prima le risorse in cui Storm dovrà scrivere, perché creare risorse dall'interno di una topologia Storm può comportare la creazione di più copie distribuite del codice che tentano di creare la stessa risorsa stessa. Creare le risorse all'esterno della topologia e usare Storm solo per operazioni di lettura/scrittura e analisi.

1. Usare SSH per connettersi al cluster HBase usando il nome utente e la password SSH forniti nel modello durante la creazione del cluster. Ad esempio, se la connessione viene stabilita tramite il comando `ssh`, usare la sintassi seguente:

        ssh USERNAME@hbase-BASENAME-ssh.azurehdinsight.net
    
    In questo comando sostituire __USERNAME__ con il nome utente SSH fornito durante la creazione del cluster e __BASENAME__ con il nome di base specificato. Quando richiesto, immettere la password per l'utente SSH.

2. Dalla sessione SSH avviare la shell di HBase.

    	hbase shell
	
	Una volta caricata la shell, verrà visualizzato un prompt `hbase(main):001:0>`.

3. Dalla shell HBase immettere il comando seguente per creare una tabella in cui archiviare i dati del sensore:

    	create 'SensorData', 'cf'

4. Verificare la corretta creazione della tabella con il comando seguente:

    	scan 'SensorData'
		
	Dovrebbe restituire informazioni simili alle seguenti, che indicano che nella tabella sono disponibili 0 righe.
	
		ROW                   COLUMN+CELL                                       0 row(s) in 0.1900 seconds

5. Usare quanto segue per uscire dalla shell HBase:

		exit

## Configurare il bolt HBase

Per scrivere in HBase dal cluster Storm, è necessario fornire al bolt HBase i dettagli di configurazione del cluster HBase. A questo scopo, il modo più semplice consiste nello scaricare __hbase-site.xml__ dal cluster e includerlo nel progetto. È anche necessario rimuovere il commento per diverse dipendenze nel file __pom.xml__ che carica il componente storm-hbase e le dipendenze necessarie.

> [AZURE.IMPORTANT] È necessario scaricare inoltre il file storm-hbase.jar fornito in Storm in cluster HDInsight versione 3.3 o 3.4. Questa versione viene compilata per l'interazione con HBase 1.1.x, che viene usato per HBase nei cluster HDInsight 3.3 e 3.4. Se si usa un componente storm-hbase da un'altra posizione, può essere compilato con una versione precedente di HBase.

### Salvare il file hbase-site.xml

A un prompt dei comandi usare SCP per scaricare il file __hbase-site.xml__ dal cluster. Nell'esempio seguente sostituire __USERNAME__ con il nome utente SSH fornito durante la creazione del cluster e __BASENAME__ con il nome di base specificato in precedenza. Quando richiesto, immettere la password per l'utente SSH. Sostituire `/path/to/TemperatureMonitor/conf/hbase-site.xml` con il percorso del file nel progetto TemperatureMonitor.

    scp USERNAME@hbase-BASENAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml /path/to/TemperatureMonitor/conf/hbase-site.xml

Il file __hbase-site.xml__ verrà scaricato nel percorso specificato.

### Scaricare e installare il componente storm-hbase

1. A un prompt dei comandi usare SCP per scaricare il file __storm-hbase.jar__ dal cluster Storm. Nell'esempio seguente sostituire __USERNAME__ con il nome utente SSH fornito durante la creazione del cluster e __BASENAME__ con il nome di base specificato in precedenza. Quando richiesto, immettere la password per l'utente SSH.

        scp USERNAME@storm-BASENAME-ssh.azurehdinsight.net:/usr/hdp/current/storm-client/contrib/storm-hbase/storm-hbase*.jar .

    Verrà scaricato un file denominato `storm-hbase-####.jar`, dove ### è il numero di versione di Storm per questo cluster. Prendere nota di questo numero, perché verrà usato in un secondo momento.

2. Usare il comando seguente per installare questo componente nel repository Maven locale nell'ambiente di sviluppo. In questo modo Maven potrà trovare il pacchetto durante la compilazione del progetto. Sostituire __###__ con il numero di versione incluso nel nome del file.

        mvn install:install-file -Dfile=storm-hbase-####.jar -DgroupId=org.apache.storm -DartifactId=storm-hbase -Dversion=#### -Dpackaging=jar

### Abilitare il componente storm-hbase nel progetto

1. Aprire il file __TemperatureMonitor/pom.xml__ ed eliminare le righe seguenti:

        <!-- uncomment this section to enable the hbase-bolt
        end comment for hbase-bolt section -->
    
    > [AZURE.IMPORTANT] Eliminare solo queste due righe, non altre righe tra di esse.
    
    In questo modo vengono abilitati diversi componenti necessari durante la comunicazione con HBase tramite il bolt hbase.

2. Trovare le righe seguenti e quindi sostituire __###__ con il numero di versione del file storm-hbase scaricato in precedenza.

        <dependency>
            <groupId>org.apache.storm</groupId>
            <artifactId>storm-hbase</artifactId>
            <version>####</version>
        </dependency>

    > [AZURE.IMPORTANT] Il numero di versione deve corrispondere alla versione usata durante l'installazione del componente nel repository Maven locale, perché Maven usa queste informazioni per caricare il componente quando compila il progetto.

2. Salvare il file __pom.xml__.

3. Aprire **TemperatureMonitor/src/main/java/com/microsoft/examples/Temperature.java** in un editor di testo e rimuovere i commenti dalle righe seguenti eliminando `//` all'inizio di ognuna.

		topologyBuilder.setBolt("HBase", new HBaseBolt("SensorData", mapper).withConfigKey("hbase.conf"), spoutConfig.getPartitionCount())
    	  .fieldsGrouping("Parser", "hbasestream", new Fields("deviceid")).setNumTasks(spoutConfig.getPartitionCount());

	Questa operazione consente di abilitare il bolt HBase.

	> [AZURE.NOTE] Attivare il bolt HBase solo durante la distribuzione nel cluster Storm, non durante l'esecuzione del test in locale.

4. Salvare il file __Temperature.java__.
    
## Compilare la soluzione, creare il pacchetto e distribuirla in HDInsight

Nell'ambiente di sviluppo seguire questa procedura per distribuire la topologia Temperature nel cluster Storm.

1. Usare il comando seguente per eseguire una nuova compilazione e creare un pacchetto JAR dal progetto:

		mvn clean compile package

	Verrà creato un file denominato **TemperatureMonitor-1.0-SNAPSHOT.jar** nella directory **target** del progetto.

2. Usare scp per caricare il file __TemperatureMonitor-1.0-SNAPSHOT.jar__ nel cluster Storm. Nell'esempio seguente sostituire __USERNAME__ con il nome utente SSH fornito durante la creazione del cluster e __BASENAME__ con il nome di base specificato in precedenza. Quando richiesto, immettere la password per l'utente SSH.

        scp target\TemperatureMonitor-1.0-SNAPSHOT.jar USERNAME@storm-BASENAME-ssh.azurehdinsight.net:
    
    > [AZURE.NOTE] Per caricare il file potrebbero essere necessari alcuni minuti, perché le relative dimensioni saranno di diversi MB.

3. Una volta completato il caricamento, connettersi al cluster tramite SSH.

        ssh USERNAME@storm-BASENAME-ssh.azurehdinsight.net

4. Nella sessione SSH usare il comando seguente per avviare la topologia.

        storm jar TemperatureMonitor-1.0-SNAPSHOT.jar com.microsoft.examples.Temperature tempmonitor
    
    Verrà avviata la topologia tramite la classe __com.microsoft.examples.Temperature__ inclusa nel pacchetto, usando __tempmonitor__ come nome descrittivo per questa istanza della topologia.

3. Dopo aver avviato la topologia, aprire un browser al sito Web pubblicato in Azure e quindi usare il comando `node app.js` per inviare dati all'hub eventi. Verrà visualizzato il dashboard Web aggiornato per mostrare le informazioni.

	![dashboard](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

## Visualizzare i dati di HBase

Dopo l'invio di dati per la topologia tramite `node app.js`, usare questa procedura per connettersi a HBase e verificare che i dati siano stati scritti nella tabella creata in precedenza.

1. Usare SSH per connettersi al cluster HBase.

        ssh USERNAME@hbase-BASENAME-ssh.azurehdinsight.net

2. Dalla sessione SSH avviare la shell di HBase.

    	hbase shell
	
	Una volta caricata la shell, verrà visualizzato un prompt `hbase(main):001:0>`.

2. Visualizzare le righe dalla tabella:

    	scan 'SensorData'
		
	Dovrebbe essere restituite informazioni simili alle seguenti, che indicano che nella tabella sono disponibili 0 righe.
	
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

    > [AZURE.NOTE] Questa operazione di analisi restituirà solo un massimo di 10 righe dalla tabella.

## Eliminare i cluster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Per eliminare il cluster, la risorsa di archiviazione e l'app Web contemporaneamente, eliminare il gruppo di risorse che li contiene.

## Passaggi successivi

In questo articolo si è appreso come usare Storm per leggere dati dagli hub eventi, archiviarli in HBase e visualizzare informazioni in un dashboard esterno tramite Socket.io e D3.js.

* Per altri esempi di topologie Storm con HDinsight, vedere:

    * [Topologie di esempio per Storm in HDInsight](hdinsight-storm-example-topology.md)

* Per altre informazioni su Apache Storm, visitare il sito Web [Apache Storm](https://storm.incubator.apache.org/).

* Per altre informazioni su HBase in HDInsight, vedere la [panoramica su HBase con HDInsight](hdinsight-hbase-overview.md)

* Per altre informazioni su Socket.io, visitare il sito Web [socket.io](http://socket.io/).

* Per altre informazioni su D3.js, vedere [D3.js - Data Driven Documents](http://d3js.org/).

* Per altre informazioni sulla creazione di topologie in Java, vedere [Sviluppo di topologie basate su Java per Apache Storm in HDInsight](hdinsight-storm-develop-java-topology.md).

* Per altre informazioni sulla creazione di topologie in .NET, vedere [Sviluppare topologie C# per Apache Storm in HDInsight tramite Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

[azure-portal]: https://portal.azure.com

<!---HONumber=AcomDC_0914_2016-->