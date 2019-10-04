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
ms.openlocfilehash: 1b62d4b2ac1bb69e2270c3202d29eb595df7aac4
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67806051"
---
**Calcolare le destinazioni possono essere riutilizzate da un processo di training al successivo**. Ad esempio, dopo aver collegato una macchina virtuale remota all'area di lavoro, è possibile riutilizzarla per più processi.

|Set di training &nbsp;destinazioni| Supporto per GPU |[Machine Learning automatizzati](../articles/machine-learning/service/concept-automated-ml.md) | [Pipeline di Machine Learning](../articles/machine-learning/service/concept-ml-pipelines.md) | [Interfaccia visiva](../articles/machine-learning/service/ui-concept-visual-interface.md)
|----|:----:|:----:|:----:|:----:|
|[Computer locale](../articles/machine-learning/service/how-to-set-up-training-targets.md#local)| Forse | sì | &nbsp; | &nbsp; |
|[Ambiente di calcolo di Machine Learning](../articles/machine-learning/service/how-to-set-up-training-targets.md#amlcompute)| sì | Sì & <br/>degli iperparametri&nbsp;ottimizzazione | sì | sì |
|[Macchina virtuale remota](../articles/machine-learning/service/how-to-set-up-training-targets.md#vm) |sì | Sì & <br/>Ottimizzazione degli iperparametri | sì | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#databricks)| &nbsp; | sì | sì | &nbsp; |
|[Azure Data Lake Analytics.](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#adla)| &nbsp; | &nbsp; | sì | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/service/how-to-set-up-training-targets.md#hdinsight)| &nbsp; | &nbsp; | sì | &nbsp; |
|[Azure Batch](../articles/machine-learning/service/how-to-set-up-training-targets.md#azbatch)| &nbsp; | &nbsp; | sì | &nbsp; |
