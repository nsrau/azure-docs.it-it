---
title: 'Avvio rapido: Cercare immagini - SDK di Ricerca immagini Bing per C#'
description: Usare questa guida introduttiva per effettuare la prima ricerca di immagini tramite l'SDK di Ricerca immagini Bing, che consiste in un wrapper per l'API e contiene le stesse funzionalità. Questa semplice applicazione C# invia una query di ricerca di immagini, analizza la risposta JSON e visualizza l'URL della prima immagine restituita.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 2d81c7b22e5d7cae0beb8a549d4e44f3a1ebc132
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2018
ms.locfileid: "53261246"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-for-c"></a>Avvio rapido: Cercare immagini con l'SDK di Ricerca immagini Bing per C#

Usare questa guida introduttiva per effettuare la prima ricerca di immagini tramite l'SDK di Ricerca immagini Bing, che consiste in un wrapper per l'API e contiene le stesse funzionalità. Questa semplice applicazione C# invia una query di ricerca di immagini, analizza la risposta JSON e visualizza l'URL della prima immagine restituita.

Il codice sorgente per questo esempio è disponibile in [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingImageSearch) con annotazioni aggiuntive e altre informazioni sulla gestione degli errori.

## <a name="prerequisites"></a>Prerequisiti
* Qualsiasi edizione di [Visual Studio 2017](https://visualstudio.microsoft.com/vs/whatsnew/).
* Il [pacchetto NuGet di ricerca cognitiva di immagini](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.ImageSearch/1.2.0).

Per installare l'SDK di Ricerca immagini Bing in Visual Studio, usare l'opzione `Manage NuGet Packages` da Esplora soluzioni in Visual Studio.

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

Vedere anche [Prezzi di Servizi cognitivi - API di ricerca Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="create-and-initialize-the-application"></a>Creare e inizializzare l'applicazione

Creare prima una nuova applicazione console C# in Visual Studio. Aggiungere quindi i pacchetti seguenti al progetto.

```csharp
using System;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Search.ImageSearch;
using Microsoft.Azure.CognitiveServices.Search.ImageSearch.Models;
```

Nel metodo principale del progetto, creare variabili per la chiave di sottoscrizione valida, i risultati immagini che devono essere restituiti da Bing e un termine di ricerca. Creare quindi un'istanza del client di ricerca delle immagini usando la chiave.

```csharp
//IMPORTANT: replace this variable with your Cognitive Services subscription key
string subscriptionKey = "ENTER YOUR KEY HERE";
//stores the image results returned by Bing
Images imageResults = null;
// the image search term to be used in the query
string searchTerm = "canadian rockies";

//initialize the client
//NOTE: If you're using version 1.2.0 or below for the Bing Image Search SDK, 
// use ImageSearchAPI() instead of ImageSearchClient() to initialize your search client.

var client = new ImageSearchClient(new ApiKeyServiceClientCredentials(subscriptionKey));
```

## <a name="send-a-search-query-using-the-client"></a>Inviare una query di ricerca tramite il client

Usare il client per eseguire una ricerca con un testo di query:

```csharp
// make the search request to the Bing Image API, and get the results"
imageResults = client.Images.SearchAsync(query: searchTerm).Result; //search query
```

## <a name="parse-and-view-the-first-image-result"></a>Analizzare e visualizzare la prima immagine dei risultati

Analizzare le immagini restituite nella risposta.
Se la risposta contiene i risultati della ricerca, archiviare il primo risultato e stamparne i dettagli, ad esempio l'URL di anteprima e l'URL originale, insieme al numero totale di immagini restituite.  

```csharp
if (imageResults != null)
{
    //display the details for the first image result.
    var firstImageResult = imageResults.Value.First();
    Console.WriteLine($"\nTotal number of returned images: {imageResults.Value.Count}\n");
    Console.WriteLine($"Copy the following URLs to view these images on your browser.\n");
    Console.WriteLine($"URL to the first image:\n\n {firstImageResult.ContentUrl}\n");
    Console.WriteLine($"Thumbnail URL for the first image:\n\n {firstImageResult.ThumbnailUrl}");
    Console.ReadKey();
}
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione sull'app a singola pagina di Ricerca immagini Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>Vedere anche 

* [Informazioni su Ricerca immagini Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Provare una demo interattiva online](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Ottenere gratuitamente una chiave di accesso per Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Esempi .NET per l'SDK di Servizi cognitivi di Azure](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
* [Documentazione di Servizi cognitivi di Azure](https://docs.microsoft.com/azure/cognitive-services)
* [Informazioni di riferimento per l'API Ricerca immagini Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
