---
title: Classificazione delle immagini con il pacchetto di Azure Machine Learning per Visione artificiale e il processo di data science per i team (TDSP) | Microsoft Docs
description: Descrive l'utilizzo del processo di data science per i team (TDSP) e il pacchetto di Azure di Machine Learning per Visione artificiale per la classificazione delle immagini.
services: machine-learning, team-data-science-process
documentationcenter: ''
author: xibingao
manager: deguhath
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: xibingao
ms.openlocfilehash: f9e88cfb7185845e96f287b39bebaaa24320f537
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/21/2018
ms.locfileid: "36300797"
---
# <a name="skin-cancer-image-classification-with-the-azure-machine-learning-package-for-computer-vision-and-team-data-science-process"></a>Classificazione delle immagini per i tumori della pelle con il pacchetto di Azure Machine Learning per Visione artificiale e il processo di data science per i team

Questo articolo illustra come usare il [pacchetto di Azure Machine Learning per Visione artificiale](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest) per sottoporre a training, testare e distribuire un modello di *classificazione delle immagini*. Nell'esempio viene usata la struttura del processo di data science per i team (TDSP) e i modelli in [Azure Machine Learning Workbench](https://docs.microsoft.com/en-us/azure/machine-learning/service/quickstart-installation). L'esempio completo è disponibile in questa procedura dettagliata. Viene usato [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) come framework di apprendimento avanzato e viene eseguito il training su un computer GPU [Data Science Virtual Machine](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview). La distribuzione usa l'interfaccia della riga di comando di operazionalizzazione di Azure Machine Learning Studio.

Molte applicazioni nell'ambito della visione artificiale possono essere inserite in un frame come problemi di classificazione delle immagini. Queste includono la creazione di modelli che rispondono a domande semplici come "Nell'immagine è presente un oggetto?", in cui l'oggetto potrebbe essere un cane, un'auto o una barca. Include anche domande più complesse, come: "Quale classe di gravità di malattia oculare si evince dalla scansione della retina di questo paziente?" Il pacchetto di Azure Machine Learning per Visione artificiale semplifica l'elaborazione dei dati di classificazione delle immagini e della pipeline di modellazione. 

## <a name="link-to-the-github-repository"></a>Collegamento al repository GitHub
Questo articolo è un documento di riepilogo sull'esempio. La documentazione più completa è disponibile nel sito [GitHub](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification).

## <a name="team-data-science-process-walkthrough"></a>Procedura dettagliata del processo di data science per i team

La procedura dettagliata usa il ciclo di vita del [processo di data science per i team](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/overview). La procedura dettagliata include i passaggi seguenti del ciclo di vita.

### <a name="1-data-acquisitionhttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode01dataacquisitionandunderstanding"></a>[1. Acquisizione dei dati](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/01_data_acquisition_and_understanding)
Il set di dati Skin Imaging Collaboration (ISIC) viene usato per l'attività di classificazione di immagini. ISIC è una collaborazione tra il mondo accademico e l'industria per facilitare l'applicazione delle immagini digitali della pelle per lo studio del melanoma e per ridurne la mortalità. L'[archivio ISIC](https://isic-archive.com/#images) contiene oltre 13.000 immagini di lesioni cutanee e la relativa classificazione, benigna o maligna. Scaricare un esempio delle immagini dall'archivio ISIC.

### <a name="2-modelinghttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode02modeling"></a>[2. Modellazione](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/02_modeling)
Nel passaggio di modellazione vengono eseguiti i passaggi secondari seguenti.

#### <a name="dataset-creation"></a>Creazione del set di dati

Per generare un oggetto set di dati con il pacchetto di Azure Machine Learning per Visione artificiale, specificare la directory radice delle immagini nel disco locale. 

#### <a name="image-visualization-and-annotation"></a>Visualizzazione e annotazione delle immagini

Visualizzare le immagini nell'oggetto set di dati e correggere le etichette in base alle esigenze.

#### <a name="image-augmentation"></a>Aumento delle immagini

Aumentare un oggetto set di dati con le trasformazioni descritte nella raccolta [imgaug](https://github.com/aleju/imgaug).

#### <a name="dnn-model-definition"></a>Definizione del modello DNN

Definire l'architettura del modello usata nel passaggio di training. Nel pacchetto di Azure Machine Learning per Visione artificiale sono supportati sei modelli di rete neurali profonde: AlexNet, Resnet-18, Resnet-34, Resnet-50, Resnet-101 e Resnet-152.

#### <a name="classifier-training"></a>Training del classificatore

Eseguire il training delle reti neurali con parametri personalizzati o predefiniti.

#### <a name="evaluation-and-visualization"></a>Valutazione e visualizzazione

Fornisce la funzionalità per valutare le prestazioni del modello con training su un set di dati di test indipendente. Le metriche di valutazione includono accuratezza, precisione e richiamo e curva ROC.

I passaggi secondari sono illustrati in dettaglio nel Jupyter Notebook corrispondente. Sono inoltre fornite linee guida per trasformare i parametri, ad esempio velocità di apprendimento, dimensione batch mini e il tasso di dropout per migliorare ulteriormente le prestazioni del modello.

### <a name="3-deploymenthttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode03deployment"></a>[3. Distribuzione](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/03_deployment)

Questo passaggio rende operativo il modello generato nel passaggio di modellazione. Introduce i prerequisiti e la configurazione necessaria. È illustrato anche l'utilizzo del servizio Web. In questa esercitazione si apprenderà come creare modelli di apprendimento avanzato con il pacchetto di Azure Machine Learning per Visione artificiale e rendere operativo il modello in Azure.

## <a name="next-steps"></a>Passaggi successivi
- Leggere altri documenti sul [pacchetto di Azure Machine Learning per Visione artificiale](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest).
- Per iniziare, leggere la documentazione sul [processo di data science per i team](https://aka.ms/tdsp).


## <a name="references"></a>Riferimenti

* [Azure Machine Learning Package for Computer Vision](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest) (Pacchetto di Azure Machine Learning per Visione artificiale)
* [Azure Machine Learning Workbench](https://docs.microsoft.com/en-us/azure/machine-learning/service/quickstart-installation)
* [Data Science Virtual Machine](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview)

