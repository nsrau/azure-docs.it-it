---
title: 'Destinazioni di calcolo: dove eseguire il training e distribuire i modelli'
titleSuffix: Azure Machine Learning
description: Definire il punto in cui si desidera eseguire il training o distribuire il modello con Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 11/04/2019
ms.openlocfilehash: c3791946ee31183e4b3c5131a8e62934bf87dfee
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497469"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning"></a>Che cosa sono le destinazioni di calcolo in Azure Machine Learning? 

Una **destinazione di calcolo** è una risorsa o un ambiente di calcolo designato in cui si esegue lo script di training o si ospita la distribuzione del servizio. Questo percorso può essere il computer locale o una risorsa di calcolo basata sul cloud. L'uso di destinazioni di calcolo semplifica la modifica dell'ambiente di calcolo in un secondo momento senza dover modificare il codice.  

In un ciclo di vita di sviluppo di modelli tipico, è possibile:
1. Inizia sviluppando e sperimentando una piccola quantità di dati. In questa fase è consigliabile usare l'ambiente locale (computer locale o macchina virtuale basata sul cloud) come destinazione di calcolo. 
2. Scalabilità verticale a dati più grandi oppure Esegui il training distribuito usando una di queste [destinazioni di calcolo](#train)per il training.  
3. Quando il modello è pronto, è possibile distribuirlo in un ambiente di hosting Web o in un dispositivo tutto con una di queste [destinazioni di calcolo della distribuzione](#deploy).

Le risorse di calcolo usate per le destinazioni di calcolo sono collegate a un' [area di lavoro](concept-workspace.md). Le risorse di calcolo diverse dal computer locale sono condivise dagli utenti dell'area di lavoro.

## <a name="train"></a>Training delle destinazioni di calcolo

Azure Machine Learning offre un supporto variabile tra le diverse risorse di calcolo.  È anche possibile aggiungere una risorsa di calcolo personalizzata, anche se il supporto per vari scenari può variare.

[!INCLUDE [aml-compute-target-train](../../../includes/aml-compute-target-train.md)]

Altre informazioni sulla [configurazione e l'uso di una destinazione di calcolo per il training del modello](how-to-set-up-training-targets.md).

## <a name="deploy"></a>Destinazioni di distribuzione

Per ospitare la distribuzione del modello, è possibile usare le risorse di calcolo seguenti.

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

Informazioni su [dove e come distribuire il modello in una destinazione di calcolo](how-to-deploy-and-where.md).

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Calcolo Azure Machine Learning (gestito)

Una risorsa di calcolo gestita viene creata e gestita da Azure Machine Learning. Questo calcolo è ottimizzato per i carichi di lavoro di machine learning. Azure Machine Learning i cluster di calcolo e le [istanze di calcolo](concept-compute-instance.md) sono gli unici calcoli gestiti. In futuro potrebbero essere aggiunte risorse di calcolo gestite aggiuntive.

È possibile creare Azure Machine Learning istanze di calcolo o cluster di calcolo in:

| | Azure Machine Learning Studio | Portale di Azure | SDK | Modello di Resource Manager | CLI |
|---| ----- | ----- | ----- | ----- | ----- |
| Istanza di calcolo | Sì | Sì | Sì | Sì |  |
| Cluster di calcolo | Sì | Sì | Sì | Sì | Sì |

Quando vengono create, queste risorse di calcolo fanno automaticamente parte dell'area di lavoro, a differenza di altri tipi di destinazioni di calcolo.

> [!NOTE]
> Le istanze di calcolo sono disponibili solo per le aree di lavoro con un'area **Stati Uniti centro-settentrionali** o **Regno Unito meridionale**.
>Se l'area di lavoro si trova in un'altra area, è possibile continuare a creare e usare una [macchina virtuale del notebook](concept-compute-instance.md#notebookvm) . 

### <a name="compute-clusters"></a>Cluster di elaborazione

È possibile usare Azure Machine Learning cluster di calcolo per il training e l'inferenza in batch (anteprima).  Con questa risorsa di calcolo sono disponibili:

* Cluster a nodo singolo o a più nodi
* Scalabilità automatica ogni volta che si invia un'esecuzione 
* Gestione automatica dei cluster e pianificazione dei processi 
* Include il supporto per le risorse di CPU e GPU



## <a name="unmanaged-compute"></a>Calcolo non gestito

Una destinazione di calcolo *non gestita non* è gestita da Azure Machine Learning. Questo tipo di destinazione di calcolo viene creato all'esterno Azure Machine Learning, quindi collegato all'area di lavoro. Le risorse di calcolo non gestite possono richiedere passaggi aggiuntivi per la manutenzione o per migliorare le prestazioni dei carichi di lavoro di machine learning.

## <a name="next-steps"></a>Passaggi successivi

È possibile passare agli argomenti seguenti:
* [Configurare una destinazione di calcolo per il training del modello](how-to-set-up-training-targets.md)
* [Distribuire il modello in una destinazione di calcolo](how-to-deploy-and-where.md)