---
title: "Guida introduttiva: Inviare una richiesta di ricerca all'API REST Ricerca entità Bing usando C#"
titlesuffix: Azure Cognitive Services
description: Usare questa guida introduttiva per inviare una richiesta all'API REST Ricerca entità Bing con C# e ricevere una risposta JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 03/12/2019
ms.author: aahi
ms.openlocfilehash: d31be245d906ba0405a44d4482272051982c943c
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/11/2019
ms.locfileid: "59488338"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-c"></a>Guida introduttiva: Inviare una richiesta di ricerca all'API REST Ricerca entità Bing usando C#

Usare questa guida introduttiva per eseguire la prima chiamata all'API Ricerca entità Bing e visualizzare la risposta JSON. Questa semplice applicazione C# invia una query di ricerca notizie all'API e visualizza la risposta. Il codice sorgente di questa applicazione è disponibile in [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingEntitySearchv7.cs).

L'applicazione è scritta in C#, ma l'API è un servizio Web RESTful compatibile con la maggior parte dei linguaggi di programmazione.


## <a name="prerequisites"></a>Prerequisiti

* Qualsiasi edizione di [Visual Studio 2017](https://www.visualstudio.com/downloads/).
* Il framework [Json.NET](https://www.newtonsoft.com/json), disponibile come pacchetto NuGet.
    * Per installare il pacchetto NuGet in Visual Studio:
        1. Fare clic con il pulsante destro in Esplora soluzioni
        2. Scegliere **Gestisci pacchetti NuGet...**
        3. Cercare **newtonsoft.json** e installare il pacchetto

* Se si usa Linux/MacOS, questa applicazione può essere eseguita tramite [Mono](https://www.mono-project.com/).


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Creare e inizializzare un progetto

1. creare una nuova soluzione di console C# in Visual Studio. Aggiungere quindi gli spazi dei nomi seguenti nel file di codice principale.
    
    ```csharp
    using Newtonsoft.Json;
    using System;
    using System.Net.Http;
    using System.Text;
    ```

2. Creare una nuova classe, quindi aggiungere le variabili per l'endpoint API, la chiave della sottoscrizione e la query della ricerca.

    ```csharp
    namespace EntitySearchSample
    {
        class Program
        {
            static string host = "https://api.cognitive.microsoft.com";
            static string path = "/bing/v7.0/entities";
    
            static string market = "en-US";
    
            // NOTE: Replace this example key with a valid subscription key.
            static string key = "ENTER YOUR KEY HERE";
    
            static string query = "italian restaurant near me";
        //...
        }
    }
    ```

## <a name="send-a-request-and-get-the-api-response"></a>Inviare una richiesta e ottenere la risposta dell'API

1. All'interno della classe creare una funzione denominata `Search()`. Creare un nuovo oggetto `HttpClient` e aggiungere la chiave della sottoscrizione all'intestazione `Ocp-Apim-Subscription-Key`.

   1. Creare l'URI per la richiesta combinando l'host e il percorso. Quindi aggiungere il proprio mercato e codificare la query con l'URL.
   2. Aspettare il risultato di `client.GetAsync()` per ottenere una risposta HTTP, quindi archiviare la risposta JSON aspettando `ReadAsStringAsync()`.
   3. Formattare la stringa JSON con `JsonConvert.DeserializeObject()` e stamparla nella console.

      ```csharp
      async static void Search()
      {
       //...
       HttpClient client = new HttpClient();
       client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

       string uri = host + path + "?mkt=" + market + "&q=" + System.Net.WebUtility.UrlEncode(query);

       HttpResponseMessage response = await client.GetAsync(uri);

       string contentString = await response.Content.ReadAsStringAsync();
       dynamic parsedJson = JsonConvert.DeserializeObject(contentString);
       Console.WriteLine(parsedJson);
      }
      ```

2. Nel metodo main dell'applicazione chiamare la funzione `Search()`.
    
    ```csharp
    static void Main(string[] args)
    {
        Search();
        Console.ReadLine();
    }
    ```


## <a name="example-json-response"></a>Risposta JSON di esempio

Viene restituita una risposta con esito positivo in formato JSON, come illustrato nell'esempio seguente: 

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "italian restaurant near me",
    "askUserForLocation": true
  },
  "places": {
    "value": [
      {
        "_type": "LocalBusiness",
        "webSearchUrl": "https://www.bing.com/search?q=sinful+bakery&filters=local...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "https://www.contoso.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98112",
          "addressCountry": "US",
          "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
      },

      . . .
      {
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Pickles+and+Preserves...",
        "name": "Munson's Pickles and Preserves Farm",
        "url": "https://www.princi.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness",
            "Restaurant"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98101",
          "addressCountry": "US",
          "neighborhood": "Capitol Hill"
        },
        "telephone": "(800) 555-1212"
      },
      
      . . .
    ]
  }
}
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare app Web a pagina singola](../tutorial-bing-entities-search-single-page-app.md)

* [Informazioni sull'API Ricerca entità Bing](../overview.md )
* [Informazioni di riferimento sull'API Ricerca entità Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)
