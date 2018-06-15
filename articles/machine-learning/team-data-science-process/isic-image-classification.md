---
title: Classificazione delle immagini con il pacchetto di Azure Machine Learning (AML) per visione artificiale (AMLPCV) e il processo di data science per i team (TDSP) | Microsoft Docs
description: Viene descritto l'utilizzo di TDSP (processo di data science per i team) e AMLPCV per la classificazione delle immagini
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
ms.openlocfilehash: a3dcfd8a9292d31c7342b8d50ec58c0da53318d3
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837219"
---
# <a name="skin-cancer-image-classification-with-azure-machine-learning-aml-package-for-computer-vision-amlpcv-and-team-data-science-process-tdsp"></a>Classificazione delle immagini per i tumori della pelle con il pacchetto Azure Machine Learning (AML) per visione artificiale (AMLPCV) e il processo di data science per i team (TDSP)

## <a name="introduction"></a>Introduzione

Questo articolo illustra come usare il [pacchetto Azure Machine Learning per Visione artificiale (AMLPCV)](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest) per sottoporre a training, testare e distribuire un modello di **classificazione delle immagini**. L'esempio usa la struttura e i modelli di TDSP in [Azure Machine Learning Workbench](https://docs.microsoft.com/en-us/azure/machine-learning/service/quickstart-installation). L'esempio completo è disponibile in questa procedura dettagliata. Viene usato [CNTK](https://www.microsoft.com/en-us/cognitive-toolkit/) come framework di apprendimento avanzato e viene eseguito il training su un computer GPU [macchina virtuale data science](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview). La distribuzione usa l'interfaccia della riga di comando di operazionalizzazione di Azure ML.

Molte applicazioni nell'ambito della visione artificiale possono essere inserite in un frame come problemi di classificazione delle immagini. Queste includono la creazione di modelli che rispondono a domande quali "Nell'immagine è presente un oggetto?" (l'oggetto può essere un *cane*, una *macchina* o una *barca*) oppure a domande più complesse, come: "Quale classe di gravità di malattia oculare si evince dalla scansione della retina di questo paziente?" AMLPCV semplifica l'elaborazione dei dati di classificazione delle immagini e della pipeline di modellazione. 

## <a name="link-to-github-repository"></a>Collegamento al repository GitHub
La documentazione di riepilogo relativa all'esempio è disponibile qui. La documentazione più completa è disponibile nel sito [GitHub](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification).

## <a name="team-data-science-process-tdsp-walkthrough-with-amlpcv"></a>Procedura dettagliata AMLPCV del Processo di data science per i team (TDSP)

La procedura dettagliata usa il ciclo di vita di [Team Data Science Process (TDSP)](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/overview) (Processo di data science per i team - TDSP).

La procedura dettagliata include i passaggi seguenti:

### <a name="1-data-acquisionhttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode01dataacquisitionandunderstanding"></a>[1. Acquisizione di dati](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/01_data_acquisition_and_understanding)
Set di dati ISIC viene usato per l'attività di classificazione di immagini. ISIC (International Skin Imaging Collaboration) è una collaborazione tra il mondo accademico e l'industria per facilitare l'applicazione delle immagini digitali della pelle per lo studio del melanoma e per ridurne la mortalità. L'[archivio ISIC](https://isic-archive.com/#images) contiene oltre 13.000 immagini di lesioni cutanee e la relativa classificazione (benigna o maligna). Scaricare un esempio delle immagini dall'archivio ISIC.

### <a name="2-modelinghttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode02modeling"></a>[2. Modellazione](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/02_modeling)
Nel passaggio di modellazione, vengono eseguiti i passaggi secondari seguenti. 

<b>2.1 Creazione del set di dati</b><br>

Per generare un oggetto set di dati in AMLPCV, fornire una directory radice delle immagini nel disco locale. 

<b>2.2 Visualizzazione e annotazione delle immagini</b><br>

Visualizzare le immagini nell'oggetto set di dati e correggere alcune delle etichette, se necessario.

<b>2.3 Aumento delle immagini</b><br>

Aumentare un oggetto set di dati con le trasformazioni descritte nella raccolta [imgaug](https://github.com/aleju/imgaug).

<b>2.4 Definizione del modello DNN</b><br>

Definire l'architettura del modello usata nel passaggio di training. Sono supportati sei modelli di rete neurali profonde in AMLPCV: AlexNet Resnet-18, Resnet-34, Resnet 50, Resnet 101 e Resnet 152.

<b>2.5 Training del classificatore</b><br>

Eseguire il training delle reti neurali con parametri personalizzati o predefiniti.

<b>2.6 Valutazione e visualizzazione</b><br>

Il passaggio secondario fornisce la funzionalità per valutare le prestazioni del modello con training su un set di dati di test indipendente. Le metriche di valutazione includono accuratezza, precisione e richiamo e curva ROC.

Tali passaggi secondari sono illustrati in dettaglio nel Jupyter Notebook corrispondente. Sono inoltre fornite linee guida per trasformare i parametri, ad esempio velocità di apprendimento, dimensione batch mini e il tasso di dropout per migliorare ulteriormente le prestazioni del modello.

### <a name="3-deploymenthttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode03deployment"></a>[3. Distribuzione](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/03_deployment)

Questo passaggio rende operativo il modello generato nel passaggio di modellazione. Introduce i prerequisiti di operazionalizzazione e la configurazione. Infine, è anche illustrato l'uso del servizio Web. Con questa esercitazione, è possibile imparare a creare modelli di apprendimento avanzato con AMLPCV e rendere operativo il modello in Azure.

## <a name="next-steps"></a>Passaggi successivi
Per iniziare, leggere altra documentazione sul [pacchetto di Azure Machine Learning (AML) per Visione artificiale (AMLPCV)](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest) e sul [processo di data science per i team (TDSP)](https://aka.ms/tdsp).


## <a name="references"></a>Riferimenti

* [Azure Machine Learning Package for Computer Vision (AMLPCV)](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest) (Pacchetto di Azure Machine Learning per Visione artificiale (AMLPCV))
* [Azure Machine Learning Workbench](https://docs.microsoft.com/en-us/azure/machine-learning/service/quickstart-installation)
* [Macchina virtuale data science](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview)

