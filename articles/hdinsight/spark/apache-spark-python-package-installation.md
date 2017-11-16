---
title: 'Azione script: installare pacchetti Python con Jupyter in Azure HDInsight | Documentazione Microsoft'
description: "Istruzioni dettagliate su come usare l’azione script e configurare notebook di Jupyter disponibili con cluster HDInsight Spark per l'uso di pacchetti Python esterni."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 21978b71-eb53-480b-a3d1-c5d428a7eb5b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2017
ms.author: nitinme
ms.openlocfilehash: ab1d48188d4e0ef1274eb175b49bafd1d63b8480
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2017
---
# <a name="use-script-action-to-install-external-python-packages-for-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>Usare Azioni script per installare pacchetti Python esterni per notebook di Jupyter in cluster Apache Spark in HDInsight
> [!div class="op_single_selector"]
> * [Uso di comandi Magic nelle celle](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Uso di azioni script](apache-spark-python-package-installation.md)
>
>

Vengono fornite informazioni su come usare le azioni script per configurare un cluster Apache Spark in HDInsight (Linux) per l'uso di pacchetti **python** esterni creati dalla community non inclusi e non immediatamente disponibili nel cluster.

> [!NOTE]
> È anche possibile configurare un notebook di Jupyter con il comando Magic `%%configure` per usare pacchetti esterni. Per istruzioni, vedere [Usare pacchetti esterni con notebook di Jupyter nei cluster Apache Spark in HDInsight](apache-spark-jupyter-notebook-use-external-packages.md).
> 
> 

Per un elenco completo dei pacchetti disponibili, è possibile eseguire una ricerca nell'[indice di pacchetto](https://pypi.python.org/pypi). È anche possibile ottenere un elenco dei pacchetti disponibili da altre origini. È possibile ad esempio installare pacchetti resi disponibili tramite [Anaconda](https://docs.continuum.io/anaconda/pkg-docs) o [conda-forge](https://conda-forge.github.io/feedstocks.html).

Questo articolo descrive come installare il pacchetto [TensorFlow](https://www.tensorflow.org/) usando azioni script nel cluster e come usarlo tramite notebook di Jupyter.

## <a name="prerequisites"></a>Prerequisiti
È necessario disporre di quanto segue:

* Una sottoscrizione di Azure. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Un cluster Apache Spark in HDInsight. Per istruzioni, vedere l'articolo relativo alla [creazione di cluster Apache Spark in Azure HDInsight](apache-spark-jupyter-spark-sql.md).

   > [!NOTE]
   > Se non dispone di un cluster Spark in HDInsight Linux, è possibile eseguire le azioni script durante la creazione del cluster. Vedere la documentazione su [come usare azioni script personalizzate](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).
   > 
   > 

## <a name="use-external-packages-with-jupyter-notebooks"></a>Usare pacchetti esterni con i notebook Jupyter

1. Dalla Schermata iniziale del [portale di Azure](https://portal.azure.com/)fare clic sul riquadro del cluster Spark (se è stato aggiunto sulla Schermata iniziale). È anche possibile passare al cluster da **Esplora tutto** > **Cluster HDInsight**.   

2. Nel pannello del cluster Spark fare clic su **Azioni script** nel riquadro sinistro. Eseguire l'azione personalizzata per installare TensorFlow nei nodi head e nei nodi del ruolo di lavoro. Per informazioni sullo script Bash, fare riferimento al sito https://hdiconfigactions.blob.core.windows.net/linuxtensorflow/tensorflowinstall.sh. Vedere la documentazione su [come usare le azioni script personalizzate](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).

   > [!NOTE]
   > Nel cluster sono presenti due installazioni di Python. Spark userà l'installazione Anaconda Python che si trova in `/usr/bin/anaconda/bin`. Fare riferimento a tale installazione nelle azioni personalizzate tramite `/usr/bin/anaconda/bin/pip` e `/usr/bin/anaconda/bin/conda`.
   > 
   > 

3. Aprire un notebook PySpark Jupyter

    ![Creare un nuovo notebook Jupyter](./media/apache-spark-python-package-installation/hdinsight-spark-create-notebook.png "Creare un nuovo notebook Jupyter")

4. Un nuovo notebook verrà creato e aperto con il nome Untitled.pynb. Fare clic sul nome del notebook nella parte superiore e immettere un nome descrittivo.

    ![Specificare un nome per il notebook](./media/apache-spark-python-package-installation/hdinsight-spark-name-notebook.png "Specificare un nome per il notebook")

5. A questo punto verranno eseguiti il codice relativo a `import tensorflow` e l'esempio di tipo hello world. 

    Codice da copiare:

        import tensorflow as tf
        hello = tf.constant('Hello, TensorFlow!')
        sess = tf.Session()
        print(sess.run(hello))

    Il risultato avrà l'aspetto seguente:
    
    ![Esecuzione del codice TensorFlow](./media/apache-spark-python-package-installation/execution.png "Esecuzione del codice TensorFlow")



## <a name="seealso"></a>Vedere anche
* [Panoramica: Apache Spark su Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenari
* [Spark con Business Intelligence: eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](apache-spark-use-bi-tools.md)
* [Spark con Machine Learning: utilizzare Spark in HDInsight per l'analisi della temperatura di compilazione utilizzando dati HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Spark con Machine Learning: usare Spark in HDInsight per prevedere i risultati del controllo degli alimenti](apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark: usare Spark in HDInsight per la creazione di applicazioni di streaming in tempo reale](apache-spark-eventhub-streaming.md)
* [Analisi dei log del sito Web mediante Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Creare ed eseguire applicazioni
* [Creare un'applicazione autonoma con Scala](apache-spark-create-standalone-application.md)
* [Eseguire processi in modalità remota in un cluster Spark usando Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Strumenti ed estensioni
* [Usare pacchetti esterni con notebook di Jupyter nei cluster Apache Spark in HDInsight](apache-spark-jupyter-notebook-use-external-packages.md)
* [Usare il plug-in degli strumenti HDInsight per IntelliJ IDEA per creare e inviare applicazioni Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely (Usare il plug-in Strumenti HDInsight per IntelliJ IDEA per eseguire il debug di applicazioni Spark in remoto)](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usare i notebook di Zeppelin con un cluster Spark in HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernel disponibili per notebook di Jupyter nel cluster Spark per HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Installare Jupyter nel computer e connetterlo a un cluster HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestire risorse
* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](apache-spark-resource-manager.md)
* [Tenere traccia ed eseguire il debug di processi in esecuzione nel cluster Apache Spark in Azure HDInsight](apache-spark-job-debugging.md)
