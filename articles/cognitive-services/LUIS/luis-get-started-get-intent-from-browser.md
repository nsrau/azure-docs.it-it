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
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 6282e768ebc51d0d4ec2b15f057727f207a7d81a
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703602"
---
# <a name="quickstart-get-intent-with-a-browser"></a>Guida introduttiva: Ottenere la finalità tramite un browser

Per comprendere ciò che restituisce un endpoint di previsione LUIS, visualizzare un risultato di previsione in un Web browser. 

## <a name="prerequisites"></a>Prerequisiti

Per eseguire query su un'app pubblica, sono necessari gli elementi seguenti:

* La chiave di Language Understanding (LUIS). Se non si ha già una sottoscrizione per creare una chiave, è possibile registrarsi per ottenere un [account gratuito](https://azure.microsoft.com/free/).
* L'ID dell'app pubblica: `df67dcdb-c37d-46af-88e1-8b97951ca1c2`. 

## <a name="use-the-browser-to-see-predictions"></a>Usare il browser per visualizzare le previsioni

1. Aprire un Web browser. 
1. Usare gli URL completi seguenti, sostituendo `{your-key}` con la propria chiave LUIS. Le richieste sono richieste GET e includono l'autorizzazione, con la chiave LUIS, come parametro della stringa di query.

    #### <a name="v2-prediction-endpoint-requesttabv2"></a>[Richiesta dell'endpoint di previsione V2](#tab/V2)
    
    Il formato dell'URL V2 per una richiesta dell'endpoint **GET** è:
    
    `
    https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key={your-key}&q=turn on all lights
    `
    
    #### <a name="v3-prediction-endpoint-requesttabv3"></a>[Richiesta dell'endpoint di previsione V3](#tab/V3)
    
    
    Il formato dell'URL V3 per una richiesta dell'endpoint **GET** (per slot) è:
    
    `
    https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key={your-key}
    `
    
    * * *

1. Incollare l'URL in una finestra del browser e premere INVIO. Il browser visualizzerà un risultato JSON che indica che LUIS rileva la finalità `HomeAutomation.TurnOn` come finalità principale e l'entità `HomeAutomation.Operation` con il valore `on`.

    #### <a name="v2-prediction-endpoint-responsetabv2"></a>[Risposta dell'endpoint di previsione V2](#tab/V2)

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

    #### <a name="v3-prediction-endpoint-responsetabv3"></a>[Risposta dell'endpoint di previsione V3](#tab/V3)

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "normalizedQuery": "turn on all lights",
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

    * * *

1. Per visualizzare tutte le finalità, aggiungere il parametro della stringa di query appropriato. 

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

    #### <a name="v3-prediction-endpointtabv3"></a>[Endpoint di previsione V3](#tab/V3)

    Aggiungere `show-all-intents=true` alla fine della stringa di query per **visualizzare tutte le finalità**:

    `
    https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key={your-key}&show-all-intents=true
    `

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "normalizedQuery": "turn on all lights",
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                },
                "HomeAutomation.TurnOff": {
                     "score": 0.0207554
                },
                "None": {
                     "score": 0.08687421
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

    * * * 

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull'[endpoint di previsione V3](luis-migration-api-v3.md).

> [!div class="nextstepaction"]
> [Creare un'app nel portale LUIS](get-started-portal-build-app.md)