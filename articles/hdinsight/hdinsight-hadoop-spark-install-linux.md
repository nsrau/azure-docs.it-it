<properties 
	pageTitle="Usare un'azione di script per installare Apache Spark in cluster HDInsight basati su Linux (Hadoop) | Microsoft Azure" 
	description="Informazioni su come installare Spark in un cluster HDInsight basato su Linux usando azioni di script. Le azioni di script consentono di personalizzare il cluster durante la creazione, modificando la configurazione del cluster o installando servizi e utilità." 
	services="hdinsight" 
	documentationCenter="" 
	authors="Blackmist" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/19/2015" 
	ms.author="larryfr"/>

# Installare e usare Spark in cluster Hadoop di HDInsight

In questo documento si apprenderà come installare Spark usando l'azione di script. Azione script consente di eseguire script per personalizzare un cluster solo durante la creazione. Per altre informazioni, vedere [Personalizzare cluster HDInsight mediante le azioni script][hdinsight-cluster-customize]. Dopo aver installato Spark, si apprenderà inoltre come eseguire una query di Spark nei cluster HDInsight.

> [AZURE.NOTE]Poiché HDInsight fornisce anche Spark come tipo di cluster, è possibile effettuare direttamente il provisioning di un cluster Spark senza modificare un cluster Hadoop. Attualmente questa funzione è tuttavia limitata ai cluster di Windows. Usando il tipo di cluster Spark, si ottiene un cluster HDInsight versione 3.2 basato su Windows con Spark versione 1.3.1. Per altre informazioni, vedere l'articolo di [introduzione a Spark Apache in HDInsight](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md).

## <a name="whatis"></a>Che cos'è Spark?

<a href="http://spark.apache.org/docs/latest/index.html" target="_blank">Apache Spark</a> è un framework open source di elaborazione parallela che supporta l'elaborazione in memoria per migliorare le prestazioni di applicazioni analitiche di Big Data. Le funzionalità di elaborazione in memoria rendono Spark un valido strumento per l'esecuzione di algoritmi iterativi in calcoli grafici e di Machine Learning.

È possibile usare Spark anche per eseguire operazioni di elaborazione dati convenzionale basata su disco. Spark rappresenta un miglioramento rispetto al framework MapReduce tradizionale in quanto evita la scrittura su disco nelle fasi intermedie. Spark è anche compatibile con il file system distribuito Hadoop (HDFS) e l'archivio BLOB di Azure e quindi i dati esistenti possono essere elaborati facilmente tramite Spark.

Questo argomento fornisce istruzioni su come personalizzare un cluster HDInsight per l'installazione di Spark.

## <a name="whatis"></a>Quale versione di Spark è possibile installare?

In questo argomento, viene usato uno script personalizzato di azione script per installare Spark in un cluster HDInsight. Questo script consente di installare Spark 1.5.1.

È possibile modificare questo script o crearne uno personalizzato per installare altre versioni di Spark.

## Funzionalità dello script

Questo script consente di installare Spark 1.5.1 in `/usr/hdp/current/spark`.

> [AZURE.WARNING]È possibile che alcuni file binari di Spark 1.3.1 siano stati installati per impostazione predefinita nel cluster HDInsight. Non devono essere usati e verranno rimossi dall'immagine del cluster HDInsight in un aggiornamento futuro.

## <a name="install"></a>Installare Spark mediante azioni di script

