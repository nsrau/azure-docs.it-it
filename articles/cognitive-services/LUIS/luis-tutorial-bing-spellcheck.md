---
title: Correggi parole errate-LUIS
titleSuffix: Azure Cognitive Services
description: È possibile correggere le parole in espressioni errate aggiungendo API Controllo ortografico Bing v7 alle query di endpoint LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 51b0d02443df872a7fae13116ea77b13d05055fa
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225457"
---
# <a name="correct-misspelled-words-with-bing-spell-check"></a>Ortografia corretta con il Controllo ortografico Bing

È possibile integrare l'app LUIS con l’[API Controllo ortografico Bing v7](https://azure.microsoft.com/services/cognitive-services/spell-check/) per correggere l'ortografia nelle espressioni prima che LUIS preveda il punteggio e le entità dell’espressione. 

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]


## <a name="create-first-key-for-bing-spell-check-v7"></a>Creare la prima chiave per Controllo ortografico Bing V7

La [prima chiave API Controllo ortografico Bing v7](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api) è gratuita. 

![Creare una chiave gratuita](./media/luis-tutorial-bing-spellcheck/free-key.png)

<a name="create-subscription-key"></a>

## <a name="create-endpoint-key"></a>Creare una chiave endpoint
Se la chiave gratuita è scaduta, creare una chiave endpoint.

1. Accedere al [Portale di Azure](https://portal.azure.com). 

2. Nell'angolo superiore sinistro selezionare **Crea una risorsa**.

3. Nella casella di ricerca immettere `Bing Spell Check API V7`.

    ![Ricerca dell'API Controllo ortografico Bing V7](./media/luis-tutorial-bing-spellcheck/portal-search.png)

4. Selezionare il servizio. 

5. A destra viene visualizzato un pannello informativo contenente informazioni, tra cui le note legali. Selezionare **Crea** per avviare il processo di creazione della sottoscrizione. 

6. Nella pannello successivo immettere le impostazioni del servizio. Attendere la fine del processo di creazione del servizio.

    ![Immissione delle impostazioni di servizio](./media/luis-tutorial-bing-spellcheck/subscription-settings.png)

7. Selezionare **Tutte le risorse** sotto il titolo **Preferiti** nel riquadro di sinistra.

8. Selezionare il nuovo servizio. Il tipo è **Servizi cognitivi** e la posizione è **globale**. 

9. Nel riquadro principale, selezionare **Chiavi** per visualizzare le nuove chiavi.

    ![Individuare le chiavi](./media/luis-tutorial-bing-spellcheck/grab-keys.png)

10. Copiare la prima chiave. È necessaria solo una delle due chiavi. 

<!--
## Using the key in LUIS test panel
There are two places in LUIS to use the key. The first is in the [test panel](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key isn't saved into LUIS but instead is a session variable. You need to set the key every time you want the test panel to apply the Bing Spell Check API v7 service to the utterance. See [instructions](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) in the test panel for setting the key.
-->
## <a name="adding-the-key-to-the-endpoint-url"></a>Aggiungere la chiave all'URL dell'endpoint
La query di endpoint richiede che la chiave sia passata nei parametri della stringa query per ogni query che si desidera applicare alla correzione ortografica. Si può avere un chatbot che chiama LUIS o si può chiamare direttamente l'API dell'endpoint LUIS. Indipendentemente dal modo in cui l'endpoint viene chiamato, ogni chiamata deve includere le informazioni necessarie affinché le correzioni ortografiche funzionino correttamente.

L'URL dell'endpoint ha diversi valori che devono essere passati correttamente. La chiave API Controllo ortografico Bing v7 è solo uno di questi. È necessario impostare il parametro **spellCheck** su true e il valore di **bing-hell-check-subscription-key** sul valore chiave:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appID}?subscription-key={luisKey}&spellCheck=**true**&bing-spell-check-subscription-key=**{bingKey}**&verbose=true&timezoneOffset=0&q={utterance}`

## <a name="send-misspelled-utterance-to-luis"></a>Inviare un’espressione con errori di ortografia a LUIS
1. In un browser Web, copiare la stringa precedente e sostituire `region`, `appId`, `luisKey` e `bingKey` con valori personalizzati. Assicurarsi di usare l'area di endpoint, se è diversa dall’[area](luis-reference-regions.md) di pubblicazione.

2. Aggiungere un espressione contenente errori di ortografia, ad esempio "How far is the mountainn?". In inglese, `mountain` con una `n` è la versione corretta. 

3. Premere INVIO per inviare la query a LUIS.

4. LUIS risponde con un risultato JSON per `How far is the mountain?`. Se l’API Controllo ortografico Bing v7 rileva un errore di ortografia, il campo `query` nella risposta JSON dell'applicazione LUIS contiene la query originale e il campo `alteredQuery` contiene la query corretta inviata a LUIS.

```json
{
  "query": "How far is the mountainn?",
  "alteredQuery": "How far is the mountain?",
  "topScoringIntent": {
    "intent": "Concierge",
    "score": 0.183866
  },
  "entities": []
}
```

## <a name="ignore-spelling-mistakes"></a>Ignorare errori di ortografia

Se non si vuole usare il servizio Controllo ortografico Bing API V7, è necessario aggiungere l'ortografia corretta e non corretta. 

Sono disponibili due soluzioni:

* Etichettare espressioni di esempio con tutte le ortografie diverse in modo che LUIS possa apprendere l'ortografia corretta e gli errori di digitazione. Questa opzione richiede un maggiore sforzo di etichettatura rispetto all'uso di un correttore ortografico.
* Creare un elenco di frasi con tutte le varianti della parola. Con questa soluzione non è necessario etichettare le varianti di parola nelle espressioni di esempio. 

## <a name="publishing-page"></a>Pubblicazione di una pagina
La pagina di [pubblicazione](luis-how-to-publish-app.md) contiene una casella di controllo **Abilita correttore ortografico Bing**. Si tratta di una comodità per creare la chiave e capire come cambia l'URL dell'endpoint. È comunque necessario usare i parametri di endpoint corretti per avere il controllo ortografico corretto per ogni espressione. 

> [!div class="nextstepaction"]
> [Altre informazioni sulle espressioni di esempio](luis-how-to-add-example-utterances.md)
