---
title: Esplorazione e modellazione avanzate dei dati con Spark | Microsoft Docs
description: Usare HDInsight Spark per eseguire l'esplorazione dei dati e il training dei modelli di classificazione binaria e regressione usando la convalida incrociata e l'ottimizzazione di iperparametri.
services: machine-learning
documentationcenter: ''
author: bradsev
manager: jhubbard
editor: cgronlun

ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2016
ms.author: deguhath;bradsev;gokuma

---
# <a name="advanced-data-exploration-and-modeling-with-spark"></a>Esplorazione e modellazione avanzate dei dati con Spark
[!INCLUDE [machine-learning-spark-modeling](../../includes/machine-learning-spark-modeling.md)]

Questa procedura dettagliata usa HDInsight Spark per eseguire l'esplorazione dei dati e il training dei modelli di classificazione binaria e regressione usando la convalida incrociata e l'ottimizzazione di iperparametri su un campione del set di dati relativo alle corse e alle tariffe dei taxi della città di New York nel 2013. Illustra i passaggi end-to-end del [processo di analisi scientifica dei dati](http://aka.ms/datascienceprocess)usando un cluster HDInsight Spark per l'elaborazione e BLOB di Azure per l'archiviazione dei dati e dei modelli. Il processo analizza e visualizza i dati ottenuti da un BLOB di Archiviazione di Azure e li prepara per la compilazione di modelli predittivi. Per il codice della soluzione e per visualizzare i relativi tracciati è stato usato Python. I modelli vengono compilati con il toolkit MLlib di Spark per l'esecuzione di attività di classificazione binaria e modellazione basata sulla regressione. 

* L'attività di **classificazione binaria** consente di prevedere se per la corsa viene lasciata o meno una mancia. 
* L'attività di **regressione** consente di prevedere l'importo della mancia in base ad altre funzionalità relative alle mance. 

La procedura di modellazione include anche un codice che illustra come eseguire il training, valutare e salvare ogni tipo di modello. Nell'argomento vengono illustrati alcuni aspetti comuni all'argomento [Modellazione ed esplorazione dei dati con Spark](machine-learning-data-science-spark-data-exploration-modeling.md). Tuttavia, questo argomento è più "avanzato" nel senso che usa anche la convalida incrociata in combinazione con la ricerca di iperparametri per formare modelli precisi di classificazione e regressione in modo ottimale. 

La **convalida incrociata** è una tecnica che consente di valutare in che modo un modello con training eseguito su un set di dati noto viene generalizzato per stimare le funzionalità di set di dati su cui non è stato eseguito il training. L'idea generale alla base di questa tecnica consiste nell'eseguire il training di un modello su un set di dati noti e quindi di testare l'accuratezza delle stime su un set di dati indipendente. Un'implementazione comune usata qui consiste nel dividere un set di dati in K riduzioni e quindi eseguire il training del modello in base a uno schema round robin su tutte le riduzioni eccetto una. 

**ottimizzazione degli iperparametri** consiste nello scegliere un set di iperparametri per un algoritmo di apprendimento, in genere con l'obiettivo di ottimizzare una misura delle prestazioni dell'algoritmo su un set di dati indipendente. **iperparametri** sono valori che devono essere specificati al di fuori della procedura di training del modello. I presupposti di questi valori possono influire sulla flessibilità e l'accuratezza dei modelli. Gli alberi delle decisioni includono, ad esempio, iperparametri come la profondità desiderata e il numero di foglie nell'albero. Le macchine a vettori di supporto (SVM, Support Vector Machine) richiedono l'impostazione di una penalità per errata classificazione. 

Un modo comune per eseguire l'ottimizzazione degli iperparametri usato in questo articolo è una ricerca nella griglia o **sweep di parametri**. Si tratta di eseguire una ricerca completa nei valori di un subset specificato dello spazio degli iperparametri per un algoritmo di apprendimento. La convalida incrociata può fornire metriche delle prestazioni per selezionare i risultati ottimali generati dall'algoritmo di ricerca nella griglia. La convalida incrociata usata con lo sweep di iperparametri limita i problemi come l'overfitting di un modello rispetto ai dati di training, in modo che il modello mantenga la capacità di essere applicato al set di dati generale da cui sono stati estratti i dati di training.

I modelli proposti includono la regressione logistica e lineare, foreste casuali e alberi con boosting a gradienti:

* [Regressione lineare con SGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) è un modello di regressione lineare che si serve di un metodo di discesa del gradiente stocastico (SGD, Stochastic Gradient Descent), usato per l'ottimizzazione e il ridimensionamento delle funzionalità allo scopo di prevedere l'importo delle mance pagate. 
* [Regressione logistica con L-BFGS](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) , o regressione "logit", è un modello di regressione che può essere usato quando la variabile dipendente usata per la classificazione dei dati è categoriale. L'algoritmo L-BFGS è un algoritmo di ottimizzazione quasi-Newton che approssima l'algoritmo di Broyden-Fletcher-Goldfarb-Shanno (BFGS) usando una quantità limitata di memoria del computer ed è ampiamente usato nell'apprendimento automatico.
* [foreste casuali](http://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) sono insiemi di alberi delle decisioni.  Combinano diversi alberi delle decisioni per ridurre il rischio di overfitting. Le foreste casuali vengono usate per la classificazione e la regressione, sono in grado di gestire funzionalità relative alle categorie e possono essere estese all'impostazione di classificazione a più classi. Non richiedono il ridimensionamento delle funzionalità e possono rilevare non linearità e interazioni di funzionalità. Le foreste casuali sono tra i modelli di apprendimento automatico più diffusi per la classificazione e la regressione.
* [Alberi con boosting a gradienti](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBT, Gradient boosted tree) sono insiemi di alberi delle decisioni. Gli alberi GBT eseguono il training degli alberi delle decisioni in modo iterativo per ridurre al minimo la perdita di funzioni. Gli alberi GBT vengono usati per la classificazione e la regressione e possono gestire funzionalità categoriche, non richiedono il ridimensionamento delle funzionalità e possono rilevare non linearità e interazioni di funzionalità. Possono anche essere usati in un'impostazione di classificazione multiclasse.

Esempi di modelli che usano la convalida incrociata e sweep di iperparametri sono illustrati per il problema della classificazione binaria. Esempi più semplici, senza sweep di parametri, sono illustrati nell'argomento principale per le attività di regressione. Nell'appendice sono tuttavia descritte anche la convalida con Elastic Net per la regressione lineare e la convalida incrociata con sweep dei parametri per la regressione tramite foresta casuale. **elastic net** è un metodo di regressione regolarizzata per l'adattamento di modelli di regressione lineare che combina in modo lineare le metriche L1 e L2 come penalità dei metodi [lasso](https://en.wikipedia.org/wiki/Lasso%20%28statistics%29) e [ridge](https://en.wikipedia.org/wiki/Tikhonov_regularization).   

> [!NOTE]
> Anche se il toolkit Spark MLlib è progettato per funzionare con set di dati di grandi dimensioni, per maggiore praticità viene usato un campione relativamente ridotto di circa 30 Mb che usa 170.000 righe, ovvero lo 0,1% circa del set di dati originale di NYC. L'esercizio qui proposto viene eseguito in modo efficiente in un cluster HDInsight con 2 nodi di lavoro, in circa 10 minuti. Lo stesso codice può essere usato per elaborare set di dati di dimensioni maggiori, con poche modifiche appropriate per il caching dei dati in memoria e la modifica delle dimensioni del cluster.
> 
> 

## <a name="prerequisites"></a>Prerequisiti
Per completare questa procedura dettagliata è necessario avere un account Azure e un cluster Spark 1.6 su HDInsight 3.4. Per istruzioni su come soddisfare questi requisiti, vedere [Panoramica dell'analisi scientifica dei dati con Spark in Azure HDInsight](machine-learning-data-science-spark-overview.md). Questo argomento contiene inoltre una descrizione dei dati dei taxi di NYC per il 2013 usati qui e istruzioni su come eseguire il codice da un notebook Jupyter nel cluster Spark. Il notebook **pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb** contenente gli esempi di codice usati in questo argomento è disponibile in [Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark).

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="setup:-storage-locations,-libraries,-and-the-preset-spark-context"></a>Configurazione: percorsi di archiviazione, librerie e contesto Spark preimpostato
Spark può eseguire operazioni di lettura e scrittura in BLOB di Archiviazione di Azure (WASB). I dati esistenti archiviati in WASB possono essere elaborati con Spark e i relativi risultati possono essere memorizzati nuovamente in BLOB di Archiviazione di Azure.

Per salvare file o modelli in WASB, è necessario specificare correttamente il percorso. È possibile fare riferimento al contenitore predefinito collegato al cluster Spark usando un percorso che inizia con: "wasb///". "wasb://" fa riferimento ad altri percorsi.

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Impostare percorsi di directory per i percorsi di archiviazione in WASB
L'esempio di codice seguente specifica il percorso dei dati da leggere e il percorso della directory di archiviazione del modello in cui viene salvato l'output del modello.

    # SET PATHS TO FILE LOCATIONS: DATA AND MODEL STORAGE

    # LOCATION OF TRAINING DATA
    taxi_train_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv";


    # SET THE MODEL STORAGE DIRECTORY PATH 
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";

    # PRINT START TIME
    import datetime
    datetime.datetime.now()

**OUTPUT**

datetime.datetime(2016, 4, 18, 17, 36, 27, 832799)

### <a name="import-libraries"></a>Importare le librerie
Importare le librerie necessarie usando il codice seguente:

    # LOAD PYSPARK LIBRARIES
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


### <a name="preset-spark-context-and-pyspark-magics"></a>Contesto di Spark preimpostato e magic di PySpark
I kernel Spark forniti con i notebook Jupyter dispongono di contesti predefiniti. Di conseguenza non è necessario impostare esplicitamente i contesti Spark o Hive prima di iniziare a usare l'applicazione in fase di sviluppo. Questi contesti sono disponibili per impostazione predefinita. Questi contesti sono:

* sc per Spark 
* sqlContext per Hive

Il kernel PySpark offre alcuni “magic” predefiniti, ovvero comandi speciali che è possibile chiamare con %%. Negli esempi di codice seguenti sono usati due comandi di questo tipo.

* **%%local**: specifica che il codice presente nelle righe successive deve essere eseguito localmente. Deve trattarsi di codice Python valido.
* **%%sql -o <variable name>**: esegue una query Hive su sqlContext. Se viene passato il parametro -o, il risultato della query viene salvato in modo permanente nel contesto Python %%local come frame di dati Pandas.

Per altre informazioni sui kernel per i notebook di Jupyter e i "magic" predefiniti messi a disposizione, vedere [Kernel disponibili per i notebook di Jupyter con cluster Apache Spark in HDInsight Linux](../hdinsight/hdinsight-apache-spark-jupyter-notebook-kernels.md).

## <a name="data-ingestion-from-public-blob:"></a>Inserimento di dati dal BLOB pubblico:
Il primo passaggio del processo di analisi scientifica dei dati consiste nel prelevare i dati da analizzare dalle origini in cui risiedono e inserirli nell'ambiente di modellazione ed esplorazione dei dati. In questa procedura dettagliata l'ambiente è Spark. Questa sezione contiene il codice per completare una serie di attività:

* Inserimento del campione di dati da modellare.
* Lettura del set di dati di input archiviato come file TSV.
* Formattazione e pulizia dei dati.
* Creazione di oggetti, RDD o frame di dati, e memorizzazione nella cache in memoria.
* Registrazione come tabella temporanea in un contesto SQL.

Di seguito è riportato il codice per l'inserimento di dati.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # IMPORT FILE FROM PUBLIC BLOB
    taxi_train_file = sc.textFile(taxi_train_file_loc)

    # GET SCHEMA OF THE FILE FROM HEADER
    schema_string = taxi_train_file.first()
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

    # PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
    taxi_header = taxi_train_file.filter(lambda l: "medallion" in l)
    taxi_temp = taxi_train_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
            .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
                            float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
                            float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))


    # CREATE DATA FRAME
    taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)

    # CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
    taxi_df_train_cleaned = taxi_train_df.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
        .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
        .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
        .drop('direct_distance').drop('surcharge')\
        .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )

    # CACHE & MATERIALIZE DATA-FRAME IN MEMORY. GOING THROUGH AND COUNTING NUMBER OF ROWS MATERIALIZES THE DATA-FRAME IN MEMORY
    taxi_df_train_cleaned.cache()
    taxi_df_train_cleaned.count()

    # REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
    taxi_df_train_cleaned.registerTempTable("taxi_train")

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**OUTPUT**

