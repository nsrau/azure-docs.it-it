---
title: Analisi in HDInsight Spark con PySpark, Scala - Processo di data science per i team
description: Esempi del processo di analisi scientifica dei dati del team che illustrano l'uso di PySpark e Scala in un'area di analisi di Azure HDInsight.Examples of the Team Data Science Process that walk through the use of PySpark and Scala on an Azure HDInsight Spark.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 91aac279a264d64ace5988d147c4caf8c52e9656
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864146"
---
# <a name="hdinsight-spark-data-science-walkthroughs-using-pyspark-and-scala-on-azure"></a>Procedure dettagliate di data science per HDInsight Spark con PySpark e Scala in Azure

Queste procedure dettagliate usano PySpark e Scala in un cluster Spark di Azure per eseguire analisi predittive. Seguono i passaggi descritti nel processo di data science per i team. Per una panoramica del processo di data science per i team, vedere [Processo di data science](overview.md). Per una panoramica di Spark in HDInsight, vedere [Introduzione a Spark in HDInsight](../../hdinsight/spark/apache-spark-overview.md).

Procedure dettagliate di data science aggiuntive che eseguono il processo di analisi scientifica dei dati del team sono raggruppate in base alla **piattaforma** in uso. Per i dettagli di questi esempi, vedere [Procedure dettagliate del processo di data science per i team](walkthroughs.md).

## <a name="predict-taxi-tips-using-pyspark-on-azure-spark"></a>Stimare le mance dei taxi con PySpark in Azure Spark

Usando i dati dei taxi di New York, la procedura dettagliata [Usare Spark in Azure HDInsight](spark-overview.md) stima se viene pagato un suggerimento e l'intervallo di importi previsti. Questo esempio usa il processo di analisi scientifica dei dati del team in uno scenario usando un cluster spark di [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) per archiviare, esplorare e fornire funzionalità ai dati dei dati relativi ai viaggi e alle tariffe di New York disponibili pubblicamente. Questo argomento di panoramica usa un cluster HdInsight Spark e i blocchi appunti Jupyter PySpark.This overview topic uses an HDInsight Spark cluster and Jupyter PySpark notebooks. Tali notebook illustrano come esplorare i dati e quindi come creare e utilizzare i modelli. Il notebook relativo a modellazione ed esplorazione avanzate dei dati mostra come includere la convalida incrociata, lo sweep di iperparametri e la valutazione del modello.

### <a name="data-exploration-and-modeling-with-spark"></a>Modellazione ed esplorazione dei dati con Spark 
Esplorare il set di dati ed eseguire operazioni di creazione, assegnazione dei punteggi e valutazione di modelli di Machine Learning seguendo quanto illustrato nell'argomento [Create binary classification and regression models for data with the Spark MLlib toolkit](spark-data-exploration-modeling.md) (Creare modelli di classificazione binaria e regressione per i dati con il toolkit Spark MLlib).

### <a name="model-consumption"></a>Modellare il consumo
Per informazioni su come valutare i modelli di regressione e di classificazione creati in questo argomento, vedere [Assegnare punteggi a modelli di apprendimento automatico compilati con Spark](spark-model-consumption.md).

### <a name="cross-validation-and-hyperparameter-sweeping"></a>Convalida incrociata e sweep di iperparametri
Vedere [Esplorazione avanzata dei dati e modellazione con Spark](spark-advanced-data-exploration-modeling.md) su come è possibile eseguire il training dei modelli utilizzando la convalida incrociata e lo sweep dei peri-parametri.


## <a name="predict-taxi-tips-using-scala-on-azure-spark"></a>Stimare le mance dei taxi con Scala in Azure Spark

La procedura dettagliata [Usa Scala con Spark in Azure](scala-walkthrough.md) stima se una mancia viene pagata e l'intervallo di importi previsti da pagare. Illustra come usare Scala per attività di Machine Learning con supervisione con la libreria di Machine Learning (MLlib) per Spark e pacchetti SparkML in un cluster Azure HDInsight Spark. Vengono illustrate le attività che costituiscono il processo di [analisi scientifica](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)dei dati: inserimento ed esplorazione dei dati, visualizzazione, progettazione delle funzionalità, modellazione e utilizzo del modello. I modelli creati includono la regressione logistica e quella lineare, foreste casuali e alberi con boosting a gradienti.


## <a name="next-steps"></a>Passaggi successivi

Per una panoramica del processo di analisi scientifica dei dati del team, vedere Panoramica del processo di [analisi scientifica dei dati del team.](overview.md)

Per una descrizione del ciclo di vita del processo di analisi scientifica dei dati del team, vedere Ciclo di vita del processo di [analisi scientifica dei dati del team](lifecycle.md). Questo ciclo di vita descrive i passaggi, dall'inizio alla fine, che i progetti in genere seguono quando vengono eseguiti. 

