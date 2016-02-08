<properties 
	pageTitle="Kernel disponibili con i notebook di Jupyter nei cluster HDInsight Spark in Linux | Microsoft Azure" 
	description="Informazioni sui kernel dei notebook di Jupyter aggiuntivi disponibili con cluster Spark in HDInsight Linux." 
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
	ms.date="12/08/2015" 
	ms.author="nitinme"/>


# Kernel disponibili per i notebook di Jupyter con cluster Spark in HDInsight (Linux)

Il cluster Apache Spark in HDInsight (Linux) include notebook di Jupyter che è possibile usare per testare le applicazioni. Per impostazione predefinita il notebook di Jupyter comprende un kernel **Python2**. I cluster HDInsight Spark offrono due kernel aggiuntivi che è possibile usare con il notebook di Jupyter. Si tratta di:

1. **Spark** (per le applicazioni scritte in Scala)
2. **PySpark** (per le applicazioni scritte in Python)

Questo articolo offre informazioni su come usare questi kernel e i relativi vantaggi.

**Prerequisiti:**

È necessario disporre di quanto segue:

- Una sottoscrizione di Azure. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un cluster Apache Spark in HDInsight Linux. Per istruzioni, vedere l'articolo relativo alla [creazione di cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## Come si usano i kernel? 

1. Dalla Schermata iniziale del [portale di anteprima di Azure](https://portal.azure.com/) fare clic sul riquadro del cluster Spark (se è stato aggiunto sulla Schermata iniziale). È anche possibile passare al cluster in **Sfoglia tutto** > **Cluster HDInsight**.   

2. Dal pannello del cluster Spark fare clic su **Collegamenti rapidi** e dal pannello **Dashboard cluster** fare clic su **Notebook di Jupyter**. Se richiesto, immettere le credenziali per il cluster.

	> [AZURE.NOTE] È anche possibile raggiungere il notebook di Jupyter per il cluster aprendo l'URL seguente nel browser. Sostituire __CLUSTERNAME__ con il nome del cluster:
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Creare un nuovo notebook con i nuovi kernel. Fare clic su **Nuovo** e quindi su **Pyspark** o **Spark**. È consigliabile usare il kernel Spark per applicazioni Scala e il kernel PySpark per applicazioni Python.

	![Creare un nuovo notebook Jupyter](./media/hdinsight-apache-spark-jupyter-notebook-kernels/jupyter-kernels.png "Creare un nuovo notebook Jupyter")

3. Verrà aperto un nuovo notebook con il kernel selezionato.

## Perché usare i nuovi kernel?

L'uso dei nuovi kernel comporta due vantaggi.

1. Con il kernel predefinito **Python2** è necessario impostare i contesti Spark, SQL o Hive prima di iniziare a usare l'applicazione in fase di sviluppo. Se si usano i nuovi kernel (**Spark** o **PySpark**), questi contesti sono disponibili per impostazione predefinita. Questi contesti sono:

	* **sc**: per il contesto Spark
	* **sqlContext**: per il contesto SQL
	* **hiveContext**: per il contesto Hive


	Quindi non è necessario eseguire istruzioni simili alle seguenti per impostare i contesti:

		###################################################
		# YOU DO NOT NEED TO RUN THIS WITH THE NEW KERNELS
		###################################################
		sc = SparkContext('yarn-client')
		sqlContext = SQLContext(sc)
		hiveContext = HiveContext(sc)

	È possibile invece usare direttamente i contesti preimpostati nell'applicazione.
	
2. È possibile usare direttamente **%sql** e **%hive** in modo da usare query SQL o Hive rispettivamente. Quindi, una soluzione simile funzionerebbe immediatamente senza istruzioni di codice iniziali.

		%hive
		SELECT * FROM hivesampletable LIMIT 10

## Considerazioni per l'uso dei nuovi kernel

Indipendentemente dal kernel usato (Python2, PySpark o Spark), se si lasciano i notebook in esecuzione verranno esaurite le risorse del cluster. Con il notebook di Python2, poiché si creano i contesti in modo esplicito, è anche possibile eliminare questi contesti quando si esce dall'applicazione.

Tuttavia, con i kernel PySpark e Spark i contesti sono preimpostati e quindi non è possibile eliminare anche il contesto in modo esplicito. Quindi, se si esce semplicemente dal notebook il contesto potrebbe essere ancora in esecuzione e usare le risorse del cluster. Una buona norma con i kernel PySpark e Spark consiste nell'usare l'opzione **Close and Halt** dal menu **File** del notebook. Questa operazione elimina il contesto e quindi esce dal notebook.


## Di seguito sono riportati alcuni esempi

Quando si apre un notebook di Jupyter verranno visualizzate due cartelle a livello di radice.

* La cartella **Python** contiene notebook di esempio che usano il kernel predefinito **Python2**.
* La cartella **Scala** contiene notebook di esempio che usano il kernel predefinito **Spark**.

È possibile aprire lo stesso notebook (ad esempio **Nota preliminare: nozioni di base di Spark in HDInsight**) da due cartelle per vedere come il notebook di Python2 inizia sempre con l'impostazione dei contesti necessari, mentre il notebook di Spark usa solo i contesti preimpostati.

## Commenti e suggerimenti

I nuovi kernel sono ancora in fase iniziale e si evolveranno nel corso del tempo. Questo potrebbe comportare un cambiamento delle API con l'evoluzione dei kernel. Sono graditi commenti e suggerimenti in merito all'uso di questi nuovi kernel. Saranno molto utili per la progettazione della versione finale di questi kernel. È possibile inserire commenti o suggerimenti nella sezione **Commenti** in fondo a questo articolo.


## <a name="seealso"></a>Vedere anche


* [Panoramica: Apache Spark su Azure HDInsight](hdinsight-apache-spark-overview.md)

### Scenari

* [Spark con Business Intelligence: eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](hdinsight-apache-spark-use-bi-tools.md)

* [Spark con Machine Learning: utilizzare Spark in HDInsight per l'analisi della temperatura di compilazione utilizzando dati HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark con Machine Learning: usare Spark in HDInsight per prevedere i risultati del controllo degli alimenti](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Streaming Spark: usare Spark in HDInsight per la creazione di applicazioni di streaming in tempo reale](hdinsight-apache-spark-eventhub-streaming.md)

* [Analisi dei log del sito Web mediante Spark in HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### Creare ed eseguire applicazioni

* [Creare un'applicazione autonoma con Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Eseguire processi in modalità remota in un cluster Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)

### Estensioni

* [Usare i notebook di Zeppelin con un cluster Spark in HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

### Gestire risorse

* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

<!---HONumber=AcomDC_0128_2016-->