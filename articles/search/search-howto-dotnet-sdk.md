<properties
   pageTitle="Come utilizzare Ricerca di Azure da un'applicazione .NET | Microsoft Azure"
   description="Come utilizzare Ricerca di Azure da un'applicazione .NET"
   services="search"
   documentationCenter=""
   authors="brjohnstmsft"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="dotnet"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="07/08/2015"
   ms.author="brjohnst"/>

# Come utilizzare Ricerca di Azure da un'applicazione .NET #

In questo articolo è descritta una procedura dettagliata per eseguire [.NET SDK di Ricerca di Azure](https://msdn.microsoft.com/library/azure/dn951165.aspx). È possibile utilizzare .NET SDK per implementare un'esperienza di ricerca completa nell'applicazione tramite Ricerca di Azure.

## Novità dell'SDK di Ricerca di Azure ##

Questo SDK è costituito da una libreria client, `Microsoft.Azure.Search`. Consente di gestire indici, origini dati e indicizzatori, nonché di caricare e gestire documenti ed eseguire query, senza la necessità di affrontare i dettagli di HTTP e JSON.

La libreria client definisce classi come `Index`, `Field`, e `Document`, nonché operazioni quali `Indexes.Create` e `Documents.Search` sulle classi `SearchServiceClient` e `SearchIndexClient`. Le classi sono organizzate negli spazi dei nomi seguenti:

- [Microsoft.Azure.Search](https://msdn.microsoft.com/library/azure/microsoft.azure.search.aspx)
- [Microsoft.Azure.Search.Models](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.aspx)

La versione corrente .NET SDK di Ricerca di Azure è la versione non definitiva. Per fornire a Microsoft commenti e suggerimenti da incorporare nella prima versione stabile, visitare la [pagina dei commenti](http://feedback.azure.com/forums/263029-azure-search).

.NET SDK supporta la versione `2015-02-28` dell'API REST di Ricerca di Azure, documentata su [MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx). Nuove funzionalità che *non* fanno parte di questa versione, ad esempio il supporto per i processori del linguaggio naturale di Microsoft o il parametro di ricerca `moreLikeThis`, sono in [anteprima](search-api-2015-02-28-preview.md) e non ancora disponibili nell'SDK. È possibile consultare [Controllo delle versioni di Ricerca di Azure](https://msdn.microsoft.com/library/azure/dn864560.aspx) o [Latest updates to Azure Search](search-latest-updates.md) per gli aggiornamenti dello stato di tali funzionalità.

Altre funzionalità non supportate in questo SDK sono:

  - [Operazioni di gestione](https://msdn.microsoft.com/library/azure/dn832684.aspx). Le operazioni di gestione includono il provisioning dei servizi Ricerca di Azure e la gestione delle chiavi API. Saranno supportate a breve in un SDK di gestione separato .NET di Ricerca di Azure.

## Requisiti per l'SDK ##

1. Visual Studio 2013 o una versione più recente.

2. Un servizio di Ricerca di Azure. Per utilizzare l'SDK, è necessario il nome del servizio e una o più chiavi API. [Creare un servizio nel portale](search-create-service-portal.md) per eseguire facilmente questi passaggi.

3. Scaricare il [pacchetto NuGet](http://www.nuget.org/packages/Microsoft.Azure.Search) di SDK .NET di Ricerca di Azure tramite "Gestisci pacchetti NuGet" in Visual Studio. Cercare il nome del pacchetto `Microsoft.Azure.Search` su NuGet.org. Assicurarsi di selezionare "Includi versione provvisoria" per garantire che la versione provvisoria dell'SDK venga visualizzata nei risultati della ricerca.

La ricerca di .NET SDK di Ricerca di Azure supporta applicazioni destinate a .NET Framework 4.0 o versione successiva, nonché applicazioni Windows Store destinate a Windows 8.1 e Windows Phone 8.1. Silverlight non è supportato.

## Scenari chiave ##

Esistono diverse operazioni che è necessario eseguire nell'applicazione di ricerca. In questa esercitazione saranno illustrati questi scenari chiave:

- Creazione di un indice
- Popolamento dell’indice con i documenti
- Ricerca di documenti mediante filtri e ricerca con testo completo

Il codice di esempio che segue illustra ciascuna di queste operazioni. È possibile utilizzare i frammenti di codice nell'applicazione.

### Panoramica ###

L'applicazione di esempio crea un nuovo indice denominato "hotels", vi inserisce alcuni documenti, quindi esegue alcune query di ricerca. Ecco il programma principale che mostra il flusso generale:

    // This sample shows how to delete, create, upload documents and query an index
    static void Main(string[] args)
    {
        // Put your search service name here. This is the hostname portion of your service URL.
        // For example, if your service URL is https://myservice.search.windows.net, then your
        // service name is myservice.
        string searchServiceName = "myservice";

        string apiKey = "Put your API admin key here."

        SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));

        Console.WriteLine("{0}", "Deleting index...\n");
        DeleteHotelsIndexIfExists(serviceClient);

        Console.WriteLine("{0}", "Creating index...\n");
        CreateHotelsIndex(serviceClient);

        SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

        Console.WriteLine("{0}", "Uploading documents...\n");
        UploadDocuments(indexClient);

        Console.WriteLine("{0}", "Searching documents 'fancy wifi'...\n");
        SearchDocuments(indexClient, searchText: "fancy wifi");

        Console.WriteLine("\n{0}", "Filter documents with category 'Luxury'...\n");
        SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

        Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
        Console.ReadKey();
    }

La procedura verrà illustrata passo per passo. In primo luogo è necessario creare un nuovo `SearchServiceClient`. Questo oggetto consente di gestire gli indici. Per creare uno, è necessario fornire il nome del servizio Ricerca di Azure, nonché una chiave API di amministrazione.

        // Put your search service name here. This is the hostname portion of your service URL.
        // For example, if your service URL is https://myservice.search.windows.net, then your
        // service name is myservice.
        string searchServiceName = "myservice";

        string apiKey = "Put your API admin key here."

        SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));

> [AZURE.NOTE]Se si fornisce una chiave non corretta (ad esempio una chiave di query dove era richiesta una chiave di amministrazione), `SearchServiceClient` invierà un `CloudException` con il messaggio di errore "Non consentito" la prima volta che si chiama un metodo di operazione, ad esempio `Indexes.Create`. In questo caso, eseguire una doppia verifica della chiave API.

Le righe successive chiamano i metodi per creare un indice denominato "hotels", eliminandolo prima se esiste già. Tali metodi saranno illustrati più avanti.

        Console.WriteLine("{0}", "Deleting index...\n");
        DeleteHotelsIndexIfExists(serviceClient);

        Console.WriteLine("{0}", "Creating index...\n");
        CreateHotelsIndex(serviceClient);

Successivamente, l'indice deve essere popolato. A tale scopo, è necessario un `SearchIndexClient`. Esistono due modi per ottenere uno: creandolo oppure chiamando `Indexes.GetClient` sul `SearchServiceClient`. Per comodità, sarà utilizzato il secondo.

        SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

> [AZURE.NOTE]In un'applicazione di ricerca tipica, il popolamento e la gestione degli indici viene gestita da un componente separato dalle query di ricerca. `Indexes.GetClient` è utile per il popolamento di un indice perché evita la necessità di fornire un altro `SearchCredentials`. Questa operazione viene eseguita passando la chiave di amministrazione utilizzata per creare il `SearchServiceClient` al nuovo `SearchIndexClient`. Tuttavia, nella parte dell'applicazione che esegue le query, è preferibile creare il `SearchIndexClient` direttamente in modo che sia possibile passare una chiave di query anziché una chiave di amministrazione. Questo procedimento è coerente con il principio del privilegio minimo e consente di rendere più sicura l'applicazione. È possibile trovare ulteriori informazioni su chiavi di amministrazione e chiavi di query [qui](https://msdn.microsoft.com/library/azure/dn798935.aspx).

Ora che è disponibile un `SearchIndexClient`, è possibile popolare l'indice. A tal fine verrà utilizzato un altro metodo che verrà descritto più avanti.

        Console.WriteLine("{0}", "Uploading documents...\n");
        UploadDocuments(indexClient);

Infine, saranno eseguite alcune query di ricerca e visualizzati i risultati, utilizzando di nuovo il `SearchIndexClient`:

        Console.WriteLine("{0}", "Searching documents 'fancy wifi'...\n");
        SearchDocuments(indexClient, searchText: "fancy wifi");

        Console.WriteLine("\n{0}", "Filter documents with category 'Luxury'...\n");
        SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

        Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
        Console.ReadKey();

Se si esegue questa applicazione con un nome di servizio valido e la chiave API, l'output dovrebbe apparire simile al seguente:

    Deleting index...

    Creating index...

    Uploading documents...

    Searching documents 'fancy wifi'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 956-532     Name: Express Rooms     Category: Budget        Tags: [wifi, budget]

    Filter documents with category 'Luxury'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 566-518     Name: Surprisingly Expensive Suites     Category: Luxury        Tags: []
    Complete.  Press any key to end application...

Alla fine di questo articolo viene fornito il codice sorgente completo dell'applicazione.

Successivamente, verrà esaminato ognuno dei metodi chiamati da `Main`.

### Creazione di un indice ###

Dopo aver creato un `SearchServiceClient`, l'operazione successiva `Main` è l’eliminazione dell’indice "hotel" se esiste già. Questa operazione viene eseguita con il metodo seguente:

    private static void DeleteHotelsIndexIfExists(SearchServiceClient serviceClient)
    {
        if (serviceClient.Indexes.Exists("hotels"))
        {
            serviceClient.Indexes.Delete("hotels");
        }
    }

Questo metodo utilizza il `SearchServiceClient` fornito per verificare l'esistenza dell'indice ed eventualmente eliminarlo.

> [AZURE.NOTE]Il codice di esempio riportato in questo articolo utilizza i metodi sincroni di .NET SDK di Ricerca di Azure per motivi di semplicità. È consigliabile utilizzare i metodi asincroni nelle proprie applicazioni per mantenerle scalabili e reattive. Ad esempio, nel metodo precedente è possibile utilizzare `ExistsAsync` e `DeleteAsync` anziché `Exists` e `Delete`.

Successivamente, `Main` crea un nuovo indice "hotel" chiamando questo metodo:

    private static void CreateHotelsIndex(SearchServiceClient serviceClient)
    {
        var definition = new Index()
        {
            Name = "hotels",
            Fields = new[]
            {
                new Field("hotelId", DataType.String)                       { IsKey = true },
                new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true },
                new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true },
                new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
                new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
                new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
            }
        };

        serviceClient.Indexes.Create(definition);
    }

