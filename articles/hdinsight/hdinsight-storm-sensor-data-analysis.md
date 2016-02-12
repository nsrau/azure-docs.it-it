<properties
   pageTitle="Analisi dei dati del sensore con Apache Storm e HBase | Microsoft Azure"
   description="Informazioni su come connettersi ad Apache Storm con una rete virtuale. Usare Storm con HBase per elaborare i dati del sensore da un hub eventi e visualizzarli con D3.js."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="01/28/2016"
   ms.author="larryfr"/>

# Analizzare i dati del sensore con Apache Storm, hub eventi e HBase in HDInsight (Hadoop)

Informazioni su come usare Apache Storm in HDInsight per elaborare i dati del sensore dall'hub eventi di Azure e visualizzarli con D3.j. Questo documento descrive inoltre come usare una rete virtuale di Azure per connettere Storm in HDInsight con HBase in HDInsight e archiviare in HBase i dati provenienti dalla topologia.

> [AZURE.NOTE] Le informazioni contenute in questo documento si basano sull'utilizzo di uno Storm basato su Windows nel cluster HDInsight. Per informazioni sull'utilizzo di hub eventi di Azure dallo Storm basato su Linux in HDInsight, vedere [Elaborare gli eventi da hub eventi di Azure con Storm in HDInsight](hdinsight-storm-develop-java-event-hub-topology.md)

## Prerequisiti

