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
ms.date: 05/30/2019
ms.openlocfilehash: aa1120db5451583153928bd2cbc383a46781a267
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489558"
---
**Le destinazioni di calcolo possono essere riutilizzate da un processo di training a quello successivo**. Ad esempio, dopo aver collegato una macchina virtuale remota all'area di lavoro, è possibile riutilizzarla per più processi.

|Training &nbsp;destinazioni| Supporto GPU |[ML automatizzato](../articles/machine-learning/service/concept-automated-ml.md) | [Pipeline ML](../articles/machine-learning/service/concept-ml-pipelines.md) | [Finestra di progettazione Azure Machine Learning](../articles/machine-learning/service/concept-designer.md)
|----|:----:|:----:|:----:|:----:|
|[Computer locale](../articles/machine-learning/service/how-to-set-up-training-targets.md#local)| Forse | Sì | &nbsp; | &nbsp; |
|[Istanza di calcolo Azure Machine Learning](../articles/machine-learning/service/concept-compute-instance.md)| Sì | | Sì |  |
|[Azure Machine Learning cluster di calcolo](../articles/machine-learning/service/how-to-set-up-training-targets.md#amlcompute)| Sì | Sì & <br/>iperparametri&nbsp;ottimizzazione | Sì | Sì |
|[Macchina virtuale remota](../articles/machine-learning/service/how-to-set-up-training-targets.md#vm) |Sì | Sì & <br/>Ottimizzazione degli iperparametri | Sì | &nbsp; |
|[Azure&nbsp;databricks](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#databricks)| &nbsp; | Sì | Sì | &nbsp; |
|[Azure Data Lake Analytics.](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#adla)| &nbsp; | &nbsp; | Sì | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/service/how-to-set-up-training-targets.md#hdinsight)| &nbsp; | &nbsp; | Sì | &nbsp; |
|[Azure Batch](../articles/machine-learning/service/how-to-set-up-training-targets.md#azbatch)| &nbsp; | &nbsp; | Sì | &nbsp; |
