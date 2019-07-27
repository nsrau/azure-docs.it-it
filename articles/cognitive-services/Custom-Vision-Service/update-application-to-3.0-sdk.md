---
title: Come eseguire la migrazione del progetto all'API 3,0
titleSuffix: Azure Cognitive Services
description: Informazioni su come eseguire la migrazione di progetti Visione personalizzata dalla versione precedente dell'API all'API 3,0.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: areddish
ms.openlocfilehash: 353fc0a2d8396def17b8e23d9a1c685c755349c5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560899"
---
# <a name="migrate-to-the-30-api"></a>Eseguire la migrazione all'API 3,0

Visione personalizzata ha ora raggiunto la disponibilità generale ed è stato sottoposto a un aggiornamento dell'API.
Questo aggiornamento include alcune nuove funzionalità e, in particolare, alcune modifiche di rilievo:

* L'API di stima ora è suddivisa in due in base al tipo di progetto.
* L'opzione di esportazione Vision AI Developer Kit (VAIDK) richiede la creazione di un progetto in un modo specifico.
* Le iterazioni predefinite sono state rimosse a favore di un'iterazione denominata Publish/UNPUBLISH.

Questa guida illustra come aggiornare i progetti per usare la nuova versione dell'API. Per un elenco completo delle modifiche, vedere le [Note sulla versione](release-notes.md) .

## <a name="use-the-updated-prediction-api"></a>Usare l'API di stima aggiornata

Le API 2. x usavano la stessa chiamata di stima per i classificatori di immagini e i progetti di rilevatori di oggetti. Entrambi i tipi di progetto sono accettabili per le chiamate **PredictImage** e **PredictImageUrl** . A partire da 3,0, è stata suddivisa questa API in modo che sia necessario associare il tipo di progetto alla chiamata:

* Usare **[ClassifyImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)** e **[ClassifyImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c14)** per ottenere stime per i progetti di classificazione delle immagini.
* Usare **[DetectImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c19)** e **[DetectImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c18)** per ottenere stime per i progetti di rilevamento oggetti.

## <a name="use-the-new-iteration-publishing-workflow"></a>Usa il nuovo flusso di lavoro di pubblicazione iterazione

Le API 2. x usavano l'iterazione predefinita o un ID iterazione specificato per scegliere l'iterazione da usare per la stima. A partire da 3,0, è stato adottato un flusso di pubblicazione per la prima volta che si pubblica un'iterazione con un nome specificato dall'API di training. Passare quindi il nome ai metodi di stima per specificare l'iterazione da usare.

> [!IMPORTANT]
> Le API 3,0 non usano la funzionalità di iterazione predefinita. Fino a quando non vengono deprecate le API precedenti, è possibile continuare a usare le API 2. x per impostare come predefinita un'iterazione. Queste API verranno mantenute per un periodo di tempo ed è possibile chiamare il metodo **[UpdateIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b818)** per contrassegnare un'iterazione come predefinita.

### <a name="publish-an-iteration"></a>Pubblicare un'iterazione

Una volta eseguito il training di un'iterazione, è possibile renderla disponibile per la stima usando il metodo **[PublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c82db28bf6a2b11a8247bbc)** . Per pubblicare un'iterazione, è necessario l'ID della risorsa di stima, disponibile nella pagina delle impostazioni del sito Web CustomVision.

![La pagina delle impostazioni del sito Web Visione personalizzata con l'ID della risorsa di stima descritto.](./media/update-application-to-3.0-sdk/prediction-id.png)

> [!TIP]
> È anche possibile ottenere queste informazioni dal [portale di Azure](https://portal.azure.com) passando alla risorsa di stima visione personalizzata e selezionando **Proprietà**.

Una volta pubblicata l'iterazione, le app possono usarla per la stima specificando il nome nella chiamata dell'API di stima. Per rendere un'iterazione non disponibile per le chiamate di stima, usare l'API **[UnpublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b81a)** .

## <a name="additional-export-options"></a>Opzioni di esportazione aggiuntive

Con le API 3,0 verranno esposte due destinazioni di esportazione aggiuntive: ARM Architecture e Vision AI Developer Kit.

* Per usare ARM, è sufficiente selezionare un dominio compatto, quindi scegliere DockerFile e quindi ARM come opzioni di esportazione.
* Per Vision AI Dev Kit, il progetto deve essere creato con il dominio __generale (Compact)__ , oltre a specificare VAIDK nell'argomento piattaforme di esportazione di destinazione.

## <a name="next-steps"></a>Passaggi successivi

* [Documentazione di riferimento sulle API di training (REST)](https://go.microsoft.com/fwlink/?linkid=865446)
* [Documentazione di riferimento dell'API di stima (REST)](https://go.microsoft.com/fwlink/?linkid=865445)