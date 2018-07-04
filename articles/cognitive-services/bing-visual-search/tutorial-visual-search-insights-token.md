---
title: SDK di Ricerca visiva Bing - Esercitazione su ImageInsightsToken | Microsoft Docs
description: Come usare l'SDK di Ricerca visiva Bing per ottenere gli URL delle immagini specificate da ImageInsightsToken.
services: cognitive-services
author: mikedodaro
manager: ronakshah
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: article
ms.date: 06/21/2018
ms.author: rosh
ms.openlocfilehash: 578fa90f504920030b488d2b8fa3a2d0232cccce
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2018
ms.locfileid: "36753598"
---
# <a name="tutorial-bing-visual-search-sdk-imageinsightstoken-and-results"></a>Esercitazione: SDK di Ricerca visiva Bing - ImageInsightsToken e risultati
L'SDK di Ricerca visiva Bing include un'opzione per trovare immagini online da una ricerca precedente che restituisce un oggetto `ImageInsightsToken`.  Questo esempio ottiene un oggetto `ImageInsightsToken` e usa il token in una ricerca successiva.  Il codice invia l'oggetto `ImageInsightsToken` a Bing e restituire risultati che includono gli URL di ricerca Bing e gli URL di immagini simili trovate online.

## <a name="prerequisites"></a>prerequisiti
Visual Studio 2017. Se necessario, è possibile scaricare la versione gratuita della community da qui: https://www.visualstudio.com/vs/community/.
Per autenticare le chiamate SDK, è necessaria una chiave di API di Servizi cognitivi. Iscriversi per ottenere una chiave di prova gratuita. La chiave di prova è valida per sette giorni, con una chiamata al secondo. Per gli scenari di produzione acquistare una chiave di accesso. Vedere anche le informazioni sui prezzi.
Possibilità di eseguire .NET Core SDK e app .NET Core 1.1. CORE, Framework e Runtime sono disponibili qui: https://www.microsoft.com/net/download/.

##<a name="application-dependencies"></a>Dipendenze dell'applicazione
Per configurare un'applicazione console tramite l'SDK di Ricerca Web Bing, passare all'opzione Gestisci pacchetti NuGet in Esplora soluzioni in Visual Studio. Aggiungere:
* Microsoft.Azure.CognitiveServices.Search.VisualSearch
* Pacchetti Microsoft.Azure.CognitiveServices.Search.ImageSearchpackage.

