---
title: Usare pacchetti Maven personalizzati con Jupyter in Spark-Azure HDInsight
description: Istruzioni dettagliate su come configurare i notebook di Jupyter disponibili con i cluster HDInsight Spark per l'uso di pacchetti Maven personalizzati.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/22/2019
ms.openlocfilehash: cec94b2ecb18bc9e8cceb24a21967a3c829d78a5
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561715"
---
# <a name="use-external-packages-with-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>Usare pacchetti esterni con notebook di Jupyter nei cluster Apache Spark in HDInsight

> [!div class="op_single_selector"]
> * [Uso di comandi Magic nelle celle](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Uso di azioni script](apache-spark-python-package-installation.md)

Informazioni su come configurare un [Jupyter notebook](https://jupyter.org/) in Apache Spark cluster in HDInsight per l'uso di pacchetti di Apache **Maven** esterni e creati dalla community che non sono inclusi nel cluster.

Per un elenco completo dei pacchetti disponibili, è possibile eseguire ricerche nel [repository Maven](https://search.maven.org/) . È anche possibile ottenere un elenco dei pacchetti disponibili da altre origini. Ad esempio, un elenco completo dei pacchetti creati dalla community è disponibile nel sito Web [spark-packages.org](https://spark-packages.org/).

Questo articolo illustra come usare il pacchetto [Spark-CSV](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) con Jupyter notebook.

## <a name="prerequisites"></a>Prerequisiti

* Un cluster Apache Spark in HDInsight. Per istruzioni, vedere l'articolo dedicato alla [creazione di cluster Apache Spark in Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* Familiarità nell'uso di Jupyter Notebook con Spark in HDInsight. Per altre informazioni, vedere l'articolo su come [caricare i dati ed eseguire query con Apache Spark in HDInsight](./apache-spark-load-data-run-query.md).

* Lo [schema URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) per l'archiviazione primaria dei cluster. Corrisponde a `wasb://` per Archiviazione di Azure, a `abfs://` per Azure Data Lake Storage Gen2 e a `adl://` per Azure Data Lake Storage Gen1. Se il trasferimento sicuro è abilitato per Archiviazione di Azure o Data Lake Storage Gen2, l'URI è rispettivamente `wasbs://` o `abfss://`. Vedere anche [trasferimento sicuro](../../storage/common/storage-require-secure-transfer.md).

## <a name="use-external-packages-with-jupyter-notebooks"></a>Usare pacchetti esterni con i notebook Jupyter

1. Passare a `https://CLUSTERNAME.azurehdinsight.net/jupyter` dove `CLUSTERNAME` è il nome del cluster Spark.

1. Creare un nuovo notebook. Selezionare **nuovo**e quindi **Spark**.

    ![Creare un nuovo notebook di Jupyter Spark](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-create-notebook.png "Creare un nuovo notebook Jupyter")

1. Un nuovo notebook verrà creato e aperto con il nome Untitled.pynb. Selezionare il nome del notebook nella parte superiore e immettere un nome descrittivo.

    ![Specificare un nome per il notebook](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-name-notebook.png "Specificare un nome per il notebook")

1. Si userà il `%%configure` Magic per configurare il notebook per l'uso di un pacchetto esterno. Nei notebook che usano pacchetti esterni assicurarsi di richiamare `%%configure` magic nella prima cella del codice. Questo accorgimento garantisce che il kernel sia configurato per l'uso del pacchetto prima dell'avvio della sessione.

    >[!IMPORTANT]  
    >Se si dimentica di configurare il kernel nella prima cella, è possibile usare `%%configure` magic con il parametro `-f`. In questo modo, tuttavia, la sessione verrà riavviata e le operazioni eseguite andranno perse.

    | Versione HDInsight | Comando |
    |-------------------|---------|
    | Per HDInsight 3.5 e HDInsight 3.6 | `%%configure`<br>`{ "conf": {"spark.jars.packages": "com.databricks:spark-csv_2.11:1.5.0" }}`|
    |Per HDInsight 3.3 e HDInsight 3.4 | `%%configure` <br>`{ "packages":["com.databricks:spark-csv_2.10:1.4.0"] }`|

1. Il frammento di codice riportato sopra attende le coordinate Maven per il pacchetto esterno nel repository centrale Maven. In questo frammento di codice `com.databricks:spark-csv_2.11:1.5.0` è la coordinata Maven per il pacchetto **spark-csv** . Di seguito viene spiegato come creare le coordinate per un pacchetto.

    a. Individuare un pacchetto nel repository Maven. Per questo articolo, si usa [Spark-CSV](https://mvnrepository.com/artifact/com.databricks/spark-csv).

    b. Recuperare dal repository i valori per **GroupId**, **ArtifactId** e **Version**. Assicurarsi che i valori che si raccolgono corrispondano al cluster. In questo caso, viene usato un pacchetto scala 2,11 ed Spark 1.5.0, ma potrebbe essere necessario selezionare versioni diverse per la versione di scala o Spark appropriata nel cluster. È possibile trovare la versione di Scala nel cluster eseguendo `scala.util.Properties.versionString` nel kernel Jupyter Spark o nell'invio di Spark. È possibile trovare la versione di Spark nel cluster eseguendo `sc.version` nei notebook Jupyter.

    ![Usare pacchetti esterni con Jupyter notebook](./media/apache-spark-jupyter-notebook-use-external-packages/use-external-packages-with-jupyter.png "Usare pacchetti esterni con Jupyter Notebook")

    c. Concatenare i tre valori, separati da due punti ( **:** ).

        com.databricks:spark-csv_2.11:1.5.0

1. Eseguire la cella di codice con `%%configure` magic. Questa operazione configura la sessione Livy sottostante per l'uso del pacchetto fornito. Nelle celle successive del notebook è ora possibile usare il pacchetto, come mostrato di seguito.

        val df = spark.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    Per HDInsight 3,4 e versioni precedenti, è necessario usare il frammento di codice seguente.

        val df = sqlContext.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

1. È quindi possibile eseguire i frammenti di codice, come mostrato di seguito, per visualizzare i dati del frame di dati creato nel passaggio precedente.

        df.show()
   
        df.select("Time").count()

## <a name="seealso"></a>Vedere anche

* [Panoramica: Apache Spark su Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenari

* [Apache Spark con BI: eseguire l'analisi interattiva dei dati mediante Spark in HDInsight con strumenti di BI](apache-spark-use-bi-tools.md)
* [Apache Spark with Machine Learning: Use Spark in HDInsight for analyzing building temperature using HVAC data](apache-spark-ipython-notebook-machine-learning.md) (Apache Spark con Machine Learning: usare Spark in HDInsight per analizzare la temperatura di un edificio usando dati HVAC)
* [Apache Spark con Machine Learning: utilizzare Spark in HDInsight per stimare i risultati dell'ispezione cibo](apache-spark-machine-learning-mllib-ipython.md)
* [Analisi dei log del sito Web con Apache Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Creare ed eseguire applicazioni

* [Creare un'applicazione autonoma con Scala](apache-spark-create-standalone-application.md)
* [Eseguire processi in modalità remota in un cluster Apache Spark usando Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Strumenti ed estensioni

* [Usare pacchetti Python esterni con Jupyter Notebook nei cluster Apache Spark in HDInsight Linux](apache-spark-python-package-installation.md)
* [Usare il plug-in degli strumenti HDInsight per IntelliJ IDEA per creare e inviare applicazioni Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Usare il plug-in Strumenti HDInsight per IntelliJ IDEA per eseguire il debug di applicazioni Apache Spark in remoto](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usare i notebook di Apache Zeppelin con un cluster Apache Spark in HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernel disponibili per notebook di Jupyter nel cluster Apache Spark per HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Installare Jupyter nel computer e connetterlo a un cluster HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestire risorse

* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](apache-spark-resource-manager.md)
* [Tenere traccia ed eseguire il debug di processi in esecuzione nel cluster Apache Spark in Azure HDInsight](apache-spark-job-debugging.md)
