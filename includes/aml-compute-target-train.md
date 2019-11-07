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
ms.openlocfilehash: 2591ab6984ebe4f864540ab290881a6e47f0be71
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580580"
---
**Le destinazioni di calcolo possono essere riutilizzate da un processo di training a quello successivo**. Ad esempio, dopo aver collegato una macchina virtuale remota all'area di lavoro, è possibile riutilizzarla per più processi.

|Training &nbsp;destinazioni| Supporto GPU |[ML automatizzato](../articles/machine-learning/service/concept-automated-ml.md) | [Pipeline ML](../articles/machine-learning/service/concept-ml-pipelines.md) | [Finestra di progettazione Azure Machine Learning](../articles/machine-learning/service/concept-designer.md)
|----|:----:|:----:|:----:|:----:|
|[Computer locale](../articles/machine-learning/service/how-to-set-up-training-targets.md#local)| Forse | Sì | &nbsp; | &nbsp; |
|[Azure Machine Learning cluster di calcolo](../articles/machine-learning/service/how-to-set-up-training-targets.md#amlcompute)| Sì | Sì & <br/>iperparametri&nbsp;ottimizzazione | Sì | Sì |
|[Macchina virtuale remota](../articles/machine-learning/service/how-to-set-up-training-targets.md#vm) |Sì | Sì & <br/>Ottimizzazione degli iperparametri | Sì | &nbsp; |
|[Azure&nbsp;databricks](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#databricks)| &nbsp; | Sì | Sì | &nbsp; |
|[Azure Data Lake Analytics.](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#adla)| &nbsp; | &nbsp; | Sì | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/service/how-to-set-up-training-targets.md#hdinsight)| &nbsp; | &nbsp; | Sì | &nbsp; |
|[Azure Batch](../articles/machine-learning/service/how-to-set-up-training-targets.md#azbatch)| &nbsp; | &nbsp; | Sì | &nbsp; |
