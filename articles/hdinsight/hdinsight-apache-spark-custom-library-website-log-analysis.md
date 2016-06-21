<properties 
	pageTitle="Usare librerie personalizzate con un cluster HDInsight Spark per analizzare i log del sito Web | Microsoft Azure" 
	description="Usare librerie personalizzate con un cluster HDInsight Spark per analizzare i log del sito Web" 
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
	ms.date="06/06/2016" 
	ms.author="nitinme"/>

# Analizzare i log del sito Web utilizzando una libreria con cluster Apache Spark su HDInsight Linux

Questo notebook dimostra come analizzare i dati dei log mediante una libreria personalizzata con Spark in HDInsight. La libreria personalizzata usata è una libreria di Python chiamata **iislogparser.py**.

> [AZURE.TIP] Questa esercitazione è disponibile anche come notebook di Jupyter in un cluster Spark (Linux) creato in HDInsight. L'esperienza offerta dal notebook consente di eseguire i frammenti di codice Python dal notebook stesso. Per eseguire l'esercitazione da un notebook, creare un cluster Spark, avviare un notebook di Jupyter (`https://CLUSTERNAME.azurehdinsight.net/jupyter`) e quindi eseguire il notebook **Analizzare i log con Spark usando una libreria personalizzata .ipynb** nella cartella **PySpark**.

**Prerequisiti:**

È necessario disporre di quanto segue:

- Una sottoscrizione di Azure. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un cluster Apache Spark in HDInsight Linux. Per istruzioni, vedere l'articolo relativo alla [creazione di cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## Salvare i dati non elaborati come RDD

