---
title: Azure Machine Learning e Machine Learning Studio (versione classica)
description: Qual è la differenza tra Azure Machine Learning e Machine Learning Studio (versione classica)?
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 03/25/2020
ms.openlocfilehash: 1d4896a2dd2115e5654baffe394618b30a294d5b
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2020
ms.locfileid: "86231744"
---
# <a name="azure-machine-learning-vs-machine-learning-studio-classic"></a>Azure Machine Learning e Machine Learning Studio (versione classica)

In questo articolo viene illustrata la differenza tra Azure Machine Learning e Machine Learning Studio (versione classica). 

Azure Machine Learning fornisce Python SDK e R SDK **e** la finestra di progettazione con "trascinamento della selezione" per compilare e distribuire modelli di Machine Learning. Studio (versione classica) offre un'esperienza di trascinamento della selezione autonoma.

È consigliabile che i nuovi utenti scelgano Azure Machine Learning per la più ampia gamma di strumenti di Machine Learning all'avanguardia.

## <a name="quick-comparison"></a>Confronto rapido

Nella tabella seguente sono riepilogate alcune delle differenze principali tra Azure Machine Learning e Studio (versione classica):

| Funzionalità | Machine Learning Studio (versione classica) | Azure Machine Learning |
|---| --- | --- |
| Interfaccia per il trascinamento della selezione | Supportato | Supportato - [Finestra di progettazione di Azure Machine Learning (anteprima)](concept-designer.md) <br/>(Richiede l'area di lavoro Enterprise) | 
| Esperimento | Scalabile (limite dei dati di training di 10 GB) | Ridimensionamento con destinazione di calcolo |
| Destinazioni di calcolo del training | Destinazione di calcolo proprietaria, solo supporto di CPU | Ampia gamma di [destinazioni di calcolo per il training](concept-compute-target.md#train) personalizzabili. Include il supporto di GPU e CPU | 
| Destinazioni di calcolo della distribuzione | Formato di servizio Web proprietario, non personalizzabile | Ampia gamma di [destinazioni di calcolo per la distribuzione](concept-compute-target.md#deploy) personalizzabili. Include il supporto di GPU e CPU |
| Pipeline di Machine Learning | Non supportate | Creazione di [pipeline](concept-ml-pipelines.md) flessibili e modulari per automatizzare i flussi di lavoro |
| MLOps | Gestione e distribuzione dei modelli di base | Controllo delle versioni delle entità (modello, dati, flussi di lavoro), automazione dei flussi di lavoro, integrazione con strumenti CICD, [e altro ancora](concept-model-management-and-deployment.md) |
| Formato dei modelli | Formato proprietario, solo Studio (versione classica) | Più formati supportati a seconda del tipo di processo di training |
| Training automatizzato dei modelli e ottimizzazione degli iperparametri |  Non supportate | [Supportato nell'SDK e nell'area di lavoro visiva](concept-automated-ml.md) | 
| Rilevamento deriva dei dati | Non supportate | [Supportato nell'SDK e nell'area di lavoro visiva](how-to-monitor-datasets.md) |


## <a name="migrate-from-machine-learning-studio-classic"></a>Migrazione da Machine Learning Studio (versione classica)

Attualmente non è possibile eseguire la migrazione delle risorse di Studio (versione classica) alla finestra di progettazione di Azure Machine Learning (anteprima). Gli utenti di Studio (versione classica) correnti possono continuare a usare le proprie risorse di Machine Learning. Tuttavia, invitiamo tutti gli utenti a prendere in considerazione l'uso della finestra di progettazione, che fornisce un'esperienza familiare di trascinamento della selezione con un flusso di lavoro migliorato **oltre a** scalabilità, controllo della versione e sicurezza aziendale.

## <a name="get-started-with-azure-machine-learning"></a>Introduzione ad Azure Machine Learning

Le risorse seguenti consentono di acquisire familiarità con Azure Machine Learning. 

- Leggere la [panoramica di Azure Machine Learning](overview-what-is-azure-ml.md).

- Creare il [primo esperimento con Python SDK](tutorial-1st-experiment-sdk-setup.md).

- [Creare la prima pipeline della finestra di progettazione](tutorial-designer-automobile-price-train-score.md) per stimare i prezzi delle automobili.

![Esempio di finestra di progettazione di Azure Machine Learning](media/concept-designer/designer-drag-and-drop.gif)

## <a name="next-steps"></a>Passaggi successivi

Oltre alle funzionalità di trascinamento della selezione della finestra di progettazione, in Azure Machine Learning sono disponibili altri strumenti:  
  + [Usare i notebook di Python per eseguire il training e la distribuzione di modelli di Machine Learning](tutorial-1st-experiment-sdk-setup.md)
  + [Usare R Markdown per eseguire il training e la distribuzione di modelli di Machine Learning](tutorial-1st-r-experiment.md) 
  + [Usare il servizio Machine Learning automatizzato per eseguire il training e la distribuzione di modelli di Machine Learning](tutorial-first-experiment-automated-ml.md)  
  + [Usare l'interfaccia della riga di comando di Machine Learning per eseguire il training e la distribuzione di un modello](tutorial-train-deploy-model-cli.md)

