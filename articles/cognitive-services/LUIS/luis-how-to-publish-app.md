---
title: Pubblica app - LUIS
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
ms.openlocfilehash: c91a3ca73d70dd5fd2848bed0f43f14a817087d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80053430"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>Pubblicare l'app attiva di cui si è eseguito il training in un endpoint di staging o di produzione

Al termine della compilazione, del training e del test dell'app LUIS attiva, renderla disponibile per l'applicazione client pubblicandola nell'endpoint. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="publishing"></a>Pubblicazione

1. Per pubblicare l'endpoint, selezionare **Pubblica** nel pannello superiore a destra. 

    ![Pulsante Pubblica nella barra di spostamento in alto a destra](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

1. Selezionare le impostazioni per l'endpoint di stima pubblicato, quindi selezionare **Pubblica**.

    ![Selezionare le impostazioni di pubblicazione, quindi selezionare il pulsante Pubblica](./media/luis-how-to-publish-app/publish-pop-up.png)

### <a name="publishing-slots"></a>Slot di pubblicazione

Selezionare lo slot corretto quando viene visualizzata la finestra pop-up: 

* Staging
* Produzione 

Usando entrambi gli slot di pubblicazione, è possibile avere due versioni diverse dell'app disponibili negli endpoint pubblicati o la stessa versione in due endpoint diversi. 

### <a name="publishing-regions"></a>Regioni di pubblicazione

L'app viene pubblicata in tutte le aree associate alle risorse dell'endpoint di stima LUIS aggiunte nel portale LUIS dalla pagina Gestisci**[risorse di Azure.The](luis-how-to-azure-subscription.md#assign-a-resource-to-an-app)** app is published to all regions associated with the LUIS prediction endpoint resources added in the LUIS portal from the **Manage** -> Azure Resources page. 

Ad esempio, per un'app creata in [www.luis.ai](https://www.luis.ai), se crei una risorsa LUIS in due aree, **westus** e **eastus**, e le aggiungi all'app come risorse, l'app viene pubblicata in entrambe le aree. Per altre informazioni sulle aree del servizio LUIS, vedere [Aree](luis-reference-regions.md).

> [!TIP]
> Ci sono 3 regioni di authoring. È necessario creare nella regione in cui si intende eseguire la pubblicazione. Se è necessario pubblicare in tutte le aree geografiche, è necessario gestire il processo di creazione e il modello di training risultante in tutte e 3 le aree di creazione e modifica. 


## <a name="configuring-publish-settings"></a>Configurazione delle impostazioni di pubblicazione

Dopo aver selezionato lo slot, configurare le impostazioni di pubblicazione per:

* Analisi del sentiment
* Correzione ortografica - Solo endpoint di stima v2
* Priming del riconoscimento vocale 

Dopo la pubblicazione, queste impostazioni sono disponibili per la revisione nella pagina Impostazioni di **pubblicazione** della sezione **Gestisci.** È possibile modificare le impostazioni ad ogni pubblicazione. Se si annulla una pubblicazione, tutte le modifiche apportate durante la pubblicazione vengono annullate. 

### <a name="when-your-app-is-published"></a>Quando l'app viene pubblicata

Quando l'app viene pubblicata correttamente, viene visualizzata una notifica di esito positivo nella parte superiore del browser. La notifica include anche un collegamento agli endpoint. 

Se è necessario l'URL dell'endpoint, selezionare il collegamento. È anche possibile accedere agli URL degli endpoint selezionando **Gestisci** nel menu in alto, quindi selezionare **Risorse di Azure** nel menu a sinistra. 

## <a name="sentiment-analysis"></a>Analisi del sentiment

<a name="enable-sentiment-analysis"></a>

L'analisi dei sentiment consente a LUIS di integrarsi con [Analisi del testo](https://azure.microsoft.com/services/cognitive-services/text-analytics/) per specificare la valutazione e la frase chiave. 

Non è necessario specificare una chiave di analisi del testo e non è previsto alcun addebito per questo servizio al proprio account Azure. 

I dati sentiment sono un punteggio compreso tra 1 e 0 che indica il sentiment positivo (più vicino a 1) o negativo (più vicino a 0) dei dati. L'etichetta sentiment `positive`, `neutral`, e `negative` è specifica per ogni lingua supportata. Attualmente solo la lingua inglese supporta le etichette sentiment. 

Per altre informazioni sulla risposta dell'endpoint JSON con l'analisi del sentiment, vedere [Analisi del sentiment](luis-concept-data-extraction.md#sentiment-analysis)

## <a name="spelling-correction"></a>Correzione di errori di ortografia

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]

Le correzioni all'ortografia vengono apportate prima della stima dell'espressione utente LUIS. Nella risposta è possibile visualizzare le modifiche apportate all'espressione originale, inclusa l'ortografia.

## <a name="speech-priming"></a>Priming del riconoscimento vocale

Il priming vocale è il processo di utilizzo dell'invio del modello LUIS ai servizi di riconoscimento vocale prima della conversione di testo in voce. Ciò consente al servizio vocale di fornire la conversione vocale in modo più accurato per il modello. In questo modo il riconoscimento vocale e LUIS richieste e risposte in una chiamata effettuando una chiamata vocale e ottenendo una risposta LUIS. Fornisce una latenza minore in generale.

## <a name="next-steps"></a>Passaggi successivi

* Vedere [Gestire le chiavi](./luis-how-to-azure-subscription.md) per aggiungere chiavi alla chiave di sottoscrizione di Azure in LUIS e per impostare la chiave del controllo ortografico Bing e includere tutte le finalità nei risultati.
* Per istruzioni su come testare l'app pubblicata nella console di test, vedere [Eseguire il training e testare l'app](luis-interactive-test.md).

