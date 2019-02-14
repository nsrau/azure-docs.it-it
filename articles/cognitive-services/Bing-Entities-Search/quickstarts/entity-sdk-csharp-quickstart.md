---
title: "Avvio rapido: Cercare entità con l'SDK di Ricerca entità Bing per C#"
titleSuffix: Azure Cognitive Services
description: Usare questa guida introduttiva per cercare entità con l'SDK di Ricerca entità Bing per C#.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: v-gedod
ms.openlocfilehash: 6a365ef5421de3ceb31c5cc78a424f786f174ab3
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55861919"
---
# <a name="send-a-search-request-with-the-bing-entity-search-sdk-for-c"></a>Inviare una richiesta di ricerca con l'SDK di Ricerca entità Bing per C#

Usare questa guida introduttiva per iniziare a cercare entità con l'SDK di Ricerca entità Bing per C#. Mentre Ricerca entità Bing ha un'API REST compatibile con la maggior parte dei linguaggi di programmazione, l'SDK offre un modo semplice per integrare il servizio nelle applicazioni. Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingEntitySearch).


## <a name="prerequisites"></a>Prerequisiti

* Qualsiasi edizione di [Visual Studio 2017](https://www.visualstudio.com/downloads/).
* Il framework [Json.NET](https://www.newtonsoft.com/json), disponibile come pacchetto NuGet.
* Se si usa Linux/MacOS, questa applicazione può essere eseguita tramite [Mono](http://www.mono-project.com/).
* Pacchetto [NuGet SDK di Ricerca notizie Bing](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.EntitySearch/1.2.0). Installando questo pacchetto vengono anche installati gli elementi seguenti:
    * Microsoft.Rest.ClientRuntime
    * Microsoft.Rest.ClientRuntime.Azure
    * Newtonsoft.Json

Per aggiungere l'SDK di Ricerca entità Bing a un progetto di Visual Studio, usare l'opzione `Manage NuGet Packages` in Esplora soluzioni e aggiungere il pacchetto `Microsoft.Azure.CognitiveServices.Search.EntitySearch`.


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-an-application"></a>Creare e inizializzare un'applicazione

1. creare una nuova soluzione di console C# in Visual Studio. Aggiungere quindi quanto segue nel file di codice principale.

    ```csharp
    using System;
    using System.Linq;
    using System.Text;
    using Microsoft.Azure.CognitiveServices.Search.EntitySearch;
    using Microsoft.Azure.CognitiveServices.Search.EntitySearch.Models;
    using Newtonsoft.Json;
    ```

## <a name="create-a-client-and-send-a-search-request"></a>Creare un client e inviare una richiesta di ricerca

2. Creare un nuovo client di ricerca. Aggiungere la chiave di sottoscrizione creando un nuovo oggetto `ApiKeyServiceClientCredentials`.

    ```csharp
    var client = new EntitySearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```

3. Usare la funzione `Entities.Search()` del client per la ricerca della query:
    
    ```csharp
    var entityData = client.Entities.Search(query: "Satya Nadella");
    ```

## <a name="get-and-print-an-entity-description"></a>Recuperare e stampare la descrizione di un'entità

1. Se l'API ha restituito risultati della ricerca, recuperare l'entità principale da `entityData`.

    ```csharp
    var mainEntity = entityData.Entities.Value.Where(thing => thing.EntityPresentationInfo.EntityScenario == EntityScenario.DominantEntity).FirstOrDefault();
    ```

2. Stampare la descrizione dell'entità principale. 

    ```csharp
    Console.WriteLine(mainEntity.Description);
    ```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare app Web a pagina singola](../tutorial-bing-entities-search-single-page-app.md)

* [Informazioni sull'API Ricerca entità Bing](../overview.md )