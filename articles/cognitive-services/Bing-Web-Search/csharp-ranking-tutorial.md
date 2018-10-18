---
title: Uso della classificazione per visualizzare i risultati di ricerca
titleSuffix: Azure Cognitive Services
description: Viene illustrato come usare la RankingResponse di Bing per visualizzare i risultati della ricerca in ordine di classificazione.
services: cognitive-services
author: bradumbaugh
manager: cgronlun
ms.assetid: 2575A80C-FC74-4631-AE5D-8101CF2591D3
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: conceptual
ms.date: 05/08/2017
ms.author: brumbaug
ms.openlocfilehash: 3e55830fcfdbea91581a75fcfc343fd522485c5a
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2018
ms.locfileid: "46123403"
---
# <a name="build-a-console-app-search-client-in-c"></a>Compilare un client di ricerca app console in C#

Questa esercitazione mostra come compilare una semplice app della console .NET Core che consenta agli utenti di eseguire query sull'API Ricerca Web Bing e di visualizzare i risultati classificati.

Questa esercitazione illustra come:

- Eseguire una semplice query per l'API Ricerca Web Bing
- Visualizzare i risultati della query in un ordine classificato

## <a name="prerequisites"></a>Prerequisiti

Per proseguire con l'esercitazione è necessario:

- Visual Studio. Se non è disponibile, [scaricare e installare la versione gratuita di Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/).
- Una chiave di sottoscrizione per l'API Ricerca Web Bing. Se non si ha una sottoscrizione, [iscriversi per una versione di prova gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).

## <a name="create-a-new-console-app-project"></a>Creare un nuovo progetto di app console

In Visual Studio creare un progetto con `Ctrl`+`Shift`+`N`.

Nella finestra di dialogo **Nuovo progetto** selezionare **Visual C# > Desktop classico di Windows > App console (.NET Framework)**.

Assegnare all'applicazione il nome **MyConsoleSearchApp** e fare clic su **OK**.

## <a name="add-the-jsonnet-nuget-package-to-the-project"></a>Aggiungere il pacchetto NuGet di JSON.net al progetto

JSON.net consente di lavorare con le risposte JSON restituite dall'API. Aggiungere il pacchetto NuGet al progetto:

- In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e scegliere **Gestisci pacchetti NuGet**.
- Nella scheda **Sfoglia**, cercare `Newtonsoft.Json`. Selezionare la versione più recente e fare clic su **Installa**.
- Fare clic su **OK** nella finestra **Rivedi modifiche**.
- Chiudere la scheda di Visual Studio intitolata **NuGet: MyConsoleSearchApp**.

## <a name="add-a-reference-to-systemweb"></a>Aggiungere un riferimento a System.Web

Questa esercitazione si basa sull'assembly `System.Web`. Aggiungere un riferimento a questo assembly per il progetto:

- In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Riferimenti** e scegliere **Aggiungi riferimento**
- Selezionare **Assembly > Framework**, quindi scorrere verso il basso e controllare **System.Web**
- Selezionare **OK**.

## <a name="add-some-necessary-using-statements"></a>Aggiungere alcune istruzioni per l'uso necessarie

Il codice in questa esercitazione richiede altre tre istruzioni per l'uso. Aggiungere queste istruzioni sotto le istruzioni `using` esistenti nella parte superiore di **Program.cs**:

```csharp
using System.Web;
using System.Net.Http;
```

## <a name="ask-the-user-for-a-query"></a>Chiedere una query all'utente

In **Esplora soluzioni** aprire **Program.cs**. Aggiornare il metodo `Main()`:

```csharp
static void Main()
{
    // Get the user's query
    Console.Write("Enter Bing query: ");
    string userQuery = Console.ReadLine();
    Console.WriteLine();

    // Run the query and display the results
    RunQueryAndDisplayResults(userQuery);

    // Prevent the console window from closing immediately
    Console.WriteLine("\nHit ENTER to exit...");
    Console.ReadLine();
}
```

Questo metodo:

- Chiede una query all'utente
- Chiama `RunQueryAndDisplayResults(userQuery)` per eseguire la query e visualizzare i risultati
- Attende l'input dell'utente per evitare che la finestra della console si chiuda immediatamente.

## <a name="search-for-query-results-using-the-bing-web-search-api"></a>Cercare i risultati della query usando l'API Ricerca Web Bing

Successivamente, aggiungere un metodo che esegua una query per l'API e visualizzi i risultati:

```csharp
static void RunQueryAndDisplayResults(string userQuery)
{
    try
    {
        // Create a query
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "<YOUR_SUBSCRIPTION_KEY_GOES_HERE>");
        var queryString = HttpUtility.ParseQueryString(string.Empty);
        queryString["q"] = userQuery;
        var query = "https://api.cognitive.microsoft.com/bing/v7.0/search?" + queryString;

        // Run the query
        HttpResponseMessage httpResponseMessage = client.GetAsync(query).Result;

        // Deserialize the response content
        var responseContentString = httpResponseMessage.Content.ReadAsStringAsync().Result;
        Newtonsoft.Json.Linq.JObject responseObjects = Newtonsoft.Json.Linq.JObject.Parse(responseContentString);

        // Handle success and error codes
        if (httpResponseMessage.IsSuccessStatusCode)
        {
            DisplayAllRankedResults(responseObjects);
        }
        else
        {
            Console.WriteLine($"HTTP error status code: {httpResponseMessage.StatusCode.ToString()}");
        }
    }
    catch (Exception e)
    {
        Console.WriteLine(e.Message);
    }
}
```

Questo metodo:

