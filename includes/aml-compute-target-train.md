---
title: includere il file
description: Includere file
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 12/04/2019
ms.openlocfilehash: dc6c29793b71525ad64254aa9f57e12e56601adc
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82726215"
---
**Le destinazioni di calcolo possono essere riutilizzate da un processo di training a quello successivo**. Ad esempio, dopo aver collegato una macchina virtuale remota all'area di lavoro, è possibile riutilizzarla per più processi.  Per le pipeline di Machine Learning, usare il [passaggio della pipeline](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) appropriato per ogni destinazione di calcolo.

|Obiettivi &nbsp;di training|[Funzionalità automatiche di Machine Learning](../articles/machine-learning/concept-automated-ml.md) | [Pipeline di apprendimento automatico](../articles/machine-learning/concept-ml-pipelines.md) | [Finestra di progettazione di Azure Machine Learning](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Computer locale](../articles/machine-learning/how-to-set-up-training-targets.md#local)| sì | &nbsp; | &nbsp; |
|[Azure Machine Learning cluster di calcolo](../articles/machine-learning/how-to-set-up-training-targets.md#amlcompute)| Sì & <br/>ottimizzazione di&nbsp;iperparametri | sì | sì |
|[Istanza di calcolo di Azure Machine Learning](../articles/machine-learning/concept-compute-instance.md) | Sì & <br/>Ottimizzazione degli iperparametri | sì | sì |
|[VM remota](../articles/machine-learning/how-to-set-up-training-targets.md#vm) | Sì & <br/>Ottimizzazione degli iperparametri | sì | &nbsp; |
|[Azure&nbsp;databricks](../articles/machine-learning/how-to-create-your-first-pipeline.md#databricks)| Sì (solo modalità locale SDK) | sì | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-create-your-first-pipeline.md#adla) | &nbsp; | sì | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-set-up-training-targets.md#hdinsight) | &nbsp; | sì | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-set-up-training-targets.md#azbatch) | &nbsp; | sì | &nbsp; |
