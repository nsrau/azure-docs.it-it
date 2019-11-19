---
title: Usare ricerca cognitiva di Azure in .NET
titleSuffix: Azure Cognitive Search
description: Informazioni su come usare ricerca cognitiva di Azure in un'applicazione .NET C# usando e .NET SDK. Le attività basate su codice includono la connessione al servizio, l'indicizzazione del contenuto e l'esecuzione di query su un indice.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: dotnet
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 54fcd1fb936b5dd41715798408b604106a24bcf9
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112588"
---
# <a name="how-to-use-azure-cognitive-search-from-a-net-application"></a>Come usare ricerca cognitiva di Azure da un'applicazione .NET

Questo articolo è una procedura dettagliata per iniziare a usare [Azure ricerca cognitiva .NET SDK](https://aka.ms/search-sdk). È possibile usare .NET SDK per implementare un'esperienza di ricerca avanzata nell'applicazione usando Azure ricerca cognitiva.

## <a name="whats-in-the-azure-cognitive-search-sdk"></a>Contenuti in Azure ricerca cognitiva SDK
Questo SDK è costituito da alcune librerie client che consentono di gestire indici, origini dati, indicizzatori e mappe di sinonimi, nonché di caricare e gestire documenti ed eseguire query, senza doversi occupare dei dettagli di HTTP e JSON. Tutte queste librerie client vengono distribuite come pacchetti NuGet.

Il pacchetto NuGet principale è `Microsoft.Azure.Search`, ovvero il meta-pacchetto che include tutti gli altri pacchetti come dipendenze. Usare questo pacchetto se si è appena iniziato o se si sa che l'applicazione richiederà tutte le funzionalità di Azure ricerca cognitiva.

Gli altri pacchetti NuGet in SDK sono:
 
  - `Microsoft.Azure.Search.Data`: usare questo pacchetto se si sta sviluppando un'applicazione .NET usando Azure ricerca cognitiva ed è sufficiente eseguire una query o aggiornare i documenti negli indici. Se invece occorre anche creare o aggiornare gli indici, le mappe di sinonimi o altre risorse a livello di servizio, usare il pacchetto `Microsoft.Azure.Search`.
  - `Microsoft.Azure.Search.Service`: usare questo pacchetto se si sviluppa l'automazione in .NET per gestire indici di Azure ricerca cognitiva, mappe sinonimi, indicizzatori, origini dati o altre risorse a livello di servizio. Se invece occorre solo eseguire query o aggiornare i documenti negli indici, usare il pacchetto `Microsoft.Azure.Search.Data`. Se sono necessarie tutte le funzionalità di Azure ricerca cognitiva, usare invece il pacchetto di `Microsoft.Azure.Search`.
  - `Microsoft.Azure.Search.Common`: tipi comuni necessari per le librerie di Azure ricerca cognitiva .NET. Non è necessario usare questo pacchetto direttamente nell'applicazione. È destinato solo a essere utilizzato come dipendenza.

Le diverse librerie client definiscono classi come `Index`, `Field`, e `Document`, nonché operazioni quali `Indexes.Create` e `Documents.Search` sulle classi `SearchServiceClient` e `SearchIndexClient`. Le classi sono organizzate negli spazi dei nomi seguenti:

* [Microsoft.Azure.Search](https://docs.microsoft.com/dotnet/api/microsoft.azure.search)
* [Microsoft.Azure.Search.Models](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models)

Per inviare commenti e suggerimenti per un aggiornamento futuro dell'SDK, vedere la pagina relativa ai [Commenti](https://feedback.azure.com/forums/263029-azure-search/) o creare un problema in [GitHub](https://github.com/azure/azure-sdk-for-net/issues) e menzionare "Azure ricerca cognitiva" nel titolo del problema.

.NET SDK supporta la versione `2019-05-06` dell' [API REST di Azure ricerca cognitiva](https://docs.microsoft.com/rest/api/searchservice/). Questa versione include il supporto per i [tipi complessi](search-howto-complex-data-types.md), l' [arricchimento di intelligenza artificiale](cognitive-search-concept-intro.md), il [completamento automatico](https://docs.microsoft.com/rest/api/searchservice/autocomplete)e la [modalità di analisi JsonLines](search-howto-index-json-blobs.md) durante l'indicizzazione dei BLOB di Azure. 

Questo SDK non supporta [operazioni di gestione](https://docs.microsoft.com/rest/api/searchmanagement/), come la creazione e la scalabilità di servizi di ricerca e la gestione delle chiavi API. Se è necessario gestire le risorse di ricerca da un'applicazione .NET, è possibile usare [Azure ricerca cognitiva .NET Management SDK](https://aka.ms/search-mgmt-sdk).

## <a name="upgrading-to-the-latest-version-of-the-sdk"></a>Aggiornamento alla versione più recente dell'SDK
Se si usa già una versione precedente di Azure ricerca cognitiva .NET SDK e si vuole eseguire l'aggiornamento alla versione più recente disponibile a livello generale, in [questo articolo](search-dotnet-sdk-migration-version-9.md) viene illustrato come.

## <a name="requirements-for-the-sdk"></a>Requisiti per l'SDK
1. Visual Studio 2017 o versione successiva.
2. Il servizio ricerca cognitiva di Azure. Per utilizzare l'SDK, è necessario il nome del servizio e una o più chiavi API. [Creare un servizio nel portale](search-create-service-portal.md) per eseguire facilmente questi passaggi.
3. Scaricare il [pacchetto NuGet](https://www.nuget.org/packages/Microsoft.Azure.Search) di Azure ricerca cognitiva .NET SDK usando "Gestisci pacchetti NuGet" in Visual Studio. Cercare semplicemente il nome del pacchetto `Microsoft.Azure.Search` in NuGet.org (o uno degli altri nomi dei pacchetti riportati sopra se è necessario solo un subset delle funzionalità).

Azure ricerca cognitiva .NET SDK supporta applicazioni destinate a .NET Framework 4.5.2 e versioni successive, oltre a .NET Core 2,0 e versioni successive.

## <a name="core-scenarios"></a>Scenari chiave
Esistono diverse operazioni che è necessario eseguire nell'applicazione di ricerca. In questa esercitazione saranno illustrati questi scenari chiave:

* Creazione di un indice
* Popolamento dell’indice con i documenti
* Ricerca di documenti mediante filtri e ricerca con testo completo

Il codice di esempio seguente illustra ognuno di questi scenari. È possibile utilizzare i frammenti di codice nell'applicazione.

### <a name="overview"></a>Overview
L'applicazione di esempio crea un nuovo indice denominato "hotels", vi inserisce alcuni documenti, quindi esegue alcune query di ricerca. Ecco il programma principale che mostra il flusso generale:

```csharp
// This sample shows how to delete, create, upload documents and query an index
static void Main(string[] args)
{
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();

    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

    string indexName = configuration["SearchIndexName"];

    Console.WriteLine("{0}", "Deleting index...\n");
    DeleteIndexIfExists(indexName, serviceClient);

    Console.WriteLine("{0}", "Creating index...\n");
    CreateIndex(indexName, serviceClient);

    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);

    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(indexClient);

    ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

    RunQueries(indexClientForQueries);

    Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
    Console.ReadKey();
}
```

> [!NOTE]
> Il codice sorgente completo dell'applicazione di esempio utilizzata è disponibile in questa procedura dettagliata su [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).
> 
>

La procedura verrà illustrata passo per passo. In primo luogo è necessario creare un nuovo `SearchServiceClient`. Questo oggetto consente di gestire gli indici. Per costruirne uno, è necessario fornire il nome del servizio ricerca cognitiva di Azure e una chiave API di amministrazione. È possibile immettere queste informazioni nel file `appsettings.json` dell'[applicazione di esempio](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
    return serviceClient;
}
```

> [!NOTE]
> Se si fornisce una chiave non corretta (ad esempio una chiave di query dove era richiesta una chiave di amministrazione), `SearchServiceClient` invierà un `CloudException` con il messaggio di errore "Non consentito" la prima volta che si chiama un metodo di operazione, ad esempio `Indexes.Create`. In questo caso, eseguire una doppia verifica della chiave API.
> 
> 

Le righe successive chiamano i metodi per creare un indice denominato "hotels", eliminandolo prima se esiste già. Tali metodi saranno illustrati più avanti.

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteIndexIfExists(indexName, serviceClient);

Console.WriteLine("{0}", "Creating index...\n");
CreateIndex(indexName, serviceClient);
```

Successivamente, l'indice deve essere popolato. Per popolare l'indice, è necessario un `SearchIndexClient`. Esistono due modi per ottenere uno: creandolo oppure chiamando `Indexes.GetClient` sul `SearchServiceClient`. Per comodità, sarà utilizzato il secondo.

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
```

> [!NOTE]
> In una tipica applicazione di ricerca, la gestione degli indici e il popolamento possono essere gestiti da un componente separato dalle query di ricerca. `Indexes.GetClient` è utile per popolare un indice perché evita di dover fornire `SearchCredentials`aggiuntive. Questa operazione viene eseguita passando la chiave di amministrazione utilizzata per creare il `SearchServiceClient` al nuovo `SearchIndexClient`. Tuttavia, nella parte dell'applicazione che esegue le query, è preferibile creare direttamente il `SearchIndexClient` in modo che sia possibile passare una chiave di query, che consente solo di leggere i dati, anziché una chiave amministratore. Questo procedimento è coerente con il principio del privilegio minimo e consente di rendere più sicura l'applicazione. È possibile trovare ulteriori informazioni su chiavi di amministrazione e chiavi di query [qui](https://docs.microsoft.com/rest/api/searchservice/#authentication-and-authorization).
> 
> 

Ora che è disponibile un `SearchIndexClient`, è possibile popolare l'indice. Il popolamento dell'indice viene eseguito da un altro metodo che verrà esaminato più avanti.

```csharp
Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(indexClient);
```

Infine, saranno eseguite alcune query di ricerca e visualizzati i risultati. In questo caso viene utilizzato un `SearchIndexClient` diverso:

```csharp
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(indexName, configuration);

RunQueries(indexClientForQueries);
```

Esamineremo il metodo `RunQueries` più da vicino in un secondo momento. Ecco il codice per creare il nuovo `SearchIndexClient`:

```csharp
private static SearchIndexClient CreateSearchIndexClient(string indexName, IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, indexName, new SearchCredentials(queryApiKey));
    return indexClient;
}
```

In questo caso è utilizzata una chiave di query poiché non è necessario l'accesso in scrittura all'indice. È possibile immettere queste informazioni nel file `appsettings.json` dell'[applicazione di esempio](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

Se si esegue questa applicazione con un nome di servizio e una chiave API validi, l'output dovrebbe essere simile a questo esempio: (alcuni output della console sono stati sostituiti con "..." a scopo illustrativo.

    Deleting index...

    Creating index...

    Uploading documents...

    Waiting for documents to be indexed...

    Search the entire index for the term 'motel' and return only the HotelName field:

    Name: Secret Point Motel

    Name: Twin Dome Motel


    Apply a filter to the index to find hotels with a room cheaper than $100 per night, and return the hotelId and description:

    HotelId: 1
    Description: The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Times Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.

    HotelId: 2
    Description: The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.


    Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:

    Name: Triple Landscape Hotel
    Last renovated on: 9/20/2015 12:00:00 AM +00:00

    Name: Twin Dome Motel
    Last renovated on: 2/18/1979 12:00:00 AM +00:00


    Search the hotel names for the term 'hotel':

    HotelId: 3
    Name: Triple Landscape Hotel
    ...

    Complete.  Press any key to end application... 

Alla fine di questo articolo viene fornito il codice sorgente completo dell'applicazione.

Successivamente, verrà esaminato ognuno dei metodi chiamati da `Main`.

### <a name="creating-an-index"></a>Creazione di un indice
Dopo la creazione di un `SearchServiceClient`, `Main` Elimina l'indice "Hotels" se esiste già. L'eliminazione viene eseguita con il metodo seguente:

```csharp
private static void DeleteIndexIfExists(string indexName, SearchServiceClient serviceClient)
{
    if (serviceClient.Indexes.Exists(indexName))
    {
        serviceClient.Indexes.Delete(indexName);
    }
}
```

Questo metodo utilizza il `SearchServiceClient` fornito per verificare l'esistenza dell'indice ed eventualmente eliminarlo.

> [!NOTE]
> Il codice di esempio in questo articolo usa i metodi sincroni di Azure ricerca cognitiva .NET SDK per semplicità. È consigliabile utilizzare i metodi asincroni nelle proprie applicazioni per mantenerle scalabili e reattive. Ad esempio, nel metodo precedente è possibile utilizzare `ExistsAsync` e `DeleteAsync` anziché `Exists` e `Delete`.
> 
> 

Successivamente, `Main` crea un nuovo indice "hotel" chiamando questo metodo:

```csharp
private static void CreateIndex(string indexName, SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = indexName,
        Fields = FieldBuilder.BuildForType<Hotel>()
    };
    
    serviceClient.Indexes.Create(definition);
}
```

Questo metodo crea un nuovo oggetto `Index` con un elenco di oggetti `Field` che definisce lo schema del nuovo indice. Ogni campo ha un nome, un tipo di dati e diversi attributi che definiscono il comportamento della ricerca. La classe `FieldBuilder` utilizza la reflection per creare un elenco di oggetti `Field` per l'indice esaminando le proprietà pubbliche e gli attributi della classe modello `Hotel` specificata. Esamineremo la classe `Hotel` più da vicino in un secondo momento.

> [!NOTE]
> Se necessario, è inoltre possibile creare l'elenco di oggetti `Field` direttamente anziché utilizzando `FieldBuilder`. Ad esempio, è possibile evitare di utilizzare una classe modello o potrebbe essere necessario utilizzare una classe modello esistente che non si desidera modificare aggiungendo attributi.
>
> 

Oltre ai campi, è anche possibile aggiungere profili di punteggio, suggerimenti o opzioni CORS all'indice. questi parametri vengono omessi dall'esempio per brevità. È possibile trovare altre informazioni sull'oggetto indice e le relative parti costituenti nella Guida di [riferimento di SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index), oltre che nel [riferimento all'API REST di Azure ricerca cognitiva](https://docs.microsoft.com/rest/api/searchservice/).

### <a name="populating-the-index"></a>Popolamento dell'indice
Il passaggio successivo di `Main` popola l'indice appena creato. Il popolamento dell'indice viene eseguito nel metodo seguente: (parte del codice sostituita con "..." a scopo illustrativo.  Vedere la soluzione di esempio completa per il codice di popolamento completo dei dati.

```csharp
private static void UploadDocuments(ISearchIndexClient indexClient)
{
    var hotels = new Hotel[]
    {
        new Hotel()
        {
            HotelId = "1",
            HotelName = "Secret Point Motel",
            ...
            Address = new Address()
            {
                StreetAddress = "677 5th Ave",
                ...
            },
            Rooms = new Room[]
            {
                new Room()
                {
                    Description = "Budget Room, 1 Queen Bed (Cityside)",
                    ...
                },
                new Room()
                {
                    Description = "Budget Room, 1 King Bed (Mountain View)",
                    ...
                },
                new Room()
                {
                    Description = "Deluxe Room, 2 Double Beds (City View)",
                    ...
                }
            }
        },
        new Hotel()
        {
            HotelId = "2",
            HotelName = "Twin Dome Motel",
            ...
            {
                StreetAddress = "140 University Town Center Dr",
                ...
            },
            Rooms = new Room[]
            {
                new Room()
                {
                    Description = "Suite, 2 Double Beds (Mountain View)",
                    ...
                },
                new Room()
                {
                    Description = "Standard Room, 1 Queen Bed (City View)",
                    ...
                },
                new Room()
                {
                    Description = "Budget Room, 1 King Bed (Waterfront View)",
                    ...
                }
            }
        },
        new Hotel()
        {
            HotelId = "3",
            HotelName = "Triple Landscape Hotel",
            ...
            Address = new Address()
            {
                StreetAddress = "3393 Peachtree Rd",
                ...
            },
            Rooms = new Room[]
            {
                new Room()
                {
                    Description = "Standard Room, 2 Queen Beds (Amenities)",
                    ...
                },
                new Room ()
                {
                    Description = "Standard Room, 2 Double Beds (Waterfront View)",
                    ...
                },
                new Room()
                {
                    Description = "Deluxe Room, 2 Double Beds (Cityside)",
                    ...
                }
            }
        }
    };

    var batch = IndexBatch.Upload(hotels);

    try
    {
        indexClient.Documents.Index(batch);
    }
    catch (IndexBatchException e)
    {
        // Sometimes when your Search service is under load, indexing will fail for some of the documents in
        // the batch. Depending on your application, you can take compensating actions like delaying and
        // retrying. For this simple demo, we just log the failed document keys and continue.
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

    Console.WriteLine("Waiting for documents to be indexed...\n");
    Thread.Sleep(2000);
}
```

Questo metodo è costituito da quattro parti. Il primo crea una matrice di 3 oggetti `Hotel` ognuno con 3 `Room` oggetti che serviranno come dati di input da caricare nell'indice. Questi dati sono hardcoded per motivi di semplicità. Nell'applicazione, i dati probabilmente proverranno da un'origine dati esterna, ad esempio un database SQL.

La seconda parte crea un oggetto `IndexBatch` contenente i documenti. Specificare l'operazione che si vuole applicare al batch al momento della creazione, in questo caso chiamando `IndexBatch.Upload`. Il batch viene quindi caricato nell'indice ricerca cognitiva di Azure dal metodo `Documents.Index`.

> [!NOTE]
> In questo esempio, verranno semplicemente caricati i documenti. Se invece si vogliono unire le modifiche in documenti esistenti o eliminare documenti, creare batch chiamando `IndexBatch.Merge`, `IndexBatch.MergeOrUpload` o `IndexBatch.Delete`. È anche possibile combinare diverse operazioni in un singolo batch chiamando `IndexBatch.New`, che accetta una raccolta di oggetti `IndexAction`, ciascuno dei quali indica ad Azure ricerca cognitiva di eseguire una determinata operazione su un documento. È possibile creare ogni oggetto `IndexAction` con la propria operazione chiamando il metodo corrispondente, ad esempio `IndexAction.Merge`, `IndexAction.Upload` e così via.
> 
> 

La terza parte di questo metodo è un blocco catch che gestisce un caso di errore importante per l'indicizzazione. Se il servizio ricerca cognitiva di Azure non riesce a indicizzare alcuni documenti nel batch, viene generata una `IndexBatchException` da `Documents.Index`. Questa eccezione può verificarsi se si indicizzazione dei documenti quando il servizio è sottoposto a un carico elevato. **Si consiglia di gestire in modo esplicito questo caso nel codice.** È possibile ritardare e quindi ritentare l'indicizzazione di documenti, accedere e continuare come nell'esempio,  oppure eseguire altre attività a seconda dei requisiti di coerenza di dati dell'applicazione.

> [!NOTE]
> È possibile usare il metodo [`FindFailedActionsToRetry`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexbatchexception.findfailedactionstoretry) per creare un nuovo batch contenente solo le azioni non riuscite in una precedente chiamata a `Index`. È disponibile una discussione su come usare questo metodo in modo corretto su [StackOverflow](https://stackoverflow.com/questions/40012885/azure-search-net-sdk-how-to-use-findfailedactionstoretry).
>
>

Infine, il metodo `UploadDocuments` ritarda per due secondi. L'indicizzazione avviene in modo asincrono nel servizio ricerca cognitiva di Azure, quindi l'applicazione di esempio deve attendere un breve periodo di tempo per assicurarsi che i documenti siano disponibili per la ricerca. Ritardi come questi in genere sono necessari solo in applicazioni di esempio, test e demo.

<a name="how-dotnet-handles-documents"></a>

#### <a name="how-the-net-sdk-handles-documents"></a>Modalità di gestione dei documenti in .NET SDK
Ci si potrebbe chiedere come Azure ricerca cognitiva .NET SDK sia in grado di caricare le istanze di una classe definita dall'utente, ad esempio `Hotel` all'indice. è possibile esaminare la classe `Hotel`:

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;
using Newtonsoft.Json;

public partial class Hotel
{
    [System.ComponentModel.DataAnnotations.Key]
    [IsFilterable]
    public string HotelId { get; set; }

    [IsSearchable, IsSortable]
    public string HotelName { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.EnLucene)]
    public string Description { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.FrLucene)]
    [JsonProperty("Description_fr")]
    public string DescriptionFr { get; set; }

    [IsSearchable, IsFilterable, IsSortable, IsFacetable]
    public string Category { get; set; }

    [IsSearchable, IsFilterable, IsFacetable]
    public string[] Tags { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public bool? ParkingIncluded { get; set; }

    // SmokingAllowed reflects whether any room in the hotel allows smoking.
    // The JsonIgnore attribute indicates that a field should not be created 
    // in the index for this property and it will only be used by code in the client.
    [JsonIgnore]
    public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;

    [IsFilterable, IsSortable, IsFacetable]
    public DateTimeOffset? LastRenovationDate { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public double? Rating { get; set; }

    public Address Address { get; set; }

    [IsFilterable, IsSortable]
    public GeographyPoint Location { get; set; }

    public Room[] Rooms { get; set; }
}
```

La prima cosa da notare è che il nome di ogni proprietà pubblica nella classe `Hotel` verrà mappato a un campo con lo stesso nome nella definizione dell'indice. Se si desidera che ogni campo inizi con una lettera minuscola ("Camel maiuscole"), è possibile indicare all'SDK di eseguire automaticamente il mapping dei nomi delle proprietà a Camel-case con l'attributo `[SerializePropertyNamesAsCamelCase]` sulla classe. Questo scenario è comune nelle applicazioni .NET che eseguono l'associazione dati in cui lo schema di destinazione è esterno al controllo dello sviluppatore di applicazioni senza dover violare le linee guida per la denominazione "Pascal case" in .NET.

> [!NOTE]
> Azure ricerca cognitiva .NET SDK usa la libreria [NewtonSoft JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm) per serializzare e deserializzare gli oggetti del modello personalizzato da e verso JSON. Se necessario, è possibile personalizzare questa serializzazione. Per altre informazioni, vedere [serializzazione personalizzata con JSON.NET](#JsonDotNet).
> 
> 

La seconda cosa da notare è che ogni proprietà è decorata con attributi quali `IsFilterable`, `IsSearchable`, `Key`e `Analyzer`. Questi attributi vengono mappati direttamente agli [attributi di campo corrispondenti in un indice di ricerca cognitiva di Azure](https://docs.microsoft.com/rest/api/searchservice/create-index#request). La classe `FieldBuilder` utilizza queste proprietà per costruire definizioni di campo per l'indice.

Il terzo aspetto importante della classe `Hotel` è costituito dai tipi di dati delle proprietà pubbliche. Viene eseguito il mapping dei tipi .NET di queste proprietà ai tipi di campi equivalenti nella definizione dell'indice. Ad esempio, viene eseguito il mapping della proprietà stringa `Category` al campo `category`, che è di tipo `Edm.String`. Esistono mapping di tipi simili tra `bool?`, `Edm.Boolean`, `DateTimeOffset?`e `Edm.DateTimeOffset` e così via. Le regole specifiche per il mapping dei tipi sono documentate con il metodo `Documents.Get` in [Azure ricerca cognitiva .NET SDK Reference](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.get). La classe `FieldBuilder` si occupa di questo mapping automaticamente, ma la comprensione può comunque essere utile nel caso in cui sia necessario risolvere eventuali problemi di serializzazione.

Si è notato che la `SmokingAllowed` proprietà?

```csharp
[JsonIgnore]
public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;
```

L'attributo `JsonIgnore` in questa proprietà indica al `FieldBuilder` di non serializzarlo nell'indice come campo.  Questo è un ottimo modo per creare proprietà calcolate sul lato client che è possibile usare come helper nell'applicazione.  In questo caso, la proprietà `SmokingAllowed` indica se qualsiasi `Room` nella raccolta `Rooms` consente il fumo.  Se tutti sono false, significa che l'intero hotel non consente il fumo.

Alcune proprietà, ad esempio `Address` e `Rooms` sono istanze delle classi .NET.  Queste proprietà rappresentano strutture di dati più complesse e, di conseguenza, richiedono campi con un [tipo di dati complesso](https://docs.microsoft.com/azure/search/search-howto-complex-data-types) nell'indice.

La proprietà `Address` rappresenta un set di più valori nella classe `Address`, definita di seguito:

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Newtonsoft.Json;

namespace AzureSearch.SDKHowTo
{
    public partial class Address
    {
        [IsSearchable]
        public string StreetAddress { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string City { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string StateProvince { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string PostalCode { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string Country { get; set; }
    }
}
```

Questa classe contiene i valori standard utilizzati per descrivere gli indirizzi nell'Stati Uniti o in Canada. È possibile utilizzare tipi simili a questo per raggruppare i campi logici nell'indice.

La proprietà `Rooms` rappresenta una matrice di oggetti `Room`:

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Newtonsoft.Json;

namespace AzureSearch.SDKHowTo
{
    public partial class Room
    {
        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
        public string Description { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.FrMicrosoft)]
        [JsonProperty("Description_fr")]
        public string DescriptionFr { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string Type { get; set; }

        [IsFilterable, IsFacetable]
        public double? BaseRate { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string BedOptions { get; set; }

        [IsFilterable, IsFacetable]
        public int SleepsCount { get; set; }

        [IsFilterable, IsFacetable]
        public bool? SmokingAllowed { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string[] Tags { get; set; }
    }
}
```

Il modello di dati in .NET e lo schema di indice corrispondente devono essere progettati per supportare l'esperienza di ricerca che si vuole assegnare all'utente finale. Ogni oggetto di primo livello in .NET, documento IE nell'indice, corrisponde a un risultato della ricerca che si presenterebbe nell'interfaccia utente. Ad esempio, in un'applicazione di ricerca di un hotel è possibile che gli utenti finali desiderino eseguire la ricerca in base al nome dell'albergo, alle funzionalità dell'albergo o alle caratteristiche di una determinata chat room. Verranno illustrati alcuni esempi di query in un secondo momento.

La possibilità di utilizzare classi personalizzate per interagire con i documenti nell'indice funziona in entrambe le direzioni. È anche possibile recuperare i risultati della ricerca e fare in modo che l'SDK li deserializza automaticamente in un tipo di propria scelta, come si vedrà nella sezione successiva.

> [!NOTE]
> Azure ricerca cognitiva .NET SDK supporta anche documenti con tipizzazione dinamica che usano la classe `Document`, ovvero un mapping chiave/valore dei nomi dei campi ai valori di campo. Ciò è utile negli scenari in cui non si conosce lo schema di indice in fase di progettazione o quando l’associazione a classi di modello specifico non sarebbe conveniente. Tutti i metodi in SDK che gestiscono documenti dispongono di overload che funzionano con la classe `Document` , nonché overload fortemente tipizzati che accettano un parametro di tipo generico. Solo questi ultimi vengono utilizzati nell'esempio di codice fornito in questa esercitazione. La [classe `Document`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.document) eredita da `Dictionary<string, object>`.
> 
>

**Perché usare tipi di dati nullable**

Quando si progettano classi di modelli personalizzate per eseguire il mapping a un indice ricerca cognitiva di Azure, è consigliabile dichiarare le proprietà dei tipi di valore, ad esempio `bool` e `int` ammettono valori null (ad esempio, `bool?` anziché `bool`). Se si usa una proprietà che non ammette i valori Null, è necessario **garantire** che nessun documento nell'indice contenga un valore Null per il campo corrispondente. Né l'SDK né il servizio ricerca cognitiva di Azure consentiranno di applicare questa operazione.

Non è solo un problema ipotetico: si pensi a uno scenario in cui si aggiunge un nuovo campo a un indice esistente di tipo `Edm.Int32`. Dopo l'aggiornamento della definizione dell'indice, tutti i documenti avranno un valore null per il nuovo campo (poiché tutti i tipi ammettono valori null in Azure ricerca cognitiva). Se quindi si usa una classe di modelli con una proprietà `int` che non ammette i valori Null per tale campo, verrà restituita un'eccezione `JsonSerializationException`, come questa, quando si cercherà di recuperare i documenti:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Per questo motivo, è consigliabile usare tipi nullable nelle classi di modelli.

<a name="JsonDotNet"></a>

#### <a name="custom-serialization-with-jsonnet"></a>Serializzazione personalizzata con JSON.NET
L'SDK usa JSON.NET per serializzare e deserializzare i documenti. Se necessario, è possibile personalizzare la serializzazione e la deserializzazione definendo la propria `JsonConverter` o `IContractResolver`. Per ulteriori informazioni, vedere la [documentazione di JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm). Questo può essere utile quando si vuole adattare una classe di modello esistente dall'applicazione per l'uso con ricerca cognitiva di Azure e altri scenari più avanzati. Con la serializzazione personalizzata, ad esempio, è possibile:

* Includere o escludere determinate proprietà della classe modello da archiviare come campi di un documento.
* Eseguire il mapping tra i nomi di proprietà nel codice e i nomi di campo nell'indice.
* Creare gli attributi personalizzati che possono essere utilizzati per il mapping di proprietà ai campi di documento.

È possibile trovare esempi di implementazione della serializzazione personalizzata negli unit test per Azure ricerca cognitiva .NET SDK su GitHub. È consigliabile iniziare da [questa cartella](https://github.com/Azure/azure-sdk-for-net/tree/4f6f4e4c90200c1b0621c4cead302a91e89f2aba/sdk/search/Microsoft.Azure.Search/tests/Tests/Models), che contiene le classi usate dai test di serializzazione personalizzata.

### <a name="searching-for-documents-in-the-index"></a>Ricerca di documenti nell'indice
L'ultimo passaggio nell'applicazione di esempio consiste nel cercare alcuni documenti nell'indice:

```csharp
private static void RunQueries(ISearchIndexClient indexClient)
{
    SearchParameters parameters;
    DocumentSearchResult<Hotel> results;

    Console.WriteLine("Search the entire index for the term 'motel' and return only the HotelName field:\n");

    parameters =
        new SearchParameters()
        {
            Select = new[] { "HotelName" }
        };

    results = indexClient.Documents.Search<Hotel>("motel", parameters);

    WriteDocuments(results);

    Console.Write("Apply a filter to the index to find hotels with a room cheaper than $100 per night, ");
    Console.WriteLine("and return the hotelId and description:\n");

    parameters =
        new SearchParameters()
        {
            Filter = "Rooms/any(r: r/BaseRate lt 100)",
            Select = new[] { "HotelId", "Description" }
        };

    results = indexClient.Documents.Search<Hotel>("*", parameters);

    WriteDocuments(results);

    Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
    Console.Write("in descending order, take the top two results, and show only hotelName and ");
    Console.WriteLine("lastRenovationDate:\n");

    parameters =
        new SearchParameters()
        {
            OrderBy = new[] { "LastRenovationDate desc" },
            Select = new[] { "HotelName", "LastRenovationDate" },
            Top = 2
        };

    results = indexClient.Documents.Search<Hotel>("*", parameters);

    WriteDocuments(results);

    Console.WriteLine("Search the entire index for the term 'hotel':\n");

    parameters = new SearchParameters();
    results = indexClient.Documents.Search<Hotel>("hotel", parameters);

    WriteDocuments(results);
}
```

Ogni volta che viene eseguita una query, questo metodo crea prima un nuovo oggetto `SearchParameters`. Questo oggetto viene utilizzato per specificare opzioni aggiuntive per la query, ad esempio ordinamento, filtro, paging e facet. In questo metodo, impostiamo le proprietà `Filter`, `Select`, `OrderBy` e `Top` per diverse query. Tutte le proprietà `SearchParameters` sono documentate [qui](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters).

Il passaggio successivo consiste nell’esecuzione effettiva della query di ricerca. L'esecuzione della ricerca viene eseguita utilizzando il metodo `Documents.Search`. Per ogni query, è possibile passare il testo di ricerca da utilizzare come stringa (o `"*"` se non è presente il testo di ricerca), oltre ai parametri di ricerca creati in precedenza. Viene inoltre specificato `Hotel` come parametro di tipo per `Documents.Search`, che indica all'SDK di deserializzare i documenti nei risultati della ricerca in oggetti di tipo `Hotel`.

> [!NOTE]
> È possibile trovare ulteriori informazioni sulla sintassi delle espressioni di query di ricerca [qui](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search).
> 
> 

Infine, dopo ogni query questo metodo scorre tutte le corrispondenze nei risultati della ricerca, stampando tutti i documenti nella console:

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

Queste query saranno ora esaminate in maggiore dettaglio. Ecco il codice per eseguire la prima query:

```csharp
parameters =
    new SearchParameters()
    {
        Select = new[] { "HotelName" }
    };

results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

In questo caso, è in corso la ricerca dell'intero indice per la parola "Motel" in qualsiasi campo ricercabile e si vogliono recuperare solo i nomi degli alberghi, come specificato dal parametro `Select`. Ecco i risultati:

    Name: Secret Point Motel

    Name: Twin Dome Motel

La query successiva è un po' più interessante.  Si desidera trovare gli eventuali Alberghi con una stanza con una tariffa notturna inferiore a $100 e restituire solo l'ID e la descrizione dell'hotel:

```csharp
parameters =
    new SearchParameters()
    {
        Filter = "Rooms/any(r: r/BaseRate lt 100)",
        Select = new[] { "HotelId", "Description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

Questa query utilizza un'espressione `$filter` OData, `Rooms/any(r: r/BaseRate lt 100)`, per filtrare i documenti nell'indice. Viene usato l' [operatore Any](https://docs.microsoft.com/azure/search/search-query-odata-collection-operators) per applicare ' BaseRate lt 100' a ogni elemento della raccolta chat room. Per altre informazioni sulla sintassi di OData supportata da Azure ricerca cognitiva, vedere [qui](https://docs.microsoft.com/azure/search/query-odata-filter-orderby-syntax).

Di seguito sono riportati i risultati della query:

    HotelId: 1
    Description: The hotel is ideally located on the main commercial artery of the city in the heart of New York...

    HotelId: 2
    Description: The hotel is situated in a nineteenth century plaza, which has been expanded and renovated to...

Ora, si desidera individuare i due hotel ristrutturati più di recente e visualizzare il nome dell'hotel e la data dell'ultima ristrutturazione. Il codice è il seguente: 

```csharp
parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "LastRenovationDate desc" },
        Select = new[] { "HotelName", "LastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

In questo caso, viene nuovamente utilizzata la sintassi di OData per specificare il parametro `OrderBy` come `lastRenovationDate desc`. È inoltre possibile impostare `Top` su 2 per avere la certezza che la ricerca restituisca solo i 2 documenti principali. Come in precedenza, viene impostato `Select` per specificare quali campi devono essere visualizzati.

Ecco i risultati:

    Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
    Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Infine, si desidera trovare tutti i nomi degli alberghi che corrispondono alla parola "Hotel":

```csharp
parameters = new SearchParameters()
{
    SearchFields = new[] { "HotelName" }
};
results = indexClient.Documents.Search<Hotel>("hotel", parameters);

WriteDocuments(results);
```

Di seguito sono riportati i risultati, che includono tutti i campi, poiché non è stata specificata la proprietà `Select`:

    HotelId: 3
    Name: Triple Landscape Hotel
    ...

Questo passaggio completa l'esercitazione. \* * I passaggi successivi forniscono ulteriori risorse per saperne di più su Azure ricerca cognitiva.

## <a name="next-steps"></a>Passaggi successivi
* Vedere le guide di riferimento di [SDK di .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) e [API REST](https://docs.microsoft.com/rest/api/searchservice/).
* Rivedere le [convenzioni di denominazione](https://docs.microsoft.com/rest/api/searchservice/Naming-rules) per informazioni sulle regole per la denominazione dei vari oggetti.
* Esaminare [i tipi di dati supportati](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types) in Azure ricerca cognitiva.
