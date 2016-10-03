<properties
	pageTitle="Assegnare punteggi a modelli di apprendimento automatico compilati con Spark | Microsoft Azure"
	description="Informazioni su come assegnare punteggi a modelli di apprendimento salvati in BLOB di Archiviazione di Azure (WASB)."
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="jhubbard"
	editor="cgronlun" /> 

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/14/2016"
	ms.author="deguhath;bradsev;gokuma" /> 

# Assegnare punteggi a modelli di apprendimento automatico compilati con Spark 

[AZURE.INCLUDE [machine-learning-spark-modeling](../../includes/machine-learning-spark-modeling.md)]

Questo argomento descrive come caricare modelli di Machine Learning compilati con MLlib di Spark e archiviati in BLOB di Archiviazione di Azure (WASB) e come assegnare loro un punteggio con set di dati archiviati in WASB. Illustra come pre-elaborare i dati di input, come trasformare le funzionalità con le funzioni di codifica e indicizzazione nel toolkit MLlib e come creare un oggetto dati punto etichettato da usare come input per l'assegnazione dei punteggi con i modelli di apprendimento automatico. I modelli usati per l'assegnazione dei punteggi includono la regressione lineare, la regressione logistica, le foreste casuali e gli alberi con boosting a gradienti.


## Prerequisiti

1. Per completare questa procedura dettagliata è necessario avere un account Azure e un cluster Spark 1.6 su HDInsight 3.4. Per informazioni sui requisiti, per una descrizione dei dati relativi ai taxi della città di New York nel 2013 usati in questa esercitazione e per istruzioni su come eseguire il codice da notebook di Jupyter nel cluster Spark, vedere l'articolo [Panoramica dell'analisi scientifica dei dati tramite Spark in Azure HDInsight](machine-learning-data-science-spark-overview.md). Il notebook **machine-learning-data-science-spark-data-exploration-modeling.ipynb** contenente gli esempi di codice usati in questo argomento è disponibile in [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark).

2. È anche necessario creare i modelli di Machine Learning per l'assegnazione dei punteggi. A tale scopo, vedere l'argomento [Modellazione ed esplorazione dei dati con Spark](machine-learning-data-science-spark-data-exploration-modeling.md).


[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]
 

## Configurazione: percorsi di archiviazione, librerie e contesto Spark preimpostato

Spark può eseguire operazioni di lettura e scrittura in BLOB di Archiviazione di Azure (WASB). I dati esistenti archiviati in WASB possono essere elaborati con Spark e i relativi risultati possono essere memorizzati nuovamente in BLOB di Archiviazione di Azure.

Per salvare file o modelli in WASB, è necessario specificare correttamente il percorso. È possibile fare riferimento al contenitore predefinito collegato al cluster Spark usando un percorso che inizia con: *"wasb//"*. L'esempio di codice seguente specifica il percorso dei dati da leggere e il percorso della directory di archiviazione del modello in cui verrà salvato l'output del modello.


### Impostare percorsi di directory per i percorsi di archiviazione in WASB

I modelli vengono salvati in: "wasb:///user/remoteuser/NYCTaxi/Models". Se questo percorso non è impostato correttamente, i modelli non vengono caricati per l'assegnazione dei punteggi.

I risultati con punteggio vengono salvati in: "wasb:///user/remoteuser/NYCTaxi/ScoredResults". Se il percorso della cartella non è corretto, i risultati non vengono salvati nella cartella.


>[AZURE.NOTE] È possibile incollare i percorsi dei file nei segnaposto di questo codice dopo averli copiati dall'output dell'ultima cella del notebook **machine-learning-data-science-spark-data-exploration-modeling.ipynb**.


Ecco il codice per impostare i percorsi di directory:

	# LOCATION OF DATA TO BE SCORED (TEST DATA)
	taxi_test_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Test.tsv";
	
	# SET THE MODEL STORAGE DIRECTORY PATH 
	# NOTE THE LAST BACKSLASH IN THIS PATH IS NEEDED
	modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 
	
	# SET SCORDED RESULT DIRECTORY PATH
	# NOTE THE LAST BACKSLASH IN THIS PATH IS NEEDED
	scoredResultDir = "wasb:///user/remoteuser/NYCTaxi/ScoredResults/"; 
	
	# FILE LOCATIONS FOR THE MODELS TO BE SCORED
	logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-04-1817_40_35.796789"
	linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-04-1817_44_00.993832"
	randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-04-1817_42_58.899412"
	randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-04-1817_44_27.204734"
	BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-04-1817_43_16.354770"
	BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-04-1817_44_46.206262"

	# RECORD START TIME
	import datetime
	datetime.datetime.now()

