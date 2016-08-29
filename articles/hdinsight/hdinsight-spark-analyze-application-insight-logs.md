<properties
pageTitle="Analizzare i log di Application Insights con Spark in HDInsight | Microsoft Azure"
description="Informazioni su come esportare i log di Application Insight nell'archiviazione BLOB e analizzare i log con Spark in HDInsight."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="paulettm"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="08/15/2016"
ms.author="larryfr"/>  

# Analizzare i log di Application Insights Telemetry con Spark in HDInsight

[Visual Studio Application Insights](../application-insights/app-insights-overview.md) è un servizio di analisi dei dati che consente di monitorare le applicazioni Web. I dati di telemetria generati da Application Insights possono essere esportati in Archiviazione di Azure e da lì analizzati con HDInsight.

Questo documento dimostra l'uso di HDInsight per l'analisi dei dati di Application Insights Telemetry tramite Apache Spark.

## Prerequisiti

* Una sottoscrizione di Azure.

* Un'applicazione configurata per l'uso di Application Insights.

* Familiarità con la creazione di un cluster HDInsight basato su Linux. Se non si ha familiarità con la creazione di un cluster, vedere [Creare Spark in HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md) per maggiori informazioni.

    > [AZURE.NOTE] Questo documento non fornisce la procedura dettagliata per la creazione di un nuovo cluster. Rimanda ad altri documenti che forniscono informazioni su come creare un cluster per accedere ai dati di telemetria.

* Un Web browser. Viene usato per eseguire in modo interattivo l'analisi tramite un notebook Jupyter.

Per lo sviluppo e il test di questo documento, sono stati usati gli elementi seguenti:

* I dati di Application Insights Telemetry sono stati generati tramite un'[app Web Node.js configurata per l'uso di Application Insights](../application-insights/app-insights-nodejs.md).

* Per analizzare i dati, è stato usato uno Spark basato su Linux nel cluster HDInsight versione 3.4.

## Architettura e pianificazione

Il diagramma seguente illustra l'architettura del servizio dell'esempio:

