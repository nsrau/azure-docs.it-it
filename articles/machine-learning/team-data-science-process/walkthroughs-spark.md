---
title: Analisi in HDInsight Spark con PySpark, Scala - Processo di data science per i team
description: Esempi del processo di Data Science per i team che analizzano l'uso di PySpark e scala in un Azure HDInsight Spark.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75864146"
---
# <a name="hdinsight-spark-data-science-walkthroughs-using-pyspark-and-scala-on-azure"></a>Procedure dettagliate di data science per HDInsight Spark con PySpark e Scala in Azure

Queste procedure dettagliate usano PySpark e Scala in un cluster Spark di Azure per eseguire analisi predittive. Seguono i passaggi descritti nel processo di data science per i team. Per una panoramica del processo di data science per i team, vedere [Processo di data science](overview.md). Per una panoramica di Spark in HDInsight, vedere [Introduzione a Spark in HDInsight](../../hdinsight/spark/apache-spark-overview.md).

Ulteriori data science procedure dettagliate che eseguono il processo di Data Science per i team sono raggruppate in base alla **piattaforma** utilizzata. Per i dettagli di questi esempi, vedere [Procedure dettagliate del processo di data science per i team](walkthroughs.md).

## <a name="predict-taxi-tips-using-pyspark-on-azure-spark"></a>Stimare le mance dei taxi con PySpark in Azure Spark

Usando i dati dei taxi di New York, la procedura dettagliata [use Spark in Azure HDInsight consente](spark-overview.md) di stimare se una mancia viene pagata e l'intervallo di importi previsti. Questo esempio usa il processo di analisi scientifica dei dati per i team in uno scenario che usa un [cluster di Azure HDInsight Spark](https://azure.microsoft.com/services/hdinsight/) per archiviare, esplorare e presentare i dati del tecnico dal set di dati relativo alle corse e alle tariffe di NYC Taxi disponibili pubblicamente Questo argomento di panoramica usa un cluster HDInsight Spark e Jupyter notebook di PySpark. Tali notebook illustrano come esplorare i dati e quindi come creare e utilizzare i modelli. Il notebook relativo a modellazione ed esplorazione avanzate dei dati mostra come includere la convalida incrociata, lo sweep di iperparametri e la valutazione del modello.

### <a name="data-exploration-and-modeling-with-spark"></a>Modellazione ed esplorazione dei dati con Spark 
Esplorare il set di dati ed eseguire operazioni di creazione, assegnazione dei punteggi e valutazione di modelli di Machine Learning seguendo quanto illustrato nell'argomento [Create binary classification and regression models for data with the Spark MLlib toolkit](spark-data-exploration-modeling.md) (Creare modelli di classificazione binaria e regressione per i dati con il toolkit Spark MLlib).

### <a name="model-consumption"></a>Modellare il consumo
Per informazioni su come valutare i modelli di regressione e di classificazione creati in questo argomento, vedere [Assegnare punteggi a modelli di apprendimento automatico compilati con Spark](spark-model-consumption.md).

### <a name="cross-validation-and-hyperparameter-sweeping"></a>Convalida incrociata e sweep di iperparametri
Vedere [esplorazione e modellazione avanzate dei dati con Spark](spark-advanced-data-exploration-modeling.md) sul modo in cui i modelli possono essere sottoposti a training usando la convalida incrociata e lo sweep di iperparametri.


## <a name="predict-taxi-tips-using-scala-on-azure-spark"></a>Stimare le mance dei taxi con Scala in Azure Spark

La procedura dettagliata [usare scala con Spark in Azure](scala-walkthrough.md) prevede se viene pagata una mancia e l'intervallo di importi previsto. Illustra come usare Scala per attività di Machine Learning con supervisione con la libreria di Machine Learning (MLlib) per Spark e pacchetti SparkML in un cluster Azure HDInsight Spark. Vengono illustrate le attività che costituiscono il processo di analisi [scientifica dei dati](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/), ovvero l'inserimento e l'esplorazione dei dati, la visualizzazione, la progettazione di funzionalità, la modellazione e l'utilizzo di modelli. I modelli creati includono la regressione logistica e quella lineare, foreste casuali e alberi con boosting a gradienti.


## <a name="next-steps"></a>Passaggi successivi

Per una panoramica del processo di analisi scientifica dei dati per i team, vedere [Panoramica del processo di Data Science](overview.md)per i team.

Per una descrizione del ciclo di vita del processo di Data Science per i team, vedere ciclo di vita del [processo di Data Science](lifecycle.md). Questo ciclo di vita descrive i passaggi, dall'inizio alla fine, che in genere seguono i progetti quando vengono eseguiti. 

