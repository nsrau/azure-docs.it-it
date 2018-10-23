---
title: "Guida introduttiva: Usare l'SDK di Ricerca Web Bing per C#"
titleSuffix: Azure Cognitive Services
description: L'SDK di Ricerca Web Bing facilita l'integrazione di Ricerca Web Bing nell'applicazione C#. In questa guida introduttiva si apprenderà come creare un'istanza di un client, inviare una richiesta e stampare la risposta.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: quickstart
ms.date: 08/16/2018
ms.author: erhopf
ms.openlocfilehash: 04a70c2afbd499d3965e23487cd49540b38e08af
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2018
ms.locfileid: "49318657"
---
# <a name="quickstart-use-the-bing-web-search-sdk-for-c"></a>Guida introduttiva: Usare l'SDK di Ricerca Web Bing per C#

L'SDK di Ricerca Web Bing facilita l'integrazione di Ricerca Web Bing nell'applicazione C#. In questa guida introduttiva si apprenderà come creare un'istanza di un client, inviare una richiesta e stampare la risposta.

[!INCLUDE [bing-web-search-quickstart-signup](../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="prerequisites"></a>Prerequisiti

Prima di eseguire i passaggi illustrati in questa guida introduttiva, verificare di avere quanto segue:

* [Visual Studio](https://visualstudio.microsoft.com/downloads/) o
* [Visual Studio Code 2017](https://code.visualstudio.com/download)
  * [C# per Visual Studio Code](https://visualstudio.microsoft.com/downloads/)
  * [Gestione pacchetti NuGet](https://github.com/jmrog/vscode-nuget-package-manager)
* [.NET Core SDK](https://www.microsoft.com/net/download)

## <a name="create-a-project-and-install-dependencies"></a>Creare un progetto e installare le dipendenze

> [!TIP]
> Ottenere la versione più recente del codice come soluzione di Visual Studio da [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/).

Il primo passaggio consiste nel creare un nuovo progetto console. Per informazioni sulla configurazione di un progetto console, vedere [Hello World - Il primo programma (Guida per programmatori C#)](https://docs.microsoft.com/dotnet/csharp/programming-guide/inside-a-program/hello-world-your-first-program). Per usare l'SDK di Ricerca Web Bing nell'applicazione, è necessario installare `Microsoft.Azure.CognitiveServices.Search.WebSearch` tramite Gestione pacchetti NuGet.

Con il [pacchetto SDK di Ricerca Web](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.WebSearch/1.2.0) vengono installati anche i componenti seguenti:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="declare-dependencies"></a>Dichiarare le dipendenze

Aprire il progetto in Visual Studio o Visual Studio Code e importare le dipendenze seguenti:

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.CognitiveServices.Search.WebSearch;
using Microsoft.Azure.CognitiveServices.Search.WebSearch.Models;
using System.Linq;
```

## <a name="create-project-scaffolding"></a>Creare lo scaffolding di progetto

Quando si è creato il nuovo progetto console, devono essere stati creati anche uno spazio dei nomi e una classe per l'applicazione. Il programma dovrebbe risultare simile al seguente:

```csharp
namespace WebSearchSDK
{
    class YOUR_PROGRAM
    {

        // The code in the following sections goes here.

    }
}
```

Nelle sezioni seguenti si compilerà l'applicazione di esempio all'interno di questa classe.

## <a name="construct-a-request"></a>Creare una richiesta

Il codice seguente crea la query di ricerca.

```csharp
public static void WebResults(WebSearchAPI client)
{
    try
    {
        var webData = client.Web.Search(query: "Yosemite National Park");
        Console.WriteLine("Searching for \"Yosemite National Park\"");

        // Code for handling responses is provided in the next section...

    }
    catch (Exception ex)
    {
        Console.WriteLine("Encountered exception. " + ex.Message);
    }
}
```

## <a name="handle-the-response"></a>Gestire la risposta

A questo punto è possibile aggiungere codice per analizzare la risposta e stampare i risultati. `name` e `url` della prima pagina Web, della prima immagine, del primo articolo e del primo video vengono stampati se sono presenti nell'oggetto risposta.

```csharp
if (webData?.WebPages?.Value?.Count > 0)
{
    // find the first web page
    var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

    if (firstWebPagesResult != null)
    {
        Console.WriteLine("Webpage Results # {0}", webData.WebPages.Value.Count);
        Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
        Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
    }
    else
    {
        Console.WriteLine("Didn't find any web pages...");
    }
}
else
{
    Console.WriteLine("Didn't find any web pages...");
}

/*
 * Images
 * If the search response contains images, the first result's name
 * and url are printed.
 */
if (webData?.Images?.Value?.Count > 0)
{
    // find the first image result
    var firstImageResult = webData.Images.Value.FirstOrDefault();

    if (firstImageResult != null)
    {
        Console.WriteLine("Image Results # {0}", webData.Images.Value.Count);
        Console.WriteLine("First Image result name: {0} ", firstImageResult.Name);
        Console.WriteLine("First Image result URL: {0} ", firstImageResult.ContentUrl);
    }
    else
    {
        Console.WriteLine("Didn't find any images...");
    }
}
else
{
    Console.WriteLine("Didn't find any images...");
}

/*
 * News
 * If the search response contains news articles, the first result's name
 * and url are printed.
 */
if (webData?.News?.Value?.Count > 0)
{
    // find the first news result
    var firstNewsResult = webData.News.Value.FirstOrDefault();

    if (firstNewsResult != null)
    {
        Console.WriteLine("\r\nNews Results # {0}", webData.News.Value.Count);
        Console.WriteLine("First news result name: {0} ", firstNewsResult.Name);
        Console.WriteLine("First news result URL: {0} ", firstNewsResult.Url);
    }
    else
    {
        Console.WriteLine("Didn't find any news articles...");
    }
}
else
{
    Console.WriteLine("Didn't find any news articles...");
}

/*
 * Videos
 * If the search response contains videos, the first result's name
 * and url are printed.
 */
if (webData?.Videos?.Value?.Count > 0)
{
    // find the first video result
    var firstVideoResult = webData.Videos.Value.FirstOrDefault();

    if (firstVideoResult != null)
    {
        Console.WriteLine("\r\nVideo Results # {0}", webData.Videos.Value.Count);
        Console.WriteLine("First Video result name: {0} ", firstVideoResult.Name);
        Console.WriteLine("First Video result URL: {0} ", firstVideoResult.ContentUrl);
    }
    else
    {
        Console.WriteLine("Didn't find any videos...");
    }
}
else
{
    Console.WriteLine("Didn't find any videos...");
}
```

## <a name="declare-the-main-method"></a>Dichiarare il metodo main

In questa applicazione il metodo main include codice che crea un'istanza del client, convalida `subscriptionKey` e chiama `WebResults`. Immettere una chiave di sottoscrizione valida per l'account di Azure prima di continuare.

```csharp
static void Main(string[] args)
{
    var client = new WebSearchAPI(new ApiKeyServiceClientCredentials("YOUR_SUBSCRIPTION_KEY"));

    WebResults(client);

    Console.WriteLine("Press any key to exit...");
    Console.ReadKey();
}
```

## <a name="run-the-application"></a>Eseguire l'applicazione

Ora eseguire l'applicazione.

```console
dotnet run
```

## <a name="define-functions-and-filter-results"></a>Definire le funzioni e filtrare i risultati

Dopo aver eseguito la prima chiamata all'API Ricerca Web Bing, esaminare ora alcune funzioni dell'SDK che consentono di affinare le query e filtrare i risultati. Ogni funzione può essere aggiunta all'applicazione C# creata nella sezione precedente.

### <a name="limit-the-number-of-results-returned-by-bing"></a>Limitare il numero di risultati restituiti da Bing

In questo esempio vengono usati i parametri `count` e `offset` per limitare il numero di risultati restituiti per "Best restaurants in Seattle". Vengono stampati `name` e `URL` per il primo risultato.

1. Aggiungere il codice seguente al progetto console:
    ```csharp
    public static void WebResultsWithCountAndOffset(WebSearchAPI client)
    {
        try
        {
            var webData = client.Web.SearchAsync(query: "Best restaurants in Seattle", offset: 10, count: 20).Result;
            Console.WriteLine("\r\nSearching for \" Best restaurants in Seattle \"");

            if (webData?.WebPages?.Value?.Count > 0)
            {
                var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

                if (firstWebPagesResult != null)
                {
                    Console.WriteLine("Web Results #{0}", webData.WebPages.Value.Count);
                    Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
                    Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
                }
                else
                {
                    Console.WriteLine("Couldn't find first web result!");
                }
            }
            else
            {
                Console.WriteLine("Didn't see any Web data..");
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine("Encountered exception. " + ex.Message);
        }
    }
    ```
2. Aggiungere `WebResultsWithCountAndOffset` a `main`:
    ```csharp
    static void Main(string[] args)
    {
        var client = new WebSearchAPI(new ApiKeyServiceClientCredentials("YOUR_SUBSCRIPTION_KEY"));

        WebResults(client);
        // Search with count and offset...
        WebResultsWithCountAndOffset(client);  

        Console.WriteLine("Press any key to exit...");
        Console.ReadKey();
    }
    ```
3. Eseguire l'applicazione.

### <a name="filter-for-news"></a>Filtrare in base alle notizie

In questo esempio viene usato il parametro `response_filter` per filtrare i risultati della ricerca. I risultati della ricerca restituiti si limitano agli articoli relativi a "Microsoft". Vengono stampati `name` e `URL` per il primo risultato.

1. Aggiungere il codice seguente al progetto console:
    ```csharp
    public static void WebSearchWithResponseFilter(WebSearchAPI client)
    {
        try
        {
            IList<string> responseFilterstrings = new List<string>() { "news" };
            var webData = client.Web.SearchAsync(query: "Microsoft", responseFilter: responseFilterstrings).Result;
            Console.WriteLine("\r\nSearching for \" Microsoft \" with response filter \"news\"");

            if (webData?.News?.Value?.Count > 0)
            {
                var firstNewsResult = webData.News.Value.FirstOrDefault();

                if (firstNewsResult != null)
                {
                    Console.WriteLine("News Results #{0}", webData.News.Value.Count);
                    Console.WriteLine("First news result name: {0} ", firstNewsResult.Name);
                    Console.WriteLine("First news result URL: {0} ", firstNewsResult.Url);
                }
                else
                {
                    Console.WriteLine("Couldn't find first News results!");
                }
            }
            else
            {
                Console.WriteLine("Didn't see any News data..");
            }

        }
        catch (Exception ex)
        {
            Console.WriteLine("Encountered exception. " + ex.Message);
        }
    }
    ```
2. Aggiungere `WebResultsWithCountAndOffset` a `main`:
    ```csharp
    static void Main(string[] args)
    {
        var client = new WebSearchAPI(new ApiKeyServiceClientCredentials("YOUR_SUBSCRIPTION_KEY"));

        WebResults(client);
        // Search with count and offset...
        WebResultsWithCountAndOffset(client);  
        // Search with news filter...
        WebSearchWithResponseFilter(client);

        Console.WriteLine("Press any key to exit...");
        Console.ReadKey();
    }
    ```
3. Eseguire l'applicazione.

### <a name="use-safe-search-answer-count-and-the-promote-filter"></a>Usare i filtri safe search, answer count e promote

In questo esempio vengono usati i parametri `answer_count`, `promote` e `safe_search` per filtrare i risultati della ricerca di "Music Videos". Vengono visualizzati `name` e `URL` per il primo risultato.

1. Aggiungere il codice seguente al progetto console:
    ```csharp
    public static void WebSearchWithAnswerCountPromoteAndSafeSearch(WebSearchAPI client)
    {
        try
        {
            IList<string> promoteAnswertypeStrings = new List<string>() { "videos" };
            var webData = client.Web.SearchAsync(query: "Music Videos", answerCount: 2, promote: promoteAnswertypeStrings, safeSearch: SafeSearch.Strict).Result;
            Console.WriteLine("\r\nSearching for \"Music Videos\"");

            if (webData?.Videos?.Value?.Count > 0)
            {
                var firstVideosResult = webData.Videos.Value.FirstOrDefault();

                if (firstVideosResult != null)
                {
                    Console.WriteLine("Video Results # {0}", webData.Videos.Value.Count);
                    Console.WriteLine("First Video result name: {0} ", firstVideosResult.Name);
                    Console.WriteLine("First Video result URL: {0} ", firstVideosResult.ContentUrl);
                }
                else
                {
                    Console.WriteLine("Couldn't find videos results!");
                }
            }
            else
            {
                Console.WriteLine("Didn't see any data..");
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine("Encountered exception. " + ex.Message);
        }
    }
    ```
2. Aggiungere `WebResultsWithCountAndOffset` a `main`:
    ```csharp
    static void Main(string[] args)
    {
        var client = new WebSearchAPI(new ApiKeyServiceClientCredentials("YOUR_SUBSCRIPTION_KEY"));

        WebResults(client);
        // Search with count and offset...
        WebResultsWithCountAndOffset(client);  
        // Search with news filter...
        WebSearchWithResponseFilter(client);
        // Search with answer count, promote, and safe search
        WebSearchWithAnswerCountPromoteAndSafeSearch(client);

        Console.WriteLine("Press any key to exit...");
        Console.ReadKey();
    }
    ```
3. Eseguire l'applicazione.

## <a name="clean-up-resources"></a>Pulire le risorse

Dopo aver terminato di usare questo progetto, rimuovere la chiave di sottoscrizione dal codice dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esempi di SDK Node.js per Servizi cognitivi](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/)
