---
title: 'Avvio rapido: Eseguire una ricerca di notizie con C#: API REST Ricerca notizie Bing'
titlesuffix: Azure Cognitive Services
description: Usare questa guida di avvio rapido per inviare una richiesta all'API REST Ricerca notizie Bing usando C# e ricevere una risposta JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 1/10/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 74ac8602e61404214f300682f5696100b8fa0c60
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55868855"
---
# <a name="quickstart-search-for-news-using-c-and-the-bing-news-search-rest-api"></a>Guida introduttiva: Cercare le notizie usando C# e l'API REST Ricerca notizie Bing

Usare questa guida introduttiva per eseguire la prima chiamata all'API Ricerca notizie Bing e visualizzare la risposta JSON. Questa semplice applicazione C# invia una query di ricerca notizie all'API e visualizza la risposta. Il codice completo per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingNewsSearchv7.cs).

L'applicazione è scritta in C#, ma l'API è un servizio Web RESTful compatibile con la maggior parte dei linguaggi di programmazione.

## <a name="prerequisites"></a>Prerequisiti

* Qualsiasi edizione di [Visual Studio 2017](https://www.visualstudio.com/downloads/).
* Il framework [Json.NET](https://www.newtonsoft.com/json), disponibile come pacchetto NuGet.
* Se si usa Linux/MacOS, questa applicazione può essere eseguita tramite [Mono](http://www.mono-project.com/).

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

Vedere anche [Prezzi di Servizi cognitivi - API di ricerca Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="create-and-initialize-a-project"></a>Creare e inizializzare un progetto

1. creare una nuova soluzione di console C# in Visual Studio. Aggiungere quindi gli spazi dei nomi seguenti nel file di codice principale.
    
    ```csharp
    using System;
    using System.Text;
    using System.Net;
    using System.IO;
    using System.Collections.Generic;
    ```

2. Creare variabili per l'endpoint dell'API, la chiave di sottoscrizione e il termine di ricerca.

    ```csharp
    const string accessKey = "enter key here";
    const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/news/search";
    const string searchTerm = "Microsoft";
    ```
## <a name="create-a-struct-to-format-the-bing-news-search-response"></a>Creare uno struct per formattare la risposta di Ricerca notizie Bing

1. Definire uno struct `SearchResult` in cui saranno contenuti i risultati della ricerca di immagini e le informazioni dell'intestazione JSON.

    ```csharp
    struct SearchResult
    {
        public String jsonResult;
        public Dictionary<String, String> relevantHeaders;
    }
    ```

## <a name="create-and-handle-a-news-search-request"></a>Creare e gestire una nuova richiesta di ricerca notizie

Creare un metodo denominato `BingNewsSearch` per eseguire la chiamata all'API e impostare il tipo restituito nello struct `SearchResult` creato in precedenza. Nel metodo,eseguire i passaggi seguenti:

1. Costruire l'URI per la richiesta di ricerca. Il termine di ricerca `toSearch` deve essere formattato prima di poter essere aggiunto alla stringa.

    ```csharp
    static SearchResult BingNewsSearch(string toSearch){

        var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(toSearch);
    //...
    ```

2. Eseguire la richiesta Web e ottenere la risposta come stringa JSON.

    ```csharp
    WebRequest request = WebRequest.Create(uriQuery);
    request.Headers["Ocp-Apim-Subscription-Key"] = subscriptionKey;
    HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
    string json = new StreamReader(response.GetResponseStream()).ReadToEnd();
    ```

3. Creare l'oggetto risultato di ricerca ed estrarre le intestazioni HTTP di Bing. Restituire quindi `searchResult`.

    ```csharp
    // Create the result object for return
    var searchResult = new SearchResult()
    {
        jsonResult = json,
        relevantHeaders = new Dictionary<String, String>()
    };

    // Extract Bing HTTP headers
    foreach (String header in response.Headers)
    {
        if (header.StartsWith("BingAPIs-") || header.StartsWith("X-MSEdge-"))
            searchResult.relevantHeaders[header] = response.Headers[header];
    }
    return searchResult;
    ```

3. Creare l'oggetto risultato di ricerca ed estrarre le intestazioni HTTP di Bing. Restituire quindi `searchResult`.

    ```csharp
    // Create the result object for return
    var searchResult = new SearchResult()
    {
        jsonResult = json,
        relevantHeaders = new Dictionary<String, String>()
    };

    // Extract Bing HTTP headers
    foreach (String header in response.Headers)
    {
        if (header.StartsWith("BingAPIs-") || header.StartsWith("X-MSEdge-"))
            searchResult.relevantHeaders[header] = response.Headers[header];
    }
    return searchResult;
    ```

## <a name="process-the-response"></a>Elaborare la risposta

1. Nel metodo principale chiamare `BingNewsSearch()` e memorizzare la risposta restituita. Deserializzare quindi la risposta JSON in un oggetto. È quindi possibile visualizzare i valori della risposta.

    ```csharp
    SearchResult result = BingNewsSearch(searchTerm);
    //deserialize the JSON response
    dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result.jsonResult);
    Console.WriteLine(jsonObj["value"][0])
    ```

## <a name="json-response"></a>Risposta JSON

Viene restituita una risposta con esito positivo in formato JSON, come illustrato nell'esempio seguente:

```json
{
   "_type": "News",
   "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/news\/search?q=Microsoft",
   "totalEstimatedMatches": 36,
   "sort": [
      {
         "name": "Best match",
         "id": "relevance",
         "isSelected": true,
         "url": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/news\/search?q=Microsoft"
      },
      {
         "name": "Most recent",
         "id": "date",
         "isSelected": false,
         "url": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/news\/search?q=Microsoft&sortby=date"
      }
   ],
   "value": [
      {
         "name": "Microsoft to open flagship London brick-and-mortar retail store",
         "url": "http:\/\/www.contoso.com\/article\/microsoft-to-open-flagshi...",
         "image": {
            "thumbnail": {
               "contentUrl": "https:\/\/www.bing.com\/th?id=ON.F9E4A49EC010417...",
               "width": 220,
               "height": 146
            }
         },
         "description": "After years of rumors about Microsoft opening a brick-and-mortar...", 
         "about": [
           {
             "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entiti...", 
             "name": "Microsoft"
           }, 
           {
             "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entit...", 
             "name": "London"
           }
         ], 
         "provider": [
           {
             "_type": "Organization", 
             "name": "Contoso"
           }
         ], 
          "datePublished": "2017-09-21T21:16:00.0000000Z", 
          "category": "ScienceAndTechnology"
      }, 

      . . .

      {
         "name": "Microsoft adds Availability Zones to its Azure cloud platform",
         "url": "https:\/\/contoso.com\/2017\/09\/21\/microsoft-adds-availability...",
         "image": {
            "thumbnail": {
               "contentUrl": "https:\/\/www.bing.com\/th?id=ON.0AE7595B9720...",
               "width": 700,
               "height": 466
            }
         },
         "description": "Microsoft has begun adding Availability Zones to its...",
         "about": [
            {
               "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entities\/a093e9b...",
               "name": "Microsoft"
            },
            {
               "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entities\/cf3abf7d-e379-...",
               "name": "Windows Azure"
            },
            {
               "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entities\/9cdd061c-1fae-d0...",
               "name": "Cloud"
            }
         ],
         "provider": [
            {
               "_type": "Organization",
               "name": "Contoso"
            }
         ],
         "datePublished": "2017-09-21T09:01:00.0000000Z",
         "category": "ScienceAndTechnology"
      }
   ]
}
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare app Web a pagina singola](tutorial-bing-news-search-single-page-app.md)