Tempo impiegato per eseguire questa cella: 276,62 secondi.

## <a name="data-exploration-&-visualization"></a>Visualizzazione ed esplorazione dei dati
Dopo aver inserito i dati in Spark, il passaggio successivo del processo di analisi scientifica dei dati consiste nell'esplorazione e nella visualizzazione dei dati per approfondirne la conoscenza. In questa sezione vengono esaminati i dati relativi ai taxi tramite query SQL e vengono tracciate le variabili di destinazione e le funzionalità potenziali per l'esame visivo. In particolare, viene tracciata la frequenza del numero di passeggeri nelle corse dei taxi, la frequenza dell'importo delle mance e la variazione delle mance in base al tipo e all'importo del pagamento.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Tracciare un istogramma delle frequenze del numero di passeggeri nel campione di corse dei taxi
Questo codice e i frammenti di codice successivi usano un magic SQL per eseguire una query sul campione e un magic local per tracciare i dati.

* **Magic SQL (`%%sql`)**: il kernel HDInsight PySpark supporta l'esecuzione di query HiveQL inline semplici su sqlContext. L'argomento (-o NOME_VARIABILE) rende persistente l'output della query SQL come un frame di dati Pandas nel server Jupyter. Questo significa che è disponibile in modalità locale.
* Il **`%%local`** viene usato per eseguire il codice in locale nel server Jupyter, che costituisce il nodo head del cluster HDInsight. In genere si usa il magic `%%local` in combinazione con il magic `%%sql` con il parametro -o. Il parametro -o rende persistente l'output della query SQL a livello locale e quindi il magic %%local attiva il successivo set di frammenti di codice che viene eseguito localmente a fronte dell'output della query SQL persistente a livello locale.

L'output viene visualizzato automaticamente dopo aver eseguito il codice.

Questa query recupera le corse per numero di passeggeri. 

    # PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

    # SQL QUERY
    %%sql -q -o sqlResults
    SELECT passenger_count, COUNT(*) as trip_counts FROM taxi_train WHERE passenger_count > 0 and passenger_count < 7 GROUP BY passenger_count


