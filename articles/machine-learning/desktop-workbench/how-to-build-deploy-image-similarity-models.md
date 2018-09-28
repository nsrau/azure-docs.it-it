---
title: Compilare e distribuire un modello di somiglianza delle immagini tramite Azure Machine Learning Package per Visione artificiale.
description: Informazioni su come compilare, sottoporre a training, testare e distribuire un modello di somiglianza delle immagini con visione artificiale usando Azure Machine Learning Package per Visione artificiale.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 05/20/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 371903e1ee080d2b98fd46ac4d6d9838416e1335
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46973423"
---
# <a name="build-and-deploy-image-similarity-models-with-azure-machine-learning"></a>Compilare e distribuire modelli di somiglianza delle immagini con Azure Machine Learning

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]


In questo articolo si apprende come usare Azure Machine Learning Package per Visione artificiale (AMLPCV) per sottoporre a training, testare e distribuire un modello di somiglianza delle immagini. Per una panoramica di questo pacchetto e la relativa documentazione di riferimento dettagliata, vedere https://aka.ms/aml-packages/vision.

Un numero elevato di problemi nell'ambito della visione artificiale possono essere risolti con la somiglianza delle immagini. Questi problemi includono la creazione di modelli che rispondono a domande come:
+ _È presente un oggetto nell'immagine? Ad esempio "cane", "automobile", "nave" e così via_
+ _Quale classe di gravità di malattia oculare si evince dalla scansione della retina di questo paziente?_

Quando si compila e si distribuisce questo modello con Azure Machine Learning Package per Visione artificiale, si segue questa procedura:
1. Creazione del set di dati
2. Visualizzazione e annotazione delle immagini
3. Aumento delle immagini
4. Definizione di un modello DNN (Deep Neural Network, Rete neurale profonda)
5. Training del classificatore
6. Valutazione e visualizzazione
7. Distribuzione del servizio Web
8. Test di carico del servizio Web

Viene usato [CNTK](https://www.microsoft.com/cognitive-toolkit/) come framework di Deep Learning, il training viene eseguito localmente in un computer con tecnologica GPU, ad esempio la [VM per l'analisi scientifica dei dati di Deep Learning](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview), e la distribuzione usa l'interfaccia della riga di comando di operazionalizzazione di Azure Machine Learning.

## <a name="prerequisites"></a>Prerequisiti

1. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

1. È necessario che gli account e l'applicazione seguenti siano configurati e installati:
   - Un account Sperimentazione di Azure Machine Learning 
   - Un account di Gestione modelli di Azure Machine Learning
   - Azure Machine Learning Workbench installato

   Se questi tre elementi non sono stati ancora creati o installati, seguire le istruzioni dell'articolo [Guida introduttiva: Installare e iniziare a usare i servizi di Azure Machine Learning](../desktop-workbench/quickstart-installation.md). 

1. È necessario che sia installato Azure Machine Learning Package per Visione artificiale. Informazioni su come installare questo pacchetto come descritto in https://aka.ms/aml-packages/vision.

## <a name="sample-data-and-notebook"></a>Dati e notebook di esempio

### <a name="get-the-jupyter-notebook"></a>Ottenere il notebook di Jupyter

Scaricare il notebook per eseguire gli esempi descritti qui.

> [!div class="nextstepaction"]
> [Scaricare il notebook di Jupyter](https://aka.ms/aml-packages/vision/notebooks/object_detection)

### <a name="load-the-sample-data"></a>Caricare i dati di esempio

L'esempio seguente usa un set di dati costituito da 63 immagini di articoli per la tavola. Ogni immagine è etichettata come appartenente a una di quattro classi diverse (bowl, cup, cutlery, plate). Il numero di immagini in questo esempio è limitato per un'esecuzione rapida. Nella pratica dovrebbero essere presenti almeno 100 immagini per ogni classe. Tutte le immagini si trovano in *"../sample_data/imgs_recycling/"*, nelle sottodirectory denominate "bowl", "cup", "cutlery" e "plate".

![Set di dati di Azure Machine Learning](media/how-to-build-deploy-image-classification-models/recycling_examples.jpg)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure Machine Learning Package per Visione artificiale vedere questi articoli:

+ Informazioni su come [migliorare l'accuratezza di questo modello](how-to-improve-accuracy-for-computer-vision-models.md).

+ Vedere la [panoramica dei pacchetti](https://aka.ms/aml-packages/vision).

+ Esplorare la [documentazione di riferimento](https://docs.microsoft.com/python/api/overview/azure-machine-learning/computer-vision) relativa a questo pacchetto.

+ Vedere le informazioni su [altri pacchetti Python per Azure Machine Learning](reference-python-package-overview.md).