L'installazione del pacchetto SDK di Ricerca Web NuGet determina anche l'installazione di dipendenze, tra cui:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="get-the-imageinsightstoken-from-image-search"></a>Ottenere l'oggetto ImageInsightsToken da Ricerca immagini
Questo esempio usa un oggetto `ImageInsightsToken` ottenuto mediante il metodo seguente.  Per altre informazioni su questa chiamata, vedere [Guida introduttiva all'SDK di Ricerca immagini in C#](https://docs.microsoft.com/en-us/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart).

Il codice cerca i risultati in una query per 'Canadian Rockies' e ottiene un oggetto ImageInsightsToken. Visualizza il primo token di informazioni dettagliate sull'immagine, l'URL di anteprima e l'URL del contenuto dell'immagine.  Il metodo restituisce l'oggetto `ImageInsightsToken` per l'uso in una richiesta di Ricerca visiva successiva.

```
        private static String ImageResults(String subKey)
        {
            String insightTok = "None";
            try
            {
                var client = new ImageSearchAPI(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey)); //
                var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
                Console.WriteLine("Search images for query \"canadian rockies\"");

                if (imageResults == null)
                {
                    Console.WriteLine("No image result data.");
                }
                else
                {
                    // Image results
                    if (imageResults.Value.Count > 0)
                    {
                        var firstImageResult = imageResults.Value.First();

                        Console.WriteLine($"\r\nImage result count: {imageResults.Value.Count}");
                        insightTok = firstImageResult.ImageInsightsToken;
                        Console.WriteLine($"First image insights token: {firstImageResult.ImageInsightsToken}");  
                        Console.WriteLine($"First image thumbnail url: {firstImageResult.ThumbnailUrl}");
                        Console.WriteLine($"First image content url: {firstImageResult.ContentUrl}");
                    }
                    else
                    {
                        insightTok = "None found";
                        Console.WriteLine("Couldn't find image results!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("\r\nEncountered exception. " + ex.Message);
            }

            return insightTok;
        }
```

## <a name="specify-the-imageinsightstoken-for-visual-search-request"></a>Specificare l'oggetto ImageInsightsToken per la richiesta di Ricerca visiva
Questo esempio usa il token di informazioni dettagliate restituito dal metodo precedente. Il codice seguente crea un oggetto `ImageInfo` dall'oggetto `ImageInsightsToken` e carica ImageInfo in un oggetto `VisualSearchRequest`. Specificare l'oggetto `ImageInsightsToken` in un oggetto `ImageInfo` per l'oggetto `VisualSearchRequest`

```
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```
## <a name="use-visual-search-to-find-images-from-an-imageinsightstoken"></a>Usare Ricerca visiva per trovare immagini da un oggetto ImageInsightsToken
L'oggetto `VisualSearchRequest` contiene informazioni sull'immagine da cercare nell'oggetto `ImageInfo`.  Il metodo `VisualSearchMethodAsync` ottiene i risultati.
```
// An image binary is not necessary here, as the image is specified by insights token.
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;
Console.WriteLine("\r\nVisual search request with knowledgeRequest");

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Ottenere i dati dell'URL da ImageModuleAction
I risultati di Ricerca visiva sono oggetti `ImageTag`.  Ogni tag contiene un elenco di oggetti `ImageAction`.  Ogni `ImageAction` contiene un campo `Data` che corrisponde a un elenco di valori che dipendono dal tipo di azione:

È possibile ottenere i vari tipi con il codice seguente:
```
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);

```
L'applicazione completa restituisce:

* ActionType: MoreSizes -> WebSearchUrl:
* ActionType: VisualSearch -> WebSearchUrl:
* ActionType: ImageById -> WebSearchUrl:
* ActionType: RelatedSearches -> WebSearchUrl:
* ActionType: DocumentLevelSuggestions -> WebSearchUrl:
* ActionType: TopicResults -> WebSearchUrl: https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=BcQifmzdKFyyBusjLxxgO42kzq1Geh7RucVVqvH-900&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fcanadian%2brocky&p=DevEx,5823.1
* ActionType: ImageResults -> WebSearchUrl: https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=PV9GzMFOI0AHZp2gKeWJ8DcveSDRE3fP2jHDKMpJSU8&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3doutdoor&p=DevEx,5831.1

Come mostrato nell'elenco precedente, i tipi `TopicResults` e `ImageResults` contengono query per le immagini correlate. Gli URL nell'elenco sono collegati ai risultati della ricerca Bing.


## <a name="pagesincluding-actiontype-urls-of-images-found-by-visual-search"></a>URL PagesIncluding ActionType delle immagini trovate da Ricerca visiva

Ottenere l'URL dell'immagine effettiva richiede un cast che legge `ActionType` come `ImageModuleAction`, che contiene un elemento `Data` con un elenco di valori.  Ogni valore è l'URL di un'immagine.  Il comando seguente esegue il cast del tipo di azione `PagesIncluding` in `ImageModuleAction` e legge i valori.
```
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```
Per altre informazioni su questi tipi di dati, vedere [Images - Visual Search](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch) (Immagini - Ricerca visiva).
## <a name="complete-code"></a>Codice completo

Negli esempi precedenti viene eseguito il codice seguente. Invia `ImageInsightsToken` in una richiesta POST. Visualizza gli URL di Ricerca Bing per ogni oggetto ActionType. Se l'oggetto ActionType è `PagesIncluding`, il codice ottiene gli elementi `ImageObject` in `Data`.  `Data` contiene un elenco di valori, che sono gli URL delle immagini nelle pagine Web.  Copiare e incollare gli URL risultanti da Ricerca visiva nel browser per visualizzare i risultati. Copiare e incollare elementi ContentUrl nel browser per visualizzare le immagini.

```
using System;
using System.IO;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Search.VisualSearch;
using Microsoft.Azure.CognitiveServices.Search.VisualSearch.Models;
using Microsoft.Azure.CognitiveServices.Search.ImageSearch;