**OUTPUT:**

datetime.datetime(2016, 4, 25, 23, 56, 19, 229403)


### Importare le librerie

Impostare il contesto Spark e importare le librerie necessarie usando il codice seguente.

	#IMPORT LIBRARIES
	import pyspark
	from pyspark import SparkConf
	from pyspark import SparkContext
	from pyspark.sql import SQLContext
	import matplotlib
	import matplotlib.pyplot as plt
	from pyspark.sql import Row
	from pyspark.sql.functions import UserDefinedFunction
	from pyspark.sql.types import *
	import atexit
	from numpy import array
	import numpy as np
	import datetime


### Contesto di Spark preimpostato e magic di PySpark

I kernel PySpark forniti con i notebook di Jupyter dispongono di un contesto preimpostato e pertanto non è necessario impostare i contesti Spark o Hive in modo esplicito prima di iniziare a usare l'applicazione in corso di sviluppo, poiché sono disponibili per impostazione predefinita. Questi contesti sono:

- sc per Spark
- sqlContext per Hive

Il kernel PySpark offre alcuni “magic” predefiniti, ovvero comandi speciali che è possibile chiamare con %%. Negli esempi di codice seguenti sono usati due comandi di questo tipo.

- **%%local**: specifica che il codice presente nelle righe successive verrà eseguito localmente. Deve trattarsi di codice Python valido.
- **%%sql -o <nome variabile>** Esegue una query Hive su sqlContext. Se viene passato il parametro -o, il risultato della query viene salvato in modo permanente nel contesto Python %%local come frame di dati Pandas.
 

Per altre informazioni sui kernel per le istanze di Jupyter Notebook e i "magic" predefiniti chiamati con %% (ad esempio %%local) messi a disposizione, vedere [Kernel disponibili per Jupyter Notebook con cluster Apache Spark in HDInsight Linux](../hdinsight/hdinsight-apache-spark-jupyter-notebook-kernels.md).


## Inserire i dati e creare un frame di dati pulito

Questa sezione contiene il codice per una serie di attività necessarie per inserire i dati per l'assegnazione dei punteggi. Leggere un campione unito in join pari allo 0,1% del file TSV relativo alle corse e alle tariffe dei taxi, formattare i dati e creare un frame di dati pulito.

I file relativi alle corse e alle tariffe dei taxi sono stati uniti seguendo la procedura illustrata nell'articolo [Processo di analisi scientifica dei dati per i team in azione: uso dei cluster Hadoop di HDInsight](machine-learning-data-science-process-hive-walkthrough.md).

	# INGEST DATA AND CREATE A CLEANED DATA FRAME

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# IMPORT FILE FROM PUBLIC BLOB
	taxi_test_file = sc.textFile(taxi_test_file_loc)
	
	# GET SCHEMA OF THE FILE FROM HEADER
	taxi_header = taxi_test_file.filter(lambda l: "medallion" in l)
	
	# PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
	taxi_temp = taxi_test_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
	        .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
	                        float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
	                        float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))
	    
	# GET SCHEMA OF THE FILE FROM HEADER
	schema_string = taxi_test_file.first()
	fields = [StructField(field_name, StringType(), True) for field_name in schema_string.split('\t')]
	fields[7].dataType = IntegerType() #Pickup hour
	fields[8].dataType = IntegerType() # Pickup week
	fields[9].dataType = IntegerType() # Weekday
	fields[10].dataType = IntegerType() # Passenger count
	fields[11].dataType = FloatType() # Trip time in secs
	fields[12].dataType = FloatType() # Trip distance
	fields[19].dataType = FloatType() # Fare amount
	fields[20].dataType = FloatType() # Surcharge
	fields[21].dataType = FloatType() # Mta_tax
	fields[22].dataType = FloatType() # Tip amount
	fields[23].dataType = FloatType() # Tolls amount
	fields[24].dataType = FloatType() # Total amount
	fields[25].dataType = IntegerType() # Tipped or not
	fields[26].dataType = IntegerType() # Tip class
	taxi_schema = StructType(fields)
	
	# CREATE DATA FRAME
	taxi_df_test = sqlContext.createDataFrame(taxi_temp, taxi_schema)
	
	# CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
	taxi_df_test_cleaned = taxi_df_test.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
	    .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
	    .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
	    .drop('direct_distance').drop('surcharge')\
	    .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )
	
	# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
	taxi_df_test_cleaned.cache()
	taxi_df_test_cleaned.count()
	
	# REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
	taxi_df_test_cleaned.registerTempTable("taxi_test")
	
	# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**OUTPUT:**

