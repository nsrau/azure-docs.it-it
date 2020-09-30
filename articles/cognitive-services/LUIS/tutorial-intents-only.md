---
title: 'Esercitazione: Prevedere finalità - LUIS'
description: In questa esercitazione viene creata un'app personalizzata che consente di prevedere l'intenzione dell'utente in base all'espressione (testo).
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 05/05/2020
ms.openlocfilehash: c4a564834166686e8280f1117269742b489fc62c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91334753"
---
# <a name="tutorial-build-a-luis-app-to-determine-user-intentions"></a>Esercitazione: Creare un'app LUIS per determinare le intenzioni dell'utente

In questa esercitazione viene creata un'app personalizzata che consente di prevedere l'intenzione dell'utente in base all'espressione (testo).

**In questa esercitazione si imparerà come:**

> [!div class="checklist"]
> * Creare una nuova app
> * Creare finalità
> * Aggiungere espressioni di esempio
> * Eseguire il training dell'app
> * Pubblicare l'app
> * Ottenere una stima della finalità dall'endpoint

## <a name="user-intentions-as-intents"></a>Intenzioni dell'utente come finalità

Lo scopo dell'app è di determinare l'intenzione della conversazione di un testo discorsivo e con linguaggio naturale:

`I'd like to order a veggie pizza with a salad on the side.`

Queste intenzioni sono suddivise in **Finalità**.

|Finalità|Scopo|
|--|--|
|`ModifyOrder`|Determinare l'ordine della pizza dell'utente.|
|`Greeting`|Inizia la conversazione con il bot.|
|`ConfirmOrder`|Confermare l'ordine della pizza.|
|`None`|Determinare se l'utente sta chiedendo qualcosa per cui l'app LUIS non è progettata per rispondere. Questa finalità viene fornita come parte della creazione dell'app e non può essere eliminata. |

## <a name="create-a-new-app"></a>Creare una nuova app

[!INCLUDE [Follow these steps to create a new LUIS app](includes/create-pizza-app.md)]

## <a name="create-a-new-intent"></a>Creare una nuova finalità

La finalità viene usata per classificare le espressioni utente in base all'intenzione dell'utente, determinata dal testo in linguaggio naturale.

Per classificare un'espressione, sono necessari esempi di espressioni utente che dovranno essere classificate con questa finalità.

1. Nella sezione **Build** (Compila) della pagina **Intents** (Finalità) selezionare **+ Create** (Crea) per creare una nuova finalità. Immettere il nome della nuova finalità `OrderPizza` e quindi selezionare **Done** (Fine).

    La finalità `OrderPizza` viene prevista quando un utente vuole ordinare una pizza.

