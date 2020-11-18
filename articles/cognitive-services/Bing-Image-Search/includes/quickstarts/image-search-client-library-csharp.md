---
title: Avvio rapido della libreria client C# di Ricerca immagini Bing
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/21/2020
ms.author: aahi
ms.openlocfilehash: 247aea49d60c2d953d8b6dff37d22188a5442a1f
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94625523"
---
Usare questa guida di avvio rapido per effettuare la prima ricerca di immagini tramite la libreria client di Ricerca immagini Bing. 

La libreria di ricerca client è un wrapper per l'API REST e contiene le stesse funzionalità. 

Creare un'applicazione C# che invia una query di ricerca di immagini, analizza la risposta JSON e visualizza l'URL della prima immagine restituita.

Il codice sorgente per questo esempio è disponibile in [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingImageSearch) con annotazioni aggiuntive e altre informazioni sulla gestione degli errori.

## <a name="prerequisites"></a>Prerequisiti

* Se si usa Windows, qualsiasi edizione di [Visual Studio 2017 o versione successiva](https://visualstudio.microsoft.com/vs/whatsnew/)
* Se si usa macOS o Linux, [VS Code](https://code.visualstudio.com) con [.NET Core installato](https://dotnet.microsoft.com/learn/dotnet/hello-world-tutorial/install)
* [Una sottoscrizione gratuita di Azure](https://azure.microsoft.com/free/dotnet)

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](~/includes/cognitive-services-bing-image-search-signup-requirements.md)]

Vedere anche [Prezzi di Servizi cognitivi - API di ricerca Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="create-a-console-project"></a>Creare un nuovo progetto console

Prima di tutto, creare una nuova applicazione console C#.

## <a name="create-a-console-project"></a>Creare un nuovo progetto console

# <a name="visual-studio"></a>[Visual Studio](#tab/visualstudio)

1. Creare una nuova soluzione console denominata *BingImageSearch* in Visual Studio.
    
1. Aggiungere il [pacchetto NuGet di ricerca cognitiva di immagini](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.ImageSearch)
    1. Fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni**.
    1. Scegliere **Gestisci pacchetti NuGet**.
    1. Cercare e selezionare *Microsoft.Azure.CognitiveServices.Search.ImageSearch*, quindi installare il pacchetto.
    
# <a name="vs-code"></a>[Visual Studio Code](#tab/vscode)

1. Aprire la finestra del terminale in VS Code.
1. Creare un nuovo progetto console denominato *BingImageSearch* immettendo il codice seguente nella finestra del terminale:
    
    ```bash
    dotnet new console -n BingImageSearch
    ```
1. Aprire la cartella *BingImageSearch* in VS Code.
1. Aggiungere il pacchetto NuGet [pacchetto NuGet di ricerca cognitiva di immagini](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.ImageSearch) immettendo il codice seguente nella finestra del terminale:

    ```bash
    dotnet add package Microsoft.Azure.CognitiveServices.Search.ImageSearch
    ```

---

## <a name="initialize-the-application"></a>Inizializzare l'applicazione


1. Sostituire tutte le istruzioni `using` in *Program.cs* con il codice seguente:

    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.ImageSearch;
    using Microsoft.Azure.CognitiveServices.Search.ImageSearch.Models;
    ```

1. Nel metodo `Main` del progetto, creare variabili per la chiave di sottoscrizione valida, i risultati immagini che devono essere restituiti da Bing e un termine di ricerca. Creare quindi un'istanza del client di ricerca delle immagini usando la chiave.

    ```csharp
    static async Task Main(string[] args)
    {
        //IMPORTANT: replace this variable with your Cognitive Services subscription key
        string subscriptionKey = "ENTER YOUR KEY HERE";
        //stores the image results returned by Bing
        Images imageResults = null;
        // the image search term to be used in the query
        string searchTerm = "canadian rockies";
        
        //initialize the client
        //NOTE: If you're using version 1.2.0 or below for the Bing Image Search client library, 
        // use ImageSearchAPI() instead of ImageSearchClient() to initialize your search client.
        
        var client = new ImageSearchClient(new ApiKeyServiceClientCredentials(subscriptionKey));
    }
    ```
    
## <a name="send-a-search-query-using-the-client"></a>Inviare una query di ricerca tramite il client
    
Sempre con il metodo `Main`, usare il client per eseguire una ricerca con il testo della query:
    
```csharp
// make the search request to the Bing Image API, and get the results"
imageResults = await client.Images.SearchAsync(query: searchTerm).Result; //search query
```

## <a name="parse-and-view-the-first-image-result"></a>Analizzare e visualizzare la prima immagine dei risultati

Analizzare le immagini restituite nella risposta. 

Se la risposta contiene risultati della ricerca, archiviare il primo risultato e stamparne alcuni dettagli.

```csharp
if (imageResults != null)
{
    //display the details for the first image result.
    var firstImageResult = imageResults.Value.First();
    Console.WriteLine($"\nTotal number of returned images: {imageResults.Value.Count}\n");
    Console.WriteLine($"Copy the following URLs to view these images on your browser.\n");
    Console.WriteLine($"URL to the first image:\n\n {firstImageResult.ContentUrl}\n");
    Console.WriteLine($"Thumbnail URL for the first image:\n\n {firstImageResult.ThumbnailUrl}");
    Console.WriteLine("Press any key to exit ...");
    Console.ReadKey();
}
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione sull'app a singola pagina di Ricerca immagini Bing](../../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Vedere anche

* [Informazioni su Ricerca immagini Bing](../../overview.md)  
* [Provare una demo interattiva online](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Esempi .NET per l'SDK di Servizi cognitivi di Azure](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
* [Documentazione di Servizi cognitivi di Azure](../../../index.yml)
* [Informazioni di riferimento per l'API Ricerca immagini Bing](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)