Questo metodo crea un nuovo oggetto `Index` con un elenco di oggetti `Field` che definisce lo schema del nuovo indice. Ogni campo ha un nome, un tipo di dati e diversi attributi che definiscono il comportamento della ricerca. Oltre ai campi, è possibile aggiungere anche profili di punteggio, suggerimenti di alternative o opzioni CORS per l'indice. Per motivi di brevità nell’esempio questi elementi sono stati omessi. È possibile trovare ulteriori informazioni sull'oggetto indice e le relative parti costituenti nel riferimento a SDK su [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.index_members.aspx), e nel [riferimento all'API REST di Ricerca di Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx).

### Popolamento dell'indice ###

Il passaggio successivo in `Main` è il popolamento l'indice appena creato. Questa operazione viene eseguita nel metodo seguente:

    private static void UploadDocuments(SearchIndexClient indexClient)
    {
        var documents =
            new Hotel[]
            {
                new Hotel()
                { 
                    HotelId = "1058-441", 
                    HotelName = "Fancy Stay", 
                    BaseRate = 199.0, 
                    Category = "Luxury", 
                    Tags = new[] { "pool", "view", "concierge" }, 
                    ParkingIncluded = false, 
                    LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero), 
                    Rating = 5, 
                    Location = GeographyPoint.Create(47.678581, -122.131577)
                },
                new Hotel()
                { 
                    HotelId = "666-437", 
                    HotelName = "Roach Motel",
                    BaseRate = 79.99,
                    Category = "Budget",
                    Tags = new[] { "motel", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                    Rating = 1,
                    Location = GeographyPoint.Create(49.678581, -122.131577)
                },
                new Hotel() 
                { 
                    HotelId = "970-501", 
                    HotelName = "Econo-Stay",
                    BaseRate = 129.99,
                    Category = "Budget",
                    Tags = new[] { "pool", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4,
                    Location = GeographyPoint.Create(46.678581, -122.131577)
                },
                new Hotel()
                { 
                    HotelId = "956-532", 
                    HotelName = "Express Rooms",
                    BaseRate = 129.99,
                    Category = "Budget",
                    Tags = new[] { "wifi", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4,
                    Location = GeographyPoint.Create(48.678581, -122.131577)
                },
                new Hotel() 
                { 
                    HotelId = "566-518", 
                    HotelName = "Surprisingly Expensive Suites",
                    BaseRate = 279.99,
                    Category = "Luxury",
                    ParkingIncluded = false
                }
            };

        try
        {
            indexClient.Documents.Index(IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc))));
        }
        catch (IndexBatchException e)
        {
            // Sometimes when your Search service is under load, indexing will fail for some of the documents in
            // the batch. Depending on your application, you can take compensating actions like delaying and
            // retrying. For this simple demo, we just log the failed document keys and continue.
            Console.WriteLine(
                "Failed to index some of the documents: {0}",
                String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
        }

        // Wait a while for indexing to complete.
        Thread.Sleep(2000);
    }

Questo metodo è costituito da quattro parti. La prima crea una matrice di oggetti `Hotel` che verranno utilizzati come dati di input per caricare l'indice. Questi dati sono hardcoded per motivi di semplicità. Nell'applicazione, i dati probabilmente proverranno da un'origine dati esterna, ad esempio un database SQL.

La seconda parte crea un `IndexAction` per ogni `Hotel`, quindi li raggruppa insieme in un nuovo `IndexBatch`. Il batch viene quindi caricato nell'indice di Ricerca di Azure dal metodo `Documents.Index`.

> [AZURE.NOTE]In questo esempio, verranno semplicemente caricati i documenti. Se si desidera unire le modifiche in un documento esistente o eliminare un documento, è possibile creare un `IndexAction` con il corrispondente `IndexActionType`. Non è necessario specificare `IndexActionType` in questo esempio perché il valore predefinito è `Upload`.

La terza parte di questo metodo è un blocco catch che gestisce un caso di errore importante per l'indicizzazione. Se il servizio Ricerca di Azure non riesce a indicizzare alcuni dei documenti nel batch, viene generato un `IndexBatchException` da `Documents.Index`. Questa situazione può verificarsi se l'indicizzazione dei documenti avviene mentre il servizio è sovraccarico. **Si consiglia di gestire in modo esplicito questo caso nel codice.** È possibile ritardare e quindi ritentare l'indicizzazione di documenti, accedere e continuare come nell'esempio, oppure eseguire altre attività a seconda dei requisiti di coerenza di dati dell'applicazione.

Infine, il metodo ritarda per due secondi. L'indicizzazione avviene in modo asincrono nel servizio Ricerca di Azure, pertanto l'applicazione di esempio deve attendere un breve periodo per garantire che i documenti siano disponibili per la ricerca. Ritardi come questi in genere sono necessari solo in applicazioni di esempio, test e demo.

#### Modalità di gestione dei documenti in .NET SDK ####

Per capire in che modo .NET SDK di Ricerca di Azure è in grado di caricare le istanze di una classe definita dall'utente come `Hotel` nell'indice, è possibile esaminare la classe `Hotel`:

    [SerializePropertyNamesAsCamelCase]
    public class Hotel
    {
        public string HotelId { get; set; }

        public string HotelName { get; set; }

        public double? BaseRate { get; set; }

        public string Category { get; set; }

        public string[] Tags { get; set; }

        public bool? ParkingIncluded { get; set; }

        public DateTimeOffset? LastRenovationDate { get; set; }

        public int? Rating { get; set; }

        public GeographyPoint Location { get; set; }

        public override string ToString()
        {
            return String.Format(
                "ID: {0}\tName: {1}\tCategory: {2}\tTags: [{3}]",
                HotelId,
                HotelName,
                Category,
                (Tags != null) ? String.Join(", ", Tags) : String.Empty);
        }
    }

La prima cosa da notare è che ogni proprietà pubblica di `Hotel` corrisponde a un campo nella definizione dell'indice, ma con una differenza fondamentale: il nome di ogni campo inizia con una lettera minuscola ("convenzione camel"), mentre il nome di ogni proprietà pubblica di `Hotel` inizia con una lettera maiuscola ("convenzione Pascal"). Si tratta di uno scenario comune in applicazioni .NET che consentono di eseguire l'associazione dati in cui lo schema di destinazione è fuori dal controllo dello sviluppatore dell'applicazione. Anziché dover violare linee guida sulla denominazione di .NET seguendo la convenzione camel per i nomi delle proprietà, è possibile indicare a SDK di eseguire automaticamente il mapping dei nomi delle proprietà alla convenzione camel con l’attributo `[SerializePropertyNamesAsCamelCase]`.

Il secondo aspetto importante della classe `Hotel` è costituita dai tipi di dati delle proprietà pubbliche. Viene eseguito il mapping dei tipi .NET di queste proprietà ai tipi di campi equivalenti nella definizione dell'indice. Ad esempio, viene eseguito il mapping della proprietà stringa `Category` al campo `category`, che è di tipo `Edm.String`. Esistono mapping di tipi simili tra `bool?` e `Edm.Boolean`, `DateTimeOffset?` e `Edm.DateTimeOffset` ecc. Le regole specifiche per il mapping dei tipi sono documentate con il metodo `Documents.Get` su [MSDN](https://msdn.microsoft.com/library/azure/dn931291.aspx). Si noti che i tipi di valore come `bool` e `int` ammettono valori null nella classe `Hotel` perché tutti i tipi primitivi di campo in Ricerca di Azure ammettono valori null.

La possibilità di utilizzare le proprie classi come documenti funziona in entrambe le direzioni; è possibile, inoltre, recuperare i risultati della ricerca e far sì che SDK li deserializzi automaticamente in un tipo di propria scelta, come sarà illustrato nella prossima sezione.

> [AZURE.NOTE].NET SDK di Ricerca di Azure supporta anche documenti tipizzati in modo dinamico utilizzando la classe `Document`, un mapping chiave/valore dei campi e valori dei campi. Ciò è utile negli scenari in cui non si conosce lo schema di indice in fase di progettazione o quando l’associazione a classi di modello specifico non sarebbe conveniente. Tutti i metodi in SDK che gestiscono documenti dispongono di overload che funzionano con la classe `Document`, nonché overload fortemente tipizzati che accettano un parametro di tipo generico. Solo questi ultimi vengono utilizzati nell'esempio di codice fornito in questa esercitazione. È possibile trovare informazioni sulla classe `Document` [qui](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.document.aspx).

### Ricerca di documenti nell'indice ###

L'ultimo passaggio nell'applicazione di esempio è cercare alcuni documenti nell'indice. A tale scopo viene utilizzato il metodo seguente:

    private static void SearchDocuments(SearchIndexClient indexClient, string searchText, string filter = null)
    {
        // Execute search based on search text and optional filter
        var sp = new SearchParameters();

        if (!String.IsNullOrEmpty(filter))
        {
            sp.Filter = filter;
        }

        DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
        foreach (SearchResult<Hotel> result in response)
        {
            Console.WriteLine(result.Document);
        }
    }

In primo luogo, questo metodo crea un nuovo oggetto `SearchParameters`. Consente di specificare opzioni aggiuntive per la query di ordinamento, filtro, impaginazione e faceting. In questo esempio, sarà impostata solo la proprietà `Filter`.

Il passaggio successivo consiste nell’esecuzione effettiva della query di ricerca. Questa operazione viene effettuata con il metodo `Documents.Search`. In questo caso, è possibile passare il testo di ricerca da utilizzare come stringa più i parametri di ricerca creati in precedenza. Viene inoltre specificato `Hotel` come parametro di tipo per `Documents.Search`, che indica all'SDK di deserializzare i documenti nei risultati della ricerca in oggetti di tipo `Hotel`.

Infine, questo metodo scorre tutte le corrispondenze nei risultati della ricerca, stampando ogni documento nella console.

Si osservi il modo in cui viene chiamato questo metodo:

    SearchDocuments(indexClient, searchText: "fancy wifi");

    SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

Nella prima chiamata, si cercano tutti i documenti che contengono i termini di query "fancy" o "wifi". Nella seconda chiamata, il testo di ricerca è impostato su "*", che significa "trova tutti gli elementi". È possibile trovare ulteriori informazioni sulla sintassi delle espressioni di query di ricerca [qui](https://msdn.microsoft.com/library/azure/dn798920.aspx).

La seconda chiamata utilizza l’espressione `$filter` OData, `category eq 'Luxury'`. Vincola la ricerca in modo che vengano restituiti solo i documenti di cui il campo `category` corrisponde esattamente alla stringa "Luxury". È possibile trovare informazioni sulla sintassi OData supportata da Ricerca di Azure [qui](https://msdn.microsoft.com/library/azure/dn798921.aspx).

A questo punto, conoscendo il funzionamento di queste due chiamate, è più semplice capire perché l'output è simile al seguente:

    Searching documents 'fancy wifi'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 956-532     Name: Express Rooms     Category: Budget        Tags: [wifi, budget]

    Filter documents with category 'Luxury'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 566-518     Name: Surprisingly Expensive Suites     Category: Luxury        Tags: []

La prima ricerca restituisce due documenti. Il primo contiene "Fancy" nel nome, mentre il secondo contiene "wifi" nel campo `tags`. La seconda ricerca restituisce due documenti, che sono i soli documenti nell'indice contenenti il campo `category` impostato su "Luxury".

Questo passaggio completa l'esercitazione. In **Passaggi successivi** vengono fornite risorse aggiuntive per ottenere ulteriori informazioni su Ricerca di Azure.

## Passaggi successivi ##

- Approfondire l'argomento tramite [video e altri esempi ed esercitazioni](https://msdn.microsoft.com/library/azure/dn818681.aspx).
- Reperire informazioni sulle caratteristiche e le funzionalità di questa versione di SDK di Ricerca di Azure: [Panoramica di Ricerca di Azure](https://msdn.microsoft.com/library/azure/dn798933.aspx)
- Rivedere le [convenzioni di denominazione](https://msdn.microsoft.com/library/azure/dn857353.aspx) per informazioni sulle regole per la denominazione dei vari oggetti.
- Rivedere i [tipi di dati supportati](https://msdn.microsoft.com/library/azure/dn798938.aspx) in Ricerca di Azure.


## Codice sorgente dell’applicazione di esempio ##

Di seguito viene riportato il codice sorgente completo dell'applicazione di esempio utilizzata in questo percorso. Si noti che per compilare ed eseguire l'esempio è necessario sostituire il nome del servizio e i segnaposto di chiavi API in Program.cs con valori personalizzati.

Program.cs

    using System;
    using System.Configuration;
    using System.Linq;
    using System.Threading;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Spatial;

    namespace AzureSearch.NETSDKSample
    {
        class Program
        {
            // This sample shows how to delete, create, upload documents and query an index
            static void Main(string[] args)
            {
                // Put your search service name here. This is the hostname portion of your service URL.
                // For example, if your service URL is https://myservice.search.windows.net, then your
                // service name is myservice.
                string searchServiceName = "myservice";

                string apiKey = "Put your API admin key here."

                SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));

                Console.WriteLine("{0}", "Deleting index...\n");
                DeleteHotelsIndexIfExists(serviceClient);

                Console.WriteLine("{0}", "Creating index...\n");
                CreateHotelsIndex(serviceClient);

                SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

                Console.WriteLine("{0}", "Uploading documents...\n");
                UploadDocuments(indexClient);

                Console.WriteLine("{0}", "Searching documents 'fancy wifi'...\n");
                SearchDocuments(indexClient, searchText: "fancy wifi");

                Console.WriteLine("\n{0}", "Filter documents with category 'Luxury'...\n");
                SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

                Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
                Console.ReadKey();
            }

            private static void DeleteHotelsIndexIfExists(SearchServiceClient serviceClient)
            {
                if (serviceClient.Indexes.Exists("hotels"))
                {
                    serviceClient.Indexes.Delete("hotels");
                }
            }

            private static void CreateHotelsIndex(SearchServiceClient serviceClient)
            {
                var definition = new Index()
                {
                    Name = "hotels",
                    Fields = new[]
                    {
                        new Field("hotelId", DataType.String)                       { IsKey = true },
                        new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true },
                        new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true },
                        new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
                        new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
                        new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
                        new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
                        new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
                        new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
                    }
                };

                serviceClient.Indexes.Create(definition);
            }

            private static void UploadDocuments(SearchIndexClient indexClient)
            {
                var documents =
                    new Hotel[]
                    {
                        new Hotel()
                        { 
                            HotelId = "1058-441", 
                            HotelName = "Fancy Stay", 
                            BaseRate = 199.0, 
                            Category = "Luxury", 
                            Tags = new[] { "pool", "view", "concierge" }, 
                            ParkingIncluded = false, 
                            LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero), 
                            Rating = 5, 
                            Location = GeographyPoint.Create(47.678581, -122.131577)
                        },
                        new Hotel()
                        { 
                            HotelId = "666-437", 
                            HotelName = "Roach Motel",
                            BaseRate = 79.99,
                            Category = "Budget",
                            Tags = new[] { "motel", "budget" },
                            ParkingIncluded = true,
                            LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                            Rating = 1,
                            Location = GeographyPoint.Create(49.678581, -122.131577)
                        },
                        new Hotel() 
                        { 
                            HotelId = "970-501", 
                            HotelName = "Econo-Stay",
                            BaseRate = 129.99,
                            Category = "Budget",
                            Tags = new[] { "pool", "budget" },
                            ParkingIncluded = true,
                            LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                            Rating = 4,
                            Location = GeographyPoint.Create(46.678581, -122.131577)
                        },
                        new Hotel()
                        { 
                            HotelId = "956-532", 
                            HotelName = "Express Rooms",
                            BaseRate = 129.99,
                            Category = "Budget",
                            Tags = new[] { "wifi", "budget" },
                            ParkingIncluded = true,
                            LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                            Rating = 4,
                            Location = GeographyPoint.Create(48.678581, -122.131577)
                        },
                        new Hotel() 
                    {
                            HotelId = "566-518", 
                            HotelName = "Surprisingly Expensive Suites",
                            BaseRate = 279.99,
                            Category = "Luxury",
                            ParkingIncluded = false
                        }
                    };

                try
                {
                    indexClient.Documents.Index(IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc))));
                }
                catch (IndexBatchException e)
                {
                    // Sometimes when your Search service is under load, indexing will fail for some of the documents in
                    // the batch. Depending on your application, you can take compensating actions like delaying and
                    // retrying. For this simple demo, we just log the failed document keys and continue.
                    Console.WriteLine(
                        "Failed to index some of the documents: {0}",
                        String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
                }

                // Wait a while for indexing to complete.
                Thread.Sleep(2000);
            }

            private static void SearchDocuments(SearchIndexClient indexClient, string searchText, string filter = null)
            {
                // Execute search based on search text and optional filter
                var sp = new SearchParameters();

                if (!String.IsNullOrEmpty(filter))
                {
                    sp.Filter = filter;
                }

                DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
                foreach (SearchResult<Hotel> result in response)
                {
                    Console.WriteLine(result.Document);
                }
            }
        }
    }

Hotel.cs:

    using System;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Spatial;

    namespace AzureSearch.NETSDKSample
    {
        [SerializePropertyNamesAsCamelCase]
        public class Hotel
        {
            public string HotelId { get; set; }

            public string HotelName { get; set; }

            public double? BaseRate { get; set; }

            public string Category { get; set; }

            public string[] Tags { get; set; }

            public bool? ParkingIncluded { get; set; }

            public DateTimeOffset? LastRenovationDate { get; set; }

            public int? Rating { get; set; }

            public GeographyPoint Location { get; set; }

            public override string ToString()
            {
                return String.Format(
                    "ID: {0}\tName: {1}\tCategory: {2}\tTags: [{3}]",
                    HotelId,
                    HotelName,
                    Category,
                    (Tags != null) ? String.Join(", ", Tags) : String.Empty);
            }
        }
    }
 

<!---HONumber=August15_HO6-->