* Una sottoscrizione di Azure. Vedere [Ottenere una versione di valutazione gratuita di Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Un [cluster Apache Storm in HDInsight](hdinsight-apache-storm-tutorial-get-started.md)

* [Node.js](http://nodejs.org/): usato per il dashboard Web e per inviare dati del sensore all'hub eventi.

* [Java e JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/index.html)

* [Maven](http://maven.apache.org/what-is-maven.html)

* [Git](http://git-scm.com/)

> [AZURE.NOTE] Java, JDK, Maven e Git sono disponibili anche nel sito dello strumento di gestione dei pacchetti [Chocolatey NuGet](http://chocolatey.org/).

## Architettura

![diagramma dell'architettura](./media/hdinsight-storm-sensor-data-analysis/devicesarchitecture.png)

Questo esempio è costituito dai componenti seguenti:

* **Hub eventi di Azure**: fornisce i dati raccolti dai sensori. Ai fini dell'esempio è disponibile un'applicazione che genera dati fittizi.

* **Storm in HDInsight**: assicura l'elaborazione in tempo reale dei dati provenienti dall'hub eventi.

* **HBase in HDInsight** (facoltativo): offre un archivio persistente di dati NoSQL.

* **Servizio Rete virtuale di Azure** (facoltativo, obbligatorio se si usa HBase): abilita la comunicazione sicura tra cluster Storm in HDInsight e HBase in HDInsight.

* **Sito Web del dashboard**: dashboard di esempio che rappresenta graficamente i dati in tempo reale.

	* Il sito Web viene implementato in Node.js ed è quindi possibile eseguirlo a scopo di test su qualsiasi sistema operativo client. In alternativa, è possibile distribuirlo in Siti Web di Azure.

	* [Socket.io](http://socket.io/) viene usato per le comunicazioni in tempo reale tra la topologia Storm e il sito Web.

		> [AZURE.NOTE] Questo è un dettaglio di implementazione. È possibile usare qualsiasi framework di comunicazione, ad esempio WebSocket o SignalR non elaborato.

	* [D3.js](http://d3js.org/) viene usato per creare un grafico dei dati inviati al sito Web.

La topologia legge i dati provenienti dall'hub eventi usando la classe **com.microsoft.eventhubs.spout.EventHubSpout** fornita nel cluster Storm in HDInsight. Per la comunicazione con il sito Web viene usato [socket.io-client.java](https://github.com/nkzawa/socket.io-client.java).

Facoltativamente, per la comunicazione con HBase è possibile usare la classe [org.apache.storm.hbase.bolt.HBaseBolt](https://storm.apache.org/javadoc/apidocs/org/apache/storm/hbase/bolt/class-use/HBaseBolt.html), fornita come parte di Storm.

Di seguito è riportato un diagramma della topologia:

![diagramma della topologia](./media/hdinsight-storm-sensor-data-analysis/sensoranalysis.png)

> [AZURE.NOTE] Si tratta di una visualizzazione molto semplificata della topologia. In fase di esecuzione, per ciascuna partizione dell'hub eventi in fase di lettura viene creata un'istanza di ogni componente. Queste istanze vengono distribuite tra i nodi del cluster e i dati vengono instradati tra di essi, come indicato di seguito:
>
> * I dati trasmessi dallo spout al parser sono sottoposti a bilanciamento del carico.
> * I dati trasmessi dal parser al dashboard e a HBase (se usato) vengono raggruppati per ID dispositivo, in modo che i messaggi provenienti dallo stesso dispositivo vengano indirizzati sempre allo stesso componente.

### Componenti

* **Spout dell'hub eventi**: lo spout viene fornito come parte del repository [HDInsight Storm Examples](https://github.com/hdinsight/hdinsight-storm-examples) in GitHub.

* **ParserBolt.java**: i dati generati dallo spout sono dati JSON non elaborati. In alcuni casi vengono generati più eventi alla volta. Questo bolt mostra come leggere i dati generati dallo spout e indirizzarli a un nuovo flusso sotto forma di una tupla contenente più campi.

* **DashboardBolt.java**: viene mostrato come usare la libreria client Socket.io per Java per inviare dati in tempo reale al dashboard Web.

## Preparare l'ambiente

Prima di usare questo esempio, è necessario creare un hub eventi di Azure che viene letto dalla topologia Storm. È inoltre necessario creare una topologia Storm in HDInsight perché il componente usato per leggere i dati provenienti dall'hub eventi è disponibile solo nel cluster.

> [AZURE.NOTE] Alla fine lo spout dell'hub eventi sarà disponibile da Maven.

### Configurare l'hub eventi

L'hub eventi è l'origine dati per questo esempio. Per creare un nuovo hub eventi, seguire questa procedura.

1. Nel [Portale di Azure classico](https://manage.windowsazure.com) selezionare **NUOVO | Bus di servizio | Hub eventi | Creazione personalizzata**.

2. Nella finestra di dialogo **Crea un nuovo hub eventi** immettere un nome in **Nome hub eventi**, selezionare l'area in cui creare l'hub in **Area geografica** e quindi creare un nuovo spazio dei nomi o selezionarne uno esistente. Infine fare clic sulla freccia per continuare.

2. Nella finestra di dialogo **Configura hub eventi** immettere i valori per **Conteggio partizioni** e **Conservazione messaggi**. Per questo esempio usare un numero di partizioni pari a 10 e un valore di conservazione dei messaggi pari a 1.

3. Dopo la creazione dell'hub eventi, selezionare lo spazio dei nomi e quindi **Hub eventi**. Infine selezionare l'hub eventi creato in precedenza.

4. Selezionare **Configura** e quindi creare due nuovi criteri di accesso usando le informazioni seguenti.

	<table>
<tr><th>Name</th><th>Autorizzazioni</th></tr>
<tr><td>Dispositivi</td><td>Invia</td></tr>
<tr><td>Storm</td><td>Attesa</td></tr>
</table>Dopo avere creato le autorizzazioni, selezionare l'icona **Salva** nella parte inferiore della pagina. Verranno creati i criteri di accesso condivisi che saranno usati per inviare messaggi all'hub e per leggere i messaggi provenienti dall'hub.

5. Dopo avere salvato i criteri, usare **Generatore di chiavi di accesso condivise** nella parte inferiore della pagina per recuperare la chiave per i criteri **devices** e **storm**. Salvare le chiavi perché verranno usate in un secondo momento.

### Creare il cluster Storm in HDInsight

1. Accedere al [portale di Azure classico](https://manage.windowsazure.com/).

2. Fare clic su **HDInsight** nel riquadro sinistro e quindi fare clic su **+NUOVO** nell'angolo inferiore sinistro della pagina.

3. Fare clic sull'icona di HDInsight nella seconda colonna e quindi selezionare **Personalizzato**.

4. Nella pagina **Dettagli cluster** immettere il nome del nuovo cluster e selezionare **Storm** in **Tipo di cluster**. Fare clic sulla freccia per continuare.

5. Immettere 1 in **Nodi dati** per indicare il numero di nodi per il cluster.

	> [AZURE.NOTE] Per contenere il costo del cluster usato in questo articolo, ridurre il valore di **Dimensione del cluster** a 1 ed eliminare il cluster al termine della procedura.

6. Immettere **Nome utente** e **Password** dell'amministratore e quindi fare clic sulla freccia per continuare.

4. In **Account di archiviazione** selezionare **Crea nuova archiviazione** o selezionare un account di archiviazione esistente. Selezionare o immettere il **Nome account** e il **Contenitore predefinito** da usare. Selezionare l'icona del segno di spunta nell'angolo inferiore sinistro per creare il cluster Storm.

## Scaricare e installare EventHubSpout

1. Scaricare il [progetto HDInsight Storm Examples](https://github.com/hdinsight/hdinsight-storm-examples/). Dopo aver eseguito il download, trovare il file **lib/eventhubs/eventhubs-storm-spout-0.9-jar-with-dependencies.jar**.

2. Al prompt dei comandi usare il comando seguente per installare il file **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** nell'archivio locale Maven. In questo modo sarà possibile aggiungerlo facilmente come riferimento nel progetto Storm in un secondo momento.

		mvn install:install-file -Dfile=target/eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar

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

Aprire il file **Config.properties** e aggiungere le informazioni usate in precedenza durante la creazione dell'hub eventi. Salvare il file dopo aver aggiunto tali informazioni.

	eventhubspout.username = storm

	eventhubspout.password = <the key of the 'storm' policy>

	eventhubspout.namespace = <the event hub namespace>

	eventhubspout.entitypath = <the event hub name>

	eventhubspout.partitions.count = <the number of partitions for the event hub>

	## if not provided, will use storm's zookeeper settings
	## zookeeper.connectionstring=localhost:2181

	eventhubspout.checkpoint.interval = 10

	eventhub.receiver.credits = 1024

## Compilare ed eseguire il test in locale

Prima di eseguire il test, è necessario avviare il dashboard per visualizzare l'output della topologia e generare i dati da archiviare nell'hub eventi.

### Avviare l'applicazione Web

1. Aprire un nuovo prompt dei comandi o terminal e passare alla directory **hdinsight-eventhub-example/dashboard**. Usare quindi il comando seguente per installare le dipendenze necessarie per l'applicazione Web:

		npm install

2. Usare il comando seguente per avviare l'applicazione Web:

		node server.js

	Verrà visualizzato un messaggio simile al seguente:

		Server listening at port 3000

2. Aprire un Web browser e immettere ****http://localhost:3000/** come indirizzo. Verrà visualizzata una pagina simile alla seguente:

	![dashboard Web](./media/hdinsight-storm-sensor-data-analysis/emptydashboard.png)

	Lasciare il prompt dei comandi o il terminal aperto. Al termine del test, usare CTRL+C per arrestare il server Web.

### Avviare la generazione dei dati

> [AZURE.NOTE] I passaggi descritti in questa sezione usano Node.js, così da poter essere eseguiti su qualsiasi piattaforma. Per esempi in altri linguaggi, vedere la directory **SendEvents**.


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

		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":0,"Temperature":7}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":1,"Temperature":39}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":2,"Temperature":86}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":3,"Temperature":29}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":4,"Temperature":30}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":5,"Temperature":5}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":6,"Temperature":24}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":7,"Temperature":40}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":8,"Temperature":43}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":9,"Temperature":84}

### Avviare la topologia

2. Avviare la topologia in locale usando il comando seguente:

	mvn compile exec:java -Dstorm.topology=com.microsoft.examples.Temperature

	Questo comando avvia la topologia, legge i file dall'hub eventi e li invia al dashboard in esecuzione in Siti Web di Azure. Nel dashboard Web dovrebbero comparire linee simili alle seguenti:

	![dashboard con dati](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

3. Mentre il dashboard è in esecuzione, usare il comando `node app.js` dei passaggi precedenti per inviare nuovi dati al dashboard. Poiché i valori di temperatura vengono generati in modo casuale, il grafico deve aggiornarsi per visualizzare i nuovi valori.

3. Dopo aver verificato il corretto funzionamento di questo meccanismo, arrestare la topologia digitando CTRL+C. Per arrestare l'app SendEvent, selezionare la finestra e premere un tasto qualsiasi. È possibile usare CTRL+C anche per arrestare il server Web.

## Creare il pacchetto della topologia e distribuirlo in HDInsight

Nell'ambiente di sviluppo seguire questa procedura per eseguire la topologia Temperature nel cluster Storm in HDInsight.

### Pubblicare il dashboard del sito Web

1. Per distribuire il dashboard in un sito Web di Azure, seguire la procedura descritta nell'articolo relativo alla [creazione e distribuzione di un sito Web Node.js in Azure](../app-service-web/web-sites-nodejs-develop-deploy-mac.md). Notare che l'URL del sito Web sarà simile a **mywebsite.azurewebsites.net**.

2. Dopo aver creato il sito Web, passare al sito nel portale di Azure classico e selezionare la scheda **Configura**. Abilitare **Web Socket** e quindi fare clic su **Salva** nella parte inferiore della pagina.

2. Aprire **hdinsight-eventhub-example\\TemperatureMonitor\\src\\main\\java\\com\\microsoft\\examples\\bolts\\DashboardBolt.java** e modificare la riga seguente in modo che punti all'URL del dashboard pubblicato:

		socket = IO.socket("http://mywebsite.azurewebsites.net");

3. Salvare il file **DashboardBolt.java**.

### Creare il pacchetto della topologia e distribuirlo

1. Usare il comando seguente per creare un pacchetto JAR dal progetto:

		mvn package

	Verrà creato un file denominato **TemperatureMonitor-1.0-SNAPSHOT.jar** nella directory **target** del progetto.

2. Seguire la procedura descritta in [Distribuzione e gestione di topologie Apache Storm in HDInsight](hdinsight-storm-deploy-monitor-topology.md) per caricare e avviare la topologia sul cluster Storm in HDInsight usando **Storm Dashboard**.

3. Dopo aver avviato la topologia, aprire un browser al sito Web pubblicato in Azure e quindi usare il comando `node app.js` per inviare dati all'hub eventi. Verrà visualizzato il dashboard Web aggiornato per mostrare le informazioni.

	![dashboard](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

## Facoltativo: usare HBase

Per usare insieme Storm e HBase, è necessario creare una rete virtuale di Azure e creare un cluster Storm e HBase all'interno di tale rete.

### Creare una rete virtuale di Azure (facoltativo)

Se si prevede di usare HBase con questo esempio, è necessario creare una rete virtuale di Azure contenente un cluster Storm in HDInsight e un cluster HBase in HDInsight.

1. Accedere al [portale di Azure classico](https://manage.windowsazure.com).

2. Nella parte inferiore della pagina fare clic su **+NUOVO** > **Servizi di rete** > **Rete virtuale** > **Creazione rapida**.

3. Digitare o selezionare i valori seguenti:

	- **Nome**: nome della rete virtuale.

	- **Spazio degli indirizzi**: scegliere uno spazio degli indirizzi per la rete virtuale di dimensioni sufficienti per fornire indirizzi per tutti i nodi del cluster. In caso contrario, il provisioning avrà esito negativo.

	- **Numero massimo VM**: scegliere uno dei valori disponibili per il numero massimo di macchine virtuali.

	- **Percorso**: il percorso deve essere uguale a quello del cluster HBase che verrà creato.

	- **Server DNS**: questo articolo usa un server DNS interno fornito da Azure. È quindi possibile scegliere **Nessuno**. Sono supportate anche configurazioni di rete più avanzate con server DNS personalizzati. Per istruzioni dettagliate, vedere [Risoluzione dei nomi (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

4. Fare clic su **Crea rete virtuale**. Il nome della rete virtuale sarà visualizzato nell'elenco. Attendere l'impostazione del valore **Creata** nella colonna Stato.

5. Nel riquadro principale fare clic sulla rete virtuale appena creata.

6. Nella parte superiore della pagina fare clic su **DASHBOARD**.

7. In **riepilogo rapido** annotare l'**ID RETE VIRTUALE**. Questa informazione sarà necessaria per il provisioning dei cluster Storm e HBase.

8. Nella parte superiore della pagina fare clic su **CONFIGURA**.

9. Nella parte inferiore della pagina il nome predefinito per la subnet è **Subnet-1**. Usare il pulsante **aggiungi subnet** per aggiungere **Subnet-2**. Queste subnet includeranno i cluster Storm e HBase.

	> [AZURE.NOTE] In questo articolo si usano cluster con un solo nodo. Se si creano cluster con più nodi, verificare il valore di **CIDR (CONTEGGIO INDIRIZZI)** per la subnet che sarà usata per il cluster. Il conteggio di indirizzi deve essere superiore al numero di nodi di lavoro più sette (Gateway: 2, Nodo head: 2, Zookeeper: 3). Ad esempio, se è necessario un cluster HBase da 10 nodi, il conteggio di indirizzi per la subnet dovrà essere maggiore di 17 (10+7). In caso contrario, la distribuzione avrà esito negativo.
	>
	> È consigliabile designare una singola subnet per un cluster.

11. Fare clic su **Salva** nella parte inferiore della pagina.

### Creare un cluster Storm e HBase nella rete virtuale

1. Accedere al [portale di Azure classico](https://manage.windowsazure.com/).

2. Fare clic su **HDInsight** nel riquadro sinistro e quindi fare clic su **+NUOVO** nell'angolo inferiore sinistro della pagina.

3. Fare clic sull'icona di HDInsight nella seconda colonna e quindi selezionare **Personalizzato**.

4. Nella pagina **Dettagli cluster** immettere il nome del nuovo cluster e selezionare **Storm** in **Tipo di cluster**. Fare clic sulla freccia per continuare.

5. Immettere 1 in **Nodi dati** per indicare il numero di nodi per il cluster. In **Area/Rete virtuale** selezionare la rete virtuale di Azure creata in precedenza. Per **Subnet della rete virtuale** selezionare **Subnet-1**.

	> [AZURE.NOTE] Per contenere il costo del cluster usato in questo articolo, ridurre il valore di **Dimensione del cluster** a 1 ed eliminare il cluster al termine della procedura.

6. Immettere **Nome utente** e **Password** dell'amministratore e quindi fare clic sulla freccia per continuare.

4. In **Account di archiviazione** selezionare **Crea nuova archiviazione** o selezionare un account di archiviazione esistente. Selezionare o immettere il **Nome account** e il **Contenitore predefinito** da usare. Selezionare l'icona del segno di spunta nell'angolo inferiore sinistro per creare il cluster Storm.

5. Ripetere questi passaggi per creare un nuovo cluster **HBase**. Di seguito sono riportate le differenze principali:

	* **Tipo di cluster**: selezionare **HBase**

	* **Subnet della rete virtuale**: selezionare **Subnet-2**

	* **Account di archiviazione**: è consigliabile usare un contenitore diverso da quello usato per il cluster Storm.

### Individuare il suffisso DNS di HBase

Per scrivere in HBase dal cluster Storm, è necessario usare il nome di dominio completo del cluster HBase. Per individuare queste informazioni, usare il comando seguente:

	curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

Nei dati JSON restituiti, trovare la voce **"host\_name"**. Questa conterrà il nome di dominio completo per i nodi del cluster, ad esempio:

	...
	"host_name": "wordkernode0.<clustername>.b1.cloudapp.net
	...

La porzione del nome di dominio che inizia con il nome del cluster è il suffisso DNS, ad esempio, **mycluster.b1.cloudapp.net**.

### Abilitare il bolt HBase

1. Aprire **hdinsight-eventhub-example\\TemperatureMonitor\\conf\\hbase-site.xml** e sostituire le voci `suffix` nella riga seguente con il suffisso DNS ottenuto in precedenza per il cluster HBase. Dopo aver apportato queste modifiche, salvare il file.

		<value>zookeeper0.suffix,zookeeper1.suffix,zookeeper2.suffix</value>

	Questo file verrà usato dal bolt HBase per comunicare con il cluster HBase.

1. Aprire **hdinsight-eventhub-example\\TemperatureMonitor\\src\\main\\java\\com\\microsoft\\examples\\bolts** in un editor di testo e rimuovere i commenti dalle righe seguenti eliminando `//` all'inizio. Dopo aver apportato queste modifiche, salvare il file.

		topologyBuilder.setBolt("HBase", new HBaseBolt("SensorData", mapper).withConfigKey("hbase.conf"), spoutConfig.getPartitionCount())
    	  .fieldsGrouping("Parser", "hbasestream", new Fields("deviceid")).setNumTasks(spoutConfig.getPartitionCount());

	Questa operazione consente di abilitare il bolt HBase.

	> [AZURE.NOTE] Attivare il bolt HBase solo durante la distribuzione nel cluster Storm, non durante l'esecuzione del test in locale.

### Dati HBase e Storm

Prima di eseguire la topologia, è necessario preparare HBase per l'accettazione dei dati.

1. Connettersi al cluster HBase tramite Desktop remoto.

2. Dal desktop avviare le riga di comando di HDInsight e immettere i comandi seguenti:

    cd %HBASE\_HOME% bin\\hbase shell

3. Dalla shell HBase immettere il comando seguente per creare una tabella in cui archiviare i dati del sensore:

    create 'SensorData', 'cf'

4. Verificare che la tabella non contenga dati immettendo il comando seguente:

    scan 'SensorData'

Dopo aver avviato la topologia nel cluster Storm ed elaborato i dati, è possibile usare di nuovo il comando `scan 'SensorData'` per verificare che i dati siano stati inseriti in HBase.


## Passaggi successivi

In questo articolo è stato illustrato come usare Storm per leggere dati dall'hub eventi e visualizzare informazioni da Storm su un dashboard esterno mediante SignalR e D3.js. Se sono stati eseguiti i passaggi facoltativi, si è appreso anche come configurare HDInsight in una rete virtuale e come consentire la comunicazione tra una topologia Storm e HBase mediante il bolt HBase.

* Per altri esempi di topologie Storm con HDinsight, vedere:

    * [Topologie di esempio per Storm in HDInsight](hdinsight-storm-example-topology.md)

* Per altre informazioni su Apache Storm, visitare il sito Web [Apache Storm](https://storm.incubator.apache.org/).

* Per altre informazioni su HBase in HDInsight, vedere la [panoramica su HBase con HDInsight](hdinsight-hbase-overview.md)

* Per altre informazioni su Socket.io, visitare il sito Web [socket.io](http://socket.io/).

* Per altre informazioni su D3.js, vedere [D3.js - Data Driven Documents](http://d3js.org/).

* Per altre informazioni sulla creazione di topologie in Java, vedere [Sviluppo di topologie basate su Java per Apache Storm in HDInsight](hdinsight-storm-develop-java-topology.md).

* Per altre informazioni sulla creazione di topologie in .NET, vedere [Sviluppare topologie C# per Apache Storm in HDInsight tramite Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

[azure-portal]: https://manage.windowsazure.com/

<!---HONumber=AcomDC_0204_2016-->