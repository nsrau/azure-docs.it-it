---
title: Ottenere la finalità con una chiamata REST in Node.js
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/20/2020
ms.author: diberry
ms.openlocfilehash: 3bef3cbb321465893b3a05242f0d72d77d091d6b
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733276"
---
## <a name="prerequisites"></a>Prerequisiti

* Linguaggio di programmazione [Node.js](https://nodejs.org/)
* [Visual Studio Code](https://code.visualstudio.com/)
* Un ID app LUIS: usare l'ID app IoT pubblica `df67dcdb-c37d-46af-88e1-8b97951ca1c2`. La query utente usata nel codice di avvio rapido è specifica di tale app.

## <a name="create-luis-runtime-key-for-predictions"></a>creare la chiave di runtime di LUIS per le previsioni

1. Accedere al [portale di Azure](https://portal.azure.com)
1. Fare clic su [Crea **Language Understanding**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. Immettere tutte le impostazioni necessarie per la chiave di **runtime**:

    |Impostazione|valore|
    |--|--|
    |Nome|Il nome desiderato (2-64 caratteri)|
    |Subscription|Selezionare la sottoscrizione appropriata|
    |Location|Selezionare una località vicina e disponibile|
    |Piano tariffario|`F0`: il piano tariffario minimo|
    |Gruppo di risorse|Selezionare un gruppo di risorse disponibile|

1. Fare clic su **Crea** e attendere che venga creata la risorsa. Una volta creata, passare alla relativa pagina.
1. Raccogliere i valori di `endpoint` e `key` configurati.

## <a name="get-intent-programmatically"></a>Ottenere la finalità a livello di codice

Usare Node.js per eseguire query sull'[endpoint di previsione](https://aka.ms/luis-apim-v3-prediction) per ottenere un risultato della previsione.

1. Copiare il frammento di codice seguente in un file denominato `predict.js`:

    ```javascript
    var request = require('request');
    var requestpromise = require('request-promise');
    var querystring = require('querystring');

    // Analyze text
    //
    getPrediction = async () => {

        // YOUR-KEY - Language Understanding runtime key
        var endpointKey = "YOUR-KEY";

        // YOUR-ENDPOINT Language Understanding endpoint URL, an example is your-resource-name.api.cognitive.microsoft.com
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

1. Sostituire i valori `YOUR-KEY` e `YOUR-ENDPOINT` con la chiave di **runtime** e l'endpoint di previsione.

    |Informazioni|Scopo|
    |--|--|
    |`YOUR-KEY`|La chiave di **runtime** di previsione di 32 caratteri.|
    |`YOUR-ENDPOINT`| L'endpoint dell'URL di previsione. Ad esempio: `replace-with-your-resource-name.api.cognitive.microsoft.com`.|

1. Installare le dipendenze `request`, `request-promise` e `querystring` con questo comando:

    ```console
    npm install request request-promise querystring
    ```

1. Eseguire l'app con questo comando:

    ```console
    node predict.js
    ```

 1. Esaminare la risposta di previsione restituita in formato JSON:

    ```console
    {"query":"turn on all lights","prediction":{"topIntent":"HomeAutomation.TurnOn","intents":{"HomeAutomation.TurnOn":{"score":0.5375382},"None":{"score":0.08687421},"HomeAutomation.TurnOff":{"score":0.0207554}},"entities":{"HomeAutomation.Operation":["on"],"$instance":{"HomeAutomation.Operation":[{"type":"HomeAutomation.Operation","text":"on","startIndex":5,"length":2,"score":0.724984169,"modelTypeId":-1,"modelType":"Unknown","recognitionSources":["model"]}]}}}}
    ```

    La risposta JSON formattata per migliorare la leggibilità:

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

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine di questo argomento di avvio rapido, eliminare il file dal file system.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiungere espressioni ed eseguire il training](../get-started-get-model-rest-apis.md)