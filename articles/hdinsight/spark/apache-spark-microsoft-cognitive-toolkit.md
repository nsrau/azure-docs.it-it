---
title: Microsoft Cognitive Toolkit con Apache Spark-Azure HDInsight
description: Informazioni su come un modello con training per l'apprendimento approfondito Microsoft Cognitive Toolkit possa essere applicato a un set di dati tramite l'API Python Spark in un cluster Azure HDInsight Spark.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/14/2020
ms.openlocfilehash: 1933db624dfef2ffa747ecb043be6730b6b884b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206555"
---
# <a name="use-microsoft-cognitive-toolkit-deep-learning-model-with-azure-hdinsight-spark-cluster"></a>Usare il modello di apprendimento approfondito Microsoft Cognitive Toolkit con un cluster Azure HDInsight Spark

In questo articolo viene illustrata la procedura seguente.

1. Eseguire uno script personalizzato per installare [Microsoft cognitive Toolkit](https://docs.microsoft.com/cognitive-toolkit/) in un cluster Azure HDInsight Spark.

2. Caricare un oggetto [Jupyter Notebook](https://jupyter.org/) nel cluster [Apache Spark](https://spark.apache.org/) per vedere come applicare ai file un modello con training di apprendimento approfondito di Microsoft Cognitive Toolkit in un account di Archiviazione BLOB di Azure tramite l'[API Python Spark (PySpark)](https://spark.apache.org/docs/latest/api/python/index.html)

## <a name="prerequisites"></a>Prerequisiti

* Un cluster Apache Spark in HDInsight. Vedere [Creare un cluster Apache Spark](./apache-spark-jupyter-spark-sql-use-portal.md).

* Familiarità nell'uso di Jupyter Notebook con Spark in HDInsight. Per altre informazioni, vedere l'articolo su come [caricare i dati ed eseguire query con Apache Spark in HDInsight](./apache-spark-load-data-run-query.md).

## <a name="how-does-this-solution-flow"></a>Svolgimento della soluzione

Questa soluzione è divisa tra questo articolo e un notebook di Jupyter caricato come parte di questo articolo. In questo articolo verrà completata la procedura seguente:

* Eseguire un'azione script in un cluster HDInsight Spark per installare i pacchetti Microsoft Cognitive Toolkit e Python.
* Caricare il notebook di Jupyter che esegue la soluzione nel cluster HDInsight Spark.

I passaggi rimanenti elencati sotto vengono trattati nel notebook di Jupyter.

* Caricare immagini di esempio in un set di dati distribuito con resilienza Spark o RDD.
  * Caricare i moduli e definire i set di impostazioni.
  * Scaricare il set di dati in locale nel cluster Spark.
  * Convertire il set di dati in RDD.
* Classificare le immagini tramite un modello con training Cognitive Toolkit.
  * Scaricare il modello con training Cognitive Toolkit nel cluster Spark.
  * Definire le funzioni usate dai nodi del ruolo di lavoro.
  * Classificare le immagini nei nodi del ruolo di lavoro.
  * Valutare l'accuratezza del modello.

## <a name="install-microsoft-cognitive-toolkit"></a>Installare Microsoft Cognitive Toolkit

È possibile installare Microsoft Cognitive Toolkit in un cluster Spark tramite l'azione script. L'azione script usa script personalizzati per installare i componenti nel cluster che non sono disponibili per impostazione predefinita. È possibile usare lo script personalizzato dalla portale di Azure, usando HDInsight .NET SDK oppure Azure PowerShell. È possibile usare lo script anche per installare il toolkit sia nell’ambito della creazione del cluster sia quando il cluster è in esecuzione.

In questo articolo il toolkit verrà installato dal portale, dopo la creazione del cluster. Per altri modi di eseguire lo script personalizzato, vedere [Personalizzare cluster HDInsight tramite azione script](../hdinsight-hadoop-customize-cluster-linux.md).

### <a name="using-the-azure-portal"></a>Uso del portale di Azure

Per istruzioni su come usare il portale di Azure per eseguire l'azione script, vedere [personalizzare cluster HDInsight mediante azione script](../hdinsight-hadoop-customize-cluster-linux.md#script-action-during-cluster-creation). Assicurarsi di specificare i dati seguenti per installare Microsoft Cognitive Toolkit. Usare i valori seguenti per l'azione script:

|Proprietà |Valore |
|---|---|
|Tipo di script|- Personalizzato|
|Nome| Installare MCT|
|URI script Bash|`https://raw.githubusercontent.com/Azure-Samples/hdinsight-pyspark-cntk-integration/master/cntk-install.sh`|
|Tipi di nodo:|Head, ruolo di lavoro|
|Parametri|nessuno|

## <a name="upload-the-jupyter-notebook-to-azure-hdinsight-spark-cluster"></a>Caricare il notebook di Jupyter nel cluster Azure HDInsight Spark

Per usare Microsoft Cognitive Toolkit con il cluster Azure HDInsight Spark, è necessario caricare il notebook di Jupyter **CNTK_model_scoring_on_Spark_walkthrough.ipynb** nel cluster Azure HDInsight Spark. Questo notebook è disponibile su GitHub all' [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration)indirizzo.

1. Scaricare e decomprimere [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration).

1. Da un Web browser passare a `https://CLUSTERNAME.azurehdinsight.net/jupyter`, dove `CLUSTERNAME` è il nome del cluster.

1. Dal notebook di Jupyter selezionare **carica** nell'angolo in alto a destra, quindi passare al download e selezionare file `CNTK_model_scoring_on_Spark_walkthrough.ipynb`.

    ![Caricare il notebook di Jupyter nel cluster Azure HDInsight Spark](./media/apache-spark-microsoft-cognitive-toolkit/hdinsight-microsoft-cognitive-toolkit-load-jupyter-notebook.png "Caricare il notebook di Jupyter nel cluster Azure HDInsight Spark")

1. Selezionare di nuovo **carica** .

1. Dopo aver caricato il notebook, fare clic sul nome del notebook e quindi seguire le istruzioni nel notebook stesso per caricare il set di dati ed eseguire l'articolo.

## <a name="see-also"></a>Vedi anche

* [Panoramica: Apache Spark su Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenari

* [Apache Spark con Business Intelligence: eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](apache-spark-use-bi-tools.md)
* [Apache Spark con Machine Learning: utilizzare Spark in HDInsight per l'analisi della temperatura di compilazione utilizzando dati HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark con Machine Learning: utilizzare Spark in HDInsight per prevedere i risultati di un controllo alimentare](apache-spark-machine-learning-mllib-ipython.md)
* [Analisi dei log del sito Web con Apache Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Analisi dei dati di telemetria di Application Insights con Apache Spark in HDInsight](apache-spark-analyze-application-insight-logs.md)

### <a name="create-and-run-applications"></a>Creare ed eseguire applicazioni

* [Creare un'applicazione autonoma con Scala](apache-spark-create-standalone-application.md)
* [Eseguire processi in modalità remota in un cluster Apache Spark usando Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Strumenti ed estensioni

* [Usare il plug-in degli strumenti HDInsight per IntelliJ IDEA per creare e inviare applicazioni Spark in Scala](apache-spark-intellij-tool-plugin.md)
* [Usare il plug-in Strumenti HDInsight per IntelliJ IDEA per eseguire il debug di applicazioni Apache Spark in remoto](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usare i notebook di Apache Zeppelin con un cluster Apache Spark in HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernel disponibili per notebook di Jupyter nel cluster Apache Spark per HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Usare pacchetti esterni con i notebook Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installare Jupyter Notebook nel computer e connetterlo a un cluster HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestione di risorse

* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](apache-spark-resource-manager.md)
* [Tenere traccia ed eseguire il debug di processi in esecuzione nel cluster Apache Spark in Azure HDInsight](apache-spark-job-debugging.md)
