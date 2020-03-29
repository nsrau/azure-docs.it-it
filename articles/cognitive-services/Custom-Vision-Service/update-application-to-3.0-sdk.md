---
title: Come aggiornare il progetto all'API 3.0
titleSuffix: Azure Cognitive Services
description: Informazioni su come aggiornare i progetti di visione personalizzata dalla versione precedente dell'API all'API 3.0.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: areddish
ms.openlocfilehash: c134f30b124113a23df0e73cd1bbc8209e335183
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647508"
---
# <a name="update-to-the-30-api"></a>Aggiornamento all'API 3.0

Custom Vision ha ora raggiunto la disponibilità generale e ha subito un aggiornamento API.
Questo aggiornamento include alcune nuove funzionalità e, soprattutto, alcune modifiche di rilievo:

* L'API di stima è ora suddivisa in due in base al tipo di progetto.
* L'opzione di esportazione Vision AI Developer Kit (VAIDK) richiede la creazione di un progetto in un modo specifico.
* Le iterazioni predefinite sono state rimosse a favore di una pubblicazione/annullamento della pubblicazione di un'iterazione denominata.

Questa guida ti mostrerà come aggiornare i progetti per lavorare con la nuova versione dell'API. Vedere le [note sulla versione](release-notes.md) per un elenco completo delle modifiche.

## <a name="use-the-updated-prediction-api"></a>Usare l'API di stima aggiornataUse the updated Prediction API

Le API 2.x hanno usato la stessa chiamata di stima sia per i classificatori di immagini che per i progetti di rilevamento di oggetti. Entrambi i tipi di progetto erano accettabili per le chiamate **PredictImage** e **PredictImageUrl.** A partire dalla 3.0, questa API è stata suddivisa in modo che sia necessario associare il tipo di progetto alla chiamata:Starting with 3.0, we have split this API so that you need to match the project type to the call:

* Usare **[ClassifyImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)** e **[ClassifyImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c14)** per ottenere stime per i progetti di classificazione delle immagini.
* Usare **[DetectImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c19)** e **[DetectImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c18)** per ottenere stime per i progetti di rilevamento di oggetti.

## <a name="use-the-new-iteration-publishing-workflow"></a>Utilizzare il nuovo flusso di lavoro di pubblicazione delle iterazioni

Le API 2.x hanno usato l'iterazione predefinita o un ID di iterazione specificato per scegliere l'iterazione da utilizzare per la stima. A partire dalla versione 3.0, è stato adottato un flusso di pubblicazione in base al quale si pubblica per la prima volta un'iterazione con un nome specificato dall'API di training. Si passa quindi il nome ai metodi di stima per specificare l'iterazione da utilizzare.

> [!IMPORTANT]
> Le API 3.0 non utilizzano la funzionalità di iterazione predefinita. Fino a quando non deprechiamo le API meno recenti, è possibile continuare a usare le API 2.x per attivare o disattivare un'iterazione come impostazione predefinita. Queste API verranno mantenute per un periodo di tempo ed è possibile chiamare il **[UpdateIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b818)** metodo per contrassegnare un'iterazione come predefinita.

### <a name="publish-an-iteration"></a>Pubblicare un'iterazione

Dopo aver eseguito il training di un'iterazione, è possibile renderla disponibile per la stima usando il metodo **[PublishIteration.Once](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c82db28bf6a2b11a8247bbc)** an iteration is trained, you can make it available for prediction using the PublishIteration method. Per pubblicare un'iterazione, è necessario l'ID risorsa di stima, disponibile nella pagina delle impostazioni del sito Web CustomVision.

![Pagina Impostazioni sito Web Visione personalizzata con l'ID risorsa di stima descritto.](./media/update-application-to-3.0-sdk/prediction-id.png)

> [!TIP]
> È anche possibile ottenere queste informazioni dal portale di [Azure](https://portal.azure.com) accedendo alla risorsa Custom Vision Prediction e selezionando **Proprietà**.

Dopo la pubblicazione dell'iterazione, le app possono usarla per la stima specificando il nome nella chiamata all'API di stima. Per rendere un'iterazione non disponibile per le chiamate di stima, usare l'API **[UnpublishIteration.To](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b81a)** make an iteration unavailable for prediction calls, use the UnpublishIteration API.

## <a name="next-steps"></a>Passaggi successivi

* [Documentazione di riferimento dell'API di training (REST)Training API reference documentation (REST)](https://go.microsoft.com/fwlink/?linkid=865446)
* [Documentazione di riferimento dell'API di stima (REST)Prediction API reference documentation (REST)](https://go.microsoft.com/fwlink/?linkid=865445)