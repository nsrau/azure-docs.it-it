---
title: Ottenere la finalità con una chiamata REST in Node.js
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/18/2019
ms.author: diberry
ms.openlocfilehash: 5d8ed625e13d31e148ef1e54d8028fc7d13a6ede
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499643"
---
## <a name="prerequisites"></a>Prerequisiti

* Linguaggio di programmazione [Node.js](https://nodejs.org/) 
* [Visual Studio Code](https://code.visualstudio.com/)
* ID app pubblica: df67dcdb-c37d-46af-88e1-8b97951ca1c2

## <a name="get-luis-key"></a>Ottenere la chiave di LUIS

[!INCLUDE [Use authoring key for endpoint](../includes/get-key-quickstart.md)]

## <a name="get-intent-programmatically"></a>Ottenere la finalità a livello di codice

Usare Node.js per eseguire query sull'[API](https://aka.ms/luis-apim-v3-prediction) GET dell'endpoint di previsione per ottenere il risultato della previsione.

1. Copiare il frammento di codice seguente in un file denominato `predict.js`:

    ```javascript
    var request = require('request');
    var requestpromise = require('request-promise');
    var querystring = require('querystring');
    
    // Analyze text
    //
    getPrediction = async () => {
    
        // YOUR-KEY - Language Understanding starter key
        var endpointKey = "YOUR-KEY";
    
        // YOUR-ENDPOINT Language Understanding endpoint URL, an example is westus2.api.cognitive.microsoft.com
        var endpoint = "YOUR-ENDPOINT";
    
        // Set the LUIS_APP_ID environment variable 
        // to df67dcdb-c37d-46af-88e1-8b97951ca1c2, which is the ID
        // of a public sample application.    
        var appId = "df67dcdb-c37d-46af-88e1-8b97951ca1c2";
    
        var utterance = "turn on all lights";
    
        // Create query string 
        var queryParams = {
            "show-all-intents": true,
            "verbose":  true,
            "query": utterance,
            "subscription-key": endpointKey
        }
    
        // append query string to endpoint URL
        var URI = `https://${endpoint}/luis/prediction/v3.0/apps/${appId}/slots/production/predict?${querystring.stringify(queryParams)}`
    
        // HTTP Request
        const response = await requestpromise(URI);
    
        // HTTP Response
        console.log(response);
    
    }
    
    // Pass an utterance to the sample LUIS app
    getPrediction().then(()=>console.log("done")).catch((err)=>console.log(err));
    ```

1. Impostare i valori seguenti:

    * `YOUR-KEY` come chiave di avvio
    * `YOUR-ENDPOINT` come l'URL dell'endpoint

1. Installare le dipendenze eseguendo il comando seguente al prompt della riga di comando: 

    ```console
    npm install request request-promise querystring
    ```

1. Eseguire il codice con il comando seguente:

    ```console
    node predict.js
    ```

 1. Esaminare la risposta di previsione in formato JSON:   
    
    ```console
    {"query":"turn on all lights","prediction":{"topIntent":"HomeAutomation.TurnOn","intents":{"HomeAutomation.TurnOn":{"score":0.5375382},"None":{"score":0.08687421},"HomeAutomation.TurnOff":{"score":0.0207554}},"entities":{"HomeAutomation.Operation":["on"],"$instance":{"HomeAutomation.Operation":[{"type":"HomeAutomation.Operation","text":"on","startIndex":5,"length":2,"score":0.724984169,"modelTypeId":-1,"modelType":"Unknown","recognitionSources":["model"]}]}}}}
    ```

    Risposta JSON formattata per migliorare la leggibilità: 

    ```JSON
    {
        "query": "turn on all lights",
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
                ],
                "$instance": {
                    "HomeAutomation.Operation": [
                        {
                            "type": "HomeAutomation.Operation",
                            "text": "on",
                            "startIndex": 5,
                            "length": 2,
                            "score": 0.724984169,
                            "modelTypeId": -1,
                            "modelType": "Unknown",
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

## <a name="luis-keys"></a>Chiavi di LUIS

[!INCLUDE [Use authoring key for endpoint](../includes/starter-key-explanation.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine di questo argomento di avvio rapido, eliminare il file dal file system. 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiungere espressioni ed eseguire il training con](../luis-get-started-node-add-utterance.md)