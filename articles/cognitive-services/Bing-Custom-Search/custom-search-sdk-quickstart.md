---
title: Guida introduttiva all'SDK di Ricerca personalizzata per C# | Microsoft Docs
titleSuffix: Cognitive Services
description: Configurazione dell'SDK di Ricerca personalizzata per l'applicazione console in C#.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 01/31/2018
ms.author: rosh
ms.openlocfilehash: 59b208b53bec974433c50c0e2304dc96bd9bd09e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372793"
---
# <a name="custom-search-sdk-c-quickstart"></a>Guida introduttiva all'SDK di Ricerca personalizzata per C#

L'SDK di Ricerca personalizzata Bing contiene la funzionalità dell'API REST per query di entità e analisi dei risultati.

## <a name="application-dependencies"></a>Dipendenze dell'applicazione

Per configurare un'applicazione console tramite l'SDK di Ricerca personalizzata Bing, passare all'opzione `Manage NuGet Packages` da Esplora soluzioni in Visual Studio. Aggiungere il pacchetto `Microsoft.Azure.CognitiveServices.Search.CustomSearch`.

L'installazione del pacchetto di [Ricerca personalizzata NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) comporta anche l'installazione di dipendenze, inclusi gli assembly seguenti:
* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="entity-search-client"></a>Client di Ricerca entità

Per creare un'istanza del client CustomSearchAPI, aggiungere le direttive using:
```
using Microsoft.Azure.CognitiveServices.Search.CustomSearch;

```

Creare un'istanza del client di ricerca personalizzata: sostituire `YOUR-CUSTOM-SEARCH-KEY` e `YOUR-CUSTOM-CONFIG-ID` con la chiave di accesso e l'ID di configurazione dell'endpoint dell'API assegnato in [My Instances](https://www.customsearch.ai/) (Istanze in uso).
```
var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-CUSTOM-SEARCH-KEY"));

```
Usare il client per eseguire una ricerca con un testo di query:
```
var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;

```
## <a name="parse-the-results"></a>Analizzare i risultati

Il metodo `SearchAsync` restituisce un oggetto `WebData` che contiene `WebPages` se sono state trovate delle pagine Web per la query. Questo codice trova il primo risultato e restituisce i valori di `Name` e `URL`.
```
var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
 
Console.WriteLine("Searched for Query# \" Xbox \"");

 //WebPages
if (webData?.WebPages?.Value?.Count > 0)
{
    // find the first web page
    var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

    if (firstWebPagesResult != null)
    {
        Console.WriteLine("Webpage Results#{0}", webData.WebPages.Value.Count);
        Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
        Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
    }
    else
    {
        Console.WriteLine("Couldn't find web results!");
    }
}
else
{
    Console.WriteLine("Didn't see any Web data..");
}

```
## <a name="complete-console-application"></a>Applicazione console completa

Il codice seguente esegue la ricerca usando la query "Xbox" e visualizza i valori di `Name` e `URL` per i primi risultati Web.
```
using System;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Search.CustomSearch;

namespace CustomSrchSDK
{
    class Program
    {
        static void Main(string[] args)
        {

            var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-CUSTOM-SEARCH-KEY"));

            try
            {
                // This will look up a single query (Xbox).
                var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
                Console.WriteLine("Searched for Query# \" Xbox \"");

                //WebPages
                if (webData?.WebPages?.Value?.Count > 0)
                {
                    // find the first web page
                    var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

                    if (firstWebPagesResult != null)
                    {
                        Console.WriteLine("Webpage Results#{0}", webData.WebPages.Value.Count);
                        Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
                        Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find web results!");
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

            Console.WriteLine("Any key to exit...");
            Console.ReadKey();
        }

    }
}


```

## <a name="next-steps"></a>Passaggi successivi

[Esempi di .NET SDK per Servizi cognitivi](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
