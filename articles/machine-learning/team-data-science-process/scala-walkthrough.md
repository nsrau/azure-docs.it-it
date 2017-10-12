---
title: Analisi scientifica dei dati tramite Scala e Spark in Azure | Documentazione Microsoft
description: "Come usare Scala per attività di Machine Learning con supervisione con la libreria MLlib scalabile per Spark e pacchetti Spark ML in un cluster Spark di Azure HDInsight."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: a7c97153-583e-48fe-b301-365123db3780
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev;deguhath
ms.openlocfilehash: 8f1d9ab5186684c4aac806ace4ebfd38ca1fb306
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="data-science-using-scala-and-spark-on-azure"></a>Analisi scientifica dei dati tramite Scala e Spark in Azure
Questo articolo illustra come usare Scala per attività di Machine Learning con supervisione con la libreria MLlib scalabile per Spark e pacchetti Spark ML in un cluster Spark di Azure HDInsight. Vengono illustrate le attività che costituiscono il [processo di analisi scientifica dei dati](http://aka.ms/datascienceprocess), ovvero l'inserimento e l'esplorazione dei dati, la visualizzazione, la progettazione, la modellazione e l'uso dei modelli. I modelli in questo articolo includono la regressione logistica e lineare, foreste casuali e alberi con boosting a gradienti (GBT), oltre a due attività comuni di Machine Learning supervisionato:

* Problema di regressione: stima dell'importo della mancia ($) per una corsa in taxi.
* Classificazione binaria: stima di mancia o non mancia (1/0) per una corsa in taxi

Il processo di modellazione richiede il training e la valutazione su un set di dati di test e le metriche di precisione pertinenti. Questo articolo illustra come archiviare questi modelli nell'archiviazione BLOB di Azure e come classificare e valutare le relative prestazioni predittive. Questo articolo descrive anche gli argomenti più avanzati su come ottimizzare i modelli tramite la convalida incrociata e lo sweep degli iperparametri. Come dati di esempio sono stati presi i set di dati relativi alle corse e alle tariffe dei taxi di New York nel 2013, disponibili su GitHub.

[Scala](http://www.scala-lang.org/)è un linguaggio basato sulla macchina virtuale Java che integra i concetti di linguaggio orientato a oggetti e funzionale. È un linguaggio scalabile particolarmente adatto per l'elaborazione distribuita nel cloud e viene eseguito in cluster Spark di Azure.

[Spark](http://spark.apache.org/) è un framework open source di elaborazione parallela che supporta l'elaborazione in memoria per migliorare le prestazioni delle applicazioni analitiche di Big Data. Il motore di elaborazione Spark è costruito per la velocità, la semplicità d'uso e le analisi sofisticate. Le funzionalità di elaborazione distribuita in memoria rendono Spark uno strumento valido per l'esecuzione di algoritmi iterativi in calcoli grafici e di Machine Learning. Il pacchetto [spark.ml](http://spark.apache.org/docs/latest/ml-guide.html) offre un set uniforme di API di alto livello basate su frame di dati che consentono di creare e ottimizzare pipeline pratiche di Machine Learning. [MLlib](http://spark.apache.org/mllib/) è la libreria scalabile per il Machine Learning di Spark che introduce funzionalità di modellazione nell'ambiente distribuito.

[HDInsight Spark](../../hdinsight/hdinsight-apache-spark-overview.md) è la soluzione ospitata in Azure di Spark open source. Include anche il supporto per istanze di Jupyter Scala Notebook nel cluster Spark ed è in grado di eseguire query Spark SQL interattive per trasformare, filtrare e visualizzare dati archiviati nell'archiviazione BLOB di Azure. I frammenti di codice Scala presentati in questo articolo che consentono di creare le soluzioni e offrono i tracciati pertinenti per la visualizzazione dei dati sono eseguiti in istanze di Jupyter Notebook installate in cluster Spark. La procedura di modellazione riportata in questi argomenti include anche codice che illustra come eseguire il training, valutare, salvare e usare ogni tipo di modello.

La procedura e il codice di installazione riportati in questo articolo si riferiscono ad Azure HDInsight 3.4 Spark 1.6. Tuttavia, il codice in questo esempio e in [Jupyter Scala Notebook](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration%20Modeling%20and%20Scoring%20using%20Scala.ipynb) è generico e funzionerà in qualsiasi cluster Spark. Se non si usa HDInsight Spark, i passaggi di configurazione e gestione del cluster potrebbero essere leggermente diversi rispetto a quanto illustrato in questo articolo.

> [!NOTE]
> Per un argomento che illustra come usare Python anziché Scala per completare le attività per un processo di analisi scientifica dei dati end-to-end, vedere [Panoramica dell'analisi scientifica dei dati con Spark in Azure HDInsight](spark-overview.md).
> 
> 

## <a name="prerequisites"></a>Prerequisiti
* È necessario disporre di una sottoscrizione di Azure. Se non è già disponibile, [ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* È necessario un cluster Azure HDInsight 3.4 Spark 1.6 per completare le procedure seguenti. Per crearne un cluster, vedere le istruzioni fornite in [Introduzione: creare cluster Apache Spark in Azure HDInsight](../../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md). Impostare il tipo e la versione del cluster nel menu **Selezionare il tipo di cluster** .

![Configurazione del tipo di cluster HDInsight](./media/scala-walkthrough/spark-cluster-on-portal.png)

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
> 
> 

Per una descrizione dei dati relativi alle corse dei taxi della città di New York e istruzioni su come eseguire codice da un'istanza di Jupyter Notebook nel cluster Spark, vedere le sezioni pertinenti di [Panoramica dell'analisi scientifica dei dati con Spark in Azure HDInsight](spark-overview.md).  

## <a name="execute-scala-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Eseguire il codice Scala da un'istanza di Jupyter Notebook nel cluster Spark
È possibile avviare un notebook di Jupyter dal portale di Azure. Trovare il cluster Spark nel dashboard e fare clic su di esso per inserire la pagina di gestione del cluster. Fare quindi clic su **Dashboard cluster** e su **Notebook di Jupyter** per aprire il notebook associato al cluster Spark.

![Dashboard del cluster e notebook Jupyter](./media/scala-walkthrough/spark-jupyter-on-portal.png)

È anche possibile accedere ai notebook di Jupyter da https://&lt;clustername&gt;.azurehdinsight.net/jupyter. Sostituire *clustername* con il nome del cluster. Sarà necessaria la password dell'account amministratore per accedere al notebook di Jupyter.

![Accedere ai notebook Jupyter usando il nome del cluster](./media/scala-walkthrough/spark-jupyter-notebook.png)

Selezionare **Scala** per visualizzare una directory che contiene alcuni esempi di notebook preassemblati che usano l'API PySpark. Le funzioni di modellazione dell'esplorazione e assegnazione del punteggio tramite il notebook Scala.ipynb che contiene gli esempi di codice per il gruppo di argomenti Spark è disponibile in [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Scala).

È possibile caricare il notebook direttamente da GitHub nel server Jupyter Notebook del cluster Spark. Nella home page di Jupyter, fare clic sul pulsante **Upload** (Carica). In Esplora file incollare l'URL di GitHub (contenuto non elaborato) del notebook di Scala e fare clic su **Open**(Apri). Il notebook di Scala è disponibile all'URL seguente:

[Exploration-Modeling-and-Scoring-using-Scala.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb)

## <a name="setup-preset-spark-and-hive-contexts-spark-magics-and-spark-libraries"></a>Installazione: contesti di Spark e Hive predefiniti, magic Spark e librerie Spark
### <a name="preset-spark-and-hive-contexts"></a>Contesti predefiniti di Spark e Hive
    # SET THE START TIME
    import java.util.Calendar
    val beginningTime = Calendar.getInstance().getTime()


I kernel Spark forniti con i notebook Jupyter dispongono di contesti predefiniti. Non è necessario impostare i contesti Spark o Hive prima di iniziare a usare l'applicazione in fase di sviluppo. I contesti predefiniti sono:

* `sc` per SparkContext
* `sqlContext` per HiveContext

### <a name="spark-magics"></a>Magic di Spark
Il kernel Spark offre alcuni "magic" predefiniti, ovvero comandi speciali che è possibile chiamare con `%%`. Due di questi comandi vengono usati negli esempi di codice seguenti.

* `%%local` : specifica che il codice presente nelle righe successive verrà eseguito localmente. Deve trattarsi di codice Scala valido.
* `%%sql -o <variable name>`: esegue una query Hive su `sqlContext`. Se viene passato il parametro `-o`, il risultato della query viene salvato in modo permanente nel contesto Scala `%%local` come frame di dati Spark.

Per altre informazioni sui kernel per i notebook Jupyter e i "magic" predefiniti chiamati con `%%`, ad esempio `%%local`, vedere [Kernel disponibili per i notebook di Jupyter con cluster Apache Spark in HDInsight Linux](../../hdinsight/hdinsight-apache-spark-jupyter-notebook-kernels.md).

### <a name="import-libraries"></a>Importare le librerie
Importare la libreria Spark, MLlib e altre librerie necessarie tramite il codice seguente.

    # IMPORT SPARK AND JAVA LIBRARIES
    import org.apache.spark.sql.SQLContext
    import org.apache.spark.sql.functions._
    import java.text.SimpleDateFormat
    import java.util.Calendar
    import sqlContext.implicits._
    import org.apache.spark.sql.Row

    # IMPORT SPARK SQL FUNCTIONS
    import org.apache.spark.sql.types.{StructType, StructField, StringType, IntegerType, FloatType, DoubleType}
    import org.apache.spark.sql.functions.rand

    # IMPORT SPARK ML FUNCTIONS
    import org.apache.spark.ml.Pipeline
    import org.apache.spark.ml.feature.{StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer, Binarizer}
    import org.apache.spark.ml.tuning.{ParamGridBuilder, TrainValidationSplit, CrossValidator}
    import org.apache.spark.ml.regression.{LinearRegression, LinearRegressionModel, RandomForestRegressor, RandomForestRegressionModel, GBTRegressor, GBTRegressionModel}
    import org.apache.spark.ml.classification.{LogisticRegression, LogisticRegressionModel, RandomForestClassifier, RandomForestClassificationModel, GBTClassifier, GBTClassificationModel}
    import org.apache.spark.ml.evaluation.{BinaryClassificationEvaluator, RegressionEvaluator, MulticlassClassificationEvaluator}

    # IMPORT SPARK MLLIB FUNCTIONS
    import org.apache.spark.mllib.linalg.{Vector, Vectors}
    import org.apache.spark.mllib.util.MLUtils
    import org.apache.spark.mllib.classification.{LogisticRegressionWithLBFGS, LogisticRegressionModel}
    import org.apache.spark.mllib.regression.{LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel}
    import org.apache.spark.mllib.tree.{GradientBoostedTrees, RandomForest}
    import org.apache.spark.mllib.tree.configuration.BoostingStrategy
    import org.apache.spark.mllib.tree.model.{GradientBoostedTreesModel, RandomForestModel, Predict}
    import org.apache.spark.mllib.evaluation.{BinaryClassificationMetrics, MulticlassMetrics, RegressionMetrics}

    # SPECIFY SQLCONTEXT
    val sqlContext = new SQLContext(sc)


## <a name="data-ingestion"></a>Inserimento di dati
Il primo passaggio nel processo di analisi scientifica dei dati è inserire i dati da analizzare. Spostare i dati dalle origini o dai sistemi in cui si trovano nell'ambiente di esplorazione e modellazione dei dati. In questo articolo, i dati inseriti rappresentano un campione unito in join pari allo 0,1% del file con estensione tsv relativo alle corse e alle tariffe dei taxi. L'ambiente di modellazione ed esplorazione dei dati è Spark. Questa sezione contiene il codice per completare la serie di attività seguenti:

1. Impostare i percorsi di directory per l'archiviazione di dati e modelli.
2. Leggere il set di dati di input archiviato come file con estensione tsv.
3. Definire uno schema per i dati e pulire i dati.
4. Creare un frame di dati puliti e memorizzarli nella cache.
5. Registrare i dati come tabella temporanea in SQLContext.
6. Eseguire query sulla tabella e importare i risultati in un frame di dati.

### <a name="set-directory-paths-for-storage-locations-in-azure-blob-storage"></a>Impostare percorsi di directory per i percorsi di archiviazione in archiviazione BLOB di Azure
Spark può leggere e scrivere nell'archiviazione BLOB di Azure. È possibile usare Spark per elaborare i dati esistenti e quindi archiviare i risultati nuovamente nell'archiviazione BLOB.

Per salvare i modelli o i file nell'archiviazione BLOB, è necessario specificare in modo corretto il percorso. Fare riferimento al contenitore predefinito collegato al cluster Spark usando un percorso che inizia con: `wasb:///`. Fare riferimento ad altre posizioni usando `wasb://`.

L'esempio di codice seguente specifica il percorso dei dati di input da leggere e il percorso dell'archiviazione BLOB di Azure collegata al cluster Spark in cui verrà salvato il modello.

    # SET PATHS TO DATA AND MODEL FILE LOCATIONS
    # INGEST DATA AND SPECIFY HEADERS FOR COLUMNS
    val taxi_train_file = sc.textFile("wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv")
    val header = taxi_train_file.first;

    # SET THE MODEL STORAGE DIRECTORY PATH
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS REQUIRED.
    val modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";


### <a name="import-data-create-an-rdd-and-define-a-data-frame-according-to-the-schema"></a>Importare i dati, creare RDD e definire un frame di dati in base allo schema
    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # DEFINE THE SCHEMA BASED ON THE HEADER OF THE FILE
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

    # CAST VARIABLES ACCORDING TO THE SCHEMA
    val taxi_temp = (taxi_train_file.map(_.split("\t"))
                            .filter((r) => r(0) != "medallion")
                            .map(p => Row(p(0), p(1), p(2),
                                p(3).toDouble, p(4), p(5), p(6), p(7).toDouble, p(8).toDouble, p(9).toDouble, p(10).toDouble,
                                p(11).toDouble, p(12).toDouble, p(13).toDouble, p(14).toDouble, p(15).toDouble, p(16).toDouble,
                                p(17), p(18), p(19).toDouble, p(20).toDouble, p(21).toDouble, p(22).toDouble,
                                p(23).toDouble, p(24).toDouble, p(25).toDouble, p(26).toDouble)))


    # CREATE AN INITIAL DATA FRAME AND DROP COLUMNS, AND THEN CREATE A CLEANED DATA FRAME BY FILTERING FOR UNWANTED VALUES OR OUTLIERS
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

    # CACHE AND MATERIALIZE THE CLEANED DATA FRAME IN MEMORY
    taxi_df_train_cleaned.cache()
    taxi_df_train_cleaned.count()

    # REGISTER THE DATA FRAME AS A TEMPORARY TABLE IN SQLCONTEXT
    taxi_df_train_cleaned.registerTempTable("taxi_train")

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Output:**

Tempo di esecuzione della cella: 8 secondi.

### <a name="query-the-table-and-import-results-in-a-data-frame"></a>Eseguire query sulla tabella e importare i risultati in un frame di dati
Successivamente, eseguire query sulla tabella per i dati relativi alle tariffe, ai passeggeri e alle mance, filtrare i dati danneggiati e non pertinenti e stampare più righe.

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

**Output:**

| fare_amount | passenger_count | tip_amount | tipped |
| --- | --- | --- | --- |
|        13,5 |1.0 |2,9 |1.0 |
|        16,0 |2.0 |3.4 |1.0 |
|        10,5 |2.0 |1.0 |1.0 |

## <a name="data-exploration-and-visualization"></a>Visualizzazione ed esplorazione dei dati
Dopo aver inserito i dati in Spark, il passaggio successivo del processo di analisi scientifica dei dati consiste nell'esplorazione e nella visualizzazione dei dati per approfondirne la conoscenza. In questa sezione verranno esaminati i dati dei taxi tramite query SQL. Vengono quindi importati i risultati in un frame di dati per tracciare le variabili di destinazione e le funzionalità potenziali per l'esame visivo tramite la funzionalità di visualizzazione automatica di Jupyter.

### <a name="use-local-and-sql-magic-to-plot-data"></a>Usare magic local e SQL per tracciare i dati
Per impostazione predefinita, l'output di ogni frammento di codice eseguito da un'istanza di Jupyter Notebook è disponibile all'interno del contesto della sessione persistente nei nodi del ruolo di lavoro. Se si vuole salvare una corsa nei nodi del ruolo di lavoro per ogni calcolo e se tutti i dati necessari per il calcolo sono disponibili localmente nel nodo server Jupyter, ovvero il nodo head, è possibile usare il magic `%%local` per eseguire il frammento di codice nel server Jupyter.

* **Magic SQL** (`%%sql`). Il kernel HDInsight Spark supporta l'esecuzione di query HiveQL inline semplici su SQLContext. L'argomento (`-o VARIABLE_NAME`) rende persistente l'output della query SQL come un frame di dati Pandas nel server Jupyter. Questo significa che sarà disponibile in modalità locale.
* `%%local` **magic**. Il magic `%%local` esegue il codice in locale nel server Jupyter, che costituisce il nodo head del cluster HDInsight. In genere si usa il magic `%%local` in combinazione con il magic `%%sql` con il parametro `-o`. Il parametro `-o` rende persistente l'output della query SQL a livello locale e quindi il magic `%%local` attiva il successivo set di frammenti di codice che viene eseguito localmente a fronte dell'output della query SQL persistente a livello locale.

### <a name="query-the-data-by-using-sql"></a>Eseguire query sui dati tramite SQL
Questa query recupera le corse dei taxi per importo della tariffa, numero di passeggeri e importo della mancia.

    # RUN THE SQL QUERY
    %%sql -q -o sqlResults
    SELECT fare_amount, passenger_count, tip_amount, tipped FROM taxi_train WHERE passenger_count > 0 AND passenger_count < 7 AND fare_amount > 0 AND fare_amount < 200 AND payment_type in ('CSH', 'CRD') AND tip_amount > 0 AND tip_amount < 25

Nel codice seguente, il magic `%%local` crea un frame di dati locali, sqlResults. È possibile usare sqlResults per tracciare i dati tramite matplotlib.

> [!TIP]
> Il magic local viene usato più volte in questo articolo. In caso di un'elevata quantità di dati, campionare i dati per creare un frame che possa essere contenuto nella memoria locale.
> 
> 

### <a name="plot-the-data"></a>Tracciare i dati
È possibile tracciare i dati usando codice Python quando il frame di dati si trova nel contesto locale come frame di dati Pandas.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES.
    # CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, ETC.)
    sqlResults


 Il kernel Spark visualizza automaticamente l'output delle query SQL (HiveQL) dopo aver eseguito il codice. È possibile scegliere tra diversi tipi di visualizzazioni:

* Tabella
* Grafico a torta
* Grafico a linee
* Area
* Grafico a barre

Di seguito è indicato il codice per tracciare i dati:

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    import matplotlib.pyplot as plt
    %matplotlib inline

    # PLOT TIP BY PAYMENT TYPE AND PASSENGER COUNT
    ax1 = sqlResults[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
    ax1.set_title('Tip amount distribution')
    ax1.set_xlabel('Tip Amount ($)')
    ax1.set_ylabel('Counts')
    plt.suptitle('')
    plt.show()

    # PLOT TIP BY PASSENGER COUNT
    ax2 = sqlResults.boxplot(column=['tip_amount'], by=['passenger_count'])
    ax2.set_title('Tip amount by Passenger count')
    ax2.set_xlabel('Passenger count')
    ax2.set_ylabel('Tip Amount ($)')
    plt.suptitle('')
    plt.show()

    # PLOT TIP AMOUNT BY FARE AMOUNT; SCALE POINTS BY PASSENGER COUNT
    ax = sqlResults.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(sqlResults.passenger_count))
    ax.set_title('Tip amount by Fare amount')
    ax.set_xlabel('Fare Amount ($)')
    ax.set_ylabel('Tip Amount ($)')
    plt.axis([-2, 80, -2, 20])
    plt.show()


**Output:**

![Istogramma degli importi delle mance](./media/scala-walkthrough/plot-tip-amount-histogram.png)

![Importo della mancia per numero di passeggeri](./media/scala-walkthrough/plot-tip-amount-by-passenger-count.png)

![Importo della mancia per importo della corsa](./media/scala-walkthrough/plot-tip-amount-by-fare-amount.png)

## <a name="create-features-and-transform-features-and-then-prep-data-for-input-into-modeling-functions"></a>Creare le funzionalità e trasformare le funzionalità, quindi preparare i dati per l'input in funzioni di modellazione
Per le funzioni di modellazione basate su albero da Spark ML e MLlib, è necessario preparare funzionalità e destinazione con una serie di tecniche, ad esempio creazione di contenitori, indicizzazione, codifica one-hot e vettorializzazione. Di seguito sono le indicate le procedure da seguire in questa sezione:

1. Ottenere una nuova funzionalità dalla **creazione di contenitori** per gli orari di trasporto.
2. Applicare **indicizzazione e la codifica one-hot** alle funzionalità categoriche.
3. **Campionare e dividere il set di dati** in frazioni di training e di test.
4. **Specificare le funzionalità e la variabile di training**e creare RDD (resilient distributed dataset )o frame di dati con punti etichettati per training e test indicizzati o con codifica one-hot.
5. **Classificare e vettorializzare automaticamente funzionalità e destinazioni** per l'uso come input per i modelli di Machine Learning.

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Ottenere una nuova funzionalità dalla creazione di contenitori per gli orari di trasporto
Questo codice illustra come ottenere una nuova funzionalità dalla creazione di contenitori per gli orari di trasporto e come memorizzare nella cache il frame di dati risultante in memoria. Quando si usano ripetutamente RDD e frame di dati, la memorizzazione nella cache consente di migliorare i tempi di esecuzione. Di conseguenza, gli RDD e i frame di dati vengono quindi memorizzati nella cache tramite le procedure seguenti.

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

    # CACHE THE DATA FRAME IN MEMORY AND MATERIALIZE THE DATA FRAME IN MEMORY
    taxi_df_train_with_newFeatures.cache()
    taxi_df_train_with_newFeatures.count()


### <a name="indexing-and-one-hot-encoding-of-categorical-features"></a>Indicizzare e applicare la codifica one-hot per le funzionalità categoriche
Per poter usare le funzioni di modellazione e previsione di MLlib, è necessario prima indicizzare o codificare le funzionalità con dati di input categorici. Questa sezione illustra come indicizzare o codificare le funzionalità categoriche per l'inserimento nelle funzioni di modellazione.

È necessario indicizzare o codificare i modelli in modi diversi, a seconda del modello. Ad esempio, i modelli di regressione logistica e lineare richiedono codifica one-hot. Ad esempio, una funzionalità con tre categorie può essere espansa in tre colonne di funzionalità. Ogni colonna contiene 0 o 1 funzionalità, a seconda della categoria di un'osservazione. Per eseguire la codifica one-hot in MLlib è disponibile la funzione [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) . Questo codificatore esegue il mapping di una colonna di indici etichetta a una colonna di vettori binari, con al massimo un singolo valore unico. Questa codifica permette di applicare a funzionalità categoriche gli algoritmi che prevedono funzionalità con valori numerici, ad esempio la regressione logistica.

In questo caso vengono trasformate solo quattro variabili per illustrare esempi che sono stringhe di caratteri. È possibile indicizzare come variabili categoriche anche altre variabili, ad esempio il giorno della settimana, rappresentate da valori numerici.

Per l'indicizzazione usare la funzione `StringIndexer()` e per la codifica one-hot e le funzioni `OneHotEncoder()` di MLlib. Di seguito è riportato il codice per l'indicizzazione e la codifica delle funzionalità categoriche:

    # CREATE INDEXES AND ONE-HOT ENCODED VECTORS FOR SEVERAL CATEGORICAL FEATURES

    # RECORD THE START TIME
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

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Output:**

Tempo di esecuzione della cella: 4 secondi.

### <a name="sample-and-split-the-data-set-into-training-and-test-fractions"></a>Campionare e dividere il set di dati in frazioni di training e di test
Questo codice crea un campionamento casuale dei dati, in questo esempio il 25%. Anche se il campionamento non è necessario per questo esempio, date le dimensioni del set di dati, questo articolo illustra come eseguire il campionamento e come usarlo quando necessario. Nei campioni di grandi dimensioni questa operazione permette di risparmiare molto tempo durante il training dei modelli. Successivamente il campione viene suddiviso in un set di training (75% in questo esempio) e un set di testing (25% in questo esempio) da usare nei modelli di regressione e classificazione.

Aggiungere un numero casuale compreso tra 0 e 1 per ogni riga di una colonna "rand" che può essere usata per selezionare riduzioni di convalida incrociata durante il training.

    # RECORD THE START TIME
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

    # ADD A RANDOM NUMBER FOR CROSS-VALIDATION
    val encodedFinalSampled = encodedFinalSampledTmp.withColumn("rand", generateRandomDouble());

    # SPLIT THE SAMPLED DATA FRAME INTO TRAIN AND TEST, WITH A RANDOM COLUMN ADDED FOR DOING CROSS-VALIDATION (SHOWN LATER)
    # INCLUDE A RANDOM COLUMN FOR CREATING CROSS-VALIDATION FOLDS
    val splits = encodedFinalSampled.randomSplit(Array(trainingFraction, testingFraction), seed = seed)
    val trainData = splits(0)
    val testData = splits(1)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Output:**

Tempo di esecuzione della cella: 2 secondi.

### <a name="specify-training-variable-and-features-and-then-create-indexed-or-one-hot-encoded-training-and-testing-input-labeled-point-rdds-or-data-frames"></a>Specificare le funzionalità e la variabile di training e quindi creare RDD o frame di dati con punti etichettati per training e test indicizzati o con codifica one-hot.
Questa sezione contiene codice che illustra come indicizzare dati di testo categorici come tipo di dati punto etichettato e codificarli per l'uso per il training e il testing della regressione logistica MLlib e di altri modelli di classificazione. Gli oggetti punto etichettato sono RDD formattati come richiesto per i dati di input dalla maggior parte degli algoritmi di Machine Learning in MLlib. Un [punto etichettato](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) è un vettore locale, che può essere denso o sparso, associato a un'etichetta o a una risposta.

In questo codice, specificare la variabile di destinazione (dipendente) e le funzionalità da usare per eseguire il training dei modelli. Creare quindi RDD o frame di dati con punti etichettati per training e test indicizzati o con codifica one-hot.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # MAP NAMES OF FEATURES AND TARGETS FOR CLASSIFICATION AND REGRESSION PROBLEMS
    val featuresIndOneHot = List("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))
    val featuresIndIndex = List("paymentIndex", "vendorIndex", "rateIndex", "TrafficTimeBinsIndex", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))

    # SPECIFY THE TARGET FOR CLASSIFICATION ('tipped') AND REGRESSION ('tip_amount') PROBLEMS
    val targetIndBinary = List("tipped").map(encodedFinalSampled.columns.indexOf(_))
    val targetIndRegression = List("tip_amount").map(encodedFinalSampled.columns.indexOf(_))

    # CREATE INDEXED LABELED POINT RDD OBJECTS
    val indexedTRAINbinary = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
    val indexedTESTbinary = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
    val indexedTRAINreg = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
    val indexedTESTreg = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))

    # CREATE INDEXED DATA FRAMES THAT YOU CAN USE TO TRAIN BY USING SPARK ML FUNCTIONS
    val indexedTRAINbinaryDF = indexedTRAINbinary.toDF()
    val indexedTESTbinaryDF = indexedTESTbinary.toDF()
    val indexedTRAINregDF = indexedTRAINreg.toDF()
    val indexedTESTregDF = indexedTESTreg.toDF()

    # CREATE ONE-HOT ENCODED (VECTORIZED) DATA FRAMES THAT YOU CAN USE TO TRAIN BY USING SPARK ML FUNCTIONS
    val assemblerOneHot = new VectorAssembler().setInputCols(Array("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount")).setOutputCol("features")
    val OneHotTRAIN = assemblerOneHot.transform(trainData)
    val OneHotTEST = assemblerOneHot.transform(testData)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Output:**

Tempo di esecuzione della cella: 4 secondi.

### <a name="automatically-categorize-and-vectorize-features-and-targets-to-use-as-inputs-for-machine-learning-models"></a>Classificare e vettorializzare automaticamente funzionalità e destinazioni per l'uso come input per i modelli di Machine Learning
Usare Spark ML per classificare la destinazione e le funzionalità da usare nelle funzioni di modellazione basate su albero. Il codice esegue due attività:

* Crea una destinazione binaria per la classificazione assegnando un valore pari a 0 o 1 a ogni punto dati compreso tra 0 e 1 tramite un valore soglia di 0,5.
* Classifica automaticamente le funzionalità. Se il numero di valori numerici distinti per una qualsiasi funzionalità è inferiore a 32, verrà classificata tale funzionalità.

Di seguito è riportato il codice per queste due attività.

    # CATEGORIZE FEATURES AND BINARIZE THE TARGET FOR THE BINARY CLASSIFICATION PROBLEM

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

    # CATEGORIZE FEATURES FOR THE REGRESSION PROBLEM
    # CREATE PROPERLY INDEXED AND CATEGORIZED DATA FRAMES FOR TREE-BASED MODELS

    # TRAIN DATA
    val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
    val indexerModel = indexer.fit(indexedTRAINregDF)
    val indexedTRAINwithCatFeat = indexerModel.transform(indexedTRAINregDF)

    # TEST DATA
    val indexerModel = indexer.fit(indexedTESTbinaryDF)
    val indexedTESTwithCatFeat = indexerModel.transform(indexedTESTregDF)



## <a name="binary-classification-model-predict-whether-a-tip-should-be-paid"></a>Modello di classificazione binaria: prevede se deve essere lasciata una mancia
In questa sezione vengono creati tre tipi di modelli di classificazione binaria per prevedere se deve essere lasciata o meno una mancia:

* Un **modello di regressione logistica** usando la funzione `LogisticRegression()` di Spark ML
* Un **modello di classificazione di foresta casuale** usando la funzione `RandomForestClassifier()` di Spark ML
* Un **modello di classificazione con alberi con boosting a gradienti** usando la funzione `GradientBoostedTrees()` di MLlib

### <a name="create-a-logistic-regression-model"></a>Creare un modello di regressione logistica
Creare quindi un modello di regressione logistica usando la funzione `LogisticRegression()` di Spark ML. La creazione del codice di compilazione del modello prevede una serie di passaggi:

1. **Training dei dati del modello** con un set di parametri.
2. **Valutazione del modello** su un set di dati di test con metriche.
3. **Salvataggio del modello** in un'archiviazione BLOB per l'uso in futuro.
4. **Assegnazione di un punteggio al modello** in base ai dati di test.
5. **Tracciamento dei risultati** con curve ROC.

Di seguito è riportato il codice per queste procedure:

    # CREATE A LOGISTIC REGRESSION MODEL
    val lr = new LogisticRegression().setLabelCol("tipped").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)
    val lrModel = lr.fit(OneHotTRAIN)

    # PREDICT ON THE TEST DATA SET
    val predictions = lrModel.transform(OneHotTEST)

    # SELECT `BinaryClassificationEvaluator()` TO COMPUTE THE TEST ERROR
    val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
    val ROC = evaluator.evaluate(predictions)
    println("ROC on test data = " + ROC)

    # SAVE THE MODEL
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "LogisticRegression__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    lrModel.save(filename);

Caricare, classificare e salvare i risultati.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # LOAD THE SAVED MODEL AND SCORE THE TEST DATA SET
    val savedModel = org.apache.spark.ml.classification.LogisticRegressionModel.load(filename)
    println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")

    # SCORE THE MODEL ON THE TEST DATA
    val predictions = savedModel.transform(OneHotTEST).select("tipped","probability","rawPrediction")
    predictions.registerTempTable("testResults")

    # SELECT `BinaryClassificationEvaluator()` TO COMPUTE THE TEST ERROR
    val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
    val ROC = evaluator.evaluate(predictions)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE ROC RESULTS
    println("ROC on test data = " + ROC)


**Output:**

ROC sui dati di test = 0.9827381497557599

Usare Python nei frame di dati Pandas locali per tracciare la curva ROC.

    # QUERY THE RESULTS
    %%sql -q -o sqlResults
    SELECT tipped, probability from testResults


    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    %matplotlib inline
    from sklearn.metrics import roc_curve,auc

    sqlResults['probFloat'] = sqlResults.apply(lambda row: row['probability'].values()[0][1], axis=1)
    predictions_pddf = sqlResults[["tipped","probFloat"]]

    # PREDICT THE ROC CURVE
    # predictions_pddf = sqlResults.rename(columns={'_1': 'probability', 'tipped': 'label'})
    prob = predictions_pddf["probFloat"]
    fpr, tpr, thresholds = roc_curve(predictions_pddf['tipped'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)

    # PLOT THE ROC CURVE
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


**Output:**

![Curva ROC per mancia o non mancia](./media/scala-walkthrough/plot-roc-curve-tip-or-not.png)

### <a name="create-a-random-forest-classification-model"></a>Creare un modello di classificazione di foresta casuale
Successivamente, creare un modello di classificazione di foresta casuale con la funzione `RandomForestClassifier()` di Spark ML e valutare il modello sui dati di test.

    # RECORD THE START TIME
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

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # CALCULATE BINARY CLASSIFICATION EVALUATION METRICS
    val evaluator = new BinaryClassificationEvaluator().setLabelCol("label").setRawPredictionCol("probability").setMetricName("areaUnderROC")
    val ROC = evaluator.evaluate(predictions)
    println("ROC on test data = " + ROC)


**Output:**

ROC sui dati di test = 0.9847103571552683

### <a name="create-a-gbt-classification-model"></a>Creare un modello di classificazione GBT
Successivamente, creare un modello GBT con la funzione `GradientBoostedTrees()` di MLlib e valutare il modello sui dati di test.

    # TRAIN A GBT CLASSIFICATION MODEL BY USING MLLIB AND A LABELED POINT

    # RECORD THE START TIME
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

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE ROC METRIC
    println(s"Area under ROC curve: ${metrics.areaUnderROC}")


**Output:**

Area sotto la curva ROC = 0,9846895479241554

## <a name="regression-model-predict-tip-amount"></a>Modello di regressione: stimare l'importo della mancia
In questa sezione vengono creati due tipi di modelli di regressione per stimare l'importo della mancia:

* Un **modello di regressione lineare regolarizzata** usando la funzione `LinearRegression()` di Spark ML. Il modello verrà salvato e valutato sui dati di test.
* Un **modello di regressione con alberi con boosting a gradienti** usando la funzione `GBTRegressor()` di Spark ML.

### <a name="create-a-regularized-linear-regression-model"></a>Creare un modello di regressione lineare regolarizzata
    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # CREATE A REGULARIZED LINEAR REGRESSION MODEL BY USING THE SPARK ML FUNCTION AND DATA FRAMES
    val lr = new LinearRegression().setLabelCol("tip_amount").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)

    # FIT THE MODEL BY USING DATA FRAMES
    val lrModel = lr.fit(OneHotTRAIN)
    println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")

    # SUMMARIZE THE MODEL OVER THE TRAINING SET AND PRINT METRICS
    val trainingSummary = lrModel.summary
    println(s"numIterations: ${trainingSummary.totalIterations}")
    println(s"objectiveHistory: ${trainingSummary.objectiveHistory.toList}")
    trainingSummary.residuals.show()
    println(s"RMSE: ${trainingSummary.rootMeanSquaredError}")
    println(s"r2: ${trainingSummary.r2}")

    # SAVE THE MODEL IN AZURE BLOB STORAGE
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "LinearRegression__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    lrModel.save(filename);

    # PRINT THE COEFFICIENTS
    println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")

    # SCORE THE MODEL ON TEST DATA
    val predictions = lrModel.transform(OneHotTEST)

    # EVALUATE THE MODEL ON TEST DATA
    val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
    val r2 = evaluator.evaluate(predictions)
    println("R-sqr on test data = " + r2)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Output:**

Tempo di esecuzione della cella: 13 secondi.

    # LOAD A SAVED LINEAR REGRESSION MODEL FROM BLOB STORAGE AND SCORE A TEST DATA SET

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # LOAD A SAVED LINEAR REGRESSION MODEL FROM AZURE BLOB STORAGE
    val savedModel = org.apache.spark.ml.regression.LinearRegressionModel.load(filename)
    println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")

    # SCORE THE MODEL ON TEST DATA
    val predictions = savedModel.transform(OneHotTEST).select("tip_amount","prediction")
    predictions.registerTempTable("testResults")

    # EVALUATE THE MODEL ON TEST DATA
    val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
    val r2 = evaluator.evaluate(predictions)
    println("R-sqr on test data = " + r2)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE RESULTS
    println("R-sqr on test data = " + r2)


**Output:**

R-sqr sui dati di test = 0.5960320470835743

Successivamente, eseguire una query sui risultati del test come frame di dati e usare AutoVizWidget e matplotlib per visualizzarli.

    # RUN A SQL QUERY
    %%sql -q -o sqlResults
    select * from testResults

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES
    # CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, AND SO ON)
    sqlResults

Il codice crea un frame di dati locale dall'output della query ed esegue il tracciato dei dati. Il magic `%%local` crea un frame di dati locale, `sqlResults`, che può essere usato per eseguire tracciati con matplotlib.

> [!NOTE]
> Questo magic Spark viene usato più volte in questo articolo. In caso di un'elevata quantità di dati, è consigliabile campionare i dati in modo da creare un frame che possa essere contenuto nella memoria locale.
> 
> 

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

**Output:**

![Importo della mancia: effettivo rispetto a stimato](./media/scala-walkthrough/plot-actual-vs-predicted-tip-amount.png)

### <a name="create-a-gbt-regression-model"></a>Creare un modello di regressione con boosting a gradienti
Creare un modello di regressione GBT con la funzione `GBTRegressor()` di Spark ML e valutare il modello sui dati di test.

[Alberi con boosting a gradienti](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBT, Gradient boosted tree) sono insiemi di alberi delle decisioni. Gli alberi GBT eseguono il training degli alberi delle decisioni in modo iterativo per ridurre al minimo la perdita di funzioni. È possibile usare GBT per la classificazione e la regressione. Gli alberi GBT possono gestire funzionalità categoriche, non richiedono il ridimensionamento delle funzionalità e possono rilevare non linearità e interazioni di funzionalità. Possono anche essere usati in un'impostazione di classificazione multiclasse.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # TRAIN A GBT REGRESSION MODEL
    val gbt = new GBTRegressor().setLabelCol("label").setFeaturesCol("featuresCat").setMaxIter(10)
    val gbtModel = gbt.fit(indexedTRAINwithCatFeat)

    # MAKE PREDICTIONS
    val predictions = gbtModel.transform(indexedTESTwithCatFeat)

    # COMPUTE TEST SET R2
    val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
    val Test_R2 = evaluator.evaluate(predictions)


    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE RESULTS
    println("Test R-sqr is: " + Test_R2);


**Output:**

Il valore R-sqr del test è: 0.7655383534596654

## <a name="advanced-modeling-utilities-for-optimization"></a>Utilità di modellazione avanzate per l'ottimizzazione
In questa sezione, usare le utilità di Machine Learning che gli sviluppatori spesso usano per l'ottimizzazione del modello. In particolare, è possibile ottimizzare i modelli di Machine Learning in tre diversi modi usando lo sweep dei parametri e la convalida incrociata:

* Suddividere i dati in set di training e convalida, ottimizzare il modello tramite sweep di iperparametri su un set di training e valutare su un set di convalida (regressione lineare)
* Ottimizzare il modello usando la convalida incrociata e lo sweep di iperparametri tramite la funzione CrossValidator di Spark ML (classificazione binaria)
* Ottimizzare il modello usando codice personalizzato di convalida incrociata e sweep di parametri per usare qualsiasi funzione e set di parametri Machine Learning (regressione lineare)

**Convalida incrociata** è una tecnica che consente di valutare in che modo un modello con training eseguito su un set di dati noto verrà generalizzato per stimare le funzionalità di set di dati su cui non è stato eseguito il training. L'idea generale alla base di questa tecnica consiste nell'eseguire il training di un modello su un set di dati noti e quindi di testare l'accuratezza delle stime su un set di dati indipendente. Un'implementazione comune consiste nel dividere un set di dati in *k*riduzioni e quindi eseguire il training del modello in base a uno schema round robin su tutte le riduzioni eccetto una.

**ottimizzazione degli iperparametri** consiste nello scegliere un set di iperparametri per un algoritmo di apprendimento, in genere con l'obiettivo di ottimizzare una misura delle prestazioni dell'algoritmo su un set di dati indipendente. Un iperparametro è un valore che è necessario specificare all'esterno della procedura di training del modello. I presupposti dei valori degli iperparametri possono influire sulla flessibilità e l'accuratezza dei modelli. Gli alberi delle decisioni includono, ad esempio, iperparametri come la profondità desiderata e il numero di foglie nell'albero. È necessario impostare un termine di penalità per errata classificazione per macchine a vettori di supporto (SVM).

Un modo comune per eseguire l'ottimizzazione degli iperparametri è la ricerca nella griglia o **sweep di parametri**. La ricerca prevede di eseguire una ricerca completa nei valori di un subset specificato dello spazio degli iperparametri per un algoritmo di apprendimento. La convalida incrociata può fornire metriche delle prestazioni per selezionare i risultati ottimali generati dall'algoritmo di ricerca nella griglia. Se si usa lo sweep di convalida incrociata degli iperparametri, è possibile limitare i problemi quali il sovradattamento di un modello ai dati di training. In questo modo, il modello consente di mantenere la capacità di applicazione al set di dati generale da cui sono stati estratti i dati di training.

### <a name="optimize-a-linear-regression-model-with-hyper-parameter-sweeping"></a>Ottimizzare un modello di regressione lineare con sweep di iperparametri
Suddividere quindi i dati in set di training e convalida, usare lo sweep degli iperparametri su un set di training per ottimizzare il modello e valutare su un set di convalida (regressione lineare).

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # RENAME `tip_amount` AS A LABEL
    val OneHotTRAINLabeled = OneHotTRAIN.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
    val OneHotTESTLabeled = OneHotTEST.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
    OneHotTRAINLabeled.cache()
    OneHotTESTLabeled.cache()

    # DEFINE THE ESTIMATOR FUNCTION: `THE LinearRegression()` FUNCTION
    val lr = new LinearRegression().setLabelCol("label").setFeaturesCol("features").setMaxIter(10)

    # DEFINE THE PARAMETER GRID
    val paramGrid = new ParamGridBuilder().addGrid(lr.regParam, Array(0.1, 0.01, 0.001)).addGrid(lr.fitIntercept).addGrid(lr.elasticNetParam, Array(0.1, 0.5, 0.9)).build()

    # DEFINE THE PIPELINE WITH A TRAIN/TEST VALIDATION SPLIT (75% IN THE TRAINING SET), AND THEN THE SPECIFY ESTIMATOR, EVALUATOR, AND PARAMETER GRID
    val trainPct = 0.75
    val trainValidationSplit = new TrainValidationSplit().setEstimator(lr).setEvaluator(new RegressionEvaluator).setEstimatorParamMaps(paramGrid).setTrainRatio(trainPct)

    # RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
    val model = trainValidationSplit.fit(OneHotTRAINLabeled)

    # MAKE PREDICTIONS ON THE TEST DATA BY USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORMS THE BEST
    val testResults = model.transform(OneHotTESTLabeled).select("label", "prediction")

    # COMPUTE TEST SET R2
    val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
    val Test_R2 = evaluator.evaluate(testResults)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    println("Test R-sqr is: " + Test_R2);


**Output:**

Il valore R-sqr del test è: 0.6226484708501209

### <a name="optimize-the-binary-classification-model-by-using-cross-validation-and-hyper-parameter-sweeping"></a>Ottimizzare il modello di classificazione binaria usando la convalida incrociata e lo sweep di iperparametri
Questa sezione illustra come ottimizzare un modello di classificazione binaria usando la convalida incrociata e lo sweep di iperparametri. Questo metodo usa la funzione `CrossValidator` di Spark ML.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # CREATE DATA FRAMES WITH PROPERLY LABELED COLUMNS TO USE WITH THE TRAIN AND TEST SPLIT
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

    # DEFINE THE TRAIN/TEST VALIDATION SPLIT (75% IN THE TRAINING SET)
    val CrossValidator = new CrossValidator().setEstimator(rf).setEvaluator(new BinaryClassificationEvaluator).setEstimatorParamMaps(paramGrid).setNumFolds(numFolds)

    # RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
    val model = CrossValidator.fit(indexedTRAINwithCatFeatBinTargetRF)

    # MAKE PREDICTIONS ON THE TEST DATA BY USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORMS THE BEST
    val testResults = model.transform(indexedTESTwithCatFeatBinTargetRF).select("label", "prediction")

    # COMPUTE THE TEST F1 SCORE
    val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
    val Test_f1Score = evaluator.evaluate(testResults)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Output:**

Tempo di esecuzione della cella: 33 secondi.

### <a name="optimize-the-linear-regression-model-by-using-custom-cross-validation-and-parameter-sweeping-code"></a>Ottimizzare il modello di regressione lineare tramite codice personalizzato di convalida incrociata e sweep di parametri
Ottimizzare quindi il modello con codice personalizzato e identificare i parametri ottimali per il modello usando il criterio di massima precisione. Creare quindi il modello finale, valutarlo sui dati di test e salvarlo nell'archiviazione BLOB. Infine, caricare il modello, assegnare un punteggio ai dati di test e valutarne la precisione.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # DEFINE THE PARAMETER GRID AND THE NUMBER OF FOLDS
    val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(5,10)).addGrid(rf.numTrees, Array(10,25,50)).build()

    val nFolds = 3
    val numModels = paramGrid.size
    val numParamsinGrid = 2

    # SPECIFY THE NUMBER OF CATEGORIES FOR CATEGORICAL VARIABLES
    val categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))

    var maxDepth = -1
    var numTrees = -1
    var param = ""
    var paramval = -1
    var validateLB = -1.0
    var validateUB = -1.0
    val h = 1.0 / nFolds;
    val RMSE  = Array.fill(numModels)(0.0)

    # CREATE K-FOLDS
    val splits = MLUtils.kFold(indexedTRAINbinary, numFolds = nFolds, seed=1234)


    # LOOP THROUGH K-FOLDS AND THE PARAMETER GRID TO GET AND IDENTIFY THE BEST PARAMETER SET BY LEVEL OF ACCURACY
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

    # GET THE BEST PARAMETERS FROM A CROSS-VALIDATION AND PARAMETER SWEEP
    var best_maxDepth = -1
    var best_numTrees = -1
    for (nParams <- 0 to (numParamsinGrid-1)) {
        param = paramGrid(minRMSEindex).toSeq(nParams).param.toString.split("__")(1)
        paramval = paramGrid(minRMSEindex).toSeq(nParams).value.toString.toInt
        if (param == "maxDepth") {best_maxDepth = paramval}
        if (param == "numTrees") {best_numTrees = paramval}
    }

    # CREATE THE BEST MODEL WITH THE BEST PARAMETERS AND A FULL TRAINING DATA SET
    val best_rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                      numTrees=best_numTrees, maxDepth=best_maxDepth,
                                                      featureSubsetStrategy="auto",impurity="variance", maxBins=32)

    # SAVE THE BEST RANDOM FOREST MODEL IN BLOB STORAGE
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "BestCV_RF_Regression__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    best_rfModel.save(sc, filename);

    # PREDICT ON THE TRAINING SET WITH THE BEST MODEL AND THEN EVALUATE
    val labelAndPreds = indexedTESTreg.map { point =>
                                            val prediction = best_rfModel.predict(point.features)
                                            ( prediction, point.label )
                                           }

    val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
    val test_rsqr = new RegressionMetrics(labelAndPreds).r2

    # GET THE TIME TO RUN THE CELL
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


