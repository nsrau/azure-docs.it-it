<properties
	pageTitle="Usare Azione di script per installare Spark in cluster Hadoop| Microsoft Azure"
	description="Informazioni su come personalizzare un cluster HDInsight con Spark tramite Azione di script."
	services="hdinsight"
	documentationCenter=""
	authors="nitinme"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/15/2015"
	ms.author="nitinme"/>

# Installare e usare Spark in cluster Hadoop di HDInsight tramite Azione di script

> [AZURE.IMPORTANT]Questo articolo è ora deprecato. HDInsight offre ora Spark come tipo di cluster di prima classe per cluster basati su Windows, quindi è possibile creare direttamente un cluster Spark senza modificare un cluster Hadoop tramite Azione di script. Usando il tipo di cluster Spark, si ottiene un cluster HDInsight versione 3.2 con Spark versione 1.3.1. Per installare versioni diverse di Spark, è possibile usare Azione di script. HDInsight offre uno script di Azione di script di esempio.

Informazioni su come installare Spark nei cluster HDInsight basati su Windows con Azione di script e su come eseguire query Spark nei cluster HDInsight.


**Articoli relativi a** - [Installare Spark nei cluster HDInsight basati su Linux](hdinsight-hadoop-spark-install-linux.md).

- [Creare cluster Hadoop in HDInsight](hdinsight-provision-clusters.md): informazioni generali sulla creazione di cluster HDInsight

- [Introduzione a Apache Spark in HDInsight](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md): creare un cluster di tipo Spark nel sistema operativo Windows.

- [Personalizzare cluster HDInsight mediante Script azione][hdinsight-cluster-customize]\: informazioni generali sulla personalizzazione di cluster HDInsight tramite Azione di Script.

- [Sviluppare script di Azione script per HDInsight](hdinsight-hadoop-script-actions.md)

## Che cos'è Spark?

<a href="http://spark.apache.org/docs/latest/index.html" target="_blank">Apache Spark</a> è un framework open source di elaborazione parallela che supporta l'elaborazione in memoria per migliorare le prestazioni di applicazioni analitiche di Big Data. Le funzionalità di elaborazione in memoria rendono Spark un valido strumento per l'esecuzione di algoritmi iterativi in calcoli grafici e di Machine Learning.

È possibile usare Spark anche per eseguire operazioni di elaborazione dati convenzionale basata su disco. Spark rappresenta un miglioramento rispetto al framework MapReduce tradizionale in quanto evita la scrittura su disco nelle fasi intermedie. Spark è anche compatibile con il file system distribuito Hadoop (HDFS) e l'archivio BLOB di Azure e quindi i dati esistenti possono essere elaborati facilmente tramite Spark.

Questo argomento fornisce istruzioni su come personalizzare un cluster HDInsight per l'installazione di Spark.

## Installare Spark tramite il portale di anteprima di Azure

Uno script di esempio per l'installazione di Spark in un cluster HDInsight è disponibile in un BLOB di archiviazione di sola lettura di Azure all'indirizzo [https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1](https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1). Questo script è in grado di installare Spark 1.2.0 o Spark 1.0.2, a seconda della versione del cluster HDInsight creata.

- Se si usa lo script durante la creazione di un cluster **HDInsight 3.2**, viene installato **Spark 1.2.0**.
- Se si usa lo script durante la creazione di un cluster **HDInsight 3.1**, viene installato **Spark 1.0.2**.

È possibile modificare questo script o crearne uno personalizzato per installare altre versioni di Spark.

> [AZURE.NOTE]Lo script di esempio funziona solo con cluster 3.1 e 3.2 HDInsight. Per altre informazioni sulle versioni dei cluster HDInsight, vedere [Versioni cluster HDInsight](hdinsight-component-versioning.md).

