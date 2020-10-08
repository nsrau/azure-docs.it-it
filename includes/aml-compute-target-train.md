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
ms.date: 08/19/2020
ms.openlocfilehash: bbd32f7e40e3039a69a95b4458de86ff35f26867
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2020
ms.locfileid: "91642698"
---
**Le destinazioni di calcolo possono essere riutilizzate per più processi di training**. Ad esempio, dopo aver collegato una macchina virtuale remota all'area di lavoro, è possibile riutilizzarla per più processi.  Per le pipeline di Machine Learning, usare il [passaggio della pipeline](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) appropriato per ogni destinazione di calcolo.

|Destinazioni di&nbsp;training|[Machine Learning automatizzato](../articles/machine-learning/concept-automated-ml.md) | [Pipeline di Machine Learning](../articles/machine-learning/concept-ml-pipelines.md) | [Finestra di progettazione di Azure Machine Learning](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Computer locale](../articles/machine-learning/how-to-create-attach-compute-sdk.md#local)| sì | &nbsp; | &nbsp; |
|[Cluster di calcolo di Azure Machine Learning](../articles/machine-learning/how-to-create-attach-compute-sdk.md#amlcompute)| sì e <br/>ottimizzazione&nbsp;degli iperparametri | sì | sì |
|[Istanza di calcolo di Azure Machine Learning](../articles/machine-learning/how-to-create-attach-compute-sdk.md#instance) | sì (tramite SDK) | sì |  |
|[Macchina virtuale remota](../articles/machine-learning/how-to-create-attach-compute-sdk.md#vm) | sì e <br/>ottimizzazione degli iperparametri | sì | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/how-to-create-attach-compute-sdk.md#databricks)| sì (solo modalità locale SDK) | sì | &nbsp; |
|[Azure Data Lake Analytics.](../articles/machine-learning/how-to-create-attach-compute-sdk.md#adla) | &nbsp; | sì | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-create-attach-compute-sdk.md#hdinsight) | &nbsp; | sì | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-create-attach-compute-sdk.md#azbatch) | &nbsp; | sì | &nbsp; |
