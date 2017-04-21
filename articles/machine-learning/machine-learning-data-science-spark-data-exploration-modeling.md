---
title: Modellazione ed esplorazione dei dati con Spark | Documentazione Microsoft
description: "Illustra le funzionalità di modellazione ed esplorazione dei dati del toolkit MLlib di Spark su Azure."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: b989b918-5ba5-4696-b8d0-76ae510a23f4
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2017
ms.author: deguhath;bradsev;gokuma
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 711407f7dd9e6d442e3f04a23962487f4808e8e2
ms.lasthandoff: 03/17/2017


---
# <a name="data-exploration-and-modeling-with-spark"></a>Modellazione ed esplorazione dei dati con Spark
[!INCLUDE [machine-learning-spark-modeling](../../includes/machine-learning-spark-modeling.md)]

Questa procedura dettagliata usa HDInsight Spark per eseguire l'esplorazione dei dati e attività di classificazione binaria e modellazione basata sulla regressione su un campione del set di dati relativo alle corse e alle tariffe taxi della città di New York nel 2013.  Illustra i passaggi end-to-end del [processo di analisi scientifica dei dati](http://aka.ms/datascienceprocess)usando un cluster HDInsight Spark per l'elaborazione e BLOB di Azure per l'archiviazione dei dati e dei modelli. Il processo analizza e visualizza i dati ottenuti da un BLOB di Archiviazione di Azure e li prepara per la compilazione di modelli predittivi. I modelli vengono compilati con il toolkit MLlib di Spark per l'esecuzione di attività di classificazione binaria e modellazione basata sulla regressione.

* L'attività di **classificazione binaria** consente di prevedere se per la corsa viene lasciata o meno una mancia. 
* L'attività di **regressione** consente di prevedere l'importo della mancia in base ad altre funzionalità relative alle mance. 

I modelli proposti includono la regressione logistica e lineare, foreste casuali e alberi con boosting a gradienti:

