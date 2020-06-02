---
title: "Guida introduttiva: Inviare una richiesta di ricerca all'API REST con C# - Ricerca entità Bing"
titleSuffix: Azure Cognitive Services
description: Usare questa guida introduttiva per inviare una richiesta all'API REST Ricerca entità Bing con C# e ricevere una risposta JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.openlocfilehash: cd89f2ae13b10c83c3fc22023fc2e3cae1770c98
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650269"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-c"></a>Guida introduttiva: Inviare una richiesta di ricerca all'API REST Ricerca entità Bing usando C#

Usare questa guida introduttiva per eseguire la prima chiamata all'API Ricerca entità Bing e visualizzare la risposta JSON. Questa semplice applicazione C# invia una query di ricerca notizie all'API e visualizza la risposta. Il codice sorgente di questa applicazione è disponibile in [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingEntitySearchv7.cs).

Anche se l'applicazione è scritta in C#, l'API è un servizio Web RESTful compatibile con la maggior parte dei linguaggi di programmazione.


## <a name="prerequisites"></a>Prerequisiti

- Qualsiasi edizione di [Visual Studio 2017 o versioni successive](https://www.visualstudio.com/downloads/).

- Il framework [Json.NET](https://www.newtonsoft.com/json), disponibile come pacchetto NuGet. Per installare il pacchetto NuGet in Visual Studio:

   1. Fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni**.
   2. Scegliere **Gestisci pacchetti NuGet**.
   3. Cercare e selezionare *Newtonsoft.Json*, quindi installare il pacchetto.

- Se si usa Linux/MacOS, questa applicazione può essere eseguita tramite [Mono](https://www.mono-project.com/).


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Creare e inizializzare un progetto

1. Creare una nuova soluzione di console C# in Visual Studio. Aggiungere quindi gli spazi dei nomi seguenti nel file di codice principale:
    
    ```csharp
    using Newtonsoft.Json;
    using System;
    using System.Net.Http;
    using System.Text;
    ```

2. Creare una nuova classe e aggiungere le variabili per l'endpoint API, la chiave di sottoscrizione e la query della ricerca. È possibile usare l'endpoint globale nel codice seguente o l'endpoint del [sottodominio personalizzato](../../../cognitive-services/cognitive-services-custom-subdomains.md) visualizzato nel portale di Azure per la risorsa.

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

1. All'interno della classe creare una funzione denominata `Search()`. All'interno di questa funzione creare un nuovo oggetto `HttpClient` e aggiungere la chiave della sottoscrizione all'intestazione `Ocp-Apim-Subscription-Key`.

2. Creare l'URI per la richiesta combinando l'host e il percorso. Aggiungere quindi il proprio mercato e applicare la codifica URL alla query.

3. Aspettare il risultato di `client.GetAsync()` per ottenere una risposta HTTP, quindi archiviare la risposta JSON aspettando il risultato di `ReadAsStringAsync()`.

4. Formattare la stringa JSON con `JsonConvert.DeserializeObject()` e stamparla nella console.

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

5. Nel metodo `Main()` dell'applicazione chiamare la funzione `Search()`.
    
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
> [Creare un'app Web a pagina singola](../tutorial-bing-entities-search-single-page-app.md)

* [Informazioni sull'API Ricerca entità Bing](../overview.md )
* [Informazioni di riferimento sull'API Ricerca entità Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)