Tempo impiegato per eseguire questa cella: 46,37 secondi.


## Preparare i dati per l'assegnazione dei punteggi in Spark 

Questa sezione illustra come indicizzare, codificare e ridimensionare le caratteristiche categoriche per prepararle all'uso in algoritmi di apprendimento supervisionato di MLlib, per la classificazione e la regressione.

### Trasformazione di funzionalità: indicizzare e codificare funzionalità categoriche per l'inserimento in modelli per l'assegnazione dei punteggi 

Questa sezione illustra come indicizzare dati categorici con `StringIndexer` e codificare funzionalità con `OneHotEncoder` per l'inserimento nei modelli.

[StringIndexer](http://spark.apache.org/docs/latest/ml-features.html#stringindexer) esegue la codifica di una colonna stringa di etichette in una colonna di indici etichetta. Gli indici sono ordinati in base alla frequenza delle etichette.

[OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) esegue il mapping di una colonna di indici etichetta a una colonna di vettori binari, con al massimo un singolo valore unico. Questa codifica permette di applicare a funzionalità categoriche gli algoritmi che prevedono funzionalità con valori continui, ad esempio la regressione logistica.
	
	#INDEX AND ONE-HOT ENCODE CATEGORICAL FEATURES

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, VectorIndexer
	
	# CREATE FOUR BUCKETS FOR TRAFFIC TIMES
	sqlStatement = """
	    SELECT *,
	    CASE
	     WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
	     WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
	     WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
	     WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
	    END as TrafficTimeBins
	    FROM taxi_test 
	"""
	taxi_df_test_with_newFeatures = sqlContext.sql(sqlStatement)
	
	# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
	taxi_df_test_with_newFeatures.cache()
	taxi_df_test_with_newFeatures.count()
	
	# INDEX AND ONE-HOT ENCODING
	stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
	model = stringIndexer.fit(taxi_df_test_with_newFeatures) # Input data-frame is the cleaned one from above
	indexed = model.transform(taxi_df_test_with_newFeatures)
	encoder = OneHotEncoder(dropLast=False, inputCol="vendorIndex", outputCol="vendorVec")
	encoded1 = encoder.transform(indexed)
	
	# INDEX AND ENCODE RATE_CODE
	stringIndexer = StringIndexer(inputCol="rate_code", outputCol="rateIndex")
	model = stringIndexer.fit(encoded1)
	indexed = model.transform(encoded1)
	encoder = OneHotEncoder(dropLast=False, inputCol="rateIndex", outputCol="rateVec")
	encoded2 = encoder.transform(indexed)
	
	# INDEX AND ENCODE PAYMENT_TYPE
	stringIndexer = StringIndexer(inputCol="payment_type", outputCol="paymentIndex")
	model = stringIndexer.fit(encoded2)
	indexed = model.transform(encoded2)
	encoder = OneHotEncoder(dropLast=False, inputCol="paymentIndex", outputCol="paymentVec")
	encoded3 = encoder.transform(indexed)
	
	# INDEX AND ENCODE TRAFFIC TIME BINS
	stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
	model = stringIndexer.fit(encoded3)
	indexed = model.transform(encoded3)
	encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
	encodedFinal = encoder.transform(indexed)
	
	# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**OUTPUT:**

Tempo impiegato per eseguire questa cella: 5,37 secondi.


### Creare oggetti RDD con matrici di funzionalità per l'inserimento in modelli

Questa sezione contiene codice che illustra come indicizzare dati di testo categorici come oggetti RDD e come usare la codifica one-hot per codificarli per l'uso per il training e il testing della regressione logistica MLlib e dei modelli basati su albero. I dati indicizzati vengono archiviati come oggetti [RDD (Resilient Distributed Dataset)](http://spark.apache.org/docs/latest/api/java/org/apache/spark/rdd/RDD.html). Si tratta dell'astrazione di base in Spark. Un oggetto RDD rappresenta una raccolta partizionata non modificabile di elementi su cui è possibile operare in parallelo con Spark.

Contiene anche codice che mostra come ridimensionare i dati con `StandardScalar`, fornito da MLlib per l'uso nella regressione lineare con la discesa del gradiente stocastica (SGD), un algoritmo molto diffuso per il training di una vasta gamma di modelli di Machine Learning. [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) viene usato per ridimensionare le funzionalità alla varianza unitaria. Il ridimensionamento di funzionalità, noto anche come normalizzazione dei dati, permette di fare in modo che alle funzionalità con valori molto dispersi non venga attribuito un peso eccessivo nella funzione obiettivo.


	# CREATE RDD OBJECTS WITH FEATURE ARRAYS FOR INPUT INTO MODELS

	# RECORD START TIME
	timestart = datetime.datetime.now()

	# IMPORT LIBRARIES
	from pyspark.mllib.linalg import Vectors
	from pyspark.mllib.feature import StandardScaler, StandardScalerModel
	from pyspark.mllib.util import MLUtils
	from numpy import array
	
	# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
	def parseRowIndexingBinary(line):
	    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex,
	                         line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
	                         line.trip_distance, line.fare_amount])
	    return  features
	
	# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC RERESSION MODELS
	def parseRowOneHotBinary(line):
	    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
	                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
	                                        line.vendorVec.toArray(), line.rateVec.toArray(), 
	                                        line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
	    return  features
	
	# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
	def parseRowIndexingRegression(line):
	    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
	                         line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
	                         line.trip_distance, line.fare_amount])
	    return  features
	
	# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
	def parseRowOneHotRegression(line):
	    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
	                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
	                                        line.vendorVec.toArray(), line.rateVec.toArray(), 
	                                        line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
	    return  features

	# FOR BINARY CLASSIFICATION TRAINING AND TESTING
	indexedTESTbinary = encodedFinal.map(parseRowIndexingBinary)
	oneHotTESTbinary = encodedFinal.map(parseRowOneHotBinary)
	
	# FOR REGRESSION CLASSIFICATION TRAINING AND TESTING
	indexedTESTreg = encodedFinal.map(parseRowIndexingRegression)
	oneHotTESTreg = encodedFinal.map(parseRowOneHotRegression)
	
	# SCALING FEATURES FOR LINEARREGRESSIONWITHSGD MODEL
	scaler = StandardScaler(withMean=False, withStd=True).fit(oneHotTESTreg)
	oneHotTESTregScaled = scaler.transform(oneHotTESTreg)
	
	# CACHE RDDS IN MEMORY
	indexedTESTbinary.cache();
	oneHotTESTbinary.cache();
	indexedTESTreg.cache();
	oneHotTESTreg.cache();
	oneHotTESTregScaled.cache();
	
	# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**OUTPUT:**