Questo codice crea un frame di dati locale dall'output della query ed esegue il tracciato dei dati. Il magic `%%local` crea un frame di dati locale, `sqlResults`, che può essere usato per eseguire tracciati con matplotlib. 

> [!NOTE]
> Tale magic PySpark viene usato più volte in questa procedura dettagliata. In caso di un'elevata quantità di dati, è consigliabile campionare i dati in modo da creare un frame che possa essere contenuto nella memoria locale.
> 
> 

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
    # CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
    sqlResults

Ecco il codice per eseguire il tracciato delle corse per numero di passeggeri

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    import matplotlib.pyplot as plt
    %matplotlib inline

    # PLOT PASSENGER NUMBER VS TRIP COUNTS
    x_labels = sqlResults['passenger_count'].values
    fig = sqlResults[['trip_counts']].plot(kind='bar', facecolor='lightblue')
    fig.set_xticklabels(x_labels)
    fig.set_title('Counts of trips by passenger count')
    fig.set_xlabel('Passenger count in trips')
    fig.set_ylabel('Trip counts')
    plt.show()

**OUTPUT**

![Frequenza delle corse per numero di passeggeri](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/frequency-of-trips-by-passenger-count.png)

È possibile scegliere tra diversi tipi di visualizzazioni (tabella, a torta, a linee, ad area o a barre) usando i pulsanti del menu **Type** (Tipo) nel notebook. In questo caso è stato scelto un tracciato a barre.

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts."></a>Tracciare un istogramma dell'importo delle mance e della relativa variazione in base al numero di passeggeri e all'importo delle corse.
Usare una query SQL per campionare i dati.

    # SQL SQUERY
    %%sql -q -o sqlResults
        SELECT fare_amount, passenger_count, tip_amount, tipped
        FROM taxi_train 
        WHERE passenger_count > 0 
        AND passenger_count < 7
        AND fare_amount > 0 
        AND fare_amount < 200
        AND payment_type in ('CSH', 'CRD')
        AND tip_amount > 0 
        AND tip_amount < 25


Questa cella di codice usa la query SQL per creare tre tracciati.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    %matplotlib inline

    # TIP BY PAYMENT TYPE AND PASSENGER COUNT
    ax1 = resultsPDDF[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
    ax1.set_title('Tip amount distribution')
    ax1.set_xlabel('Tip Amount ($)')
    ax1.set_ylabel('Counts')
    plt.suptitle('')
    plt.show()

    # TIP BY PASSENGER COUNT
    ax2 = resultsPDDF.boxplot(column=['tip_amount'], by=['passenger_count'])
    ax2.set_title('Tip amount ($) by Passenger count')
    ax2.set_xlabel('Passenger count')
    ax2.set_ylabel('Tip Amount ($)')
    plt.suptitle('')
    plt.show()

    # TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
    ax = resultsPDDF.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(resultsPDDF.passenger_count))
    ax.set_title('Tip amount by Fare amount ($)')
    ax.set_xlabel('Fare Amount')
    ax.set_ylabel('Tip Amount')
    plt.axis([-2, 120, -2, 30])
    plt.show()


**OUTPUT:** 

![Distribuzione dell'importo delle mance](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/tip-amount-distribution.png)

![Importo della mancia per numero di passeggeri](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Importo della mancia per importo della corsa](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="feature-engineering,-transformation-and-data-preparation-for-modeling"></a>Progettazione di funzionalità, trasformazione e preparazione dei dati per la modellazione
Questa sezione descrive le procedure usate per preparare i dati da usare nella modellazione per l'apprendimento automatico, fornisce il relativo codice e illustra come eseguire queste attività:

* Ottenere una nuova funzionalità dalla creazione di contenitori per gli orari di trasporto
* Indicizzare funzionalità categoriche e applicare la codifica one-hot
* Creare oggetti punto etichettato per l'inserimento in funzioni di apprendimento automatico
* Creare un sottocampionamento casuale dei dati e dividerlo in set di training e di testing
* Ridimensionamento di funzionalità
* Memorizzazione nella cache di oggetti in memoria

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Ottenere una nuova funzionalità dalla creazione di contenitori per gli orari di trasporto
Questo codice illustra come ottenere una nuova funzionalità dalla creazione di contenitori per gli orari di trasporto e come memorizzare nella cache il frame di dati risultante in memoria. Quando si usano ripetutamente RDD (Resilient Distributed Dataset) e frame di dati, la memorizzazione nella cache consente di migliorare i tempi di esecuzione. RDD e frame di dati vengono quindi memorizzati nella cache in varie fasi di questa procedura dettagliata.

    # CREATE FOUR BUCKETS FOR TRAFFIC TIMES
    sqlStatement = """
        SELECT *,
        CASE
         WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
         WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
         WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
         WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
        END as TrafficTimeBins
        FROM taxi_train 
    """
    taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)

    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    # THE .COUNT() GOES THROUGH THE ENTIRE DATA-FRAME,
    # MATERIALIZES IT IN MEMORY, AND GIVES THE COUNT OF ROWS.
    taxi_df_train_with_newFeatures.cache()
    taxi_df_train_with_newFeatures.count()

**OUTPUT**

126050

### <a name="index-and-one-hot-encode-categorical-features"></a>Indicizzare funzionalità categoriche e applicare la codifica one-hot
Questa sezione illustra come indicizzare o codificare le funzionalità categoriche per l'inserimento nelle funzioni di modellazione. Per poter usare le funzioni di modellazione e previsione di MLlib, è necessario prima indicizzare o codificare le funzionalità con dati di input categorici. 

A seconda del modello, è necessario indicizzare o codificare tali funzioni in modi diversi. Ad esempio, i modelli logistici e di regressione lineare richiedono la codifica one-hot in cui, ad esempio, una funzionalità con tre categorie può essere espansa in tre colonne di funzionalità, in cui ogni colonna contiene 0 o 1 a seconda della categoria di un'osservazione. Per eseguire la codifica one-hot in MLlib è disponibile la funzione [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder). Questo codificatore esegue il mapping di una colonna di indici etichetta a una colonna di vettori binari, con al massimo un singolo valore unico. Questa codifica permette di applicare a funzionalità categoriche gli algoritmi che prevedono funzionalità con valori numerici, ad esempio la regressione logistica.

Di seguito è riportato il codice per l'indicizzazione e la codifica delle funzionalità categoriche:

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer

    # INDEX AND ENCODE VENDOR_ID
    stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
    model = stringIndexer.fit(taxi_df_train_with_newFeatures) # Input data-frame is the cleaned one from above
    indexed = model.transform(taxi_df_train_with_newFeatures)
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

    # INDEX AND TRAFFIC TIME BINS
    stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
    model = stringIndexer.fit(encoded3)
    indexed = model.transform(encoded3)
    encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
    encodedFinal = encoder.transform(indexed)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**OUTPUT**

Tempo impiegato per eseguire questa cella: 3,14 secondi.

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Creare oggetti punto etichettato per l'inserimento in funzioni di apprendimento automatico
Questa sezione contiene codice che illustra come indicizzare dati di testo categorici come tipo di dati punto etichettato e codificarli per l'uso per il training e il testing della regressione logistica MLlib e di altri modelli di classificazione. Gli oggetti punto etichettato sono RDD (Resilient Distributed Dataset) formattati come richiesto per i dati di input dalla maggior parte degli algoritmi di apprendimento automatico in MLlib. Un [punto etichettato](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) è un vettore locale, che può essere denso o sparso, associato a un'etichetta o a una risposta.

Ecco il codice per indicizzare e codificare le funzionalità di testo per la classificazione binaria.

    # FUNCTIONS FOR BINARY CLASSIFICATION

    # LOAD LIBRARIES
    from pyspark.mllib.regression import LabeledPoint
    from numpy import array

    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingBinary(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.pickup_hour, line.weekday,
                             line.passenger_count, line.trip_time_in_secs, line.trip_distance, line.fare_amount])
        labPt = LabeledPoint(line.tipped, features)
        return  labPt

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC RERESSION MODELS
    def parseRowOneHotBinary(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                   line.vendorVec.toArray(), line.rateVec.toArray(), line.paymentVec.toArray()), axis=0)
        labPt = LabeledPoint(line.tipped, features)
        return  labPt


