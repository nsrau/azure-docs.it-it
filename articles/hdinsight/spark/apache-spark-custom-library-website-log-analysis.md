---
title: 'Analizzare i log del sito Web con le librerie Python in Spark: Azure'
description: Questo notebook dimostra come analizzare i dati dei log mediante una libreria personalizzata con Spark in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/27/2019
ms.openlocfilehash: c6bf26d8f3a73db6ee69b2aa0de73872911893bf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75552713"
---
# <a name="analyze-website-logs-using-a-custom-python-library-with-apache-spark-cluster-on-hdinsight"></a>Analizzare i log del sito Web usando una libreria Python personalizzata con cluster Apache Spark in HDInsight

Questo notebook dimostra come analizzare i dati dei log mediante una libreria personalizzata con Apache Spark in HDInsight. La libreria personalizzata usata è una libreria di Python chiamata **iislogparser.py**.

## <a name="prerequisites"></a>Prerequisiti

Un cluster Apache Spark in HDInsight. Per istruzioni, vedere l'articolo dedicato alla [creazione di cluster Apache Spark in Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="save-raw-data-as-an-rdd"></a>Salvare i dati non elaborati come RDD

In questa sezione viene usato il notebook di [Jupyter](https://jupyter.org) associato a un cluster Apache Spark in HDInsight per eseguire i processi che elaborano i dati di esempio non elaborati e li salvano come tabella Hive. I dati di esempio sono un file con estensione csv (hvac.csv) disponibile in tutti i cluster per impostazione predefinita.

Una volta salvati i dati come Apache Hive tabella, nella sezione successiva si eseguirà la connessione alla tabella hive usando gli strumenti di business intelligence, ad esempio Power BI e tableau.

1. In un Web browser passare a `https://CLUSTERNAME.azurehdinsight.net/jupyter` dove `CLUSTERNAME` è il nome del cluster.

1. Creare un nuovo notebook. Selezionare **nuovo**e quindi **PySpark**.

    ![Creare un nuovo notebook Apache Jupyter](./media/apache-spark-custom-library-website-log-analysis/hdinsight-create-jupyter-notebook.png "Creare un nuovo notebook Jupyter")

1. Un nuovo notebook verrà creato e aperto con il nome Untitled.pynb. Selezionare il nome del notebook nella parte superiore e immettere un nome descrittivo.

    ![Specificare un nome per il notebook](./media/apache-spark-custom-library-website-log-analysis/hdinsight-name-jupyter-notebook.png "Specificare un nome per il notebook")

1. Poiché è stato creato un notebook usando il kernel PySpark, non è necessario creare contesti in modo esplicito. I contesti Spark e Hive vengono creati automaticamente quando si esegue la prima cella di codice. È possibile iniziare con l'importazione dei tipi necessari per questo scenario. Incollare il frammento di codice seguente in una cella vuota e quindi premere **MAIUSC + INVIO**.

    ```pyspark
    from pyspark.sql import Row
    from pyspark.sql.types import *
    ```

1. Creare un RDD con i dati di log di esempio già disponibili nel cluster. È possibile accedere ai dati nell'account di archiviazione predefinito associato al cluster all'indirizzo `\HdiSamples\HdiSamples\WebsiteLogSampleData\SampleLog\909f2b.log` . Eseguire questo codice:

    ```pyspark
    logs = sc.textFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log')
    ```

1. Recuperare un set di log di esempio per verificare che il passaggio precedente sia stato completato correttamente.

    ```pyspark
    logs.take(5)
    ```

    Verrà visualizzato un output simile al testo seguente:

    ```output
    [u'#Software: Microsoft Internet Information Services 8.0',
    u'#Fields: date time s-sitename cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Cookie) cs(Referer) cs-host sc-status sc-substatus sc-win32-status sc-bytes cs-bytes time-taken',
    u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46',
    u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32',
    u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step4.png X-ARR-LOG-ID=4bea5b3d-8ac9-46c9-9b8c-ec3e9500cbea 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 72177 871 47']
    ```

## <a name="analyze-log-data-using-a-custom-python-library"></a>Analizzare i dati di log usando una libreria personalizzata di Python

1. Nell'output precedente, le prime due righe includono le informazioni di intestazione e ogni riga rimanente corrisponde allo schema descritto nell'intestazione. L'analisi di tali log potrebbe essere complessa. Quindi, viene usata una libreria personalizzata di Python (**iislogparser.py**) che semplifica notevolmente l'analisi di tali log. Per impostazione predefinita, questa libreria è inclusa nel cluster Spark in HDInsight all'indirizzo `/HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py` .

    Tuttavia, questa libreria non è disponibile in, `PYTHONPATH` pertanto non è possibile utilizzarla utilizzando un'istruzione Import come `import iislogparser` . Per usare questa libreria è necessario distribuirla a tutti i nodi di lavoro. Eseguire il frammento di codice seguente.

    ```pyspark
    sc.addPyFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py')
    ```

1. `iislogparser` offre una funzione `parse_log_line` che restituisce `None` se una riga del log è una riga di intestazione e restituisce un'istanza della classe `LogLine` se viene rilevata una riga del log. Usare la classe `LogLine` per estrarre solo le righe del log dall'RDD:

    ```pyspark
    def parse_line(l):
        import iislogparser
        return iislogparser.parse_log_line(l)
    logLines = logs.map(parse_line).filter(lambda p: p is not None).cache()
    ```

1. Recuperare un paio di righe estratte del log per verificare che il passaggio sia stato completato correttamente.

    ```pyspark
    logLines.take(2)
    ```

   L'output dovrebbe essere simile al testo seguente:

    ```output
    [2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46,
    2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32]
    ```

1. La classe `LogLine`, a sua volta, offre alcuni metodi utili, ad esempio `is_error()`, che determina se una voce di log contiene un codice di errore. Utilizzare questa classe per calcolare il numero di errori nelle righe di log estratte, quindi registrare tutti gli errori in un file diverso.

    ```pyspark
    errors = logLines.filter(lambda p: p.is_error())
    numLines = logLines.count()
    numErrors = errors.count()
    print 'There are', numErrors, 'errors and', numLines, 'log entries'
    errors.map(lambda p: str(p)).saveAsTextFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b-2.log')
    ```

    L'output deve essere stato `There are 30 errors and 646 log entries` .

1. È anche possibile usare **Matplotlib** per costruire una visualizzazione dei dati. Ad esempio, se si vuole isolare la causa delle richieste in esecuzione da molto tempo si potrebbero trovare i file che mediamente richiedono più tempo per servire una richiesta. Il frammento di codice seguente recupera le 25 risorse principali che hanno impiegato più tempo per servire una richiesta.

    ```pyspark
    def avgTimeTakenByKey(rdd):
        return rdd.combineByKey(lambda line: (line.time_taken, 1),
                                lambda x, line: (x[0] + line.time_taken, x[1] + 1),
                                lambda x, y: (x[0] + y[0], x[1] + y[1]))\
                    .map(lambda x: (x[0], float(x[1][0]) / float(x[1][1])))

    avgTimeTakenByKey(logLines.map(lambda p: (p.cs_uri_stem, p))).top(25, lambda x: x[1])
    ```

   Verrà visualizzato un output simile al testo seguente:

    ```output
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
    ```

1. È anche possibile presentare queste informazioni sotto forma di tracciato. Come primo passaggio per creare un tracciato, creeremo prima di tutto una tabella temporanea, **AverageTime**. La tabella raggruppa i log in base all'ora per vedere se si sono verificati eventuali picchi di latenza insoliti in un determinato momento.

    ```pyspark
    avgTimeTakenByMinute = avgTimeTakenByKey(logLines.map(lambda p: (p.datetime.minute, p))).sortByKey()
    schema = StructType([StructField('Minutes', IntegerType(), True),
                        StructField('Time', FloatType(), True)])

    avgTimeTakenByMinuteDF = sqlContext.createDataFrame(avgTimeTakenByMinute, schema)
    avgTimeTakenByMinuteDF.registerTempTable('AverageTime')
    ```

1. Quindi, per ottenere tutti i record della tabella **AverageTime** , è possibile eseguire la query SQL seguente.

    ```pyspark
    %%sql -o averagetime
    SELECT * FROM AverageTime
    ```

   Il comando speciale `%%sql` seguito da `-o averagetime` assicura che l'output della query venga mantenuto in locale nel server Jupyter, di solito il nodo head del cluster. L'output viene mantenuto come frame di dati [Pandas](https://pandas.pydata.org/) con il nome specificato **averagetime**.

   Verrà visualizzato un output simile all'immagine seguente:

   ![output della query SQL di HDInsight jupyter](./media/apache-spark-custom-library-website-log-analysis/hdinsight-jupyter-sql-qyery-output.png "Output della query SQL")

   Per altre informazioni sul comando Magic `%%sql`, vedere [Parametri supportati con il comando Magic %%sql](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

1. È ora possibile creare un tracciato tramite Matplotlib, una libreria che consente di creare visualizzazioni di dati. Dato che il tracciato deve essere creato dal frame di dati **averagetime** con salvataggio permanente in locale, il frammento di codice deve iniziare con il magic `%%local`. Ciò garantisce che il codice venga eseguito localmente nel server di Jupyter.

    ```pyspark
    %%local
    %matplotlib inline
    import matplotlib.pyplot as plt

    plt.plot(averagetime['Minutes'], averagetime['Time'], marker='o', linestyle='--')
    plt.xlabel('Time (min)')
    plt.ylabel('Average time taken for request (ms)')
    ```

   Verrà visualizzato un output simile all'immagine seguente:

   ![traccia di analisi del log Web Apache Spark](./media/apache-spark-custom-library-website-log-analysis/hdinsight-apache-spark-web-log-analysis-plot.png "Output Matplotlib")

1. Al termine dell'esecuzione dell'applicazione, è necessario arrestare il notebook per rilasciare le risorse. Per fare ciò, dal menu **File** del notebook fare clic su **Close and Halt** (Chiudi e interrompi). Questa azione arresterà e chiuderà il notebook.

## <a name="next-steps"></a>Passaggi successivi

Esaminare gli articoli seguenti:

* [Panoramica: Apache Spark in Azure HDInsight](apache-spark-overview.md)
* [Usare pacchetti esterni con i notebook Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installare Jupyter Notebook nel computer e connetterlo a un cluster HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)