Tempo impiegato per eseguire questa cella: 11,72 secondi.


## Assegnare punteggi con il modello di regressione logistica e salvare l'output in BLOB

Il codice riportato in questa sezione illustra come caricare un modello di regressione logistica salvato in un archivio BLOB di Azure e come usarlo per prevedere se viene lasciata una mancia per una corsa in taxi, assegnare un punteggio con metriche di classificazione standard e quindi salvare e tracciare i risultati nell'archivio BLOB. I risultati con punteggio vengono archiviati in oggetti RDD.


	# SCORE AND EVALUATE LOGISTIC REGRESSION MODEL

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# IMPORT LIBRARIES
	from pyspark.mllib.classification import LogisticRegressionModel
	
	## LOAD SAVED MODEL
	savedModel = LogisticRegressionModel.load(sc, logisticRegFileLoc)
	predictions = oneHotTESTbinary.map(lambda features: (float(savedModel.predict(features))))
	
	## SAVE SCORED RESULTS (RDD) TO BLOB
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp + ".txt";
	dirfilename = scoredResultDir + logisticregressionfilename;
	predictions.saveAsTextFile(dirfilename)
	
	
	# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**OUTPUT:**

Tempo impiegato per eseguire questa cella: 19,22 secondi.


## Assegnare punteggi a un modello di regressione lineare

