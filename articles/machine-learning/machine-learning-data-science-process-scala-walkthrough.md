<properties
	pageTitle="Analisi scientifica dei dati mediante Scala con Spark in Azure | Microsoft Azure"
	description="Come usare Scala per attività di Machine Learning con supervisione con la libreria di Machine Learning (MLlib) scalabile per Spark e pacchetti SparkML in un cluster Spark di Azure HDInsight."  
	services="machine-learning"
	documentationCenter=""
	authors="bradsev,deguhath"
	manager="paulettm"
	editor="cgronlun" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/01/2016"
	ms.author="bradsev;"/>


# Analisi scientifica dei dati mediante Scala con Spark in Azure

Questo argomento illustra come usare Scala per attività di Machine Learning con supervisione con la libreria di Machine Learning (MLlib) scalabile per Spark e pacchetti SparkML in un cluster Spark di Azure HDInsight. Vengono illustrate le attività che costituiscono il [processo di analisi scientifica dei dati](http://aka.ms/datascienceprocess), ovvero l'inserimento e l'esplorazione dei dati, la visualizzazione, la progettazione, la modellazione e l'utilizzo dei modelli. I modelli creati includono la regressione logistica e lineare, foreste casuali e alberi con boosting a gradienti. Vengono trattate due attività comuni di Machine Learning con supervisione:

- Problema di regressione: stima dell'importo della mancia ($)
- Classificazione binaria: stima di mancia o non mancia (1/0) per una corsa in taxi

Il processo di modellazione richiede il training e la valutazione su un set di dati di test con le metriche di precisione pertinenti. Viene illustrato anche come archiviare questi modelli nell'archivio BLOB di Azure (WASB) e come classificare e valutare le relative prestazioni predittive. Un argomento più avanzato illustra come ottimizzare i modelli con la convalida incrociata e lo sweep di iperparametri. Come dati di esempio sono stati presi i dati relativi alle corse e alle tariffe dei taxi di New York nel 2013.

[Scala](http://www.scala-lang.org/) è un linguaggio basato su Java Virtual Machine che integra i concetti di linguaggio orientato a oggetti e funzionale. È un linguaggio scalabile particolarmente adatto per l'elaborazione distribuita nel cloud e viene eseguito in cluster Spark di Azure.

[Spark](http://spark.apache.org/) è un framework open source di elaborazione parallela che supporta l'elaborazione in memoria per migliorare le prestazioni delle applicazioni analitiche di Big Data. Il motore di elaborazione Spark è costruito per la velocità, la semplicità d'uso e le analisi sofisticate. Le funzionalità di elaborazione distribuita in memoria rendono Spark uno strumento valido per l'esecuzione di algoritmi iterativi in calcoli grafici e di Machine Learning. Il pacchetto [spark.ml](http://spark.apache.org/docs/latest/ml-guide.html) offre un set uniforme di API di alto livello basate su frame di dati che consentono di creare e ottimizzare pipeline pratiche di Machine Learning. [MLlib](http://spark.apache.org/mllib/) è la libreria scalabile per il Machine Learning di Spark che introduce funzionalità di modellazione nell'ambiente distribuito.

[HDInsight Spark](../hdinsight/hdinsight-apache-spark-overview.md) è la soluzione di Spark open source ospitata in Azure. Include anche il supporto per istanze di **Jupyter Scala Notebook** nel cluster Spark che possono eseguire query Spark SQL interattive per trasformare, filtrare e visualizzare dati archiviati nei BLOB di Azure (WASB). I frammenti di codice Scala che consentono di creare le soluzioni e offrono i tracciati pertinenti per la visualizzazione dei dati sono eseguiti qui in istanze di Jupyter Notebook installate in cluster Spark. La procedura di modellazione riportata in questi argomenti include anche codice che illustra come eseguire il training, valutare, salvare e usare ogni tipo di modello.

La procedura di installazione e il codice fornito in questa procedura dettagliata è per HDInsight 3.4 Spark 1.6. Tuttavia, il codice in questo esempio e in [Jupyter Scala Notebook](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration%20Modeling%20and%20Scoring%20using%20Scala.ipynb) per questa procedura dettagliata è generico e funzionerà in qualsiasi cluster Spark. Se non si usa HDInsight Spark, i passaggi di configurazione e gestione del cluster possono essere leggermente diversi rispetto a quanto illustrato qui.

> [AZURE.NOTE] Per un argomento che illustra come usare Python anziché Scala per completare le attività per un processo di analisi scientifica dei dati end-to-end, vedere [Panoramica dell'analisi scientifica dei dati con Spark in Azure HDInsight](machine-learning-data-science-spark-overview.md).


## Prerequisiti

1\.Prima di iniziare a esaminare questi argomenti, è necessario avere una sottoscrizione di Azure. Se non è già disponibile, vedere [Get Azure free trial](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/) (Ottenere una versione di valutazione gratuita di Azure).

2\. Per completare questa procedura dettagliata è necessario un cluster HDInsight 3.4 Spark 1.6. Per crearne uno, vedere le istruzioni disponibili in [Introduzione: creare cluster Apache Spark in HDInsight Linux ed eseguire query interattive usando Spark SQL](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md). Il tipo e la versione del cluster vengono specificati tramite il menu **Selezionare il tipo di cluster**.

![](./media/machine-learning-data-science-process-scala-walkthrough/spark-cluster-on-portal.png)


>[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


Per una descrizione dei dati relativi alle corse dei taxi della città di New York e istruzioni su come eseguire codice da un'istanza di Jupyter Notebook nel cluster Spark, vedere le sezioni pertinenti di [Panoramica dell'analisi scientifica dei dati con Spark in Azure HDInsight](machine-learning-data-science-spark-overview.md).


## Eseguire il codice Scala da un'istanza di Jupyter Notebook nel cluster Spark 

È possibile avviare il notebook di Jupyter dal portale di Azure. Trovare il cluster Spark nel dashboard e fare clic su di esso per inserire la pagina di gestione del cluster. Fare quindi clic su **Dashboard cluster** -> **Notebook di Jupyter** per aprire il notebook associato al cluster Spark.

![](./media/machine-learning-data-science-process-scala-walkthrough/spark-jupyter-on-portal.png)

Per accedere alle istanze di Jupyter Notebook è anche possibile passare a ***https://CLUSTERNAME.azurehdinsight.net/jupyter***. Sostituire la parte CLUSTERNAME dell'URL con il nome del proprio cluster. Sarà necessaria la password dell'account amministratore accedere al notebook.

![](./media/machine-learning-data-science-process-scala-walkthrough/spark-jupyter-notebook.png)

Selezionare Scala per visualizzare una directory con alcuni esempi di notebook preconfezionati che usano l'API PySpark. Il notebook **Exploration Modeling and Scoring using Scala.ipynb** contenente i codici di esempio per questa serie di argomenti su Spark è disponibile in [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Scala)


È possibile caricare il notebook direttamente da GitHub nel server Jupyter Notebook del cluster Spark. Nella home page di Jupyter fare clic sul pulsante **Upload** (Carica) a destra nella schermata. Si aprirà una finestra di esplorazione file, in cui è possibile incollare l'URL di GitHub (contenuto non elaborato) del notebook di Scala e fare clic su **Open** (Apri). Il notebook di Scala è disponibile all'URL seguente:

[Exploration-Modeling-and-Scoring-using-Scala.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb)


## Installazione: contesti preimpostati, magic e librerie di Spark

### Contesto preimpostato di Spark e Hive

	# SET START TIME
	import java.util.Calendar
	val beginningTime = Calendar.getInstance().getTime()


I kernel Spark forniti con Jupyter Notebook hanno contesti preimpostati e non è quindi necessario impostare i contesti Spark o Hive in modo esplicito prima di iniziare a usare l'applicazione in fase di sviluppo perché sono disponibili per impostazione predefinita. Questi contesti sono:

- sc - per SparkContext
- sqlContext - per HiveContext


### Magic di Spark

Il kernel Spark offre alcuni "magic" predefiniti, ovvero comandi speciali che è possibile chiamare con %%. Negli esempi di codice seguenti sono usati due comandi di questo tipo.

- **%%local**: specifica che il codice presente nelle righe successive verrà eseguito localmente. Deve trattarsi di codice Scala valido.
- **%%sql -o <nome variabile>**: esegue una query Hive su sqlContext. Se viene passato il parametro -o, il risultato della query viene salvato in modo permanente nel contesto Scala %%local come frame di dati Spark.

Per altre informazioni sui kernel per le istanze di Jupyter Notebook e i "magic" predefiniti chiamati con %% (ad esempio %%local) messi a disposizione, vedere [Kernel disponibili per Jupyter Notebook con cluster Apache Spark in HDInsight Linux](../hdinsight/hdinsight-apache-spark-jupyter-notebook-kernels.md).


### Importare le librerie

Importare la libreria Spark, MLlib e altre librerie necessarie con il codice seguente.

	# IMPORT SPARK AND JAVA LIBRARIES 
	import org.apache.spark.sql.SQLContext
	import org.apache.spark.sql.functions._
	import java.text.SimpleDateFormat
	import java.util.Calendar
	import sqlContext.implicits._
	import org.apache.spark.sql.Row
	
	# SPARK SQL FUNCTIONS
	import org.apache.spark.sql.types.{StructType, StructField, StringType, IntegerType, FloatType, DoubleType}
	import org.apache.spark.sql.functions.rand
	
	# SPARK ML FUNCTIONS
	import org.apache.spark.ml.Pipeline
	import org.apache.spark.ml.feature.{StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer, Binarizer}
	import org.apache.spark.ml.tuning.{ParamGridBuilder, TrainValidationSplit, CrossValidator}
	import org.apache.spark.ml.regression.{LinearRegression, LinearRegressionModel, RandomForestRegressor, RandomForestRegressionModel, GBTRegressor, GBTRegressionModel}
	import org.apache.spark.ml.classification.{LogisticRegression, LogisticRegressionModel, RandomForestClassifier, RandomForestClassificationModel, GBTClassifier, GBTClassificationModel}
	import org.apache.spark.ml.evaluation.{BinaryClassificationEvaluator, RegressionEvaluator, MulticlassClassificationEvaluator}
	
	# SPARK MLLIB FUNCTIONS
	import org.apache.spark.mllib.linalg.{Vector, Vectors}
	import org.apache.spark.mllib.util.MLUtils
	import org.apache.spark.mllib.classification.{LogisticRegressionWithLBFGS, LogisticRegressionModel}
	import org.apache.spark.mllib.regression.{LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel}
	import org.apache.spark.mllib.tree.{GradientBoostedTrees, RandomForest}
	import org.apache.spark.mllib.tree.configuration.BoostingStrategy
	import org.apache.spark.mllib.tree.model.{GradientBoostedTreesModel, RandomForestModel, Predict}
	import org.apache.spark.mllib.evaluation.{BinaryClassificationMetrics, MulticlassMetrics, RegressionMetrics}
	
	# SPECIFY SQL CONTEXT
	val sqlContext = new SQLContext(sc)


## Inserimento di dati
 
Il primo passaggio del processo di analisi scientifica dei dati consiste nel prelevare i dati da analizzare dalle origini esterne o dai sistemi in cui risiedono e inserirli nell'ambiente di modellazione ed esplorazione dei dati. In questa procedura dettagliata viene letto un campione unito in join pari allo 0,1% del file TSV relativo alle corse e alle tariffe dei taxi. L'ambiente di modellazione ed esplorazione dei dati è Spark. Questa sezione contiene il codice per completare la serie di attività seguenti:

- impostare i percorsi di directory per l'archiviazione di dati e modelli
- Lettura del set di dati di input archiviato come file TSV.
- definire uno schema per i dati e pulire i dati
- creare un frame di dati puliti, memorizzarli nella cache
- registrare come tabella temporanea in un contesto SQL
- eseguire query sulla tabella e importare i risultati in un frame di dati.


### Impostare percorsi di directory per i percorsi di archiviazione in WASB

Spark può eseguire operazioni di lettura e scrittura in BLOB di Archiviazione di Azure (WASB). I dati esistenti archiviati in WASB possono essere elaborati con Spark e i relativi risultati possono essere memorizzati nuovamente in BLOB di Archiviazione di Azure.

Per salvare file o modelli in WASB, è necessario specificare correttamente il percorso. È possibile fare riferimento al contenitore predefinito collegato al cluster Spark usando un percorso che inizia con: "wasb///". "wasb://" fa riferimento ad altri percorsi.

L'esempio di codice seguente specifica il percorso dei dati di input da leggere e il percorso dell'archivio BLOB di Azure collegato al cluster Spark in cui verrà salvato il modello.

	# SET PATHS TO DATA AND MODEL FILE LOCATIONS: 
	# INGEST DATA DATA AND SPECIFY HEADERS FOR COLUMNS
	val taxi_train_file = sc.textFile("wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv")
	val header = taxi_train_file.first;
	
	# SET THE MODEL STORAGE DIRECTORY PATH 
	# NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
	val modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";


### Importare i dati, creare RDD e definire un frame di dati in base allo schema 

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# DEFINE SCHEMA BASED ON HEADER OF THE FILE
	val sqlContext = new SQLContext(sc)
	val taxi_schema = StructType(
	    Array(
	        StructField("medallion", StringType, true), 
	        StructField("hack_license", StringType, true),
	        StructField("vendor_id", StringType, true), 
	        StructField("rate_code", DoubleType, true),
	        StructField("store_and_fwd_flag", StringType, true),
	        StructField("pickup_datetime", StringType, true),
	        StructField("dropoff_datetime", StringType, true),
	        StructField("pickup_hour", DoubleType, true),
	        StructField("pickup_week", DoubleType, true),
	        StructField("weekday", DoubleType, true),
	        StructField("passenger_count", DoubleType, true),
	        StructField("trip_time_in_secs", DoubleType, true),
	        StructField("trip_distance", DoubleType, true),
	        StructField("pickup_longitude", DoubleType, true),
	        StructField("pickup_latitude", DoubleType, true),
	        StructField("dropoff_longitude", DoubleType, true),
	        StructField("dropoff_latitude", DoubleType, true),
	        StructField("direct_distance", StringType, true),
	        StructField("payment_type", StringType, true),
	        StructField("fare_amount", DoubleType, true),
	        StructField("surcharge", DoubleType, true),
	        StructField("mta_tax", DoubleType, true),
	        StructField("tip_amount", DoubleType, true),
	        StructField("tolls_amount", DoubleType, true),
	        StructField("total_amount", DoubleType, true),
	        StructField("tipped", DoubleType, true),
	        StructField("tip_class", DoubleType, true)
	        )
	    )
	
	# CAST VARIABLES ACCORDING TO SCHEMA
	val taxi_temp = (taxi_train_file.map(_.split("\t"))
	                        .filter((r) => r(0) != "medallion")
	                        .map(p => Row(p(0), p(1), p(2),
	                            p(3).toDouble, p(4), p(5), p(6), p(7).toDouble, p(8).toDouble, p(9).toDouble, p(10).toDouble,
	                            p(11).toDouble, p(12).toDouble, p(13).toDouble, p(14).toDouble, p(15).toDouble, p(16).toDouble,
	                            p(17), p(18), p(19).toDouble, p(20).toDouble, p(21).toDouble, p(22).toDouble,
	                            p(23).toDouble, p(24).toDouble, p(25).toDouble, p(26).toDouble)))
	
	
	# CREATE INITIAL DATA-FRAME, DROP COLUMNS, AND CREATE CLEANED DATA-FRAME BY FILTERING FOR UNDESIRED VALUES OR OUTLIERS
	val taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)
	
	val taxi_df_train_cleaned = (taxi_train_df.drop(taxi_train_df.col("medallion"))
	        .drop(taxi_train_df.col("hack_license")).drop(taxi_train_df.col("store_and_fwd_flag"))
	        .drop(taxi_train_df.col("pickup_datetime")).drop(taxi_train_df.col("dropoff_datetime"))
	        .drop(taxi_train_df.col("pickup_longitude")).drop(taxi_train_df.col("pickup_latitude"))
	        .drop(taxi_train_df.col("dropoff_longitude")).drop(taxi_train_df.col("dropoff_latitude"))
	        .drop(taxi_train_df.col("surcharge")).drop(taxi_train_df.col("mta_tax"))
	        .drop(taxi_train_df.col("direct_distance")).drop(taxi_train_df.col("tolls_amount"))
	        .drop(taxi_train_df.col("total_amount")).drop(taxi_train_df.col("tip_class"))
	        .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200"));
	
	# CACHE AND MATERIALIZE CLEANED DATA-FRAME IN MEMORY
	taxi_df_train_cleaned.cache()
	taxi_df_train_cleaned.count()
	
	# REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
	taxi_df_train_cleaned.registerTempTable("taxi_train")
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**OUTPUT:**

Tempo impiegato per eseguire questa cella: 8 secondi.


### Eseguire query sulla tabella e importare i risultati in un frame di dati. 

Eseguire query sulla tabella per i dati relativi alle tariffe, ai passeggeri e alle mance, filtrare i dati danneggiati e non pertinenti e stampare più righe.

	# QUERY THE DATA
	val sqlStatement = """
	    SELECT fare_amount, passenger_count, tip_amount, tipped
	    FROM taxi_train 
	    WHERE passenger_count > 0 AND passenger_count < 7
	    AND fare_amount > 0 AND fare_amount < 200
	    AND payment_type in ('CSH', 'CRD')
	    AND tip_amount > 0 AND tip_amount < 25
	"""
	val sqlResultsDF = sqlContext.sql(sqlStatement)
	
	# SHOW ONLY THE TOP THREE ROWS
	sqlResultsDF.show(3)

**OUTPUT:**

fare\_amount|passenger\_count|tip\_amount|tipped
-----------|---------------|----------|------
 13,5| 1\.0| 2,9| 1\.0
 16,0| 2\.0| 3\.4| 1\.0
 10,5| 2\.0| 1\.0| 1\.0


## Visualizzazione ed esplorazione dei dati 

Dopo aver inserito i dati in Spark, il passaggio successivo del processo di analisi scientifica dei dati consiste nell'esplorazione e nella visualizzazione dei dati per approfondirne la conoscenza. In questa sezione vengono esaminati i dati relativi ai taxi tramite query SQL e vengono importati i risultati in un frame di dati per tracciare le variabili di destinazione e le funzionalità potenziali per l'esame visivo con la funzionalità di visualizzazione automatica di Jupyter.

### Usare magic local e SQL per tracciare i dati

Per impostazione predefinita, l'output di ogni frammento di codice eseguito da un'istanza di Jupyter Notebook è disponibile all'interno del contesto della sessione persistente nei nodi del ruolo di lavoro. Se si vuole salvare una corsa nei nodi del ruolo di lavoro per ogni calcolo e se tutti i dati necessari per il calcolo sono disponibili localmente nel nodo server Jupyter, ovvero il nodo head, è possibile usare il magic %%local per eseguire il frammento di codice nel server Jupyter.

- **Magic SQL (`%%sql`)**: il kernel HDInsight Spark supporta l'esecuzione di query HiveQL inline semplici su sqlContext. L'argomento (-o NOME\_VARIABILE) rende persistente l'output della query SQL come un frame di dati Pandas nel server Jupyter. Questo significa che sarà disponibile in modalità locale.
- Il **magic `%%local`** viene usato per eseguire il codice in locale nel server Jupyter, che costituisce il nodo head del cluster HDInsight. In genere si usa il magic `%%local` in combinazione con il magic `%%sql` con il parametro -o. Il parametro -o rende persistente l'output della query SQL a livello locale e quindi il magic %%local attiva il successivo set di frammenti di codice che viene eseguito localmente a fronte dell'output della query SQL persistente a livello locale.

### Eseguire query sui dati con SQL
Questa query recupera le corse per importo della tariffa, numero di passeggeri e importo della mancia.

	# SQL QUERY
	%%sql -q -o sqlResults
	SELECT fare_amount, passenger_count, tip_amount, tipped FROM taxi_train WHERE passenger_count > 0 AND passenger_count < 7 AND fare_amount > 0 AND fare_amount < 200 AND payment_type in ('CSH', 'CRD') AND tip_amount > 0 AND tip_amount < 25

Nel codice seguente, il magic %%local crea un frame di dati locale, sqlResults, che può essere usato per eseguire tracciati con matplotlib.

> [AZURE.TIP] Il magic local viene usato più volte in questa procedura dettagliata. In caso di un'elevata quantità di dati, campionare i dati per creare un frame che possa essere contenuto nella memoria locale.

### Tracciare i dati

È possibile tracciare i dati usando codice Python quando il frame di dati si trova nel contesto locale come frame di dati Pandas.

	# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
	%%local
	
	# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
	# CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
	sqlResults


 Il kernel Spark visualizza automaticamente l'output delle query SQL (HiveQL) dopo aver eseguito il codice. È possibile scegliere tra diversi tipi di visualizzazione:

- Tabella
- Grafico a torta
- Grafico a linee
- Area
- Grafico a barre

Di seguito è indicato il codice per tracciare i dati:

	# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
	%%local
	import matplotlib.pyplot as plt
	%matplotlib inline
	
	# TIP BY PAYMENT TYPE AND PASSENGER COUNT
	ax1 = sqlResults[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
	ax1.set_title('Tip amount distribution')
	ax1.set_xlabel('Tip Amount ($)')
	ax1.set_ylabel('Counts')
	plt.suptitle('')
	plt.show()
	
	# TIP BY PASSENGER COUNT
	ax2 = sqlResults.boxplot(column=['tip_amount'], by=['passenger_count'])
	ax2.set_title('Tip amount by Passenger count')
	ax2.set_xlabel('Passenger count')
	ax2.set_ylabel('Tip Amount ($)')
	plt.suptitle('')
	plt.show()
	
	# TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
	ax = sqlResults.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(sqlResults.passenger_count))
	ax.set_title('Tip amount by Fare amount')
	ax.set_xlabel('Fare Amount ($)')
	ax.set_ylabel('Tip Amount ($)')
	plt.axis([-2, 80, -2, 20])
	plt.show()


**OUTPUT:**

![Istogramma degli importi delle mance](./media/machine-learning-data-science-process-scala-walkthrough/plot-tip-amount-histogram.png)

![Importo della mancia per numero di passeggeri](./media/machine-learning-data-science-process-scala-walkthrough/plot-tip-amount-by-passenger-count.png)

![Importo della mancia per importo della corsa](./media/machine-learning-data-science-process-scala-walkthrough/plot-tip-amount-by-fare-amount.png)



## Creazione di funzionalità e preparazione dei dati per le funzioni di modellazione 

Per usare le funzioni di modellazione basate su albero da Spark ML e MLlib, è necessario preparare funzionalità e destinazione con una serie di tecniche appropriate, ad esempio creazione di contenitori, indicizzazione, codifica one-hot e vettorializzazione. Di seguito sono le indicate le procedure analizzate in questa sezione:

- Ottenere una nuova funzionalità dalla **creazione di contenitori** per gli orari di trasporto
- **Indicizzare e applicare la codifica one-hot** per le funzionalità categoriche
- **Campionare e dividere il set di dati** in frazioni di training e di test
- **Specificare la variabile e le funzionalità di training** e creare RDD o frame di dati con punti etichettati per training e test indicizzati o con codifica one-hot
- **Classificare e vettorializzare automaticamente funzionalità e destinazioni** per l'uso come input per i modelli ML


### Ottenere una nuova funzionalità dalla creazione di contenitori per gli orari di trasporto 

Questo codice illustra come ottenere una nuova funzionalità dalla creazione di contenitori per gli orari di trasporto e come memorizzare nella cache il frame di dati risultante in memoria. Quando si usano ripetutamente RDD (Resilient Distributed Dataset) e frame di dati, la memorizzazione nella cache consente di migliorare i tempi di esecuzione. RDD e frame di dati vengono quindi memorizzati nella cache in varie fasi di questa procedura dettagliata.

	# CREATE FOUR BUCKETS FOR TRAFFIC TIMES
	val sqlStatement = """
	    SELECT *,
	    CASE
	     WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
	     WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
	     WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
	     WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
	    END as TrafficTimeBins
	    FROM taxi_train 
	"""
	val taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)
	
	# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY 
	taxi_df_train_with_newFeatures.cache()
	taxi_df_train_with_newFeatures.count()


### Indicizzare e applicare la codifica one-hot per le funzionalità categoriche 

Questa sezione illustra come indicizzare o codificare le funzionalità categoriche per l'inserimento nelle funzioni di modellazione. Per poter usare le funzioni di modellazione e previsione di MLlib, è necessario prima indicizzare o codificare le funzionalità con dati di input categorici.

A seconda del modello, è necessario indicizzare o codificare tali funzioni in modi diversi. Ad esempio, i modelli logistici e di regressione lineare richiedono la codifica one-hot. Ad esempio, una funzionalità con 3 categorie può essere espansa in 3 colonne di funzionalità, in cui ogni colonna contiene 0 o 1 a seconda della categoria di un'osservazione. Per eseguire la codifica one-hot in MLlib è disponibile la funzione [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder). Questo codificatore esegue il mapping di una colonna di indici etichetta a una colonna di vettori binari, con al massimo un singolo valore unico. Questa codifica permette di applicare a funzionalità categoriche gli algoritmi che prevedono funzionalità con valori numerici, ad esempio la regressione logistica.

In questo caso vengono trasformate solo quattro variabili per illustrare esempi che sono stringhe di caratteri. È possibile indicizzare come variabili categoriche anche altre variabili, ad esempio il giorno della settimana, rappresentate da valori numerici.

Per l'indicizzazione è stata usata la funzione `StringIndexer()` e per la codifica one-hot è stata usata la funzione `OneHotEncoder()` di MLlib. Di seguito è riportato il codice per l'indicizzazione e la codifica delle funzionalità categoriche:


	# HERE WE CREATE INDEXES, AND ONE-HOT ENCODED VECTORS FOR SEVERAL CATEGORICAL FEATURES

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# INDEX AND ENCODE VENDOR_ID
	val stringIndexer = new StringIndexer().setInputCol("vendor_id").setOutputCol("vendorIndex").fit(taxi_df_train_with_newFeatures)
	val indexed = stringIndexer.transform(taxi_df_train_with_newFeatures)
	val encoder = new OneHotEncoder().setInputCol("vendorIndex").setOutputCol("vendorVec")
	val encoded1 = encoder.transform(indexed)
	
	# INDEX AND ENCODE RATE_CODE
	val stringIndexer = new StringIndexer().setInputCol("rate_code").setOutputCol("rateIndex").fit(encoded1)
	val indexed = stringIndexer.transform(encoded1)
	val encoder = new OneHotEncoder().setInputCol("rateIndex").setOutputCol("rateVec")
	val encoded2 = encoder.transform(indexed)
	
	# INDEX AND ENCODE PAYMENT_TYPE
	val stringIndexer = new StringIndexer().setInputCol("payment_type").setOutputCol("paymentIndex").fit(encoded2)
	val indexed = stringIndexer.transform(encoded2)
	val encoder = new OneHotEncoder().setInputCol("paymentIndex").setOutputCol("paymentVec")
	val encoded3 = encoder.transform(indexed)
	
	# INDEX AND TRAFFIC TIME BINS
	val stringIndexer = new StringIndexer().setInputCol("TrafficTimeBins").setOutputCol("TrafficTimeBinsIndex").fit(encoded3)
	val indexed = stringIndexer.transform(encoded3)
	val encoder = new OneHotEncoder().setInputCol("TrafficTimeBinsIndex").setOutputCol("TrafficTimeBinsVec")
	val encodedFinal = encoder.transform(indexed)
	
	# GET TIME TO RUN THE CELL 
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**OUTPUT:**

Tempo impiegato per eseguire questa cella: 4 secondi.



### Campionare e dividere il set di dati in frazioni di training e di test

Questo codice crea un campionamento casuale dei dati, qui viene usato il 25%. Anche se non è necessario per questo esempio, date le dimensioni del set di dati, viene illustrato come eseguire il campionamento e come usarlo quando necessario. Nei campioni di grandi dimensioni questa operazione permette di risparmiare molto tempo durante il training dei modelli. Successivamente il campione viene suddiviso in un set di training (75%) e un set di testing (25%) da usare nei modelli di regressione e classificazione.

Viene aggiunto un numero casuale compreso tra 0 e 1 per raggiungere la riga di una colonna "rand" che può essere usata per selezionare riduzioni di convalida incrociata durante il training.


	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# SPECIFY SAMPLING AND SPLITTING FRACTIONS
	val samplingFraction = 0.25;
	val trainingFraction = 0.75; 
	val testingFraction = (1-trainingFraction);
	val seed = 1234;
	val encodedFinalSampledTmp = encodedFinal.sample(withReplacement = false, fraction = samplingFraction, seed = seed)
	val sampledDFcount = encodedFinalSampledTmp.count().toInt
	
	val generateRandomDouble = udf(() => {
	    scala.util.Random.nextDouble
	})
	
	# ADD RANDOM NUMBER FOR CV
	val encodedFinalSampled = encodedFinalSampledTmp.withColumn("rand", generateRandomDouble());
	
	# SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST, WITH A RANDOM COLUMN ADDED FOR DOING CV (SHOWN LATER)
	# INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS
	val splits = encodedFinalSampled.randomSplit(Array(trainingFraction, testingFraction), seed = seed)
	val trainData = splits(0)
	val testData = splits(1)
	
	# GET TIME TO RUN THE CELL 
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**OUTPUT:**

Tempo impiegato per eseguire questa cella: 2 secondi.


### Specificare le funzionalità e la variabile di training e creare RDD o frame di dati con punti etichettati per training e test indicizzati o con codifica one-hot. 

Questa sezione contiene codice che illustra come indicizzare dati di testo categorici come tipo di dati punto etichettato e codificarli per l'uso per il training e il testing della regressione logistica MLlib e di altri modelli di classificazione. Gli oggetti punto etichettato sono RDD (Resilient Distributed Dataset) formattati come richiesto per i dati di input dalla maggior parte degli algoritmi di apprendimento automatico in MLlib. Un [punto etichettato](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) è un vettore locale, che può essere denso o sparso, associato a un'etichetta o a una risposta.

In questo codice vengono specificate la variabile di destinazione (dipendente) e le funzionalità da usare per il training e vengono creati RDD o frame di dati con punti etichettati per training e test indicizzati o con codifica one-hot.


	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# MAP NAMES OF FEATURES AND TARGETS FOR CLASSIFICATION AND REGRESSION PROBLEMS.
	val featuresIndOneHot = List("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))
	val featuresIndIndex = List("paymentIndex", "vendorIndex", "rateIndex", "TrafficTimeBinsIndex", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))
	
	# SPECIFY THE TARGET FOR CLASSIFICATION ('tipped') AND REGRESSION ('tip_amount') PROBLEMS
	val targetIndBinary = List("tipped").map(encodedFinalSampled.columns.indexOf(_))
	val targetIndRegression = List("tip_amount").map(encodedFinalSampled.columns.indexOf(_))
	
	# INDEXED LAELEDPOINT RDD OBJECTS
	val indexedTRAINbinary = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
	val indexedTESTbinary = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
	val indexedTRAINreg = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
	val indexedTESTreg = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
	
	# Indexed DFs that can be used for training using Spark ML functions
	val indexedTRAINbinaryDF = indexedTRAINbinary.toDF()
	val indexedTESTbinaryDF = indexedTESTbinary.toDF()
	val indexedTRAINregDF = indexedTRAINreg.toDF()
	val indexedTESTregDF = indexedTESTreg.toDF()
	
	# One-hot encoded (vectorized) DFs that can be used for training using Spark ML functions
	val assemblerOneHot = new VectorAssembler().setInputCols(Array("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount")).setOutputCol("features")
	val OneHotTRAIN = assemblerOneHot.transform(trainData) 
	val OneHotTEST = assemblerOneHot.transform(testData)
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**OUTPUT:**

Tempo impiegato per eseguire questa cella: 4 secondi.


### Classificare e vettorializzare automaticamente funzionalità e destinazioni per l'uso come input per i modelli ML

Classificare correttamente la destinazione e le funzionalità da usare nelle funzioni di modellazione basate su albero in Spark ML. Il codice esegue due attività:

1. Crea una destinazione binaria per la classificazione assegnando un valore pari a 0 o 1 a ogni punto dati compreso tra 0 e 1 con un valore soglia di 0,5.
2. Classifica automaticamente le funzionalità. Se il numero di valori numerici distinti per una qualsiasi funzionalità è < 32, verrà classificata tale funzionalità.

Di seguito è riportato il codice per queste due attività.

	# CATEGORIZE FEATURES AND BINARIZE TARGET FOR BINARY CLASSIFICATION PROBLEM

	# TRAIN DATA
	val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
	val indexerModel = indexer.fit(indexedTRAINbinaryDF)
	val indexedTrainwithCatFeat = indexerModel.transform(indexedTRAINbinaryDF)
	val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
	val indexedTRAINwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)
	
	# TEST DATA
	val indexerModel = indexer.fit(indexedTESTbinaryDF)
	val indexedTrainwithCatFeat = indexerModel.transform(indexedTESTbinaryDF)
	val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
	val indexedTESTwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)
	
	# CATEGORIZE FEATURES FOR REGRESSION PROBLEM
	# CREATE PROPERLY INDEXED AND CATEGORIED DFS FOR TREE-BASED MODELS

	# TRAIN DATA
	val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
	val indexerModel = indexer.fit(indexedTRAINregDF)
	val indexedTRAINwithCatFeat = indexerModel.transform(indexedTRAINregDF)
	
	# TEST DATA
	val indexerModel = indexer.fit(indexedTESTbinaryDF)
	val indexedTESTwithCatFeat = indexerModel.transform(indexedTESTregDF)


## Classificazione binaria: prevede se viene lasciata o meno una mancia

In questa sezione vengono creati tre tipi di modelli di classificazione binaria per prevedere se viene lasciata o meno una mancia:

- un **modello di regressione logistica** con la funzione `LogisticRession()` di Spark ML
- un **modello di classificazione di foresta casuale** con la funzione `RandomForestClassifier()` di Spark ML
- un **modello di classificazione con alberi con boosting a gradienti** con la funzione `GradientBoostedTrees()` di MLlib

### Creare un modello di regressione logistica

In questa sezione viene creato un modello di regressione logistica con la funzione `LogisticRession()` di Spark ML. Il modello di compilazione del codice in questa sezione è costituito da una serie di passaggi:

1. Dati di **training del modello** con un set di parametri
2. **Valutazione del modello** su un set di dati di test con metriche
3. **Salvataggio del modello** in un BLOB per l'utilizzo in futuro
4. **un punteggio al modello** in base ai dati di test
5. **Creazione del tracciato dei risultati**: curve ROC

Di seguito è riportato il codice per queste procedure.

	# CREATE LOGISTIC REGRESSION MODEL 
	val lr = new LogisticRegression().setLabelCol("tipped").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)
	val lrModel = lr.fit(OneHotTRAIN)
	
	# PREDICT ON TEST DATA SET
	val predictions = lrModel.transform(OneHotTEST)
	
	# SELECT BinaryClassificationEvaluator() TO COMPUTE TEST ERROR
	val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
	val ROC = evaluator.evaluate(predictions)
	println("ROC on test data = " + ROC)
	
	# SAVE THE MODEL
	val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
	val modelName = "LogisticRegression__"
	val filename = modelDir.concat(modelName).concat(datestamp)
	lrModel.save(filename);

