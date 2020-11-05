---
title: Usare Azure.Search.Documents (V11) in .NET
titleSuffix: Azure Cognitive Search
description: Informazioni su come creare e gestire gli oggetti di ricerca in un'applicazione .NET usando C# e la libreria client Azure.Search.Documents (V11). I frammenti di codice illustrano la connessione al servizio, la creazione di indici e query.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: dotnet
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/27/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 4b3256591c0aa2536fd42bcdbb2ef339fc1d5c48
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93356808"
---
# <a name="how-to-use-azuresearchdocuments-in-a-c-net-application"></a>Come usare Azure.Search.Documents in un'applicazione .NET C#

Questo articolo illustra come creare e gestire gli oggetti di ricerca usando C# e la libreria client [**Azure.Search.Documents**](/dotnet/api/overview/azure/search) (versione 11).

## <a name="about-version-11"></a>Informazioni sulla versione 11

Azure SDK per .NET aggiunge un nuovo [**Azure.Search.Doc**](/dotnet/api/overview/azure/search) libreria client uments dal team di Azure SDK, che è funzionalmente equivalente a [Microsoft. Azure. search](/dotnet/api/overview/azure/search/client10) client libraries, ma usa approcci e convenzioni comuni laddove applicabile. Alcuni esempi includono [`AzureKeyCredential`](/dotnet/api/azure.azurekeycredential) l'autenticazione della chiave e la [System.Text.Js. Serializzazione](/dotnet/api/system.text.json.serialization) per la serializzazione JSON.

Come per le versioni precedenti, è possibile usare questa libreria per:

+ Creazione e gestione di indici di ricerca, origini dati, indicizzatori, skillsets e mappe sinonime
+ Caricare e gestire i documenti di ricerca in un indice
+ Eseguire query, tutto senza dover gestire i dettagli di HTTP e JSON