- Crea un `HttpClient` per eseguire la query per l'API Ricerca Web
- Imposta l'intestazione HTTP `Ocp-Apim-Subscription-Key`, che Bing usa per autenticare la richiesta
- Esegue la richiesta e usa JSON.net per deserializzare i risultati
- Chiama `DisplayAllRankedResults(responseObjects)` per visualizzare tutti i risultati in un ordine classificato

Assicurarsi di impostare il valore di `Ocp-Apim-Subscription-Key` sulla chiave di sottoscrizione.

## <a name="display-ranked-results"></a>Visualizzare i risultati classificati

Prima di capire come visualizzare i risultati in ordine classificato, esaminare un esempio di risposta della ricerca Web:

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346...",
        "totalEstimatedMatches" : 982000,
        "value" : [{
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
            "name" : "Contoso Sailing Club - Seattle",
            "url" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FE...",
            "displayUrl" : "https:\/\/contososailingsea...",
            "snippet" : "Come sail with Contoso in Seattle...",
            "dateLastCrawled" : "2017-04-07T02:25:00"
        },
        {
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/7\/#WebPages.6",
            "name" : "Contoso Sailing Lessons - Official Site",
            "url" : "http:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FE...",
            "displayUrl" : "https:\/\/www.constososailinglessonsseat...",
            "snippet" : "Contoso sailing lessons in Seattle...",
            "dateLastCrawled" : "2017-04-09T14:30:00"
        },

        ...

        ],
        "someResultsRemoved" : true
    },
    "relatedSearches" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/7\/#RelatedSearches",
        "value" : [{
            "text" : "sailing lessons",
            "displayText" : "sailing lessons",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346E..."
        }

        ...

        ]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "WebPages",
                "resultIndex" : 0,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 1,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.1"
                }
            }

            ...

            ]
        },
        "sidebar" : {
            "items" : [{
                "answerType" : "RelatedSearches",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches"
                }
            }]
        }
    }
}
```

L'oggetto JSON `rankingResponse` ([documentazione](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse)) descrive l'ordine di visualizzazione appropriato per i risultati di ricerca. Include uno o più dei gruppi seguenti, in ordine di priorità:

- `pole`: i risultati della ricerca da rendere più visibili (visualizzati ad esempio sopra la riga principale e la barra laterale).
- `mainline`: i risultati di ricerca da visualizzare nella riga principale.
- `sidebar`: i risultati di ricerca da visualizzare nella barra laterale. Se non ci sono barre laterali, visualizzare i risultati sotto la linea principale.

La risposta classificata JSON può includere uno o più gruppi.

In **Program.cs** aggiungere il metodo seguente per visualizzare i risultati nell'ordine classificato correttamente:

```csharp
static void DisplayAllRankedResults(Newtonsoft.Json.Linq.JObject responseObjects)
{
    string[] rankingGroups = new string[] { "pole", "mainline", "sidebar" };

    // Loop through the ranking groups in priority order
    foreach (string rankingName in rankingGroups)
    {
        Newtonsoft.Json.Linq.JToken rankingResponseItems = responseObjects.SelectToken($"rankingResponse.{rankingName}.items");
        if (rankingResponseItems != null)
        {
            foreach (Newtonsoft.Json.Linq.JObject rankingResponseItem in rankingResponseItems)
            {
                Newtonsoft.Json.Linq.JToken resultIndex;
                rankingResponseItem.TryGetValue("resultIndex", out resultIndex);
                var answerType = rankingResponseItem.Value<string>("answerType");
                switch (answerType)
                {
                    case "WebPages":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("webPages.value"), "WebPage", "name", "url", "displayUrl", "snippet");
                        break;
                    case "News":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("news.value"), "News", "name", "url", "description");
                        break;
                    case "Images":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("images.value"), "Image", "thumbnailUrl");
                        break;
                    case "Videos":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("videos.value"), "Video", "embedHtml");
                        break;
                    case "RelatedSearches":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("relatedSearches.value"), "RelatedSearch", "displayText", "webSearchUrl");
                        break;
                }
            }
        }
    }
}
```

Questo metodo:

- Si sovrappone ai gruppi `rankingResponse` contenuti nella risposta
- Visualizza gli elementi in ciascun gruppo chiamando `DisplaySpecificResults(...)`

In **Program.cs** aggiungere i due metodi seguenti:

```csharp
static void DisplaySpecificResults(Newtonsoft.Json.Linq.JToken resultIndex, Newtonsoft.Json.Linq.JToken items, string title, params string[] fields)
{
    if (resultIndex == null)
    {
        foreach (Newtonsoft.Json.Linq.JToken item in items)
        {
            DisplayItem(item, title, fields);
        }
    }
    else
    {
        DisplayItem(items.ElementAt((int)resultIndex), title, fields);
    }
}

static void DisplayItem(Newtonsoft.Json.Linq.JToken item, string title, string[] fields)
{
    Console.WriteLine($"{title}: ");
    foreach( string field in fields )
    {
        Console.WriteLine($"- {field}: {item[field]}");
    }
    Console.WriteLine();
}
```

Questi metodi operano insieme per restituire i risultati di ricerca nella console.

## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'applicazione. L'output dovrebbe essere simile al seguente:

```
Enter Bing query: sailing lessons seattle

WebPage:
- name: Contoso Sailing Club - Seattle
- url: https://www.bing.com/cr?IG=70BE289346ED4594874FE...
- displayUrl: https://contososailingsea....
- snippet: Come sail with Contoso in Seattle...

WebPage:
- name: Contoso Sailing Lessons Seattle - Official Site
- url: http://www.bing.com/cr?IG=70BE289346ED4594874FE...
- displayUrl: https://www.constososailinglessonsseat...
- snippet: Contoso sailing lessons in Seattle...

...
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull'[uso della classificazione per visualizzare i risultati](rank-results.md).