Caricare, classificare e salvare i risultati.

	# RECORD START TIME 
	val starttime = Calendar.getInstance().getTime()

	# LOAD SAVED MODEL AND SCORE THE TEST DATA SET
	val savedModel = org.apache.spark.ml.classification.LogisticRegressionModel.load(filename)
	println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")
	
	# SCORE THE MODEL ON THE TEST DATA
	val predictions = savedModel.transform(OneHotTEST).select("tipped","probability","rawPrediction")
	predictions.registerTempTable("testResults")
	
	# SELECT BinaryClassificationEvaluator() TO COMPUTE TEST ERROR
	val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
	val ROC = evaluator.evaluate(predictions)
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");
	
	# PRINT THE ROC RESULTS
	println("ROC on test data = " + ROC)


**OUTPUT:**

ROC sui dati di test = 0.9827381497557599


Usare Python nei frame di dati Pandas locali per tracciare la curva ROC.


	# QUERY RESULTS
	%%sql -q -o sqlResults
	SELECT tipped, probability from testResults


	# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
	%%local
	%matplotlib inline
	from sklearn.metrics import roc_curve,auc
	
	sqlResults['probFloat'] = sqlResults.apply(lambda row: row['probability'].values()[0][1], axis=1)
	predictions_pddf = sqlResults[["tipped","probFloat"]]
	
	# ROC CURVE
	# predictions_pddf = sqlResults.rename(columns={'_1': 'probability', 'tipped': 'label'})
	prob = predictions_pddf["probFloat"] 
	fpr, tpr, thresholds = roc_curve(predictions_pddf['tipped'], prob, pos_label=1);
	roc_auc = auc(fpr, tpr)
	
	#PLOT
	plt.figure(figsize=(5,5))
	plt.plot(fpr, tpr, label='ROC curve (area = %0.2f)' % roc_auc)
	plt.plot([0, 1], [0, 1], 'k--')
	plt.xlim([0.0, 1.0])
	plt.ylim([0.0, 1.05])
	plt.xlabel('False Positive Rate')
	plt.ylabel('True Positive Rate')
	plt.title('ROC Curve')
	plt.legend(loc="lower right")
	plt.show()


