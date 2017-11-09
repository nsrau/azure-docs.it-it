---
title: Soluzione predittiva per il rischio di credito con Machine Learning | Documentazione Microsoft
description: Una procedura dettagliata che mostra come creare una soluzione di analisi predittiva per la valutazione del rischio di credito in Azure Machine Learning Studio.
keywords: rischio di credito, soluzione di analisi predittiva, valutazione del rischio
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 43300854-a14e-4cd2-9bb1-c55c779e0e93
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/23/2017
ms.author: garye
ms.openlocfilehash: fe504d826b6c40099f1f8706ef7e8780eed5cf9a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="walkthrough-develop-a-predictive-analytics-solution-for-credit-risk-assessment-in-azure-machine-learning"></a>Procedura dettagliata: Sviluppare una soluzione di analisi predittiva per la valutazione del rischio di credito in Azure Machine Learning

In questa procedura dettagliata viene esaminato il processo di sviluppo di una soluzione di analisi predittiva in Machine Learning Studio. Viene sviluppato un modello semplice in Machine Learning Studio, che viene quindi distribuito come servizio Web di Azure Machine Learning, in cui il modello può creare previsioni usando nuovi dati. 

In questa procedura dettagliata si presuppone che Machine Learning Studio sia già stato usato almeno una volta e che alcuni concetti di Machine Learning siano noti, ma non si dà per scontato che l'utente sia un esperto.

Se non si ha alcuna esperienza di **Azure Machine Learning Studio**, è consigliabile iniziare con l'esercitazione [Creare il primo esperimento di analisi scientifica dei dati in Azure Machine Learning Studio](create-experiment.md), Tale esercitazione guida l'utente nel primo uso di Machine Learning Studio, offre le nozioni di base su come trascinare moduli nell'esperimento, connetterli, eseguire l'esperimento ed esaminare i risultati. Un altro strumento che potrebbe essere utile all'inizio è un diagramma che offre una panoramica delle funzionalità di Machine Learning Studio. È possibile scaricarlo e stamparlo da qui: [Diagramma della panoramica delle funzionalità di Azure Machine Learning Studio](studio-overview-diagram.md).
 
Se non si ha familiarità con il campo dell'apprendimento automatico in generale, è disponibile una serie di video che possono risultare utile all'utente. Si chiama [Analisi scientifica dei dati per principianti](data-science-for-beginners-the-5-questions-data-science-answers.md) e offre un'ottima introduzione all'apprendimento automatico grazie a un linguaggio e a concetti comuni.


[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]
 

## <a name="the-problem"></a>Problema

Si supponga di dover prevedere il rischio di credito di un soggetto in base alle informazioni fornite in una richiesta di credito.  

La valutazione del rischio di credito è un problema complesso, ma è possibile semplificarla per questa procedura dettagliata. Verrà usata come esempio di come è possibile creare una soluzione di analisi predittiva con Microsoft Azure Machine Learning Studio. Per farlo, vengono usati Azure Machine Learning Studio e i servizi Web di Machine Learning.  

## <a name="the-solution"></a>Soluzione

In questa procedura dettagliata, si inizia con dati sul rischio di credito disponibili pubblicamente, quindi si sviluppa e si crea un modello predittivo in base a tali dati. Viene poi distribuito il modello come servizio Web così da poter essere usato da altri utenti per la valutazione del rischio di credito.

Per creare la soluzione di valutazione del rischio di credito, seguire la procedura seguente:  

1. [Creare un'area di lavoro di Machine Learning](walkthrough-1-create-ml-workspace.md)
2. [Caricare i dati esistenti](walkthrough-2-upload-data.md)
3. [Creare un esperimento](walkthrough-3-create-new-experiment.md)
4. [Eseguire il training e valutare i modelli](walkthrough-4-train-and-evaluate-models.md)
5. [Distribuire il servizio web](walkthrough-5-publish-web-service.md)
6. [Accedere al servizio Web](walkthrough-6-access-web-service.md)

> [!TIP] 
> È possibile trovare una copia di lavoro dell'esperimento sviluppato in questa procedura dettaglia in [Cortana Intelligence Gallery](https://gallery.cortanaintelligence.com) (Raccolta di Cortana Intelligence). Passare a **[Walkthrough - Credit risk prediction](https://gallery.cortanaintelligence.com/Experiment/Walkthrough-Credit-risk-prediction-1)** (Procedura dettagliata: previsione del rischio di credito) e fare clic su **Apri in Studio** per scaricare una copia dell'esperimento nell'area di lavoro di Machine Learning Studio.
> 
> Questa procedura dettagliata è basata su una versione semplificata dell'esperimento di esempio [Binary Classfication: Credit risk prediction](http://go.microsoft.com/fwlink/?LinkID=525270) (Classificazione binaria: previsione del rischio di credito) nella [raccolta](http://gallery.cortanaintelligence.com/).
