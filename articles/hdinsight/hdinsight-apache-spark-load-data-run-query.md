---
title: Eseguire query interattive in un cluster Azure HDInsight Spark | Microsoft Docs
description: Guida introduttiva di HDInsight Spark per la creazione di un cluster Apache Spark in HDInsight.
keywords: guida introduttiva spark,spark interattivo,query interattiva,hdinsight spark,azure spark
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: ada1c3d1482c68834dbbf5eabbd045a7e0c01f9f
ms.contentlocale: it-it
ms.lasthandoff: 07/25/2017

---
# <a name="run-interactive-queries-on-an-hdinsight-spark-cluster"></a>Eseguire query interattive in un cluster HDInsight Spark

In questo articolo si userà un notebook di Jupyter per eseguire query SQL Spark interattive su un cluster Spark. Il notebook di Jupyter è un'applicazione basata su browser che consente di estendere al Web l'esperienza interattiva basata su console. Per altre informazioni, vedere [The Jupyter notebook](http://jupyter-notebook.readthedocs.io/en/latest/notebook.html) (Il notebook di Jupyter).

In questa esercitazione si userà il kernel **PySpark** nel notebook di Jupyter per eseguire una query SQL Spark interattiva. I notebook di Jupyter nei cluster HDInsight supportano anche due altri kernel: **PySpark3** e **Spark**. Per altre informazioni sui kernel e i vantaggi offerti dall'uso di **PySpark**, vedere [Usare i kernel per Jupyter Notebook con cluster Apache Spark in HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md).

## <a name="prerequisites"></a>Prerequisiti

* **Cluster Azure HDInsight Spark**. Per istruzioni, vedere [Creare un cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="create-a-jupyter-notebook-to-run-interactive-queries"></a>Creare un notebook di Jupyter per eseguire query interattive

Per eseguire le query si useranno i dati di esempio disponibili per impostazione predefinita nella risorsa di archiviazione associata al cluster. È necessario, tuttavia, caricare prima i dati in Spark come un dataframe. Solo a questo punto sarà possibile eseguire query sul dataframe usando il notebook di Jupyter. In questa sezione si analizzerà come:

* Registrare un set di dati di esempio come un dataframe di Spark.
* Eseguire query sul dataframe.

1. Aprire il [portale di Azure](https://portal.azure.com/). Se si è scelto di aggiungere il cluster al dashboard, fare clic sul riquadro del cluster nel dashboard per avviare il relativo pannello.

    Se il cluster non è stato aggiunto al dashboard, nel riquadro sinistro fare clic su **Cluster HDInsight** e quindi sul cluster creato.

3. In **Collegamenti rapidi** fare clic su **Dashboard cluster** e quindi su **Notebook di Jupyter**. Se richiesto, immettere le credenziali per il cluster.

   ![Aprire Jupyter Notebook per eseguire la query interattiva Spark SQL](./media/hdinsight-apache-spark-load-data-run-query/hdinsight-spark-start-jupyter-interactive-spark-sql-query.png "Aprire Jupyter Notebook per eseguire la query interattiva Spark SQL")

   > [!NOTE]
   > È anche possibile accedere all'istanza di Jupyter Notebook per il cluster aprendo l'URL seguente nel browser. Sostituire **CLUSTERNAME** con il nome del cluster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. Creare un notebook. Fare clic su **Nuovo** e quindi su **PySpark**.

   ![Creare un'istanza di Jupyter Notebook per eseguire la query interattiva Spark SQL](./media/hdinsight-apache-spark-load-data-run-query/hdinsight-spark-create-jupyter-interactive-Spark-SQL-query.png "Creare un'istanza di Jupyter Notebook per eseguire la query interattiva Spark SQL")

   Un nuovo notebook verrà creato e aperto con il nome Untitled (Untitled.pynb).

4. Fare clic sul nome del notebook nella parte superiore e, se si vuole, immettere un nome descrittivo.

    ![Specificare un nome per l'istanza di Jupyter Notebook dalla quale eseguire la query interattiva Spark](./media/hdinsight-apache-spark-load-data-run-query/hdinsight-spark-jupyter-notebook-name.png "Specificare un nome per l'istanza di Jupyter Notebook dalla quale eseguire la query interattiva Spark")

5. Incollare il codice seguente in una cella vuota e quindi premere **MAIUSC + INVIO** per eseguire il codice. Il codice importa i tipi necessari per questo scenario:

        from pyspark.sql.types import *

    Poiché il notebook è stato creato tramite il kernel PySpark, non è necessario creare contesti in modo esplicito. I contesti Spark e Hive vengono creati automaticamente quando si esegue la prima cella di codice.

    ![Stato della query interattiva Spark SQL](./media/hdinsight-apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "Stato della query interattiva Spark SQL")

    Ogni volta che si esegue una query interattiva in Jupyter, il titolo della finestra del Web browser visualizza lo stato **(Occupato)** accanto al titolo del notebook. È anche visibile un cerchio pieno accanto al testo **PySpark** nell'angolo in alto a destra. Al termine del processo viene visualizzato un cerchio vuoto.

6. Prima di caricare i dati in un cluster Spark, è possibile visualizzarne di seguito uno snapshot. I dati di esempio usati in questa esercitazione sono disponibili come file CSV in tutti i cluster HDInsight Spark all'indirizzo **\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv**. I dati acquisiscono le variazioni di temperatura di un edificio. Di seguito sono riportate le prime righe di dati.

    ![Snapshot di dati per query SQL Spark interattive](./media/hdinsight-apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "Snapshot di dati per query SQL Spark interattive")

6. Creare un dataframe e una tabella temporanea (**hvac**) eseguendo il codice seguente. Ai fini di questa esercitazione, nella tabella temporanea verrà creato un numero inferiore di colonne rispetto alle colonne relative ai dati CSV non elaborati. 

        # Create an RDD from sample data
        hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        # Create a schema for our data
        Entry = Row('Date', 'Time', 'TargetTemp', 'ActualTemp', 'BuildingID')

        # Parse the data and create a schema
        hvacParts = hvacText.map(lambda s: s.split(',')).filter(lambda s: s[0] != 'Date')
        hvac = hvacParts.map(lambda p: Entry(str(p[0]), str(p[1]), int(p[2]), int(p[3]), int(p[6])))
        
        # Infer the schema and create a table       
        hvacTable = sqlContext.createDataFrame(hvac)
        hvacTable.registerTempTable('hvactemptable')
        dfw = DataFrameWriter(hvacTable)
        dfw.saveAsTable('hvac')

7. Dopo aver creato la tabella, eseguire una query interattiva sui dati usando il codice seguente.

        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

   Dato che si usa un kernel PySpark, è ora possibile eseguire direttamente una query SQL interattiva sulla tabella temporanea **hvac** creata usando il comando Magic `%%sql`. Per altre informazioni sul comando Magic `%%sql` e sugli altri comandi Magic disponibili con il kernel PySpark, vedere [Kernel disponibili per i notebook di Jupyter con cluster Spark HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

   L'output tabulare seguente viene visualizzato per impostazione predefinita.

     ![Tabella di output dei risultati della query interattiva Spark](./media/hdinsight-apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "Tabella di output dei risultati della query interattiva Spark")

    È anche possibile visualizzare i risultati in altri formati. Ad esempio, un grafico ad area per lo stesso output apparirebbe come segue.

    ![Grafico ad area dei risultati della query interattiva Spark](./media/hdinsight-apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "Grafico ad area dei risultati della query interattiva Spark")

9. Al termine dell'esecuzione dell'applicazione, arrestare il notebook per rilasciare le risorse del cluster. A tale scopo, dal menu **File** del notebook fare clic su **Close and Halt** (Chiudi e interrompi).

## <a name="next-step"></a>Passaggio successivo

In questo articolo è stata illustrata la procedura per eseguire query interattive in Spark usando il notebook di Jupyter. Passare all’articolo successivo per scoprire come eseguire lo spostamento forzato dei dati registrati in Spark in uno strumento di analisi BI come Power BI o Tableau. 

> [!div class="nextstepaction"]
>[Spark BI usando gli strumenti di visualizzazione di dati con Azure HDInsight](hdinsight-apache-spark-use-bi-tools.md)





