---
title: 'Avvio rapido: Libreria client di Ricerca personalizzata Bing per C#'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.author: aahi
ms.openlocfilehash: ba80d1396b30b61bdfe4c121220429f5a7d994b0
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "79486034"
---
Introduzione alla libreria client di Ricerca personalizzata Bing per C#. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base. L'API Ricerca personalizzata Bing permette di creare esperienze di ricerca personalizzate senza annunci per specifici argomenti di interesse. Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingCustomWebSearch).

Usare la libreria client di Ricerca personalizzata Bing per C# per:
* Trovare i risultati della ricerca sul Web dall'istanza di Ricerca personalizzata Bing.

[Documentazione di riferimento](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingcustomsearch?view=azure-dotnet) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingCustomSearch) | [Pacchetto (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) | [Esempi](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)


## <a name="prerequisites"></a>Prerequisiti

- Un'istanza di Ricerca personalizzata Bing. Vedere [Avvio rapido: Creare la prima istanza di Ricerca personalizzata Bing](../../quick-start.md) per altre informazioni.
- Microsoft [.NET Core](https://www.microsoft.com/net/download/core)
- Qualsiasi edizione di [Visual Studio 2017 o versioni successive](https://www.visualstudio.com/downloads/)
- Se si usa Linux/MacOS, questa applicazione può essere eseguita tramite [Mono](https://www.mono-project.com/).
- Il pacchetto NuGet [Ricerca personalizzata Bing](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0). 
    - Da **Esplora soluzioni** in Visual Studio fare clic con il pulsante destro del mouse sul progetto e scegliere **Gestisci pacchetti NuGet** dal menu. Installare il pacchetto `Microsoft.Azure.CognitiveServices.Search.CustomSearch`. L'installazione del pacchetto Ricerca personalizzata NuGet comporta anche l'installazione degli assembly seguenti:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Creare e inizializzare l'applicazione

1. Creare una nuova applicazione console C# in Visual Studio. Aggiungere quindi i pacchetti seguenti al progetto.

    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.CustomSearch;
    ```

2. Nel metodo main dell'applicazione creare un'istanza del client di ricerca con la chiave API.

    ```csharp
    var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-SUBSCRIPTION-KEY"));
    ```

## <a name="send-the-search-request-and-receive-a-response"></a>Inviare la richiesta di ricerca e ricevere una risposta
    
1. Inviare una query di ricerca usando il metodo `SearchAsync()` del client e salvare la risposta. Assicurarsi di sostituire `YOUR-CUSTOM-CONFIG-ID` con l'ID di configurazione dell'istanza. L'ID è disponibile nel [portale Ricerca personalizzata Bing](https://www.customsearch.ai/). Questo esempio cerca il termine "Xbox".

    ```csharp
    // This will look up a single query (Xbox).
    var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
    ```

2. Il metodo `SearchAsync()` restituisce un oggetto `WebData`. Usare l'oggetto per eseguire l'iterazione di tutti gli oggetti `WebPages` trovati. Questo codice consente di trovare la prima pagina Web risultante e di stamparne `Name` e `URL`.

    ```csharp
    if (webData?.WebPages?.Value?.Count > 0)
    {
        // find the first web page
        var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

        if (firstWebPagesResult != null)
        {
            Console.WriteLine("Number of webpage results {0}", webData.WebPages.Value.Count);
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

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Compilare un'app Web di Ricerca personalizzata](../../tutorials/custom-search-web-page.md)