In questa sezione viene usato il notebook di [Jupyter](https://jupyter.org) associato a un cluster Apache Spark in HDInsight per eseguire i processi che elaborano i dati di esempio non elaborati e li salvano come tabella Hive. I dati di esempio sono un file con estensione csv (hvac.csv) disponibile in tutti i cluster per impostazione predefinita.

Dopo avere salvato i dati come tabella Hive, nella sezione successiva verrà effettuata la connessione alla tabella Hive mediante strumenti di Business Intelligence come Power BI e Tableau.

1. Dal [portale di Azure](https://portal.azure.com/), dalla schermata iniziale, fare clic sul riquadro per il cluster Spark (se lo si è bloccato alla schermata iniziale). È anche possibile passare al cluster in **Sfoglia tutto** > **Cluster HDInsight**.   

2. Dal pannello del cluster Spark fare clic su **Collegamenti rapidi** e dal pannello **Dashboard cluster** fare clic su **Notebook di Jupyter**. Se richiesto, immettere le credenziali per il cluster.

	> [AZURE.NOTE] È anche possibile raggiungere il notebook di Jupyter per il cluster aprendo l'URL seguente nel browser. Sostituire __CLUSTERNAME__ con il nome del cluster:
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Creare un nuovo notebook. Fare clic su **Nuovo** e quindi su **PySpark**.

	![Creare un nuovo notebook Jupyter](./media/hdinsight-apache-spark-custom-library-website-log-analysis/hdispark.note.jupyter.createnotebook.png "Creare un nuovo notebook Jupyter")

3. Un nuovo notebook verrà creato e aperto con il nome Untitled.pynb. Fare clic sul nome del notebook nella parte superiore e immettere un nome descrittivo.

	![Specificare un nome per il notebook](./media/hdinsight-apache-spark-custom-library-website-log-analysis/hdispark.note.jupyter.notebook.name.png "Specificare un nome per il notebook")

4. Poiché il notebook è stato creato tramite il kernel PySpark, non è necessario creare contesti in modo esplicito. I contesti Spark e Hive vengono creati automaticamente quando si esegue la prima cella di codice. È possibile iniziare con l'importazione dei tipi necessari per questo scenario. Incollare il frammento di codice seguente in una cella vuota e quindi premere **MAIUSC+INVIO**.


		from pyspark.sql import Row
		from pyspark.sql.types import *


5. Creare un RDD con i dati di log di esempio già disponibili nel cluster. È possibile accedere ai dati nell'account di archiviazione predefinito associato al cluster al percorso **\\HdiSamples\\HdiSamples\\WebsiteLogSampleData\\SampleLog\\909f2b.log**.


		logs = sc.textFile('wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log')


6. Recuperare un set di log di esempio per verificare che il passaggio precedente sia stato completato correttamente.

		logs.take(5)

	L'output dovrebbe essere simile al seguente:

		# -----------------
		# THIS IS AN OUTPUT
		# -----------------

		[u'#Software: Microsoft Internet Information Services 8.0',
		 u'#Fields: date time s-sitename cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Cookie) cs(Referer) cs-host sc-status sc-substatus sc-win32-status sc-bytes cs-bytes time-taken',
		 u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46',
		 u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32',
		 u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step4.png X-ARR-LOG-ID=4bea5b3d-8ac9-46c9-9b8c-ec3e9500cbea 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 72177 871 47']

## Analizzare i dati di log usando una libreria personalizzata di Python

7. Nell'output precedente, le prime due righe includono le informazioni di intestazione e ogni riga rimanente corrisponde allo schema descritto nell'intestazione. L'analisi di tali log potrebbe essere complessa. Quindi, viene usata una libreria personalizzata di Python (**iislogparser.py**) che semplifica notevolmente l'analisi di tali log. Per impostazione predefinita, questa libreria è inclusa con il cluster Spark in HDInsight in **/HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py**.

	Tuttavia, questa libreria non è in `PYTHONPATH` e quindi non può essere usata tramite un'istruzione di importazione come `import iislogparser`. Per usare questa libreria è necessario distribuirla a tutti i nodi di lavoro. Eseguire il frammento di codice seguente.


		sc.addPyFile('wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py')


9. `iislogparser` offre una funzione `parse_log_line` che restituisce `None` se una riga del log è una riga di intestazione e restituisce un'istanza della classe `LogLine` se viene rilevata una riga del log. Usare la classe `LogLine` per estrarre solo le righe del log dall'RDD:

		def parse_line(l):
		    import iislogparser
		    return iislogparser.parse_log_line(l)
		logLines = logs.map(parse_line).filter(lambda p: p is not None).cache()


10. Recuperare un paio di righe estratte del log per verificare che il passaggio sia stato completato correttamente.

		logLines.take(2)

	L'output dovrebbe essere simile al seguente:

		# -----------------
		# THIS IS AN OUTPUT
		# -----------------

		[2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46,
 		2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32]


11. La classe `LogLine`, a sua volta, offre alcuni metodi utili, ad esempio `is_error()`, che determina se una voce di log contiene un codice di errore. Usarla per calcolare il numero di errori nelle righe di log estratte e quindi registrare tutti gli errori in un file diverso.

		errors = logLines.filter(lambda p: p.is_error())
		numLines = logLines.count()
		numErrors = errors.count()
		print 'There are', numErrors, 'errors and', numLines, 'log entries'
		errors.map(lambda p: str(p)).saveAsTextFile('wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b-2.log')

	Verrà visualizzato un output simile al seguente:

		# -----------------
		# THIS IS AN OUTPUT
		# -----------------

		There are 30 errors and 646 log entries

12. È anche possibile usare **Matplotlib** per costruire una visualizzazione dei dati. Ad esempio, se si vuole isolare la causa delle richieste in esecuzione da molto tempo si potrebbero trovare i file che mediamente richiedono più tempo per servire una richiesta. Il frammento di codice seguente recupera le 25 risorse principali che hanno impiegato più tempo per servire una richiesta.

		def avgTimeTakenByKey(rdd):
		    return rdd.combineByKey(lambda line: (line.time_taken, 1),
		                            lambda x, line: (x[0] + line.time_taken, x[1] + 1),
		                            lambda x, y: (x[0] + y[0], x[1] + y[1]))\
		              .map(lambda x: (x[0], float(x[1][0]) / float(x[1][1])))
		    
		avgTimeTakenByKey(logLines.map(lambda p: (p.cs_uri_stem, p))).top(25, lambda x: x[1])

	Verrà visualizzato un output simile al seguente:

		# -----------------
		# THIS IS AN OUTPUT
		# -----------------

		[(u'/blogposts/mvc4/step13.png', 197.5),
		 (u'/blogposts/mvc2/step10.jpg', 179.5),
		 (u'/blogposts/extractusercontrol/step5.png', 170.0),
		 (u'/blogposts/mvc4/step8.png', 159.0),
		 (u'/blogposts/mvcrouting/step22.jpg', 155.0),
		 (u'/blogposts/mvcrouting/step3.jpg', 152.0),
		 (u'/blogposts/linqsproc1/step16.jpg', 138.75),
		 (u'/blogposts/linqsproc1/step26.jpg', 137.33333333333334),
		 (u'/blogposts/vs2008javascript/step10.jpg', 127.0),
		 (u'/blogposts/nested/step2.jpg', 126.0),
		 (u'/blogposts/adminpack/step1.png', 124.0),
		 (u'/BlogPosts/datalistpaging/step2.png', 118.0),
		 (u'/blogposts/mvc4/step35.png', 117.0),
		 (u'/blogposts/mvcrouting/step2.jpg', 116.5),
		 (u'/blogposts/aboutme/basketball.jpg', 109.0),
		 (u'/blogposts/anonymoustypes/step11.jpg', 109.0),
		 (u'/blogposts/mvc4/step12.png', 106.0),
		 (u'/blogposts/linq8/step0.jpg', 105.5),
		 (u'/blogposts/mvc2/step18.jpg', 104.0),
		 (u'/blogposts/mvc2/step11.jpg', 104.0),
		 (u'/blogposts/mvcrouting/step1.jpg', 104.0),
		 (u'/blogposts/extractusercontrol/step1.png', 103.0),
		 (u'/blogposts/sqlvideos/sqlvideos.jpg', 102.0),
		 (u'/blogposts/mvcrouting/step21.jpg', 101.0),
		 (u'/blogposts/mvc4/step1.png', 98.0)]


13. È anche possibile presentare queste informazioni sotto forma di tracciato. Come primo passaggio per creare un tracciato, creeremo prima di tutto una tabella temporanea, **AverageTime**. La tabella raggruppa i registri in base all'ora per vedere se si sono verificati eventuali picchi di latenza insoliti in un determinato momento.

		avgTimeTakenByMinute = avgTimeTakenByKey(logLines.map(lambda p: (p.datetime.minute, p))).sortByKey()
		schema = StructType([StructField('Minutes', IntegerType(), True),
		                     StructField('Time', FloatType(), True)])
		                     
		avgTimeTakenByMinuteDF = sqlContext.createDataFrame(avgTimeTakenByMinute, schema)
		avgTimeTakenByMinuteDF.registerTempTable('AverageTime')

14. Quindi, per ottenere tutti i record della tabella **AverageTime**, è possibile eseguire la query SQL seguente.

		%%sql -o averagetime
		SELECT * FROM AverageTime

	Il magic `%%sql` seguito da `-o averagetime` assicura che l'output della query venga mantenuto in locale nel server di Jupyter, di solito il nodo head del cluster. L'output viene mantenuto come frame di dati [Pandas](http://pandas.pydata.org/) con il nome specificato **averagetime**.

	Verrà visualizzato un output simile al seguente:

	![Output della query SQL](./media/hdinsight-apache-spark-custom-library-website-log-analysis/sql.output.png "Output della query SQL")

	Per altre informazioni sul magic `%%sql` e sugli altri magic disponibili con il kernel PySpark, vedere [Kernel disponibili per i notebook di Jupyter con cluster Spark in HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels).

15. È ora possibile creare un tracciato tramite Matplotlib, una libreria che consente di creare visualizzazioni di dati. Poiché il tracciato deve essere tracciato dal frame di dati **averagetime** mantenuto in locale, il frammento di codice deve iniziare con il magic `%%local`. Ciò garantisce che il codice venga eseguito localmente nel server di Jupyter.

		%%local
		%matplotlib inline
		import matplotlib.pyplot as plt
		
		plt.plot(averagetime['Minutes'], averagetime['Time'], marker='o', linestyle='--')
		plt.xlabel('Time (min)')
		plt.ylabel('Average time taken for request (ms)')

	Verrà visualizzato un output simile al seguente:

	![Output Matplotlib](./media/hdinsight-apache-spark-custom-library-website-log-analysis/hdi-apache-spark-web-log-analysis-plot.png "Output Matplotlib")

16. Dopo aver eseguito l'applicazione, è consigliabile arrestare il notebook per rilasciare le risorse. A tale scopo, dal menu **File** del notebook fare clic su **Close and Halt**. Questa operazione consente di arrestare e chiudere il notebook.
	

## <a name="seealso"></a>Vedere anche


* [Panoramica: Apache Spark su Azure HDInsight](hdinsight-apache-spark-overview.md)

### Scenari

* [Spark con Business Intelligence: eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](hdinsight-apache-spark-use-bi-tools.md)

* [Spark con Machine Learning: utilizzare Spark in HDInsight per l'analisi della temperatura di compilazione utilizzando dati HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark con Machine Learning: usare Spark in HDInsight per prevedere i risultati del controllo degli alimenti](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Streaming Spark: usare Spark in HDInsight per creare applicazioni di streaming in tempo reale](hdinsight-apache-spark-eventhub-streaming.md)

### Creare ed eseguire applicazioni

* [Creare un'applicazione autonoma con Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Eseguire processi in modalità remota in un cluster Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)

### Strumenti ed estensioni

* [Usare il plug-in degli strumenti HDInsight per IntelliJ IDEA per creare e inviare applicazioni Spark in Scala](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Utilizzare il plug-in Strumenti HDInsight per IntelliJ IDEA per eseguire il debug di applicazioni Spark in remoto](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Usare i notebook di Zeppelin con un cluster Spark in HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Kernel disponibili per notebook di Jupyter nel cluster Spark per HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Usare pacchetti esterni con i notebook Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Installare Jupyter nel computer e connetterlo a un cluster HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### Gestire risorse

* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Tenere traccia ed eseguire il debug di processi in esecuzione nel cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-job-debugging.md)

<!---HONumber=AcomDC_0608_2016-->