**OUTPUT:**

![Curva ROC per mancia o non mancia](./media/machine-learning-data-science-process-scala-walkthrough/plot-roc-curve-tip-or-not.png)


### Creare un modello di classificazione di foresta casuale

In questa sezione viene creato un modello di classificazione di foresta casuale con la funzione `RandomForestClassifier()` di Spark ML. Il modello viene quindi valutato sui dati di test.


	# RECORD START TIME 
	val starttime = Calendar.getInstance().getTime()
	
	# CREATE THE RANDOM FOREST CLASSIFIER MODEL
	val rf = new RandomForestClassifier().setLabelCol("labelBin").setFeaturesCol("featuresCat").setNumTrees(10).setSeed(1234)
	
	# FIT THE MODEL
	val rfModel = rf.fit(indexedTRAINwithCatFeatBinTarget)
	val predictions = rfModel.transform(indexedTESTwithCatFeatBinTarget)
	
	# EVALUATE THE MODEL
	val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
	val Test_f1Score = evaluator.evaluate(predictions)
	println("F1 score on test data: " + Test_f1Score);
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");
	
	# CALCULATE BINARY CLASSIFICATION EVALUATION METRICSS
	val evaluator = new BinaryClassificationEvaluator().setLabelCol("label").setRawPredictionCol("probability").setMetricName("areaUnderROC")
	val ROC = evaluator.evaluate(predictions)
	println("ROC on test data = " + ROC)