Ecco il codice per codificare e indicizzare le funzionalità di testo categoriche per l'analisi di regressione lineare.

    # FUNCTIONS FOR REGRESSION WITH TIP AMOUNT AS TARGET VARIABLE

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingRegression(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])
        labPt = LabeledPoint(line.tip_amount, features)
        return  labPt

    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
    def parseRowOneHotRegression(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        labPt = LabeledPoint(line.tip_amount, features)
        return  labPt


### <a name="create-a-random-sub-sampling-of-the-data-and-split-it-into-training-and-testing-sets"></a>Creare un sottocampionamento casuale dei dati e dividerlo in set di training e di testing
Questo codice crea un campionamento casuale dei dati, qui viene usato il 25%. Anche se non è necessario per questo esempio, date le dimensioni del set di dati, viene illustrato come eseguire il campionamento e come usarlo quando necessario. Nei campioni di grandi dimensioni questa operazione permette di risparmiare molto tempo durante il training dei modelli. Successivamente il campione viene suddiviso in un set di training (75%) e un set di testing (25%) da usare nei modelli di regressione e classificazione.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # SPECIFY SAMPLING AND SPLITTING FRACTIONS
    from pyspark.sql.functions import rand

    samplingFraction = 0.25;
    trainingFraction = 0.75; testingFraction = (1-trainingFraction);
    seed = 1234;
    encodedFinalSampled = encodedFinal.sample(False, samplingFraction, seed=seed)

    # SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST, WITH A RANDOM COLUMN ADDED FOR DOING CV (SHOWN LATER)
    # INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS
    dfTmpRand = encodedFinalSampled.select("*", rand(0).alias("rand"));
    trainData, testData = dfTmpRand.randomSplit([trainingFraction, testingFraction], seed=seed);

    # CACHE TRAIN AND TEST DATA
    trainData.cache()
    testData.cache()

    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTRAINbinary = trainData.map(parseRowIndexingBinary)
    indexedTESTbinary = testData.map(parseRowIndexingBinary)
    oneHotTRAINbinary = trainData.map(parseRowOneHotBinary)
    oneHotTESTbinary = testData.map(parseRowOneHotBinary)

    # FOR REGRESSION TRAINING AND TESTING
    indexedTRAINreg = trainData.map(parseRowIndexingRegression)
    indexedTESTreg = testData.map(parseRowIndexingRegression)
    oneHotTRAINreg = trainData.map(parseRowOneHotRegression)
    oneHotTESTreg = testData.map(parseRowOneHotRegression)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**OUTPUT**

Tempo impiegato per eseguire questa cella: 0,31 secondi.

### <a name="feature-scaling"></a>Ridimensionamento di funzionalità
Il ridimensionamento di funzionalità, noto anche come normalizzazione dei dati, permette di fare in modo che alle funzionalità con valori molto dispersi non venga attribuito un peso eccessivo nella funzione obiettivo. Per ridimensionare le funzionalità alla varianza unitaria, il relativo codice usa [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) . Viene fornito da MLlib per l'uso nella regressione lineare con la discesa del gradiente stocastica (SGD), un algoritmo molto diffuso per il training di una vasta gamma di modelli di apprendimento automatico, come la regressione regolarizzata o le macchine a vettori di supporto (SVM).   

> [!TIP]
> L'algoritmo LinearRegressionWithSGD è risultato sensibile al ridimensionamento di funzionalità.   
> 
> 

Ecco il codice per ridimensionare le variabili per l'uso con l'algoritmo SGD lineare regolarizzato.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.regression import LabeledPoint
    from pyspark.mllib.linalg import Vectors
    from pyspark.mllib.feature import StandardScaler, StandardScalerModel
    from pyspark.mllib.util import MLUtils

    # SCALE VARIABLES FOR REGULARIZED LINEAR SGD ALGORITHM
    label = oneHotTRAINreg.map(lambda x: x.label)
    features = oneHotTRAINreg.map(lambda x: x.features)
    scaler = StandardScaler(withMean=False, withStd=True).fit(features)
    dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
    oneHotTRAINregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))

    label = oneHotTESTreg.map(lambda x: x.label)
    features = oneHotTESTreg.map(lambda x: x.features)
    scaler = StandardScaler(withMean=False, withStd=True).fit(features)
    dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
    oneHotTESTregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**OUTPUT**

Tempo impiegato per eseguire questa cella: 11,67 secondi.

### <a name="cache-objects-in-memory"></a>Memorizzazione nella cache di oggetti in memoria
Il caching degli oggetti del frame di dati di input usati per la classificazione, la regressione e le funzionalità con ridimensionamento permette di ridurre il tempo impiegato per il training e il testing degli algoritmi di Machine Learning.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTRAINbinary.cache()
    indexedTESTbinary.cache()
    oneHotTRAINbinary.cache()
    oneHotTESTbinary.cache()

    # FOR REGRESSION TRAINING AND TESTING
    indexedTRAINreg.cache()
    indexedTESTreg.cache()
    oneHotTRAINreg.cache()
    oneHotTESTreg.cache()

    # SCALED FEATURES
    oneHotTRAINregScaled.cache()
    oneHotTESTregScaled.cache()

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**OUTPUT** 

Tempo impiegato per eseguire questa cella: 0,13 secondi.

## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>Uso di modelli di classificazione binaria per prevedere se viene lasciata o meno una mancia
Questa sezione illustra come usare tre modelli per l'attività di classificazione binaria di previsione di una possibile mancia lasciata per una corsa in taxi. I modelli presentati sono:

* Regressione logistica 
* Foresta casuale
* Alberi con boosting a gradienti.

Ogni sezione di codice di compilazione del modello è suddivisa in passaggi: 

1. **training del modello** con un set di parametri
2. **Valutazione del modello** su un set di dati di test con metriche
3. **Salvataggio del modello** in un BLOB per l'utilizzo in futuro

Ecco due modi per eseguire la convalida incrociata con sweep di parametri:

1. Tramite codice **generico** personalizzato che può essere applicato a qualsiasi algoritmo in MLlib e a qualsiasi set di parametri in un algoritmo. 
2. Tramite la **funzione della pipeline CrossValidator pySpark**. Si noti che anche se utile, in base all'esperienza CrossValidator presenta alcune limitazioni per Spark 1.5.0: 
   
   * I modelli di pipeline non possono essere salvati/resi persistenti per un utilizzo futuro.
   * Non può essere usato per ogni parametro in un modello.
   * Non può essere usato per ogni algoritmo MLlib.

