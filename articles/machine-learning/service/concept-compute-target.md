---
title: 'Destinazioni di calcolo: dove eseguire il training e distribuire i modelli'
titleSuffix: Azure Machine Learning service
description: Definire il punto in cui si vuole eseguire il training o distribuire il modello con Azure Machine Learning servizio.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 07/10/2019
ms.openlocfilehash: b2e4c9160ccad66b04dd30979f0568da68836d76
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860597"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning-service"></a>Che cosa sono le destinazioni di calcolo nel servizio Azure Machine Learning? 

Una **destinazione di calcolo** è una risorsa o un ambiente di calcolo designato in cui si esegue lo script di training o si ospita la distribuzione del servizio. Questo percorso può essere il computer locale o una risorsa di calcolo basata sul cloud. L'uso di destinazioni di calcolo semplifica la modifica dell'ambiente di calcolo in un secondo momento senza dover modificare il codice.  

In un ciclo di vita di sviluppo di modelli tipico, è possibile:
1. Inizia sviluppando e sperimentando una piccola quantità di dati. In questa fase è consigliabile usare l'ambiente locale (computer locale o macchina virtuale basata sul cloud) come destinazione di calcolo. 
2. Scalabilità verticale a dati più grandi oppure Esegui il training distribuito usando una di queste [destinazioni di calcolo](#train)per il training.  
3. Quando il modello è pronto, è possibile distribuirlo in un ambiente di hosting Web o in un dispositivo tutto con una di queste [destinazioni di calcolo della distribuzione](#deploy).

Le risorse di calcolo usate per le destinazioni di calcolo sono collegate a un' [area di lavoro](concept-workspace.md). Le risorse di calcolo diverse dal computer locale sono condivise dagli utenti dell'area di lavoro.

## <a name="train"></a>Training delle destinazioni di calcolo

Il servizio Azure Machine Learning ha un supporto variabile tra le diverse risorse di calcolo.  È anche possibile aggiungere una risorsa di calcolo personalizzata, anche se il supporto per vari scenari può variare.

[!INCLUDE [aml-compute-target-train](../../../includes/aml-compute-target-train.md)]

Altre informazioni sulla [configurazione e l'uso di una destinazione di calcolo per il training del modello](how-to-set-up-training-targets.md).

## <a name="deploy"></a>Destinazioni di distribuzione

Per ospitare la distribuzione del modello, è possibile usare le risorse di calcolo seguenti.

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

Informazioni su [dove e come distribuire il modello in una destinazione di calcolo](how-to-deploy-and-where.md).

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Calcolo Azure Machine Learning (gestito)

Una risorsa di calcolo gestita viene creata e gestita dal servizio Azure Machine Learning. Questo calcolo è ottimizzato per i carichi di lavoro di machine learning. Azure Machine Learning calcolo è l'unico calcolo gestito a partire dal 30 maggio 2019. In futuro potrebbero essere aggiunte risorse di calcolo gestite aggiuntive.

È possibile usare Azure Machine Learning calcolo per il training e l'inferenza in batch (anteprima).  Con questa risorsa di calcolo sono disponibili:

* Cluster a nodo singolo o a più nodi
* Scalabilità automatica ogni volta che si invia un'esecuzione 
* Gestione automatica dei cluster e pianificazione dei processi 
* Include il supporto per le risorse di CPU e GPU

È possibile creare Azure Machine Learning istanze di calcolo in portale di Azure o nella [pagina di destinazione dell'area di lavoro (anteprima)](https://ml.azure.com), con l'SDK o con l'interfaccia della riga di comando. Quando viene creato, viene automaticamente inclusa nell'area di lavoro, a differenza di altri tipi di destinazioni di calcolo.

## <a name="unmanaged-compute"></a>Calcolo non gestito

Una destinazione di calcolo *non gestita non* è gestita dal servizio Azure Machine Learning. Questo tipo di destinazione di calcolo viene creato all'esterno Azure Machine Learning, quindi collegato all'area di lavoro. Le risorse di calcolo non gestite possono richiedere passaggi aggiuntivi per la manutenzione o per migliorare le prestazioni dei carichi di lavoro di machine learning.

## <a name="next-steps"></a>Passaggi successivi

È possibile passare agli argomenti seguenti:
* [Configurare una destinazione di calcolo per il training del modello](how-to-set-up-training-targets.md)
* [Distribuire il modello in una destinazione di calcolo](how-to-deploy-and-where.md)