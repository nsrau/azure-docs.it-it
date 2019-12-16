---
title: 'Esercitazione: Entità di espressione regolare - LUIS'
titleSuffix: Azure Cognitive Services
description: Estrarre dati formattati in modo coerente da un'espressione usando l'entità di espressione regolare.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: diberry
ms.openlocfilehash: 8da47899e2d3d2c1d04e9cf4768a968e7893ce96
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74852446"
---
# <a name="tutorial-get-well-formatted-data-from-the-utterance"></a>Esercitazione: Ottenere dati formattati in modo corretto dall'espressione
Questa esercitazione illustra come creare un'entità di espressione regolare per estrarre i dati formattati in modo coerente da un'espressione.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

**In questa esercitazione si imparerà come:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importare l'app
> * Aggiungere le finalità
> * Aggiungere un'entità di espressione regolare
> * Eseguire il training, la pubblicazione e le query sull'app per ottenere i dati estratti

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="regular-expression-entities"></a>Entità di espressione regolare

Usare l'entità di espressione regolare per estrarre il testo formattato correttamente da un'espressione. Mentre la finalità dell'espressione è sempre stabilita tramite Machine Learning, ciò non vale per questo tipo di entità specifico. L'entità di espressione regolare garantisce un funzionamento ottimale con qualsiasi testo che può essere rappresentato in modo coerente da un'[espressione regolare](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference).

`Send pizza delivery time to x123432`

Questo esempio usa un _codice breve_ per l'invio di messaggi SMS. Questo codice breve è un codice numerico a 5 o 6 cifre, preceduto da una x che può essere descritto con l'espressione regolare `x\d{5,6}`.

Quando si aggiunge un'entità di espressione regolare a un'app LUIS, non è necessario [assegnare un'etichetta](label-entity-example-utterance.md) al testo con l'entità di espressione regolare. Viene applicata a tutte le espressioni in tutte le finalità.

## <a name="import-example-json-to-begin-app"></a>Importare il file con estensione json per iniziare l'app

1.  Scaricare e salvare il [file JSON dell'app](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json).

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="create-intent-for-sending-confirmation-text-messages"></a>Creare la finalità per l'invio di messaggi SMS di conferma

1. Selezionare **+ Create** (+ Crea) per creare una nuova finalità per classificare la finalità di un'espressione per l'invio di un SMS di conferma.

1. Immettere `ConfirmationText` nella finestra di dialogo popup, quindi selezionare **Done** (Fine).

1. Aggiungere espressioni di esempio alla finalità.

    |Espressioni di esempio|
    |--|
    |Invia ora di consegna della pizza a x123432|
    |Invia SMS a x234567 con l'ora|
    |x23987 per l'avviso|

    Per estrarre le entità di Machine Learning, è necessario fornire esempi che includano l'entità in un'ampia gamma di espressioni ma con questa entità non di Machine Learning, la variazione non è importante. Se il testo corrisponde all'espressione regolare, verrà estratto.

## <a name="use-the-regular-expression-entity-for-well-formatted-data"></a>Utilizzare l'entità di espressione regolare per dati formattati in modo corretto
Creare un'entità di espressione regolare per la corrispondenza con il numero del testo. Questa espressione regolare trova la corrispondenza con il testo ma ignora le varianti di maiuscole/minuscole e di impostazioni cultura.

1. Selezionare **Entities** (Entità) nel pannello di sinistra.

1. Selezionare **+ Create** (+ Crea) nella pagina Entities list (Elenco entità).

1. Nella finestra di dialogo popup immettere il nome della nuova entità `ConfirmationTextRegEx`, selezionare **RegEx** come tipo di entità e quindi selezionare **Next** (Avanti).

    > [!div class="mx-imgBorder"]
    > ![Avvio della procedura di creazione dell'entità per l'entità di espressione regolare](./media/luis-quickstart-intents-regex-entity/pizza-create-new-entity.png)

1. In **Create a regex entity** (Crea un'entità di espressione regolare) immettere `x\d{5,6}` come valore per **Regex** e quindi selezionare **Create** (Create).

    > [!div class="mx-imgBorder"]
    > ![Immettere un'espressione regolare per estrarre i dati dall'espressione di esempio](./media/luis-quickstart-intents-regex-entity/pizza-set-regular-expression-for-new-entity.png)

1. Selezionare **Intents** (Finalità) dal menu a sinistra, quindi la finalità **ConfirmationText** per visualizzare l'espressione regolare etichettata nelle espressioni.

    > [!div class="mx-imgBorder"]
    > ![Visualizzare l'espressione regolare etichettata nelle espressioni di esempio](./media/luis-quickstart-intents-regex-entity/pizza-reg-ex-entity-shown-example-utterances-intent.png)

    Poiché l'entità non è un'entità di Machine Learning, l'entità viene applicata alle espressioni e visualizzata nel portale di LUIS non appena viene creata.

## <a name="train-the-app-before-testing-or-publishing"></a>Eseguire il training dell'app prima del test o della pubblicazione

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Pubblicare l'app per eseguire query dall'endpoint

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Ottenere la stima di finalità ed entità dall'endpoint

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

2. Andare alla fine dell'URL nell'indirizzo e immettere l'espressione seguente:

    `Text my pizza delivery to x23456 x234567 x12345`

    L'ultimo parametro querystring è `query`, la **query** dell'espressione.

    ```json
    {
        "query": "Text my pizza delivery to x23456 x234567 x12345",
        "prediction": {
            "topIntent": "ConfirmationText",
            "intents": {
                "ConfirmationText": {
                    "score": 0.7061845
                },
                "ModifyOrder": {
                    "score": 0.196021989
                },
                "None": {
                    "score": 0.02342912
                },
                "Test-Pizza": {
                    "score": 0.01213586
                },
                "CancelOrder": {
                    "score": 0.0063042324
                },
                "Confirmation": {
                    "score": 0.0058615827
                },
                "Greetings": {
                    "score": 0.00398947531
                }
            },
            "entities": {
                "ConfirmationTextRegEx": [
                    "x23456",
                    "x234567",
                    "x12345"
                ],
                "$instance": {
                    "ConfirmationTextRegEx": [
                        {
                            "type": "ConfirmationTextRegEx",
                            "text": "x23456",
                            "startIndex": 26,
                            "length": 6,
                            "modelTypeId": 8,
                            "modelType": "Regex Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "ConfirmationTextRegEx",
                            "text": "x234567",
                            "startIndex": 33,
                            "length": 7,
                            "modelTypeId": 8,
                            "modelType": "Regex Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "ConfirmationTextRegEx",
                            "text": "x12345",
                            "startIndex": 41,
                            "length": 6,
                            "modelTypeId": 8,
                            "modelType": "Regex Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

    Usando un'entità di espressione regolare, Language Understanding estrae dati denominati, risultato più utile a livello di codice per l'applicazione client che riceve la risposta JSON.


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Informazioni correlate

* [Concetto - entità](luis-concept-entity-types.md)
* [Informazioni di riferimento JSON sull'entità di espressione regolarei](reference-entity-regular-expression.md?tabs=V3)
* [Come aggiungere le entità per estrarre i dati](luis-how-to-add-entities.md)

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stata creata una nuova finalità, sono state aggiunte espressioni di esempio, quindi è stata creata un'entità Espressione regolare per estrarre dati formattati correttamente dalle espressioni. Dopo il training e la pubblicazione dell'app, una query per l'endpoint ha identificato la finalità e restituito i dati estratti.

> [!div class="nextstepaction"]
> [Informazioni sull'entità di elenco](tutorial-list-entity.md)