### <a name="generic-cross-validation-and-hyperparameter-sweeping-used-with-the-logistic-regression-algorithm-for-binary-classification"></a>Convalida incrociata generica e sweep di iperparametri usati con l'algoritmo di regressione logistica per la classificazione binaria
Il codice riportato in questa sezione illustra come eseguire il training, valutare e salvare un modello di regressione logistica con l'algoritmo [L-BFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) , che consente di prevedere se viene lasciata o meno una mancia per una corsa nel set di dati relativo alle corse e tariffe dei taxi della città di New York. Il training del modello viene eseguito con convalida incrociata e sweep di iperparametri implementati con codice personalizzato, che è possibile applicare a qualsiasi algoritmo di apprendimento in MLlib.   

> [!NOTE]
> L'esecuzione del codice personalizzato di convalida incrociata può richiedere alcuni minuti.
> 
> 

**Eseguire il training del modello di regressione logistica usando la convalida incrociata e lo sweep degli iperparametri**

    # LOGISTIC REGRESSION CLASSIFICATION WITH CV AND HYPERPARAMETER SWEEPING

    # GET ACCURACY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionWithLBFGS 
    from pyspark.mllib.evaluation import BinaryClassificationMetrics

    # CREATE PARAMETER GRID FOR LOGISTIC REGRESSION PARAMETER SWEEP
    from sklearn.grid_search import ParameterGrid
    grid = [{'regParam': [0.01, 0.1], 'iterations': [5, 10], 'regType': ["l1", "l2"], 'tolerance': [1e-3, 1e-4]}]
    paramGrid = list(ParameterGrid(grid))
    numModels = len(paramGrid)

    # SET NUM FOLDS AND NUM PARAMETER SETS TO SWEEP ON
    nFolds = 3;
    h = 1.0 / nFolds;
    metricSum = np.zeros(numModels);

    # BEGIN CV WITH PARAMETER SWEEP
    for i in range(nFolds):
        # Create training and x-validation sets
        validateLB = i * h
        validateUB = (i + 1) * h
        condition = (trainData["rand"] >= validateLB) & (trainData["rand"] < validateUB)
        validation = trainData.filter(condition)
        # Create LabeledPoints from data-frames
        if i > 0:
            trainCVLabPt.unpersist()
            validationLabPt.unpersist()
        trainCV = trainData.filter(~condition)
        trainCVLabPt = trainCV.map(parseRowOneHotBinary)
        trainCVLabPt.cache()
        validationLabPt = validation.map(parseRowOneHotBinary)
        validationLabPt.cache()
        # For parameter sets compute metrics from x-validation
        for j in range(numModels):
            regt = paramGrid[j]['regType']
            regp = paramGrid[j]['regParam']
            iters = paramGrid[j]['iterations']
            tol = paramGrid[j]['tolerance']
            # Train logistic regression model with hypermarameter set
            model = LogisticRegressionWithLBFGS.train(trainCVLabPt, regType=regt, iterations=iters,  
                                                      regParam=regp, tolerance = tol, intercept=True)
            predictionAndLabels = validationLabPt.map(lambda lp: (float(model.predict(lp.features)), lp.label))
            # Use ROC-AUC as accuracy metrics
            validMetrics = BinaryClassificationMetrics(predictionAndLabels)
            metric = validMetrics.areaUnderROC
            metricSum[j] += metric

    avgAcc = metricSum / nFolds;
    bestParam = paramGrid[np.argmax(avgAcc)];

    # UNPERSIST OBJECTS
    trainCVLabPt.unpersist()
    validationLabPt.unpersist()

    # TRAIN ON FULL TRAIING SET USING BEST PARAMETERS FROM CV/PARAMETER SWEEP
    logitBest = LogisticRegressionWithLBFGS.train(oneHotTRAINbinary, regType=bestParam['regType'], 
                                                  iterations=bestParam['iterations'], 
                                                  regParam=bestParam['regParam'], tolerance = bestParam['tolerance'], 
                                                  intercept=True)


    # PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
    # NOTE: There are 20 coefficient terms for the 10 features, 
    #       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
    print("Coefficients: " + str(logitBest.weights))
    print("Intercept: " + str(logitBest.intercept))

    # PRINT ELAPSED TIME    
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**OUTPUT**

Coefficienti: [0,0082065285375, -0,0223675576104, -0,0183812028036, -3,48124578069e-05, -0,00247646947233, -0,00165897881503, 0,0675394837328, -0,111823113101, -0,324609912762, -0,204549780032, -1,36499216354, 0,591088507921, -0,664263411392, -1,00439726852, 3,46567827545, -3,51025855172, -0,0471341112232, -0,043521833294, 0,000243375810385, 0,054518719222]

Intercetta: -0,0111216486893

Tempo impiegato per eseguire questa cella: 14,43 secondi.

**Valutare il modello di classificazione binaria con le metriche standard**

Il codice in questa sezione illustra come valutare un modello di regressione logistica su un set di dati di test, incluso un tracciato della curva ROC.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    #IMPORT LIBRARIES
    from sklearn.metrics import roc_curve,auc
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.mllib.evaluation import MulticlassMetrics

    # PREDICT ON TEST DATA WITH BEST/FINAL MODEL
    predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitBest.predict(lp.features)), lp.label))

    # INSTANTIATE METRICS OBJECT
    metrics = BinaryClassificationMetrics(predictionAndLabels)

    # AREA UNDER PRECISION-RECALL CURVE
    print("Area under PR = %s" % metrics.areaUnderPR)

    # AREA UNDER ROC CURVE
    print("Area under ROC = %s" % metrics.areaUnderROC)
    metrics = MulticlassMetrics(predictionAndLabels)

    # OVERALL STATISTICS
    precision = metrics.precision()
    recall = metrics.recall()
    f1Score = metrics.fMeasure()
    print("Summary Stats")
    print("Precision = %s" % precision)
    print("Recall = %s" % recall)
    print("F1 Score = %s" % f1Score)

    # OUTPUT PROBABILITIES AND REGISTER TEMP TABLE
    logitBest.clearThreshold(); # This clears threshold for classification (0.5) and outputs probabilities
    predictionAndLabelsDF = predictionAndLabels.toDF()
    predictionAndLabelsDF.registerTempTable("tmp_results");

    # PRINT ELAPSED TIME    
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**OUTPUT**

Area in PR = 0,985336538462

Area in ROC = 0,983383274312

Statistiche di riepilogo

Precisione = 0,984174341679

Richiamo = 0,984174341679

Punteggio F1 = 0,984174341679

Tempo impiegato per eseguire questa cella: 2,67 secondi.

**Tracciare la curva ROC.**

*predictionAndLabelsDF* viene registrato come tabella, *tmp_results*, nella cella precedente. *tmp_results* può essere usato per eseguire query e risultati di output nel frame di dati sqlResults per il tracciato. Ecco il codice.

    # QUERY RESULTS                              
    %%sql -q -o sqlResults
    SELECT * from tmp_results


