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
ms.openlocfilehash: 9a04bca11c6fca480ea1c9fd3d4a51956c1830de
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88933112"
---
**Le destinazioni di calcolo possono essere riutilizzate per più processi di training**. Ad esempio, dopo aver collegato una macchina virtuale remota all'area di lavoro, è possibile riutilizzarla per più processi.  Per le pipeline di Machine Learning, usare il [passaggio della pipeline](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) appropriato per ogni destinazione di calcolo.

|Destinazioni di&nbsp;training|[Machine Learning automatizzato](../articles/machine-learning/concept-automated-ml.md) | [Pipeline di Machine Learning](../articles/machine-learning/concept-ml-pipelines.md) | [Finestra di progettazione di Azure Machine Learning](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Computer locale](../articles/machine-learning/how-to-set-up-training-targets.md#local)| sì | &nbsp; | &nbsp; |
|[Cluster di calcolo di Azure Machine Learning](../articles/machine-learning/how-to-set-up-training-targets.md#amlcompute)| sì e <br/>ottimizzazione&nbsp;degli iperparametri | sì | sì |
|[Istanza di calcolo di Azure Machine Learning](../articles/machine-learning/how-to-set-up-training-targets.md#instance) | sì e <br/>ottimizzazione degli iperparametri | sì |  |
|[Macchina virtuale remota](../articles/machine-learning/how-to-set-up-training-targets.md#vm) | sì e <br/>ottimizzazione degli iperparametri | sì | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/how-to-set-up-training-targets.md#databricks)| sì (solo modalità locale SDK) | sì | &nbsp; |
|[Azure Data Lake Analytics.](../articles/machine-learning/how-to-set-up-training-targets.md#adla) | &nbsp; | sì | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-set-up-training-targets.md#hdinsight) | &nbsp; | sì | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-set-up-training-targets.md#azbatch) | &nbsp; | sì | &nbsp; |