**OUTPUT:**

ROC sui dati di test = 0.9847103571552683


### Creare un modello di classificazione con alberi con boosting a gradienti

In questa sezione viene creato un modello di classificazione con alberi con boosting a gradienti con la funzione `GradientBoostedTrees()` di MLlib. Il modello viene quindi valutato sui dati di test.


	# TRAIN A GBT CLASSIFICATION MODEL USING MLIB AND LABELEDPOINT

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# DEFINE THE GBT CLASSIFICATION MODEL
	val boostingStrategy = BoostingStrategy.defaultParams("Classification")
	boostingStrategy.numIterations = 20
	boostingStrategy.treeStrategy.numClasses = 2
	boostingStrategy.treeStrategy.maxDepth = 5
	boostingStrategy.treeStrategy.categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))
	
	# TRAIN THE MODEL
	val gbtModel = GradientBoostedTrees.train(indexedTRAINbinary, boostingStrategy)
	
	# SAVE THE MODEL IN BLOB STORAGE
	val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
	val modelName = "GBT_Classification__"
	val filename = modelDir.concat(modelName).concat(datestamp)
	gbtModel.save(sc, filename);
	
	# EVALUATE THE MODEL ON TEST INSTANCES AND THE COMPUTE TEST ERROR
	val labelAndPreds = indexedTESTbinary.map { point =>
	  val prediction = gbtModel.predict(point.features)
	  (point.label, prediction)
	}
	val testErr = labelAndPreds.filter(r => r._1 != r._2).count.toDouble / indexedTRAINbinary.count()
	//println("Learned classification GBT model:\n" + gbtModel.toDebugString)
	println("Test Error = " + testErr)
	
	# USE BINARY AND MULTICLASS METRICS TO EVALUATE THE MODEL ON THE TEST DATA
	val metrics = new MulticlassMetrics(labelAndPreds)
	println(s"Precision: ${metrics.precision}")
	println(s"Recall: ${metrics.recall}")
	println(s"F1 Score: ${metrics.fMeasure}")
	
	val metrics = new BinaryClassificationMetrics(labelAndPreds)
	println(s"Area under PR curve: ${metrics.areaUnderPR}")
	println(s"Area under ROC curve: ${metrics.areaUnderROC}")
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");
	
	# PRINT THE ROC METRIC
	println(s"Area under ROC curve: ${metrics.areaUnderROC}")


