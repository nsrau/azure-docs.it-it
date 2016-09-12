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
	ms.date="08/25/2016" 
	ms.author="nitinme"/>


# Kernel disponibili per i notebook di Jupyter con cluster Apache Spark in HDInsight Linux

Il cluster Apache Spark in HDInsight (Linux) include notebook di Jupyter che è possibile usare per testare le applicazioni. Un kernel è un programma che esegue e interpreta il codice. I cluster HDInsight Spark offrono due kernel che è possibile usare con il notebook di Jupyter. Si tratta di:

1. **PySpark** (per le applicazioni scritte in Python)
2. **Spark** (per le applicazioni scritte in Scala)

Questo articolo offre informazioni su come usare questi kernel e i relativi vantaggi.

**Prerequisiti:**

È necessario disporre di quanto segue:

- Una sottoscrizione di Azure. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un cluster Apache Spark in HDInsight Linux. Per istruzioni, vedere l'articolo relativo alla [creazione di cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## Come si usano i kernel? 

1. Dalla Schermata iniziale del [portale di Azure](https://portal.azure.com/) fare clic sul riquadro del cluster Spark (se è stato aggiunto sulla Schermata iniziale). È anche possibile passare al cluster in **Sfoglia tutto** > **Cluster HDInsight**.

2. Dal pannello del cluster Spark fare clic su **Collegamenti rapidi** e dal pannello **Dashboard cluster** fare clic su **Notebook di Jupyter**. Se richiesto, immettere le credenziali per il cluster.

	> [AZURE.NOTE] È anche possibile raggiungere il notebook di Jupyter per il cluster aprendo l'URL seguente nel browser. Sostituire __CLUSTERNAME__ con il nome del cluster:
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Creare un nuovo notebook con i nuovi kernel. Fare clic su **Nuovo** e quindi su **Pyspark** o **Spark**. È consigliabile usare il kernel Spark per applicazioni Scala e il kernel PySpark per applicazioni Python.

	![Creare un nuovo notebook Jupyter](./media/hdinsight-apache-spark-jupyter-notebook-kernels/jupyter-kernels.png "Creare un nuovo notebook Jupyter")

3. Verrà aperto un nuovo notebook con il kernel selezionato.

## Perché usare i kernel PySpark o Spark?

L'uso dei nuovi kernel comporta alcuni vantaggi.

1. **Contesti predefiniti**. Con i kernel **PySpark** o **Spark**, disponibili con i notebook Jupyter, non è necessario impostare i contesti Spark o Hive in modo esplicito prima di iniziare a usare l'applicazione in corso di sviluppo, perché sono disponibili per impostazione predefinita. Questi contesti sono:

	* **sc**: per il contesto Spark
	* **sqlContext**: per il contesto Hive


	Quindi non è necessario eseguire istruzioni simili alle seguenti per impostare i contesti:

		###################################################
		# YOU DO NOT NEED TO RUN THIS WITH THE NEW KERNELS
		###################################################
		sc = SparkContext('yarn-client')
		sqlContext = HiveContext(sc)

	È possibile invece usare direttamente i contesti preimpostati nell'applicazione.
	
2. **Celle magic**. Il kernel PySpark offre alcuni "magic" predefiniti. Si tratta di comandi speciali che è possibile chiamare con `%%`, ad esempio `%%MAGIC` <args>. Il comando magic deve essere la prima parola di una cella di codice e consente di generare più righe di contenuto. La parola magic deve essere la prima della cella. L'aggiunta di qualsiasi elemento prima di magic, anche un commento, determina un errore. Per altre informazioni sui magic, vedere [qui](http://ipython.readthedocs.org/en/stable/interactive/magics.html).

	La tabella seguente elenca i diversi magic disponibili tramite i kernel.

	| Magic | Esempio | Descrizione |
	|-----------|---------------------------------|--------------|
	| help | `%%help` | Genera una tabella di tutti i magic disponibili con esempi e descrizioni |
	| info | `%%info` | Visualizza informazioni sulla sessione per l'endpoint Livy corrente |
	| configure | `%%configure -f`<br>`{"executorMemory": "1000M"`,<br>`"executorCores": 4`} | Configura i parametri per la creazione di una sessione. Il flag di forzatura (-f) è obbligatorio se è già stata creata una sessione e tale sessione verrà eliminata e ricreata. Visitare la pagina relativa al [corpo della richiesta POST/sessions di Livy](https://github.com/cloudera/livy#request-body) per un elenco dei parametri validi. I parametri devono essere passati come una stringa JSON e devono essere nella riga successiva al magic, come illustrato nella colonna di esempio. |
	| sql | `%%sql -o <variable name>`<br> `SHOW TABLES` | Esegue una query Hive su sqlContext. Se viene passato il parametro `-o`, il risultato della query viene salvato in modo permanente nel contesto Python %%local come frame di dati [Pandas](http://pandas.pydata.org/). |
	| local | `%%local`<br>`a=1` | Tutto il codice presente nelle righe successive verrà eseguito localmente. Deve trattarsi di codice Python valido. |
	| logs | `%%logs` | Visualizza i log per la sessione Livy corrente. |
	| delete | `%%delete -f -s <session number>` | Elimina una sessione specifica dell'endpoint Livy corrente. Si noti che non è possibile eliminare la sessione avviata dal kernel stesso. |
	| cleanup | `%%cleanup -f` | Elimina tutte le sessioni per l'endpoint Livy corrente, inclusa quella del notebook. Il flag di forzatura -f è obbligatorio. |

	>[AZURE.NOTE] Oltre ai magic aggiunti dal kernel PySpark, è possibile anche usare il [magic IPython incorporato](https://ipython.org/ipython-doc/3/interactive/magics.html#cell-magics), tra cui `%%sh`. È possibile usare il magic `%%sh` per eseguire script e il blocco del codice nel nodo head del cluster.

3. **Visualizzazione automatica**. Il kernel **Pyspark** visualizza automaticamente l'output delle query Hive e SQL. È possibile scegliere tra diversi tipi di visualizzazione, inclusi Table, Pie, Line, Area, Bar.

## Parametri supportati con il magic %%sql

Il magic %%sql supporta parametri diversi che è possibile utilizzare per controllare il tipo di output ricevuto quando si eseguono query. La tabella seguente elenca l'output.

| Parametro | Esempio | Descrizione |
|-----------|---------------------------------|--------------|
| -o | `-o <VARIABLE NAME>`   | Usare questo parametro per salvare in modo permanente il risultato della query nel contesto Python %%local come frame di dati [Pandas](http://pandas.pydata.org/). Il nome della variabile del frame di dati è il nome della variabile specificato. |
| -q | `-q`   | Consente di disattivare le visualizzazioni per la cella. Se non si vuole visualizzare automaticamente il contenuto di una cella ma solo acquisirlo come un frame di dati, usare `-q -o <VARIABLE>`. Se si vuole disattivare le visualizzazioni senza acquisire i risultati, ad esempio per l'esecuzione di una query SQL con effetti collaterali, come un'istruzione `CREATE TABLE`, usare semplicemente `-q` senza specificare un argomento `-o`. |
| -m | `-m <METHOD>`   | Dove **METHOD** è **take** o **sample**. Per impostazione predefinita è **take**. Se il metodo è **take**, il kernel sceglie gli elementi dall'inizio del set di dati dei risultati specificato da MAXROWS, descritto più avanti in questa tabella. Se il metodo è **sample**, il kernel campiona in modo casuale gli elementi del set di dati in base al parametro `-r`, descritto di seguito in questa tabella. |
| -r | `-r <FRACTION>` | Qui **FRACTION** è un numero a virgola mobile compreso tra 0,0 e 1,0. Se il metodo sample per la query SQL è `sample`, allora il kernel campionerà automaticamente in modo casuale la frazione specificata degli elementi del set di risultati. Ad esempio, se si esegue una query SQL con gli argomenti `-m sample -r 0.01`, allora l'1% delle righe dei risultati verrà campionato in modo casuale. |
| -n | `-n <MAXROWS>`   | **MAXROWS** è un valore intero. Il kernel limita il numero di righe di output a **MAXROWS**. Se il valore **MAXROWS** è un numero negativo, ad esempio **-1**, il numero di righe nel set di risultati non sarà limitato. |

**Esempio:**

	%%sql -q -m sample -r 0.1 -n 500 -o query2 
	SELECT * FROM hivesampletable

L'istruzione precedente esegue le operazioni seguenti:

* Seleziona tutti i record da **hivesampletable**.
* Dal momento che viene usato -q, disattiva la visualizzazione automatica.
* Dal momento che si usa `-m sample -r 0.1 -n 500`, campiona in modo casuale il 10% delle righe di hivesampletable e limita la dimensione del set di risultati a 500 righe.
* Infine, poiché è stato usato `-o query2`, salva anche l'oputput in un frame di dati denominato **query2**.
	

## Considerazioni per l'uso dei nuovi kernel

Indipendentemente dal kernel usato (PySpark o Spark), se si lasciano i notebook in esecuzione verranno esaurite le risorse del cluster. Con questi kernel, dato che i contesti sono predefiniti, la semplice uscita dal notebook non termina il contesto e pertanto le risorse del cluster restano in uso. Una buona norma con i kernel PySpark e Spark consiste nell'usare l'opzione **Close and Halt** dal menu **File** del notebook. Questa operazione elimina il contesto e quindi esce dal notebook.


## Di seguito sono riportati alcuni esempi

Quando si apre un notebook di Jupyter verranno visualizzate due cartelle a livello di radice.

* La cartella **PySpark** contiene notebook di esempio che usano il nuovo kernel **Python**.
* La cartella **Scala** contiene notebook di esempio che usano il kernel predefinito **Spark**.

Per conoscere i diversi magic disponibili, è possibile aprire il notebook **00 - [READ ME FIRST] Spark Magic Kernel Features** dalla cartella **PySpark** o **Spark**. Per informazioni su come realizzare diversi scenari usando i notebook Jupyter con cluster HDInsight Spark, è anche possibile usare gli altri notebook di esempio disponibili nelle due cartelle.

## Dove sono archiviati i notebook

I notebook Jupyter vengono salvati nell'account di archiviazione associato al cluster nella cartella **/HdiNotebooks**. I notebook, i file di testo e le cartelle creati dall'interno di Jupyter saranno accessibili da WASB. Ad esempio, se si usa Jupyter per creare una cartella **myfolder** e un notebook **myfolder/mynotebook.ipynb**, sarà possibile accedere al notebook dal percorso `wasbs:///HdiNotebooks/myfolder/mynotebook.ipynb`. È anche vero il contrario, ovvero se si carica un notebook direttamente nell'account di archiviazione al percorso `/HdiNotebooks/mynotebook1.ipynb`, il notebook sarà visibile anche da Jupyter. I notebook vengono conservati nell'account di archiviazione anche dopo l'eliminazione del cluster.

La modalità di salvataggio dei notebook nell'account di archiviazione è compatibile con HDFS. Pertanto, se si usa SSH nel cluster, sarà possibile usare i comandi di gestione dei file nel modo indicato:

	hdfs dfs -ls /HdiNotebooks             				  # List everything at the root directory – everything in this directory is visible to Jupyter from the home page
	hdfs dfs –copyToLocal /HdiNotebooks    				# Download the contents of the HdiNotebooks folder
	hdfs dfs –copyFromLocal example.ipynb /HdiNotebooks   # Upload a notebook example.ipynb to the root folder so it’s visible from Jupyter


In caso di problemi di accesso all'account di archiviazione per il cluster, anche i notebook vengono salvati nel nodo head `/var/lib/jupyter`.

## Browser supportati
I notebook Jupyter in esecuzione su cluster HDInsight Spark sono supportati solo su Google Chrome.

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

* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely (Usare il plug-in Strumenti HDInsight per IntelliJ IDEA per eseguire il debug di applicazioni Spark in remoto)](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Usare i notebook di Zeppelin con un cluster Spark in HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Usare pacchetti esterni con i notebook Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Installare Jupyter Notebook nel computer e connetterlo a un cluster HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### Gestire risorse

* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Tenere traccia ed eseguire il debug di processi in esecuzione nel cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-job-debugging.md)

<!---HONumber=AcomDC_0831_2016-->