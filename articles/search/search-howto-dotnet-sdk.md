---
title: Come usare Ricerca di Azure da un'applicazione .NET | Documentazione Microsoft
description: Come utilizzare Ricerca di Azure da un'applicazione .NET
services: search
documentationcenter: 
author: brjohnstmsft
manager: jlembicz
editor: 
ms.assetid: 93653341-c05f-4cfd-be45-bb877f964fcb
ms.service: search
ms.devlang: dotnet
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/22/2017
ms.author: brjohnst
ms.openlocfilehash: 7273ae6a698f2af52e78ea2aae9ca5cd80f6a2b1
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2017
---
# <a name="how-to-use-azure-search-from-a-net-application"></a>Come utilizzare Ricerca di Azure da un'applicazione .NET
In questo articolo è descritta una procedura dettagliata per eseguire [.NET SDK di Ricerca di Azure](https://aka.ms/search-sdk). È possibile utilizzare .NET SDK per implementare un'esperienza di ricerca completa nell'applicazione tramite Ricerca di Azure.

## <a name="whats-in-the-azure-search-sdk"></a>Novità dell'SDK di Ricerca di Azure
Questo SDK è costituito da una libreria client, `Microsoft.Azure.Search`. Consente di gestire indici, origini dati e indicizzatori, nonché di caricare e gestire documenti ed eseguire query, senza la necessità di affrontare i dettagli di HTTP e JSON.

La libreria client definisce classi come `Index`, `Field`, e `Document`, nonché operazioni quali `Indexes.Create` e `Documents.Search` sulle classi `SearchServiceClient` e `SearchIndexClient`. Le classi sono organizzate negli spazi dei nomi seguenti:

* [Microsoft.Azure.Search](https://docs.microsoft.com/dotnet/api/microsoft.azure.search)
* [Microsoft.Azure.Search.Models](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models)

La versione corrente di Azure Search .NET SDK è ora in disponibilità generale. Per fornire a Microsoft commenti e suggerimenti da incorporare nella prima versione successiva, visitare la [pagina dei commenti](https://feedback.azure.com/forums/263029-azure-search/).

.NET SDK supporta la versione `2016-09-01` dell'[API REST di Ricerca di Azure](https://docs.microsoft.com/rest/api/searchservice/). Questa versione include ora il supporto per gli analizzatori personalizzati, BLOB di Azure e l'Indicizzatore di tabelle di Azure. Le funzionalità di anteprima che *non* sono incluse in questa versione, ad esempio il supporto per l'indicizzazione di file JSON e CSV, sono disponibili in [anteprima](search-api-2016-09-01-preview.md) e tramite la [versione 4.0.1-preview di .NET SDK](https://aka.ms/search-sdk-preview).

Questo SDK non supporta [operazioni di gestione](https://docs.microsoft.com/rest/api/searchmanagement/), come la creazione e la scalabilità di servizi di ricerca e la gestione delle chiavi API. Se è necessario gestire le risorse di ricerca da un'applicazione .NET, è possibile utilizzare l'[SDK di gestione .NET di Ricerca di Azure](https://aka.ms/search-mgmt-sdk).

## <a name="upgrading-to-the-latest-version-of-the-sdk"></a>Aggiornamento alla versione più recente dell'SDK
Se si usa già una versione precedente di Azure Search .NET SDK e si vuole eseguire l'aggiornamento alla nuova versione in disponibilità generale, seguire le indicazioni in [questo articolo](search-dotnet-sdk-migration.md) .

## <a name="requirements-for-the-sdk"></a>Requisiti per l'SDK
1. Visual Studio 2017.
2. Un servizio di Ricerca di Azure. Per utilizzare l'SDK, è necessario il nome del servizio e una o più chiavi API. [Creare un servizio nel portale](search-create-service-portal.md) per eseguire facilmente questi passaggi.
3. Scaricare il [pacchetto NuGet](http://www.nuget.org/packages/Microsoft.Azure.Search) di SDK .NET di Ricerca di Azure tramite "Gestisci pacchetti NuGet" in Visual Studio. Cercare il nome del pacchetto `Microsoft.Azure.Search` su NuGet.org.

Azure Search .NET SDK supporta applicazioni destinate a .NET Framework 4.6 e .NET Core.

## <a name="core-scenarios"></a>Scenari chiave
Esistono diverse operazioni che è necessario eseguire nell'applicazione di ricerca. In questa esercitazione saranno illustrati questi scenari chiave:

* Creazione di un indice
* Popolamento dell’indice con i documenti
* Ricerca di documenti mediante filtri e ricerca con testo completo

Il codice di esempio che segue illustra ciascuna di queste operazioni. È possibile utilizzare i frammenti di codice nell'applicazione.

### <a name="overview"></a>Overview
L'applicazione di esempio crea un nuovo indice denominato "hotels", vi inserisce alcuni documenti, quindi esegue alcune query di ricerca. Ecco il programma principale che mostra il flusso generale:

```csharp
// This sample shows how to delete, create, upload documents and query an index
static void Main(string[] args)
{
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();

    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

    Console.WriteLine("{0}", "Deleting index...\n");
    DeleteHotelsIndexIfExists(serviceClient);

    Console.WriteLine("{0}", "Creating index...\n");
    CreateHotelsIndex(serviceClient);

    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

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

La procedura verrà illustrata passo per passo. In primo luogo è necessario creare un nuovo `SearchServiceClient`. Questo oggetto consente di gestire gli indici. Per creare uno, è necessario fornire il nome del servizio Ricerca di Azure, nonché una chiave API di amministrazione. È possibile immettere queste informazioni nel file `appsettings.json` dell'[applicazione di esempio](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

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
DeleteHotelsIndexIfExists(serviceClient);

Console.WriteLine("{0}", "Creating index...\n");
CreateHotelsIndex(serviceClient);
```

Successivamente, l'indice deve essere popolato. A tale scopo, è necessario un `SearchIndexClient`. Esistono due modi per ottenere uno: creandolo oppure chiamando `Indexes.GetClient` sul `SearchServiceClient`. Per comodità, sarà utilizzato il secondo.

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

> [!NOTE]
> In un'applicazione di ricerca tipica, il popolamento e la gestione degli indici viene gestita da un componente separato dalle query di ricerca. `Indexes.GetClient` è utile per il popolamento di un indice perché evita di specificare un altro `SearchCredentials`. Questa operazione viene eseguita passando la chiave di amministrazione utilizzata per creare il `SearchServiceClient` al nuovo `SearchIndexClient`. Tuttavia, nella parte dell'applicazione che esegue le query, è preferibile creare il `SearchIndexClient` direttamente in modo che sia possibile passare una chiave di query anziché una chiave di amministrazione. Questo procedimento è coerente con il principio del privilegio minimo e consente di rendere più sicura l'applicazione. È possibile trovare ulteriori informazioni su chiavi di amministrazione e chiavi di query [qui](https://docs.microsoft.com/rest/api/searchservice/#authentication-and-authorization).
> 
> 

Ora che è disponibile un `SearchIndexClient`, è possibile popolare l'indice. A tal fine verrà utilizzato un altro metodo che verrà descritto più avanti.

```csharp
Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(indexClient);
```

Infine, saranno eseguite alcune query di ricerca e visualizzati i risultati. In questo caso viene utilizzato un `SearchIndexClient` diverso:

```csharp
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

RunQueries(indexClientForQueries);
```

Esamineremo il metodo `RunQueries` più da vicino in un secondo momento. Ecco il codice per creare il nuovo `SearchIndexClient`:

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "hotels", new SearchCredentials(queryApiKey));
    return indexClient;
}
```

In questo caso è utilizzata una chiave di query poiché non è necessario l'accesso in scrittura all'indice. È possibile immettere queste informazioni nel file `appsettings.json` dell'[applicazione di esempio](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

Se si esegue questa applicazione con un nome di servizio valido e le chiavi API, l'output dovrebbe essere simile al seguente:

    Deleting index...
    
    Creating index...
    
    Uploading documents...
    
    Waiting for documents to be indexed...
    
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
    
    Complete.  Press any key to end application...

Alla fine di questo articolo viene fornito il codice sorgente completo dell'applicazione.

Successivamente, verrà esaminato ognuno dei metodi chiamati da `Main`.

### <a name="creating-an-index"></a>Creazione di un indice
Dopo aver creato un `SearchServiceClient`, l'operazione successiva `Main` è l’eliminazione dell’indice "hotel" se esiste già. Questa operazione viene eseguita con il metodo seguente:

```csharp
private static void DeleteHotelsIndexIfExists(SearchServiceClient serviceClient)
{
    if (serviceClient.Indexes.Exists("hotels"))
    {
        serviceClient.Indexes.Delete("hotels");
    }
}
```

Questo metodo utilizza il `SearchServiceClient` fornito per verificare l'esistenza dell'indice ed eventualmente eliminarlo.

> [!NOTE]
> Il codice di esempio riportato in questo articolo utilizza i metodi sincroni di .NET SDK di Ricerca di Azure per motivi di semplicità. È consigliabile utilizzare i metodi asincroni nelle proprie applicazioni per mantenerle scalabili e reattive. Ad esempio, nel metodo precedente è possibile utilizzare `ExistsAsync` e `DeleteAsync` anziché `Exists` e `Delete`.
> 
> 

Successivamente, `Main` crea un nuovo indice "hotel" chiamando questo metodo:

```csharp
private static void CreateHotelsIndex(SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = "hotels",
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

Oltre ai campi, è possibile aggiungere anche profili di punteggio, suggerimenti di alternative o opzioni CORS per l'indice. Per motivi di brevità nell’esempio questi elementi sono stati omessi. Altre informazioni sull'oggetto indice e le relative parti costituenti sono disponibili nella [guida di riferimento dell'SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index#microsoft_azure_search_models_index) e nella [guida di riferimento dell'interfaccia API REST di Ricerca di Azure](https://docs.microsoft.com/rest/api/searchservice/).

### <a name="populating-the-index"></a>Popolamento dell'indice
Il passaggio successivo in `Main` è il popolamento dell'indice appena creato. Questa operazione viene eseguita nel metodo seguente:

```csharp
private static void UploadDocuments(ISearchIndexClient indexClient)
{
    var hotels = new Hotel[]
    {
        new Hotel()
        { 
            HotelId = "1", 
            BaseRate = 199.0, 
            Description = "Best hotel in town",
            DescriptionFr = "Meilleur hôtel en ville",
            HotelName = "Fancy Stay",
            Category = "Luxury", 
            Tags = new[] { "pool", "view", "wifi", "concierge" },
            ParkingIncluded = false, 
            SmokingAllowed = false,
            LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero), 
            Rating = 5, 
            Location = GeographyPoint.Create(47.678581, -122.131577)
        },
        new Hotel()
        { 
            HotelId = "2", 
            BaseRate = 79.99,
            Description = "Cheapest hotel in town",
            DescriptionFr = "Hôtel le moins cher en ville",
            HotelName = "Roach Motel",
            Category = "Budget",
            Tags = new[] { "motel", "budget" },
            ParkingIncluded = true,
            SmokingAllowed = true,
            LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
            Rating = 1,
            Location = GeographyPoint.Create(49.678581, -122.131577)
        },
        new Hotel() 
        { 
            HotelId = "3", 
            BaseRate = 129.99,
            Description = "Close to town hall and the river"
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

Questo metodo è costituito da quattro parti. La prima crea una matrice di oggetti `Hotel` che verranno utilizzati come dati di input per caricare l'indice. Questi dati sono hardcoded per motivi di semplicità. Nell'applicazione, i dati probabilmente proverranno da un'origine dati esterna, ad esempio un database SQL.

La seconda parte crea un oggetto `IndexBatch` contenente i documenti. Specificare l'operazione che si vuole applicare al batch al momento della creazione, in questo caso chiamando `IndexBatch.Upload`. Il batch viene quindi caricato nell'indice di Ricerca di Azure dal metodo `Documents.Index` .

> [!NOTE]
> In questo esempio, verranno semplicemente caricati i documenti. Se invece si vogliono unire le modifiche in documenti esistenti o eliminare documenti, creare batch chiamando `IndexBatch.Merge`, `IndexBatch.MergeOrUpload` o `IndexBatch.Delete`. È inoltre possibile combinare diverse operazioni in un singolo batch chiamando `IndexBatch.New`, che accetta una raccolta di oggetti `IndexAction`, ognuno dei quali indica a Ricerca di Azure di eseguire una particolare operazione su un documento. È possibile creare ogni oggetto `IndexAction` con la propria operazione chiamando il metodo corrispondente, ad esempio `IndexAction.Merge`, `IndexAction.Upload` e così via.
> 
> 

La terza parte di questo metodo è un blocco catch che gestisce un caso di errore importante per l'indicizzazione. Se il servizio Ricerca di Azure non riesce a indicizzare alcuni dei documenti nel batch, viene generato un `IndexBatchException` da `Documents.Index`. Questa situazione può verificarsi se l'indicizzazione dei documenti avviene mentre il servizio è sovraccarico. **Si consiglia di gestire in modo esplicito questo caso nel codice.** È possibile ritardare e quindi ritentare l'indicizzazione di documenti, accedere e continuare come nell'esempio,  oppure eseguire altre attività a seconda dei requisiti di coerenza di dati dell'applicazione.

> [!NOTE]
> È possibile utilizzare il metodo `FindFailedActionsToRetry` per costruire un nuovo batch contenente solo le azioni non riuscite in una precedente chiamata a `Index`. Il metodo è documentato [qui](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexbatchexception#Microsoft_Azure_Search_IndexBatchException_FindFailedActionsToRetry_Microsoft_Azure_Search_Models_IndexBatch_System_String_) ed è una discussione su come utilizzarlo in modo corretto [su StackOverflow](http://stackoverflow.com/questions/40012885/azure-search-net-sdk-how-to-use-findfailedactionstoretry).
>
>

Infine, il metodo `UploadDocuments` ritarda per due secondi. L'indicizzazione avviene in modo asincrono nel servizio Ricerca di Azure, pertanto l'applicazione di esempio deve attendere un breve periodo per garantire che i documenti siano disponibili per la ricerca. Ritardi come questi in genere sono necessari solo in applicazioni di esempio, test e demo.

#### <a name="how-the-net-sdk-handles-documents"></a>Modalità di gestione dei documenti in .NET SDK
Per capire in che modo .NET SDK di Ricerca di Azure è in grado di caricare le istanze di una classe definita dall'utente come `Hotel` nell'indice, è possibile esaminare la classe `Hotel`:

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;
using Newtonsoft.Json;

// The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Search .NET SDK.
// It ensures that Pascal-case property names in the model class are mapped to camel-case
// field names in the index.
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    [System.ComponentModel.DataAnnotations.Key]
    [IsFilterable]
    public string HotelId { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }

    [IsSearchable]
    public string Description { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.FrLucene)]
    [JsonProperty("description_fr")]
    public string DescriptionFr { get; set; }

    [IsSearchable, IsFilterable, IsSortable]
    public string HotelName { get; set; }

    [IsSearchable, IsFilterable, IsSortable, IsFacetable]
    public string Category { get; set; }

    [IsSearchable, IsFilterable, IsFacetable]
    public string[] Tags { get; set; }

    [IsFilterable, IsFacetable]
    public bool? ParkingIncluded { get; set; }

    [IsFilterable, IsFacetable]
    public bool? SmokingAllowed { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public DateTimeOffset? LastRenovationDate { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public int? Rating { get; set; }

    [IsFilterable, IsSortable]
    public GeographyPoint Location { get; set; }
}
```

La prima cosa da notare è che ogni proprietà pubblica di `Hotel` corrisponde a un campo nella definizione dell'indice, ma con una differenza fondamentale: il nome di ogni campo inizia con una lettera minuscola ("convenzione camel"), mentre il nome di ogni proprietà pubblica di `Hotel` inizia con una lettera maiuscola ("convenzione Pascal"). Si tratta di uno scenario comune in applicazioni .NET che consentono di eseguire l'associazione dati in cui lo schema di destinazione è fuori dal controllo dello sviluppatore dell'applicazione. Anziché dover violare linee guida sulla denominazione di .NET seguendo la convenzione camel per i nomi delle proprietà, è possibile indicare a SDK di eseguire automaticamente il mapping dei nomi delle proprietà alla convenzione camel con l’attributo `[SerializePropertyNamesAsCamelCase]` .

> [!NOTE]
> Azure Search .NET SDK usa la libreria [NewtonSoft JSON.NET](http://www.newtonsoft.com/json/help/html/Introduction.htm) per serializzare e deserializzare gli oggetti modello personalizzati in e da JSON. Se necessario, è possibile personalizzare questa serializzazione. Per altre informazioni, vedere [Serializzazione personalizzata con JSON.NET](#JsonDotNet).
> 
> 

La seconda cosa da notare sono gli attributi, ad esempio `IsFilterable`, `IsSearchable`, `Key` e `Analyzer`, che decorano ogni proprietà pubblica. Questi attributi eseguono il mapping direttamente agli [attributi corrispondenti dell'indice di Ricerca di Azure](https://docs.microsoft.com/rest/api/searchservice/create-index#request). La classe `FieldBuilder` li utilizza per creare definizioni di campo per l'indice.

Il terzo aspetto importante della classe `Hotel` sono i tipi di dati delle proprietà pubbliche. I tipi .NET di queste proprietà eseguono il mapping ai tipi di campi equivalenti nella definizione dell'indice. Ad esempio, viene eseguito il mapping della proprietà stringa `Category` al campo `category`, che è di tipo `Edm.String`. Esistono mapping di tipi simili tra `bool?` e `Edm.Boolean`, `DateTimeOffset?` e `Edm.DateTimeOffset` ecc. Le regole specifiche per il mapping dei tipi sono documentate con il metodo `Documents.Get` nella [documentazione di riferimento relativa a .NET SDK di Ricerca di Azure](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations#Microsoft_Azure_Search_IDocumentsOperations_GetWithHttpMessagesAsync__1_System_String_System_Collections_Generic_IEnumerable_System_String__Microsoft_Azure_Search_Models_SearchRequestOptions_System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_). La classe `FieldBuilder` si occupa di questo mapping automaticamente, ma la comprensione può comunque essere utile nel caso in cui sia necessario risolvere eventuali problemi di serializzazione.

La possibilità di utilizzare le proprie classi come documenti funziona in entrambe le direzioni; è possibile, inoltre, recuperare i risultati della ricerca e far sì che SDK li deserializzi automaticamente in un tipo di propria scelta, come sarà illustrato nella prossima sezione.

> [!NOTE]
> .NET SDK di Ricerca di Azure supporta anche documenti tipizzati in modo dinamico utilizzando la classe `Document`, un mapping chiave/valore dei campi e valori dei campi. Ciò è utile negli scenari in cui non si conosce lo schema di indice in fase di progettazione o quando l’associazione a classi di modello specifico non sarebbe conveniente. Tutti i metodi in SDK che gestiscono documenti dispongono di overload che funzionano con la classe `Document` , nonché overload fortemente tipizzati che accettano un parametro di tipo generico. Solo questi ultimi vengono utilizzati nell'esempio di codice fornito in questa esercitazione. La classe `Document` eredita da `Dictionary<string, object>`. Per informazioni più dettagliate, vedere [qui](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.document#microsoft_azure_search_models_document).
> 
> 

**Perché usare tipi di dati nullable**

Quando si progettano classi di modello personalizzate per eseguire il mapping a un indice di Ricerca di Azure, è consigliabile dichiarare le proprietà dei tipi di valore, ad esempio `bool` e `int` da rendere nullable (ad esempio, `bool?` invece di `bool`). Se si usa una proprietà che non ammette i valori Null, è necessario **garantire** che nessun documento nell'indice contenga un valore Null per il campo corrispondente. Né l'SDK né il servizio di Ricerca di Azure consentiranno di applicare questo valore.

Non è solo un problema ipotetico: si pensi a uno scenario in cui si aggiunge un nuovo campo a un indice esistente di tipo `Edm.Int32`. Dopo l'aggiornamento della definizione dell'indice, tutti i documenti avranno un valore Null per il nuovo campo (perché tutti i tipi sono nullable in Ricerca di Azure). Se quindi si usa una classe di modelli con una proprietà `int` che non ammette i valori Null per tale campo, verrà restituita un'eccezione `JsonSerializationException`, come questa, quando si cercherà di recuperare i documenti:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Per questo motivo, è consigliabile usare tipi nullable nelle classi di modelli.

<a name="JsonDotNet"></a>

#### <a name="custom-serialization-with-jsonnet"></a>Serializzazione personalizzata con JSON.NET
L'SDK usa JSON.NET per serializzare e deserializzare i documenti. Se necessario, è possibile personalizzare la serializzazione e deserializzazione definendo il proprio `JsonConverter` o `IContractResolver` (vedere la [ documentazione di JSON.NET](http://www.newtonsoft.com/json/help/html/Introduction.htm) per altri dettagli). Ciò può risultare utile quando si vuole adattare una classe modello esistente dell'applicazione per usarla con Ricerca di Azure e in altri scenari più avanzati. Con la serializzazione personalizzata, ad esempio, è possibile:

* Includere o escludere determinate proprietà della classe modello da archiviare come campi di un documento.
* Eseguire il mapping tra i nomi di proprietà nel codice e i nomi di campo nell'indice.
* Creare gli attributi personalizzati che possono essere utilizzati per il mapping di proprietà ai campi di documento.

Esempi di implementazione della serializzazione personalizzata sono disponibili negli unit test per Azure Search .NET SDK in GitHub. È consigliabile iniziare da [questa cartella](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Search/Search.Tests/Tests/Models), che contiene le classi usate dai test di serializzazione personalizzata.

### <a name="searching-for-documents-in-the-index"></a>Ricerca di documenti nell'indice
L'ultimo passaggio nell'applicazione di esempio è cercare alcuni documenti nell'indice. A tale scopo viene utilizzato il metodo seguente:

```csharp
private static void RunQueries(ISearchIndexClient indexClient)
{
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
}
```

Ogni volta che viene eseguita una query, questo metodo crea prima un nuovo oggetto `SearchParameters`. Consente di specificare opzioni aggiuntive per la query di ordinamento, filtro, impaginazione e faceting. In questo metodo, impostiamo le proprietà `Filter`, `Select`, `OrderBy` e `Top` per diverse query. Tutte le proprietà `SearchParameters` sono documentate [qui](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters).

Il passaggio successivo consiste nell’esecuzione effettiva della query di ricerca. Questa operazione viene effettuata con il metodo `Documents.Search` . Per ogni query, è possibile passare il testo di ricerca da utilizzare come stringa (o `"*"` se non è presente il testo di ricerca), oltre ai parametri di ricerca creati in precedenza. Viene inoltre specificato `Hotel` come parametro di tipo per `Documents.Search`, che indica all'SDK di deserializzare i documenti nei risultati della ricerca in oggetti di tipo `Hotel`.

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
        Select = new[] { "hotelName" }
    };

results = indexClient.Documents.Search<Hotel>("budget", parameters);

WriteDocuments(results);
```

In questo caso, viene eseguita la ricerca degli hotel che corrispondono alla parola "budget". Verranno visualizzati solo i nomi degli hotel, come specificato dal parametro `Select`. Ecco i risultati:

    Name: Roach Motel

Poi, si desidera trovare gli hotel con una tariffa notturna inferiore a 150 dollari e visualizzare solo l'ID dell'hotel e la descrizione:

```csharp
parameters =
    new SearchParameters()
    {
        Filter = "baseRate lt 150",
        Select = new[] { "hotelId", "description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

Questa query utilizza un'espressione `$filter` OData, `baseRate lt 150`, per filtrare i documenti nell'indice. È possibile trovare informazioni sulla sintassi OData supportata da Ricerca di Azure [qui](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search).

Di seguito sono riportati i risultati della query:

    ID: 2   Description: Cheapest hotel in town
    ID: 3   Description: Close to town hall and the river

Ora, si desidera individuare i due hotel ristrutturati più di recente e visualizzare il nome dell'hotel e la data dell'ultima ristrutturazione. Il codice è il seguente: 

```csharp
parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "lastRenovationDate desc" },
        Select = new[] { "hotelName", "lastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

In questo caso, viene nuovamente utilizzata la sintassi di OData per specificare il parametro `OrderBy` come `lastRenovationDate desc`. È inoltre possibile impostare `Top` su 2 per avere la certezza che la ricerca restituisca solo i 2 documenti principali. Come in precedenza, viene impostato `Select` per specificare quali campi devono essere visualizzati.

Ecco i risultati:

    Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
    Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Si desidera infine trovare tutti gli hotel che corrispondono alla parola "motel":

```csharp
parameters = new SearchParameters();
results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

Di seguito sono riportati i risultati, che includono tutti i campi, poiché non è stata specificata la proprietà `Select`:

    ID: 2   Base rate: 79.99        Description: Cheapest hotel in town     Description (French): Hôtel le moins cher en ville      Name: Roach Motel       Category: Budget        Tags: [motel, budget]   Parking included: yes   Smoking allowed: yes    Last renovated on: 4/28/1982 12:00:00 AM +00:00 Rating: 1/5     Location: Latitude 49.678581, longitude -122.131577

Questo passaggio completa l'esercitazione. **Passaggi successivi** vengono fornite risorse aggiuntive per ottenere ulteriori informazioni su Ricerca di Azure.

## <a name="next-steps"></a>Passaggi successivi
* Vedere le guide di riferimento di [SDK di .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) e [API REST](https://docs.microsoft.com/rest/api/searchservice/).
* Approfondire l'argomento tramite [video e altri esempi ed esercitazioni](search-video-demo-tutorial-list.md).
* Rivedere le [convenzioni di denominazione](https://docs.microsoft.com/rest/api/searchservice/Naming-rules) per informazioni sulle regole per la denominazione dei vari oggetti.
* Rivedere i [tipi di dati supportati](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types) in Ricerca di Azure.