**OUTPUT:**

Area sotto la curva ROC = 0,9846895479241554


## Regressione: stimare l'importo della mancia 

In questa sezione vengono creati due tipi di modelli di regressione per stimare l'importo della mancia:

- un **modello di regressione lineare regolarizzata** con la funzione `LinearRegression()` di Spark ML. Il modello viene quindi salvato e valutato sui dati di test.
- un **modello di regressione con alberi con boosting a gradienti** con la funzione `GBTRegressor() ` di Spark ML


### Creare un modello di regressione lineare regolarizzata

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# CREATE REGULARIZED LINEAR REGRESSION MODEL USING SPARK ML FUNCTION AND DATA-FRAME
	val lr = new LinearRegression().setLabelCol("tip_amount").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)
	
	# FIT THE MODEL USING DATA-FRAME
	val lrModel = lr.fit(OneHotTRAIN)
	println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")
	
	# SUMMARIZE THE MODEL OVER THE TRAIINNG SET AND PRINT OUT SOME METRICS
	val trainingSummary = lrModel.summary
	println(s"numIterations: ${trainingSummary.totalIterations}")
	println(s"objectiveHistory: ${trainingSummary.objectiveHistory.toList}")
	trainingSummary.residuals.show()
	println(s"RMSE: ${trainingSummary.rootMeanSquaredError}")
	println(s"r2: ${trainingSummary.r2}")
	
	# SAVE THE MODEL IN BLOB STORAGE
	val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
	val modelName = "LinearRegression__"
	val filename = modelDir.concat(modelName).concat(datestamp)
	lrModel.save(filename);
	
	# PRINT COEFFICIENTS
	println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")
	
	# SCORE THE MODEL ON TEST DATA
	val predictions = lrModel.transform(OneHotTEST)
	
	# EVALUATE THE MODEL ON TEST DATA
	val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
	val r2 = evaluator.evaluate(predictions)
	println("R-sqr on test data = " + r2)
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**OUTPUT:**

