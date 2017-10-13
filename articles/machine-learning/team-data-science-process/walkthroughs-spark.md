---
title: Procedure dettagliate per HDInsight Spark con PySpark e Scala in Azure | Microsoft Docs
description: Esempi del processo di data science per i team che descrivono l'uso di PySpark e Scala in Azure HDInsight Spark per eseguire analisi predittive.
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: bradsev
ms.openlocfilehash: 99b9f047525f46f68dc2c1029f6d53fbda87b69e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="hdinsight-spark-data-science-walkthroughs-using-pyspark-and-scala-on-azure"></a>Procedure dettagliate di data science per HDInsight Spark con PySpark e Scala in Azure

Queste procedure dettagliate usano PySpark e Scala in un cluster Spark di Azure per eseguire analisi predittive. Seguono i passaggi descritti nel processo di data science per i team. Per una panoramica del processo di data science per i team, vedere [Processo di data science](overview.md). Per una panoramica di Spark in HDInsight, vedere [Introduzione a Spark in HDInsight](../../hdinsight/hdinsight-apache-spark-overview.md).

Le procedure dettagliate di data science aggiuntive che eseguono il processo di data science per i team sono raggruppate in base alla **piattaforma** che usano. Per i dettagli di questi esempi, vedere [Procedure dettagliate del processo di data science per i team](walkthroughs.md).

## <a name="predict-taxi-tips-using-pyspark-on-azure-spark"></a>Stimare le mance dei taxi con PySpark in Azure Spark

La procedura dettagliata [Usare Spark on Azure HDInsight](spark-overview.md) usa i dati dei taxi di New York per stimare se viene lasciata una mancia e l'intervallo di importi previsto. Usa il processo di analisi scientifica dei dati per i team in uno scenario con un [cluster Azure HDInsight Spark](https://azure.microsoft.com/services/hdinsight/) per archiviare ed esplorare i dati e progettarne le funzionalità in base al set di dati disponibile pubblicamente relativo a corse e tariffe dei taxi nella città di New York. Questo argomento introduttivo descrive la configurazione del cluster HDInsight Spark e del notebook Jupyter PySpark utilizzati nel resto della procedura dettagliata. Tali notebook illustrano come esplorare i dati e quindi come creare e utilizzare i modelli. Il notebook relativo a modellazione ed esplorazione avanzate dei dati mostra come includere la convalida incrociata, lo sweep di iperparametri e la valutazione del modello.

### <a name="data-exploration-and-modeling-with-spark"></a>Modellazione ed esplorazione dei dati con Spark 
Esplorare il set di dati ed eseguire operazioni di creazione, assegnazione dei punteggi e valutazione di modelli di Machine Learning seguendo quanto illustrato nell'argomento [Create binary classification and regression models for data with the Spark MLlib toolkit](spark-data-exploration-modeling.md) (Creare modelli di classificazione binaria e regressione per i dati con il toolkit Spark MLlib).

### <a name="model-consumption"></a>Modellare il consumo
Per informazioni su come valutare i modelli di regressione e di classificazione creati in questo argomento, vedere [Assegnare punteggi a modelli di apprendimento automatico compilati con Spark](spark-model-consumption.md).

### <a name="cross-validation-and-hyperparameter-sweeping"></a>Convalida incrociata e sweep di iperparametri
Per informazioni su come istruire i modelli sulla convalida incrociata e lo sweep di iperparametri, vedere [Esplorazione e modellazione avanzate dei dati con Spark](spark-advanced-data-exploration-modeling.md).


## <a name="predict-taxi-tips-using-scala-on-azure-spark"></a>Stimare le mance dei taxi con Scala in Azure Spark

La procedura dettagliata [Usare Scala con Spark in Azure](scala-walkthrough.md) utilizza i dati dei taxi di New York per stimare se viene lasciata una mancia e l'intervallo di importi previsto. Illustra come usare Scala per attività di Machine Learning con supervisione con la libreria di Machine Learning (MLlib) per Spark e pacchetti SparkML in un cluster Azure HDInsight Spark. Vengono illustrate le attività che costituiscono il [processo di analisi scientifica dei dati](http://aka.ms/datascienceprocess), ovvero l'inserimento e l'esplorazione dei dati, la visualizzazione, la progettazione, la modellazione e l'utilizzo dei modelli. I modelli creati includono la regressione logistica e quella lineare, foreste casuali e alberi con boosting a gradienti.


## <a name="next-steps"></a>Passaggi successivi

Per una descrizione dei componenti principali che costituiscono il processo di data science per i team, vedere [Panoramica del processo di data science per i team](overview.md).

Per una descrizione del ciclo di vita del processo di data science per i team che è possibile usare per definire la struttura dei progetti di data science, vedere [Ciclo di vita del processo di data science per i team](lifecycle.md). Il ciclo di vita descrive tutti i passaggi generalmente seguiti dai progetti in fase di esecuzione. 