namespace VisualSearchFeatures
{
    class Program
    {
        static void Main(string[] args)
        {
            String subscriptionKey = "YOUR-ACCESS-KEY";

            insightsToken = ImageResults(subscriptionKey);

            VisualSearchInsightsToken(subscriptionKey, insightsToken);

            Console.WriteLine("Any key to quit...");
            Console.ReadKey();

        }

        // Searches for results on query (Canadian Rockies) and gets an ImageInsightsToken.
        // Also prints first image insights token, thumbnail url, and image content url.
        private static String ImageResults(String subKey)
        {
            String insightTok = "None";
            try
            {
                var client = new ImageSearchAPI(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey)); //
                var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
                Console.WriteLine("Search images for query \"canadian rockies\"");

                if (imageResults == null)
                {
                    Console.WriteLine("No image result data.");
                }
                else
                {
                    // Image results
                    if (imageResults.Value.Count > 0)
                    {
                        var firstImageResult = imageResults.Value.First();

                        Console.WriteLine($"\r\nImage result count: {imageResults.Value.Count}");
                        insightTok = firstImageResult.ImageInsightsToken;
                        Console.WriteLine($"First image insights token: {firstImageResult.ImageInsightsToken}");  
                        Console.WriteLine($"First image thumbnail url: {firstImageResult.ThumbnailUrl}");
                        Console.WriteLine($"First image content url: {firstImageResult.ContentUrl}");
                    }
                    else
                    {
                        insightTok = "None found";
                        Console.WriteLine("Couldn't find image results!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("\r\nEncountered exception. " + ex.Message);
            }

            return insightTok;
        }


        // This method will get Visual Search results from an imageInsightsToken obtained from the return value of the ImageResults method.
        // The method includes imageInsightsToken the in a knowledgeRequest parameter, along with a cropArea object. 
        // It prints out the imageInsightsToken uploaded in the request.
        // Finally the example prints URLs of images found using the imageInsightsToken.

        public static void VisualSearchInsightsToken(string subscriptionKey, string insightsTok)
        {
            var client = new VisualSearchAPI(new Microsoft.Azure.CognitiveServices.Search.VisualSearch.ApiKeyServiceClientCredentials(subscriptionKey));

            try
            {
                // The image can be specified via an insights token, in the ImageInfo object.
                ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);

                // An image binary is not necessary here, as the image is specified by insights token.
                VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

                var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;
                Console.WriteLine("\r\nVisual search request with imageInsightsToken and knowledgeRequest");

                if (visualSearchResults == null)
                {
                    Console.WriteLine("No visual search result data.");
                }
                else
                {
                    // Visual Search results
                    if (visualSearchResults.Image?.ImageInsightsToken != null)
                    {
                        Console.WriteLine($"Uploaded image insights token: {insightsTok}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find image insights token!");
                    }

                    if (visualSearchResults.Tags.Count > 0)
                    {
                        // List of tags
                        foreach (ImageTag t in visualSearchResults.Tags)
                        {
                            foreach (ImageAction i in t.Actions)
                            {
                                Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " WebSearchURL: " + i.WebSearchUrl);

                                if (i.ActionType == "PagesIncluding")
                                {
                                    foreach (ImageObject o in (i as ImageModuleAction).Data.Value)
                                    {
                                        Console.WriteLine("ContentURL: " + o.ContentUrl);
                                    }
                                }
                            }
                        }
                    }

                    else
                    {
                        Console.WriteLine("Couldn't find image tags!");
                    }

                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }
        }
    }
}

```
## <a name="next-steps"></a>Passaggi successivi
[Risposta di Ricerca visiva](https://review.docs.microsoft.com/en-us/azure/cognitive-services/bing-visual-search/overview?branch=pr-en-us-44614#the-response)