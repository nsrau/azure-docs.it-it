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
ms.openlocfilehash: 6106d4e0801500b0429e634651f3de342646b754
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77156302"
---
**Le destinazioni di calcolo possono essere riutilizzate da un processo di training a quello successivo**. Ad esempio, dopo aver collegato una macchina virtuale remota all'area di lavoro, è possibile riutilizzarla per più processi.  Per le pipeline di Machine Learning, usare il [passaggio della pipeline](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py) appropriato per ogni destinazione di calcolo.

|Obiettivi &nbsp;di training|[Funzionalità automatiche di Machine Learning](../articles/machine-learning/concept-automated-ml.md) | [Pipeline di apprendimento automatico](../articles/machine-learning/concept-ml-pipelines.md) | [Finestra di progettazione di Azure Machine Learning](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Computer locale](../articles/machine-learning/how-to-set-up-training-targets.md#local)| sì | &nbsp; | &nbsp; |
|[Azure Machine Learning cluster di calcolo](../articles/machine-learning/how-to-set-up-training-targets.md#amlcompute)| Sì & <br/>ottimizzazione di&nbsp;iperparametri | sì | sì |
|[VM remota](../articles/machine-learning/how-to-set-up-training-targets.md#vm) | Sì & <br/>Ottimizzazione degli iperparametri | sì | &nbsp; |
|[Azure&nbsp;databricks](../articles/machine-learning/how-to-create-your-first-pipeline.md#databricks)| Sì (solo modalità locale SDK) | sì | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-create-your-first-pipeline.md#adla) | &nbsp; | sì | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-set-up-training-targets.md#hdinsight) | &nbsp; | sì | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-set-up-training-targets.md#azbatch) | &nbsp; | sì | &nbsp; |