Tempo impiegato per eseguire questa cella: 13 secondi.


	# LOAD A SAVED LINEARREGRESSION MODEL FROM BLOB STORAGE AND SCORE A TEST DATA SET. 

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# LOAD A SAVED LINEARREGRESSION MODEL FROM BLOB STORAGE
	val savedModel = org.apache.spark.ml.regression.LinearRegressionModel.load(filename)
	println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")
	
	# SCORE THE MODEL ON TEST DATA
	val predictions = savedModel.transform(OneHotTEST).select("tip_amount","prediction")
	predictions.registerTempTable("testResults")
	
	# EVALUATE THE MODEL ON TEST DATA
	val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
	val r2 = evaluator.evaluate(predictions)
	println("R-sqr on test data = " + r2)
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");
	
	# PRINT RESULTS
	println("R-sqr on test data = " + r2)


**OUTPUT:**

R-sqr sui dati di test = 0.5960320470835743


Eseguire quindi una query sui risultati del test come frame di dati e visualizzare usando autoviz di Jupyter e matplotlib di Python.


	# SQL QUERY
	%%sql -q -o sqlResults
	select * from testResults

	# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
	%%local

	# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
	# CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
	sqlResults

Il codice crea un frame di dati locale dall'output della query ed esegue il tracciato dei dati. Il magic `%%local` crea un frame di dati locale, `sqlResults`, che può essere usato per eseguire tracciati con matplotlib.

>[AZURE.NOTE] Questo magic Spark viene usato più volte in questa procedura dettagliata. In caso di un'elevata quantità di dati, è consigliabile campionare i dati in modo da creare un frame che possa essere contenuto nella memoria locale.

