---
title: Usare pacchetti esterni con notebook di Jupyter nei cluster Apache Spark in HDInsight | Documentazione Microsoft
description: Istruzioni dettagliate su come configurare notebook Jupyter disponibili con cluster HDInsight Spark per l&quot;uso di pacchetti Spark esterni.
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2a8bc545-064e-436f-8b5f-e67c26cfbf98
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 53753790b63d783a284aa0db39841c4bd6a8a0aa
ms.openlocfilehash: 11bcf7f4f51d105f6693545669a2f17af196bf57


---
# <a name="use-external-packages-with-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight-linux"></a>Utilizzare pacchetti esterni con notebook di Jupyter nei cluster Apache Spark in HDInsight Linux

Vengono fornite informazioni su come configurare Jupyter Notebook in un cluster Apache Spark in HDInsight (Linux) per l'uso di pacchetti **maven** esterni creati dalla community e non inclusi e subito utilizzabili nel cluster. 

Per un elenco completo dei pacchetti disponibili, è possibile eseguire ricerche nel [repository Maven](http://search.maven.org/) . È anche possibile ottenere un elenco dei pacchetti disponibili da altre origini. Ad esempio, un elenco completo dei pacchetti creati dalla community è disponibile nel sito Web [spark-packages.org](http://spark-packages.org/).

In questo articolo si apprenderà a usare il pacchetto [spark-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) con Jupyter Notebook.



## <a name="prerequisites"></a>Prerequisiti
È necessario disporre di quanto segue:

* Una sottoscrizione di Azure. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Un cluster Apache Spark in HDInsight Linux. Per istruzioni, vedere l'articolo relativo alla [creazione di cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="use-external-packages-with-jupyter-notebooks"></a>Usare pacchetti esterni con i notebook Jupyter
1. Dalla Schermata iniziale del [portale di Azure](https://portal.azure.com/)fare clic sul riquadro del cluster Spark (se è stato aggiunto sulla Schermata iniziale). È anche possibile passare al cluster da **Esplora tutto** > **Cluster HDInsight**.   
2. Dal pannello del cluster Spark fare clic su **Collegamenti rapidi** e dal pannello **Dashboard cluster** fare clic su **Notebook di Jupyter**. Se richiesto, immettere le credenziali per il cluster.

   
   > [!NOTE]
   > È anche possibile raggiungere il notebook di Jupyter per il cluster aprendo l'URL seguente nel browser. Sostituire **CLUSTERNAME** con il nome del cluster:
   > 
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   > 


3. Creare un nuovo notebook. Fare clic su **New** (Nuovo) e quindi su **Spark**.
   
    ![Creare un nuovo notebook Jupyter](./media/hdinsight-apache-spark-jupyter-notebook-use-external-packages/hdispark.note.jupyter.createnotebook.png "Create a new Jupyter notebook")

4. Un nuovo notebook verrà creato e aperto con il nome Untitled.pynb. Fare clic sul nome del notebook nella parte superiore e immettere un nome descrittivo.
   
    ![Specificare un nome per il notebook](./media/hdinsight-apache-spark-jupyter-notebook-use-external-packages/hdispark.note.jupyter.notebook.name.png "Provide a name for the notebook")

5. Si userà `%%configure` magic verrà usato per configurare il notebook per l'uso di un pacchetto esterno. Nei notebook che usano pacchetti esterni assicurarsi di richiamare `%%configure` magic nella prima cella del codice. Questo accorgimento garantisce che il kernel sia configurato per l'uso del pacchetto prima dell'avvio della sessione.

    >[!IMPORTANT] 
    >Se si dimentica di configurare il kernel nella prima cella, è possibile usare `%%configure` magic con il parametro `-f`. In questo modo, tuttavia, la sessione verrà riavviata e le operazioni eseguite andranno perse.

    | Versione HDInsight | Comando |
    |-------------------|---------|
    |Per HDInsight 3.3 e HDInsight 3.4 | `%%configure` <br>`{ "packages":["com.databricks:spark-csv_2.10:1.4.0"] }`|
    | Per HDInsight 3.5 | `%%configure`<br>`{ "conf": {"spark.jars.packages": "com.databricks:spark-csv_2.10:1.4.0" }}`|

6. Il frammento di codice riportato sopra attende le coordinate Maven per il pacchetto esterno nel repository centrale Maven. In questo frammento di codice `com.databricks:spark-csv_2.10:1.4.0` è la coordinata Maven per il pacchetto **spark-csv** . Di seguito viene spiegato come creare le coordinate per un pacchetto.
   
    a. Individuare un pacchetto nel repository Maven. In questa esercitazione si userà [spark-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
   
    b. Recuperare dal repository i valori per **GroupId**, **ArtifactId** e **Version**.
   
    ![Usare pacchetti esterni con Jupyter Notebook](./media/hdinsight-apache-spark-jupyter-notebook-use-external-packages/use-external-packages-with-jupyter.png "Use external packages with Jupyter notebook")
   
    c. Concatenare i tre valori, separati da due punti (**:**).
   
        com.databricks:spark-csv_2.10:1.4.0

7. Eseguire la cella di codice con `%%configure` magic. Questa operazione configura la sessione Livy sottostante per l'uso del pacchetto fornito. Nelle celle successive del notebook è ora possibile usare il pacchetto, come mostrato di seguito.
   
        val df = sqlContext.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

8. È quindi possibile eseguire i frammenti di codice, come mostrato di seguito, per visualizzare i dati del frame di dati creato nel passaggio precedente.
   
        df.show()
   
        df.select("Time").count()

## <a name="a-nameseealsoasee-also"></a><a name="seealso"></a>Vedere anche
* [Panoramica: Apache Spark su Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Scenari
* [Spark con Business Intelligence: eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](hdinsight-apache-spark-use-bi-tools.md)
* [Spark con Machine Learning: utilizzare Spark in HDInsight per l'analisi della temperatura di compilazione utilizzando dati HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark con Machine Learning: usare Spark in HDInsight per prevedere i risultati del controllo degli alimenti](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark: usare Spark in HDInsight per la creazione di applicazioni di streaming in tempo reale](hdinsight-apache-spark-eventhub-streaming.md)
* [Analisi dei log del sito Web mediante Spark in HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Creare ed eseguire applicazioni
* [Creare un'applicazione autonoma con Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Eseguire processi in modalità remota in un cluster Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Strumenti ed estensioni

* [Usare pacchetti Python esterni con Jupyter Notebook nei cluster Apache Spark in HDInsight Linux](hdinsight-apache-spark-python-package-installation.md)
* [Usare il plug-in degli strumenti HDInsight per IntelliJ IDEA per creare e inviare applicazioni Spark in Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely (Usare il plug-in Strumenti HDInsight per IntelliJ IDEA per eseguire il debug di applicazioni Spark in remoto)](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usare i notebook di Zeppelin con un cluster Spark in HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)
* [Kernel disponibili per notebook di Jupyter nel cluster Spark per HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Installare Jupyter nel computer e connetterlo a un cluster HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestire risorse
* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Tenere traccia ed eseguire il debug di processi in esecuzione nel cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-job-debugging.md)




<!--HONumber=Dec16_HO1-->


