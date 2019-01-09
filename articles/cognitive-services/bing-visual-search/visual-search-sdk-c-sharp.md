---
title: "Avvio rapido: Ottenere informazioni dettagliate sulle immagini usando l'SDK di Ricerca visiva Bing per C#"
titleSuffix: Azure Cognitive Services
description: Informazioni su come caricare un'immagine usando l'SDK di Ricerca visiva Bing e ottenere informazioni dettagliate su di essa.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: quickstart
ms.date: 05/16/2018
ms.author: v-gedod
ms.openlocfilehash: c589cc14fd429385dd47aad489e827804916d406
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2018
ms.locfileid: "53741219"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-sdk-for-c"></a>Avvio rapido: Ottenere informazioni dettagliate sulle immagini usando l'SDK di Ricerca visiva Bing per C#

Usare questo argomento di avvio rapido per iniziare a ottenere informazioni dettagliate sulle immagini dal servizio Ricerca visiva Bing con l'SDK per C#. Mentre Ricerca visiva Bing ha un'API REST compatibile con la maggior parte dei linguaggi di programmazione, l'SDK fornisce un modo semplice per integrare il servizio nelle applicazioni. Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVisualSearch).

## <a name="prerequisites"></a>Prerequisiti

* Qualsiasi edizione di [Visual Studio 2017](https://www.visualstudio.com/downloads/).
* Se si usa Linux/MacOS, questa applicazione può essere eseguita tramite [Mono](http://www.mono-project.com/).
* Il pacchetto NuGet di Ricerca visiva. 
    - Da Esplora soluzioni in Visual Studio fare clic con il pulsante destro del mouse sul progetto e scegliere `Manage NuGet Packages` dal menu. Installare il pacchetto `Microsoft.Azure.CognitiveServices.Search.CustomSearch`. Installando i pacchetti NuGet viene anche installato quanto segue:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json


[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

<a name="client"></a>

## <a name="create-and-initialize-the-application"></a>Creare e inizializzare l'applicazione

1. In Visual Studio creare un nuovo progetto. Aggiungere quindi le direttive seguenti.
    
    ```csharp
    using Microsoft.Azure.CognitiveServices.Search.VisualSearch;
    using Microsoft.Azure.CognitiveServices.Search.VisualSearch.Models;
    ```

2. Creare un'istanza del client con la propria chiave di sottoscrizione.
    
    ```csharp
    var client = new WebSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```
    
## <a name="send-a-search-request"></a>Inviare una richiesta di ricerca 

1. Creare un `FileStream` alle immagini (in questo caso `TestImages/image.jpg`). Usare quindi il client per inviare una richiesta di ricerca con `client.Images.VisualSearchMethodAsync()`. 
    
    ```csharp
     System.IO.FileStream stream = new FileStream(Path.Combine("TestImages", "image.jpg"), FileMode.Open;
     // The knowledgeRequest parameter is not required if an image binary is passed in the request body
     var visualSearchResults = client.Images.VisualSearchMethodAsync(image: stream, knowledgeRequest: (string)null).Result;
    ```
    
2. Analizzare i risultati della query precedente:

    ```csharp
    // Visual Search results
    if (visualSearchResults.Image?.ImageInsightsToken != null)
    {
        Console.WriteLine($"Uploaded image insights token: {visualSearchResults.Image.ImageInsightsToken}");
    }
    else
    {
        Console.WriteLine("Couldn't find image insights token!");
    }
    
    // List of tags
    if (visualSearchResults.Tags.Count > 0)
    {
        var firstTagResult = visualSearchResults.Tags.First();
        Console.WriteLine($"Visual search tag count: {visualSearchResults.Tags.Count}");
    
        // List of actions in first tag
        if (firstTagResult.Actions.Count > 0)
        {
            var firstActionResult = firstTagResult.Actions.First();
            Console.WriteLine($"First tag action count: {firstTagResult.Actions.Count}");
            Console.WriteLine($"First tag action type: {firstActionResult.ActionType}");
        }
        else
        {
            Console.WriteLine("Couldn't find tag actions!");
        }
    ```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un'app Web a pagina singola](tutorial-bing-visual-search-single-page-app.md)