1. Avviare la creazione di un cluster tramite l'opzione **CREAZIONE PERSONALIZZATA**, come descritto in [Creare cluster Hadoop in HDInsight](hdinsight-provision-clusters.md#portal). Selezionare la versione del cluster a seconda di quanto segue:

	- Per installare **Spark 1.2.0**, creare un cluster HDInsight 3.2.
	- Per installare **Spark 1.0.2**, creare un cluster HDInsight 3.1.


2. Nella pagina **Azioni script** della procedura guidata fare clic su **aggiungi azione script** per specificare i dettagli relativi all'azione script, come descritto di seguito:

	![Usare Azione di script per personalizzare un cluster](./media/hdinsight-hadoop-spark-install/HDI.CustomProvision.Page6.png "Usare Azione di script per personalizzare un cluster")

	<table border='1'>
	<tr><th>Proprietà</th><th>Valore</th></tr>
	<tr><td>Nome</td>
		<td>Specificare un nome per l'azione di script. Ad esempio, <b>Install Spark</b>.</td></tr>
	<tr><td>URI script</td>
		<td>Specificare l'URI (Uniform Resource Identifier) dello script da richiamare per personalizzare il cluster. Ad esempio, <i>https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1</i></td></tr>
	<tr><td>Tipo di nodo</td>
		<td>Specificare i nodi in cui viene eseguito lo script di personalizzazione. È possibile scegliere <b>Tutti i nodi</b>, <b>Solo nodi head</b> o <b>Solo nodi di lavoro</b>.
	<tr><td>Parametri</td>
		<td>Specificare i parametri, se richiesti dallo script. Lo script per installare Spark non richiede alcun parametro, di conseguenza è possibile lasciare vuoto questo campo.</td></tr>
</table>È possibile aggiungere altre azioni di script per installare più componenti nel cluster. Dopo aver aggiunto gli script, fare clic sul segno di spunta per avviare la creazione del cluster.

È inoltre possibile usare lo script per installare Spark in HDInsight usando Azure PowerShell o HDInsight .NET SDK. Le istruzioni relative a queste procedure vengono fornite più avanti in questo argomento.

## Usare Spark in HDInsight
Spark fornisce API in Scala, Python e Java. Inoltre, è possibile usare la shell interattiva di Spark per eseguire query di Spark. Questa sezione fornisce le istruzioni per l'uso di Spark in base ai diversi approcci:

