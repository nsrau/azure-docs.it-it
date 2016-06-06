<properties 
	pageTitle="Usare pacchetti esterni con notebook Jupyter in cluster Apache Spark in HDInsight | Azure"
	description="Istruzioni dettagliate su come configurare notebook Jupyter disponibili con cluster HDInsight Spark per l'uso di pacchetti Spark esterni." 
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
	ms.date="05/25/2016" 
	ms.author="nitinme"/>


# Usare pacchetti esterni con i notebook Jupyter in cluster Apache Spark in Azure HDInsight (anteprima)

Vengono fornite informazioni su come configurare Jupyter Notebook in un cluster Apache Spark in HDInsight (Linux) per l'uso di pacchetti esterni creati dalla community e non inclusi e subito utilizzabili nel cluster.

Per un elenco completo dei pacchetti disponibili, è possibile eseguire ricerche nel [repository Maven](http://search.maven.org/). È anche possibile ottenere un elenco dei pacchetti disponibili da altre origini. Ad esempio, un elenco completo dei pacchetti creati dalla community è disponibile nel sito Web [spark-packages.org](http://spark-packages.org/).

In questo articolo si apprenderà a usare il pacchetto [spark-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) con Jupyter Notebook.

**Prerequisiti:**

È necessario disporre di quanto segue:

- Una sottoscrizione di Azure. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un cluster Apache Spark in HDInsight Linux. Per istruzioni, vedere [Creare cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## Usare pacchetti esterni con i notebook Jupyter 

1. Dalla Schermata iniziale del [portale di Azure](https://portal.azure.com/) fare clic sul riquadro del cluster Spark (se è stato aggiunto sulla Schermata iniziale). È anche possibile passare al cluster in **Sfoglia tutto** > **Cluster HDInsight**.   

2. Dal pannello del cluster Spark fare clic su **Collegamenti rapidi** e dal pannello **Dashboard cluster** fare clic su **Notebook di Jupyter**. Se richiesto, immettere le credenziali per il cluster.

	> [AZURE.NOTE] È anche possibile raggiungere il notebook di Jupyter per il cluster aprendo l'URL seguente nel browser. Sostituire __CLUSTERNAME__ con il nome del cluster:
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Creare un nuovo notebook. Fare clic su **New** (Nuovo) e quindi su **Spark**.

	![Creare un nuovo notebook Jupyter](./media/hdinsight-apache-spark-jupyter-notebook-use-external-packages/hdispark.note.jupyter.createnotebook.png "Creare un nuovo notebook Jupyter")

3. Un nuovo notebook verrà creato e aperto con il nome Untitled.pynb. Fare clic sul nome del notebook nella parte superiore e immettere un nome descrittivo.

	![Specificare un nome per il notebook](./media/hdinsight-apache-spark-jupyter-notebook-use-external-packages/hdispark.note.jupyter.notebook.name.png "Specificare un nome per il notebook")

4. Il magic `%%configure` verrà usato per configurare il notebook per l'uso di un pacchetto esterno. Nei notebook che usano pacchetti esterni assicurarsi di richiamare il magic `%%configure` nella prima cella del codice. Questo accorgimento garantisce che il kernel sia configurato per l'uso del pacchetto prima dell'avvio della sessione.

		%%configure
		{ "packages":["com.databricks:spark-csv_2.10:1.4.0"] }


	>[AZURE.IMPORTANT] Se si dimentica di configurare il kernel nella prima cella, è possibile usare il magic `%%configure` con il parametro `-f`. In questo modo, tuttavia, la sessione verrà riavviata e il lavoro fatto andrà perso.

5. Nel frammento di codice sopra riportato `packages` attende un elenco di coordinate Maven nel repository centrale Maven. In questo frammento di codice `com.databricks:spark-csv_2.10:1.4.0` è la coordinata Maven per il pacchetto **spark-csv**. Di seguito viene spiegato come creare le coordinate per un pacchetto.

	a. Individuare un pacchetto nel repository Maven. In questa esercitazione si userà [spark-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
	
	b. Recuperare dal repository i valori per **GroupId** (ID gruppo), **ArtifactId** (ID elemento) e **Version** (Versione).

	![Usare pacchetti esterni con Jupyter Notebook](./media/hdinsight-apache-spark-jupyter-notebook-use-external-packages/use-external-packages-with-jupyter.png "Usare pacchetti esterni con Jupyter Notebook")

	c. Concatenare i tre valori, separati da virgola (**:**).

		com.databricks:spark-csv_2.10:1.4.0

6. Eseguire la cella di codice con il magic `%%configure`. Questa operazione configura la sessione Livy sottostante per l'uso del pacchetto fornito. Nelle celle successive del notebook è ora possibile usare il pacchetto, come mostrato di seguito.

		val df = sqlContext.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

7. È quindi possibile eseguire i frammenti di codice, come mostrato di seguito, per visualizzare i dati del frame di dati creato nel passaggio precedente.

		df.show()

		df.select("Time").count()


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

### Strumenti ed estensioni

* [Usare il plug-in degli strumenti HDInsight per IntelliJ IDEA per creare e inviare applicazioni Spark in Scala](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Usare i notebook di Zeppelin con un cluster Spark in HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

### Gestire risorse

* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

<!---HONumber=AcomDC_0525_2016-->