Per il training di un modello di regressione lineare con discesa del gradiente stocastica (SGD) per l'ottimizzazione allo scopo di prevedere l'importo delle mance lasciate è stato usato [LinearRegressionWithSGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD).

Il codice riportato in questa sezione illustra come caricare un modello di regressione lineare dall'archivio BLOB di Azure, assegnare un punteggio con variabili ridimensionate e salvare nuovamente i risultati nel BLOB.

	#SCORE LINEAR REGRESSION MODEL

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	#LOAD LIBRARIES​
	from pyspark.mllib.regression import LinearRegressionWithSGD, LinearRegressionModel
	
	# LOAD MODEL AND SCORE USING **SCALED VARIABLES**
	savedModel = LinearRegressionModel.load(sc, linearRegFileLoc)
	predictions = oneHotTESTregScaled.map(lambda features: (float(savedModel.predict(features))))
	
	# SAVE RESULTS
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
	dirfilename = scoredResultDir + linearregressionfilename;
	predictions.saveAsTextFile(dirfilename)
	
	# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL​
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**OUTPUT:**

Tempo impiegato per eseguire questa cella: 16,63 secondi.


## Assegnare punteggi a modelli di foresta casuale per la classificazione e la regressione

Il codice riportato in questa sezione illustra come caricare i modelli di foresta casuale per la classificazione e la regressione salvati nell'archivio BLOB di Azure, assegnare punteggi alle relative prestazioni con misure di classificazione e regressione standard e salvare nuovamente i risultati nell'archivio BLOB.

