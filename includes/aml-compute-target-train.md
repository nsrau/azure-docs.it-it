---
title: File di inclusione
description: File di inclusione
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 12/04/2019
ms.openlocfilehash: 2c275e1d8f54191e12454f7e97a8dd59e7c84be2
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/28/2019
ms.locfileid: "75539385"
---
**Le destinazioni di calcolo possono essere riutilizzate da un processo di training a quello successivo**. Ad esempio, dopo aver collegato una macchina virtuale remota all'area di lavoro, è possibile riutilizzarla per più processi.  Per le pipeline di Machine Learning, usare il [passaggio della pipeline](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) appropriato per ogni destinazione di calcolo.

|Training &nbsp;destinazioni|[ML automatizzato](../articles/machine-learning/concept-automated-ml.md) | [Pipeline ML](../articles/machine-learning/concept-ml-pipelines.md) | [Finestra di progettazione Azure Machine Learning](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Computer locale](../articles/machine-learning/how-to-set-up-training-targets.md#local)| sì | &nbsp; | &nbsp; |
|[Azure Machine Learning istanza di calcolo (anteprima)](../articles/machine-learning/concept-compute-instance.md) | | sì |  |
|[Azure Machine Learning cluster di calcolo](../articles/machine-learning/how-to-set-up-training-targets.md#amlcompute)| Sì & <br/>iperparametri&nbsp;ottimizzazione | sì | sì |
|[Macchina virtuale remota](../articles/machine-learning/how-to-set-up-training-targets.md#vm) | Sì & <br/>Ottimizzazione degli iperparametri | sì | &nbsp; |
|[Azure&nbsp;databricks](../articles/machine-learning/how-to-create-your-first-pipeline.md#databricks)| sì | sì | &nbsp; |
|[Azure Data Lake Analytics.](../articles/machine-learning/how-to-create-your-first-pipeline.md#adla) | &nbsp; | sì | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-set-up-training-targets.md#hdinsight) | &nbsp; | sì | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-set-up-training-targets.md#azbatch) | &nbsp; | sì | &nbsp; |
