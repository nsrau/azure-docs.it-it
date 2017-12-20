---
title: 'Eseguire query su un indice: API .NET e Ricerca di Azure | Microsoft Docs'
description: Compilare una query di ricerca in Ricerca di Azure e usare i parametri di ricerca per filtrare e ordinare i risultati della ricerca.
services: search
manager: jhubbard
documentationcenter: 
author: brjohnstmsft
ms.assetid: 12c3efba-ea99-4187-9d2d-f63b5ec7040d
ms.service: search
ms.devlang: dotnet
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 05/19/2017
ms.author: brjohnst
ms.openlocfilehash: 52bd0fd4cf70401dcf881c7f28d5cd91397bb059
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2017
---
# <a name="query-your-azure-search-index-using-the-net-sdk"></a>Eseguire query su un indice di Ricerca di Azure con .NET SDK
> [!div class="op_single_selector"]
> * [Panoramica](search-query-overview.md)
> * [Portale](search-explorer.md)
> * [.NET](search-query-dotnet.md)
> * [REST](search-query-rest-api.md)
> 
> 

Questo articolo illustra come eseguire query su un indice con [Azure Search .NET SDK](https://aka.ms/search-sdk).

Prima di iniziare questa procedura dettagliata, è necessario avere [creato un indice di Ricerca di Azure](search-what-is-an-index.md) e [averlo popolato con dati](search-what-is-data-import.md).

> [!NOTE]
> Tutto il codice di esempio in questo articolo è scritto in C#. Il codice sorgente completo è disponibile su [GitHub](http://aka.ms/search-dotnet-howto). Per una descrizione più dettagliata del codice di esempio, vedere le informazioni relative a [Azure Search .NET SDK](search-howto-dotnet-sdk.md).

## <a name="identify-your-azure-search-services-query-api-key"></a>Identificare la chiave API di query del servizio Ricerca di Azure
Dopo avere creato un indice di Ricerca di Azure, si è quasi pronti per eseguire query con .NET SDK. Prima di tutto è necessario ottenere una delle chiavi API di query generate per il servizio di ricerca di cui è stato effettuato il provisioning. .NET SDK invierà questa chiave API a ogni richiesta al servizio. La presenza di una chiave valida stabilisce una relazione di trust, in base alle singole richieste, tra l'applicazione che invia la richiesta e il servizio che la gestisce.

1. Per trovare le chiavi API del servizio, è possibile accedere al [portale di Azure](https://portal.azure.com/)
2. Passare al pannello del servizio Ricerca di Azure.
3. Fare clic sull'icona "Chiavi".

Il servizio avrà *chiavi amministratore* e *chiavi di query*.

* Le *chiavi amministratore* primarie e secondarie concedono diritti completi a tutte le operazioni, inclusa la possibilità di gestire il servizio, creare ed eliminare indici, indicizzatori e origini dati. Sono disponibili due chiavi, quindi è possibile continuare a usare la chiave secondaria se si decide di rigenerare la chiave primaria e viceversa.
* Le *chiavi di query* concedono l'accesso in sola lettura agli indici e ai documenti e vengono in genere distribuite alle applicazioni client che inviano richieste di ricerca.

Ai fini di una query su un indice, è possibile usare una delle chiavi di query. Si possono anche usare le chiavi amministratore per le query, ma è necessario usare una chiave di query nel codice dell'applicazione, perché questo approccio è più coerente con il [principio del privilegio minimo](https://en.wikipedia.org/wiki/Principle_of_least_privilege).

## <a name="create-an-instance-of-the-searchindexclient-class"></a>Creare un'istanza della classe SearchIndexClient
Per eseguire query con Azure Search .NET SDK, è necessario creare un'istanza della classe `SearchIndexClient` . Questa classe ha diversi costruttori. Quello appropriato accetta il nome del servizio di ricerca, il nome dell'indice e un oggetto `SearchCredentials` come parametri. `SearchCredentials` esegue il wrapping della chiave API.

Il codice seguente crea un nuovo oggetto `SearchIndexClient` per l'indice "hotels" creato in [Creare un indice di Ricerca di Azure con .NET SDK](search-create-index-dotnet.md), usando i valori archiviati nel file di configurazione dell'applicazione, `appsettings.json` nel caso dell'[applicazione di esempio](http://aka.ms/search-dotnet-howto), per il nome del servizio di ricerca e la chiave API:

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "hotels", new SearchCredentials(queryApiKey));
    return indexClient;
}
```

`SearchIndexClient` include una proprietà `Documents`. Questa proprietà fornisce tutti i metodi necessari per eseguire query sugli indici di Ricerca di Azure.

## <a name="query-your-index"></a>Eseguire query sull'indice
Per eseguire una ricerca con .NET SDK è sufficiente chiamare il metodo `Documents.Search` sull'oggetto `SearchIndexClient`. Questo metodo accetta alcuni parametri, incluso il testo di ricerca, nonché un oggetto `SearchParameters` che può essere usato per perfezionare ulteriormente la query.

#### <a name="types-of-queries"></a>Tipi di query
I due [tipi di query](search-query-overview.md#types-of-queries) principali che si useranno sono `search` e `filter`. Una query `search` esegue la ricerca di uno o più termini in tutti i campi *ricercabile* nell'indice. Una query `filter` valuta un'espressione booleana su tutti i campi *filtrabili* di un indice.

Ricerche e filtri vengono eseguiti usando il metodo `Documents.Search` . Una query di ricerca può essere passata nel parametro `searchText`, mentre un'espressione di filtro può essere passata nella proprietà `Filter` della classe `SearchParameters`. Per filtrare senza eseguire ricerche, passare semplicemente `"*"` per il parametro `searchText`. Per eseguire una ricerca senza filtrare, lasciare la proprietà `Filter` non impostata oppure non passare un'istanza di `SearchParameters`.

#### <a name="example-queries"></a>Query di esempio
Il codice di esempio seguente mostra alcuni modi per eseguire una query sull'indice "hotels" definito in [Creare un indice di Ricerca di Azure con .NET SDK](search-create-index-dotnet.md#DefineIndex). Si noti che i documenti restituiti con i risultati della ricerca sono istanze della classe `Hotel` , che è stata definita in [Importazione di dati in Ricerca di Azure tramite .NET SDK](search-import-data-dotnet.md#HotelClass). Il codice di esempio usa un metodo `WriteDocuments` per restituire i risultati di ricerca nella console. Questo metodo è descritto nella sezione successiva.

```csharp
SearchParameters parameters;
DocumentSearchResult<Hotel> results;

