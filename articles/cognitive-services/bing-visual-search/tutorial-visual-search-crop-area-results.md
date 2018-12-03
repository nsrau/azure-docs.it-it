---
title: "Esercitazione: area di ritaglio dell'immagine e risultati - Ricerca visiva Bing"
description: Come usare l'SDK di Ricerca visiva Bing per ottenere gli URL delle immagini simili all'area di ritaglio di un'immagine caricata.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: tutorial
ms.date: 06/20/2018
ms.author: rosh
ms.openlocfilehash: 27141c014c9ccdf9d62c9bde5c96bd31abfc025e
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2018
ms.locfileid: "52447096"
---
# <a name="tutorial-bing-visual-search-sdk-image-crop-area-and-results"></a>Esercitazione: area di ritaglio dell'immagine e risultati dell'SDK di Ricerca visiva Bing
L'SDK di Ricerca visiva include un'opzione per selezionare un'area di un'immagine e trovare le immagini online che sono simili all'area di ritaglio dell'immagine più grande.  Questo esempio specifica l'area di ritaglio che mostra una persona da un'immagine che contiene più persone.  Il codice invia l'area di ritaglio e l'URL dell'immagine più grande e restituisce i risultati che includono gli URL di ricerca Bing e gli URL di immagini simili trovate online.

## <a name="prerequisites"></a>Prerequisiti

È necessario [Visual Studio 2017](https://www.visualstudio.com/downloads/) perché questo codice possa essere eseguito su Windows. È possibile usare la versione gratuita Community Edition.

Per questa esercitazione è necessario avviare una sottoscrizione in base al piano tariffario S9, come illustrato in [Prezzi di Servizi cognitivi - API di ricerca Bing](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/search-api/). 

Per avviare una sottoscrizione nel portale di Azure:
1. Nella parte superiore del portale di Azure immettere 'BingSearchV7' nella casella di testo `Search resources, services, and docs`.  
2. Nella sezione Marketplace dell'elenco a discesa selezionare `Bing Search v7`.
3. Immettere `Name` per la nuova risorsa.
4. Selezionare la sottoscrizione `Pay-As-You-Go`.
5. Selezionare il piano tariffario `S9`.
6. Fare clic su `Enable` per iniziare a usare la sottoscrizione.

## <a name="application-dependencies"></a>Dipendenze dell'applicazione
Per configurare un'applicazione console tramite l'SDK di Ricerca Web Bing, passare all'opzione Gestisci pacchetti NuGet in Esplora soluzioni in Visual Studio. Aggiungere il pacchetto Microsoft.Azure.CognitiveServices.Search.VisualSearch.

L'installazione del pacchetto SDK di Ricerca Web NuGet determina anche l'installazione di dipendenze, tra cui:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="image-and-crop-area"></a>Area di ritaglio e immagine
La figura seguente mostra il team di leadership senior di Microsoft.  Usando l'SDK di Ricerca visiva, viene caricata un'area di ritaglio dell'immagine e vengono trovate altre immagini e pagine Web che includono l'entità nell'area selezionata dell'immagine più grande.  In questo caso, l'entità è una persona.

![Team di leadership senior di Microsoft](./media/MS_SrLeaders.jpg)

## <a name="specify-the-crop-area-as-imageinfo-in-visualsearchrequest"></a>Specificare l'area di ritaglio come ImageInfo in VisualSearchRequest
Questo esempio usa un'area di ritaglio dell'immagine precedente che specifica le coordinate superiore a sinistra e inferiore a destra come una percentuale dell'intera immagine.  Il codice seguente crea un oggetto `ImageInfo` dall'area di ritaglio e carica l'oggetto `ImageInfo` in `VisualSearchRequest`.  L'oggetto `ImageInfo` include anche l'URL dell'immagine online.

```
CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
string imageURL = "https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg;
ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);

VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);
```
## <a name="search-for-images-similar-to-crop-area"></a>Ricerca di immagini simili all'area di ritaglio
`VisualSearchRequest` contiene informazioni dell'area di ritaglio relative all'immagine e al relativo URL.  Il metodo `VisualSearchMethodAsync` ottiene i risultati.
```
Console.WriteLine("\r\nSending visual search request with knowledgeRequest that contains URL and crop area");
var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result; 

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Ottenere i dati dell'URL da ImageModuleAction
I risultati di Ricerca visiva sono oggetti `ImageTag`.  Ogni tag contiene un elenco di oggetti `ImageAction`.  Ogni `ImageAction` contiene un campo `Data` che corrisponde a un elenco di valori che dipendono dal tipo di azione:

È possibile ottenere i vari tipi con il codice seguente:
```
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);

