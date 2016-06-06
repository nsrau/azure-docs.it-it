<properties 
	pageTitle="Installare Jupyter Notebook nel computer e connetterlo a un cluster HDInsight Spark| Microsoft Azure" 
	description="Informazioni su come installare Jupyter Notebook in locale nel computer e su come connetterlo a un cluster Apache Spark in Azure HDInsight." 
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


# Installare Jupyter Notebook nel computer e connetterlo al cluster Apache Spark in Azure HDInsight (anteprima)

Questo articolo illustra come installare Jupyter Notebook, insieme ai kernel personalizzati PySpark (per Python) e Spark (per Scala) e al magic Spark, e come connettere l'applicazione a un cluster HDInsight.

Anche se i notebook Jupyter sono già disponibili nel cluster Spark in Azure HDInsight, l'installazione di Jupyter nel computer offre la possibilità di creare i notebook in locale, testare l'applicazione con un cluster in esecuzione e quindi caricare i notebook nel cluster. È possibile caricare i notebook nel cluster usando Jupyter Notebook già in esecuzione sul cluster oppure salvare i notebook nella cartella /HdiNotebooks nell'account di archiviazione associato al cluster. Per altre informazioni sul modo in cui i notebook vengono archiviati nel cluster, vedere l'argomento relativo ai [percorsi di archiviazione dei notebook Jupyter](hdinsight-apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored).


L'installazione di Jupyter e del magic Spark nel computer si articola in tre passaggi chiave.

* Installare Jupyter Notebook
* Installare i kernel PySpark e Spark con il magic Spark
* Configurare il magic Spark per l'accesso al cluster Spark in HDInsight

Per altre informazioni sul magic Spark e sui kernel personalizzati disponibili per i notebook Jupyter con il cluster HDInsight, vedere [Kernel disponibili per i notebook Jupyter con cluster HDInsight Spark Linux su HDInsight (anteprima)](hdinsight-apache-spark-jupyter-notebook-kernels.md).

**Prerequisiti:**

I prerequisiti elencati di seguito non riguardano l'installazione di Jupyter. Riguardano invece la connessione di Jupyter Notebook a un cluster HDInsight dopo l'installazione.

- Una sottoscrizione di Azure. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un cluster Apache Spark in HDInsight Linux. Per istruzioni, vedere [Creare cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## Installare Jupyter Notebook nel computer

Prima di installare i notebook Jupyter è necessario installare Python. Sia Python che Jupyter sono disponibili come parte della [distribuzione Anaconda](https://www.continuum.io/downloads). Quando si installa Anaconda, viene effettivamente installata una distribuzione di Python. Dopo aver installato Anaconda, aggiungere l'installazione di Jupyter mediante l'esecuzione di un comando. Questa sezione fornisce istruzioni che è necessario seguire.

1. Scaricare il [programma di installazione di Anaconda](https://www.continuum.io/downloads) per la piattaforma in uso ed eseguirlo. Quando si esegue l'installazione guidata, assicurarsi di selezionare l'opzione per l'aggiunta di Anaconda alla variabile PATH.

2. Eseguire il comando seguente per installare Jupyter.

		conda install jupyter

	Per altre informazioni sull'installazione di Jupyter, vedere l'argomento relativo all'[installazione di Jupyter mediante Anaconda](http://jupyter.readthedocs.io/en/latest/install.html).

## Installare i kernel e il magic Spark

Questa sezione illustra come installare il magic Spark e i kernel PySpark e Spark. Illustra quindi come configurare i kernel per la connessione a un cluster Apache Spark in esecuzione in Azure HDInsight.

1. Scaricare l'anteprima pubblica più recente del magic Spark da [GitHub](https://github.com/jupyter-incubator/sparkmagic/archive/publicpreview0.5.zip).

2. Decomprimere il file scaricato in un percorso sul disco. Nelle istruzioni fornite di seguito questo percorso viene denominato `$SPARKMAGIC_PATH`.

2. Eseguire il comando seguente

		pip install -r $SPARKMAGIC_PATH/requirements.txt  

3. Eseguire il comando seguente per installare il magic Spark.

		pip install -e $SPARKMAGIC_PATH

4. Installare i kernel PySpark e Spark. Eseguire i comandi seguenti.

		jupyter-kernelspec install $SPARKMAGIC_PATH/remotespark/kernels/sparkkernel
		jupyter-kernelspec install $SPARKMAGIC_PATH/remotespark/kernels/pysparkkernel

## Configurare il magic Spark per l'accesso al cluster HDInsight Spark

Questa sezione illustra come configurare il magic Spark installato in precedenza per la connessone a un cluster Apache Spark. È necessario che tale cluster sia già stato creato in Azure HDInsight.

1. Le informazioni di configurazione di Jupyter sono in genere archiviate nella home directory dell'utente. Per individuare la home directory su una qualsiasi piattaforma del sistema operativo, digitare i comandi seguenti.

	Avviare la shell di Python. In una finestra di comando digitare quanto segue:

		python

	Nella shell di Python immettere il comando seguente per individuare la home directory.

		import os
		print os.path.expanduser('~')

2. Passare alla home directory e, se non esiste già, creare una cartella denominata **.sparkmagic**.

3. All'interno della cartella creare un file denominato **config.json** e aggiungere a quest'ultimo il frammento di codice JSON seguente.

		{
		  "kernel_python_credentials" : {
		    "username": "{USERNAME}",
		    "base64_password": "{BASE64ENCODEDPASSWORD}",
		    "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
		  },
		  "kernel_scala_credentials" : {
		    "username": "{USERNAME}",
		    " base64_password ": "{BASE64ENCODEDPASSWORD}",
		    "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
		  }
		}

4. Sostituire **{USERNAME}**, **{CLUSTERDNSNAME}** e **{BASE64ENCODEDPASSWORD}** con i valori appropriati. È possibile usare diverse utilità del linguaggio di programmazione preferito o uno strumento online per convertire la password corrente in una password con codifica Base 64. Un semplice frammento di codice Python da eseguire dal prompt dei comandi può essere il seguente:

		python -c "import base64; print(base64.b64encode('{YOURPASSWORD}'))"

5. Avviare Jupyter. Usare il comando seguente dal prompt dei comandi.

		jupyter notebook

6. Verificare che sia possibile connettersi al cluster mediante Jupyter Notebook e usare il magic Spark disponibile con i kernel. Seguire questa procedura.

	1. Creare un nuovo notebook. Nell'angolo a destra fare clic su **New** (Nuovo). Verranno visualizzati il kernel predefinito **Python2** e i due nuovi kernel installati, **PySpark** e **Spark**.

		![Creare un nuovo notebook Jupyter](./media/hdinsight-apache-spark-jupyter-notebook-install-locally/jupyter-kernels.png "Creare un nuovo notebook Jupyter")

	
		Fare clic su **PySpark**.


	2. Eseguire il frammento di codice seguente.

			%%sql
			SELECT * FROM hivesampletable LIMIT 5

		Se è stato possibile recuperare l'output, viene verificata la connessione al cluster HDInsight.

	>[AZURE.TIP] Se si desidera aggiornare la configurazione del notebook per connettersi a un cluster differente, aggiornare il file config.json con un nuovo set di valori come illustrato nel Passaggio 3.




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