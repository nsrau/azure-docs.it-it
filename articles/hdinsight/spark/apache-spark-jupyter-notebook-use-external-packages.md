---
title: Usare i pacchetti Maven personalizzati con Jupyter in Spark in Azure HDInsight | Microsoft Docs
description: Istruzioni dettagliate su come configurare i notebook di Jupyter disponibili con i cluster HDInsight Spark per l'uso di pacchetti Maven personalizzati.
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2a8bc545-064e-436f-8b5f-e67c26cfbf98
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: 7df9fc58ac06ffde81df88ffd698c2527583f18f
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2018
---
# <a name="use-external-packages-with-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>Usare pacchetti esterni con notebook di Jupyter nei cluster Apache Spark in HDInsight
> [!div class="op_single_selector"]
> * [Uso di comandi Magic nelle celle](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Uso di azioni script](apache-spark-python-package-installation.md)
>
>

Informazioni su come configurare un notebook di Jupyter in un cluster Apache Spark in HDInsight per l'uso di pacchetti **maven** esterni creati dalla community e non inclusi per impostazione predefinita nel cluster. 

Per un elenco completo dei pacchetti disponibili, è possibile eseguire ricerche nel [repository Maven](http://search.maven.org/) . È anche possibile ottenere un elenco dei pacchetti disponibili da altre origini. Ad esempio, un elenco completo dei pacchetti creati dalla community è disponibile nel sito Web [spark-packages.org](http://spark-packages.org/).

In questo articolo si apprenderà a usare il pacchetto [spark-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) con Jupyter Notebook.

## <a name="prerequisites"></a>prerequisiti
È necessario disporre di quanto segue:

* Un cluster Apache Spark in HDInsight. Per istruzioni, vedere l'articolo dedicato alla [creazione di cluster Apache Spark in Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="use-external-packages-with-jupyter-notebooks"></a>Usare pacchetti esterni con i notebook Jupyter
1. Dalla Schermata iniziale del [portale di Azure](https://portal.azure.com/)fare clic sul riquadro del cluster Spark (se è stato aggiunto sulla Schermata iniziale). È anche possibile passare al cluster da **Esplora tutto** > **Cluster HDInsight**.   

2. Dal pannello del cluster Spark fare clic su **Collegamenti rapidi** e dal pannello **Dashboard cluster** fare clic su **Notebook di Jupyter**. Se richiesto, immettere le credenziali per il cluster.

    > [!NOTE]
    > È anche possibile raggiungere il notebook di Jupyter per il cluster aprendo l'URL seguente nel browser. Sostituire **CLUSTERNAME** con il nome del cluster:
    > 
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
    > 

3. Creare un nuovo notebook. Fare clic su **New** (Nuovo) e quindi su **Spark**.
   
    ![Creare un nuovo notebook Jupyter](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-create-notebook.png "Creare un nuovo notebook Jupyter")

4. Un nuovo notebook verrà creato e aperto con il nome Untitled.pynb. Fare clic sul nome del notebook nella parte superiore e immettere un nome descrittivo.
   
    ![Specificare un nome per il notebook](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-name-notebook.png "Specificare un nome per il notebook")

5. Si userà `%%configure` magic verrà usato per configurare il notebook per l'uso di un pacchetto esterno. Nei notebook che usano pacchetti esterni assicurarsi di richiamare `%%configure` magic nella prima cella del codice. Questo accorgimento garantisce che il kernel sia configurato per l'uso del pacchetto prima dell'avvio della sessione.

    >[!IMPORTANT] 
    >Se si dimentica di configurare il kernel nella prima cella, è possibile usare `%%configure` magic con il parametro `-f`. In questo modo, tuttavia, la sessione verrà riavviata e le operazioni eseguite andranno perse.

    | Versione HDInsight | Comando |
    |-------------------|---------|
    |Per HDInsight 3.3 e HDInsight 3.4 | `%%configure` <br>`{ "packages":["com.databricks:spark-csv_2.10:1.4.0"] }`|
    | Per HDInsight 3.5 e HDInsight 3.6 | `%%configure`<br>`{ "conf": {"spark.jars.packages": "com.databricks:spark-csv_2.10:1.4.0" }}`|

6. Il frammento di codice riportato sopra attende le coordinate Maven per il pacchetto esterno nel repository centrale Maven. In questo frammento di codice `com.databricks:spark-csv_2.10:1.4.0` è la coordinata Maven per il pacchetto **spark-csv** . Di seguito viene spiegato come creare le coordinate per un pacchetto.
   
    a. Individuare un pacchetto nel repository Maven. In questa esercitazione si userà [spark-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
   
    b. Recuperare dal repository i valori per **GroupId**, **ArtifactId** e **Version**. Assicurarsi che i valori che si raccolgono corrispondano al cluster. Si usano, in questo caso, un pacchetto Scala 2.10 e un pacchetto Spark 1.4.0, ma potrebbe essere necessario selezionare versioni diverse per la versione di Scala o di Spark appropriata al cluster. È possibile trovare la versione di Scala nel cluster eseguendo `scala.util.Properties.versionString` nel kernel Jupyter Spark o nell'invio di Spark. È possibile trovare la versione di Spark nel cluster eseguendo `sc.version` nei notebook Jupyter.
   
    ![Usare pacchetti esterni con notebook di Jupyter](./media/apache-spark-jupyter-notebook-use-external-packages/use-external-packages-with-jupyter.png "Usare pacchetti esterni con notebook di Jupyter")
   
    c. Concatenare i tre valori, separati da due punti (**:**).
   
        com.databricks:spark-csv_2.10:1.4.0

7. Eseguire la cella di codice con `%%configure` magic. Questa operazione configura la sessione Livy sottostante per l'uso del pacchetto fornito. Nelle celle successive del notebook è ora possibile usare il pacchetto, come mostrato di seguito.
   
        val df = sqlContext.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    Per HDInsight 3.6, usare il frammento di codice seguente.

        val df = spark.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

8. È quindi possibile eseguire i frammenti di codice, come mostrato di seguito, per visualizzare i dati del frame di dati creato nel passaggio precedente.
   
        df.show()
   
        df.select("Time").count()

## <a name="seealso"></a>Vedere anche
* [Panoramica: Apache Spark su Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenari
* [Spark con Business Intelligence: eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](apache-spark-use-bi-tools.md)
* [Spark con Machine Learning: utilizzare Spark in HDInsight per l'analisi della temperatura di compilazione utilizzando dati HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Spark con Machine Learning: utilizzare Spark in HDInsight per stimare i risultati dell'ispezione cibo](apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark: usare Spark in HDInsight per la creazione di applicazioni di streaming in tempo reale](apache-spark-eventhub-streaming.md)
* [Analisi dei log del sito Web mediante Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Creare ed eseguire applicazioni
* [Creare un'applicazione autonoma con Scala](apache-spark-create-standalone-application.md)
* [Eseguire processi in modalità remota in un cluster Spark usando Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Strumenti ed estensioni

* [Usare pacchetti Python esterni con Jupyter Notebook nei cluster Apache Spark in HDInsight Linux](apache-spark-python-package-installation.md)
* [Usare il plug-in degli strumenti HDInsight per IntelliJ IDEA per creare e inviare applicazioni Spark in Scala](apache-spark-intellij-tool-plugin.md)
* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely (Usare il plug-in Strumenti HDInsight per IntelliJ IDEA per eseguire il debug di applicazioni Spark in remoto)](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usare i notebook di Zeppelin con un cluster Spark in HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernel disponibili per notebook di Jupyter nel cluster Spark per HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Installare Jupyter nel computer e connetterlo a un cluster HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestire risorse
* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](apache-spark-resource-manager.md)
* [Tenere traccia ed eseguire il debug di processi in esecuzione nel cluster Apache Spark in Azure HDInsight](apache-spark-job-debugging.md)