```
L'applicazione completa restituisce:

* ActionType: PagesIncluding WebSearchURL:
* ActionType: MoreSizes WebSearchURL:
* ActionType: VisualSearch WebSearchURL:
* ActionType: ImageById WebSearchURL: 
* ActionType: RelatedSearches  WebSearchURL:
* ActionType: Entity -> WebSearchUrl: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=BvvDoRtmZ35Xc_UZE4lZx6_eg7FHgcCkigU1D98NHQo&v=1&r=https%3a%2f%2fwww.bing.com%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5380.1
* ActionType: TopicResults -> WebSearchUrl: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=3QGtxPb3W9LemuHRxAlW4CW7XN4sPkUYCUynxAqI9zQ&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fnadella%2bsatya&p=DevEx,5382.1
* ActionType: ImageResults -> WebSearchUrl: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=l-WNHO89Kkw69AmIGe2MhlUp6MxR6YsJszgOuM5sVLs&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5384.1

Come mostrato nell'elenco precedente `Entity` `ActionType` contiene una query di ricerca Bing che restituisce informazioni relative a una persona, un luogo o un oggetto riconoscibile.  I tipi `TopicResults` e `ImageResults` tipi contengono query per le immagini correlate. Gli URL nell'elenco sono collegati ai risultati della ricerca Bing.


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

## <a name="complete-code"></a>Codice completo

Negli esempi precedenti viene eseguito il codice seguente. Invia un file binario di immagini nel corpo della richiesta POST insieme a un oggetto cropArea e stampa gli URL della ricerca Bing per ogni ActionType. Se ActionType è PagesIncluding, il codice ottiene gli elementi ImageObject nei dati ImageObject.  I dati contengono un elenco di valori, che sono gli URL delle immagini nelle pagine Web.  Copiare e incollare gli URL risultanti da Ricerca visiva nel browser per visualizzare i risultati. Copiare e incollare elementi ContentUrl nel browser per visualizzare le immagini.

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

            VisualSearchImageBinaryWithCropArea(subscriptionKey);

            Console.WriteLine("Any key to quit...");
            Console.ReadKey();

        }

        public static void VisualSearchImageBinaryWithCropArea(string subscriptionKey)
        {
            var client = new VisualSearchAPI(new Microsoft.Azure.CognitiveServices.Search.VisualSearch.ApiKeyServiceClientCredentials(subscriptionKey));

            try
            {
                CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
                
                // The ImageInfo struct specifies the crop area in the image and the URL of the larger image. 
                string imageURL = "https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg";
                ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);
                
                VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);

                var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result; 

                Console.WriteLine("\r\nVisual search request with image from URL and crop area combined in knowledgeRequest");

                if (visualSearchResults == null)
                {
                    Console.WriteLine("No visual search result data.");
                }
                else
                {
                    // List of tags
                    if (visualSearchResults.Tags.Count > 0)
                    {

                        foreach(ImageTag t in visualSearchResults.Tags)
                        {
                            foreach (ImageAction i in t.Actions)
                            { 
                                Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);

                                if (i.ActionType == "PagesIncluding")
                                {
                                    foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
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
[Risposta di Ricerca visiva](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/overview#the-response)