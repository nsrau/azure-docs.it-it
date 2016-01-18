<properties 
	pageTitle="Utilizzare Hub eventi di Azure con Apache Spark in HDInsight per l'elaborazione del flusso di dati | Microsoft Azure" 
	description="Istruzioni dettagliate su come inviare un flusso di dati a Hub eventi di Azure e quindi ricevere tali eventi in Spark usando un'applicazione Scala" 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/29/2015" 
	ms.author="nitinme"/>


# Streaming Spark: Elaborare eventi da Hub eventi di Azure con Apache Spark in HDInsight (Linux)

Streaming Spark estende l'API di Spark per compilare applicazioni di elaborazione scalabili, a velocità effettiva elevata e tolleranza d'errore del flusso principale. I dati possono essere acquisiti da molte origini. In questo articolo si userà Hub eventi di Azure per l'inserimento di dati. Hub eventi è un sistema altamente scalabile dell'inserimento tale che può assumere milioni di eventi al secondo.

In questa esercitazione si apprenderà come creare un Hub eventi di Azure, come inserire messaggi in un hub eventi usando un'applicazione console in Java e recuperarli in parallelo con un'applicazione Spark scritta in Scala. L'applicazione utilizza i dati trasmessi tramite Hub eventi e li instrada a output diversi, come BLOB di archiviazione di Azure, tabelle Hive e tabelle SQL.

