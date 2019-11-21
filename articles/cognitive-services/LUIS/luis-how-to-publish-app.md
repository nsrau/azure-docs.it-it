---
title: Publish app - LUIS
titleSuffix: Azure Cognitive Services
description: Dopo aver compilato ed eseguito il test dell'app LUIS attiva, renderla disponibile per l'applicazione client effettuandone la pubblicazione sull'endpoint.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: f213c1d43930075c78cf81de345f612e46bbfb1c
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74221711"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>Pubblicare l'app attiva di cui si è eseguito il training in un endpoint di staging o di produzione

When you finish building, training, and testing your active LUIS app, make it available to your client application by publishing it to the endpoint. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="publishing"></a>Pubblicazione

1. Per pubblicare l'endpoint, selezionare **Pubblica** nel pannello superiore a destra. 

    ![Publish button in top, right nav bar](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

1. Select your settings for the published prediction endpoint, then select **Publish**.

    ![Select publish settings then select Publish button](./media/luis-how-to-publish-app/publish-pop-up.png)

### <a name="publishing-slots"></a>Publishing slots

Select the correct slot when the pop-up window displays: 

* Staging
* Produzione 

By using both publishing slots, this allows you to have two different versions of your app available at the published endpoints or the same version on two different endpoints. 

### <a name="publishing-regions"></a>Regioni di pubblicazione

The app is published to all regions associated with the LUIS prediction endpoint resources added in the LUIS portal from the **Manage** ->  **[Azure Resources](/luis-how-to-azure-subscription.md#assign-a-resource-to-an-app)** page. 

For example, for an app created on [www.luis.ai](https://www.luis.ai), if you create a LUIS resource in two regions, **westus** and **eastus**, and add these to the app as resources, the app is published in both regions. Per altre informazioni sulle aree del servizio LUIS, vedere [Aree](luis-reference-regions.md).

> [!TIP]
> There are 3 authoring regions. You must author in the region you intend to publish to. If you need to publish to all regions, you need to manage your authoring process and the resulting trained model in all 3 authoring regions. 


## <a name="configuring-publish-settings"></a>Configurazione delle impostazioni di pubblicazione

After you select the slot, configure the publish settings for:

* Analisi dei sentimenti
* Spelling correction - v2 prediction endpoint only
* Priming del riconoscimento vocale 

After you publish, these settings are available for review from the **Manage** section's **Publish settings** page. You can change the settings with every publish. If you cancel a publish, any changes you made during the publish are also canceled. 

### <a name="when-your-app-is-published"></a>When your app is published

When your app is successfully published, a success notification appears at the top of the browser. The notification also includes a link to the endpoints. 

Se è necessario l'URL dell'endpoint, selezionare il collegamento. You can also get to the endpoint URLs by selecting **Manage** in the top menu, then select **Azure Resources** in the left menu. 

## <a name="sentiment-analysis"></a>Analisi dei sentimenti

<a name="enable-sentiment-analysis"></a>

L'analisi dei sentiment consente a LUIS di integrarsi con [Analisi del testo](https://azure.microsoft.com/services/cognitive-services/text-analytics/) per specificare la valutazione e la frase chiave. 

Non è necessario specificare una chiave di analisi del testo e non è previsto alcun addebito per questo servizio al proprio account Azure. 

I dati sentiment sono un punteggio compreso tra 1 e 0 che indica il sentiment positivo (più vicino a 1) o negativo (più vicino a 0) dei dati. L'etichetta sentiment `positive`, `neutral`, e `negative` è specifica per ogni lingua supportata. Attualmente solo la lingua inglese supporta le etichette sentiment. 

Per altre informazioni sulla risposta dell'endpoint JSON con l'analisi del sentiment, vedere [Analisi del sentiment](luis-concept-data-extraction.md#sentiment-analysis)

## <a name="spelling-correction"></a>Spelling correction

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]

Corrections to spelling are made before the LUIS user utterance prediction. You can see any changes to the original utterance, including spelling, in the response.

## <a name="speech-priming"></a>Priming del riconoscimento vocale

Speech priming is the process of using sending the LUIS model to Speech services prior to conversion of text to speech. This allows the speech service to provide speech conversion more accurately for your model. This allows bot Speech and LUIS requests and responses in one call by making one speech call and getting back a LUIS response. It provides less latency overall.

## <a name="next-steps"></a>Passaggi successivi

* Vedere [Gestire le chiavi](./luis-how-to-azure-subscription.md) per aggiungere chiavi alla chiave di sottoscrizione di Azure in LUIS e per impostare la chiave del controllo ortografico Bing e includere tutte le finalità nei risultati.
* Per istruzioni su come testare l'app pubblicata nella console di test, vedere [Eseguire il training e testare l'app](luis-interactive-test.md).

