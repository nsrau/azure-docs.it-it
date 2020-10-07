---
title: "Guida introduttiva: Chiamare l'endpoint di Ricerca personalizzata Bing usando C# | Microsoft Docs"
titleSuffix: Azure Cognitive Services
description: Usare questo argomento di avvio rapido per iniziare a richiedere risultati della ricerca dall'istanza di Ricerca personalizzata Bing in C#.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: eae9565db5fd88a38343423422cfcc92a3fac33f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "88936810"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-c"></a>Guida introduttiva: Chiamare l'endpoint di Ricerca personalizzata Bing usando C# 

Seguire questo argomento di avvio rapido per informazioni su come richiedere risultati della ricerca all'istanza di Ricerca personalizzata Bing. Anche se l'applicazione è scritta in C#, l'API Ricerca personalizzata Bing è un servizio Web RESTful compatibile con la maggior parte dei linguaggi di programmazione. Il codice sorgente di questo esempio è disponibile in [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingCustomSearchv7.cs).

## <a name="prerequisites"></a>Prerequisiti

- Un'istanza di Ricerca personalizzata Bing. Per altre informazioni, vedere [Avvio rapido: Creare la prima istanza di Ricerca personalizzata Bing](quick-start.md).
- [Microsoft .NET Core](https://www.microsoft.com/net/download/core).
- Qualsiasi edizione di [Visual Studio 2019 o versione successiva](https://www.visualstudio.com/downloads/).
- Se si usa Linux/MacOS, questa applicazione può essere eseguita tramite [Mono](https://www.mono-project.com/).
- Il pacchetto NuGet [Ricerca personalizzata Bing](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/2.0.0). 

   Per installare questo pacchetto in Visual Studio: 
     1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e scegliere **Gestisci pacchetti NuGet**. 
     2. Cercare e selezionare *Microsoft.Azure.CognitiveServices.Search.CustomSearch*, quindi installare il pacchetto.

   Quando si installa il pacchetto NuGet di Ricerca personalizzata Bing, Visual Studio installa anche i pacchetti seguenti:
     - **Microsoft.Rest.ClientRuntime**
     - **Microsoft.Rest.ClientRuntime.Azure**
     - **Newtonsoft.Json**


[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Creare e inizializzare l'applicazione

1. Creare una nuova applicazione console C# in Visual Studio. Aggiungere quindi i pacchetti seguenti al progetto:

    ```csharp
    using System;
    using System.Net.Http;
    using System.Web;
    using Newtonsoft.Json;
    ```

2. Creare le classi seguenti per archiviare i risultati della ricerca restituiti dall'API Ricerca personalizzata Bing:

    ```csharp
    public class BingCustomSearchResponse {        
        public string _type{ get; set; }            
        public WebPages webPages { get; set; }
    }

    public class WebPages {
        public string webSearchUrl { get; set; }
        public int totalEstimatedMatches { get; set; }
        public WebPage[] value { get; set; }        
    }

    public class WebPage {
        public string name { get; set; }
        public string url { get; set; }
        public string displayUrl { get; set; }
        public string snippet { get; set; }
        public DateTime dateLastCrawled { get; set; }
        public string cachedPageUrl { get; set; }
    }
    ```

3. Nel metodo main del progetto creare le variabili seguenti per la chiave di sottoscrizione dell'API Ricerca personalizzata Bing, l'ID configurazione personalizzato dell'istanza di ricerca e un termine di ricerca:

    ```csharp
    var subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    var customConfigId = "YOUR-CUSTOM-CONFIG-ID";
    var searchTerm = args.Length > 0 ? args[0]:"microsoft";
    ```

4. Creare l'URL della richiesta aggiungendo il termine di ricerca al parametro di query `q=` e l'ID configurazione personalizzato dell'istanza di ricerca al parametro `customconfig=`. Separare i parametri con una e commerciale (`&`). Per il valore della variabile `url`, è possibile usare l'endpoint globale nel codice seguente o l'endpoint di [sottodominio personalizzato](../../cognitive-services/cognitive-services-custom-subdomains.md) visualizzato nel portale di Azure per la risorsa.

    ```csharp
    var url = "https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?" +
                "q=" + searchTerm + "&" +
                "customconfig=" + customConfigId;
    ```

## <a name="send-and-receive-a-search-request"></a>Inviare e ricevere una richiesta di ricerca 

1. Creare un client di richiesta e aggiungere la chiave di sottoscrizione all'intestazione `Ocp-Apim-Subscription-Key`.

    ```csharp
    var client = new HttpClient();
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
    ```

2. Eseguire la richiesta di ricerca e ricevere la risposta come oggetto JSON.

    ```csharp
    var httpResponseMessage = client.GetAsync(url).Result;
    var responseContent = httpResponseMessage.Content.ReadAsStringAsync().Result;
    BingCustomSearchResponse response = JsonConvert.DeserializeObject<BingCustomSearchResponse>(responseContent);
    ```
## <a name="process-and-view-the-results"></a>Elaborare e visualizzare i risultati

- Eseguire l'iterazione sull'oggetto risposta per visualizzare informazioni su ogni risultato della ricerca, inclusi nome, URL e data dell'ultima ricerca per indicizzazione della pagina Web.

    ```csharp
    for(int i = 0; i < response.webPages.value.Length; i++) {                
        var webPage = response.webPages.value[i];
        
        Console.WriteLine("name: " + webPage.name);
        Console.WriteLine("url: " + webPage.url);                
        Console.WriteLine("displayUrl: " + webPage.displayUrl);
        Console.WriteLine("snippet: " + webPage.snippet);
        Console.WriteLine("dateLastCrawled: " + webPage.dateLastCrawled);
        Console.WriteLine();
    }
    Console.WriteLine("Press any key to exit...");
    Console.ReadKey();
    ```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Compilare un'app Web di Ricerca personalizzata](./tutorials/custom-search-web-page.md)
