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
ms.date: 09/17/2020
ms.openlocfilehash: c45b30fb16293652e169b89a6d93520509777a40
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95555480"
---
**Le destinazioni di calcolo possono essere riutilizzate per più processi di training**. Ad esempio, dopo aver collegato una macchina virtuale remota all'area di lavoro, è possibile riutilizzarla per più processi. Per le pipeline di Machine Learning, usare il [passaggio della pipeline](/python/api/azureml-pipeline-steps/azureml.pipeline.steps?preserve-view=true&view=azure-ml-py) appropriato per ogni destinazione di calcolo.

Per la maggior parte dei processi, è possibile usare qualsiasi risorsa seguente come destinazione di calcolo. Non tutte le risorse possono essere usate per Machine Learning automatizzato, pipeline di Machine Learning o progettazione.

|Destinazioni di&nbsp;training|[Machine Learning automatizzato](../articles/machine-learning/concept-automated-ml.md) | [Pipeline di apprendimento automatico](../articles/machine-learning/concept-ml-pipelines.md) | [Finestra di progettazione di Azure Machine Learning](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Computer locale](../articles/machine-learning/how-to-attach-compute-targets.md#local)| Sì | &nbsp; | &nbsp; |
|[Cluster di calcolo di Azure Machine Learning](../articles/machine-learning/how-to-create-attach-compute-cluster.md)| Sì | Sì | Sì |
|[Istanza di calcolo di Azure Machine Learning](../articles/machine-learning/how-to-create-manage-compute-instance.md) | Sì (tramite SDK)  | Sì |  |
|[Macchina virtuale remota](../articles/machine-learning/how-to-attach-compute-targets.md#vm) | Sì  | Sì | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/how-to-attach-compute-targets.md#databricks)| Sì (solo modalità locale SDK) | Sì | &nbsp; |
|[Azure Data Lake Analytics.](../articles/machine-learning/how-to-attach-compute-targets.md#adla) | &nbsp; | Sì | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-attach-compute-targets.md#hdinsight) | &nbsp; | Sì | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-attach-compute-targets.md#azbatch) | &nbsp; | Sì | &nbsp; |