Uno script di esempio per l'installazione di Spark in un cluster HDInsight è disponibile in un BLOB di archiviazione di sola lettura di Azure all'indirizzo [https://hdiconfigactions.blob.core.windows.net/linuxsparkconfigactionv02/spark-installer-v02.sh](https://hdiconfigactions.blob.core.windows.net/linuxsparkconfigactionv02/spark-installer-v02.sh). Questa sezione fornisce istruzioni su come usare lo script di esempio quando si crea il cluster usando il portale di Azure.

> [AZURE.NOTE]È anche possibile usare Azure PowerShell o HDInsight .NET SDK per creare un cluster con questo script. Per altre informazioni sull'uso di questi metodi, vedere [Personalizzare cluster HDInsight mediante Azione di script](hdinsight-hadoop-customize-cluster-linux.md).

1. Avviare la creazione di un cluster seguendo la procedura descritta in [Creazione di cluster HDInsight basati su Linux](hdinsight-provision-linux-clusters.md#portal), ma non completare la creazione.

2. Nel pannello **Configurazione facoltativa** selezionare **Azioni di script** e specificare le informazioni seguenti:

	* __NOME__: immettere un nome descrittivo per l'azione di script.
	* __URI SCRIPT__: https://hdiconfigactions.blob.core.windows.net/linuxsparkconfigactionv02/spark-installer-v02.sh
	* __HEAD__: selezionare questa opzione
	* __LAVORO__: deselezionare questa opzione
	* __ZOOKEEPER__: deselezionare questa opzione
	* __PARAMETRI__: lasciare questo campo vuoto
    
    > [AZURE.NOTE]Lo script Spark di esempio installa componenti solo sui nodi head, in modo che sia possibile deselezionare gli altri tipi di nodo.

3. Nella parte inferiore di **Azioni script** usare il pulsante **Seleziona** per salvare la configurazione. Usare infine il pulsante **Seleziona** nella parte inferiore del pannello **Configurazione facoltativa** per salvare le informazioni relative alla configurazione facoltativa.

4. Continuare il provisioning del cluster come descritto nella pagina [Creazione di cluster HDInsight basati su Linux](hdinsight-provision-linux-clusters.md#portal).

## <a name="usespark"></a>Come si usa Spark in HDInsight?

Spark fornisce API in Scala, Python e Java. Inoltre, è possibile usare la shell interattiva di Spark per eseguire query di Spark. Al termine della creazione del cluster, usare il comando seguente per connettersi al cluster HDInsight:

	ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
	
Per altre informazioni sull'uso di SSH con HDInsight, vedere gli articoli seguenti:

* [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

* [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

Dopo aver effettuato la connessione, usare le sezioni seguenti per la procedura specifica sull'uso di Spark:

- [Uso della shell di Spark per eseguire query interattive](#sparkshell)
- [Uso della shell di Spark per eseguire query Spark SQL](#sparksql) 
- [Uso di un programma Scala autonomo](#standalone)

###<a name="sparkshell"></a>Uso della shell di Spark per eseguire query interattive

1. Eseguire il comando seguente per avviare la shell di Spark:

		 /usr/hdp/current/spark/bin/spark-shell --master yarn

	Al termine dell'esecuzione del comando, viene visualizzato un prompt di Scala:

		 scala>

5. Nel prompt di Scala immettere la query di Spark riportata di seguito. Questa query consente di contare le occorrenze di ciascuna parola presente nel file davinci.txt disponibile nel percorso /example/data/gutenberg/ dell'archivio BLOB di Azure associato al cluster.

		val file = sc.textFile("/example/data/gutenberg/davinci.txt")
		val counts = file.flatMap(line => line.split(" ")).map(word => (word, 1)).reduceByKey(_ + _)
		counts.toArray().foreach(println)

6. L'output sarà simile al seguente:

		(felt,,1)
		(axle-tree,1)
		(deals,9)
		(virtuous,4)

7. Immettere :q per uscire dal prompt di Scala.

		:q

###<a name="sparksql"></a>Uso della shell di Spark per eseguire query Spark SQL

Spark SQL consente di usare Spark per eseguire query relazionali espresse in SQL (Structured Query Language), HiveQL o Scala. Questa sezione spiega come usare Spark per eseguire una query Hive su una tabella Hive di esempio. La tabella Hive usata in questa sezione (denominata **hivesampletable**) è disponibile per impostazione predefinita quando si crea un cluster.

1. Eseguire il comando seguente per avviare la shell di Spark:

		 /usr/hdp/current/spark/bin/spark-shell --master yarn

	Al termine dell'esecuzione del comando, viene visualizzato un prompt di Scala:

		 scala>

2. Al prompt di Scala, impostare il contesto di Hive, necessario per lavorare con le query Hive usando Spark.

		val hiveContext = new org.apache.spark.sql.hive.HiveContext(sc)

	> [AZURE.NOTE]`sc`: in questa istruzione, costituisce il contesto di Spark predefinito che viene impostato quando si avvia la shell di Spark.

5. Eseguire una query Hive usando il contesto di Hive e stampare l'output nella console. La query recupera i dati sui dispositivi di una marca specifica e limita il numero di record recuperati a 20.

		hiveContext.sql("""SELECT * FROM hivesampletable WHERE devicemake LIKE "HTC%" LIMIT 20""").collect().foreach(println)

6. Verrà visualizzato un output simile al seguente:

		[820,11:35:17,it-IT,Android,HTC,Inspire 4G,Louisiana,UnitedStates, 2.7383836,0,1]
		[1055,17:24:08,it-IT,Android,HTC,Incredible,Ohio,United States,18.0894738,0,0]
		[1067,03:42:29,it-IT,Windows Phone,HTC,HD7,District Of Columbia,United States,null,0,0]

7. Immettere :q per uscire dal prompt di Scala.

		:q

### <a name="standalone"></a>Uso di un programma Scala autonomo

In questa sezione verrà creata un'applicazione Scala che conta il numero di righe contenenti le lettere "a" e "b" in un file di dati di esempio (/example/data/gutenberg/davinci.txt)

1. Usare i comandi seguenti per installare lo strumento di compilazione Scala:

		echo "deb http://dl.bintray.com/sbt/debian /" | sudo tee -a /etc/apt/sources.list.d/sbt.list
		sudo apt-get update
		sudo apt-get install sbt
		
	Quando richiesto, selezionare __Y__ per continuare.

2. Creare la struttura di directory per il progetto Scala:

		mkdir -p SimpleScalaApp/src/main/scala
		
3. Creare un nuovo file denominato __simple.sbt__, contenente le informazioni di configurazione necessarie per questo progetto.

		cd SimpleScalaApp
		nano simple.sbt
		
	Usare quanto segue come contenuto del file:

		name := "SimpleApp"
	
		version := "1.0"
	
		scalaVersion := "2.10.4"
	
		libraryDependencies += "org.apache.spark" %% "spark-core" % "1.2.0"


	> [AZURE.NOTE]Assicurarsi di mantenere le righe vuote tra una voce e l'altra.
	
	Usare __Ctrl+X__, quindi __Y__ e __Invio__ per salvare il file.

4. Usare il comando seguente per creare un nuovo file denominato __SimpleApp.scala__ nella directory __SimpleScalaApp/src/main/scala__:

		nano src/main/scala/SimpleApp.scala

	Usare quanto segue come contenuto del file:

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

	Usare __Ctrl+X__, quindi __Y__ e __Invio__ per salvare il file.

5. Dalla directory __SimpleScalaApp__ usare il comando seguente per compilare l'applicazione e archiviarla in un file con estensione jar:

		sbt package

	Dopo aver compilato l'applicazione, verrà visualizzato un file **simpleapp\_2.10-1.0.jar** creato nella directory \_\_SimpleScalaApp/target/scala-2.10**.

6. Usare il comando seguente per eseguire il programma SimpleApp.scala:


		/usr/hdp/current/spark/bin/spark-submit --class "SimpleApp" --master yarn target/scala-2.10/simpleapp_2.10-1.0.jar

4. Al termine dell'esecuzione del programma, l'output verrà visualizzato sulla console.

		Lines with a: 21374, Lines with b: 11430

## Passaggi successivi

- [Installare e usare Hue nei cluster HDInsight](hdinsight-hadoop-hue-linux.md). Hue è un'interfaccia utente che semplifica la creazione, l'esecuzione e il salvataggio di processi Pig e Hive, nonché l'esplorazione dell'archivio predefinito per il cluster HDInsight.

- [Installare R in cluster HDInsight][hdinsight-install-r] per istruzioni su come usare la personalizzazione dei cluster per installare e usare R nei cluster Hadoop di HDInsight. R è un linguaggio open source e un ambiente per l'elaborazione statistica. Fornisce centinaia di funzioni statistiche predefinite e un proprio linguaggio che combina aspetti di programmazione funzionale con aspetti di programmazione orientata agli oggetti. Offre inoltre funzionalità complete di grafica.

- [Installare Giraph in cluster HDInsight](hdinsight-hadoop-giraph-install-linux.md). Usare la personalizzazione cluster per installare Giraph in cluster Hadoop di HDInsight. Giraph permette di elaborare grafici con Hadoop e può essere usato con Azure HDInsight.

- [Installare Solr in cluster HDInsight](hdinsight-hadoop-solr-install-linux.md). Usare la personalizzazione cluster per installare Solr in cluster Hadoop di HDInsight. Solr consente di eseguire operazioni di ricerca avanzate sui dati archiviati.

- [Installare Hue in cluster HDInsight](hdinsight-hadoop-hue-linux.md). Usare la personalizzazione dei cluster per installare Hue nei cluster Hadoop di HDInsight. Hue è un insieme di applicazioni Web che consente di interagire con un cluster Hadoop.



[hdinsight-provision]: hdinsight-provision-clusters-linux.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
[powershell-install-configure]: ../install-configure-powershell.md
 

<!---HONumber=Oct15_HO4-->