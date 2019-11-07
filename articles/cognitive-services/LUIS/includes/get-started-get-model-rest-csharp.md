---
title: Ottenere il modello con una chiamata REST in C#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/18/2019
ms.author: diberry
ms.openlocfilehash: 503482243f5aa2e7f833257a3a6eb91a3b5c5ec1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505836"
---
## <a name="prerequisites"></a>Prerequisiti

* Chiave di avvio.
* Importare l'app [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) dal repository GitHub cognitive-services-language-understanding.
* L'ID applicazione di LUIS per l'app TravelAgent importata. L'ID applicazione viene visualizzato nel dashboard delle applicazioni.
* L'ID della versione all'interno dell'applicazione che riceve le espressioni. L'ID predefinito è "0.1".
* [.NET Core V2.2+](https://dotnet.microsoft.com/download)
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>File JSON delle espressioni di esempio

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="get-luis-key"></a>Ottenere la chiave di LUIS

[!INCLUDE [Use authoring key for endpoint](../includes/get-key-quickstart.md)]

## <a name="change-model-programmatically"></a>Cambiare il modello a livello di codice

Usare C# per aggiungere all'applicazione un'[API](https://aka.ms/luis-apim-v3-authoring) entità basata su Machine Learning. 

1. Creare una nuova applicazione console destinata al linguaggio C#, con un nome di progetto e di cartella `model-with-rest`. 

    ```console
    dotnet new console -lang C# -n model-with-rest
    ```

1. Installare le dipendenze necessarie con i comandi dell'interfaccia della riga di comando di .NET.

    ```console
    dotnet add package System.Net.Http
    dotnet add package JsonFormatterPlus
    ```
1. Sovrascrivere Program.cs con il codice seguente:

    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    using System.Linq;
    
    // 3rd party NuGet packages
    using JsonFormatterPlus;
    
    namespace AddUtterances
    {
        class Program
        {
            // NOTE: use your starter key value
            static string authoringKey = "YOUR-KEY";
    
            // NOTE: Replace this endpoint with your starter key endpoint
            // for example, westus.api.cognitive.microsoft.com
            static string endpoint = "YOUR-ENDPOINT";
    
            // NOTE: Replace this with the ID of your LUIS application
            static string appID = "YOUR-APP-ID";
    
            // NOTE: Replace this your version number
            static string appVersion = "0.1";
    
            static string host = String.Format("https://{0}/luis/authoring/v3.0-preview/apps/{1}/versions/{2}/", endpoint, appID, appVersion);
    
            // GET request with authentication
            async static Task<HttpResponseMessage> SendGet(string uri)
            {
                using (var client = new HttpClient())
                using (var request = new HttpRequestMessage())
                {
                    request.Method = HttpMethod.Get;
                    request.RequestUri = new Uri(uri);
                    request.Headers.Add("Ocp-Apim-Subscription-Key", authoringKey);
                    return await client.SendAsync(request);
                }
            }
            // POST request with authentication
            async static Task<HttpResponseMessage> SendPost(string uri, string requestBody)
            {
                using (var client = new HttpClient())
                using (var request = new HttpRequestMessage())
                {
                    request.Method = HttpMethod.Post;
                    request.RequestUri = new Uri(uri);
    
                    if (!String.IsNullOrEmpty(requestBody))
                    {
                        request.Content = new StringContent(requestBody, Encoding.UTF8, "text/json");
                    }
    
                    request.Headers.Add("Ocp-Apim-Subscription-Key", authoringKey);
                    return await client.SendAsync(request);
                }
            }        
            // Add utterances as string with POST request
            async static Task AddUtterances(string utterances)
            {
                string uri = host + "examples";
    
                var response = await SendPost(uri, utterances);
                var result = await response.Content.ReadAsStringAsync();
                Console.WriteLine("Added utterances.");
                Console.WriteLine(JsonFormatter.Format(result));
            }
            // Train app after adding utterances
            async static Task Train()
            {
                string uri = host  + "train";
    
                var response = await SendPost(uri, null);
                var result = await response.Content.ReadAsStringAsync();
                Console.WriteLine("Sent training request.");
                Console.WriteLine(JsonFormatter.Format(result));
            }    
            // Check status of training
            async static Task Status()
            {
                var response = await SendGet(host  + "train");
                var result = await response.Content.ReadAsStringAsync();
                Console.WriteLine("Requested training status.");
                Console.WriteLine(JsonFormatter.Format(result));
            }    
            // Add utterances, train, check status
            static void Main(string[] args)
            {
                string utterances = @"
                [
                    {
                    'text': 'go to Seattle today',
                    'intentName': 'BookFlight',
                    'entityLabels': [
                        {
                        'entityName': 'Location::LocationTo',
                        'startCharIndex': 6,
                        'endCharIndex': 12
                        }
                    ]
                    },
                    {
                        'text': 'a barking dog is annoying',
                        'intentName': 'None',
                        'entityLabels': []
                    }
                ]
                ";            
                AddUtterances(utterances).Wait();
                Train().Wait();
                Status().Wait();
            }
        }
    }
    ```

1. Sostituire i valori seguenti:

    * `YOUR-KEY` con la chiave di avvio
    * `YOUR-ENDPOINT` con l'endpoint, ad esempio `westus2.api.cognitive.microsoft.com`
    * `YOUR-APP-ID` con l'ID dell'app

1. Compilare l'applicazione console. 

    ```console
    dotnet build
    ```

1. Eseguire l'applicazione console. L'output della console mostra lo stesso codice JSON visualizzato in precedenza nella finestra del browser.

    ```console
    dotnet run
    ```

## <a name="luis-keys"></a>Chiavi di LUIS

[!INCLUDE [Use authoring key for endpoint](../includes/starter-key-explanation.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine di questo argomento di avvio rapido, eliminare il file dal file system. 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Procedure consigliate per un'app](../luis-concept-best-practices.md)