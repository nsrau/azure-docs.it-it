---
title: Trovare immagini simili da ricerche precedenti con ImageInsightsToken - Ricerca visiva Bing
titleSuffix: Azure Cognitive Services
description: Usare l'SDK di Ricerca visiva Bing per ottenere gli URL delle immagini specificate da ImageInsightsToken.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: article
ms.date: 04/05/2019
ms.author: rosh
ms.openlocfilehash: 39a95e877c766eb8f491c166edeb9d96f21db7dd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60829515"
---
# <a name="find-similar-images-from-previous-searches-using-imageinsightstoken"></a>Trovare immagini simili da ricerche precedenti con ImageInsightsToken

L'SDK di Ricerca visiva Bing consente di trovare immagini online da una ricerca precedente che restituisce un oggetto `ImageInsightsToken`. Questa applicazione ottiene un oggetto `ImageInsightsToken` e usa il token in una ricerca successiva. Invia quindi il `ImageInsightsToken` a Bing e restituire i risultati che includono gli URL di ricerca Bing e gli URL delle immagini simili disponibili online.

Il codice sorgente completo per questa esercitazione è reperibile con annotazioni e la gestione degli errori aggiuntivo sul [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchInisghtsTokens.cs).

## <a name="prerequisites"></a>Prerequisiti

* Qualsiasi edizione di [Visual Studio 2017](https://www.visualstudio.com/downloads/).
* Se si usa Linux/MacOS, è possibile eseguire questa applicazione utilizzando [Mono](https://www.mono-project.com/).
* I pacchetti Ricerca visiva e Ricerca immagini NuGet.
    - Da Esplora soluzioni in Visual Studio, fare clic destro sul progetto, quindi scegliere **Gestisci pacchetti NuGet** dal menu di scelta. Installare il pacchetto `Microsoft.Azure.CognitiveServices.Search.CustomSearch` e il pacchetto `Microsoft.Azure.CognitiveServices.Search.ImageSearch`. Installando i pacchetti NuGet vengono anche installati gli elementi seguenti:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="get-the-imageinsightstoken-from-the-bing-image-search-sdk"></a>Ottenere l'oggetto ImageInsightsToken dall'SDK di Ricerca immagini Bing

Questa applicazione usa un oggetto `ImageInsightsToken` ottenuto tramite l'[SDK di Ricerca immagini Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart). In una nuova applicazione console C# creare un client per chiamare l'API tramite `ImageSearchAPI()`. Usare quindi `SearchAsync()` con la query:

```csharp
var client = new ImageSearchAPI(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey));
var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
Console.WriteLine("Search images for query \"canadian rockies\"");
```

Archiviare il primo risultato di ricerca ottenuto usando `imageResults.Value.First()` e quindi archiviare l'`ImageInsightsToken` delle informazioni dettagliate sull'immagine.

```csharp
String insightTok = "None";
if (imageResults.Value.Count > 0)
{
    var firstImageResult = imageResults.Value.First();
    insightTok = firstImageResult.ImageInsightsToken;
}
else
{
    insightTok = "None found";
    Console.WriteLine("Couldn't find image results!");
}
```

Ciò `ImageInsightsToken` viene inviato in una richiesta di ricerca visiva Bing.

## <a name="add-the-imageinsightstoken-to-a-visual-search-request"></a>Aggiungere l'oggetto ImageInsightsToken a una richiesta di Ricerca visiva

Specificare l'`ImageInsightsToken` per una richiesta di Ricerca visiva mediante la creazione di un oggetto `ImageInfo` dall'`ImageInsightsToken` contenuto nelle risposte di Ricerca visiva Bing.

```csharp
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```

## <a name="use-bing-visual-search-to-find-images-from-an-imageinsightstoken"></a>Usare Ricerca visiva Bing per trovare immagini da un oggetto ImageInsightsToken

L'oggetto `VisualSearchRequest` contiene informazioni sull'immagine nell'oggetto `ImageInfo` da cercare. Il metodo `VisualSearchMethodAsync()` ottiene i risultati. Non è necessario fornire un file binario di immagine, in quanto l'immagine è rappresentato dal token.

```csharp
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;

```

## <a name="iterate-through-the-visual-search-results"></a>Eseguire un'iterazione sui risultati di Ricerca visiva

I risultati di Ricerca visiva sono oggetti `ImageTag`. Ogni tag contiene un elenco di oggetti `ImageAction`. Ciascuna `ImageAction` contiene un `Data` campo, ovvero un elenco di valori che dipendono dal tipo di azione. Ad esempio è possibile eseguire un'iterazione sugli oggetti `ImageTag` in `visualSearchResults.Tags` e ottenere il tag `ImageAction` al suo interno. L'esempio seguente visualizza i dettagli di `PagesIncluding` azioni:

```csharp
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
```

### <a name="pagesincluding-actiontypes"></a>PagesIncluding ActionTypes

Ottenere l'URL dell'immagine effettiva dai tipi di azione richiede un cast che legge `ActionType` come `ImageModuleAction`, che contiene un elemento `Data` con un elenco di valori. Ogni valore è l'URL di un'immagine.  L'esecuzione dei seguenti cast di `PagesIncluding` tipo di azione `ImageModuleAction` e legge i valori:

```csharp
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```

Per altre informazioni su questi tipi di dati, vedere [Images - Visual Search](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch) (Immagini - Ricerca visiva).

## <a name="returned-urls"></a>URL restituiti

L'applicazione completa restituisce gli URL seguenti:

|ActionType  |URL  | |
|---------|---------|---------|
|MoreSizes -> WebSearchUrl     |         |
|VisualSearch -> WebSearchUrl     |         |
|ImageById -> WebSearchUrl    |         |
|RelatedSearches -> WebSearchUrl:    |         |
|DocumentLevelSuggestions -> WebSearchUrl:     |         |
|TopicResults -> WebSearchUrl    | https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=BcQifmzdKFyyBusjLxxgO42kzq1Geh7RucVVqvH-900&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fcanadian%2brocky&p=DevEx,5823.1       |
|ImageResults -> WebSearchUrl    |  https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=PV9GzMFOI0AHZp2gKeWJ8DcveSDRE3fP2jHDKMpJSU8&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3doutdoor&p=DevEx,5831.1       |

Come mostrato sopra, i tipi `TopicResults` e `ImageResults` contengono query per le immagini correlate. Gli URL sono collegati ai risultati della ricerca Bing.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un'app Web a pagina singola con Ricerca visiva](tutorial-bing-visual-search-single-page-app.md)
