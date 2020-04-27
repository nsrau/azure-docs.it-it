---
title: Ottenere la stima con la chiamata REST in C#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/20/2020
ms.author: diberry
ms.openlocfilehash: 20916ff80ae52ee9fc215d87c0987900d89e590a
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733300"
---
## <a name="prerequisites"></a>Prerequisiti

* [.NET Core V2.2+](https://dotnet.microsoft.com/download)
* [Visual Studio Code](https://code.visualstudio.com/)
* Un ID app LUIS: usare l'ID app IoT pubblica `df67dcdb-c37d-46af-88e1-8b97951ca1c2`. La query utente usata nel codice di avvio rapido è specifica di tale app.

## <a name="create-luis-runtime-key-for-predictions"></a>creare la chiave di runtime di LUIS per le previsioni

1. Accedere al [portale di Azure](https://portal.azure.com)
1. Fare clic su [Crea **Language Understanding**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. Immettere tutte le impostazioni necessarie per la chiave di runtime:

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

Usare C# (.NET Core) per eseguire query sull'[endpoint di previsione](https://aka.ms/luis-apim-v3-prediction) per ottenere un risultato della previsione.

1. Creare una nuova applicazione console destinata al linguaggio C#, con un nome di progetto e di cartella `predict-with-rest`.

    ```console
    dotnet new console -lang C# -n predict-with-rest
    ```

1. Passare alla directory `predict-with-rest` appena creata e installare le dipendenze necessarie con i comandi seguenti:

    ```console
    cd predict-with-rest
    dotnet add package System.Net.Http
    ```

1. Aprire `Program.cs` nell'IDE o nell'editor preferito. Sovrascrivere quindi `Program.cs` con il codice seguente:

   ```csharp
    using System;
    using System.Net.Http;
    using System.Web;

    namespace predict_with_rest
    {
        class Program
        {
            static void Main(string[] args)
            {
                // YOUR-KEY: 32 character key
                var key = "YOUR-KEY";

                // YOUR-ENDPOINT: example is your-resource-name.api.cognitive.microsoft.com
                var endpoint = "YOUR-ENDPOINT";

                // //public sample app
                var appId = "df67dcdb-c37d-46af-88e1-8b97951ca1c2";

                var utterance = "turn on all lights";

                MakeRequest(key, endpoint, appId, utterance);

                Console.WriteLine("Hit ENTER to exit...");
                Console.ReadLine();
            }
            static async void MakeRequest(string key, string endpoint, string appId, string utterance)
            {
                var client = new HttpClient();
                var queryString = HttpUtility.ParseQueryString(string.Empty);

                // The request header contains your subscription key
                client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

                // The "q" parameter contains the utterance to send to LUIS
                queryString["query"] = utterance;

                // These optional request parameters are set to their default values
                queryString["verbose"] = "true";
                queryString["show-all-intents"] = "true";
                queryString["staging"] = "false";
                queryString["timezoneOffset"] = "0";

                var endpointUri = String.Format("https://{0}/luis/prediction/v3.0/apps/{1}/slots/production/predict?query={2}", endpoint, appId, queryString);

                var response = await client.GetAsync(endpointUri);

                var strResponseContent = await response.Content.ReadAsStringAsync();

                // Display the JSON result from LUIS
                Console.WriteLine(strResponseContent.ToString());
            }
        }
    }

   ```

1. Sostituire i valori `YOUR-KEY` e `YOUR-ENDPOINT` con la chiave e l'endpoint di previsione.

    |Informazioni|Scopo|
    |--|--|
    |`YOUR-KEY`|La chiave di previsione di 32 caratteri.|
    |`YOUR-ENDPOINT`| L'endpoint dell'URL di previsione. Ad esempio: `replace-with-your-resource-name.api.cognitive.microsoft.com`.|

1. Compilare l'applicazione console con il comando seguente:

    ```console
    dotnet build
    ```

1. Eseguire l'applicazione console. L'output della console mostra lo stesso codice JSON visualizzato in precedenza nella finestra del browser.

    ```console
    dotnet run
    ```

1. Esaminare la risposta di previsione restituita in formato JSON:

    ```console
    Hit ENTER to exit...
    {'query': 'turn on all lights', 'prediction': {'topIntent': 'HomeAutomation.TurnOn', 'intents': {'HomeAutomation.TurnOn': {'score': 0.5375382}, 'None': {'score': 0.08687421}, 'HomeAutomation.TurnOff': {'score': 0.0207554}}, 'entities': {'HomeAutomation.Operation': ['on'], '$instance': {'HomeAutomation.Operation': [{'type': 'HomeAutomation.Operation', 'text': 'on', 'startIndex': 5, 'length': 2, 'score': 0.724984169, 'modelTypeId': -1, 'modelType': 'Unknown', 'recognitionSources': ['model']}]}}}}
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