Console.WriteLine("Search the entire index for the term 'budget' and return only the hotelName field:\n");

parameters =
    new SearchParameters()
    {
        Select = new[] { "hotelName" }
    };

results = indexClient.Documents.Search<Hotel>("budget", parameters);

WriteDocuments(results);

Console.Write("Apply a filter to the index to find hotels cheaper than $150 per night, ");
Console.WriteLine("and return the hotelId and description:\n");

parameters =
    new SearchParameters()
    {
        Filter = "baseRate lt 150",
        Select = new[] { "hotelId", "description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
Console.Write("in descending order, take the top two results, and show only hotelName and ");
Console.WriteLine("lastRenovationDate:\n");

parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "lastRenovationDate desc" },
        Select = new[] { "hotelName", "lastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'motel':\n");

parameters = new SearchParameters();
results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

## <a name="handle-search-results"></a>Gestire i risultati della ricerca
Il metodo `Documents.Search` restituisce un oggetto `DocumentSearchResult` che contiene i risultati della query. Nell'esempio nella sezione precedente viene usato un metodo denominato `WriteDocuments` per restituire i risultati di ricerca nella console:

```csharp
private static void WriteDocuments(DocumentSearchResult<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.Results)
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

Ecco i risultati per le query nella sezione precedente, supponendo che l'indice "hotels" sia popolato con i dati di esempio in [Importazione di dati in Ricerca di Azure tramite .NET SDK](search-import-data-dotnet.md):

```
Search the entire index for the term 'budget' and return only the hotelName field:

Name: Roach Motel

Apply a filter to the index to find hotels cheaper than $150 per night, and return the hotelId and description:

ID: 2   Description: Cheapest hotel in town
ID: 3   Description: Close to town hall and the river

Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:

Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Search the entire index for the term 'motel':

ID: 2   Base rate: 79.99        Description: Cheapest hotel in town     Description (French): Hôtel le moins cher en ville      Name: Roach Motel       Category: Budget        Tags: [motel, budget]   Parking included: yes   Smoking allowed: yes    Last renovated on: 4/28/1982 12:00:00 AM +00:00 Rating: 1/5     Location: Latitude 49.678581, longitude -122.131577
```

Il codice di esempio precedente usa la console per restituire i risultati della ricerca. Analogamente, sarà necessario visualizzare i risultati della ricerca nella propria applicazione. Per un esempio di rendering dei risultati di ricerca in un'applicazione Web basata su ASP.NET MVC, vedere [questo esempio su GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetSample) .

