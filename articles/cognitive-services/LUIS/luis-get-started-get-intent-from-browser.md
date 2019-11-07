---
title: 'Guida introduttiva: Ottenere la finalità tramite browser - LUIS'
titleSuffix: Azure Cognitive Services
description: In questa guida introduttiva si usa un'app LUIS pubblica disponibile per stabilire l'intenzione di un utente partendo da un testo discorsivo in un browser.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 10/17/2019
ms.author: diberry
ms.openlocfilehash: a99dbc594b53d00ae02b2581d149fe7b4573ab7d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73495223"
---
# <a name="quickstart-get-intent-with-a-browser"></a>Guida introduttiva: Ottenere la finalità tramite un browser

Per comprendere ciò che restituisce un endpoint di stima LUIS, visualizzare un risultato di stima in un Web browser. 

## <a name="prerequisites"></a>Prerequisiti

Per eseguire query su un'app pubblica, sono necessari gli elementi seguenti:

* La chiave di Language Understanding (LUIS). Se non si ha già una sottoscrizione per creare una chiave, è possibile registrarsi per ottenere un [account gratuito](https://azure.microsoft.com/free/). Non sarà possibile usare la chiave di creazione di LUIS. 
* L'ID dell'app pubblica: `df67dcdb-c37d-46af-88e1-8b97951ca1c2`. 

## <a name="use-the-browser-to-see-predictions"></a>Usare il browser per visualizzare le previsioni

1. Aprire un Web browser. 
1. Usare gli URL completi seguenti, sostituendo `YOUR-KEY` con la propria chiave LUIS. Le richieste sono richieste GET e includono l'autorizzazione, con la chiave LUIS, come parametro della stringa di query.

    #### <a name="v3-prediction-requesttabv3-1-1"></a>[Richiesta di previsione V3](#tab/V3-1-1)
    
    
    Il formato dell'URL V3 per una richiesta dell'endpoint **GET** (per slot) è:
    
    `
    https://westus.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-KEY
    `

    #### <a name="v2-prediction-requesttabv2-1-2"></a>[Richiesta di previsione V2](#tab/V2-1-2)
    
    Il formato dell'URL V2 per una richiesta dell'endpoint **GET** è:
    
    `
    https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=YOUR-KEY&q=turn on all lights
    `

1. Incollare l'URL in una finestra del browser e premere INVIO. Il browser visualizzerà un risultato JSON che indica che LUIS rileva la finalità `HomeAutomation.TurnOn` come finalità principale e l'entità `HomeAutomation.Operation` con il valore `on`.

    #### <a name="v3-prediction-responsetabv3-2-1"></a>[Risposta di previsione V3](#tab/V3-2-1)

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

    #### <a name="v2-prediction-responsetabv2-2-2"></a>[Risposta di previsione V2](#tab/V2-2-2)

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

    #### <a name="v3-prediction-endpointtabv3-3-1"></a>[Endpoint di previsione V3](#tab/V3-3-1)

    Aggiungere `show-all-intents=true` alla fine della stringa di query per **visualizzare tutte le finalità**:

    `
    https://westus.api.cognitive.microsoft.com/luis/predict/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-KEY&show-all-intents=true
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

    #### <a name="v2-prediction-endpointtabv2"></a>[Endpoint di previsione V2](#tab/V2)

    Aggiungere `verbose=true` alla fine della stringa di query per **visualizzare tutte le finalità**:

    `
    https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?q=turn on all lights&subscription-key={your-key}&verbose=true
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


<!-- FIX - is the public app getting updated for the new prebuilt domain with entities? -->   

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull'[endpoint di previsione V3](luis-migration-api-v3.md).

> [!div class="nextstepaction"]
> [Creare un'app nel portale LUIS](get-started-portal-build-app.md)