![diagramma che illustra un flusso di dati da Application Insights nell'archiviazione BLOB e la fase di elaborazione Spark in HDInsight](./media/hdinsight-spark-analyze-application-insight-logs/appinsightshdinsight.png)  

### Archiviazione di Azure

Un cluster HDInsight può accedere direttamente ai BLOB in blocchi da un account di archiviazione di Azure e Application Insights può essere configurato per esportare continuamente dati di telemetria nei BLOB in archiviazione di Azure. Tuttavia, esistono alcuni requisiti da seguire:

* __Posizione__: l'account di archiviazione deve trovarsi nella stessa area geografica di HDInsight. Questo riduce la latenza dell'accesso ai dati ed evita i costi di uscita legati allo spostamento dei dati tra le aree.

* __Tipo di BLOB__: in HDInsight sono supportati solo i BLOB in blocchi. Application Insights usa per impostazione predefinita i BLOB in blocchi, quindi funzionerà con HDInsight.

* __Autorizzazioni di accesso__: se si usa lo stesso account di archiviazione per l'esportazione continua di Application Insights e lo spazio di archiviazione predefinito di HDInsight, HDInsight dispone di accesso completo ai dati di Application Insight Telemetry. Ciò significa che è possibile eliminare i dati di telemetria dal cluster HDInsight.

    In alternativa, è consigliabile usare account di archiviazione separati per HDInsight e Application Insights Telemetry e [le firme di accesso condiviso (SAS) per limitare l'accesso ai dati da HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md). L'uso di una firma di accesso condiviso consente di concedere l'accesso ad HDInsight in sola lettura per i dati di telemetria.

### Schema dei dati

Application Insights offre informazione sul [modello di esportazione dei dati](../application-insights/app-insights-export-data-model.md) per il formato di dati di telemetria esportati nei BLOB. La procedura descritta in questo documento usa Spark SQL per lavorare con i dati. Spark SQL può generare automaticamente uno schema per la struttura di dati JSON a cui si accede da Application Insights, pertanto non è necessario definire manualmente lo schema durante l'esecuzione dell'analisi.

## Esportare i dati di telemetria

Seguire la procedura in [Configure Continuous Export](../application-insights/app-insights-export-telemetry.md) (Configurare l'esportazione continua) per configurare Application Insights per l'esportazione delle informazioni di telemetria in un BLOB di archiviazione di Azure.

## Configurare HDInsight per l'accesso ai dati

Usare le informazioni contenute in [Usare le firme di accesso condiviso di Archiviazione di Azure per limitare l'accesso ai dati con HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md) per creare una firma di accesso condiviso per il contenitore BLOB che contiene i dati di telemetria esportati. La firma di accesso condiviso deve fornire accesso ai di dati in sola lettura.

Il documento di firma di accesso condiviso fornisce informazioni su come aggiungere l'archiviazione della firma di accesso condiviso a un cluster HDInsight basato su Linux esistente. Il documento fornisce anche informazioni su come aggiungere l'archiviazione durante la creazione di un nuovo cluster HDInsight.

## Analizzare i dati tramite Python (PySpark)

1. Nel [Portale di Azure](https://portal.azure.com) selezionare il proprio Spark sul cluster HDInsight. Dalla sezione __Collegamenti rapidi__ selezionare __Dashboard cluster__, quindi selezionare __Notebook di Jupyter__ dal pannello\_\_Dashboard cluster.

    ![Il dashboard del cluster](./media/hdinsight-spark-analyze-application-insight-logs/clusterdashboards.png)

2. Nell'angolo superiore destro della pagina Jupyter, selezionare __Nuovo__ e quindi __PySpark__. Si apre una nuova scheda del browser contenente un notebook di Jupyter basato su Python.

3. Nel primo campo (chiamato __cella__) della pagina immettere quanto segue:

        sc._jsc.hadoopConfiguration().set('mapreduce.input.fileinputformat.input.dir.recursive', 'true')

    In questo modo Spark ottiene accesso continuo alla struttura della directory per i dati di input. Application Insights Telemetry è connesso a una struttura di directory simile alla seguente:

        /{telemetry type}/YYYY-MM-DD/{##}/

4. Eseguire il codice tramite __MAIUSC + INVIO__. Sul lato sinistro della cella, viene visualizzato un "*" tra parentesi per indicare che il codice contenuto nella cella è in esecuzione. Dopo l'esecuzione, "*" diventerà un numero e verrà visualizzato sotto la cella un output simile al seguente:

        Creating SparkContext as 'sc'

        ID	YARN Application ID	Kind	State	Spark UI	Driver log	Current session?
        3	application_1468969497124_0001	pyspark	idle	Link	Link	✔

        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...

5. Una nuova cella è stata creata sotto la prima. Immettere quanto segue nella nuova cella. Sostituire __CONTAINER__ e __STORAGEACCOUNT__ con il nome dell'account di archiviazione di Azure e con il nome del contenitore BLOB usato per la configurazione dell'esportazione continua di Application Insights.

        %%bash
        hdfs dfs -ls wasb://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
    
    Eseguire la cella tramite __MAIUSC + INVIO__. Il risultato visualizzato sarà simile al seguente:

        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831
    
    Il percorso wasb restituito è il percorso dei dati di Application Insights Telemetry. Modificare la riga `hdfs dfs -ls` nella cella per usare il percorso wasb restituito e quindi digitare __MAIUSC + INVIO__ per eseguire di nuovo la cella. Questa volta, il risultato dovrebbe visualizzare le directory che contengono i dati di telemetria.

    > [AZURE.NOTE] Per il resto dei passaggi in questa sezione è stata usata la directory `wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests`. Questa directory potrebbe non esistere, a meno che i dati di telemetria non siano destinati a un'app Web. Se si usano dati di telemetria che non includono una directory di richieste, selezionare un'altra directory e modificare il resto dei passaggi per usare tale directory e lo schema per i dati in essa archiviati.

6. Immettere quanto segue nella prossima cella. Sostituire __WASB\_PATH__ con il percorso nel passaggio precedente.

        jsonFiles = sc.textFile('WASB_PATH')
        jsonData = sqlContext.read.json(jsonFiles)

    Verrà creato un nuovo frame di dati dai file JSON esportati dal processo di esportazione continua. Eseguire la cella tramite __MAIUSC + INVIO__.

7. Nella prossima cella, immettere ed eseguire il comando seguente per visualizzare lo schema Spark creato per i file JSON:

        jsonData.printSchema()
    
    Lo schema per ogni tipo di dati di telemetria sarà diverso. Di seguito è riportato lo schema generato per le richieste Web (dati archiviati nella sottodirectory `Requests`):

        root
        |-- context: struct (nullable = true)
        |    |-- application: struct (nullable = true)
        |    |    |-- version: string (nullable = true)
        |    |-- custom: struct (nullable = true)
        |    |    |-- dimensions: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |    |-- metrics: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- eventTime: string (nullable = true)
        |    |    |-- isSynthetic: boolean (nullable = true)
        |    |    |-- samplingRate: double (nullable = true)
        |    |    |-- syntheticSource: string (nullable = true)
        |    |-- device: struct (nullable = true)
        |    |    |-- browser: string (nullable = true)
        |    |    |-- browserVersion: string (nullable = true)
        |    |    |-- deviceModel: string (nullable = true)
        |    |    |-- deviceName: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- osVersion: string (nullable = true)
        |    |    |-- type: string (nullable = true)
        |    |-- location: struct (nullable = true)
        |    |    |-- city: string (nullable = true)
        |    |    |-- clientip: string (nullable = true)
        |    |    |-- continent: string (nullable = true)
        |    |    |-- country: string (nullable = true)
        |    |    |-- province: string (nullable = true)
        |    |-- operation: struct (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |-- session: struct (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- isFirst: boolean (nullable = true)
        |    |-- user: struct (nullable = true)
        |    |    |-- anonId: string (nullable = true)
        |    |    |-- isAuthenticated: boolean (nullable = true)
        |-- internal: struct (nullable = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- documentVersion: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |-- request: array (nullable = true)
        |    |-- element: struct (containsNull = true)
        |    |    |-- count: long (nullable = true)
        |    |    |-- durationMetric: struct (nullable = true)
        |    |    |    |-- count: double (nullable = true)
        |    |    |    |-- max: double (nullable = true)
        |    |    |    |-- min: double (nullable = true)
        |    |    |    |-- sampledValue: double (nullable = true)
        |    |    |    |-- stdDev: double (nullable = true)
        |    |    |    |-- value: double (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |    |-- responseCode: long (nullable = true)
        |    |    |-- success: boolean (nullable = true)
        |    |    |-- url: string (nullable = true)
        |    |    |-- urlData: struct (nullable = true)
        |    |    |    |-- base: string (nullable = true)
        |    |    |    |-- hashTag: string (nullable = true)
        |    |    |    |-- host: string (nullable = true)
        |    |    |    |-- protocol: string (nullable = true)

8. Per registrare il frame di dati come una tabella temporanea ed eseguire una query sui dati, usare quanto segue:

        jsonData.registerTempTable("requests")
        sqlContext.sql("select context.location.city from requests where context.location.city is not null")

    Questa query restituirà le informazioni sulla città per i primi 20 record in cui context.location.city non ha valore Null.

    > [AZURE.NOTE] La struttura del contesto è presente in tutti i dati di telemetria registrati da Application Insights. Tuttavia, l'elemento città non può essere inserito nei log. Usare lo schema per identificare altri elementi su cui è possibile eseguire una query che potrebbe contenere i dati per i log.

    La query restituirà informazioni simili alle seguenti:

        +---------+
        |     city|
        +---------+
        | Bellevue|
        |  Redmond|
        |  Seattle|
        |Charlotte|
        ...
        +---------+

## Analizzare i dati tramite Scala

1. Nel [Portale di Azure](https://portal.azure.com) selezionare il proprio Spark sul cluster HDInsight. Dalla sezione __Collegamenti rapidi__ selezionare __Dashboard cluster__, quindi selezionare __Notebook di Jupyter__ dal pannello\_\_Dashboard cluster.

    ![Il dashboard del cluster](./media/hdinsight-spark-analyze-application-insight-logs/clusterdashboards.png)  

2. Nell'angolo superiore destro della pagina Jupyter, selezionare __Nuovo__ e quindi __Scala__. Si apre una nuova scheda del browser contenente un notebook di Jupyter basato su Scala.

3. Nel primo campo (chiamato __cella__) della pagina immettere quanto segue:

        sc.hadoopConfiguration.set("mapreduce.input.fileinputformat.input.dir.recursive", "true")

    In questo modo Spark ottiene accesso continuo alla struttura della directory per i dati di input. Application Insights Telemetry è connesso a una struttura di directory simile alla seguente:

        /{telemetry type}/YYYY-MM-DD/{##}/

4. Eseguire il codice tramite __MAIUSC + INVIO__. Sul lato sinistro della cella, viene visualizzato un "*" tra parentesi per indicare che il codice contenuto nella cella è in esecuzione. Dopo l'esecuzione, "*" diventerà un numero e verrà visualizzato sotto la cella un output simile al seguente:

        Creating SparkContext as 'sc'

        ID	YARN Application ID	Kind	State	Spark UI	Driver log	Current session?
        3	application_1468969497124_0001	spark	idle	Link	Link	✔

        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...

5. Una nuova cella è stata creata sotto la prima. Immettere quanto segue nella nuova cella. Sostituire __CONTAINER__ e __STORAGEACCOUNT__ con il nome dell'account di archiviazione di Azure e con il nome del contenitore BLOB usato per la configurazione dell'esportazione continua di Application Insights.

        %%bash
        hdfs dfs -ls wasb://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
    
    Eseguire la cella tramite __MAIUSC + INVIO__. Il risultato visualizzato sarà simile al seguente:

        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831
    
    Il percorso wasb restituito è il percorso dei dati di Application Insights Telemetry. Modificare la riga `hdfs dfs -ls` nella cella per usare il percorso wasb restituito e quindi digitare __MAIUSC + INVIO__ per eseguire di nuovo la cella. Questa volta, il risultato dovrebbe visualizzare le directory che contengono i dati di telemetria.

    > [AZURE.NOTE] Per il resto dei passaggi in questa sezione è stata usata la directory `wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests`. Questa directory potrebbe non esistere, a meno che i dati di telemetria non siano destinati a un'app Web. Se si usano dati di telemetria che non includono una directory di richieste, selezionare un'altra directory e modificare il resto dei passaggi per usare tale directory e lo schema per i dati in essa archiviati.

6. Immettere quanto segue nella prossima cella. Sostituire __WASB\_PATH__ con il percorso nel passaggio precedente.

        jsonFiles = sc.textFile('WASB_PATH')
        jsonData = sqlContext.read.json(jsonFiles)

    Verrà creato un nuovo frame di dati dai file JSON esportati dal processo di esportazione continua. Eseguire la cella tramite __MAIUSC + INVIO__.

7. Nella prossima cella, immettere ed eseguire il comando seguente per visualizzare lo schema Spark creato per i file JSON:

        jsonData.printSchema
    
    Lo schema per ogni tipo di dati di telemetria sarà diverso. Di seguito è riportato lo schema generato per le richieste Web (dati archiviati nella sottodirectory `Requests`):

        root
        |-- context: struct (nullable = true)
        |    |-- application: struct (nullable = true)
        |    |    |-- version: string (nullable = true)
        |    |-- custom: struct (nullable = true)
        |    |    |-- dimensions: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |    |-- metrics: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- eventTime: string (nullable = true)
        |    |    |-- isSynthetic: boolean (nullable = true)
        |    |    |-- samplingRate: double (nullable = true)
        |    |    |-- syntheticSource: string (nullable = true)
        |    |-- device: struct (nullable = true)
        |    |    |-- browser: string (nullable = true)
        |    |    |-- browserVersion: string (nullable = true)
        |    |    |-- deviceModel: string (nullable = true)
        |    |    |-- deviceName: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- osVersion: string (nullable = true)
        |    |    |-- type: string (nullable = true)
        |    |-- location: struct (nullable = true)
        |    |    |-- city: string (nullable = true)
        |    |    |-- clientip: string (nullable = true)
        |    |    |-- continent: string (nullable = true)
        |    |    |-- country: string (nullable = true)
        |    |    |-- province: string (nullable = true)
        |    |-- operation: struct (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |-- session: struct (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- isFirst: boolean (nullable = true)
        |    |-- user: struct (nullable = true)
        |    |    |-- anonId: string (nullable = true)
        |    |    |-- isAuthenticated: boolean (nullable = true)
        |-- internal: struct (nullable = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- documentVersion: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |-- request: array (nullable = true)
        |    |-- element: struct (containsNull = true)
        |    |    |-- count: long (nullable = true)
        |    |    |-- durationMetric: struct (nullable = true)
        |    |    |    |-- count: double (nullable = true)
        |    |    |    |-- max: double (nullable = true)
        |    |    |    |-- min: double (nullable = true)
        |    |    |    |-- sampledValue: double (nullable = true)
        |    |    |    |-- stdDev: double (nullable = true)
        |    |    |    |-- value: double (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |    |-- responseCode: long (nullable = true)
        |    |    |-- success: boolean (nullable = true)
        |    |    |-- url: string (nullable = true)
        |    |    |-- urlData: struct (nullable = true)
        |    |    |    |-- base: string (nullable = true)
        |    |    |    |-- hashTag: string (nullable = true)
        |    |    |    |-- host: string (nullable = true)
        |    |    |    |-- protocol: string (nullable = true)

8. Per registrare il frame di dati come una tabella temporanea ed eseguire una query sui dati, usare quanto segue:

        jsonData.registerTempTable("requests")
        var city = sqlContext.sql("select context.location.city from requests where context.location.city is not null limit 10").show()

    Questa query restituirà le informazioni sulla città per i primi 20 record in cui context.location.city non ha valore Null.

    > [AZURE.NOTE] La struttura del contesto è presente in tutti i dati di telemetria registrati da Application Insights. Tuttavia, l'elemento città non può essere inserito nei log. Usare lo schema per identificare altri elementi su cui è possibile eseguire una query che potrebbe contenere i dati per i log.

    La query restituirà informazioni simili alle seguenti:

        +---------+
        |     city|
        +---------+
        | Bellevue|
        |  Redmond|
        |  Seattle|
        |Charlotte|
        ...
        +---------+

## Passaggi successivi

Per altri esempi sull'uso di Spark per lavorare con dati e servizi di Azure, vedere i documenti seguenti:

* [Spark con Business Intelligence: eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](hdinsight-apache-spark-use-bi-tools.md)

* [Spark con Machine Learning: utilizzare Spark in HDInsight per l'analisi della temperatura di compilazione utilizzando dati HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark con Machine Learning: usare Spark in HDInsight per prevedere i risultati del controllo degli alimenti](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Streaming Spark: usare Spark in HDInsight per la creazione di applicazioni di streaming in tempo reale](hdinsight-apache-spark-eventhub-streaming.md)

* [Analisi dei log del sito Web mediante Spark in HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

Per informazioni sulla creazione e l'esecuzione delle applicazioni Spark, vedere i documenti seguenti:

* [Creare un'applicazione autonoma con Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Eseguire processi in modalità remota in un cluster Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)

<!---HONumber=AcomDC_0817_2016-->