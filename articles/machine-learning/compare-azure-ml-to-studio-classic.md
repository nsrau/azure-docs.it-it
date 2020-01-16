---
title: Azure Machine Learning e Machine Learning Studio (versione classica)
description: Differenze tra Azure Machine Learning e Machine Learning Studio (versione classica)
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 10/29/2019
ms.openlocfilehash: a3122a3ae1687369b87d193efc693b3b7c659aac
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75974144"
---
# <a name="how-azure-machine-learning-differs-from-machine-learning-studio-classic"></a>Differenze tra Azure Machine Learning e Machine Learning Studio (versione classica)

Questo articolo confronta le caratteristiche, le funzionalità e l'interfaccia di Azure Machine Learning e Machine Learning Studio (versione classica). 

## <a name="about-machine-learning-studio-classic"></a>Informazioni su Machine Learning Studio (versione classica)
[Machine Learning Studio (versione classica)](studio/what-is-ml-studio.md) è un'area di lavoro collaborativa e grafica con trascinamento della selezione in cui è possibile creare, testare e distribuire soluzioni di Machine Learning senza la necessità di scrivere codice. Usa algoritmi di Machine Learning e moduli di gestione dei dati precompilati e preconfigurati, oltre a una piattaforma di calcolo proprietaria.

## <a name="about-azure-machine-learning"></a>Informazioni su Azure Machine Learning

[Azure Machine Learning](overview-what-is-azure-ml.md) offre **sia** un'interfaccia Web denominata finestra di progettazione (anteprima) che vari SDK e interfacce della riga di comando per preparare rapidamente i dati, nonché per eseguire il training dei modelli di Machine Learning e distribuirli. Azure Machine Learning offre scalabilità, supporto di più framework e funzionalità avanzate di Machine Learning quali il servizio Machine Learning automatizzato e il supporto di pipeline.

La finestra di progettazione di Azure Machine Learning fornisce un'esperienza di trascinamento della selezione simile a Studio (versione classica). A differenza della piattaforma di calcolo proprietaria di Studio, tuttavia, la finestra di progettazione usa le risorse di calcolo dell'utente, è scalabile ed è pienamente integrata in Azure Machine Learning.  

> [!TIP]
> I clienti che attualmente usano o valutano Machine Learning Studio (versione classica) sono invitati a provare la [finestra di progettazione di Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-designer) (anteprima), che fornisce moduli di ML di trascinamento della selezione __più__ scalabilità, controllo della versione e sicurezza aziendale.

## <a name="comparison-azure-machine-learning-vs-machine-learning-studio-classic"></a>Confronto: Azure Machine Learning e Machine Learning Studio (versione classica)

Ecco un confronto rapido.

||  Finestra di progettazione di Azure Machine Learning|Studio (versione classica) |
|---| --- | --- |
||La finestra di progettazione è disponibile in anteprima, mentre Azure Machine Learning è disponibile a livello generale|Disponibile a livello generale | 
|Interfaccia di trascinamento della selezione| Sì | Sì|
|Esperimento| Ridimensionamento con destinazione di calcolo|Scalabilità (limite dei dati di training di 10 GB) | 
|Moduli per l'interfaccia| [Molti moduli tra quelli più diffusi](algorithm-module-reference/module-reference.md) | Molti |
|Destinazioni di calcolo del training| Calcolo di AML (GPU/CPU)|Destinazione di calcolo proprietaria, solo CPU|
|Destinazioni di calcolo di inferenza| Servizio Azure Kubernetes per inferenza in tempo reale <br/>Ambiente di calcolo di AML per inferenza batch|Formato di servizio Web proprietario, non personalizzabile | 
|Pipeline di Machine Learning| Creazione di pipeline <br/> Pipeline pubblicata <br/> Endpoint della pipeline <br/> [Altre informazioni sulle pipeline di Machine Learning](concept-ml-pipelines.md)|Non supportate | 
|Operazioni di Machine Learning| Distribuzione configurabile, controllo delle versioni dei modelli e delle pipeline|Gestione e distribuzione dei modelli di base | 
|Modello| Formato standard, varia a seconda del processo di training|Formato proprietario e non portabile.| 
|Training di modelli automatizzato|Non ancora disponibile nella finestra di progettazione, ma possibile tramite l'interfaccia e gli SDK.| No | 

## <a name="get-started-with-azure-machine-learning"></a>Introduzione ad Azure Machine Learning

Le risorse seguenti consentono di acquisire familiarità con Azure Machine Learning

- Leggere la [panoramica di Azure Machine Learning](tutorial-first-experiment-automated-ml.md) 

- [Creare la prima pipeline della finestra di progettazione](tutorial-designer-automobile-price-train-score.md) per stimare i prezzi delle automobili.

![Esempio di finestra di progettazione di Azure Machine Learning](media/concept-designer/designer-drag-and-drop.gif)

## <a name="next-steps"></a>Passaggi successivi

Oltre alle funzionalità di trascinamento della selezione della finestra di progettazione, in Azure Machine Learning sono disponibili altri strumenti:  
  + [Usare i notebook di Python per eseguire il training e la distribuzione di modelli di Machine Learning](tutorial-1st-experiment-sdk-setup.md)
  + [Usare R Markdown per eseguire il training e la distribuzione di modelli di Machine Learning](tutorial-1st-r-experiment.md) 
  + [Usare il servizio Machine Learning automatizzato per eseguire il training e la distribuzione di modelli di Machine Learning](tutorial-designer-automobile-price-train-score.md) 
  + [Usare l'interfaccia della riga di comando di Machine Learning per eseguire il training e la distribuzione di un modello](tutorial-train-deploy-model-cli.md)