Creare tracciati usando matplotlib di Python.

	# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
	%%local
	sqlResults
	%matplotlib inline
	import numpy as np
	
	# PLOT THE RESULTS
	ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='tip_amount', y='prediction', color='blue', alpha = 0.25, label='Actual vs. predicted');
	fit = np.polyfit(sqlResults['tip_amount'], sqlResults['prediction'], deg=1)
	ax.set_title('Actual vs. Predicted Tip Amounts ($)')
	ax.set_xlabel("Actual")
	ax.set_ylabel("Predicted")
	#ax.plot(sqlResults['tip_amount'], fit[0] * sqlResults['prediction'] + fit[1], color='magenta')
	plt.axis([-1, 15, -1, 8])
	plt.show(ax)

**OUTPUT:**

![Importo della mancia: effettivo rispetto a stimato](./media/machine-learning-data-science-process-scala-walkthrough/plot-actual-vs-predicted-tip-amount.png)


### Creare un modello di regressione con alberi con boosting a gradienti

In questa sezione viene creato un modello di regressione con alberi con boosting a gradienti con la funzione `ML GBTRegressor()` di Spark. Il modello viene quindi valutato sui dati di test.

Gli [alberi con boosting a gradienti](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBT, Gradient boosted tree) sono insiemi di alberi delle decisioni. Gli alberi GBT eseguono il training degli alberi delle decisioni in modo iterativo per ridurre al minimo la perdita di funzioni. Gli alberi GBT vengono usati per la classificazione e la regressione e possono gestire funzionalità categoriche, non richiedono il ridimensionamento delle funzionalità e possono rilevare non linearità e interazioni di funzionalità. Possono anche essere usati in un'impostazione di classificazione multiclasse.


	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# TRAIN A GBT REGRESSION MODEL
	val gbt = new GBTRegressor().setLabelCol("label").setFeaturesCol("featuresCat").setMaxIter(10)
	val gbtModel = gbt.fit(indexedTRAINwithCatFeat)
	
	# MAKE PREDICTIONS
	val predictions = gbtModel.transform(indexedTESTwithCatFeat)
	
	# COMPUTE TEST SET R2
	val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
	val Test_R2 = evaluator.evaluate(predictions)
	
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");
	
	# PRINT RESULTS
	println("Test R-sqr is: " + Test_R2);


**OUTPUT:**

Il valore R-sqr del test è: 0.7655383534596654



## Utilità di modellazione avanzate per l'ottimizzazione

Questa sezione illustra come usare le utilità ML che vengono spesso impiegate per l'ottimizzazione dei modelli. Verranno in particolare illustrati tre modi diversi per ottimizzare i modelli ML con sweep dei parametri e convalida incrociata:

1\. Suddividere i dati in set di training e convalida, ottimizzare il modello tramite sweep di iperparametri su un set di training e valutazione su un set di convalida (regressione lineare)

2\. Ottimizzare il modello usando la convalida incrociata e lo sweep di iperparametri tramite la funzione CrossValidator di Spark ML (classificazione binaria)

3\. Ottimizzare il modello tramite codice personalizzato di convalida incrociata e sweep di parametri per usare qualsiasi funzione e set di parametri ML (regressione lineare)


La **convalida incrociata** è una tecnica che consente di valutare in che modo un modello con training eseguito su un set di dati noto verrà generalizzato per stimare le funzionalità di set di dati su cui non è stato eseguito il training. L'idea generale alla base di questa tecnica consiste nell'eseguire il training di un modello su un set di dati noti e quindi di testare l'accuratezza delle stime su un set di dati indipendente. Un'implementazione comune usata qui consiste nel dividere un set di dati in K riduzioni e quindi eseguire il training del modello in base a uno schema round robin su tutte le riduzioni eccetto una.

L'**ottimizzazione degli iperparametri** consiste nello scegliere un set di iperparametri per un algoritmo di apprendimento, in genere con l'obiettivo di ottimizzare una misura delle prestazioni dell'algoritmo su un set di dati indipendente. Gli **iperparametri** sono valori che devono essere specificati al di fuori della procedura di training del modello. I presupposti di questi valori possono influire sulla flessibilità e l'accuratezza dei modelli. Gli alberi delle decisioni includono, ad esempio, iperparametri come la profondità desiderata e il numero di foglie nell'albero. Le macchine a vettori di supporto (SVM, Support Vector Machine) richiedono l'impostazione di una penalità per errata classificazione.

Un modo comune per eseguire l'ottimizzazione degli iperparametri usato in questo articolo è una ricerca nella griglia o **sweep di parametri**. Si tratta di eseguire una ricerca completa nei valori di un subset specificato dello spazio degli iperparametri per un algoritmo di apprendimento. La convalida incrociata può fornire metriche delle prestazioni per selezionare i risultati ottimali generati dall'algoritmo di ricerca nella griglia. La convalida incrociata usata con lo sweep di iperparametri limita i problemi come l'overfitting di un modello rispetto ai dati di training, in modo che il modello mantenga la capacità di essere applicato al set di dati generale da cui sono stati estratti i dati di training.

### Ottimizzare il modello di regressione lineare con sweep di iperparametri

Suddividere i dati in set di training e convalida, ottimizzare il modello tramite sweep di iperparametri su un set di training e valutazione su un set di convalida (regressione lineare)

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# RENAME tip_amount AS LABEL
	val OneHotTRAINLabeled = OneHotTRAIN.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label") 
	val OneHotTESTLabeled = OneHotTEST.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
	OneHotTRAINLabeled.cache()
	OneHotTESTLabeled.cache()
	
	# DEFINE THE ESTIMATOR FUNCTION: THE LinearRegression() FUNCTION
	val lr = new LinearRegression().setLabelCol("label").setFeaturesCol("features").setMaxIter(10)
	
	# DEFINE THE PARAMETER GRID
	val paramGrid = new ParamGridBuilder().addGrid(lr.regParam, Array(0.1, 0.01, 0.001)).addGrid(lr.fitIntercept).addGrid(lr.elasticNetParam, Array(0.1, 0.5, 0.9)).build()
	
	# DEFINE PIPELINE WITH TRAIN-TEST VALIDATION SPLIT, WITH 75% IN THE TRAIING SET, SPECIFY ESTIMATOR, EVALUATOR, AND PARAMETER GRID
	val trainPct = 0.75
	val trainValidationSplit = new TrainValidationSplit().setEstimator(lr).setEvaluator(new RegressionEvaluator).setEstimatorParamMaps(paramGrid).setTrainRatio(trainPct)
	
	# RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
	val model = trainValidationSplit.fit(OneHotTRAINLabeled)
	
	# MAKE PREDICTIONS ON THE TEST DATA USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORM THE BEST
	val testResults = model.transform(OneHotTESTLabeled).select("label", "prediction")
	
	# COMPUTE TEST SET R2
	val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
	val Test_R2 = evaluator.evaluate(testResults)
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");
	
	println("Test R-sqr is: " + Test_R2);


**OUTPUT:**

Il valore R-sqr del test è: 0.6226484708501209


### Ottimizzare il modello di classificazione binaria usando la convalida incrociata e lo sweep di iperparametri

