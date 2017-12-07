---
title: Come usare MMLSpark Machine Learning | Microsoft Docs
description: Informazioni su come usare la libreria MMLSpark con Azure Machine Learning.
services: machine-learning
author: rastala
ms.author: roastala
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/26/2017
ms.openlocfilehash: 228ff59e2a495e8ca7c310214327bc846ecfb828
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2017
---
# <a name="how-to-use-microsoft-machine-learning-library-for-apache-spark"></a>Informazioni su come usare la libreria Microsoft Machine Learning per Apache Spark

## <a name="introduction"></a>Introduzione

[La libreria Microsoft Machine Learning per Apache Spark](https://github.com/Azure/mmlspark) (MMLSpark) fornisce strumenti che consentono di creare con facilità modelli dimensionabili di machine learning per set di dati di grandi dimensioni. Integra le pipeline di Spark Machine Learning con [Microsoft Cognitive Toolkit](https://github.com/Microsoft/CNTK) e [OpenCV](http://www.opencv.org/), consentendo di: 
 * Inserire e pre-elaborare i dati dell'immagine
 * Definire le caratteristiche di immagini e testo usando modelli di apprendimento avanzato con training preliminare
 * Eseguire modelli di training, classificazione dei punteggi e regressione tramite la definizione implicita delle caratteristiche.

## <a name="prerequisites"></a>Prerequisiti

Per proseguire con questa guida è necessario:
- [Installare Azure Machine Learning Workbench](quickstart-installation.md)
- [Configurare un cluster Azure HDInsight Spark](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql)

## <a name="run-your-experiment-in-docker-container"></a>Eseguire l'esperimento nel contenitore Docker

Azure Machine Learning Workbench è configurato per usare MMLSpark quando si eseguono esperimenti nel contenitore Docker, sia in locale sia nella macchina virtuale remota. Questa funzionalità consente di eseguire facilmente il debug e sperimentare i modelli di PySpark, prima che vengano eseguiti su larga scala in un cluster. 

Per iniziare a usare un esempio, creare un nuovo progetto e selezionare l'esempio nella raccolta "MMLSpark on Adult Census". Selezionare "Docker" come contesto di calcolo dal dashboard del progetto e scegliere "Esegui". Azure Machine Learning Workbench compila il contenitore Docker per eseguire il programma PySpark e quindi esegue il programma.

Una volta completata l'esecuzione, è possibile visualizzare i risultati nella vista della cronologia di esecuzione di Azure Machine Learning Workbench.

## <a name="install-mmlspark-on-azure-hdinsight-spark-cluster"></a>Installare MMLSpark in un cluster di Azure HDInsight Spark.

Per completare questo passaggio e il seguente, è necessario innanzitutto [creare un cluster di Azure HDInsight Spark](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql).

Per impostazione predefinita, Azure Machine Learning Workbench installa il pacchetto MMLSpark nel cluster quando si esegue l'esperimento. È possibile controllare questo comportamento e installare altri pacchetti Spark modificando il file denominato _aml_config/spark_dependencies.yml_ nella cartella del progetto.

```
# Spark configuration properties.
configuration:
  "spark.app.name": "Azure ML Experiment"
  "spark.yarn.maxAppAttempts": 1

repositories:
  - "https://mmlspark.azureedge.net/maven"
packages:
  - group: "com.microsoft.ml.spark"
    artifact: "mmlspark_2.11"
    version: "0.7.9"
```

È anche possibile installare MMLSpark direttamente nel cluster HDInsight Spark usando l'[azione Script](https://github.com/Azure/mmlspark#hdinsight).

## <a name="set-up-azure-hdinsight-spark-cluster-as-compute-target"></a>Configurare un cluster Azure HDInsight Spark come destinazione del calcolo

Aprire una finestra dell'interfaccia della riga di comando in Azure Machine Learning Workbench, passare al menu File e fare clic su Prompt dei comandi.

Nella finestra dell'interfaccia della riga di comando eseguire questi comandi:

```
az ml computetarget attach cluster --name <myhdi> --address <myhdi-ssh.azurehdinsight.net> --username <sshusername> --password <sshpwd> 
```

```
az ml experiment prepare -c <myhdi>
```

A questo punto il cluster è disponibile come destinazione di calcolo per il progetto.

## <a name="run-experiment-on-azure-hdinsight-spark-cluster"></a>Eseguire l'esperimento in un cluster di Azure HDInsight Spark.

Tornare al dashboard del progetto di esempio "MMLSpark on Adult Census". Selezionare il cluster come destinazione di calcolo e fare clic su Esegui.

Azure Machine Learning Workbench invia il processo Spark al cluster. È possibile monitorare lo stato di avanzamento e visualizzare i risultati nella vista della cronologia di esecuzione.

## <a name="next-steps"></a>Passaggi successivi
Per informazioni sulla libreria MMLSpark ed esempi, vedere il [Repository GitHub relativo a MMLSpark](https://github.com/Azure/mmlspark)

*Apache®, Apache Spark, e Spark® sono marchi o marchi registrati di Apache Software Foundation negli Stati Uniti e/o in altri paesi.*
