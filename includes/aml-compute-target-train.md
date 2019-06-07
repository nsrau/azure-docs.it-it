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
ms.openlocfilehash: 50905eb987defac612f1055b450b682726f0a56f
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2019
ms.locfileid: "66752963"
---
|Set di training &nbsp;destinazioni| Supporto per GPU |[Machine Learning automatizzati](../articles/machine-learning/service/concept-automated-ml.md) | [Pipeline di Machine Learning](../articles/machine-learning/service/concept-ml-pipelines.md) | [Interfaccia visiva](../articles/machine-learning/service/ui-concept-visual-interface.md)
|----|:----:|:----:|:----:|:----:|
|[Computer locale](../articles/machine-learning/service/how-to-set-up-training-targets.md#local)| Forse | sì | &nbsp; | &nbsp; |
|[Ambiente di calcolo di Machine Learning](../articles/machine-learning/service/how-to-set-up-training-targets.md#amlcompute)| sì | Sì & <br/>degli iperparametri&nbsp;ottimizzazione | sì | sì |
|[Macchina virtuale remota](../articles/machine-learning/service/how-to-set-up-training-targets.md#vm) |sì | Sì & <br/>Ottimizzazione degli iperparametri | sì | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#databricks)| &nbsp; | sì | sì | &nbsp; |
|[Azure Data Lake Analytics.](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#adla)| &nbsp; | &nbsp; | sì | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/service/how-to-set-up-training-targets.md#hdinsight)| &nbsp; | &nbsp; | sì | &nbsp; |
|[Azure Batch](../articles/machine-learning/service/how-to-set-up-training-targets.md#azbatch)| &nbsp; | &nbsp; | sì | &nbsp; |

**Tutte le destinazioni di calcolo possono essere riutilizzate per più processi di training**. Ad esempio, dopo aver collegato una macchina virtuale remota all'area di lavoro, è possibile riutilizzarla per più processi.