Ecco il codice per eseguire stime e tracciare la curva ROC.

    # MAKE PREDICTIONS AND PLOT ROC-CURVE

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES                              
    %%local
    %matplotlib inline
    from sklearn.metrics import roc_curve,auc

    #PREDICTIONS
    predictions_pddf = sqlResults.rename(columns={'_1': 'probability', '_2': 'label'})
    prob = predictions_pddf["probability"] 
    fpr, tpr, thresholds = roc_curve(predictions_pddf['label'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)

    # PLOT ROC CURVES
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


**OUTPUT**

![Curva ROC di regressione logistica per approccio generico](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/logistic-regression-roc-curve.png)

**Rendere persistente il modello in un BLOB per l'utilizzo in futuro**

Il codice in questa sezione illustra come salvare il modello di regressione logistica per l'utilizzo.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionModel

    # PERSIST MODEL
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp;
    dirfilename = modelDir + logisticregressionfilename;

    logitBest.save(sc, dirfilename);

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";


**OUTPUT**

Tempo impiegato per eseguire questa cella: 34,57 secondi.

### <a name="use-mllib's-crossvalidator-pipeline-function-with-logistic-regression-(elastic-regression)-model"></a>Usare la funzione della pipeline CrossValidator di MLlib con il modello di regressione logistica (regressione elastica)
Il codice riportato in questa sezione illustra come eseguire il training, valutare e salvare un modello di regressione logistica con l'algoritmo [L-BFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) , che consente di prevedere se viene lasciata o meno una mancia per una corsa nel set di dati relativo alle corse e tariffe dei taxi della città di New York. Il training del modello viene eseguito con convalida incrociata e sweep di iperparametri implementati con la funzione della pipeline CrossValidator MLlib per convalida incrociata con sweep di iperparametri.   

> [!NOTE]
> L'esecuzione del codice di convalida incrociata MLlib può richiedere alcuni minuti.
> 
> 

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml import Pipeline
    from pyspark.ml.evaluation import BinaryClassificationEvaluator
    from pyspark.ml.tuning import CrossValidator, ParamGridBuilder
    from sklearn.metrics import roc_curve,auc

    # DEFINE ALGORITHM / MODEL
    lr = LogisticRegression()

    # DEFINE GRID PARAMETERS
    paramGrid = ParamGridBuilder().addGrid(lr.regParam, (0.01, 0.1))\
                                  .addGrid(lr.maxIter, (5, 10))\
                                  .addGrid(lr.tol, (1e-4, 1e-5))\
                                  .addGrid(lr.elasticNetParam, (0.25,0.75))\
                                  .build()

    # DEFINE CV WITH PARAMETER SWEEP
    cv = CrossValidator(estimator= lr,
                        estimatorParamMaps=paramGrid,
                        evaluator=BinaryClassificationEvaluator(),
                        numFolds=3)

    # CONVERT TO DATA-FRAME: THIS DOES NOT RUN ON RDDs
    trainDataFrame = sqlContext.createDataFrame(oneHotTRAINbinary, ["features", "label"])

    # TRAIN WITH CROSS-VALIDATION
    cv_model = cv.fit(trainDataFrame)


    ## PREDICT AND EVALUATE ON TEST DATA-SET

    # USE TEST DATASET FOR PREDICTION
    testDataFrame = sqlContext.createDataFrame(oneHotTESTbinary, ["features", "label"])
    test_predictions = cv_model.transform(testDataFrame)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**OUTPUT**

Tempo impiegato per eseguire questa cella: 107,98 secondi.

**Tracciare la curva ROC.**

*predictionAndLabelsDF* viene registrato come tabella, *tmp_results*, nella cella precedente. *tmp_results* può essere usato per eseguire query e risultati di output nel frame di dati sqlResults per il tracciato. Ecco il codice.

    # QUERY RESULTS
    %%sql -q -o sqlResults
    SELECT label, prediction, probability from tmp_results

Ecco il codice per tracciare la curva ROC.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES 
    %%local
    from sklearn.metrics import roc_curve,auc

    # ROC CURVE
    prob = [x["values"][1] for x in sqlResults["probability"]]
    fpr, tpr, thresholds = roc_curve(sqlResults['label'], prob, pos_label=1);
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


**OUTPUT**

![Curva ROC di regressione logistica con CrossValidator di MLlib](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/mllib-crossvalidator-roc-curve.png)

### <a name="random-forest-classification"></a>Classificazione tramite foresta casuale
Il codice riportato in questa sezione illustra come eseguire il training, valutare e salvare una regressione tramite foresta casuale, che consente di prevedere se viene lasciata o meno una mancia per una corsa nel set di dati relativo alle corse e tariffe dei taxi di NYC.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.mllib.evaluation import MulticlassMetrics

    # SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

    # TRAIN RANDOMFOREST MODEL
    rfModel = RandomForest.trainClassifier(indexedTRAINbinary, numClasses=2, 
                                           categoricalFeaturesInfo=categoricalFeaturesInfo,
                                           numTrees=25, featureSubsetStrategy="auto",
                                           impurity='gini', maxDepth=5, maxBins=32)
    ## UN-COMMENT IF YOU WANT TO PRING TREES
    #print('Learned classification forest model:')
    #print(rfModel.toDebugString())

    # PREDICT ON TEST DATA AND EVALUATE
    predictions = rfModel.predict(indexedTESTbinary.map(lambda x: x.features))
    predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)

    # AREA UNDER ROC CURVE
    metrics = BinaryClassificationMetrics(predictionAndLabels)
    print("Area under ROC = %s" % metrics.areaUnderROC)

    # PERSIST MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfclassificationfilename = "RandomForestClassification_" + datestamp;
    dirfilename = modelDir + rfclassificationfilename;

    rfModel.save(sc, dirfilename);

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**OUTPUT**

Area in ROC = 0,985336538462

Tempo impiegato per eseguire questa cella: 26,72 secondi.

### <a name="gradient-boosting-trees-classification"></a>Classificazione tramite alberi con boosting a gradienti
Il codice riportato in questa sezione illustra come eseguire il training, valutare e salvare un modello di alberi con boosting a gradienti, che consente di prevedere se viene lasciata o meno una mancia per una corsa nel set di dati relativo alle corse e tariffe dei taxi di NYC.

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel

    # SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

    gbtModel = GradientBoostedTrees.trainClassifier(indexedTRAINbinary, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                    numIterations=10)
    ## UNCOMMENT IF YOU WANT TO PRINT TREE DETAILS
    #print('Learned classification GBT model:')
    #print(bgtModel.toDebugString())

    # PREDICT ON TEST DATA AND EVALUATE
    predictions = gbtModel.predict(indexedTESTbinary.map(lambda x: x.features))
    predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)

    # Area under ROC curve
    metrics = BinaryClassificationMetrics(predictionAndLabels)
    print("Area under ROC = %s" % metrics.areaUnderROC)

    # PERSIST MODEL IN A BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp;
    dirfilename = modelDir + btclassificationfilename;

    gbtModel.save(sc, dirfilename)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**OUTPUT**

Area in ROC = 0,985336538462

Tempo impiegato per eseguire questa cella: 28,13 secondi.

## <a name="predict-tip-amount-with-regression-models-(not-using-cv)"></a>Prevedere l'importo delle mance con di modelli di regressione (senza usare la convalida incrociata)
Questa sezione illustra come usare tre modelli per l'attività di regressione relativa alla previsione dell'importo della mancia lasciata per una corsa in taxi in base ad altre funzionalità relative alle mance. I modelli presentati sono:

* Regressione lineare regolarizzata
* Foresta casuale
* Alberi con boosting a gradienti.

Questi modelli sono stati descritti nell'introduzione. Ogni sezione di codice di compilazione del modello è suddivisa in passaggi: 

1. **training del modello** con un set di parametri
2. **Valutazione del modello** su un set di dati di test con metriche
3. **Salvataggio del modello** in un BLOB per l'utilizzo in futuro   

