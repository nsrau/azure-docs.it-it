---
title: Destinazioni di calcolo
titleSuffix: Azure Machine Learning service
description: Una destinazione di calcolo consente di specificare la risorsa di calcolo in cui si esegue lo script di training o di un host la distribuzione del servizio. Questo percorso può essere nel computer locale o una risorsa di calcolo basati sul cloud.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/30/2019
ms.openlocfilehash: 42c0f5460a63b781aafdd43410761e2d7b17944d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66755351"
---
#  <a name="what-is-a-compute-target-in-azure-machine-learning-service"></a>Che cos'è una destinazione di calcolo nel servizio Azure Machine Learning? 

Una destinazione di calcolo consente di specificare la risorsa di calcolo in cui si esegue lo script di training o di un host la distribuzione del servizio. Questo percorso può essere nel computer locale o una risorsa di calcolo basati sul cloud.

Le destinazioni di calcolo rendono più semplice modificare l'ambiente di calcolo senza modificare il codice.  Un ciclo di vita di sviluppo tipico modello:

* Iniziare con dev/sperimentazione in una piccola quantità di dati. In questa fase è consigliabile usare un ambiente locale, ad esempio il computer locale o una macchina virtuale basata sul cloud.
* Aumentare il training sul set di dati di dimensioni maggiori o distribuito usando uno dei corsi di formazione il [destinazioni training](#train).  
* Distribuire gli ambienti di hosting web diversi o a un dispositivo IoT usando uno dei [destinazioni di distribuzione](#deploy).

Le risorse di calcolo usate per le destinazioni di calcolo collegate a un [dell'area di lavoro](concept-workspace.md). Calcolo delle risorse diverso dal computer locale vengono condivisi dagli utenti dell'area di lavoro.

## <a name="train"></a> Destinazioni di training

Servizio Azure Machine Learning include il supporto di diverse tra le risorse di calcolo diversi.  È possibile collegare la propria risorsa di calcolo, anche se il supporto per i diversi scenari può variare, come indicato di seguito:

[!INCLUDE [aml-compute-target-train](../../../includes/aml-compute-target-train.md)]


## <a name="deploy"></a>Destinazioni di distribuzione

Le seguenti risorse di calcolo sono utilizzabile per ospitare la distribuzione del modello.

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]


## <a name="managed-compute"></a>Calcolo gestito

Una risorsa di calcolo gestita viene creata e gestita dal servizio Azure Machine Learning. Questo calcolo è ottimizzata per carichi di lavoro di machine learning. Calcolo di Azure Machine Learning è il calcolo gestita solo a partire da 30 maggio 2019. È possibile aggiungere risorse di calcolo gestiti aggiuntive in futuro.

### <a name="amlcompute"></a> Calcolo di Azure Machine Learning

È possibile usare il programma di calcolo di Azure Machine Learning per il training e inferenza dei batch (anteprima).  Con questa risorsa di calcolo, è necessario:

* Cluster singolo o più node
* Viene ridimensionato automaticamente ogni volta che si invia un'esecuzione 
* Gestione di cluster automatico e pianificazione dei processi 
* Include il supporto per le risorse di CPU e GPU

È possibile creare istanze di calcolo di Azure Machine Learning con una qualsiasi delle operazioni seguenti:

* Portale di Azure
* Azure Machine Learning SDK
* Interfaccia della riga di comando di Azure

Tutte le altre risorse di calcolo devono essere creati di fuori l'area di lavoro e quindi collegati a esso.

## <a name="unmanaged-compute"></a>Calcolo non gestito

È una risorsa di calcolo non gestito *non* gestito dal servizio di Azure Machine Learning. Creare questo tipo di calcolo di fuori di Azure Machine Learning, quindi allegarlo all'area di lavoro. Le risorse di calcolo non gestito possono richiedere passaggi aggiuntivi che è possibile mantenere o migliorare le prestazioni per i carichi di lavoro di machine learning.

## <a name="next-steps"></a>Passaggi successivi

* [Configurare le destinazioni di calcolo per il training del modello](how-to-set-up-training-targets.md)
* [Distribuire modelli con il servizio di Azure Machine Learning](how-to-deploy-and-where.md)