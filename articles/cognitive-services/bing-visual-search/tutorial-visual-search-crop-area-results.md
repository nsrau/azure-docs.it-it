---
title: "Esercitazione: Ritagliare un'immagine con l'SDK di Ricerca visiva Bing"
description: Usare l'SDK di Ricerca visiva Bing per ottenere informazioni approfondite da aree specifiche di un'immagine.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: article
ms.date: 04/03/2019
ms.author: rosh
ms.openlocfilehash: d1d3243f1d11ee9093a249ce61ceab8128dbf91d
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59048590"
---
# <a name="tutorial-crop-an-image-with-the-bing-visual-search-sdk-for-c"></a>Esercitazione: Ritagliare un'immagine con l'SDK di Ricerca visiva Bing per C#

il SDK di ricerca Bing Visual consente di ritagliare un'immagine prima di trovare immagini linea simili. Questa applicazione ritaglia una singola persona da un'immagine che contiene più persone e quindi restituisce risultati della ricerca che contengono immagini simili disponibili online.

Il codice sorgente completo per questa applicazione è disponibile in [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchCropImage.cs) con annotazioni aggiuntive e altre informazioni sulla gestione degli errori.

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Inviare una richiesta usando l'SDK di Ricerca visiva Bing
> * Ritagliare un'area dell'immagine da cercare con Ricerca visiva Bing
> * Ricevere e gestire la risposta
> * Trovare gli URL delle azioni nella risposta

## <a name="prerequisites"></a>Prerequisiti

* Qualsiasi edizione di [Visual Studio 2017](https://www.visualstudio.com/downloads/).
* Se si usa Linux/MacOS, questa applicazione può essere eseguita tramite [Mono](https://www.mono-project.com/).
* Il pacchetto [Ricerca personalizzata NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) installato.
    - Da Esplora soluzioni in Visual Studio, fare clic destro sul progetto, quindi scegliere **Gestisci pacchetti NuGet** dal menu di scelta. Installare il pacchetto `Microsoft.Azure.CognitiveServices.Search.CustomSearch`. L'installazione del pacchetto Ricerca personalizzata NuGet comporta anche l'installazione degli assembly seguenti:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="specify-the-image-crop-area"></a>Specificare l'area di ritaglio dell'immagine

L'applicazione ritaglia un'area di questa immagine del team di dirigenti senior di Microsoft. Questa area di ritaglio è definita usando le coordinate alto a sinistra e basso a destra, rappresentate come percentuale dell'intera immagine:  

![Team di leadership senior di Microsoft](./media/MS_SrLeaders.jpg)

Questa immagine viene ritagliata mediante la creazione di un oggetto `ImageInfo` dall'area di ritaglio dell'oggetto e il caricamento dell'oggetto `ImageInfo` in un `VisualSearchRequest`. Il `ImageInfo` oggetto include anche l'URL dell'immagine:

```csharp
CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
string imageURL = "https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg";
ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);

VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);
```

## <a name="search-for-images-similar-to-the-crop-area"></a>Cercare immagini simili all'area di ritaglio

La variabile `VisualSearchRequest` contiene informazioni sull'area di ritaglio dell'immagine e il relativo URL. Il `VisualSearchMethodAsync()` metodo ottiene i risultati:

```csharp
Console.WriteLine("\r\nSending visual search request with knowledgeRequest that contains URL and crop area");
var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result;

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Ottenere i dati dell'URL da `ImageModuleAction`

I risultati di Ricerca visiva Bing sono oggetti `ImageTag`. Ogni tag contiene un elenco di oggetti `ImageAction`. Ciascuna `ImageAction` contiene un `Data` campo, ovvero un elenco di valori che dipendono dal tipo di azione.

È possibile stampare i vari tipi con il codice seguente:

```csharp
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);
```

L'applicazione completa restituisce:

|ActionType  |URL  | |
|---------|---------|---------|
|PagesIncluding WebSearchURL     |         |
|MoreSizes WebSearchURL     |         |  
|VisualSearch WebSearchURL    |         |
|ImageById WebSearchURL     |         |  
|RelatedSearches WebSearchURL     |         |
|Entity -> WebSearchUrl     | https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=BvvDoRtmZ35Xc_UZE4lZx6_eg7FHgcCkigU1D98NHQo&v=1&r=https%3a%2f%2fwww.bing.com%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5380.1        |
|TopicResults -> WebSearchUrl    |  https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=3QGtxPb3W9LemuHRxAlW4CW7XN4sPkUYCUynxAqI9zQ&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fnadella%2bsatya&p=DevEx,5382.1        |
|ImageResults -> WebSearchUrl    |  https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=l-WNHO89Kkw69AmIGe2MhlUp6MxR6YsJszgOuM5sVLs&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5384.1        |

Come mostrato sopra, `Entity` ActionType contiene una query di ricerca Bing che restituisce informazioni relative a una persona, un luogo o un oggetto riconoscibile. I tipi `TopicResults` e `ImageResults` tipi contengono query per le immagini correlate. Gli URL nell'elenco sono collegati ai risultati della ricerca Bing.

## <a name="get-urls-for-pagesincluding-actiontype-images"></a>Ottenere gli URL per `PagesIncluding` `ActionType` immagini

Ottenere l'URL dell'immagine effettiva richiede un cast che legge `ActionType` come `ImageModuleAction`, che contiene un elemento `Data` con un elenco di valori. Ogni valore è l'URL di un'immagine. L'esecuzione dei seguenti cast di `PagesIncluding` tipo di azione `ImageModuleAction` e legge i valori:

```csharp
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Creare un'app Web a pagina singola con Ricerca visiva](tutorial-bing-visual-search-single-page-app.md)

## <a name="see-also"></a>Vedere anche 
> [Informazioni sull'API Ricerca visiva Bing](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/overview)