Le [foreste casuali](http://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) sono insiemi di alberi delle decisioni. Combinano diversi alberi delle decisioni per ridurre il rischio di overfitting. Le foreste casuali possono gestire funzionalità categoriche, si estendono all'impostazione di classificazione multiclasse, non richiedono il ridimensionamento delle funzionalità e possono rilevare non linearità e interazioni di funzionalità. Le foreste casuali sono tra i modelli di apprendimento automatico più diffusi per la classificazione e la regressione.

[spark.mllib](http://spark.apache.org/mllib/) supporta foreste casuali per la classificazione binaria e multiclasse e per la regressione, con funzionalità sia continue che categoriche.

	# SCORE RANDOM FOREST MODELS FOR CLASSIFICATION AND REGRESSION

	# RECORD START TIME
	timestart = datetime.datetime.now()

	#IMPORT MLLIB LIBRARIES	
	from pyspark.mllib.tree import RandomForest, RandomForestModel
	
	
	# CLASSIFICATION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB
	savedModel = RandomForestModel.load(sc, randomForestClassificationFileLoc)
	predictions = savedModel.predict(indexedTESTbinary)
	
	# SAVE RESULTS
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	rfclassificationfilename = "RandomForestClassification_" + datestamp + ".txt";
	dirfilename = scoredResultDir + rfclassificationfilename;
	predictions.saveAsTextFile(dirfilename)
	

	# REGRESSION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB
	savedModel = RandomForestModel.load(sc, randomForestRegFileLoc)
	predictions = savedModel.predict(indexedTESTreg)
	
	# SAVE RESULTS
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	rfregressionfilename = "RandomForestRegression_" + datestamp + ".txt";
	dirfilename = scoredResultDir + rfregressionfilename;
	predictions.saveAsTextFile(dirfilename)

	# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**OUTPUT:**

Tempo impiegato per eseguire questa cella: 31,07 secondi.


## Assegnare punteggi a modelli di alberi con boosting a gradienti per la classificazione e la regressione

Il codice riportato in questa sezione illustra come caricare i modelli di alberi con boosting a gradienti (GBT) per la classificazione e la regressione salvati nell'archivio BLOB di Azure, assegnare punteggi alle relative prestazioni con misure di classificazione e regressione standard e salvare nuovamente i risultati nell'archivio BLOB.

**spark.mllib** supporta gli alberi GBT per la classificazione binaria e per la regressione, con funzionalità sia continue che categoriche.

Gli [alberi con boosting a gradienti](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBT) sono insiemi di alberi delle decisioni. Gli alberi GBT eseguono il training degli alberi delle decisioni in modo iterativo per ridurre al minimo la perdita di funzioni. Gli alberi GBT possono gestire funzionalità categoriche, non richiedono il ridimensionamento delle funzionalità e possono rilevare non linearità e interazioni di funzionalità. Possono anche essere usati in un'impostazione di classificazione multiclasse.


	# SCORE GRADIENT BOOSTING TREE MODELS FOR CLASSIFICATION AND REGRESSION

	# RECORD START TIME
	timestart = datetime.datetime.now()

	#IMPORT MLLIB LIBRARIES
	from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
	
	# CLASSIFICATION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB

	#LOAD AND SCORE THE MODEL
	savedModel = GradientBoostedTreesModel.load(sc, BoostedTreeClassificationFileLoc)
	predictions = savedModel.predict(indexedTESTbinary)
	
	# SAVE RESULTS
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp + ".txt";
	dirfilename = scoredResultDir + btclassificationfilename;
	predictions.saveAsTextFile(dirfilename)
	

	# REGRESSION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB

	# LOAD AND SCORE MODEL 
	savedModel = GradientBoostedTreesModel.load(sc, BoostedTreeRegressionFileLoc)
	predictions = savedModel.predict(indexedTESTreg)
	
	# SAVE RESULTS
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	btregressionfilename = "GradientBoostingTreeRegression_" + datestamp + ".txt";
	dirfilename = scoredResultDir + btregressionfilename;
	predictions.saveAsTextFile(dirfilename)


	# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
	
**OUTPUT:**

Tempo impiegato per eseguire questa cella: 14,6 secondi.


## Pulire gli oggetti dalla memoria e stampare i percorsi di file con punteggio

	# UNPERSIST OBJECTS CACHED IN MEMORY
	taxi_df_test_cleaned.unpersist()
	indexedTESTbinary.unpersist();
	oneHotTESTbinary.unpersist();
	indexedTESTreg.unpersist();
	oneHotTESTreg.unpersist();
	oneHotTESTregScaled.unpersist();


	# PRINT OUT PATH TO SCORED OUTPUT FILES
	print "logisticRegFileLoc: " + logisticregressionfilename;
	print "linearRegFileLoc: " + linearregressionfilename;
	print "randomForestClassificationFileLoc: " + rfclassificationfilename;
	print "randomForestRegFileLoc: " + rfregressionfilename;
	print "BoostedTreeClassificationFileLoc: " + btclassificationfilename;
	print "BoostedTreeRegressionFileLoc: " + btregressionfilename;


**OUTPUT:**

logisticRegFileLoc: LogisticRegressionWithLBFGS_2016-05-0317_22\_38.953814.txt

linearRegFileLoc: LinearRegressionWithSGD_2016-05-0317_22\_58.878949

randomForestClassificationFileLoc: RandomForestClassification_2016-05-0317_23\_15.939247.txt

randomForestRegFileLoc: RandomForestRegression_2016-05-0317_23\_31.459140.txt

BoostedTreeClassificationFileLoc: GradientBoostingTreeClassification_2016-05-0317_23\_49.648334.txt

BoostedTreeRegressionFileLoc: GradientBoostingTreeRegression_2016-05-0317_23\_56.860740.txt



## Utilizzare i modelli Spark da un'interfaccia Web

Spark offre un meccanismo che permette di inviare in modalità remota processi batch o query interattive tramite un'interfaccia REST con un componente denominato Livy. Livy è abilitato per impostazione predefinita nel cluster HDInsight Spark. Per altre informazioni, vedere [Inviare processi Spark in modalità remota tramite Livy](../hdinsight/hdinsight-apache-spark-livy-rest-interface.md).

Livy può essere usato per inviare in modalità remota un processo che assegna punteggi in batch a un file archiviato in un BLOB di Azure e quindi scrive i risultati in un altro BLOB. A tale scopo, caricare lo script Python da [GitHub](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/Spark/Python/ConsumeGBNYCReg.py) nel BLOB del cluster Spark. Per copiare lo script nel BLOB del cluster è possibile usare uno strumento come **Explorer di Archiviazione di Microsoft Azure** o **AzCopy**. In questo caso lo script è stato caricato in ***wasb:///example/python/ConsumeGBNYCReg.py***.


>[AZURE.NOTE] Le chiavi di accesso necessarie sono reperibili nel portale dell'account di archiviazione associato al cluster Spark.


Dopo aver eseguito il caricamento in questo percorso, lo script viene eseguito all'interno del cluster Spark in un contesto distribuito. Carica il modello ed esegue previsioni sui file di input in base al modello.

È possibile richiamare lo script in modalità remota tramite una semplice richiesta HTTPS/REST in Livy. Di seguito è riportato un comando curl per costruire la richiesta HTTP per richiamare lo script Python in modalità remota. Sostituire CLUSTERLOGIN, CLUSTERPASSWORD e CLUSTERNAME con i valori appropriati per il cluster Spark usato.


	# CURL COMMAND TO INVOKE PYTHON SCRIPT WITH HTTP REQUEST

    curl -k --user "CLUSTERLOGIN:CLUSTERPASSWORD" -X POST --data "{"file": "wasb:///example/python/ConsumeGBNYCReg.py"}" -H "Content-Type: application/json" https://CLUSTERNAME.azurehdinsight.net/livy/batches

Con una semplice chiamata HTTPS con autenticazione di base è possibile usare qualsiasi linguaggio nel sistema remoto per richiamare il processo Spark tramite Livy.


>[AZURE.NOTE] Potrebbe essere preferibile usare la libreria di richieste Python quando si esegue la chiamata HTTP, ma attualmente non è installata per impostazione predefinita in Funzioni di Azure. Vengono quindi usate altre librerie HTTP.


Di seguito è riportato il codice Python per la chiamata HTTP:

	#MAKE AN HTTPS CALL ON LIVY. 

	import os

	# OLDER HTTP LIBRARIES USED HERE INSTEAD OF THE REQUEST LIBRARY AS THEY ARE AVAILBLE BY DEFAULT
	import httplib, urllib, base64
	
	# REPLACE VALUE WITH ONES FOR YOUR SPARK CLUSTER
	host = '<spark cluster name>.azurehdinsight.net:443'
	username='<username>'
	password='<password>'
	
	#AUTHORIZATION
	conn = httplib.HTTPSConnection(host)
	auth = base64.encodestring('%s:%s' % (username, password)).replace('\n', '')
	headers = {'Content-Type': 'application/json', 'Authorization': 'Basic %s' % auth}
	
	# SPECIFY THE PYTHON SCRIPT TO RUN ON THE SPARK CLUSTER
	# IN THE FILE PARAMETER OF THE JSON POST REQUEST BODY
	r=conn.request("POST", '/livy/batches', '{"file": "wasb:///example/python/ConsumeGBNYCReg.py"}', headers )
	response = conn.getresponse().read()
	print(response)
	conn.close()


È anche possibile aggiungere questo codice Python a [Funzioni di Azure](https://azure.microsoft.com/documentation/services/functions/) per attivare l'invio di un processo Spark che assegna punteggi a un BLOB in base a vari eventi, ad esempio timer, creazione o aggiornamento di un BLOB.

Se si preferisce non ricorrere al codice, usare [App per la logica di Azure](https://azure.microsoft.com/documentation/services/app-service/logic/) per richiamare l'assegnazione di punteggi in batch di Spark. A tale scopo, specificare un'azione HTTP nell'**area di progettazione delle app per la logica** e impostarne i parametri.

- Nel portale di Azure creare una nuova app per la logica selezionando **+Nuovo** -> **Web e dispositivi mobili** -> **App per la logica**.
- Immettere il nome dell'app per la logica e del piano di servizio app per visualizzare l'**area di progettazione delle app per la logica**.
- Selezionare un'azione HTTP e immettere i parametri mostrati nella figura seguente:

![](./media/machine-learning-data-science-spark-model-consumption/spark-logica-app-client.png) 


## Passaggi successivi 

**Convalida incrociata e sweep di iperparametri**: vedere [Esplorazione e modellazione avanzate dei dati con Spark](machine-learning-data-science-spark-advanced-data-exploration-modeling.md) per informazioni su come istruire i modelli sulla convalida incrociata e lo sweep di iperparametri.

<!---HONumber=AcomDC_0921_2016-->