---
title: 'Le destinazioni di calcolo: la posizione in cui eseguire il training e distribuire modelli'
titleSuffix: Azure Machine Learning service
description: Definire in cui si desidera eseguire il training o distribuire il modello con il servizio di Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 07/10/2019
ms.openlocfilehash: a7944b284a9c1c0424af54874554d05d49ad4b20
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67806030"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning-service"></a>Quali sono le destinazioni di calcolo nel servizio Azure Machine Learning? 

Oggetto **destinazione di calcolo** è un risorsa/ambiente di calcolo designata in cui si esegue lo script di training o di un host la distribuzione del servizio. Questo percorso può essere nel computer locale o una risorsa di calcolo basati sul cloud. Utilizzando destinazioni rendono più semplice in cui in seguito si modifica l'ambiente di calcolo senza dover modificare il codice di calcolo.  

In un ciclo di vita di sviluppo tipico modello, è possibile:
1. Avviare lo sviluppo e la sperimentazione a una piccola quantità di dati. In questa fase, è consigliabile locale ambiente (computer locale o macchina virtuale basata su cloud) come destinazione di calcolo. 
2. Scalabilità verticale a dati di dimensioni maggiori o distribuito usando uno di questi corsi di formazione [destinazioni di calcolo training](#train).  
3. Quando il modello è pronto, distribuirla in un dispositivo IoT con uno di questi o ambiente di hosting web [destinazioni di calcolo distribuzione](#deploy).

Le risorse di calcolo usate per le destinazioni di calcolo collegate a un [dell'area di lavoro](concept-workspace.md). Calcolo delle risorse diverso dal computer locale vengono condivisi dagli utenti dell'area di lavoro.

## <a name="train"></a> Destinazioni di calcolo di training

Servizio Azure Machine Learning include il supporto di diverse tra le risorse di calcolo diversi.  È anche possibile collegare la propria risorsa di calcolo, anche se il supporto per vari scenari possono variare.

[!INCLUDE [aml-compute-target-train](../../../includes/aml-compute-target-train.md)]

Altre informazioni sulle [impostazione e l'utilizzo di una destinazione di calcolo per il training del modello](how-to-set-up-training-targets.md).

## <a name="deploy"></a>Destinazioni di distribuzione

Le seguenti risorse di calcolo sono utilizzabile per ospitare la distribuzione del modello.

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

Scopri [dove e come distribuire il modello in una destinazione di calcolo](how-to-deploy-and-where.md).

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Calcolo di Machine Learning di Azure (gestito)

Una risorsa di calcolo gestita viene creata e gestita dal servizio Azure Machine Learning. Questo calcolo è ottimizzata per carichi di lavoro di machine learning. Calcolo di Azure Machine Learning è il calcolo gestita solo a partire da 30 maggio 2019. È possibile aggiungere risorse di calcolo gestiti aggiuntive in futuro.

È possibile usare il programma di calcolo di Azure Machine Learning per il training e inferenza dei batch (anteprima).  Con questa risorsa di calcolo, è necessario:

* Cluster singolo o più node
* Viene ridimensionato automaticamente ogni volta che si invia un'esecuzione 
* Gestione di cluster automatico e pianificazione dei processi 
* Include il supporto per le risorse di CPU e GPU

È possibile creare istanze di calcolo di Azure Machine Learning nel portale di Azure con il SDK o con l'interfaccia della riga di comando. Quando viene creato automaticamente è parte dell'area di lavoro a differenza di altri tipi di destinazioni di calcolo.

## <a name="unmanaged-compute"></a>Calcolo non gestito

È una destinazione di calcolo non gestito *non* gestito dal servizio di Azure Machine Learning. Creare questo tipo di destinazione di calcolo di fuori di Azure Machine Learning, quindi allegarlo all'area di lavoro. Le risorse di calcolo non gestito possono richiedere passaggi aggiuntivi che è possibile mantenere o migliorare le prestazioni per i carichi di lavoro di machine learning.

## <a name="next-steps"></a>Passaggi successivi

È possibile passare agli argomenti seguenti:
* [Configurare una destinazione di calcolo per il training del modello](how-to-set-up-training-targets.md)
* [Distribuire il modello a una destinazione di calcolo](how-to-deploy-and-where.md)