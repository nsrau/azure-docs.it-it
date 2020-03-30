---
title: Cosa sono gli obiettivi di calcolo
titleSuffix: Azure Machine Learning
description: Definire dove si vuole eseguire il training o distribuire il modello con Azure Machine Learning.Define where you want to train or deploy your model with Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 11/04/2019
ms.openlocfilehash: ec2d9152bf8d3d7c60f00e902f155212ee1b81cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270420"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning"></a>Quali sono le destinazioni di calcolo in Azure Machine Learning? 

Una destinazione di **calcolo** è una risorsa/ambiente di calcolo designato in cui si esegue lo script di training o si ospita la distribuzione del servizio. Questo percorso può essere il computer locale o una risorsa di elaborazione basata su cloud. L'uso delle destinazioni di calcolo semplifica la modifica in un secondo momento dell'ambiente di calcolo senza dover modificare il codice.  

In un ciclo di vita di sviluppo di un modello tipico, è possibile:In a typical model development lifecycle, you might:
1. Inizia a sviluppare e sperimentare su una piccola quantità di dati. In questa fase, è consigliabile l'ambiente locale (computer locale o macchina virtuale basata su cloud) come destinazione di calcolo. 
2. Scalabilità verticale a dati più grandi o eseguire training distribuito usando uno di questi obiettivi di calcolo di [training.](#train)  
3. Quando il modello è pronto, distribuirlo in un ambiente di hosting Web o in un dispositivo IoT con una di queste destinazioni di calcolo della [distribuzione.](#deploy)

Le risorse di calcolo utilizzate per le destinazioni di calcolo sono collegate a [un'area di lavoro.](concept-workspace.md) Le risorse di calcolo diverse dal computer locale sono condivise dagli utenti dell'area di lavoro.

## <a name="training-compute-targets"></a><a name="train"></a>Training di obiettivi di elaborazione

Azure Machine Learning offre un supporto variabile tra diverse risorse di elaborazione.  È anche possibile collegare la propria risorsa di calcolo, anche se il supporto per vari scenari può variare.

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

Ulteriori informazioni [sull'impostazione e l'utilizzo](how-to-set-up-training-targets.md)di una destinazione di calcolo per il training del modello .

## <a name="deployment-targets"></a><a name="deploy"></a>Destinazioni della distribuzione

Le risorse di calcolo seguenti possono essere usate per ospitare la distribuzione del modello.

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

Informazioni su dove e come distribuire il modello in una destinazione di [calcolo.](how-to-deploy-and-where.md)

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Calcolo di Azure Machine Learning (gestito)Azure Machine Learning compute (managed)

Una risorsa di calcolo gestita viene creata e gestita da Azure Machine Learning.A managed compute resource is created and managed by Azure Machine Learning. Questo calcolo è ottimizzato per i carichi di lavoro di apprendimento automatico. I cluster di calcolo di Azure Machine Learning e le istanze di [calcolo](concept-compute-instance.md) sono gli unici calcoli gestiti. In futuro potrebbero essere aggiunte altre risorse di calcolo gestite.

È possibile creare istanze di calcolo di Azure Machine Learning (anteprima) o raggruppare cluster in:You can create Azure Machine Learning compute instances (preview) or compute clusters in:

| | Azure Machine Learning Studio | Portale di Azure | SDK | Modello di Resource Manager | CLI |
|---| ----- | ----- | ----- | ----- | ----- |
| Istanza di calcolo | sì | sì | sì | sì |  |
| Cluster di calcolo | sì | sì | sì | sì | sì |

Quando vengono create, queste risorse di calcolo fanno automaticamente parte dell'area di lavoro, a differenza di altri tipi di destinazioni di calcolo.

### <a name="compute-clusters"></a>Cluster di elaborazione

È possibile usare i cluster di calcolo di Azure Machine Learning per il training e per l'inferenza di batch (anteprima).  Con questa risorsa di calcolo, è necessario:With this compute resource, you have:

* Cluster a singolo o più nodi
* Scalabilità automatica ogni volta che si invia una corsa 
* Gestione automatica dei cluster e pianificazione dei processi 
* Include il supporto per le risorse di CPU e GPU



## <a name="unmanaged-compute"></a>Calcolo non gestito

Una destinazione di calcolo non gestita non è gestita da Azure Machine Learning.An unmanaged compute target is *not* managed by Azure Machine Learning. Questo tipo di destinazione di calcolo viene creato all'esterno di Azure Machine Learning e quindi collegarlo all'area di lavoro. Le risorse di calcolo non gestite possono richiedere passaggi aggiuntivi per gestire o migliorare le prestazioni per i carichi di lavoro di Machine Learning.Unmanaged compute resources can require additional steps for you to maintain or to improve performance for machine learning workloads.

## <a name="next-steps"></a>Passaggi successivi

Viene illustrato come:
* [Impostare una destinazione di calcolo per eseguire il training del modelloSet up a compute target to train your model](how-to-set-up-training-targets.md)
* [Distribuire il modello in una destinazione di calcoloDeploy your model to a compute target](how-to-deploy-and-where.md)