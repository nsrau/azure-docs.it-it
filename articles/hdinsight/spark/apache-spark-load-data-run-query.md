---
title: Eseguire query interattive in un cluster Azure HDInsight Spark | Microsoft Docs
description: Guida introduttiva di HDInsight Spark per la creazione di un cluster Apache Spark in HDInsight.
keywords: guida introduttiva spark,spark interattivo,query interattiva,hdinsight spark,azure spark
services: hdinsight
documentationcenter: 
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: jgao
ms.openlocfilehash: 78ab44a7afa6523e1e9e4082b3f45b1a28affe77
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2017
---
# <a name="run-interactive-queries-on-spark-clusters-in-hdinsight"></a>Eseguire query interattive nei cluster Spark in HDInsight

Informazioni su come usare il notebook di Jupyter per eseguire query SQL Spark interattive su un cluster Spark. 

Il [notebook di Jupyter](http://jupyter-notebook.readthedocs.io/en/latest/notebook.html) è un'applicazione basata su browser che consente di estendere al Web l'esperienza interattiva basata su console. Spark in HDInsight include anche [Zeppelin Notebook](apache-spark-zeppelin-notebook.md). Per questa esercitazione viene usato Jupyter Notebook.

Gli oggetti Jupyter Notebook nei cluster HDInsight supportano tre kernel: **PySpark**, **PySpark3** e **Spark**. In questa esercitazione viene usato il kernel **PySpark**. Per altre informazioni sui kernel e i vantaggi offerti dall'uso di **PySpark**, vedere [Usare i kernel per Jupyter Notebook con cluster Apache Spark in HDInsight](apache-spark-jupyter-notebook-kernels.md). Per usare il notebook di Zeppelin, vedere [Usare i notebook di Zeppelin con cluster Apache Spark in Azure HDInsight](./apache-spark-zeppelin-notebook.md).

In questa esercitazione viene eseguita una query dei dati in un file csv. È necessario caricare prima i dati in Spark come frame di dati. È quindi possibile eseguire query sul frame di dati usando il notebook di Jupyter. 

## <a name="prerequisites"></a>prerequisiti

* **Cluster Azure HDInsight Spark**. Per le istruzioni, vedere [Creare un cluster Apache Spark in Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* **Notebook di Jupyter con PySpark**. Per le istruzioni, vedere [Creare un notebook di Jupyter](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).

## <a name="create-a-dataframe-from-a-csv-file"></a>Creare un frame di dati da un file csv

Con SQLContext, le applicazioni possono creare frame di dati da un set di dati RDD esistente, da una tabella Hive o da origini dati. 

**Per creare un frame di dati da un file csv**

1. Creare un notebook di Jupyter usando PySpark, se necessario. Per le istruzioni, vedere [Creare un notebook di Jupyter](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).

2. Incollare il codice seguente in una cella vuota del notebook e quindi premere **MAIUSC+INVIO** per eseguire il codice. Il codice importa i tipi necessari per questo scenario:

    ```PySpark
    from pyspark.sql import *
    from pyspark.sql.types import *
    ```
    Usando il kernel PySpark per creare un notebook, i contesti Spark e Hive vengono creati automaticamente quando si esegue la prima cella di codice. Non è necessario creare in modo esplicito i contesti.

    Quando si esegue una query interattiva in Jupyter, la barra del titolo della scheda o della finestra del Web browser visualizza lo stato **(Occupato)** accanto al titolo del notebook. È anche visibile un cerchio pieno accanto al testo **PySpark** nell'angolo in alto a destra. Al termine del processo viene visualizzato un cerchio vuoto.

    ![Stato della query interattiva Spark SQL](./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "Stato della query interattiva Spark SQL")

3. Eseguire il codice seguente per creare un frame di dati e una tabella temporanea (**hvac**). Il codice non estrae tutte le colonne disponibili nel file CSV. 

    ```PySpark
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
    ```
    Lo screenshot seguente mostra uno snapshot del file HVAC.csv. Il file CSV è disponibile con tutti i cluster HDInsight Spark. I dati acquisiscono le variazioni di temperatura di un edificio.

    ![Snapshot di dati per query SQL Spark interattive](./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "Snapshot di dati per query SQL Spark interattive")

## <a name="run-queries-on-the-dataframe"></a>Eseguire query sul frame di dati

Dopo aver creato la tabella, è possibile eseguire una query interattiva sui dati.

**Per eseguire una query**

1. Eseguire il codice seguente in una cella vuota del notebook:

    ```PySpark
    %%sql
    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```

   Poiché nel notebook viene usato il kernel PySpark, è ora possibile eseguire direttamente una query SQL interattiva sulla tabella temporanea **hvac** creata usando il comando Magic `%%sql`. Per altre informazioni sul comando Magic `%%sql` e sugli altri comandi Magic disponibili con il kernel PySpark, vedere [Kernel disponibili per i notebook di Jupyter con cluster Spark HDInsight](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

   L'output tabulare seguente viene visualizzato per impostazione predefinita.

     ![Tabella di output dei risultati della query interattiva Spark](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "Tabella di output dei risultati della query interattiva Spark")

3. È anche possibile visualizzare i risultati in altri formati. Per visualizzare un grafico ad area per lo stesso output, selezionare **Area** e quindi impostare altri valori, come illustrato.

    ![Grafico ad area dei risultati della query interattiva Spark](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "Grafico ad area dei risultati della query interattiva Spark")

10. Dal menu **File** del notebook scegliere **Save and Checkpoint** (Salva e checkpoint). 

11. Se si intende iniziare l'[esercitazione successiva](apache-spark-use-bi-tools.md) a questo punto, lasciare aperto il notebook. In caso contrario, arrestare il notebook per rilasciare le risorse cluster: dal menu **File** nel notebook scegliere **Close and Halt** (Chiudi e interrompi).

## <a name="next-step"></a>Passaggio successivo

In questo articolo è stata illustrata la procedura per eseguire query interattive in Spark usando il notebook di Jupyter. Passare all’articolo successivo per scoprire come eseguire lo spostamento forzato dei dati registrati in Spark in uno strumento di analisi BI come Power BI o Tableau. 

> [!div class="nextstepaction"]
>[Spark BI usando gli strumenti di visualizzazione di dati con Azure HDInsight](apache-spark-use-bi-tools.md)




