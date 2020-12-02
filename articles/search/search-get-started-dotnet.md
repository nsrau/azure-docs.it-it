---
title: 'Avvio rapido: Creare un indice di ricerca in .NET'
titleSuffix: Azure Cognitive Search
description: Questa guida di avvio rapido per C# illustra come creare un indice, caricare dati ed eseguire query con la libreria client Azure.Search.Documents.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 11/20/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 448f2b3e42e98d78652a005f5d1c11f55acdebb3
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95021185"
---
# <a name="quickstart-create-a-search-index-using-the-azuresearchdocuments-client-library"></a>Avvio rapido: Creare un indice di ricerca con la libreria client Azure.Search.Documents

Usare la nuova [libreria client Azure.Search.Documents (versione 11)](/dotnet/api/overview/azure/search.documents-readme) per creare un'applicazione console .NET Core in C# che crea e carica un indice di ricerca ed esegue query su di esso.

È possibile [scaricare il codice sorgente](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11) per iniziare con un progetto finito oppure seguire la procedura descritta in questo articolo per crearne uno.

> [!NOTE]
> Per una versione precedente, vedere invece [Creare un indice di ricerca con Microsoft.Azure.Search v10](search-get-started-dotnet-v10.md).

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, è necessario avere gli strumenti e i servizi seguenti:

+ Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/).

+ Un'istanza del servizio Ricerca cognitiva di Azure. [Creare un servizio](search-create-service-portal.md) o [trovarne uno esistente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). È possibile usare un servizio gratuito per questo avvio rapido. 

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/), qualsiasi edizione. Il codice di esempio è stato testato nell'edizione Community gratuita di Visual Studio 2019.

