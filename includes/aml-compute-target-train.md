---
title: file di inclusione
description: file di inclusione
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 12/04/2019
ms.openlocfilehash: 25a865296d2ebaabcf9043341878928763d61a6f
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74875423"
---
**Le destinazioni di calcolo possono essere riutilizzate da un processo di training a quello successivo**. Ad esempio, dopo aver collegato una macchina virtuale remota all'area di lavoro, è possibile riutilizzarla per più processi.  Per le pipeline di Machine Learning, usare il [passaggio della pipeline](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) appropriato per ogni destinazione di calcolo.

|Training &nbsp;destinazioni|[ML automatizzato](../articles/machine-learning/service/concept-automated-ml.md) | [Pipeline ML](../articles/machine-learning/service/concept-ml-pipelines.md) | [Finestra di progettazione Azure Machine Learning](../articles/machine-learning/service/concept-designer.md)
|----|:----:|:----:|:----:|
|[Computer locale](../articles/machine-learning/service/how-to-set-up-training-targets.md#local)| Sì | &nbsp; | &nbsp; |
|[Azure Machine Learning cluster di calcolo](../articles/machine-learning/service/how-to-set-up-training-targets.md#amlcompute)| Sì & <br/>iperparametri&nbsp;ottimizzazione | Sì | Sì |
|[Macchina virtuale remota](../articles/machine-learning/service/how-to-set-up-training-targets.md#vm) | Sì & <br/>Ottimizzazione degli iperparametri | Sì | &nbsp; |
|[Azure&nbsp;databricks](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#databricks)| Sì | Sì | &nbsp; |
|[Azure Data Lake Analytics.](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#adla) | &nbsp; | Sì | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/service/how-to-set-up-training-targets.md#hdinsight) | &nbsp; | Sì | &nbsp; |
|[Azure Batch](../articles/machine-learning/service/how-to-set-up-training-targets.md#azbatch) | &nbsp; | Sì | &nbsp; |