> [AZURE.NOTE]Per seguire le istruzioni riportate in questo articolo, è necessario utilizzare entrambe le versioni del portale di Azure. Per creare un Hub eventi si utilizzerà il [portale di Azure](https://manage.windowsazure.com). Per utilizzare il cluster HDInsight Spark, si utilizzerà il [portale di anteprima di Azure](https://ms.portal.azure.com/).

**Prerequisiti:**

È necessario disporre di quanto segue:

- Una sottoscrizione di Azure. Vedere [Ottenere una versione di valutazione gratuita di Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un cluster Apache Spark. Per istruzioni, vedere [Creare cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).
- Oracle Java Development Kit. Per installarlo, fare clic [qui](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
- Ambiente IDE Java. Questo articolo usa IntelliJ IDEA 15.0.1. Per installarlo, fare clic [qui](https://www.jetbrains.com/idea/download/).
- Microsoft JDBC Driver per SQL Server v4.1 o successiva. È necessario scrivere dati eventi in un database SQL Server. Per installarlo, fare clic [qui](https://msdn.microsoft.com/sqlserver/aa937724.aspx).
- un database SQL di Azure. Per istruzioni, vedere [Creare un database SQL in pochi minuti](sql-database/sql-database-get-started.md)

## Scopo di questa soluzione

Ecco il flusso di questa soluzione di streaming:

1. Creare un Hub eventi di Azure che riceve un flusso di eventi.

2. Eseguire un'applicazione autonoma locale che genera eventi e ne effettua il push all'Hub eventi di Azure. L'applicazione di esempio che esegue questa operazione è pubblicata all'indirizzo [https://github.com/hdinsight/spark-streaming-data-persistence-examples](https://github.com/hdinsight/spark-streaming-data-persistence-examples).

2. Eseguire un'applicazione di streaming in modalità remota in un cluster Spark che legge eventi di streaming dall'Hub eventi di Azure e ne effettua il push a lo inserisce in posizioni diverse, come BLOB di Azure, tabelle Hive e di database SQL.

## Creare Hub eventi di Azure

1. Nel [Portale di Azure](https://manage.windowsazure.com) selezionare **NUOVO** > **Bus di servizio** > **Hub eventi** > **Creazione personalizzata**.

2. Nella schermata **Aggiungi hub eventi** immettere un nome in **Nome hub eventi**, selezionare l'area in cui creare l'hub in **Area geografica** e creare un nuovo spazio dei nomi o selezionarne uno esistente. Fare clic sulla **freccia** per continuare.

	![procedura guidata - pagina 1](./media/hdinsight-apache-spark-eventhub-streaming/hdispark.streaming.create.event.hub.png "Creare un Hub di eventi di Azure")

	> [AZURE.NOTE]È necessario selezionare lo stesso **Percorso** del cluster Apache Spark in HDInsight per ridurre i costi e la latenza.

3. Nella schermata **Configura hub eventi** immettere i valori **Conteggio partizioni** e **Conservazione messaggi** e quindi fare clic sul segno di spunta. Per questo esempio usare un numero di partizioni pari a 10 e un valore di conservazione dei messaggi pari a 1. Prendere nota del numero di partizioni, perché questo valore sarà necessario in seguito.

	![procedura guidata - pagina 2](./media/hdinsight-apache-spark-eventhub-streaming/hdispark.streaming.create.event.hub2.png "Specificare i giorni di conservazione e la dimensione di partizione per Hub di eventi")

4. Fare clic sull'hub eventi creato, su **Configura** e quindi creare due criteri di accesso per l'hub eventi.

	<table>
<tr><th>Nome</th><th>Autorizzazioni</th></tr>
<tr><td>mysendpolicy</td><td>Invio</td></tr>
<tr><td>myreceivepolicy</td><td>Attesa</td></tr>
</table>Dopo avere creato le autorizzazioni, selezionare l'icona **Salva** nella parte inferiore della pagina. Ciò consente di creare criteri di accesso condiviso che saranno usati per l'invio (**mysendpolicy**) e l'ascolto (**myreceivepolicy**) in questo hub eventi.

	![criteri](./media/hdinsight-apache-spark-eventhub-streaming/hdispark.streaming.event.hub.policies.png "Creare criteri di Hub di eventi")

	
5. Nella stessa pagina, prendere nota delle chiavi di criteri generati per i due criteri. Salvare queste informazioni perché verranno usate in seguito.

	![chiavi dei criteri](./media/hdinsight-apache-spark-eventhub-streaming/hdispark.streaming.event.hub.policy.keys.png "Salvare le chiavi dei criteri")

6. Nella pagina **Dashboard** fare clic su **Informazioni di connessione** in basso per recuperare e salvare le stringhe di connessione per l'hub eventi usando i due criteri.

	![chiavi dei criteri](./media/hdinsight-apache-spark-eventhub-streaming/hdispark.streaming.event.hub.policy.connection.strings.png "Salvare le stringhe di connessione criteri")

## Usare un'applicazione Scala per inviare messaggi all'Hub eventi

In questa sezione si userà un'applicazione Scala locale autonoma per inviare un flusso di eventi all'Hub eventi di Azure creato nel passaggio precedente. Questa applicazione è disponibile in GitHub all'indirizzo [https://github.com/hdinsight/eventhubs-sample-event-producer](https://github.com/hdinsight/eventhubs-sample-event-producer). Questi passaggi presuppongono che sia già stato eseguito il fork di questo repository GitHub.

1. Aprire l'applicazione **EventhubsSampleEventProducer** in IntelliJ IDEA.
	
2. Compilare il progetto. Scegliere **Make Project** dal menu **Build**. Il file JAR di output viene creato in **\\out\\artifacts**.

>[AZURE.TIP]È anche possibile usare un'opzione disponibile in IntelliJ IDEA per creare il progetto direttamente da un repository GitHub. Per sapere come usare questo approccio, vedere le istruzioni nella sezione successiva per informazioni aggiuntive. Si noti che molti passaggi descritti nella sezione successiva non sono applicabili per l'applicazione Scala creata in questo passaggio. Ad esempio:

> * Non è necessario aggiornare il modello POM per includere la versione di Spark, perché non esistono dipendenze da Spark per la creazione di questa applicazione.
> * Non è necessario aggiungere alcuni file JAR di dipendenza nella libreria del progetto, perché i file JAR non sono richiesti per il progetto.

## Aggiornare l'applicazione di streaming Scala per la ricezione di eventi

Un'applicazione Scala di esempio per ricevere l'evento e instradarlo a destinazioni diverse è disponibile all'indirizzo [https://github.com/hdinsight/spark-streaming-data-persistence-examples](https://github.com/hdinsight/spark-streaming-data-persistence-examples). Seguire questa procedura per aggiornare l'applicazione e creare il file JAR di output.

1. Avviare IntelliJ IDEA e dalla schermata di avvio selezionare **Check out from Version Control** e quindi fare clic su **Git**.
		
	![Ottenere origini da Git](./media/hdinsight-apache-spark-eventhub-streaming/get-source-from-git.png)

2. Nella finestra di dialogo **Clone Repository** immettere l'URL del repository Git da cui eseguire la clonazione, specificare la directory da clonare e quindi fare clic su **Clone**.

	![Clonare da Git](./media/hdinsight-apache-spark-eventhub-streaming/clone-from-git.png)

	
3. Seguire le istruzioni finché la clonazione del progetto non sarà completa. Premere **ALT+1** per aprire la **Project View**, che dovrebbe essere simile alla seguente.

	![Project View](./media/hdinsight-apache-spark-eventhub-streaming/project-view.png)
	
4. Aprire il file pom.xml e verificare che la versione di Spark sia corretta. Nel nodo <properties> cercare il frammento seguente e verificare la versione di Spark.

		<scala.version>2.10.4</scala.version>
    	<scala.compat.version>2.10.4</scala.compat.version>
    	<scala.binary.version>2.10</scala.binary.version>
    	<spark.version>1.5.1</spark.version>

	Assicurarsi che il valore di **spark.version** sia impostato su **1.5.1**.

5. L'applicazione richiede due file JAR di dipendenza:

	* **File JAR del ricevitore EventHub**. È necessario per consentire a Spark di ricevere i messaggi dall'Hub eventi. Il file JAR è disponibile nel cluster Linux Spark in `/usr/hdp/current/spark-client/lib/spark-streaming-eventhubs-example-1.5.1.2.3.2.1-12-jar-with-dependencies.jar`. È possibile usare pscp per copiare il file JAR nel computer locale.

			pscp sshuser@mysparkcluster-ssh.azurehdinsight.net/usr/hdp/current/spark-client/lib/spark-streaming-eventhubs-example-1.5.1.2.3.2.1-12-jar-with-dependencies.jar C:/eventhubjar

		Il file JAR verrà copiato dal cluster Spark nel computer locale.

	* **File JAR del driver JDBC**. È necessario per scrivere i messaggi ricevuti dall'Hub eventi in un database SQL di Azure. È possibile scaricare la versione 4.1 o successiva di questo file JAR [qui](https://msdn.microsoft.com/it-IT/sqlserver/aa937724.aspx).
	

		Aggiungere riferimenti a questi file JAR nella libreria del progetto. Eseguire la procedura seguente:

		1. Nella finestra di IntelliJ IDEA in cui è aperta l'applicazione fare clic su **File**, su **Project Structure** e quindi su **Libraries**. 

			![aggiungere dipendenze mancanti](./media/hdinsight-apache-spark-eventhub-streaming/add-missing-dependency-jars.png "aggiungere file JAR di dipendenza mancanti")

			Fare clic sull'icona di aggiunta (![icona per l'aggiunta](./media/hdinsight-apache-spark-eventhub-streaming/add-icon.png)), fare clic su **Java** e quindi passare al percorso in cui è stato scaricato il file JAR del ricevitore EventHub. Seguire le istruzioni per aggiungere il file JAR alla libreria del progetto.

		1. Ripetere il passaggio precedente per aggiungere anche il file JAR JDBC alla libreria del progetto.
	
			![aggiungere dipendenze mancanti](./media/hdinsight-apache-spark-eventhub-streaming/add-missing-dependency-jars.png "aggiungere file JAR di dipendenza mancanti")

		1. Fare clic su **Apply**.

6. Creare il file JAR di output. Seguire questa procedura.
	1. Nella finestra di dialogo **Project Structure** fare clic su **Artifacts** e quindi sul segno più. Nella finestra di dialogo popup fare clic su **JAR**, quindi fare clic su **From modules with dependencies**.

		![Creare un file JAR](./media/hdinsight-apache-spark-eventhub-streaming/create-jar-1.png)

	1. Nella finestra di dialogo **Create JAR from Modules** fare clic sui puntini di sospensione (![puntini di sospensione](./media/hdinsight-apache-spark-eventhub-streaming/ellipsis.png)) relativi a **Main Class**.

	1. Nel finestra di dialogo **Select Main Class** selezionare una delle classi disponibili e quindi fare clic su **OK**.

		![Creare un file JAR](./media/hdinsight-apache-spark-eventhub-streaming/create-jar-2.png)

	1. Nella finestra di dialogo **Create JAR from Modules** verificare che l'opzione per **estrarre nel file JAR di destinazione** sia selezionata e quindi fare clic su **OK**. Verrà creato un singolo file JAR con tutte le dipendenze.

		![Creare un file JAR](./media/hdinsight-apache-spark-eventhub-streaming/create-jar-3.png)

	1. Nella scheda **Output Layout** sono elencati tutti i file JAR inclusi nel progetto Maven. È possibile selezionare ed eliminare quelli in cui l'applicazione Scala non ha dipendenze dirette. Per l'applicazione che si sta creando è possibile rimuovere tutti i file tranne l'ultimo, **microsoft-spark-streaming-examples compile output**. Selezionare i file JAR da eliminare e quindi fare clic sull'icona **Delete** (![icona di eliminazione](./media/hdinsight-apache-spark-eventhub-streaming/delete-icon.png)).

		![Creare un file JAR](./media/hdinsight-apache-spark-eventhub-streaming/delete-output-jars.png)

		Assicurarsi che la casella **Build on make** sia selezionata per garantire che il file jar venga creato ogni volta che il progetto viene creato o aggiornato. Fare clic su **Apply** e quindi su **OK**.

	1. Nella scheda **Output Layout** in fondo alla casella Available Elements sono disponibili i due file JAR di dipendenza aggiunti in precedenza alla libreria del progetto. È necessario aggiungerli alla scheda Output Layout. Fare clic con il pulsante destro del mouse su ogni file con estensione JAR e quindi scegliere **Extract Into Output Root**.

		![Estrarre file JAR di dipendenza](./media/hdinsight-apache-spark-eventhub-streaming/extract-dependency-jar.png)

		Ripetere questo passaggio per l'altro file JAR di dipendenza. La scheda **Output Layout** dovrebbe essere simile alla seguente.

		![Scheda output finale](./media/hdinsight-apache-spark-eventhub-streaming/final-output-tab.png)

		Nella finestra di dialogo **Project Structure** fare clic su **Apply** e quindi su **OK**.

	1. Sulla barra dei menu fare clic su **Build**e quindi su **Make Project**. È anche possibile fare clic su **Build Artifacts** per creare il file JAR. Il file JAR di output viene creato in **\\out\\artifacts**.

		![Creare un file JAR](./media/hdinsight-apache-spark-create-standalone-application/output.png)

## Eseguire le applicazioni in modalità remota in un cluster Spark usando Livy

Per eseguire l'applicazione di streaming in modalità remota in un cluster Spark si userà Livy. Per informazioni dettagliate sull'uso di Livy il cluster Spark HDInsight, vedere [Inviare processi in modalità remota a un cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-livy-rest-interface.md). Prima di avviare l'esecuzione di processi remoti per lo streaming di eventi con Spark, ecco un paio di operazioni che è necessario eseguire:

1. Avviare l'applicazione autonoma locale per generare eventi e inviarli all'Hub eventi. A questo scopo, usare il comando seguente:

		java -cp EventhubsSampleEventProducer.jar com.microsoft.eventhubs.client.example.EventhubsClientDriver --eventhubs-namespace "mysbnamespace" --eventhubs-name "myeventhub" --policy-name "mysendpolicy" --policy-key "<policy key>" --message-length 32 --thread-count 32 --message-count -1

2. Copiare il file JAR di streaming (**microsoft-spark-streaming-examples.jar**) nell'archivio BLOB di Azure associato al cluster. Questa operazione rende il file JAR accessibile a Livy. A questo scopo è possibile usare [**AzCopy**](storage/storage-use-azcopy.md), un'utilità della riga di comando. È possibile usare molti altri client per caricare i dati. Altre informazioni su questi client sono disponibili in [Caricare dati per processi Hadoop in HDInsight](hdinsight-upload-data.md).

3. Installare CURL nel computer in cui si eseguono tali applicazioni. CURL viene usato per richiamare gli endpoint Livy per eseguire i processi in modalità remota.

### Eseguire le applicazioni per ricevere gli eventi in un BLOB di archiviazione di Azure come testo

Aprire un prompt dei comandi, passare alla directory in cui è installato CURL ed eseguire il comando seguente (sostituire nome utente/password e nome cluster):

	curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputBlob.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

I parametri nel file **inputBlob.txt** sono definiti come segue:

	{ "file":"wasb:///example/jars/microsoft-spark-streaming-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsEventCount", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Ecco una descrizione dei parametri nel file di input:

* **file** è il percorso del file JAR dell'applicazione è già stato copiato nell'account di archiviazione di Azure associato al cluster.
* **className** è il nome della classe nel file JAR.
* **args** è l'elenco di argomenti richiesti dalla classe.
* **numExecutors** è il numero di core usati da Spark per eseguire l'applicazione di streaming. Deve essere sempre almeno il doppio del numero di partizioni dell'Hub eventi.
* **executorMemory**, **executorCores**, **driverMemory** sono i parametri usati per assegnare le risorse richieste per l'applicazione di streaming.

>[AZURE.NOTE]Non è necessario creare le cartelle di output (EventCheckpoint, EventCount/EventCount10) usate come parametri, perché vengono create automaticamente dall'applicazione di streaming.
	
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

L'esecuzione dell'applicazione continuerà fino a quando non viene terminata. A questo scopo, usare il comando seguente:

	curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/1"

### Eseguire le applicazioni per ricevere gli eventi in un BLOB di archiviazione di Azure come JSON

Aprire un prompt dei comandi, passare alla directory in cui è installato CURL ed eseguire il comando seguente (sostituire nome utente/password e nome cluster):

	curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputJSON.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

I parametri nel file **inputJSON.txt** sono definiti come segue:

	{ "file":"wasb:///example/jars/microsoft-spark-streaming-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToAzureBlobAsJSON", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--event-store-folder", "/EventStore10"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

I parametri sono simili a quanto specificato per l'output di testo nel passaggio precedente. Anche in questo caso non è necessario creare le cartelle di output (EventCheckpoint, EventCount/EventCount10) usate come parametri, perché vengono create automaticamente dall'applicazione di streaming.

 Dopo l'esecuzione del comando, è possibile esaminare l'account di archiviazione di Azure associato al cluster, dove sarà visualizzata la cartella **/EventStore10** creata. Aprire un file qualsiasi con prefisso **part-** dove saranno visualizzati gli eventi elaborati in un formato JSON.

### Eseguire le applicazioni per ricevere gli eventi in una tabella Hive

Per eseguire l'applicazione che trasmette eventi a una tabella Hive, sono necessari alcuni componenti aggiuntivi. Si tratta di:

* datanucleus-api-jdo-3.2.6.jar
* datanucleus-rdbms-3.2.9.jar
* datanucleus-core-3.2.10.jar
* hive-site.xml

I file con estensione **.jar** sono disponibili nel cluster HDInsight Spark in `/usr/hdp/current/spark-client/lib`. Il file **hive-site.xml** è disponibile in `/usr/hdp/current/spark-client/conf`.

È possibile usare [WinScp](http://winscp.net/eng/download.php) per copiare i file dal cluster nel computer locale. È quindi possibile usare strumenti per copiare questi file nell'account di archiviazione associato al cluster. Per altre informazioni su come caricare i file nell'account di archiviazione, vedere [Caricare dati per processi Hadoop in HDInsight](hdinsight-upload-data.md).

Dopo avere copiato i file nell'account di archiviazione di Azure, aprire un prompt dei comandi, passare alla directory in cui è installato CURL ed eseguire il comando seguente (sostituire nome utente/password e nome cluster):

	curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputHive.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

I parametri nel file **inputHive.txt** sono definiti come segue:

	{ "file":"wasb:///example/jars/microsoft-spark-streaming-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToHiveTable", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--event-hive-table", "EventHiveTable10" ], "jars":["wasb:///example/jars/datanucleus-api-jdo-3.2.6.jar", "wasb:///example/jars/datanucleus-rdbms-3.2.9.jar", "wasb:///example/jars/datanucleus-core-3.2.10.jar"], "files":["wasb:///example/jars/hive-site.xml"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

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


### Eseguire le applicazioni per ricevere gli eventi in una tabella di database SQL di Azure

Prima di eseguire questo passaggio, assicurarsi che sia stato creato un database SQL di Azure. Saranno necessari valori per il nome del database, nome del server di database e le credenziali di amministratore del database come parametri. Non è però necessario creare la tabella di database, perché viene creata automaticamente dall'applicazione di streaming.

Aprire un prompt dei comandi, passare alla directory in cui è installato CURL ed eseguire il comando seguente:

	curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputSQL.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

I parametri nel file **inputSQL.txt** sono definiti come segue:

	{ "file":"wasb:///example/jars/microsoft-spark-streaming-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToAzureSQLTable", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--sql-server-fqdn", "<database-server-name>.database.windows.net", "--sql-database-name", "mysparkdatabase", "--database-username", "sparkdbadmin", "--database-password", "<put-password-here>", "--event-sql-table", "EventContent" ], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Per verificare che l'applicazione venga eseguita correttamente, è possibile connettersi al database SQL di Azure con SQL Server Management Studio. Per istruzioni su come eseguire questa operazione, vedere [Connettersi al database SQL con SQL Server Management Studio](sql-database/sql-database-connect-query-ssms). Una volta connessi al database, è possibile passare alla tabella il **EventContent** creata dall'applicazione di streaming. È possibile eseguire una query rapida per ottenere i dati dalla tabella. Eseguire questa query:

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

### Scenari

* [Spark con Business Intelligence: eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](hdinsight-apache-spark-use-bi-tools.md)

* [Spark con Machine Learning: usare Spark in HDInsight per l'analisi della temperatura dell'edificio mediante dati HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark con Machine Learning: usare Spark in HDInsight per prevedere i risultati del controllo degli alimenti](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Analisi dei log del sito Web con Spark in HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### Creare ed eseguire applicazioni

* [Creare un'applicazione autonoma con Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Eseguire processi in modalità remota in un cluster Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)

### Estensioni

* [Usare i notebook di Zeppelin con un cluster Spark in HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Kernel disponibili per notebook di Jupyter nel cluster Spark per HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

### Gestire le risorse

* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-resource-manager.md)


[hdinsight-versions]: ../hdinsight-component-versioning/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/

<!---HONumber=AcomDC_0107_2016-->