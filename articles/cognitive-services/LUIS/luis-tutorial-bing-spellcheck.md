---
title: Correggere gli errori di ortografia
titleSuffix: Azure
description: È possibile correggere le parole in espressioni errate aggiungendo API Controllo ortografico Bing v7 alle query di endpoint LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/04/2019
ms.author: diberry
ms.openlocfilehash: 1e5536b08b3b78f35426207369f444e6eb21c87d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60495001"
---
# <a name="correct-misspelled-words-with-bing-spell-check"></a>Ortografia corretta con il Controllo ortografico Bing

È possibile integrare l'app LUIS con l’[API Controllo ortografico Bing v7](https://azure.microsoft.com/services/cognitive-services/spell-check/) per correggere l'ortografia nelle espressioni prima che LUIS preveda il punteggio e le entità dell’espressione. 

## <a name="create-first-key-for-bing-spell-check-v7"></a>Creare la prima chiave per Controllo ortografico Bing V7
La [prima chiave API Controllo ortografico Bing v7](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api) è gratuita. 

![Creare una chiave gratuita](./media/luis-tutorial-bing-spellcheck/free-key.png)

<a name"create-subscription-key"></a>
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

## <a name="using-the-key-in-luis-test-panel"></a>Utilizzare la chiave nel pannello di test LUIS
Esistono due posizioni in LUIS per usare la chiave. La prima è il [pannello test](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). La chiave non viene salvata in LUIS ma è invece una variabile di sessione. È necessario impostare la chiave ogni volta che si desidera che il pannello di test applichi il servizio di API Controllo ortografico Bing v7 all’espressione. Vedere [istruzioni](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) nel pannello di test per l'impostazione della chiave.

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
Se non si desidera utilizzare il servizio API Controllo ortografico Bing v7, è possibile etichettare le espressioni che presentano errori di ortografia in modo che LUIS possa imparare l'ortografia corretta e gli errori di battitura. Questa opzione richiede un maggiore sforzo di etichettatura rispetto all'uso di un correttore ortografico.

## <a name="publishing-page"></a>Pubblicazione di una pagina
La pagina di [pubblicazione](luis-how-to-publish-app.md) contiene una casella di controllo **Abilita correttore ortografico Bing**. Si tratta di una comodità per creare la chiave e capire come cambia l'URL dell'endpoint. È comunque necessario usare i parametri di endpoint corretti per avere il controllo ortografico corretto per ogni espressione. 

> [!div class="nextstepaction"]
> [Altre informazioni sulle espressioni di esempio](luis-how-to-add-example-utterances.md)
