---
title: 'Avvio rapido: Eseguire una ricerca di notizie - SDK di Ricerca notizie Bing per C#'
titleSuffix: Azure Cognitive Services
description: Usare questa guida di avvio rapido per cercare notizie con l'SDK di Ricerca notizie Bing per Python ed elaborare la risposta.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: quickstart
ms.date: 01/30/2018
ms.author: v-gedod
ms.custom: seodec2018
ms.openlocfilehash: 5b3e68765fbcff12dcb5337aec38623b8994882c
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2019
ms.locfileid: "54156800"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-c"></a>Avvio rapido: Eseguire una ricerca di notizie con l'SDK di Ricerca notizie Bing per C#

L'SDK di Ricerca notizie Bing contiene la funzionalità dell'API REST per query di notizie e analisi dei risultati. 

Il [codice sorgente per gli esempi dell'SDK di Ricerca notizie Bing per C#](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingNewsSearch) è disponibile su GitHub.

## <a name="application-dependencies"></a>Dipendenze dell'applicazione
Ottenere una [chiave di accesso di Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/) in **Ricerca**.  Vedere anche [Prezzi di Servizi cognitivi - API di ricerca Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).  

Per configurare un'applicazione console tramite l'SDK di Ricerca notizie Bing, passare all'opzione `Manage NuGet Packages` da Esplora soluzioni in Visual Studio.  Aggiungere il pacchetto `Microsoft.Azure.CognitiveServices.Search.NewsSearch`.

L'installazione del [pacchetto SDK di Ricerca notizie NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.NewsSearch/1.2.0) determina anche l'installazione di dipendenze, tra cui:
* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="news-search-client"></a>Client di Ricerca notizie
Per creare un'istanza di `NewsSearchClient`, aggiungere la direttiva using:
```
using Microsoft.Azure.CognitiveServices.Search.NewsSearch;

```
Creare quindi un'istanza del client:
```
var client = new NewsSearchClient(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));


```
Usare il client per eseguire una ricerca con un testo di query:
```
var newsResults = client.News.SearchAsync(query: "Quantum  Computing", market: "en-us", count: 10).Result;
Console.WriteLine("Search news for query \"Quantum  Computing\" with market and count");

```
Analizzare le notizie restituite nei risultati della query precedente:
```
if (newsResults.Value.Count > 0)
{
    var firstNewsResult = newsResults.Value[0];

    Console.WriteLine($"TotalEstimatedMatches value: {newsResults.TotalEstimatedMatches}");
    Console.WriteLine($"News result count: {newsResults.Value.Count}");
    Console.WriteLine($"First news name: {firstNewsResult.Name}");
    Console.WriteLine($"First news url: {firstNewsResult.Url}");
    Console.WriteLine($"First news description: {firstNewsResult.Description}");
    Console.WriteLine($"First news published time: {firstNewsResult.DatePublished}");
    Console.WriteLine($"First news provider: {firstNewsResult.Provider[0].Name}");
}

else
{
    Console.WriteLine("Couldn't find news results!");
}

```
## <a name="complete-console-application"></a>Applicazione console completa

Questa applicazione console esegue la query definita in precedenza e cerca le notizie per "Quantum Computing". La richiesta include i parametri `market` e `count`. Il codice verifica il numero di risultati e visualizza `totalEstimatedMatches`, `name`, `url`, `description`, `published time` e `name` di `provider` per il primo risultato delle notizie.

```
using System;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Search.NewsSearch;

namespace NewsSrchSDK
{
    class Program
    {
        static void Main(string[] args)
        {
            var client = new NewsSearchClient(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));

            try
            {
                var newsResults = client.News.SearchAsync(query: "Quantum  Computing", market: "en-us", count: 10).Result;
                Console.WriteLine("Search news for query \"Quantum  Computing\" with market and count");

                if (newsResults == null)
                {
                    Console.WriteLine("Didn't see any news result data..");
                }
                else
                {
                    if (newsResults.Value.Count > 0)
                    {
                        var firstNewsResult = newsResults.Value.First();

                        Console.WriteLine($"TotalEstimatedMatches value: {newsResults.TotalEstimatedMatches}");
                        Console.WriteLine($"News result count: {newsResults.Value.Count}");
                        Console.WriteLine($"First news name: {firstNewsResult.Name}");
                        Console.WriteLine($"First news url: {firstNewsResult.Url}");
                        Console.WriteLine($"First news description: {firstNewsResult.Description}");
                        Console.WriteLine($"First news published time: {firstNewsResult.DatePublished}");
                        Console.WriteLine($"First news provider: {firstNewsResult.Provider.First().Name}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find news results!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }

            // Include these methods to run queries explained under headings following.
            // NewsSearchWithFilters(client);
            // NewsCategory(client);
            // TrendingTopics(client);

            Console.WriteLine("Any key to exit...");
            Console.ReadKey();
        }

    }
}

```
## <a name="recent-news-freshness-and-sortby-parameters"></a>Notizie recenti e parametri freshness e sortBy
Il codice seguente cerca le notizie più recenti per "Artificial Intelligence" con i parametri `freshness` e `sortBy`. Verifica il numero di risultati e visualizza `totalEstimatedMatches`, `name`, `url`, `description`, `published time` e `name` del provider del primo risultato delle notizie.
```
        public static void NewsSearchWithFilters(NewsSearchClient client)
        {
            try
            {
                var newsResults = client.News.SearchAsync(query: "Artificial Intelligence", market: "en-us", freshness: "Week", sortBy: "Date").Result;
                Console.WriteLine("Search most recent news for query \"Artificial Intelligence\" with freshness and sortBy");

                if (newsResults == null)
                {
                    Console.WriteLine("Didn't see any news result data..");
                }
                else
                {
                    if (newsResults.Value.Count > 0)
                    {
                        var firstNewsResult = newsResults.Value.First();

                        Console.WriteLine($"\r\nTotalEstimatedMatches value: {newsResults.TotalEstimatedMatches}");
                        Console.WriteLine($"News result count: {newsResults.Value.Count}");
                        Console.WriteLine($"First news name: {firstNewsResult.Name}");
                        Console.WriteLine($"First news url: {firstNewsResult.Url}");
                        Console.WriteLine($"First news description: {firstNewsResult.Description}");
                        Console.WriteLine($"First news published time: {firstNewsResult.DatePublished}");
                        Console.WriteLine($"First news provider: {firstNewsResult.Provider.First().Name}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find news results!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }
        }

```

## <a name="category-news-safe-search"></a>Notizie di categoria, ricerca sicura
Il codice seguente cerca le notizie relative a intrattenimento film e TV nelle notizie di categoria e usa la funzionalità di ricerca sicura.  Verifica il numero di risultati e visualizza `category`, `name`, `url`, `description`, `published time` e `name` del provider del primo risultato delle notizie.
```
        public static void NewsCategory(NewsSearchClient client)
        {
            try
            {
                var newsResults = client.News.CategoryAsync(category: "Entertainment_MovieAndTV", market: "en-us", safeSearch: "strict").Result;
                Console.WriteLine("Search category news for movie and TV entertainment with safe search");

                if (newsResults == null)
                {
                    Console.WriteLine("Didn't see any news result data..");
                }
                else
                {
                    if (newsResults.Value.Count > 0)
                    {
                        var firstNewsResult = newsResults.Value.First();

                        Console.WriteLine($"\r\nNews result count: {newsResults.Value.Count}");
                        Console.WriteLine($"First news category: {firstNewsResult.Category}");
                        Console.WriteLine($"First news name: {firstNewsResult.Name}");
                        Console.WriteLine($"First news url: {firstNewsResult.Url}");
                        Console.WriteLine($"First news description: {firstNewsResult.Description}");
                        Console.WriteLine($"First news published time: {firstNewsResult.DatePublished}");
                        Console.WriteLine($"First news provider: {firstNewsResult.Provider.First().Name}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find news results!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }
        }

```
## <a name="trending-topics"></a>Argomenti di tendenza
Il codice seguente cerca gli argomenti di tendenza delle notizie in Bing. Verifica il numero di risultati e visualizza `name`, `text of query`, `webSearchUrl`, `newsSearchUrl` e `image.Url` del primo risultato delle notizie.
```
        public static void TrendingTopics(NewsSearchClient client)
        {
            try
            {
                var trendingTopics = client.News.TrendingAsync(market: "en-us").Result;
                Console.WriteLine("Search news trending topics in Bing");

                if (trendingTopics == null)
                {
                    Console.WriteLine("Didn't see any news trending topics..");
                }
                else
                {
                    if (trendingTopics.Value.Count > 0)
                    {
                        var firstTopic = trendingTopics.Value.First();

                        Console.WriteLine($"\r\nTrending topics count: {trendingTopics.Value.Count}");
                        Console.WriteLine($"First topic name: {firstTopic.Name}");
                        Console.WriteLine($"First topic query: {firstTopic.Query.Text}");
                        Console.WriteLine($"First topic image url: {firstTopic.Image.Url}");
                        Console.WriteLine($"First topic webSearchUrl: {firstTopic.WebSearchUrl}");
                        Console.WriteLine($"First topic newsSearchUrl: {firstTopic.NewsSearchUrl}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find news trending topics!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }
        }

```

## <a name="next-steps"></a>Passaggi successivi

[Esempi di .NET SDK per Servizi cognitivi](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
