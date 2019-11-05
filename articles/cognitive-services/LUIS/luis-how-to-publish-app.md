---
title: Pubblica app-LUIS
titleSuffix: Azure Cognitive Services
description: Dopo aver compilato ed eseguito il test dell'app LUIS attiva, renderla disponibile per l'applicazione client effettuandone la pubblicazione sull'endpoint.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 0d3a413249cb9058e4098f2836131494670a1727
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491334"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>Pubblicare l'app attiva di cui si è eseguito il training in un endpoint di staging o di produzione

Dopo aver compilato ed eseguito il test dell'app LUIS attiva, renderla disponibile per l'applicazione client effettuandone la pubblicazione sull'endpoint. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

<a name="publish-your-trained-app-to-an-http-endpoint"></a>

## <a name="publishing"></a>Pubblicazione

1. Per pubblicare l'endpoint, selezionare **Pubblica** nel pannello superiore a destra. 

    ![Pulsante pubblica nella parte superiore, barra nav destra](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

1. Selezionare le impostazioni per l'endpoint di stima pubblicato, quindi selezionare **pubblica**.

    ![Selezionare pubblica impostazioni e quindi fare clic sul pulsante pubblica](./media/luis-how-to-publish-app/publish-pop-up.png)

### <a name="publishing-slots"></a>Pubblicazione di slot

Quando viene visualizzata la finestra popup, selezionare lo slot corretto: 

* Gestione temporanea.
* Produzione. 

Con entrambi gli slot di pubblicazione è possibile disporre di due diverse versioni dell'app disponibili negli endpoint pubblicati o nella stessa versione in due endpoint diversi. 

### <a name="publishing-regions"></a>Regioni di pubblicazione

L'app viene pubblicata in tutte le aree associate alle risorse dell'endpoint di stima LUIS aggiunto nel portale LUIS. 

Per un'app creata in [www.Luis.ai](https://www.luis.ai), ad esempio, se si crea una risorsa Luis in due aree, **westus** e **eastus**e si aggiungono tali risorse all'app come risorse, l'app viene pubblicata in entrambe le aree. Per altre informazioni sulle aree del servizio LUIS, vedere [Aree](luis-reference-regions.md).


## <a name="configuring-publish-settings"></a>Configurazione delle impostazioni di pubblicazione

Dopo aver selezionato lo slot, configurare le impostazioni di pubblicazione per:

* Analisi del sentiment
* Correzione ortografica
* Priming del riconoscimento vocale 

Dopo la pubblicazione, queste impostazioni sono disponibili per la revisione nella pagina impostazioni di **pubblicazione** della sezione **Gestisci** . È possibile modificare le impostazioni con ogni pubblicazione. Se si annulla una pubblicazione, verranno annullate anche tutte le modifiche apportate durante la pubblicazione. 

### <a name="when-your-app-is-published"></a>Quando l'app viene pubblicata

Dopo aver pubblicato correttamente l'app, nella parte superiore del browser viene visualizzata una notifica di operazione riuscita verde. La barra di notifica verde include anche un collegamento agli endpoint. 

![Finestra popup di pubblicazione con collegamento all'endpoint](./media/luis-how-to-publish-app/publish-success.png)

Se è necessario l'URL dell'endpoint, selezionare il collegamento. Per ottenere gli URL degli endpoint, è anche possibile scegliere **Gestisci** dal menu in alto e quindi selezionare **risorse di Azure** nel menu a sinistra. 

## <a name="sentiment-analysis"></a>Analisi del sentiment

<a name="enable-sentiment-analysis"></a>

L'analisi dei sentiment consente a LUIS di integrarsi con [Analisi del testo](https://azure.microsoft.com/services/cognitive-services/text-analytics/) per specificare la valutazione e la frase chiave. 

Non è necessario specificare una chiave di analisi del testo e non è previsto alcun addebito per questo servizio al proprio account Azure. 

I dati sentiment sono un punteggio compreso tra 1 e 0 che indica il sentiment positivo (più vicino a 1) o negativo (più vicino a 0) dei dati. L'etichetta sentiment `positive`, `neutral`, e `negative` è specifica per ogni lingua supportata. Attualmente solo la lingua inglese supporta le etichette sentiment. 

Per altre informazioni sulla risposta dell'endpoint JSON con l'analisi del sentiment, vedere [Analisi del sentiment](luis-concept-data-extraction.md#sentiment-analysis)

## <a name="spelling-correction"></a>Correzione ortografica

Le correzioni all'ortografia vengono effettuate prima della stima dell'espressione utente LUIS. Nella risposta è possibile visualizzare tutte le modifiche apportate all'espressione originale, inclusa l'ortografia.

## <a name="speech-priming"></a>Priming del riconoscimento vocale

L'innesco vocale è il processo di utilizzo dell'invio del modello LUIS ai servizi vocali prima della conversione del testo in sintesi vocale. Ciò consente al servizio di riconoscimento vocale di fornire la conversione vocale in modo più accurato per il modello. In questo modo, le richieste e le risposte di bot e le richieste LUIS vengono effettuate in una sola chiamata eseguendo una chiamata vocale e ottenendo una risposta LUIS. Fornisce una minore latenza complessiva.

## <a name="next-steps"></a>Passaggi successivi

* Vedere [Gestire le chiavi](./luis-how-to-azure-subscription.md) per aggiungere chiavi alla chiave di sottoscrizione di Azure in LUIS e per impostare la chiave del controllo ortografico Bing e includere tutte le finalità nei risultati.
* Per istruzioni su come testare l'app pubblicata nella console di test, vedere [Eseguire il training e testare l'app](luis-interactive-test.md).

