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
	ms.date="02/17/2016" 
	ms.author="nitinme"/>


# Kernel disponibili per i notebook di Jupyter con cluster Spark in HDInsight (Linux)

Il cluster Apache Spark in HDInsight (Linux) include notebook di Jupyter che è possibile usare per testare le applicazioni. Per impostazione predefinita il notebook di Jupyter comprende un kernel **Python2**. Un kernel è un programma che esegue e interpreta il codice. I cluster HDInsight Spark offrono due kernel aggiuntivi che è possibile usare con il notebook di Jupyter. Si tratta di:

1. **PySpark** (per le applicazioni scritte in Python)
2. **Spark** (per le applicazioni scritte in Scala)

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

L'uso dei nuovi kernel comporta alcuni vantaggi.

1. **Contesti predefiniti**. Con il kernel predefinito **Python2**, disponibile con i notebook Jupyter, è necessario impostare esplicitamente i contesti Spark, SQL o Hive prima di iniziare a usare l'applicazione in fase di sviluppo. Se si usano i nuovi kernel, **PySpark** o **Spark**, questi contesti sono disponibili per impostazione predefinita. Questi contesti sono:

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
	
2. **Celle magic**. Il kernel PySpark offre alcuni "magic" predefiniti. Si tratta di comandi speciali che è possibile chiamare con `%%`, ad esempio `%%MAGIC` <args>. Il comando magic deve essere la prima parola di una cella di codice e consente di generare più righe di contenuto. La parola magic deve essere la prima della cella. L'aggiunta di qualsiasi elemento prima di magic, anche un commento, determina un errore. Per altre informazioni sui magic, vedere [qui](http://ipython.readthedocs.org/en/stable/interactive/magics.html).

	La tabella seguente elenca i diversi magic disponibili tramite i kernel.

	| Magic | Esempio | Descrizione |
	|-----------|---------------------------------|--------------|
	| help | `%%help` | Genera una tabella di tutti i magic disponibili con esempi e descrizioni |
	| info | `%%info` | Visualizza informazioni sulla sessione per l'endpoint Livy corrente |
	| configure | `%%configure -f {"executorMemory": "1000M", "executorCores": 4`} | Configura i parametri per la creazione di una sessione. Il flag di forzatura (-f) è obbligatorio se è già stata creata una sessione e tale sessione verrà eliminata e ricreata. Visitare la pagina relativa al [corpo della richiesta POST/sessions di Livy](https://github.com/cloudera/livy#request-body) per un elenco dei parametri validi. I parametri devono essere passati come stringa JSON. |
	| sql | `%%sql -o <variable name>`<br> `SHOW TABLES` | Esegue una query SQL su sqlContext. Se il parametro `-o` viene passato, il risultato della query viene mantenuto nel contesto Python %%local come frame di dati [Pandas](http://pandas.pydata.org/). |
	| hive | `%%hive -o <variable name>`<br> `SHOW TABLES` | Esegue una query Hive su hivelContext. Se il parametro viene passato, il risultato della query viene mantenuto nel contesto Python %%local come frame di dati [Pandas](http://pandas.pydata.org/). |
	| local | `%%local`<br>`a=1` | Tutto il codice presente nelle righe successive verrà eseguito localmente. Deve trattarsi di codice Python valido. |
	| logs | `%%logs` | Visualizza i log per la sessione Livy corrente. |
	| delete | `%%delete -f -s <session number>` | Elimina una sessione specifica dell'endpoint Livy corrente. Si noti che non è possibile eliminare la sessione avviata dal kernel stesso. |
	| cleanup | `%%cleanup -f` | Elimina tutte le sessioni per l'endpoint Livy corrente, inclusa quella del notebook. Il flag di forzatura -f è obbligatorio. |

3. **Visualizzazione automatica**. Il kernel **Pyspark** visualizza automaticamente l'output delle query Hive e SQL. È possibile scegliere tra diversi tipi di visualizzazione, inclusi Table, Pie, Line, Area, Bar.


## Considerazioni per l'uso dei nuovi kernel

Indipendentemente dal kernel usato (Python2, PySpark o Spark), se si lasciano i notebook in esecuzione verranno esaurite le risorse del cluster. Con il notebook di Python2, poiché si creano i contesti in modo esplicito, è anche possibile eliminare questi contesti quando si esce dall'applicazione.

Tuttavia, con i kernel PySpark e Spark i contesti sono preimpostati e quindi non è possibile eliminare anche il contesto in modo esplicito. Quindi, se si esce semplicemente dal notebook il contesto potrebbe essere ancora in esecuzione e usare le risorse del cluster. Una buona norma con i kernel PySpark e Spark consiste nell'usare l'opzione **Close and Halt** dal menu **File** del notebook. Questa operazione elimina il contesto e quindi esce dal notebook.


## Di seguito sono riportati alcuni esempi

Quando si apre un notebook di Jupyter verranno visualizzate due cartelle a livello di radice.

* La cartella **PySpark** contiene notebook di esempio che usano il nuovo kernel **Python**.
* La cartella **Scala** contiene notebook di esempio che usano il kernel predefinito **Spark**.

Per conoscere i diversi magic disponibili, è possibile aprire il notebook **00 - [READ ME FIRST] Spark Magic Kernel Features** dalla cartella **PySpark** o **Spark**. Per informazioni su come realizzare diversi scenari usando i notebook Jupyter con cluster HDInsight Spark, è anche possibile usare gli altri notebook di esempio disponibili nelle due cartelle.

## Commenti e suggerimenti

I nuovi kernel sono ancora in una fase iniziale e si evolveranno nel tempo. Questo potrebbe comportare un cambiamento delle API con l'evoluzione dei kernel. Sono graditi commenti e suggerimenti in merito all'uso di questi nuovi kernel. Saranno molto utili per la progettazione della versione finale di questi kernel. È possibile inserire commenti o suggerimenti nella sezione **Commenti** in fondo a questo articolo.


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

<!---HONumber=AcomDC_0224_2016-->