1. Aggiungere a questa finalità diverse espressioni di esempio che verranno presumibilmente usate dagli utenti:

    |Espressioni di esempio `OrderPizza`|
    |--|
    |`can i get a pepperoni pizza and a can of coke please`|
    |`can i get a small pizza with onions peppers and olives`|
    |`delivery for a small pepperoni pizza`|
    |`pickup a cheddar cheese pizza large with extra anchovies`|
    |`i need 2 large cheese pizzas 6 large pepperoni pizzas and 1 large supreme pizza`|
    |`Order a pizza for me`|

    > [!div class="mx-imgBorder"]
    > ![Screenshot dell'aggiunta di espressioni di esempio nella pagina Intents del portale LUIS](media/tutorial-intents-only/add-example-utterances-for-pizza-order.png)

    Fornendo _espressioni di esempio_, LUIS impara quali sono i tipi di espressione che dovranno essere previste per la finalità in questione. Questi sono esempi positivi. Le espressioni in tutte le altre finalità sono considerate esempi negativi per questa finalità.

    [!INCLUDE [Do not use too few utterances](includes/do-not-use-too-few-utterances.md)]

## <a name="create-remaining-intents"></a>Creare le finalità rimanenti

1. Creare la finalità `Greeting` e aggiungere le espressioni di esempio seguenti. Si tratta della finalità per determinare se un utente sta per iniziare una nuova conversazione per l'ordine della pizza.

    |Espressioni di esempio `Greeting`|
    |--|
    |`Hi`|
    |`Hello`|
    |`Hey`|
    |`Start`|
    |`Begin`|

1. Creare la finalità `Confirm` e aggiungere le espressioni di esempio seguenti. Si tratta della finalità per determinare se un utente ha completato l'ordine e accetta i dettagli dell'ordine.

    |Espressioni di esempio `Confirm`|
    |--|
    |`Go ahead`|
    |`ok`|
    |`Yes`|
    |`Sure`|


## <a name="none-intent-example-utterances"></a>Espressioni di esempio della finalità None (Nessuna)

[!INCLUDE [Follow these steps to add the None intent to the app](includes/add-example-utterances-none-intent.md)]

## <a name="train-the-app"></a>Eseguire il training dell'app

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app"></a>Pubblicare l'app

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-prediction"></a>Ottenere la stima della finalità

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Passare alla fine dell'URL nella barra degli indirizzi e immettere:

    `get a medium vegetarian pizza for delivery`

  
    L'ultimo parametro della stringa di query è `query`, la **query** dell'espressione viene passata nell'URI. Questa espressione non è uguale a nessuna delle espressioni di esempio. Si tratta di un test efficace per verificare se LUIS apprende e prevede la finalità `OrderPizza` come la finalità con il punteggio più elevato.

    ```JSON
    {
        "query": "get a medium vegetarian pizza for delivery",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.6488959
                },
                "None": {
                    "score": 0.139966831
                },
                "Confirm": {
                    "score": 0.00736504374
                },
                "Greeting": {
                    "score": 0.003970454
                }
            },
            "entities": {}
        }
    }
    ```

    La matrice delle entità è vuota perché questa app attualmente non dispone di entità (unità di dati all'interno dell'espressione da estrarre).

    Il risultato JSON identifica la finalità con il punteggio più alto come proprietà **`prediction.topIntent`** . Tutti i punteggi sono compresi tra 1 e 0, con il punteggio migliore prossimo a 1.

1. Modificare il parametro di **query** dell'URL in modo che abbia come destinazione la finalità **Greeting**:

    `Howdy`

    Poiché il testo non corrisponde esattamente a un'espressione di esempio, si tratta di un ottimo test per verificare se LUIS è in grado di apprendere ciò che deve essere stimato con questa finalità.

    ```json
    {
        "query": "howdy",
        "prediction": {
            "topIntent": "Greeting",
            "intents": {
                "Greeting": {
                    "score": 0.446016937
                },
                "Confirm": {
                    "score": 0.2390079
                },
                "None": {
                    "score": 0.09119555
                },
                "OrderPizza": {
                    "score": 0.00109590159
                }
            },
            "entities": {}
        }
    }
    ```

    Questa stima ha un punteggio di attendibilità del 44%. Per aumentare il punteggio di attendibilità, aggiungere da 15 a 30 espressioni di esempio.

## <a name="client-application-next-steps"></a>Passaggi successivi dell'applicazione client

In questa esercitazione è stata creata un'app LUIS, sono state create le finalità, sono state aggiunte espressioni di esempio a ognuna delle finalità e alla finalità None (Nessuna) e sono stati eseguiti il training, la pubblicazione e il test a livello dell'endpoint. Questi sono i passaggi di base per la creazione di un modello LUIS.

Dopo che LUIS restituisce la risposta JSON, LUIS termina la richiesta. LUIS non risponde alle espressioni dell'utente, si limita a identificare il tipo di informazione richiesta in linguaggio naturale. Il follow-up discorsivo viene fornito dall'applicazione client, ad esempio il servizio Azure Bot.


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Informazioni correlate

* [Tipi di entità](luis-concept-entity-types.md)
* [Come eseguire il training](luis-how-to-train.md)
* [Come eseguire la pubblicazione](luis-how-to-publish-app.md)
* [Come testare nel portale LUIS](luis-interactive-test.md)
* [Azure Bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiungere un'entità scomponibile a questa app](tutorial-machine-learned-entity.md)