- [Usare la shell di Spark per eseguire query interattive](#sparkshell)
- [Usare la shell di Spark per eseguire query Spark SQL](#sparksql)
- [Usare un programma Scala autonomo](#standalone)

###<a name="sparkshell"></a>Usare la shell di Spark per eseguire query interattive
Per eseguire query di Spark da una shell interattiva di Spark, eseguire i passaggi seguenti. In questa sezione verrà eseguita una query Spark su un file di dati di esempio (/example/data/gutenberg/davinci.txt) disponibile in cluster HDInsight per impostazione predefinita.

1. Dal portale di Azure abilitare il desktop remoto per il cluster creato con Spark installato, quindi accedere in remoto al cluster. Per istruzioni, vedere <a href="http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">Connettersi a cluster HDInsight tramite RDP</a>.

2. Nella sessione RDP (Remote Desktop Protocol), dal desktop aprire la riga di comando di Hadoop (da un collegamento sul desktop) e passare al percorso in cui è installato Spark, ad esempio **C:\\apps\\dist\\spark-1.2.0**.


3. Eseguire il comando seguente per avviare la shell di Spark:

		 .\bin\spark-shell --master yarn

	Al termine dell'esecuzione del comando, viene visualizzato un prompt di Scala:

		 scala>

5. Nel prompt di Scala immettere la query di Spark riportata di seguito. Questa query consente di contare le occorrenze di ciascuna parola presente nel file davinci.txt disponibile nel percorso /example/data/gutenberg/ dell'archivio BLOB di Azure associato al cluster.

		val file = sc.textFile("/example/data/gutenberg/davinci.txt")
		val counts = file.flatMap(line => line.split(" ")).map(word => (word, 1)).reduceByKey(_ + _)
		counts.toArray().foreach(println)

6. L'output sarà simile al seguente:

	![Output dall'esecuzione della shell interattiva Scala in un cluster HDInsight](./media/hdinsight-hadoop-spark-install/hdi-scala-interactive.png)


7. Immettere :q per uscire dal prompt di Scala.

		:q

###<a name="sparksql"></a>Usare la shell di Spark per eseguire query Spark SQL

Spark SQL consente di usare Spark per eseguire query relazionali espresse in SQL (Structured Query Language), HiveQL o Scala. Questa sezione spiega come usare Spark per eseguire una query Hive su una tabella Hive di esempio. La tabella Hive usata in questa sezione (chiamata **hivesampletable**) è disponibile per impostazione predefinita quando si crea un cluster.

>[AZURE.NOTE]L'esempio seguente è stato creato in **Spark 1.2.0**, che viene installato se si esegue lo script di Azione di script durante la creazione del cluster HDInsight 3.2.

1. Dal portale di Azure abilitare il desktop remoto per il cluster creato con Spark installato, quindi accedere in remoto al cluster. Per istruzioni, vedere <a href="http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">Connettersi a cluster HDInsight tramite RDP</a>.

2. Nella sessione RDP, dal desktop aprire la riga di comando di Hadoop (da un collegamento sul desktop) e passare al percorso in cui è installato Spark, ad esempio **C:\\apps\\dist\\spark-1.2.0**.


3. Eseguire il comando seguente per avviare la shell di Spark:

		 .\bin\spark-shell --master yarn

	Al termine dell'esecuzione del comando, viene visualizzato un prompt di Scala:

		 scala>

4. Al prompt di Scala, impostare il contesto di Hive, necessario per lavorare con le query Hive usando Spark.

		val hiveContext = new org.apache.spark.sql.hive.HiveContext(sc)

	Si noti che **sc** è il contesto di Spark predefinito che viene impostato quando si avvia la shell di Spark.

5. Eseguire una query Hive usando il contesto di Hive e stampare l'output nella console. La query recupera i dati sui dispositivi di una marca specifica e limita il numero di record recuperati a 20.

		hiveContext.sql("""SELECT * FROM hivesampletable WHERE devicemake LIKE "HTC%" LIMIT 20""").collect().foreach(println)

6. Verrà visualizzato un output simile al seguente:

	![Output dall'esecuzione di Spark SQL in un cluster HDInsight](./media/hdinsight-hadoop-spark-install/hdi-spark-sql.png)

7. Immettere :q per uscire dal prompt di Scala.

		:q

### <a name="standalone"></a>Usare un programma Scala autonomo

In questa sezione verrà scritta un'applicazione Scala che conta il numero di righe contenenti le lettere "a" e "b" in un file di dati di esempio (/example/data/gutenberg/davinci.txt) disponibile in cluster HDInsight per impostazione predefinita. Per scrivere e usare un programma Scala autonomo in un cluster personalizzato mediante l'installazione di Spark, è necessario eseguire i passaggi seguenti:

- Scrivere un programma Scala
- Compilare il programma Scala per ottenere il file jar
- Eseguire il processo nel cluster

#### Scrivere un programma Scala
In questa sezione verrà scritto un programma Scala che conta il numero di righe contenenti le lettere "a" e "b" in un file di dati di esempio

1. Aprire un editor di testo e incollare il codice seguente:


		/* SimpleApp.scala */
		import org.apache.spark.SparkContext
		import org.apache.spark.SparkContext._
		import org.apache.spark.SparkConf

		object SimpleApp {
		  def main(args: Array[String]) {
		    val logFile = "/example/data/gutenberg/davinci.txt"			//Location of the sample data file on Azure Blob storage
		    val conf = new SparkConf().setAppName("SimpleApplication")
		    val sc = new SparkContext(conf)
		    val logData = sc.textFile(logFile, 2).cache()
		    val numAs = logData.filter(line => line.contains("a")).count()
		    val numBs = logData.filter(line => line.contains("b")).count()
		    println("Lines with a: %s, Lines with b: %s".format(numAs, numBs))
		  }
		}

2. Salvare il file con il nome **SimpleApp.scala**.

#### Compilare il programma Scala
In questa sezione si usa <a href="http://www.scala-sbt.org/0.13/docs/index.html" target="_blank">Simple Build Tool</a> (o SBT) per compilare il programma Scala. Poiché SBT richiede Java 1.6 o versione successiva, prima di continuare con la sezione accertarsi di avere installato la versione di Java corretta.

1. Installare SBT da http://www.scala-sbt.org/0.13/tutorial/Installing-sbt-on-Windows.html.
2. Creare una cartella denominata **SimpleScalaApp** e, all'interno di tale cartella, creare un file chiamato **simple.sbt**. Si tratta di un file di configurazione contenente informazioni sulla versione di Scala, le dipendenze della libreria e così via. Incollare quanto riportato di seguito nel file simple.sbt e salvare:


		name := "SimpleApp"

		version := "1.0"

		scalaVersion := "2.10.4"

		libraryDependencies += "org.apache.spark" %% "spark-core" % "1.2.0"



	>[AZURE.NOTE]Assicurarsi di mantenere le righe vuote del file.


3. Nella cartella **SimpleScalaApp** creare una struttura di directory **\\src\\main\\scala** e incollare il programma Scala (**SimpleApp.scala**) creato in precedenza nella cartella \\src\\main\\scala.
4. Aprire un prompt dei comandi, passare alla directory SimpleScalaApp e immettere il comando seguente:


		sbt package


	Una volta compilata l'applicazione, verrà visualizzato un file **simpleapp\_2.10-1.0.jar** creato nella directory **\\target\\scala-2.10** all'interno della cartella radice SimpleScalaApp.


#### Eseguire il processo nel cluster
In questa sezione si esegue l'accesso remoto al cluster in cui è installato Spark e si copia la cartella di destinazione del progetto SimpleScalaApp. Si usa quindi il comando **spark-submit** per inviare il processo al cluster.

1. Accedere in remoto al cluster in cui è installato Spark. Nel computer usato per la scrittura e la compilazione del programma SimpleApp.scala copiare la cartella **SimpleScalaApp\\target** e incollarla in un percorso nel cluster.
2. Nella sezione RDP, dal desktop, aprire la riga di comando di Hadoop e passare al percorso in cui è stata incollata la cartella **target**.
3. Immettere il comando seguente per eseguire il programma SimpleApp.scala:


		C:\apps\dist\spark-1.2.0\bin\spark-submit --class "SimpleApp" --master local target/scala-2.10/simpleapp_2.10-1.0.jar

4. Al termine dell'esecuzione del programma, l'output verrà visualizzato sulla console.


		Lines with a: 21374, Lines with b: 11430

## Installare Spark tramite Azure PowerShell

In questa sezione si usa il cmdlet **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** per richiamare gli script usando l'azione script per personalizzare un cluster. Prima di procedere, assicurarsi di aver installato e configurato Azure PowerShell. Per informazioni sulla configurazione di una workstation per l'esecuzione dei cmdlet Azure PowerShell per HDInsight, vedere [Installare e configurare Azure PowerShell][powershell-install-configure].

Eseguire la procedura seguente:

1. Aprire una finestra di Azure PowerShell e dichiarare le variabili seguenti:

		# Provide values for these variables
		$subscriptionName = "<SubscriptionName>"		# Name of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure Storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the Storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# Location of the HDInsight cluster. It must be in the same data center as the Storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# Number of nodes in the HDInsight cluster
		$version = "<HDInsightClusterVersion>"          # For example, "3.2"

2. Specificare i valori di configurazione, ad esempio i nodi del cluster e l'archivio predefinito da usare.

		# Specify the configuration options
		Select-AzureSubscription $subscriptionName
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
		$config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
		$config.DefaultStorageAccount.StorageContainerName=$containerName

3. Usare il cmdlet **Add-AzureHDInsightScriptAction** per aggiungere un'azione script alla configurazione del cluster. Successivamente, quando viene creato il cluster, viene eseguita l'azione di script.

		# Add a script action to the cluster configuration
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install Spark" -ClusterRoleCollection HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1

	Il cmdlet **Add-AzureHDInsightScriptAction** accetta i parametri seguenti:

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
<tr>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Parametro</th>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:550px; padding-left:5px; padding-right:5px;">Definizione</th></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Config</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Oggetto di configurazione a cui vengono aggiunte le informazioni dell'azione di script.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nome</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nome dell'azione di script.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">ClusterRoleCollection</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Specifica i nodi in cui viene eseguito lo script di personalizzazione. I valori validi sono HeadNode (per eseguire l'installazione nel nodo head) o DataNode (per eseguire l'installazione in tutti i nodi di dati). È possibile usare uno o entrambi i valori.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Uri</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Specifica l'URI per lo script eseguito.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Parametri</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Parametri richiesti dallo script. Poiché lo script di esempio usato in questo argomento non richiede alcun parametro, questo parametro non viene visualizzato nel frammento riportato sopra.
</td></tr>
</table>

4. Al termine, avviare la creazione di un cluster personalizzato con Spark installato.

		# Start creating a cluster with Spark installed
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version

Quando richiesto, immettere le credenziali per il cluster. La creazione del cluster può richiedere alcuni minuti.

## Installare Spark tramite PowerShell

Vedere [Personalizzare cluster HDInsight mediante Azione di script](hdinsight-hadoop-customize-cluster.md#call_scripts_using_powershell).

## Installare Spark tramite .NET SDK

Vedere [Personalizzare cluster HDInsight mediante Azione di script](hdinsight-hadoop-customize-cluster.md#call_scripts_using_azure_powershell).


## Vedere anche

- [Installare Spark nei cluster HDInsight basati su Linux](hdinsight-hadoop-spark-install-linux.md): installare Spark nei cluster HDInsight basati su Linux.
- [Creare cluster Hadoop in HDInsight](hdinsight-provision-clusters.md): creare cluster HDInsight.
- [Introduzione ad Apache Spark in HDInsight](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md): introduzione a Spark in HDInsight.
- [Personalizzare cluster HDInsight mediante Azione di script][hdinsight-cluster-customize]\: personalizzare cluster HDInsight mediante Azione di script.
- [Sviluppare script di Azione di script per HDInsight](hdinsight-hadoop-script-actions.md): sviluppare script di Azione di script.
- [Installare R in cluster HDInsight][hdinsight-install-r] per istruzioni su come usare la personalizzazione dei cluster per installare e usare R nei cluster Hadoop di HDInsight. R è un linguaggio open source e un ambiente per l'elaborazione statistica. Fornisce centinaia di funzioni statistiche predefinite e un proprio linguaggio che combina aspetti di programmazione funzionale con aspetti di programmazione orientata agli oggetti. Offre inoltre funzionalità complete di grafica.
- [Installare Giraph in cluster HDInsight](hdinsight-hadoop-giraph-install.md). Usare la personalizzazione cluster per installare Giraph in cluster Hadoop di HDInsight. Giraph permette di elaborare grafici con Hadoop e può essere usato con Azure HDInsight.
- [Installare Solr in cluster HDInsight](hdinsight-hadoop-solr-install.md). Usare la personalizzazione cluster per installare Solr in cluster Hadoop di HDInsight. Solr consente di eseguire operazioni di ricerca avanzate sui dati archiviati.

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
[powershell-install-configure]: ../install-configure-powershell.md

<!---HONumber=Oct15_HO4-->