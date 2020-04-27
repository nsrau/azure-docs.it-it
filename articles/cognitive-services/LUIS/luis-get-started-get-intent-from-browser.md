---
title: 'Guida introduttiva: Eseguire query per la stima con un browser - LUIS'
description: In questa guida introduttiva si usa un'app LUIS pubblica disponibile per stabilire l'intenzione di un utente partendo da un testo discorsivo in un browser.
ms.topic: quickstart
ms.date: 04/21/2020
ms.openlocfilehash: 5ba86882ebf3cb538ad6b865382342fcbd43d27c
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769976"
---
# <a name="quickstart-query-prediction-runtime-with-user-text"></a>Guida introduttiva: Eseguire query sul runtime di stima con un testo dell'utente

Per comprendere ciò che restituisce un endpoint di stima LUIS, visualizzare un risultato di stima in un Web browser.

## <a name="prerequisites"></a>Prerequisiti

Per eseguire query su un'app pubblica, sono necessari gli elementi seguenti:

* Informazioni sulla risorsa LUIS (Language Understanding):
    * **Chiave di stima**, che è possibile ottenere dal [portale LUIS](https://www.luis.ai/). Se non si ha già una sottoscrizione per creare una chiave, è possibile registrarsi per ottenere un [account gratuito](https://azure.microsoft.com/free/).
    * **Sottodominio dell'endpoint di stima**: il sottodominio è anche il **nome** della risorsa LUIS.
* Un ID app LUIS: usare l'ID app IoT pubblica `df67dcdb-c37d-46af-88e1-8b97951ca1c2`. La query utente usata nel codice di avvio rapido è specifica di tale app.

## <a name="use-the-browser-to-see-predictions"></a>Usare il browser per visualizzare le previsioni

1. Aprire un Web browser.
1. Usare gli URL completi seguenti, sostituendo `YOUR-KEY` con la propria chiave di stima LUIS. Le richieste sono richieste GET e includono l'autorizzazione, con la chiave di stima LUIS, come parametro della stringa di query.

    #### <a name="v3-prediction-request"></a>[Richiesta di previsione V3](#tab/V3-1-1)


    Il formato dell'URL V3 per una richiesta dell'endpoint **GET** (per slot) è:

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-LUIS-PREDICTION-KEY
    `

    #### <a name="v2-prediction-request"></a>[Richiesta di previsione V2](#tab/V2-1-2)

    Il formato dell'URL V2 per una richiesta dell'endpoint **GET** è:

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=YOUR-LUIS-PREDICTION-KEY&q=turn on all lights
    `

1. Incollare l'URL in una finestra del browser e premere INVIO. Il browser visualizzerà un risultato JSON che indica che LUIS rileva la finalità `HomeAutomation.TurnOn` come finalità principale e l'entità `HomeAutomation.Operation` con il valore `on`.

    #### <a name="v3-prediction-response"></a>[Risposta di previsione V3](#tab/V3-2-1)

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ]
            }
        }
    }
    ```

    #### <a name="v2-prediction-response"></a>[Risposta di previsione V2](#tab/V2-2-2)

    ```json
    {
        "query": "turn on all lights",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.5375382
        },
        "entities": [
            {
                "entity": "on",
                "type": "HomeAutomation.Operation",
                "startIndex": 5,
                "endIndex": 6,
                "score": 0.724984169
            }
        ]
    }
    ```

    * * *

1. Per visualizzare tutte le finalità, aggiungere il parametro della stringa di query appropriato.

    #### <a name="v3-prediction-endpoint"></a>[Endpoint di previsione V3](#tab/V3-3-1)

    Aggiungere `show-all-intents=true` alla fine della stringa di query per **visualizzare tutte le finalità**:

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/predict/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-LUIS-PREDICTION-KEY&show-all-intents=true
    `

    ```JSON
    {
        "query": "turn off the living room light",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                },
                "None": {
                    "score": 0.08687421
                },
                "HomeAutomation.TurnOff": {
                    "score": 0.0207554
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ]
            }
        }
    }
    ```

    #### <a name="v2-prediction-endpoint"></a>[Endpoint di previsione V2](#tab/V2)

    Aggiungere `verbose=true` alla fine della stringa di query per **visualizzare tutte le finalità**:

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?q=turn on all lights&subscription-key=YOUR-LUIS-PREDICTION-KEY&verbose=true
    `

    ```json
    {
        "query": "turn on all lights",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.5375382
        },
        "intents": [
            {
                "intent": "HomeAutomation.TurnOn",
                "score": 0.5375382
            },
            {
                "intent": "None",
                "score": 0.08687421
            },
            {
                "intent": "HomeAutomation.TurnOff",
                "score": 0.0207554
            }
        ],
        "entities": [
            {
                "entity": "on",
                "type": "HomeAutomation.Operation",
                "startIndex": 5,
                "endIndex": 6,
                "score": 0.724984169
            }
        ]
    }
    ```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su:
* [Endpoint di previsione V3](luis-migration-api-v3.md)
* [Sottodomini personalizzati](../cognitive-services-custom-subdomains.md)

> [!div class="nextstepaction"]
> [Creare un'app nel portale LUIS](get-started-portal-build-app.md)