> NOTA PER AZURE: la convalida incrociata non viene usata con i tre modelli di regressione in questa sezione, in quanto è stata illustrata in dettaglio per i modelli di regressione logistica. Nell'appendice di questo argomento viene fornito un esempio che illustra come usare la convalida incrociata con Elastic Net per la regressione lineare.
> 
> NOTA PER AZURE: la convergenza di modelli LinearRegressionWithSGD può risultare problematica ed è necessario modificare oppure ottimizzare attentamente i parametri per ottenere un modello valido. Il ridimensionamento delle variabili può essere molto utile per la convergenza. Al posto di LinearRegressionWithSGD è anche possibile usare la regressione Elastic Net illustrata nell'appendice di questo argomento.
> 
> 

### <a name="linear-regression-with-sgd"></a>Regressione lineare con SGD
Il codice riportato in questa sezione illustra come usare le funzionalità con ridimensionamento per il training di una regressione lineare che usa la discesa del gradiente stocastica (SGD) per l'ottimizzazione e come assegnare punteggi, valutare e salvare il modello in BLOB di Archiviazione di Azure (WASB).

> [!TIP]
> La convergenza di modelli LinearRegressionWithSGD può risultare problematica ed è necessario modificare oppure ottimizzare attentamente i parametri per ottenere un modello valido. Il ridimensionamento delle variabili può essere molto utile per la convergenza.
> 
> 

    # LINEAR REGRESSION WITH SGD 

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD LIBRARIES
    from pyspark.mllib.regression import LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel
    from pyspark.mllib.evaluation import RegressionMetrics
    from scipy import stats

    # USE SCALED FEATURES TO TRAIN MODEL
    linearModel = LinearRegressionWithSGD.train(oneHotTRAINregScaled, iterations=100, step = 0.1, regType='l2', regParam=0.1, intercept = True)

    # PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
    # NOTE: There are 20 coefficient terms for the 10 features, 
    #       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
    print("Coefficients: " + str(linearModel.weights))
    print("Intercept: " + str(linearModel.intercept))

    # SCORE ON SCALED TEST DATA-SET & EVALUATE
    predictionAndLabels = oneHotTESTregScaled.map(lambda lp: (float(linearModel.predict(lp.features)), lp.label))
    testMetrics = RegressionMetrics(predictionAndLabels)

    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
    dirfilename = modelDir + linearregressionfilename;

    linearModel.save(sc, dirfilename)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**OUTPUT**

Coefficienti: [0,0141707753435, -0,0252930927087, -0,0231442517137, 0,247070902996, 0,312544147152, 0,360296120645, 0,0122079566092, -0,00456498588241, -0,0898228505177, 0,0714046248793, 0,102171263868, 0,100022455632, -0,00289545676449, -0,00791124681938, 0,54396316518, -0,536293513569, 0,0119076553369, -0,0173039244582, 0,0119632796147, 0,00146764882502]

Intercetta: 0,854507624459

RMSE = 1,23485131376

R-sqr = 0,597963951127

Tempo impiegato per eseguire questa cella: 38,62 secondi.

### <a name="random-forest-regression"></a>Regressione tramite foresta casuale
Il codice riportato in questa sezione illustra come eseguire il training, valutare e salvare un modello di foresta casuale, che consente di prevedere l'importo della mancia per il set di dati relativo alle corse in taxi di NYC.   

> [!NOTE]
> Nell'appendice è illustrata la convalida incrociata con sweep di parametri tramite codice personalizzato.
> 
> 

    #PREDICT TIP AMOUNTS USING RANDOM FOREST

    # RECORD START TIME
    timestart= datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import RegressionMetrics


    # TRAIN MODEL
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=25, featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=10, maxBins=32)
    # UN-COMMENT IF YOU WANT TO PRING TREES
    #print('Learned classification forest model:')
    #print(rfModel.toDebugString())

    # PREDICT AND EVALUATE ON TEST DATA-SET
    predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = oneHotTESTreg.map(lambda lp: lp.label).zip(predictions)

    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfregressionfilename = "RandomForestRegression_" + datestamp;
    dirfilename = modelDir + rfregressionfilename;

    rfModel.save(sc, dirfilename);

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**OUTPUT**

RMSE = 0,931981967875

R-sqr = 0,733445485802

Tempo impiegato per eseguire questa cella: 25,98 secondi.

### <a name="gradient-boosting-trees-regression"></a>Regressione tramite alberi con boosting a gradienti
Il codice riportato in questa sezione illustra come eseguire il training, valutare e salvare un modello di alberi con boosting a gradienti, che consente di prevedere l'importo della mancia per il set di dati relativo alle corse in taxi di NYC.

**Eseguire il training e valutare **

    #PREDICT TIP AMOUNTS USING GRADIENT BOOSTING TREES

    # RECORD START TIME
    timestart= datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
    from pyspark.mllib.util import MLUtils

    # TRAIN MODEL
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    gbtModel = GradientBoostedTrees.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo, 
                                                    numIterations=10, maxBins=32, maxDepth = 4, learningRate=0.1)

    # EVALUATE A TEST DATA-SET
    predictions = gbtModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES
    test_predictions= sqlContext.createDataFrame(predictionAndLabels)
    test_predictions_pddf = test_predictions.toPandas()

    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btregressionfilename = "GradientBoostingTreeRegression_" + datestamp;
    dirfilename = modelDir + btregressionfilename;
    gbtModel.save(sc, dirfilename)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**OUTPUT**

RMSE = 0,928172197114

R-sqr = 0,732680354389

Tempo impiegato per eseguire questa cella: 20,9 secondi.

**Grafico**

*tmp_results* viene registrato come una tabella Hive nella cella precedente. I risultati della tabella vengono restituiti nel frame di dati *sqlResults* per delineare tracciati. Ecco il codice

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT * from tmp_results


Ecco il codice per tracciare i dati usando il server Jupyter.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    import numpy as np

    # PLOT
    ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='_1', y='_2', color='blue', alpha = 0.25, label='Actual vs. predicted');
    fit = np.polyfit(sqlResults['_1'], sqlResults['_2'], deg=1)
    ax.set_title('Actual vs. Predicted Tip Amounts ($)')
    ax.set_xlabel("Actual")
    ax.set_ylabel("Predicted")
    ax.plot(sqlResults['_1'], fit[0] * sqlResults['_1'] + fit[1], color='magenta')
    plt.axis([-1, 15, -1, 15])
    plt.show(ax)

![Actual-vs-predicted-tip-amounts](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/actual-vs-predicted-tips.png)

## <a name="appendix:-additional-regression-tasks-using-cross-validation-with-parameter-sweeps"></a>Appendice: Attività di regressione aggiuntive tramite convalida incrociata con sweep di parametri
Questa appendice contiene il codice che illustra come eseguire la convalida incrociata con Elastic Net per la regressione lineare e come eseguire la convalida incrociata con sweep di parametri usando codice personalizzato per la regressione tramite foresta casuale.