Quando si configura il progetto, verrà scaricato il [pacchetto NuGet Azure.Search.Documents](https://www.nuget.org/packages/Azure.Search.Documents/).

Azure SDK per .NET è conforme a [.NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support), il che significa che .NET Framework 4.6.1 e .NET Core 2.0 sono i requisiti minimi.

## <a name="set-up-your-project"></a>Impostare il progetto

Assemblare le informazioni di connessione del servizio e quindi avviare Visual Studio per creare un nuovo progetto App console che possa essere eseguito in .NET Core.

<a name="get-service-info"></a>

### <a name="copy-a-key-and-endpoint"></a>Copiare una chiave e un endpoint

Le chiamate al servizio richiedono un URL endpoint e una chiave di accesso per ogni richiesta. Prima di tutto occorre trovare la chiave API e l'URL da aggiungere al progetto. Entrambi i valori dovranno essere specificati per creare il client più avanti.

1. [Accedere al portale di Azure](https://portal.azure.com/) e ottenere l'URL nella pagina **Panoramica** del servizio di ricerca. Un endpoint di esempio potrebbe essere simile a `https://mydemo.search.windows.net`.

2. In **Impostazioni** > **Chiavi** ottenere una chiave amministratore per avere diritti completi sul servizio, necessari in caso di creazione o eliminazione di oggetti. Vengono visualizzate due chiavi intercambiabili: la chiave primaria e quella secondaria. È possibile usare una qualsiasi delle due.

   ![Ottenere un endpoint HTTP e una chiave di accesso](media/search-get-started-rest/get-url-key.png "Ottenere un endpoint HTTP e una chiave di accesso")

Per ogni richiesta inviata al servizio è necessario specificare una chiave API. La presenza di una chiave valida stabilisce una relazione di trust, in base alle singole richieste, tra l'applicazione che invia la richiesta e il servizio che la gestisce.

### <a name="install-the-nuget-package"></a>Installare il pacchetto NuGet

Dopo aver creato il progetto, aggiungere la libreria client. Il [pacchetto Azure.Search.Documents](https://www.nuget.org/packages/Azure.Search.Documents/) è costituito da una libreria client che include tutte le API che consentono di usare un servizio di ricerca in .NET.

1. Avviare Visual Studio e creare un'applicazione console .NET Core.

1. In **Strumenti** > **Gestione pacchetti NuGet**, selezionare **Gestisci pacchetti NuGet per la soluzione**. 

1. Fare clic su **Sfoglia**.

1. Cercare `Azure.Search.Documents` e selezionare la versione 11.0 o successiva.

1. Fare clic su **Installa** a destra per aggiungere l'assembly al progetto e alla soluzione.

### <a name="create-a-search-client"></a>Creare un client di ricerca

1. In **Program.cs** modificare lo spazio dei nomi in `AzureSearch.SDK.Quickstart.v11` e quindi aggiungere le direttive `using` seguenti.

   ```csharp
   using Azure;
   using Azure.Search.Documents;
   using Azure.Search.Documents.Indexes;
   using Azure.Search.Documents.Indexes.Models;
   using Azure.Search.Documents.Models;
   ```

1. Creare due client. [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) crea l'indice, mentre [SearchClient](/dotnet/api/azure.search.documents.searchclient) carica un indice esistente ed esegue query su di esso. Per entrambi è necessario l'endpoint di servizio e una chiave API amministratore per l'autenticazione con diritti di creazione/eliminazione.

   ```csharp
   static void Main(string[] args)
   {
       string serviceName = "<YOUR-SERVICE-NAME>";
       string indexName = "hotels-quickstart";
       string apiKey = "<YOUR-ADMIN-API-KEY>";

        // Create a SearchIndexClient to send create/delete index commands
        Uri serviceEndpoint = new Uri($"https://{serviceName}.search.windows.net/");
        AzureKeyCredential credential = new AzureKeyCredential(apiKey);
        SearchIndexClient adminClient = new SearchIndexClient(serviceEndpoint, credential);

        // Create a SearchClient to load and query documents
        SearchClient srchclient = new SearchClient(serviceEndpoint, indexName, credential);
    ```

## <a name="1---create-an-index"></a>1 - Creare un indice

Questa guida di avvio rapido compila un indice di hotel in cui si caricheranno i dati relativi agli hotel su cui si eseguiranno query. In questo passaggio definire i campi dell'indice. Ogni definizione di campo include un nome, un tipo di dati e gli attributi che determinano come viene usato il campo.

In questo esempio, per una maggiore leggibilità e semplicità vengono usati i metodi sincroni della libreria Azure.Search.Documents. Per scenari di produzione, tuttavia, è consigliabile usare metodi asincroni per mantenere la scalabilità e la reattività dell'app. Ad esempio, usare [CreateIndexAsync](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindexasync) invece di [CreateIndex](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex).

1. Aggiungere una definizione di classe vuota al progetto: **Hotel.cs**.

1. Copiare il codice seguente in **Hotel.cs** per definire la struttura di un documento di hotel. Gli attributi nel campo ne determinano l'uso in un'applicazione. L'attributo `IsFilterable`, ad esempio, deve essere assegnato a ogni campo che supporta un'espressione filtro.

    ```csharp
    using System;
    using System.Text.Json.Serialization;
    using Azure.Search.Documents.Indexes;
    using Azure.Search.Documents.Indexes.Models;

    namespace AzureSearch.Quickstart
    {
        public partial class Hotel
        {
            [SimpleField(IsKey = true, IsFilterable = true)]
            public string HotelId { get; set; }

            [SearchableField(IsSortable = true)]
            public string HotelName { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnLucene)]
            public string Description { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.FrLucene)]
            [JsonPropertyName("Description_fr")]
            public string DescriptionFr { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string Category { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string[] Tags { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public bool? ParkingIncluded { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public DateTimeOffset? LastRenovationDate { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public double? Rating { get; set; }

            [SearchableField]
            public Address Address { get; set; }
        }
    }
    ```

   Nella libreria client Azure.Search.Documents è possibile usare [SearchableField](/dotnet/api/azure.search.documents.indexes.models.searchablefield) e [SimpleField](/dotnet/api/azure.search.documents.indexes.models.simplefield) per semplificare le definizioni dei campi. Entrambi derivano da un oggetto [SearchField](/dotnet/api/azure.search.documents.indexes.models.searchfield) e possono semplificare il codice:

   + `SimpleField` può essere di qualsiasi tipo di dati, non è mai ricercabile (viene ignorato per le query di ricerca full-text) ed è recuperabile (non è nascosto). Altri attributi sono disattivati per impostazione predefinita, ma possono essere attivati. Si potrebbe usare un oggetto `SimpleField` per gli ID di documento o i campi usati solo in filtri, facet o profili di punteggio. In tal caso, assicurarsi di applicare tutti gli attributi necessari per lo scenario, ad esempio `IsKey = true` per un ID documento. Per altre informazioni, vedere [SimpleFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SimpleFieldAttribute.cs) nel codice sorgente.

   + `SearchableField` deve essere una stringa ed è sempre ricercabile e recuperabile. Altri attributi sono disattivati per impostazione predefinita, ma possono essere attivati. Poiché questo tipo di campo è ricercabile, supporta i sinonimi e tutte le proprietà dell'analizzatore. Per altre informazioni, vedere [SearchableFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SearchableFieldAttribute.cs) nel codice sorgente.

   Sia che si usi l'API `SearchField` di base o uno dei modelli di supporto, è necessario abilitare in modo esplicito gli attributi di filtro, facet e ordinamento. Ad esempio, [IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable), [IsSortable](/dotnet/api/azure.search.documents.indexes.models.searchfield.issortable) e [IsFacetable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfacetable) devono essere attribuiti in modo esplicito come illustrato nell'esempio precedente. 

1. Aggiungere una seconda definizione di classe vuota al progetto: **Address.cs**.  Copiare il codice seguente nella classe.

   ```csharp
   using Azure.Search.Documents.Indexes;

    namespace AzureSearch.Quickstart
    {
        public partial class Address
        {
            [SearchableField(IsFilterable = true)]
            public string StreetAddress { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string City { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string StateProvince { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string PostalCode { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string Country { get; set; }
        }
    }
   ```

1. Creare altre due classi: **Hotel.Methods.cs** e **Address.Methods.cs** per gli override ToString(). Queste classi vengono usate per visualizzare i risultati della ricerca nell'output della console.  Il contenuto di queste classi non viene fornito in questo articolo, ma è possibile copiare il codice dai [file in GitHub](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11/AzureSearchQuickstart-v11).

1. In **Program.cs** creare un oggetto [SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex), quindi chiamare il metodo [CreateIndex](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex) per esprimere l'indice nel servizio di ricerca. L'indice include anche un oggetto [SearchSuggester](/dotnet/api/azure.search.documents.indexes.models.searchsuggester) per abilitare il completamento automatico nei campi specificati.

   ```csharp
    // Create hotels-quickstart index
    private static void CreateIndex(string indexName, SearchIndexClient adminClient)
    {
        FieldBuilder fieldBuilder = new FieldBuilder();
        var searchFields = fieldBuilder.Build(typeof(Hotel));

        var definition = new SearchIndex(indexName, searchFields);

        var suggester = new SearchSuggester("sg", new[] { "HotelName", "Category", "Address/City", "Address/StateProvince" });
        definition.Suggesters.Add(suggester);

        adminClient.CreateOrUpdateIndex(definition);
    }
   ```

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - Caricare i documenti

Ricerca cognitiva di Azure esegue ricerche sul contenuto archiviato nel servizio. In questo passaggio si caricheranno documenti JSON conformi all'indice di hotel appena creato.

In Ricerca cognitiva di Azure i documenti di ricerca sono strutture di dati che costituiscono sia l'input per l'indicizzazione che l'output restituito dalle query. In quanto ottenuti da un'origine dati esterna, gli input dei documenti possono essere righe in un database, BLOB nell'archiviazione BLOB o documenti JSON nel disco. Per brevità, in questo esempio i documenti JSON per i quattro alberghi verranno incorporati nel codice stesso. 

Quando si caricano i documenti, è necessario usare un oggetto [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1). Un oggetto `IndexDocumentsBatch` contiene una raccolta di oggetti [Action](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1.actions), ognuno dei quali contiene un documento e una proprietà che indicano a Ricerca cognitiva di Azure quale azione eseguire ([upload, merge, delete e mergeOrUpload](search-what-is-data-import.md#indexing-actions)).

1. In **Program.cs** creare una matrice di documenti e azioni di indice e quindi passarla a `IndexDocumentsBatch`. I documenti seguenti sono conformi all'indice hotels-quickstart, come definito dalla classe hotel.

    ```csharp
    // Upload documents in a single Upload request.
    private static void UploadDocuments(SearchClient searchClient)
    {
        IndexDocumentsBatch<Hotel> batch = IndexDocumentsBatch.Create(
            IndexDocumentsAction.Upload(
                new Hotel()
                {
                    HotelId = "1",
                    HotelName = "Secret Point Motel",
                    Description = "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
                    DescriptionFr = "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "air conditioning", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate = new DateTimeOffset(1970, 1, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.6,
                    Address = new Address()
                    {
                        StreetAddress = "677 5th Ave",
                        City = "New York",
                        StateProvince = "NY",
                        PostalCode = "10022",
                        Country = "USA"
                    }
                }),
            IndexDocumentsAction.Upload(
                new Hotel()
                {
                    HotelId = "2",
                    HotelName = "Twin Dome Motel",
                    Description = "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "free wifi", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate = new DateTimeOffset(1979, 2, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.60,
                    Address = new Address()
                    {
                        StreetAddress = "140 University Town Center Dr",
                        City = "Sarasota",
                        StateProvince = "FL",
                        PostalCode = "34243",
                        Country = "USA"
                    }
                }),
            IndexDocumentsAction.Upload(
                new Hotel()
                {
                    HotelId = "3",
                    HotelName = "Triple Landscape Hotel",
                    Description = "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Resort and Spa",
                    Tags = new[] { "air conditioning", "bar", "continental breakfast" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(2015, 9, 20, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4.80,
                    Address = new Address()
                    {
                        StreetAddress = "3393 Peachtree Rd",
                        City = "Atlanta",
                        StateProvince = "GA",
                        PostalCode = "30326",
                        Country = "USA"
                    }
                }),
            IndexDocumentsAction.Upload(
                new Hotel()
                {
                    HotelId = "4",
                    HotelName = "Sublime Cliff Hotel",
                    Description = "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
                    DescriptionFr = "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
                    Category = "Boutique",
                    Tags = new[] { "concierge", "view", "24-hour front desk service" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1960, 2, 06, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4.60,
                    Address = new Address()
                    {
                        StreetAddress = "7400 San Pedro Ave",
                        City = "San Antonio",
                        StateProvince = "TX",
                        PostalCode = "78216",
                        Country = "USA"
                    }
                })
            );

        try
        {
            IndexDocumentsResult result = searchClient.IndexDocuments(batch);
        }
        catch (Exception)
        {
            // If for some reason any documents are dropped during indexing, you can compensate by delaying and
            // retrying. This simple demo just logs the failed document keys and continues.
            Console.WriteLine("Failed to index some of the documents: {0}");
        }
    }
    ```

    Dopo aver inizializzato l'oggetto [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1), è possibile inviarlo all'indice chiamando [IndexDocuments](/dotnet/api/azure.search.documents.searchclient.indexdocuments) nell'oggetto [SearchClient](/dotnet/api/azure.search.documents.searchclient).

1. Aggiungere le righe seguenti in Main(). Il caricamento dei documenti viene eseguito tramite SearchClient, ma l'operazione richiede anche diritti di amministratore per il servizio, che è in genere associato a SearchIndexClient. Per configurare questa operazione, è possibile ottenere SearchClient tramite SearchIndexClient (adminClient in questo esempio).

   ```csharp
    SearchClient ingesterClient = adminClient.GetSearchClient(indexName);

    // Load documents
    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(ingesterClient);
   ```

1. Dato che si tratta di un'app console che esegue tutti i comandi in sequenza, aggiungere un tempo di attesa di 2 secondi tra l'indicizzazione e le query.

    ```csharp
    // Wait 2 seconds for indexing to complete before starting queries (for demo and console-app purposes only)
    Console.WriteLine("Waiting for indexing...\n");
    System.Threading.Thread.Sleep(2000);
    ```

    Il ritardo di due secondi compensa l'indicizzazione, che è asincrona, in modo che tutti i documenti possano essere indicizzati prima dell'esecuzione delle query. La scrittura di codice in un ritardo è in genere necessaria solo in applicazioni di esempio, test e demo.

## <a name="3---search-an-index"></a>3 - Eseguire la ricerca in un indice

È possibile ottenere risultati della query subito dopo l'indicizzazione del primo documento, ma per il test effettivo dell'indice è necessario attendere il completamento dell'indicizzazione di tutti i documenti.

In questa sezione vengono aggiunte elementi di funzionalità, ovvero la logica di query e i risultati. Per le query usare il metodo [Search](/dotnet/api/azure.search.documents.searchclient.search). Questo metodo accetta il testo da cercare (stringa di query) nonché altre [opzioni](/dotnet/api/azure.search.documents.searchoptions).

La classe [SearchResults](/dotnet/api/azure.search.documents.models.searchresults-1) rappresenta i risultati.

1. In **Program.cs** creare un metodo **WriteDocuments** che stampa i risultati della ricerca nella console.

    ```csharp
    // Write search results to console
    private static void WriteDocuments(SearchResults<Hotel> searchResults)
    {
        foreach (SearchResult<Hotel> result in searchResults.GetResults())
        {
            Console.WriteLine(result.Document);
        }

        Console.WriteLine();
    }
    ```

1. Creare un metodo **RunQueries** per eseguire le query e restituire i risultati. I risultati sono oggetti Hotel. Questo esempio mostra la firma del metodo e la prima query. Questa query illustra il parametro Select e consente di comporre il risultato usando i campi selezionati dal documento.

    ```csharp
    // Run queries, use WriteDocuments to print output
    private static void RunQueries(SearchClient srchclient)
    {
        SearchOptions options;
        SearchResults<Hotel> response;

        Console.WriteLine("Query #1: Search on empty term '*' to return all documents, showing a subset of fields...\n");

        options = new SearchOptions()
        {
            IncludeTotalCount = true,
            Filter = "",
            OrderBy = { "" }
        };

        options.Select.Add("HotelId");
        options.Select.Add("HotelName");
        options.Select.Add("Address/City");

        response = srchclient.Search<Hotel>("*", options);
        WriteDocuments(response);
        ```

1. In the second query, search on a term, add a filter that selects documents where Rating is greater than 4, and then sort by Rating in descending order. Filter is a boolean expression that is evaluated over [IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable) fields in an index. Filter queries either include or exclude values. As such, there is no relevance score associated with a filter query. 

    ```csharp
    Console.WriteLine("Query #2: Search on 'hotels', filter on 'Rating gt 4', sort by Rating in descending order...\n");

    options = new SearchOptions()
    {
        Filter = "Rating gt 4",
        OrderBy = { "Rating desc" }
    };

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Rating");

    response = srchclient.Search<Hotel>("hotels", options);
    WriteDocuments(response);
    ```

1. La terza query illustra searchFields, che si usa per impostare l'ambito di un'operazione di ricerca full-text su specifici campi.

    ```csharp
    Console.WriteLine("Query #3: Limit search to specific fields (pool in Tags field)...\n");

    options = new SearchOptions()
    {
        SearchFields = { "Tags" }
    };

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Tags");

    response = srchclient.Search<Hotel>("pool", options);
    WriteDocuments(response);
    ```

1. La quarta query illustra i facet, che possono essere usati per creare una struttura di esplorazione in base a facet. 

   ```csharp
    Console.WriteLine("Query #4: Facet on 'Category'...\n");

    options = new SearchOptions()
    {
        Filter = ""
    };

    options.Facets.Add("Category");

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Category");

    response = srchclient.Search<Hotel>("*", options);
    WriteDocuments(response);
   ```

1. La quinta query restituisce un documento specifico. Una ricerca di documenti è una tipica risposta a un evento OnClick in un set di risultati.

   ```csharp
    Console.WriteLine("Query #5: Look up a specific document...\n");

    Response<Hotel> lookupResponse;
    lookupResponse = srchclient.GetDocument<Hotel>("3");

    Console.WriteLine(lookupResponse.Value.HotelId);
   ```

1. L'ultima query mostra la sintassi del completamento automatico, simulando l'input utente parziale "sa" che si risolve in due possibili corrispondenze nei campi sourceFields associati allo strumento suggerimenti definito nell'indice.

   ```csharp
    Console.WriteLine("Query #6: Call Autocomplete on HotelName that starts with 'sa'...\n");

    var autoresponse = srchclient.Autocomplete("sa", "sg");
    WriteDocuments(autoresponse);
   ```

1. Aggiungere **RunQueries** a Main().

    ```csharp
    // Call the RunQueries method to invoke a series of queries
    Console.WriteLine("Starting queries...\n");
    RunQueries(srchclient);

    // End the program
    Console.WriteLine("{0}", "Complete. Press any key to end this program...\n");
    Console.ReadKey();
    ```

Le query precedenti mostrano più [modi per trovare termini corrispondenti in una query](search-query-overview.md#types-of-queries): ricerca full-text, filtri e completamento automatico.

La ricerca full-text e i filtri vengono eseguiti con il metodo [SearchClient.Search](/dotnet/api/azure.search.documents.searchclient.search). Una query di ricerca può essere passata nella stringa `searchText`, mentre un'espressione filtro può essere passata nella proprietà [Filter](/dotnet/api/azure.search.documents.searchoptions.filter) della classe [SearchOptions](/dotnet/api/azure.search.documents.searchoptions). Per filtrare senza eseguire ricerche, passare soltanto `"*"` per il parametro `searchText` del metodo [Search](/dotnet/api/azure.search.documents.searchclient.search). Per eseguire una ricerca senza filtrare, lasciare la proprietà `Filter` non impostata oppure non passare un'istanza di `SearchOptions`.

## <a name="run-the-program"></a>Eseguire il programma

Premere F5 per ricompilare l'app ed eseguire il programma completo. 

L'output include i messaggi restituiti da [Console.WriteLine](/dotnet/api/system.console.writeline), con l'aggiunta di informazioni sulle query e i risultati.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando si lavora nella propria sottoscrizione, alla fine di un progetto è opportuno verificare se le risorse create sono ancora necessarie. Le risorse che rimangono in esecuzione hanno un costo. È possibile eliminare le singole risorse oppure il gruppo di risorse per eliminare l'intero set di risorse.

Per trovare e gestire le risorse nel portale, usare il collegamento **Tutte le risorse** o **Gruppi di risorse** nel riquadro di spostamento a sinistra.

Se si usa un servizio gratuito, tenere presente che il numero di indicizzatori e origini dati è limitato a tre. Per non superare il limite, è possibile eliminare i singoli elementi nel portale. 

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido per C# è stata eseguita una serie di attività per creare un indice, caricarvi documenti ed eseguire query. In diverse fasi sono state adottate delle scorciatoie per semplificare il codice e renderlo più leggibile e comprensibile. Se si conoscono già i concetti di base, è consigliabile leggere l'articolo successivo in cui vengono esaminati concetti e approcci alternativi per approfondire l'argomento. 

> [!div class="nextstepaction"]
> [Come sviluppare in .NET](search-howto-dotnet-sdk.md)

Si vuole ottimizzare e risparmiare sulla spesa per il cloud?

> [!div class="nextstepaction"]
> [Per iniziare, analizzare i costi con Gestione costi](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)