La libreria viene distribuita come una singola [Azure.Search.Docpacchetto NuGet ument](https://www.nuget.org/packages/Azure.Search.Documents/), che include tutte le API usate per l'accesso a livello di codice a un servizio di ricerca.

La libreria client definisce classi come `SearchIndex`, `SearchField`, e `SearchDocument`, nonché operazioni quali `SearchIndexClient.CreateIndex` e `SearchClient.Search` sulle classi `SearchIndexClient` e `SearchClient`. Le classi sono organizzate negli spazi dei nomi seguenti:

+ [`Azure.Search.Documents`](/dotnet/api/azure.search.documents)
+ [`Azure.Search.Documents.Indexes`](/dotnet/api/azure.search.documents.indexes)
+ [`Azure.Search.Documents.Indexes.Models`](/dotnet/api/azure.search.documents.indexes.models)
+ [`Azure.Search.Documents.Models`](/dotnet/api/azure.search.documents.models)

Azure.Search.Documents (versione 11) ha come destinazione [ `2020-06-30` la versione dell'API REST di Azure ricerca cognitiva](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/data-plane/Azure.Search/preview/2020-06-30). 

La libreria client non fornisce [operazioni di gestione del servizio](/rest/api/searchmanagement/), ad esempio la creazione e la scalabilità dei servizi di ricerca e la gestione delle chiavi API. Se è necessario gestire le risorse di ricerca da un'applicazione .NET, usare la libreria [Microsoft. Azure. Management. search](/dotnet/api/overview/azure/search/management) in Azure SDK per .NET.

## <a name="upgrade-to-v11"></a>Eseguire l'aggiornamento a V11

Se si usa la versione precedente di .NET SDK e si vuole eseguire l'aggiornamento alla versione corrente disponibile a livello generale, vedere eseguire l' [aggiornamento ad Azure ricerca cognitiva .NET SDK versione 11](search-dotnet-sdk-migration-version-11.md)

## <a name="sdk-requirements"></a>Requisiti di SDK

+ Visual Studio 2019 o versione successiva.

+ Il servizio ricerca cognitiva di Azure. Per utilizzare l'SDK, è necessario il nome del servizio e una o più chiavi API. [Creare un servizio nel portale](search-create-service-portal.md) , se non è già presente.

+ Scaricare il [pacchetto diAzure.Search.Documents](https://www.nuget.org/packages/Azure.Search.Documents) usando **strumenti**  >  **Gestione pacchetti NuGet**  >  **Gestisci pacchetti NuGet per la soluzione** in Visual Studio. Cercare il nome del pacchetto `Azure.Search.Documents` .

Azure SDK per .NET è conforme a [.NET Standard 2,0](/dotnet/standard/net-standard#net-implementation-support), ovvero .NET Framework 4.6.1 e .net core 2,0 come requisiti minimi.

## <a name="example-application"></a>Applicazione di esempio

Questo articolo "insegna da esempio", che si basa sull'esempio di codice [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) su GitHub per illustrare i concetti fondamentali in Azure ricerca cognitiva, in particolare come creare, caricare ed eseguire query su un indice di ricerca.

Nella parte restante di questo articolo, si presuppone un nuovo indice denominato "Hotels", popolato con pochi documenti, con diverse query che corrispondono ai risultati.

Di seguito è riportato il programma principale, che mostra il flusso generale:

```csharp
// This sample shows how to delete, create, upload documents and query an index
static void Main(string[] args)
{
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();

    SearchIndexClient indexClient = CreateSearchIndexClient(configuration);

    string indexName = configuration["SearchIndexName"];

    Console.WriteLine("{0}", "Deleting index...\n");
    DeleteIndexIfExists(indexName, indexClient);

    Console.WriteLine("{0}", "Creating index...\n");
    CreateIndex(indexName, indexClient);

    SearchClient searchClient = indexClient.GetSearchClient(indexName);

    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(searchClient);

    SearchClient indexClientForQueries = CreateSearchClientForQueries(indexName, configuration);

    Console.WriteLine("{0}", "Run queries...\n");
    RunQueries(indexClientForQueries);

    Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
    Console.ReadKey();
}
```

Di seguito è riportata una schermata parziale dell'output, presupponendo che l'applicazione venga eseguita con un nome di servizio e chiavi API validi:

:::image type="content" source="media/search-howto-dotnet-sdk/console-output.png" alt-text="Output di console. WriteLine dal programma di esempio":::

### <a name="client-types"></a>Tipi di client

La libreria client usa tre tipi di client per varie operazioni: [`SearchIndexClient`](/dotnet/api/azure.search.documents.indexes.searchindexclient) per creare, aggiornare o eliminare indici, [`SearchClient`](/dotnet/api/azure.search.documents.searchclient) per caricare o eseguire query su un indice e [`SearchIndexerClient`](/dotnet/api/azure.search.documents.indexes.searchindexerclient) per lavorare con gli indicizzatori e con skillsets. Questo articolo è incentrato sulle prime due. 

Come minimo, tutti i client richiedono il nome o l'endpoint del servizio e una chiave API. È comune fornire queste informazioni in un file di configurazione, in modo analogo a quanto si trova nel `appsettings.json` file dell' [applicazione di esempio DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo). Per leggere il file di configurazione, aggiungere `using Microsoft.Extensions.Configuration;` al programma.

L'istruzione seguente crea il client index utilizzato per creare, aggiornare o eliminare gli indici. Accetta un endpoint di ricerca e una chiave API di amministrazione.

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
    string searchServiceEndPoint = configuration["SearchServiceEndPoint"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(new Uri(searchServiceEndPoint), new AzureKeyCredential(adminApiKey));
    return indexClient;
}
```

L'istruzione successiva crea il client di ricerca utilizzato per caricare documenti o eseguire query. `SearchClient` richiede un indice. Per caricare i documenti sarà necessaria una chiave API di amministrazione, ma è possibile usare una chiave API di query per eseguire query. 

```csharp
string indexName = configuration["SearchIndexName"];

private static SearchClient CreateSearchClientForQueries(string indexName, IConfigurationRoot configuration)
{
    string searchServiceEndPoint = configuration["SearchServiceEndPoint"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchClient searchClient = new SearchClient(new Uri(searchServiceEndPoint), indexName, new AzureKeyCredential(queryApiKey));
    return searchClient;
}
```

> [!NOTE]
> Se si fornisce una chiave non valida per l'operazione di importazione (ad esempio, una chiave di query in cui è stata richiesta una chiave amministrativa), il genererà `SearchClient` una `CloudException` con il messaggio di errore "Forbidden" la prima volta che si chiama un metodo di operazione su di esso. In tal caso, controllare la chiave API.
>

### <a name="deleting-the-index"></a>Eliminazione dell'indice

Nelle fasi iniziali dello sviluppo, potrebbe essere necessario includere un' [`DeleteIndex`](/dotnet/api/azure.search.documents.indexes.searchindexclient.deleteindex) istruzione per eliminare un indice di lavoro in corso, in modo da poterlo ricreare con una definizione aggiornata. Il codice di esempio per Azure ricerca cognitiva spesso include un passaggio di eliminazione, in modo che sia possibile eseguire di nuovo l'esempio.

La riga seguente chiama `DeleteIndexIfExists` :

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteIndexIfExists(indexName, indexClient);
```

Questo metodo utilizza l'oggetto specificato `SearchIndexClient` per verificare se l'indice esiste e, in caso affermativo, lo elimina:

```csharp
private static void DeleteIndexIfExists(string indexName, SearchIndexClient indexClient)
{
    try
    {
        if (indexClient.GetIndex(indexName) != null)
        {
            indexClient.DeleteIndex(indexName);
        }
    }
    catch (RequestFailedException e) when (e.Status == 404)
    {
        // Throw an exception if the index name isn't found
        Console.WriteLine("The index doesn't exist. No deletion occurred.");
```

> [!NOTE]
> Il codice di esempio in questo articolo usa i metodi sincroni per semplicità, ma è consigliabile usare i metodi asincroni nelle proprie applicazioni per mantenerli scalabili e reattive. Nel metodo precedente, ad esempio, è possibile usare [`DeleteIndexAsync`](/dotnet/api/azure.search.documents.indexes.searchindexclient.deleteindexasync) anziché [`DeleteIndex`](/dotnet/api/azure.search.documents.indexes.searchindexclient.deleteindex) .
>

## <a name="create-an-index"></a>Creare un indice

È possibile utilizzare [`SearchIndexClient`](/dotnet/api/azure.search.documents.indexes.searchindexclient) per creare un indice. 

Il metodo seguente consente di creare un nuovo [`SearchIndex`](/dotnet/api/azure.search.documents.indexes.models.searchindex) oggetto con un elenco di [`SearchField`](/dotnet/api/azure.search.documents.indexes.models.searchfield) oggetti che definiscono lo schema del nuovo indice. Ogni campo ha un nome, un tipo di dati e diversi attributi che definiscono il comportamento della ricerca. 

I campi possono essere definiti da una classe di modello usando [`FieldBuilder`](/dotnet/api/azure.search.documents.indexes.fieldbuilder) . La classe `FieldBuilder` utilizza la reflection per creare un elenco di oggetti `SearchField` per l'indice esaminando le proprietà pubbliche e gli attributi della classe modello `Hotel` specificata. Esamineremo la classe `Hotel` più da vicino in un secondo momento.

```csharp
private static void CreateIndex(string indexName, SearchIndexClient indexClient)
{
    FieldBuilder fieldBuilder = new FieldBuilder();
    var searchFields = fieldBuilder.Build(typeof(Hotel));

    var definition = new SearchIndex(indexName, searchFields);

    indexClient.CreateOrUpdateIndex(definition);
}
```

Oltre ai campi, è anche possibile aggiungere profili di punteggio, suggerimenti o opzioni CORS all'indice. questi parametri vengono omessi dall'esempio per brevità. È possibile trovare altre informazioni sull'oggetto SearchIndex e sulle relative parti costitutive nell' [`SearchIndex`](/dotnet/api/azure.search.documents.indexes.models.searchindex) elenco delle proprietà, oltre che nelle informazioni di [riferimento sull'API REST](/rest/api/searchservice/).

> [!NOTE]
> Se necessario, è inoltre possibile creare l'elenco di oggetti `Field` direttamente anziché utilizzando `FieldBuilder`. Ad esempio, è possibile evitare di utilizzare una classe modello o potrebbe essere necessario utilizzare una classe modello esistente che non si desidera modificare aggiungendo attributi.
>

### <a name="call-createindex-in-main"></a>Chiamare CreateIndex in Main ()

`Main` Crea un nuovo indice "Hotels" chiamando il metodo precedente:

```csharp
Console.WriteLine("{0}", "Creating index...\n");
CreateIndex(indexName, indexClient);
```

## <a name="use-a-model-class-for-data-representation"></a>Usare una classe modello per la rappresentazione dei dati

Nell'esempio DotNetHowTo vengono utilizzate le classi del modello per le strutture di dati [Hotel](https://github.com/Azure-Samples/search-dotnet-getting-started/blob/master/DotNetHowTo/DotNetHowTo/Hotel.cs), [Indirizzo](https://github.com/Azure-Samples/search-dotnet-getting-started/blob/master/DotNetHowTo/DotNetHowTo/Address.cs)e [chat room](https://github.com/Azure-Samples/search-dotnet-getting-started/blob/master/DotNetHowTo/DotNetHowTo/Room.cs) . `Hotel` riferimenti `Address` , un tipo complesso a singolo livello (un campo multiparte) e `Room` (una raccolta di campi multiparte).

È possibile utilizzare questi tipi per creare e caricare l'indice e per strutturare la risposta da una query:

```csharp
// Use-case: <Hotel> in a field definition
FieldBuilder fieldBuilder = new FieldBuilder();
var searchFields = fieldBuilder.Build(typeof(Hotel));

// Use-case: <Hotel> in a response
private static void WriteDocuments(SearchResults<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.GetResults())
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

### <a name="field-definitions"></a>Definizioni dei campi

Il modello di dati in .NET e lo schema di indice corrispondente dovrebbero supportare l'esperienza di ricerca che si vuole assegnare all'utente finale. Ogni oggetto di primo livello in .NET, ad esempio un documento di ricerca in un indice di ricerca, corrisponde a un risultato della ricerca presente nell'interfaccia utente. Ad esempio, in un'applicazione di ricerca di un hotel è possibile che gli utenti finali desiderino eseguire la ricerca in base al nome dell'albergo, alle funzionalità dell'albergo o alle caratteristiche di una determinata chat room. 

All'interno di ogni classe, un campo viene definito con un tipo di dati e gli attributi che determinano il modo in cui viene usato. Il nome di ogni proprietà pubblica in ogni classe è mappato a un campo con lo stesso nome nella definizione dell'indice. 

Esaminare il frammento di codice seguente che estrae diverse definizioni di campo dalla classe dell'hotel. Si noti che Address e Rooms sono tipi C# con le rispettive definizioni di classe (fare riferimento al codice di esempio se si vuole visualizzarle). Entrambi sono tipi complessi. Per ulteriori informazioni, vedere [come modellare tipi complessi](search-howto-complex-data-types.md).

```csharp
public partial class Hotel
{
    [SimpleField(IsKey = true, IsFilterable = true)]
    public string HotelId { get; set; }

    [SearchableField(IsSortable = true)]
    public string HotelName { get; set; }

    [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnLucene)]
    public string Description { get; set; }

    [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
    public string Category { get; set; }

    [JsonIgnore]
    public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;

    [SearchableField]
    public Address Address { get; set; }

    public Room[] Rooms { get; set; }
```

#### <a name="choosing-a-field-class"></a>Scelta di una classe di campo

Quando si definiscono i campi, è possibile usare la classe di base [`SearchField`](/dotnet/api/azure.search.documents.indexes.models.searchfield) oppure usare modelli di supporto derivati che fungano da "modelli", con proprietà preconfigurate.

Esattamente un campo nell'indice deve essere utilizzato come chiave del documento ( `IsKey = true` ). Deve essere una stringa e deve identificare in modo univoco ogni documento. È anche necessario disporre di `IsHidden = true` , il che significa che non può essere visibile nei risultati della ricerca.

| Tipo di campo | Descrizione e utilizzo |
|------------|-----------------------|
| [`SearchField`](/dotnet/api/azure.search.documents.indexes.models.searchfield) | Classe di base, con la maggior parte delle proprietà impostate su null, tranne `Name` che è obbligatorio e `AnalyzerName` per impostazione predefinita è Lucene standard. |
| [`SimpleField`](/dotnet/api/azure.search.documents.indexes.models.simplefield) | Modello Helper. Può essere qualsiasi tipo di dati, è sempre non ricercabile (viene ignorato per le query di ricerca full-text) ed è recuperabile (non è nascosto). Altri attributi sono disattivati per impostazione predefinita, ma possono essere attivati. Si potrebbe usare un oggetto `SimpleField` per gli ID di documento o i campi usati solo in filtri, facet o profili di punteggio. In tal caso, assicurarsi di applicare tutti gli attributi necessari per lo scenario, ad esempio `IsKey = true` per un ID documento. Per altre informazioni, vedere [SimpleFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SimpleFieldAttribute.cs) nel codice sorgente. |
| [`SearchableField`](/dotnet/api/azure.search.documents.indexes.models.searchablefield) | Modello Helper. Deve essere una stringa ed è sempre eseguibile per la ricerca e il recupero. Altri attributi sono disattivati per impostazione predefinita, ma possono essere attivati. Poiché questo tipo di campo è ricercabile, supporta i sinonimi e tutte le proprietà dell'analizzatore. Per altre informazioni, vedere [SearchableFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SearchableFieldAttribute.cs) nel codice sorgente. |

Sia che si usi l'API `SearchField` di base o uno dei modelli di supporto, è necessario abilitare in modo esplicito gli attributi di filtro, facet e ordinamento. Ad esempio, [IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable), [IsSortable](/dotnet/api/azure.search.documents.indexes.models.searchfield.issortable) e [IsFacetable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfacetable) devono essere attribuiti in modo esplicito come illustrato nell'esempio precedente.

#### <a name="adding-field-attributes"></a>Aggiunta di attributi di campo

Si noti che ogni campo è decorato con attributi quali `IsFilterable` ,, `IsSortable` `IsKey` e `AnalyzerName` . Questi attributi vengono mappati direttamente agli [attributi di campo corrispondenti in un indice di ricerca cognitiva di Azure](/rest/api/searchservice/create-index). La `FieldBuilder` classe utilizza queste proprietà per costruire definizioni di campo per l'indice.

#### <a name="field-type-mapping"></a>Mapping dei tipi di campo

I tipi .NET delle proprietà vengono mappati ai tipi di campo equivalenti nella definizione dell'indice. Ad esempio, viene eseguito il mapping della proprietà stringa `Category` al campo `category`, che è di tipo `Edm.String`. Esistono mapping di tipi simili tra `bool?` , `Edm.Boolean` , `DateTimeOffset?` e `Edm.DateTimeOffset` e così via. 

La proprietà è stata Nota `SmokingAllowed` ?

```csharp
[JsonIgnore]
public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;
```

L' `JsonIgnore` attributo di questa proprietà indica `FieldBuilder` a di non serializzarlo nell'indice come campo.  Questo è un ottimo modo per creare proprietà calcolate sul lato client che è possibile usare come helper nell'applicazione.  In questo caso, la `SmokingAllowed` proprietà indica se un `Room` oggetto della `Rooms` raccolta consente il fumo. Se tutti sono false, significa che l'intero hotel non consente il fumo.

## <a name="load-an-index"></a>Caricare un indice

Il passaggio successivo in `Main` popola l'indice "Hotels" appena creato. Il popolamento dell'indice viene eseguito nel metodo seguente: (parte del codice sostituita con "..." a scopo illustrativo. Vedere la soluzione di esempio completa per il codice di popolamento completo dei dati.

```csharp
private static void UploadDocuments(SearchClient searchClient)
{
    IndexDocumentsBatch<Hotel> batch = IndexDocumentsBatch.Create(
        IndexDocumentsAction.Upload(
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
            }),
        IndexDocumentsAction.Upload(
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
            }),
        IndexDocumentsAction.Upload(
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

    try
    {
        IndexDocumentsResult result = searchClient.IndexDocuments(batch);
    }
    catch (Exception)
    {
        // Sometimes when your Search service is under load, indexing will fail for some of the documents in
        // the batch. Depending on your application, you can take compensating actions like delaying and
        // retrying. For this simple demo, we just log the failed document keys and continue.
        Console.WriteLine("Failed to index some of the documents: {0}");
    }

    Console.WriteLine("Waiting for documents to be indexed...\n");
    Thread.Sleep(2000);
```

Questo metodo è costituito da quattro parti. Il primo crea una matrice di 3 `Hotel` oggetti ognuno con 3 `Room` oggetti che serviranno come dati di input da caricare nell'indice. Questi dati sono hardcoded per motivi di semplicità. In un'applicazione effettiva i dati proverranno probabilmente da un'origine dati esterna, ad esempio un database SQL.

Nella seconda parte viene creato un oggetto [`IndexDocumentsBatch`](/dotnet/api/azure.search.documents.models.indexdocumentsbatch) contenente i documenti. Specificare l'operazione che si desidera applicare al batch nel momento in cui viene creata, in questo caso chiamando [`IndexDocumentsAction.Upload`](/dotnet/api/azure.search.documents.models.indexdocumentsaction.upload) . Il batch viene quindi caricato nell'indice ricerca cognitiva di Azure dal [`IndexDocuments`](/dotnet/api/azure.search.documents.searchclient.indexdocuments) metodo.

> [!NOTE]
> In questo esempio, verranno semplicemente caricati i documenti. Se invece si vogliono unire le modifiche in documenti esistenti o eliminare documenti, creare batch chiamando `IndexDocumentsAction.Merge`, `IndexDocumentsAction.MergeOrUpload` o `IndexDocumentsAction.Delete`. È anche possibile combinare diverse operazioni in un singolo batch chiamando `IndexBatch.New` , che accetta una raccolta di `IndexDocumentsAction` oggetti, ognuno dei quali indica ad Azure ricerca cognitiva di eseguire una determinata operazione su un documento. È possibile creare ogni oggetto `IndexDocumentsAction` con la propria operazione chiamando il metodo corrispondente, ad esempio `IndexDocumentsAction.Merge`, `IndexAction.Upload` e così via.
> 

La terza parte di questo metodo è un blocco catch che gestisce un caso di errore importante per l'indicizzazione. Se il servizio di ricerca non è in grado di indicizzare alcuni documenti nel batch, `IndexBatchException` viene generata un'eccezione da `IndexDocuments` . Questa eccezione può verificarsi se si indicizzazione dei documenti quando il servizio è sottoposto a un carico elevato. **Si consiglia di gestire in modo esplicito questo caso nel codice.**  È possibile ritardare e quindi ritentare l'indicizzazione di documenti, accedere e continuare come nell'esempio,  oppure eseguire altre attività a seconda dei requisiti di coerenza di dati dell'applicazione.

Infine, il metodo `UploadDocuments` ritarda per due secondi. L'indicizzazione avviene in modo asincrono nel servizio di ricerca, pertanto l'applicazione di esempio deve attendere un breve periodo di tempo per assicurarsi che i documenti siano disponibili per la ricerca. Ritardi come questi in genere sono necessari solo in applicazioni di esempio, test e demo.

### <a name="call-uploaddocuments-in-main"></a>Chiamare UploadDocuments in Main ()

Il frammento di codice seguente configura un'istanza di [`SearchClient`](/dotnet/api/azure.search.documents.searchclient) usando il [`GetSearchClient`](/dotnet/api/azure.search.documents.indexes.searchindexclient.getsearchclient) metodo di indexClient. Il indexClient usa una chiave API di amministrazione nelle richieste, che è necessario per il caricamento o l'aggiornamento dei documenti.

Un approccio alternativo consiste nel chiamare `SearchClient` direttamente, passando una chiave API di amministrazione su `AzureKeyCredential` .

```csharp
SearchClient searchClient = indexClient.GetSearchClient(indexName);

Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(searchClient);
```

## <a name="run-queries"></a>Eseguire le query

Per prima cosa, configurare un `SearchClient` che legga l'endpoint di ricerca e la chiave API di query da **appsettings.jsin** :

```csharp
private static SearchClient CreateSearchClientForQueries(string indexName, IConfigurationRoot configuration)
{
    string searchServiceEndPoint = configuration["SearchServiceEndPoint"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchClient searchClient = new SearchClient(new Uri(searchServiceEndPoint), indexName, new AzureKeyCredential(queryApiKey));
    return searchClient;
}
```

In secondo luogo, definire un metodo che invii una richiesta di query. 

Ogni volta che il metodo esegue una query, viene creato un nuovo [`SearchOptions`](/dotnet/api/azure.search.documents.searchoptions) oggetto. Questo oggetto viene utilizzato per specificare opzioni aggiuntive per la query, ad esempio ordinamento, filtro, paging e facet. In questo metodo, vengono impostate le `Filter` proprietà, `Select` e `OrderBy` per le diverse query. Per ulteriori informazioni sulla sintassi delle espressioni di query di ricerca, [sintassi di query semplice](/rest/api/searchservice/Simple-query-syntax-in-Azure-Search).

Il passaggio successivo consiste nell’esecuzione effettiva della query di ricerca. L'esecuzione della ricerca viene eseguita utilizzando il `SearchClient.Search` metodo. Per ogni query, passare il testo di ricerca da utilizzare come stringa (o `"*"` se non esiste un testo di ricerca), più le opzioni di ricerca create in precedenza. Viene inoltre specificato `Hotel` come parametro di tipo per `SearchClient.Search`, che indica all'SDK di deserializzare i documenti nei risultati della ricerca in oggetti di tipo `Hotel`.

```csharp
private static void RunQueries(SearchClient searchClient)
{
    SearchOptions options;
    SearchResults<Hotel> results;

    Console.WriteLine("Query 1: Search for 'motel'. Return only the HotelName in results:\n");

    options = new SearchOptions();
    options.Select.Add("HotelName");

    results = searchClient.Search<Hotel>("motel", options);

    WriteDocuments(results);

    Console.Write("Query 2: Apply a filter to find hotels with rooms cheaper than $100 per night, ");
    Console.WriteLine("returning the HotelId and Description:\n");

    options = new SearchOptions()
    {
        Filter = "Rooms/any(r: r/BaseRate lt 100)"
    };
    options.Select.Add("HotelId");
    options.Select.Add("Description");

    results = searchClient.Search<Hotel>("*", options);

    WriteDocuments(results);

    Console.Write("Query 3: Search the entire index, order by a specific field (lastRenovationDate) ");
    Console.Write("in descending order, take the top two results, and show only hotelName and ");
    Console.WriteLine("lastRenovationDate:\n");

    options =
        new SearchOptions()
        {
            Size = 2
        };
    options.OrderBy.Add("LastRenovationDate desc");
    options.Select.Add("HotelName");
    options.Select.Add("LastRenovationDate");

    results = searchClient.Search<Hotel>("*", options);

    WriteDocuments(results);

    Console.WriteLine("Query 4: Search the HotelName field for the term 'hotel':\n");

    options = new SearchOptions();
    options.SearchFields.Add("HotelName");

    //Adding details to select, because "Location" is not supported yet when deserialize search result to "Hotel"
    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Description");
    options.Select.Add("Category");
    options.Select.Add("Tags");
    options.Select.Add("ParkingIncluded");
    options.Select.Add("LastRenovationDate");
    options.Select.Add("Rating");
    options.Select.Add("Address");
    options.Select.Add("Rooms");

    results = searchClient.Search<Hotel>("hotel", options);

    WriteDocuments(results);
}
```

In terzo luogo, definire un metodo che scrive la risposta, stampando ogni documento nella console:

```csharp
private static void WriteDocuments(SearchResults<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.GetResults())
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

### <a name="call-runqueries-in-main"></a>Chiamare RunQueries in Main ()

```csharp
SearchClient indexClientForQueries = CreateSearchClientForQueries(indexName, configuration);

Console.WriteLine("{0}", "Running queries...\n");
RunQueries(indexClientForQueries);
```

### <a name="explore-query-constructs"></a>Esplora costrutti di query

Queste query saranno ora esaminate in maggiore dettaglio. Ecco il codice per eseguire la prima query:

```csharp
options = new SearchOptions();
options.Select.Add("HotelName");

results = searchClient.Search<Hotel>("motel", options);

WriteDocuments(results);
```

In questo caso, viene eseguita la ricerca dell'intero indice per la parola "Motel" in qualsiasi campo ricercabile e si desidera recuperare solo i nomi degli alberghi, come specificato dall' `Select` opzione. Ecco i risultati:

```output
Name: Secret Point Motel

Name: Twin Dome Motel
```

Nella seconda query usare un filtro per selezionare una stanza con una frequenza notturna inferiore a $100. Restituire solo l'ID e la descrizione dell'hotel nei risultati:

```csharp
options = new SearchOptions()
{
    Filter = "Rooms/any(r: r/BaseRate lt 100)"
};
options.Select.Add("HotelId");
options.Select.Add("Description");

results = searchClient.Search<Hotel>("*", options);
```

Nella query precedente viene utilizzata un' `$filter` espressione OData, `Rooms/any(r: r/BaseRate lt 100)` , per filtrare i documenti nell'indice. Viene usato l' [operatore Any](./search-query-odata-collection-operators.md) per applicare ' BaseRate lt 100' a ogni elemento della raccolta chat room. Per ulteriori informazioni, vedere [sintassi del filtro OData](./query-odata-filter-orderby-syntax.md).

Nella terza query trovare i primi due alberghi rinnovati più di recente e visualizzare il nome dell'hotel e l'ultima data di rinnovo. Il codice è il seguente: 

```csharp
options =
    new SearchOptions()
    {
        Size = 2
    };
options.OrderBy.Add("LastRenovationDate desc");
options.Select.Add("HotelName");
options.Select.Add("LastRenovationDate");

results = searchClient.Search<Hotel>("*", options);

WriteDocuments(results);
```

Nell'ultima query trovare tutti i nomi degli alberghi che corrispondono alla parola "Hotel":

```csharp
options.Select.Add("HotelId");
options.Select.Add("HotelName");
options.Select.Add("Description");
options.Select.Add("Category");
options.Select.Add("Tags");
options.Select.Add("ParkingIncluded");
options.Select.Add("LastRenovationDate");
options.Select.Add("Rating");
options.Select.Add("Address");
options.Select.Add("Rooms");

results = searchClient.Search<Hotel>("hotel", options);

WriteDocuments(results);
```

Questa sezione conclude questa introduzione a .NET SDK, ma non è possibile arrestarla. La sezione successiva suggerisce risorse aggiuntive per ottenere altre informazioni sulla programmazione con Azure ricerca cognitiva.

## <a name="next-steps"></a>Passaggi successivi

+ Esaminare la documentazione di riferimento per le API per [Azure.Search.Documents](/dotnet/api/azure.search.documents) e l' [API REST](/rest/api/searchservice/)

+ Esplorare altri esempi di codice in base a Azure.Search.Documents in [Azure-Search-DotNet-Samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) e [Search-Getting-Started-DotNet](https://github.com/Azure-Samples/search-dotnet-getting-started)

+ Esaminare le [convenzioni di denominazione](/rest/api/searchservice/Naming-rules) per apprendere le regole per la denominazione di vari oggetti

+ Esaminare [i tipi di dati supportati](/rest/api/searchservice/Supported-data-types)