Questa sezione illustra come ottimizzare un modello di classificazione binaria usando la convalida incrociata e lo sweep di iperparametri. Per questa operazione viene usata la funzione CrossValidator di Spark ML.

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# CREATE DATA-FRAMES WITH PROPERLY LABELED COLUMNS FOR USE WITH TRAIN/TEST SPLIT
	val indexedTRAINwithCatFeatBinTargetRF = indexedTRAINwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
	val indexedTESTwithCatFeatBinTargetRF = indexedTESTwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
	indexedTRAINwithCatFeatBinTargetRF.cache()
	indexedTESTwithCatFeatBinTargetRF.cache()
	
	# DEFINE THE ESTIMATOR FUNCTION
	val rf = new RandomForestClassifier().setLabelCol("label").setFeaturesCol("features").setImpurity("gini").setSeed(1234).setFeatureSubsetStrategy("auto").setMaxBins(32)
	
	# DEFINE THE PARAMETER GRID
	val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(4,8)).addGrid(rf.numTrees, Array(5,10)).addGrid(rf.minInstancesPerNode, Array(100,300)).build()
	
	# SPECIFY THE NUMBER OF FOLDS
	val numFolds = 3
	
	# DEFINE THE TRAIN-TEST VALIDATION SPLIT WITH 75% IN THE TRAIING SET
	val CrossValidator = new CrossValidator().setEstimator(rf).setEvaluator(new BinaryClassificationEvaluator).setEstimatorParamMaps(paramGrid).setNumFolds(numFolds)
	
	# RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
	val model = CrossValidator.fit(indexedTRAINwithCatFeatBinTargetRF)
	
	# MAKE PREDICTIONS ON THE TEST DATA USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORM THE BEST
	val testResults = model.transform(indexedTESTwithCatFeatBinTargetRF).select("label", "prediction")
	
	# COMPUTE TEST F1 SCORE
	val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
	val Test_f1Score = evaluator.evaluate(testResults)
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**OUTPUT:**

Tempo impiegato per eseguire questa cella: 33 secondi.


### Ottimizzare il modello di regressione lineare tramite codice personalizzato di convalida incrociata e sweep di parametri

In questa sezione viene ottimizzato il modello con codice personalizzato e vengono identificati i parametri ottimali per il modello usando il criterio di massima precisione. Verrà quindi creato il modello finale, che verrà valutato sui dati di test e salvato nell'archivio BLOB. Il modello verrà quindi caricato, verrà assegnato un punteggio ai dati di test e ne verrà valutata la precisione.

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# DEFINE PARAMETER GRID AND THE NUMBER OF FOLDS
	val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(5,10)).addGrid(rf.numTrees, Array(10,25,50)).build()
	
	val nFolds = 3
	val numModels = paramGrid.size
	val numParamsinGrid = 2
	
	# SPECIFY THE NUMBER OF CATEGORIES FOR THE CATEGORIAL VARIBLES
	val categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))
	
	var maxDepth = -1
	var numTrees = -1
	var param = ""
	var paramval = -1
	var validateLB = -1.0
	var validateUB = -1.0
	val h = 1.0 / nFolds;
	val RMSE  = Array.fill(numModels)(0.0)
	
	# CREATE K FOLDS
	val splits = MLUtils.kFold(indexedTRAINbinary, numFolds = nFolds, seed=1234)
	
	
	# LOOP THROUGH K-FOLDS AND THE PARAMETER GRID TO GET AND IDENTIFY THE BEST PARAMETER SET BY ACCURACY
	for (i <- 0 to (nFolds-1)) {
	    validateLB = i * h
	    validateUB = (i + 1) * h
	    val validationCV = trainData.filter($"rand" >= validateLB  && $"rand" < validateUB)
	    val trainCV = trainData.filter($"rand" < validateLB  || $"rand" >= validateUB)
	    val validationLabPt = validationCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
	    val trainCVLabPt = trainCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
	    validationLabPt.cache()
	    trainCVLabPt.cache()
	
	    for (nParamSets <- 0 to (numModels-1)) {
	        for (nParams <- 0 to (numParamsinGrid-1)) {
	            param = paramGrid(nParamSets).toSeq(nParams).param.toString.split("__")(1)
	            paramval = paramGrid(nParamSets).toSeq(nParams).value.toString.toInt
	            if (param == "maxDepth") {maxDepth = paramval}
	            if (param == "numTrees") {numTrees = paramval}
	        }
	        val rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
	                                                  numTrees=numTrees, maxDepth=maxDepth,
	                                                  featureSubsetStrategy="auto",impurity="variance", maxBins=32)
	        val labelAndPreds = validationLabPt.map { point =>
	                                                 val prediction = rfModel.predict(point.features)
	                                                 ( prediction, point.label )
	                                                }
	        val validMetrics = new RegressionMetrics(labelAndPreds)
	        val rmse = validMetrics.rootMeanSquaredError
	        RMSE(nParamSets) += rmse
	    }
	    validationLabPt.unpersist();
	    trainCVLabPt.unpersist();
	}
	val minRMSEindex = RMSE.indexOf(RMSE.min)
	
	# GET BEST PARAMETERS FROM CV AND PARAMETER SWEEP 
	var best_maxDepth = -1
	var best_numTrees = -1
	for (nParams <- 0 to (numParamsinGrid-1)) {
	    param = paramGrid(minRMSEindex).toSeq(nParams).param.toString.split("__")(1)
	    paramval = paramGrid(minRMSEindex).toSeq(nParams).value.toString.toInt
	    if (param == "maxDepth") {best_maxDepth = paramval}
	    if (param == "numTrees") {best_numTrees = paramval}
	}
	
	# CREATE BEST MODEL WITH BEST PARAMETERS AND FULL TRAIING DATASET
	val best_rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
	                                                  numTrees=best_numTrees, maxDepth=best_maxDepth,
	                                                  featureSubsetStrategy="auto",impurity="variance", maxBins=32)
	
	# SAVE BEST RF MODEL IN BLOB STORAGE
	val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
	val modelName = "BestCV_RF_Regression__"
	val filename = modelDir.concat(modelName).concat(datestamp)
	best_rfModel.save(sc, filename);
	
	# PREDICT ON TRAINING SET WITH BEST MODEL AND EVALUATE
	val labelAndPreds = indexedTESTreg.map { point =>
	                                        val prediction = best_rfModel.predict(point.features)
	                                        ( prediction, point.label )
	                                       }
	
	val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
	val test_rsqr = new RegressionMetrics(labelAndPreds).r2
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");


	# LOAD THE MODEL
	val savedRFModel = RandomForestModel.load(sc, filename)
	
	val labelAndPreds = indexedTESTreg.map { point =>
	                                        val prediction = savedRFModel.predict(point.features)
	                                        ( prediction, point.label )
	                                       }
	# TEST THE MODEL
	val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
	val test_rsqr = new RegressionMetrics(labelAndPreds).r2


**OUTPUT:**

Tempo impiegato per eseguire questa cella: 61 secondi.


## Usare automaticamente i modelli ML compilati con Spark con Scala

La procedura per l'uso del codice Scala per caricare automaticamente nuovi set di dati e assegnare loro un punteggio con modelli ML compilati in Spark e salvati nei BLOB di Azure è disponile nell'argomento [Assegnare punteggi a modelli di apprendimento automatico compilati con Spark](machine-learning-data-science-spark-model-consumption.md). È possibile seguire le istruzioni illustrate nell'argomento e sostituire semplicemente il codice Python con il codice Scala indicato in precedenza per abilitare l'utilizzo automatico.

## Passaggi successivi

Per una panoramica degli argomenti che forniscono informazioni dettagliate sulle attività che costituiscono il processo di analisi scientifica dei dati in Azure, vedere [Processo di analisi scientifica dei dati per i team](http://aka.ms/datascienceprocess).

Per una descrizione delle altre procedure dettagliate end-to-end che illustrano i passaggi del processo di analisi scientifica dei dati per i team sulla base di **scenari specifici** e spiegano come combinare strumenti cloud, strumenti locali e servizi in un flusso di lavoro o una pipeline per creare un'applicazione intelligente, vedere [Procedure dettagliate del Processo di analisi scientifica dei dati per i team](data-science-process-walkthroughs.md).

<!---HONumber=AcomDC_0803_2016-->