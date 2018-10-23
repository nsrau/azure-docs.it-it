---
title: 'Guida introduttiva: Custom Search SDK, C#'
titleSuffix: Azure Cognitive Services
description: Configurazione dell'SDK di Ricerca personalizzata per l'applicazione console in C#.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: quickstart
ms.date: 09/06/2018
ms.author: scottwhi
ms.openlocfilehash: 6dc10bc2dedfe99573b5ad646461e66827c6df9e
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2018
ms.locfileid: "49320170"
---
# <a name="quickstart-using-the-bing-custom-search-sdk-with-c"></a>Guida introduttiva: Uso di Bing Custom Search SDK con C#

L'SDK di Ricerca personalizzata Bing offre un modello di programmazione più semplice rispetto all'API REST di Ricerca personalizzata Bing. Questa sezione illustra in dettaglio la creazione delle prime chiamate di Ricerca personalizzata tramite C# SDK.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione introduttiva, sono necessari gli elementi seguenti:

- Un'istanza di Ricerca personalizzata pronta per l'uso. Vedere [Creare la prima istanza di Ricerca personalizzata Bing](quick-start.md).  
  
- Una chiave di sottoscrizione. È possibile ottenere una chiave di sottoscrizione quando si attiva la [versione di prova gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search). In alternativa è possibile usare una chiave di sottoscrizione a pagamento dal dashboard di Azure (vedere [Account delle API di Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)).  
  
- Visual Studio 2017 installato. Se non è stato ancora installato, è possibile scaricare **gratuitamente** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/).  
  
- Il pacchetto [Ricerca personalizzata NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) installato. Da Esplora soluzioni in Visual Studio fare clic con il pulsante destro del mouse sul progetto e scegliere `Manage NuGet Packages` dal menu. Installare il pacchetto `Microsoft.Azure.CognitiveServices.Search.CustomSearch`.

L'installazione del pacchetto Ricerca personalizzata NuGet comporta anche l'installazione degli assembly seguenti:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json



## <a name="run-the-code"></a>Eseguire il codice

> [!TIP]
> Ottenere la versione più recente del codice come soluzione di Visual Studio da [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingCustomWebSearch).

Per eseguire l'esempio, seguire questa procedura:

1. Aprire Visual Studio 2017.
  
2. Fare clic sul menu **File**, su **Nuovo**, **Progetto**e quindi sul modello **Visual C# Console Application** (Applicazione console Visual C#).
  
3. Assegnare alla soluzione il nome CustomSearchSolution e passare alla cartella in cui inserire la soluzione stessa.
  
4. Fare clic su OK per creare la soluzione.  
  
4. Da Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto (che ha lo stesso nome della soluzione) e selezionare `Manage NuGet Packages`. In Gestione pacchetti NuGet fare clic su **Sfoglia**. Nella casella di ricerca immettere Microsoft.Azure.CognitiveServices.Search.CustomSearch e premere INVIO. Selezionare il pacchetto e fare clic su Installa.  
  
4. Copiare il codice seguente nel file Program.cs. Sostituire **YOUR-SUBSCRIPTION-KEY** e **YOUR-CUSTOM-CONFIG-ID** con la chiave di sottoscrizione e l'ID di configurazione.  
  
    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.CustomSearch;

    namespace CustomSrchSDK
    {
        class Program
        {
            static void Main(string[] args)
            {

                var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-SUBSCRIPTION-KEY"));

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
                }
                catch (Exception ex)
                {
                    Console.WriteLine("Encountered exception. " + ex.Message);
                }

                Console.WriteLine("Press any key to exit...");
                Console.ReadKey();
            }

        }
    }
    ```  
  
5. Compilare la soluzione ed eseguirla (o eseguirne il debug). 




## <a name="breaking-it-down"></a>Suddivisione

Dopo aver installato il pacchetto Ricerca personalizzata NuGet, è necessario aggiungere una direttiva using per tale pacchetto.

```csharp
using Microsoft.Azure.CognitiveServices.Search.CustomSearch;
```

Creare quindi un'istanza del client di ricerca personalizzata, da usare per creare richieste di ricerca. Assicurarsi di sostituire `YOUR-SUBSCRIPTION-KEY` con la propria chiave.

```csharp
var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-CUSTOM-SEARCH-KEY"));
```

È ora possibile usare il client per inviare una richiesta di ricerca. Assicurarsi di sostituire `YOUR-CUSTOM-CONFIG-ID` con l'ID di configurazione dell'istanza. L'ID è reperibile nel [portale Ricerca personalizzata](https://www.customsearch.ai/). Questo esempio cerca Xbox. Aggiornarlo in base alle proprie esigenze.

```csharp
var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
```

Il metodo `SearchAsync` restituisce un oggetto `WebData`. Usare `WebData` per eseguire l'iterazione attraverso gli oggetti `WebPages` trovati. Questo codice consente di trovare la prima pagina Web risultante e di stamparne `Name` e `URL`.

```csharp
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


## <a name="next-steps"></a>Passaggi successivi

Vedere gli esempi dell'SDK. Ogni esempio include un file Leggimi con i dettagli sui prerequisiti, sull'installazione e sull'esecuzione dell'esempio stesso.

* Introduzione agli [esempi .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7) 
    * [Pacchetto NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0)
    * Vedere anche le [librerie .NET](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Search/BingCustomSearch) per le definizioni e le dipendenze.
* Introduzione agli [esempi NodeJS](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples) 
    * Vedere anche le [librerie NodeJS](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/customSearch) per le definizioni e le dipendenze.
* Introduzione agli [esempi Java](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples) 
    * Vedere anche [Librerie Java](https://github.com/Azure/azure-sdk-for-java/tree/master/cognitiveservices/azure-customsearch) per le definizioni e le dipendenze.
* Introduzione agli [esempi Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples) 
    * Vedere anche [Librerie Python](https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-search-customsearch) per le definizioni e le dipendenze.

