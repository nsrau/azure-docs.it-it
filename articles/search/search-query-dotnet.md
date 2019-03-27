---
title: Eseguire query sui dati in un indice di Ricerca di Azure in C# (.NET SDK) - Ricerca di Azure
description: Esempio di codice C# per compilare una query di ricerca in Azure Search. Aggiungere i parametri di ricerca per filtrare e ordinare i risultati.
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/20/2019
ms.openlocfilehash: 6bb170a5f3353288ab9c393e01b7a0902361913b
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58287010"
---
# <a name="quickstart-3---query-an-azure-search-index-in-c"></a>Avvio rapido: 3 - Eseguire query in un indice di Ricerca di Azure in C#

Questo articolo illustra come eseguire query in un [indice di Ricerca di Azure](search-what-is-an-index.md) usando C# e [.NET SDK](https://aka.ms/search-sdk). Per eseguire ricerche di documenti nell'indice, eseguire le attività seguenti:

> [!div class="checklist"]
> * Creare un oggetto [`SearchIndexClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) da connettere a un indice di ricerca con diritti di sola lettura.
> * Creare un oggetto [`SearchParameters`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters?view=azure-dotnet) contenente la definizione di ricerca o di filtro.
> * Chiamare il metodo `Documents.Search` in `SearchIndexClient` per inviare query a un indice.

## <a name="prerequisites"></a>Prerequisiti

[Caricare un indice di Ricerca di Azure](search-import-data-dotnet.md) con i dati di esempio degli hotel.

Ottenere una chiave di query usata per l'accesso in sola lettura ai documenti. Finora è stata usata una chiave API di amministratore per poter creare oggetti e contenuti in un servizio di ricerca. Ma per il supporto delle query nelle app, è consigliabile usare una chiave di query. Per le istruzioni, vedere [Creare una chiave di query](search-security-api-keys.md#create-query-keys).

## <a name="create-a-client"></a>Creare un client
Creare un'istanza della classe `SearchIndexClient` in modo da fornirle una chiave di query per l'accesso in sola lettura (invece ai diritti di accesso in scrittura conferiti a `SearchServiceClient` nella lezione precedente).

Questa classe ha diversi costruttori. Quello appropriato accetta il nome del servizio di ricerca, il nome dell'indice e un oggetto [`SearchCredentials`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchcredentials?view=azure-dotnet) come parametri. `SearchCredentials` esegue il wrapping della chiave API.

Il codice seguente crea un nuovo oggetto `SearchIndexClient` per l'indice "hotels" usando i valori del nome del servizio di ricerca e della chiave API archiviati nel file di configurazione dell'applicazione, `appsettings.json` nel caso dell'[applicazione di esempio](https://aka.ms/search-dotnet-howto):

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "hotels", new SearchCredentials(queryApiKey));
    return indexClient;
}
```

`SearchIndexClient` include una proprietà [`Documents`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient.documents?view=azure-dotnet). Questa proprietà fornisce tutti i metodi necessari per eseguire query sugli indici di Ricerca di Azure.

## <a name="construct-searchparameters"></a>Creare SearchParameters
Per eseguire una ricerca con .NET SDK è sufficiente chiamare il metodo `Documents.Search` sull'oggetto `SearchIndexClient`. Questo metodo accetta alcuni parametri, incluso il testo di ricerca, nonché un oggetto `SearchParameters` che può essere usato per perfezionare ulteriormente la query.

### <a name="types-of-queries"></a>Tipi di query
I due [tipi di query](search-query-overview.md#types-of-queries) principali che si useranno sono `search` e `filter`. Una query `search` esegue la ricerca di uno o più termini in tutti i campi *ricercabile* nell'indice. Una query `filter` valuta un'espressione booleana su tutti i campi *filtrabili* di un indice. È possibile usare le ricerche e i filtri insieme o separatamente.

Ricerche e filtri vengono eseguiti usando il metodo `Documents.Search` . Una query di ricerca può essere passata nel parametro `searchText`, mentre un'espressione di filtro può essere passata nella proprietà `Filter` della classe `SearchParameters`. Per filtrare senza eseguire ricerche, passare semplicemente `"*"` per il parametro `searchText`. Per eseguire una ricerca senza filtrare, lasciare la proprietà `Filter` non impostata oppure non passare un'istanza di `SearchParameters`.

### <a name="example-queries"></a>Query di esempio
Il codice di esempio seguente mostra alcuni modi per eseguire una query sull'indice "hotels" definito in [Creare un indice di Ricerca di Azure in C#](search-create-index-dotnet.md#DefineIndex). Si noti che i documenti restituiti con i risultati della ricerca sono istanze della classe `Hotel`, che è stata definita in [Importare dati in un indice di Ricerca di Azure in C#](search-import-data-dotnet.md#construct-indexbatch). Il codice di esempio usa un metodo `WriteDocuments` per restituire i risultati di ricerca nella console. Questo metodo è descritto nella sezione successiva.

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

Ecco i risultati per le query nella sezione precedente, supponendo che l'indice "hotels" sia popolato con i dati di esempio:

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

Il codice di esempio precedente usa la console per restituire i risultati della ricerca. Analogamente, sarà necessario visualizzare i risultati della ricerca nella propria applicazione. Per un esempio di visualizzazione dei risultati della ricerca in un'applicazione Web basata su ASP.NET MVC, vedere il [progetto DotNetSample](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetSample) in GitHub.

## <a name="next-steps"></a>Passaggi successivi

Se non è già stato fatto, esaminare il codice di esempio di [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) in GitHub, oltre a leggere l'articolo su [come usare Ricerca di Azure da un'applicazione .NET](search-howto-dotnet-sdk.md) per una descrizione più dettagliata del codice di esempio. 