**Output:**

Tempo di esecuzione della cella: 61 secondi.

## <a name="consume-spark-built-machine-learning-models-automatically-with-scala"></a>Usare automaticamente i modelli di Machine Learning compilati con Spark con Scala
Per una panoramica degli argomenti che forniscono informazioni dettagliate sulle attività che costituiscono il processo di analisi scientifica dei dati in Azure, vedere [Processo di analisi scientifica dei dati per i team](http://aka.ms/datascienceprocess).

[Procedure dettagliate del Processo di analisi scientifica dei dati per i team](walkthroughs.md) descrive altre procedure dettagliate end-to-end che illustrano i passaggi del processo di analisi scientifica dei dati per i team in scenari specifici. Le procedure dettagliate illustrano anche come combinare strumenti cloud, strumenti locali e servizi in un flusso di lavoro o in una pipeline per creare un'applicazione intelligente.

[Assegnare punteggi a modelli di apprendimento automatico compilati con Spark](spark-model-consumption.md) illustra come usare il codice di Scala per caricare automaticamente nuovi set di dati e assegnare loro un punteggio con modelli di Machine Learning compilati in Spark e salvati nell'archiviazione BLOB di Azure. È possibile seguire le istruzioni illustrate nell'argomento e sostituire semplicemente il codice Python con il codice Scala indicato in questo articolo per abilitare l'uso automatico.