### <a name="cross-validation-using-elastic-net-for-linear-regression"></a>Convalida incrociata con Elastic Net per la regressione lineare
Il codice in questa sezione illustra come eseguire la convalida incrociata usando Elastic Net per la regressione lineare e come valutare il modello rispetto a dati di test.

    ###  CV USING ELASTIC NET FOR LINEAR REGRESSION

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.regression import LinearRegression
    from pyspark.ml import Pipeline
    from pyspark.ml.evaluation import RegressionEvaluator
    from pyspark.ml.tuning import CrossValidator, ParamGridBuilder

    # DEFINE ALGORITHM/MODEL
    lr = LinearRegression()

    # DEFINE GRID PARAMETERS
    paramGrid = ParamGridBuilder().addGrid(lr.regParam, (0.01, 0.1))\
                                  .addGrid(lr.maxIter, (5, 10))\
                                  .addGrid(lr.tol, (1e-4, 1e-5))\
                                  .addGrid(lr.elasticNetParam, (0.25,0.75))\
                                  .build() 

    # DEFINE PIPELINE 
    # SIMPLY THE MODEL HERE, WITHOUT TRANSFORMATIONS
    pipeline = Pipeline(stages=[lr])

    # DEFINE CV WITH PARAMETER SWEEP
    cv = CrossValidator(estimator= lr,
                        estimatorParamMaps=paramGrid,
                        evaluator=RegressionEvaluator(),
                        numFolds=3)

    # CONVERT TO DATA FRAME, AS CROSSVALIDATOR WON'T RUN ON RDDS
    trainDataFrame = sqlContext.createDataFrame(oneHotTRAINreg, ["features", "label"])

    # TRAIN WITH CROSS-VALIDATION
    cv_model = cv.fit(trainDataFrame)


    # EVALUATE MODEL ON TEST SET
    testDataFrame = sqlContext.createDataFrame(oneHotTESTreg, ["features", "label"])

    # MAKE PREDICTIONS ON TEST DOCUMENTS
    # cvModel uses the best model found (lrModel).
    predictionAndLabels = cv_model.transform(testDataFrame)

    # CONVERT TO DF AND SAVE REGISER DF AS TABLE
    predictionAndLabels.registerTempTable("tmp_results");

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**OUTPUT**

Tempo impiegato per eseguire questa cella: 161,21 secondi.

**Eseguire la valutazione con la metrica R-SQR**

*tmp_results* viene registrato come una tabella Hive nella cella precedente. I risultati della tabella vengono restituiti nel frame di dati *sqlResults* per delineare tracciati. Ecco il codice

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT label,prediction from tmp_results


Ecco il codice per calcolare R-sqr.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    from scipy import stats

    #R-SQR TEST METRIC
    corstats = stats.linregress(sqlResults['label'],sqlResults['prediction'])
    r2 = (corstats[2]*corstats[2])
    print("R-sqr = %s" % r2)


**OUTPUT**

R-sqr = 0,619184907088

### <a name="cross-validation-with-parameter-sweep-using-custom-code-for-random-forest-regression"></a>Convalida incrociata con sweep di parametri usando codice personalizzato per la regressione tramite foresta casuale
Il codice in questa sezione illustra come eseguire la convalida incrociata con sweep di parametri usando codice personalizzato per la regressione tramite foresta casuale e come valutare il modello rispetto a dati di test.

    # RECORD START TIME
    timestart= datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    # GET ACCURARY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import RegressionMetrics
    from sklearn.grid_search import ParameterGrid

    ## CREATE PARAMETER GRID
    grid = [{'maxDepth': [5,10], 'numTrees': [25,50]}]
    paramGrid = list(ParameterGrid(grid))

    ## SPECIFY LEVELS OF CATEGORICAL VARIBLES
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

    # SPECIFY NUMFOLDS AND ARRAY TO HOLD METRICS
    nFolds = 3;
    numModels = len(paramGrid)
    h = 1.0 / nFolds;
    metricSum = np.zeros(numModels);

    for i in range(nFolds):
        # Create training and x-validation sets
        validateLB = i * h
        validateUB = (i + 1) * h
        condition = (trainData["rand"] >= validateLB) & (trainData["rand"] < validateUB)
        validation = trainData.filter(condition)
        # Create labeled points from data-frames
        if i > 0:
            trainCVLabPt.unpersist()
            validationLabPt.unpersist()
        trainCV = trainData.filter(~condition)
        trainCVLabPt = trainCV.map(parseRowIndexingRegression)
        trainCVLabPt.cache()
        validationLabPt = validation.map(parseRowIndexingRegression)
        validationLabPt.cache()
        # For parameter sets compute metrics from x-validation
        for j in range(numModels):
            maxD = paramGrid[j]['maxDepth']
            numT = paramGrid[j]['numTrees']
            # Train logistic regression model with hypermarameter set
            rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=numT, featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=maxD, maxBins=32)
            predictions = rfModel.predict(validationLabPt.map(lambda x: x.features))
            predictionAndLabels = validationLabPt.map(lambda lp: lp.label).zip(predictions)
            # Use ROC-AUC as accuracy metrics
            validMetrics = RegressionMetrics(predictionAndLabels)
            metric = validMetrics.rootMeanSquaredError
            metricSum[j] += metric

    avgAcc = metricSum/nFolds;
    bestParam = paramGrid[np.argmin(avgAcc)];

    # UNPERSIST OBJECTS
    trainCVLabPt.unpersist()
    validationLabPt.unpersist()

    ## TRAIN FINAL MODL WIHT BEST PARAMETERS
    rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=bestParam['numTrees'], featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=bestParam['maxDepth'], maxBins=32)

    # EVALUATE MODEL ON TEST DATA
    predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

    #PRINT TEST METRICS
    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**OUTPUT**

RMSE = 0,906972198262

R-sqr = 0,740751197012

Tempo impiegato per eseguire questa cella: 69,17 secondi.

### <a name="clean-up-objects-from-memory-and-print-model-locations"></a>Pulire gli oggetti dalla memoria e stampare i percorsi dei modelli
Usare `unpersist()` per eliminare gli oggetti memorizzati nella cache.

    # UNPERSIST OBJECTS CACHED IN MEMORY

    # REMOVE ORIGINAL DFs
    taxi_df_train_cleaned.unpersist()
    taxi_df_train_with_newFeatures.unpersist()
    trainData.unpersist()
    trainData.unpersist()

    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTRAINbinary.unpersist()
    indexedTESTbinary.unpersist()
    oneHotTRAINbinary.unpersist()
    oneHotTESTbinary.unpersist()

    # FOR REGRESSION TRAINING AND TESTING
    indexedTRAINreg.unpersist()
    indexedTESTreg.unpersist()
    oneHotTRAINreg.unpersist()
    oneHotTESTreg.unpersist()

    # SCALED FEATURES
    oneHotTRAINregScaled.unpersist()
    oneHotTESTregScaled.unpersist()


**OUTPUT**

PythonRDD[122] at RDD at PythonRDD.scala: 43

**Stampare il percorso dei file di modello da usare nel notebook sul consumo. ** Per il consumo e l'assegnazione dei punteggi di un set di dati indipendente, è necessario copiare e incollare questi nomi di file nel "notebook sul consumo".

    # PRINT MODEL FILE LOCATIONS FOR CONSUMPTION
    print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
    print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
    print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
    print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
    print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
    print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";


**OUTPUT**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05-0316_47_30.096528"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05-0316_51_28.433670"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05-0316_50_17.454440"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05-0316_51_57.331730"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05-0316_50_40.138809"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05-0316_52_18.827237"

## <a name="what's-next?"></a>Passaggi successivi
Dopo aver creato i modelli regressivi e di classificazione con MlLib di Spark, è possibile imparare a valutare e assegnare punteggi a questi modelli.

**Uso dei modelli:** per informazioni su come valutare e assegnare punteggi ai modelli di regressione e di classificazione creati in questo argomento, vedere [Assegnare punteggi a modelli di apprendimento automatico compilati con Spark](machine-learning-data-science-spark-model-consumption.md).

<!--HONumber=Oct16_HO2-->


