---
title: 'Esercitazione: Caricare i dati ed eseguire query in un cluster Apache Spark in Azure HDInsight '
description: Informazioni su come caricare i dati ed eseguire query interattive nei cluster Spark in Azure HDInsight.
services: azure-hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.author: jasonh
ms.date: 05/17/2018
ms.openlocfilehash: e83c7a4ed5987108a74153897abe4839dd24418c
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2018
ms.locfileid: "39619431"
---
# <a name="tutorial-load-data-and-run-queries-on-an-apache-spark-cluster-in-azure-hdinsight"></a>Esercitazione: Caricare i dati ed eseguire query in un cluster Apache Spark in Azure HDInsight

In questa esercitazione si apprenderà come usare un frame di dati da un file csv e come eseguire query interattive Spark SQL su un cluster Apache Spark in HDInsight di Azure. In Spark un frame di dati è una raccolta distribuita di dati organizzati in colonne denominate. Dal punto di vista concettuale in frame di dati equivale a una tabella in un database relazionale o a un frame di dati in R/Python.
 
In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Creare un frame di dati da un file csv
> * Eseguire query sul frame di dati

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* Completare [Creare un cluster Apache Spark in Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="create-a-dataframe-from-a-csv-file"></a>Creare un frame di dati da un file csv

Le applicazioni possono creare frame di dati da un set di dati RDD (Resilient Distributed Dataset) esistente, da una tabella Hive o da origini dati usando l'oggetto SQLContext. Lo screenshot seguente mostra uno snapshot del file HVAC.csv usato in questa esercitazione. Il file csv è disponibile con tutti i cluster HDInsight Spark. I dati acquisiscono le variazioni di temperatura di alcuni edifici.
    
![Snapshot di dati per query SQL Spark interattive](./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "Snapshot di dati per query SQL Spark interattive")


1. Aprire il Jupyter notebook creato nella sezione dei prerequisiti.
2. Incollare il codice seguente in una cella vuota del notebook e quindi premere **MAIUSC+INVIO** per eseguire il codice. Il codice importa i tipi necessari per questo scenario:

    ```PySpark
    from pyspark.sql import *
    from pyspark.sql.types import *
    ```

    Quando si esegue una query interattiva in Jupyter, la barra del titolo della scheda o della finestra del Web browser visualizza lo stato **(Occupato)** accanto al titolo del notebook. È anche visibile un cerchio pieno accanto al testo **PySpark** nell'angolo in alto a destra. Al termine del processo viene visualizzato un cerchio vuoto.

    ![Stato della query interattiva Spark SQL](./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "Stato della query interattiva Spark SQL")

3. Eseguire il codice seguente per creare un frame di dati e una tabella temporanea (**hvac**). 

    ```PySpark
    # Create an RDD from sample data
    csvFile = spark.read.csv('wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv', header=True, inferSchema=True)
    csvFile.write.saveAsTable("hvac")
    ```

    > [!NOTE]
    > Usando il kernel PySpark per creare un notebook, i contesti SQL vengono creati automaticamente quando si esegue la prima cella di codice. Non è necessario creare in modo esplicito i contesti.


## <a name="run-queries-on-the-dataframe"></a>Eseguire query sul frame di dati

Dopo aver creato la tabella, è possibile eseguire una query interattiva sui dati.

1. Eseguire il codice seguente in una cella vuota del notebook:

    ```PySpark
    %%sql
    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```

   Poiché nel notebook viene usato il kernel PySpark, è ora possibile eseguire direttamente una query SQL interattiva sulla tabella temporanea **hvac**.

   Viene visualizzato l'output tabulare seguente.

     ![Tabella di output dei risultati della query interattiva Spark](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "Tabella di output dei risultati della query interattiva Spark")

3. È anche possibile visualizzare i risultati in altri formati. Per visualizzare un grafico ad area per lo stesso output, selezionare **Area** e quindi impostare altri valori, come illustrato.

    ![Grafico ad area dei risultati della query interattiva Spark](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "Grafico ad area dei risultati della query interattiva Spark")

10. Dal menu **File** del notebook scegliere **Save and Checkpoint** (Salva e checkpoint). 

11. Se si intende iniziare l'[esercitazione successiva](apache-spark-use-bi-tools.md) a questo punto, lasciare aperto il notebook. In caso contrario, arrestare il notebook per rilasciare le risorse cluster: dal menu **File** nel notebook scegliere **Close and Halt** (Chiudi e interrompi).

## <a name="clean-up-resources"></a>Pulire le risorse

Con HDInsight i dati vengono salvati in Archiviazione di Azure o in Azure Data Lake Store in modo che sia possibile eliminare tranquillamente un cluster quando non viene usato. Vengono addebitati i costi anche per i cluster HDInsight che non sono in uso. Poiché i costi per il cluster sono decisamente superiori a quelli per l'archiviazione, economicamente ha senso eliminare i cluster quando non vengono usati. Se si prevede di lavorare immediatamente nell'esercitazione successiva, si potrebbe voler mantenere il cluster.

Aprire il cluster nel portale di Azure e selezionare **Elimina**.

![Eliminare cluster HDInsight](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "Eliminare cluster HDInsight")

È anche possibile selezionare il nome del gruppo di risorse per aprire la pagina del gruppo di risorse e quindi selezionare **Elimina gruppo di risorse**. Eliminando il gruppo di risorse, si elimina sia il cluster HDInsight Spark che l'account di archiviazione predefinito.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

* Creare un frame di dati di Spark.
* Eseguire Spark SQL sul frame di dati.

Passare all'articolo successivo per scoprire come eseguire il pull dei dati registrati in Spark in uno strumento di analisi BI come Power BI. 
> [!div class="nextstepaction"]
> [Analizzare i dati usando gli strumenti di business intelligence](apache-spark-use-bi-tools.md)