* [Regressione lineare con SGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) è un modello di regressione lineare che si serve di un metodo di discesa del gradiente stocastico (SGD, Stochastic Gradient Descent), usato per l'ottimizzazione e il ridimensionamento delle funzionalità allo scopo di prevedere l'importo delle mance pagate. 
* [Regressione logistica con L-BFGS](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) , o regressione "logit", è un modello di regressione che può essere usato quando la variabile dipendente usata per la classificazione dei dati è categoriale. L'algoritmo L-BFGS è un algoritmo di ottimizzazione quasi-Newton che approssima l'algoritmo di Broyden-Fletcher-Goldfarb-Shanno (BFGS) usando una quantità limitata di memoria del computer ed è ampiamente usato nell'apprendimento automatico.
* [foreste casuali](http://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) sono insiemi di alberi delle decisioni.  Queste foreste combinano diversi alberi delle decisioni per ridurre il rischio di overfitting. Le foreste casuali vengono usate per la classificazione e la regressione, sono in grado di gestire funzionalità relative alle categorie e possono essere estese all'impostazione di classificazione multiclasse. Non richiedono il ridimensionamento delle funzionalità e possono rilevare non linearità e interazioni di funzionalità. Le foreste casuali sono tra i modelli di apprendimento automatico più diffusi per la classificazione e la regressione.
* [Alberi con boosting a gradienti](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBT, Gradient boosted tree) sono insiemi di alberi delle decisioni. Gli alberi GBT eseguono il training degli alberi delle decisioni in modo iterativo per ridurre al minimo la perdita di funzioni. Gli alberi GBT vengono usati per la classificazione e la regressione e possono gestire funzionalità categoriche, non richiedono il ridimensionamento delle funzionalità e possono rilevare non linearità e interazioni di funzionalità. Possono anche essere usati in un'impostazione di classificazione multiclasse.

La procedura di modellazione include anche codice che illustra come eseguire il training, la valutazione e il salvataggio di ogni tipo di modello. Per il codice della soluzione e per visualizzare i relativi tracciati è stato usato Python.   

> [!NOTE]
> Anche se il toolkit Spark MLlib è progettato per funzionare con set di dati di grandi dimensioni, per maggiore praticità viene usato un campione relativamente ridotto, di circa 30 Mb con 170.000 righe, ovvero lo 0,1% circa del set di dati originale della città di New York. L'esercizio qui proposto viene eseguito in modo efficiente in un cluster HDInsight con 2 nodi di lavoro, in circa 10 minuti. Lo stesso codice può essere usato per elaborare set di dati di dimensioni maggiori, con poche modifiche appropriate per il caching dei dati in memoria e la modifica delle dimensioni del cluster.
> 
> 

## <a name="prerequisites"></a>Prerequisiti
Per completare questa procedura dettagliata sono necessari un account Azure e un cluster HDInsight Spark 1.6 o 2.0. Per istruzioni su come soddisfare questi requisiti, vedere [Panoramica dell'analisi scientifica dei dati con Spark in Azure HDInsight](machine-learning-data-science-spark-overview.md). Questo argomento contiene inoltre una descrizione dei dati dei taxi di NYC per il 2013 usati qui e istruzioni su come eseguire il codice da un notebook Jupyter nel cluster Spark. 

## <a name="spark-clusters-and-notebooks"></a>Notebook e cluster Spark
La procedura di installazione e il codice forniti in questa procedura dettagliata sono per l'uso di un HDInsight Spark 1.6. Ma vengono forniti i notebook di Jupyter per i cluster HDInsight sia Spark 1.6 sia Spark 2.0. +Vengono inoltre forniti una descrizione dei notebook e i relativi collegamenti nel [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) per il repository GitHub che li contiene. Inoltre, il codice in questo esempio e nei notebook collegati è generico e funzionerà in qualsiasi cluster Spark. Se non si usa HDInsight Spark, i passaggi di configurazione e gestione del cluster possono essere leggermente diversi rispetto a quanto illustrato qui. Per praticità, ecco i collegamenti per i notebook di Jupyter per Spark 1.6 (da eseguire nel kernek di pySpark del server Notebook di Jupyter) e Spark 2.0 (da eseguire nel kernel di pySpark3 del server Notebook di Jupyter):

### <a name="spark-16-notebooks"></a>Notebook Spark 1.6

[pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb): fornisce informazioni su come eseguire l'esplorazione dei dati, la modellazione e l'assegnazione del punteggio con diversi algoritmi.

### <a name="spark-20-notebooks"></a>Notebook Spark 2.0
Le attività di classificazione e regressione implementate con un cluster Spark 2.0 si trovano in notebook diversi e il notebook di classificazione usa un dataset diverso:

- [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): questo file fornisce informazioni su come eseguire l'esplorazione dei dati, la modellazione e l'assegnazione del punteggio nei cluster Spark 2.0 usando i dati relativi alle corse e alle tariffe dei taxi di NYC descritti [qui](https://docs.microsoft.com/en-us/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data). Questo notebook può essere un buon punto di partenza per esplorare velocemente il codice fornito per Spark 2.0. Per un notebook più dettagliato che analizza i dati dei taxi di NYC, vedere il notebook successivo di questo elenco. Vedere le note dopo l'elenco che confrontano questi notebook. 
- [Spark2.0-pySpark3_NYC_Taxi_Tip_Regression.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_NYC_Taxi_Tip_Regression.ipynb): questo file mostra come eseguire la gestione dei dati (Spark SQL e operazioni del frame di dati), l'esplorazione, il modellamento e l'assegnazione del punteggio utilizzando il set di dati delle corse e delle tariffe dei taxi di NYC descritti [qui](https://docs.microsoft.com/en-us/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data).
- [Spark2.0-pySpark3_Airline_Departure_Delay_Classification.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_Airline_Departure_Delay_Classification.ipynb): questo file mostra come eseguire la gestione dei dati (Spark SQL e operazioni del frame di dati), l'esplorazione, il modellamento e l'assegnazione del punteggio utilizzando il set di dati relativi alle partenze puntuali dei voli del 2011 e 2012 di una compagnia area. Abbiamo integrato il set di dati della compagnia aerea con i dati delle condizioni atmosferiche dell'aeroporto (ad esempio, velocità del vento, temperatura, altitudine e così via) prima del modellamento, in modo da poterli includere nel modello.

<!-- -->

> [!NOTE]
> Il set di dati della compagnia aerea è stato aggiunto ai notebook Spark 2.0 per illustrare al meglio l'uso degli algoritmi di classificazione. Vedere i collegamenti seguenti per informazioni sul set di dati delle partenze puntuali dei voli della compagnia aerea e sul set di dati delle condizioni atmosferiche:

>- Dati sulle partenze puntuali dei voli della compagnia aerea: [http://www.transtats.bts.gov/ONTIME/](http://www.transtats.bts.gov/ONTIME/)

>- Dati sulle condizioni atmosferiche dell'aeroporto: [https://www.ncdc.noaa.gov/](https://www.ncdc.noaa.gov/) 
> 
> 

<!-- -->

<!-- -->

> [!NOTE]
L'esecuzione dei notebook Spark 2.0 sui dati dei taxi di NYC e sui ritardi dei voli può impiegare 10 minuti o più (in base alla dimensione del cluster HDI). Il primo notebook dell'elenco precedente illustra molti aspetti dell'esplorazione dei dati, della visualizzazione e dell'addestramento del modello ML in un notebook che richiede meno tempo di esecuzione con dataset di NYC ricampionati, in cui i file dei dati di taxi e tariffe sono stati precedentemente uniti: [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb) Questo notebook termina in un tempo molto più breve (2-3 minuti) e può essere un buon punto di partenza per esplorare rapidamente il codice fornito per Spark 2.0. 

<!-- -->

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

<!-- -->

> [!NOTE]
Le descrizioni riportate di seguito riguardano l'uso di Spark 1.6. Per le versioni Spark 2.0 usare i notebook le cui descrizioni e collegamenti sono riportati sopra. 

<!-- -->

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Configurazione: percorsi di archiviazione, librerie e contesto Spark preimpostato
Spark può eseguire operazioni di lettura e scrittura in BLOB di Archiviazione di Azure (WASB). I dati esistenti archiviati in WASB possono essere elaborati con Spark e i relativi risultati possono essere memorizzati nuovamente in BLOB di Archiviazione di Azure.

Per salvare file o modelli in WASB, è necessario specificare correttamente il percorso. È possibile fare riferimento al contenitore predefinito collegato al cluster Spark usando un percorso che inizia con: "wasb///". "wasb://" fa riferimento ad altri percorsi.

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Impostare percorsi di directory per i percorsi di archiviazione in WASB
L'esempio di codice seguente specifica il percorso dei dati da leggere e il percorso della directory di archiviazione del modello in cui viene salvato l'output del modello:

    # SET PATHS TO FILE LOCATIONS: DATA AND MODEL STORAGE

    # LOCATION OF TRAINING DATA
    taxi_train_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv";

    # SET THE MODEL STORAGE DIRECTORY PATH 
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 


### <a name="import-libraries"></a>Importare le librerie
Per la configurazione occorre anche importare le librerie necessarie. Impostare il contesto Spark e importare le librerie necessarie usando il codice seguente:

    # IMPORT LIBRARIES
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
I kernel PySpark forniti con i notebook di Jupyter hanno un contesto preimpostato, quindi non è necessario impostare esplicitamente i contesti Spark o Hive prima di iniziare a usare l'applicazione in fase di sviluppo. Questi contesti sono disponibili per impostazione predefinita. Questi contesti sono:

* sc per Spark 
* sqlContext per Hive

Il kernel PySpark offre alcuni “magic” predefiniti, ovvero comandi speciali che è possibile chiamare con %%. Negli esempi di codice seguenti sono usati due comandi di questo tipo.

* **%%local**: specifica che il codice presente nelle righe successive deve essere eseguito localmente. Deve trattarsi di codice Python valido.
* **%%sql -o <variable name>**: esegue una query Hive su sqlContext. Se viene passato il parametro -o, il risultato della query viene salvato in modo permanente nel contesto Python %%local come frame di dati Pandas.

Per altre informazioni sui kernel per i notebook di Jupyter e i "magic" predefiniti messi a disposizione, vedere [Kernels available for Jupyter notebooks with HDInsight Spark Linux clusters on HDInsight](../hdinsight/hdinsight-apache-spark-jupyter-notebook-kernels.md) (Kernel disponibili per i notebook di Jupyter con cluster Apache Spark in HDInsight Linux).

## <a name="data-ingestion-from-public-blob"></a>Inserimento di dati dal BLOB pubblico
Il primo passaggio del processo di analisi scientifica dei dati consiste nel prelevare i dati da analizzare dalle origini in cui si trovano e inserirli nell'ambiente di modellazione ed esplorazione dei dati. In questa procedura dettagliata l'ambiente è Spark. Questa sezione contiene il codice per completare una serie di attività:

* Inserimento del campione di dati da modellare.
* Lettura del set di dati di input archiviato come file TSV.
* Formattazione e pulizia dei dati.
* Creazione di oggetti, RDD o frame di dati, e memorizzazione nella cache in memoria.
* Registrazione come tabella temporanea in un contesto SQL.

Di seguito è riportato il codice per l'inserimento di dati.

    # INGEST DATA

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


    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    taxi_df_train_cleaned.cache()
    taxi_df_train_cleaned.count()

    # REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
    taxi_df_train_cleaned.registerTempTable("taxi_train")

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**OUTPUT:**

Tempo impiegato per eseguire questa cella: 51,72 secondi

## <a name="data-exploration--visualization"></a>Visualizzazione ed esplorazione dei dati
Dopo aver inserito i dati in Spark, il passaggio successivo del processo di analisi scientifica dei dati consiste nell'esplorazione e nella visualizzazione dei dati per approfondirne la conoscenza. In questa sezione vengono esaminati i dati relativi ai taxi tramite query SQL e vengono tracciate le variabili di destinazione e le funzionalità potenziali per l'esame visivo. In particolare, viene tracciata la frequenza del numero di passeggeri nelle corse dei taxi, la frequenza dell'importo delle mance e la variazione delle mance in base al tipo e all'importo del pagamento.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Tracciare un istogramma delle frequenze del numero di passeggeri nel campione di corse dei taxi
Questo codice e i frammenti di codice successivi usano un magic SQL per eseguire una query sul campione e un magic local per tracciare i dati.

* **Magic SQL (`%%sql`)**: il kernel HDInsight PySpark supporta l'esecuzione di query HiveQL inline semplici su sqlContext. L'argomento (-o NOME_VARIABILE) salva in modo permanente l'output della query SQL come frame di dati Pandas nel server Jupyter. Questo significa che è disponibile in modalità locale.
* Il **`%%local`** viene usato per eseguire il codice in locale nel server Jupyter, che costituisce il nodo head del cluster HDInsight. In genere si usa il magic `%%local` in combinazione con il magic `%%sql` con il parametro -o. Il parametro -o rende persistente l'output della query SQL a livello locale e quindi il magic %%local attiva il successivo set di frammenti di codice che viene eseguito localmente a fronte dell'output della query SQL persistente a livello locale.

L'output viene visualizzato automaticamente dopo aver eseguito il codice.

Questa query recupera le corse per numero di passeggeri. 

    # PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

    # HIVEQL QUERY AGAINST THE sqlContext
    %%sql -q -o sqlResults
    SELECT passenger_count, COUNT(*) as trip_counts 
    FROM taxi_train 
    WHERE passenger_count > 0 and passenger_count < 7 
    GROUP BY passenger_count 

Questo codice crea un frame di dati locale dall'output della query ed esegue il tracciato dei dati. Il magic `%%local` crea un frame di dati locale, `sqlResults`, che può essere usato per eseguire tracciati con matplotlib. 

> [!NOTE]
> Tale magic PySpark viene usato più volte in questa procedura dettagliata. In caso di un'elevata quantità di dati, è consigliabile campionare i dati in modo da creare un frame che possa essere contenuto nella memoria locale.
> 
> 

    #CREATE LOCAL DATA-FRAME AND USE FOR MATPLOTLIB PLOTTING

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
    # CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
    sqlResults

Ecco il codice per eseguire il tracciato delle corse per numero di passeggeri

    # PLOT PASSENGER NUMBER VS. TRIP COUNTS
    %%local
    import matplotlib.pyplot as plt
    %matplotlib inline

    x_labels = sqlResults['passenger_count'].values
    fig = sqlResults[['trip_counts']].plot(kind='bar', facecolor='lightblue')
    fig.set_xticklabels(x_labels)
    fig.set_title('Counts of trips by passenger count')
    fig.set_xlabel('Passenger count in trips')
    fig.set_ylabel('Trip counts')
    plt.show()

**OUTPUT:**

![Frequenza delle corse per numero di passeggeri](./media/machine-learning-data-science-spark-data-exploration-modeling/trip-freqency-by-passenger-count.png)

È possibile scegliere tra diversi tipi di visualizzazioni (tabella, a torta, a linee, ad area o a barre) usando i pulsanti del menu **Type** (Tipo) nel notebook. In questo caso è stato scelto un tracciato a barre.

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Tracciare un istogramma dell'importo delle mance e della relativa variazione in base al numero di passeggeri e all'importo delle corse.
Usare una query SQL per campionare i dati.

    #PLOT HISTOGRAM OF TIP AMOUNTS AND VARIATION BY PASSENGER COUNT AND PAYMENT TYPE

    # HIVEQL QUERY AGAINST THE sqlContext
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

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # HISTOGRAM OF TIP AMOUNTS AND PASSENGER COUNT
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
    plt.axis([-2, 100, -2, 20])
    plt.show()


**OUTPUT:** 

![Distribuzione dell'importo delle mance](./media/machine-learning-data-science-spark-data-exploration-modeling/tip-amount-distribution.png)

![Importo della mancia per numero di passeggeri](./media/machine-learning-data-science-spark-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Importo della mancia per importo della corsa](./media/machine-learning-data-science-spark-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>Progettazione di funzionalità, trasformazione e preparazione dei dati per la modellazione
Questa sezione descrive le procedure usate per preparare i dati da usare nella modellazione per l'apprendimento automatico, fornisce il relativo codice e illustra come eseguire queste attività:

* Ottenere una nuova funzionalità dalla creazione di contenitori per gli orari di trasporto
* Indicizzare e codificare funzionalità categoriche
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

**OUTPUT:** 

126050

### <a name="index-and-encode-categorical-features-for-input-into-modeling-functions"></a>Indicizzare e codificare funzionalità categoriche per l'inserimento in funzioni di modellazione
Questa sezione illustra come indicizzare o codificare le funzionalità categoriche per l'inserimento nelle funzioni di modellazione. Per poter usare le funzioni di modellazione e previsione di MLlib, è necessario prima indicizzare o codificare le funzionalità con dati di input categorici. A seconda del modello, è necessario indicizzare o codificare tali funzioni in modi diversi:  

* **Modelli basati su albero**: richiedono la codifica delle categorie come valori numerici (ad esempio, una funzionalità con tre categorie può essere codificata con 0, 1, 2). Questo avviene tramite la funzione [StringIndexer](http://spark.apache.org/docs/latest/ml-features.html#stringindexer) di MLlib, che codifica una colonna stringa di etichette in una colonna di indici etichetta ordinati in base alle frequenze di etichetta. Anche se viene usata l'indicizzazione con valori numerici per l'input e la gestione dei dati, è possibile specificare gli algoritmi basati su albero affinché trattino questi elementi in modo appropriato come categorie. 
* I **modelli logistici e di regressione lineare** richiedono la codifica one-hot in cui, ad esempio, una funzionalità con tre categorie può essere espansa in tre colonne delle funzionalità, in cui ogni colonna contiene 0 o 1, a seconda della categoria di un'osservazione. Per eseguire la codifica one-hot in MLlib è disponibile la funzione [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder). Questo codificatore esegue il mapping di una colonna di indici etichetta a una colonna di vettori binari, con al massimo un singolo valore unico. Questa codifica permette di applicare a funzionalità categoriche gli algoritmi che prevedono funzionalità con valori numerici, ad esempio la regressione logistica.

Di seguito è riportato il codice per l'indicizzazione e la codifica delle funzionalità categoriche:

    # INDEX AND ENCODE CATEGORICAL FEATURES

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES    
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, VectorIndexer

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

**OUTPUT:**

Tempo impiegato per eseguire questa cella: 1,28 secondi.

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Creare oggetti punto etichettato per l'inserimento in funzioni di apprendimento automatico
Questa sezione contiene codice che illustra come indicizzare dati di testo categorici come tipo di dati punto etichettato e codificarli per l'uso per il training e il testing della regressione logistica MLlib e di altri modelli di classificazione. Gli oggetti punto etichettato sono RDD (Resilient Distributed Dataset) formattati come richiesto per i dati di input dalla maggior parte degli algoritmi di apprendimento automatico in MLlib. Un [punto etichettato](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) è un vettore locale, che può essere denso o sparso, associato a un'etichetta o a una risposta.  

Questa sezione contiene un codice che illustra come indicizzare dati di testo categorici come dati di tipo [punto etichettato](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) e codificarli per l'uso a scopo di training e testing della regressione logistica MLlib e di altri modelli di classificazione. Gli oggetti punto etichettato RDD (Resilient Distributed Dataset) costituiti da un'etichetta, o variabile di destinazione/risposta, e da un vettore di funzionalità. Questo formato è richiesto come input dalla maggior parte degli algoritmi di apprendimento automatico in MLlib.

Ecco il codice per indicizzare e codificare le funzionalità di testo per la classificazione binaria.

    # FUNCTIONS FOR BINARY CLASSIFICATION

    # LOAD LIBRARIES
    from pyspark.mllib.regression import LabeledPoint
    from numpy import array

    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingBinary(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex,
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])
        labPt = LabeledPoint(line.tipped, features)
        return  labPt

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC RERESSION MODELS
    def parseRowOneHotBinary(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
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

    # LOAD PYSPARK LIBRARIES
    from pyspark.sql.functions import rand

    # SPECIFY SAMPLING AND SPLITTING FRACTIONS
    samplingFraction = 0.25;
    trainingFraction = 0.75; testingFraction = (1-trainingFraction);
    seed = 1234;
    encodedFinalSampled = encodedFinal.sample(False, samplingFraction, seed=seed)

    # SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST
    # INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS (FOR USE LATER IN AN ADVANCED TOPIC)
    dfTmpRand = encodedFinalSampled.select("*", rand(0).alias("rand"));
    trainData, testData = dfTmpRand.randomSplit([trainingFraction, testingFraction], seed=seed);

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

**OUTPUT:**

Tempo impiegato per eseguire questa cella: 0,24 secondi.

### <a name="feature-scaling"></a>Ridimensionamento di funzionalità
Il ridimensionamento di funzionalità, noto anche come normalizzazione dei dati, permette di fare in modo che alle funzionalità con valori molto dispersi non venga attribuito un peso eccessivo nella funzione obiettivo. Per ridimensionare le funzionalità alla varianza unitaria, il relativo codice usa [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) . Viene fornito da MLlib per l'uso nella regressione lineare con la discesa del gradiente stocastica (SGD), un algoritmo molto diffuso per il training di una vasta gamma di modelli di apprendimento automatico, come la regressione regolarizzata o le macchine a vettori di supporto (SVM).

> [!NOTE]
> L'algoritmo LinearRegressionWithSGD è risultato sensibile al ridimensionamento di funzionalità.
> 
> 

Ecco il codice per ridimensionare le variabili per l'uso con l'algoritmo SGD lineare regolarizzato.

    # FEATURE SCALING

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

**OUTPUT:**

Tempo impiegato per eseguire questa cella: 13,17 secondi.

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

**OUTPUT:** 

Tempo impiegato per eseguire questa cella: 0,15 secondi.

## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>Uso di modelli di classificazione binaria per prevedere se viene lasciata o meno una mancia
Questa sezione illustra come usare tre modelli per l'attività di classificazione binaria di previsione di una possibile mancia lasciata per una corsa in taxi. I modelli presentati sono:

* Regressione logistica regolarizzata. 
* Modello di foresta casuale.
* Alberi con boosting a gradienti.

Ogni sezione di codice di compilazione del modello è suddivisa in passaggi: 

1. **training del modello** con un set di parametri
2. **Valutazione del modello** su un set di dati di test con metriche
3. **Salvataggio del modello** in un BLOB per l'uso in futuro

### <a name="classification-using-logistic-regression"></a>Classificazione tramite regressione logistica
Il codice riportato in questa sezione illustra come eseguire il training, valutare e salvare un modello di regressione logistica con l'algoritmo [L-BFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) , che consente di prevedere se viene lasciata o meno una mancia per una corsa nel set di dati relativo alle corse e tariffe dei taxi della città di New York.

**Eseguire il training del modello di regressione logistica usando la convalida incrociata e lo sweep degli iperparametri**

    # LOGISTIC REGRESSION CLASSIFICATION WITH CV AND HYPERPARAMETER SWEEPING

    # GET ACCURACY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionWithLBFGS 
    from sklearn.metrics import roc_curve,auc
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.mllib.evaluation import MulticlassMetrics


    # CREATE MODEL WITH ONE SET OF PARAMETERS
    logitModel = LogisticRegressionWithLBFGS.train(oneHotTRAINbinary, iterations=20, initialWeights=None, 
                                                   regParam=0.01, regType='l2', intercept=True, corrections=10, 
                                                   tolerance=0.0001, validateData=True, numClasses=2)

    # PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
    # NOTE: There are 20 coefficient terms for the 10 features, 
    #       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
    print("Coefficients: " + str(logitModel.weights))
    print("Intercept: " + str(logitModel.intercept))

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**OUTPUT:** 

Coefficienti: [0.0082065285375, -0.0223675576104, -0.0183812028036, -3.48124578069e-05, -0.00247646947233, -0.00165897881503, 0.0675394837328, -0.111823113101, -0.324609912762, -0.204549780032, -1.36499216354, 0.591088507921, -0.664263411392, -1.00439726852, 3.46567827545, -3.51025855172, -0.0471341112232, -0.043521833294, 0.000243375810385, 0.054518719222]

Intercetta: -0,0111216486893

Tempo impiegato per eseguire questa cella: 14,43 secondi.

**Valutare il modello di classificazione binaria con le metriche standard**

    #EVALUATE LOGISTIC REGRESSION MODEL WITH LBFGS

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # PREDICT ON TEST DATA WITH MODEL
    predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitModel.predict(lp.features)), lp.label))

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


    ## SAVE MODEL WITH DATE-STAMP
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp;
    dirfilename = modelDir + logisticregressionfilename;
    logitModel.save(sc, dirfilename);

    # OUTPUT PROBABILITIES AND REGISTER TEMP TABLE
    logitModel.clearThreshold(); # This clears threshold for classification (0.5) and outputs probabilities
    predictionAndLabelsDF = predictionAndLabels.toDF()
    predictionAndLabelsDF.registerTempTable("tmp_results");

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**OUTPUT:** 

Area in PR = 0,985297691373

Area in ROC = 0,983714670256

Statistiche di riepilogo

Precisione = 0,984304060189

Richiamo = 0,984304060189

Punteggio F1 = 0,984304060189

Tempo impiegato per eseguire questa cella: 57,61 secondi.

**Tracciare la curva ROC.**

L'oggetto *predictionAndLabelsDF* viene registrato come tabella, *tmp_results*, nella cella precedente. L'oggetto *tmp_results* può essere usato per eseguire query e restituire i risultati nel frame di dati sqlResults per il tracciamento. Ecco il codice.

    # QUERY RESULTS                              
    %%sql -q -o sqlResults
    SELECT * from tmp_results


Ecco il codice per eseguire stime e tracciare la curva ROC.

    # MAKE PREDICTIONS AND PLOT ROC-CURVE

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    %matplotlib inline
    from sklearn.metrics import roc_curve,auc

    # MAKE PREDICTIONS
    predictions_pddf = test_predictions.rename(columns={'_1': 'probability', '_2': 'label'})
    prob = predictions_pddf["probability"] 
    fpr, tpr, thresholds = roc_curve(predictions_pddf['label'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)

    # PLOT ROC CURVE
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

![Logistic regression ROC curve.png](./media/machine-learning-data-science-spark-data-exploration-modeling/logistic-regression-roc-curve.png)

### <a name="random-forest-classification"></a>Classificazione tramite foresta casuale
Il codice riportato in questa sezione illustra come eseguire il training, la valutazione e il salvataggio di un modello di foresta casuale, che consente di prevedere se viene lasciata o meno una mancia per una corsa nel set di dati relativo a corse e tariffe dei taxi della città di New York.

    #PREDICT WHETHER A TIP IS PAID OR NOT USING RANDOM FOREST

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
    ## UN-COMMENT IF YOU WANT TO PRINT TREES
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

**OUTPUT:**

Area in ROC = 0,985297691373

Tempo impiegato per eseguire questa cella: 31,09 secondi.

### <a name="gradient-boosting-trees-classification"></a>Classificazione tramite alberi con boosting a gradienti
Il codice riportato in questa sezione illustra come eseguire il training, la valutazione e il salvataggio di un modello di alberi con boosting a gradienti, che consente di prevedere se viene lasciata o meno una mancia per una corsa nel set di dati relativo a corse e tariffe dei taxi della città di New York.

    #PREDICT WHETHER A TIP IS PAID OR NOT USING GRADIENT BOOSTING TREES

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel

    # SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

    gbtModel = GradientBoostedTrees.trainClassifier(indexedTRAINbinary, categoricalFeaturesInfo=categoricalFeaturesInfo, numIterations=5)
    ## UNCOMMENT IF YOU WANT TO PRINT TREE DETAILS
    #print('Learned classification GBT model:')
    #print(bgtModel.toDebugString())

    # PREDICT ON TEST DATA AND EVALUATE
    predictions = gbtModel.predict(indexedTESTbinary.map(lambda x: x.features))
    predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)

    # AREA UNDER ROC CURVE
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


**OUTPUT:**

Area in ROC = 0,985297691373

Tempo impiegato per eseguire questa cella: 19,76 secondi.

## <a name="predict-tip-amounts-for-taxi-trips-with-regression-models"></a>Uso di modelli regressivi per prevedere l'importo delle mance per le corse in taxi
Questa sezione illustra come usare tre modelli per l'attività di regressione relativa alla previsione dell'importo della mancia lasciata per una corsa in taxi in base ad altre funzionalità relative alle mance. I modelli presentati sono:

* Regressione lineare regolarizzata
* Foresta casuale
* Alberi con boosting a gradienti.

Questi modelli sono stati descritti nell'introduzione. Ogni sezione di codice di compilazione del modello è suddivisa in passaggi: 

1. **training del modello** con un set di parametri
2. **Valutazione del modello** su un set di dati di test con metriche
3. **Salvataggio del modello** in un BLOB per l'uso in futuro

### <a name="linear-regression-with-sgd"></a>Regressione lineare con SGD
Il codice riportato in questa sezione illustra come usare le funzionalità con ridimensionamento per il training di una regressione lineare che usa la discesa del gradiente stocastica (SGD) per l'ottimizzazione e come assegnare punteggi, valutare e salvare il modello in BLOB di Archiviazione di Azure (WASB).

> [!TIP]
> La convergenza di modelli LinearRegressionWithSGD può risultare problematica ed è necessario modificare oppure ottimizzare attentamente i parametri per ottenere un modello valido. Il ridimensionamento delle variabili può essere molto utile per la convergenza. 
> 
> 

    #PREDICT TIP AMOUNTS USING LINEAR REGRESSION WITH SGD

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

    # PRINT TEST METRICS
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # SAVE MODEL WITH DATE-STAMP IN THE DEFAULT BLOB FOR THE CLUSTER
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
    dirfilename = modelDir + linearregressionfilename;

    linearModel.save(sc, dirfilename)

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**OUTPUT:**

Coefficienti: [0.00457675809917, -0.0226314167349, -0.0191910355236, 0.246793409578, 0.312047890459, 0.359634405999, 0.00928692253981, -0.000987181489428, -0.0888306617845, 0.0569376211553, 0.115519551711, 0.149250164995, -0.00990211159703, -0.00637410344522, 0.545083566179, -0.536756072402, 0.0105762393099, -0.0130117577055, 0.0129304737772, -0.00171065945959]

Intercetta: -0,853872718283

RMSE = 1,24190115863

R-sqr = 0,608017146081

Tempo impiegato per eseguire questa cella: 58,42 secondi.

### <a name="random-forest-regression"></a>Regressione tramite foresta casuale
Il codice riportato in questa sezione illustra come eseguire il training, la valutazione e il salvataggio di una regressione tramite foresta casuale, che consente di prevedere l'importo della mancia per i dati relativi alle corse in taxi della città di New York.

    #PREDICT TIP AMOUNTS USING RANDOM FOREST

    # RECORD START TIME
    timestart= datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import RegressionMetrics


    ## TRAIN MODEL
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=25, featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=10, maxBins=32)
    ## UN-COMMENT IF YOU WANT TO PRING TREES
    #print('Learned classification forest model:')
    #print(rfModel.toDebugString())

    ## PREDICT AND EVALUATE ON TEST DATA-SET
    predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = oneHotTESTreg.map(lambda lp: lp.label).zip(predictions)

    # TEST METRICS
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

**OUTPUT:**

RMSE = 0,891209218139

R-sqr = 0,759661334921

Tempo impiegato per eseguire questa cella: 49,21 secondi.

### <a name="gradient-boosting-trees-regression"></a>Regressione tramite alberi con boosting a gradienti
Il codice riportato in questa sezione illustra come eseguire il training, valutare e salvare un modello di alberi con boosting a gradienti, che consente di prevedere l'importo della mancia per il set di dati relativo alle corse in taxi di NYC.

**Eseguire il training e valutare**

    #PREDICT TIP AMOUNTS USING GRADIENT BOOSTING TREES

    # RECORD START TIME
    timestart= datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
    from pyspark.mllib.util import MLUtils

    ## TRAIN MODEL
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    gbtModel = GradientBoostedTrees.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo, 
                                                    numIterations=10, maxBins=32, maxDepth = 4, learningRate=0.1)

    ## EVALUATE A TEST DATA-SET
    predictions = gbtModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

    # TEST METRICS
    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btregressionfilename = "GradientBoostingTreeRegression_" + datestamp;
    dirfilename = modelDir + btregressionfilename;
    gbtModel.save(sc, dirfilename)

    # CONVER RESULTS TO DF AND REGISER TEMP TABLE
    test_predictions = sqlContext.createDataFrame(predictionAndLabels)
    test_predictions.registerTempTable("tmp_results");

    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**OUTPUT:**

RMSE = 0,908473148639

R-sqr = 0,753835096681

Tempo impiegato per eseguire questa cella: 34,52 secondi.

**Grafico**

L'oggetto *tmp_results* viene registrato come tabella Hive nella cella precedente. I risultati della tabella vengono restituiti nel frame di dati *sqlResults* per il tracciamento. Ecco il codice

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT * from tmp_results

Ecco il codice per tracciare i dati usando il server Jupyter.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    %matplotlib inline
    import numpy as np

    # PLOT 
    ax = test_predictions_pddf.plot(kind='scatter', figsize = (6,6), x='_1', y='_2', color='blue', alpha = 0.25, label='Actual vs. predicted');
    fit = np.polyfit(test_predictions_pddf['_1'], test_predictions_pddf['_2'], deg=1)
    ax.set_title('Actual vs. Predicted Tip Amounts ($)')
    ax.set_xlabel("Actual")
    ax.set_ylabel("Predicted")
    ax.plot(test_predictions_pddf['_1'], fit[0] * test_predictions_pddf['_1'] + fit[1], color='magenta')
    plt.axis([-1, 20, -1, 20])
    plt.show(ax)


**OUTPUT:**

![Actual-vs-predicted-tip-amounts](./media/machine-learning-data-science-spark-data-exploration-modeling/actual-vs-predicted-tips.png)

## <a name="clean-up-objects-from-memory"></a>Pulire gli oggetti dalla memoria
Usare `unpersist()` per eliminare gli oggetti memorizzati nella cache.

    # REMOVE ORIGINAL DFs
    taxi_df_train_cleaned.unpersist()
    taxi_df_train_with_newFeatures.unpersist()

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


## <a name="record-storage-locations-of-the-models-for-consumption-and-scoring"></a>Registrazione dei percorsi di archiviazione dei modelli per l'utilizzo e l'assegnazione di punteggi
Per l'assegnazione di punteggi e l'utilizzo di un set di dati indipendente descritto nell'argomento [Assegnare punteggi a modelli di apprendimento automatico compilati con Spark](machine-learning-data-science-spark-model-consumption.md), è necessario copiare e incollare questi nomi di file che contengono i modelli salvati, creati qui nel notebook di Jupyter. Di seguito è riportato il codice per stampare i percorsi dei file di modello necessari.

    # MODEL FILE LOCATIONS FOR CONSUMPTION
    print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
    print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
    print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
    print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
    print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
    print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";


**OUTPUT**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05-0317_03_23.516568"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05-0317_05_21.577773"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05-0317_04_11.950206"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05-0317_06_08.723736"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05-0317_04_36.346583"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05-0317_06_51.737282"

## <a name="whats-next"></a>Passaggi successivi
Dopo aver creato i modelli regressivi e di classificazione con MlLib di Spark, è possibile imparare a valutare e assegnare punteggi a questi modelli. Il notebook per la modellazione e l'esplorazione avanzata dei dati approfondisce la convalida incrociata, lo sweep degli iperparametri e la valutazione del modello. 

**Uso dei modelli:** per informazioni su come valutare e assegnare punteggi ai modelli di regressione e di classificazione creati in questo argomento, vedere [Assegnare punteggi a modelli di apprendimento automatico compilati con Spark](machine-learning-data-science-spark-model-consumption.md).

**Convalida incrociata e sweep di iperparametri**: vedere [Esplorazione e modellazione avanzate dei dati con Spark](machine-learning-data-science-spark-advanced-data-exploration-modeling.md) per informazioni su come istruire i modelli sulla convalida incrociata e lo sweep di iperparametri


