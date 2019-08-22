---
title: 'Guida di avvio rapido per C#: Creare e caricare un indice ed eseguirvi query con .NET SDK - Ricerca di Azure'
description: Informazioni su come creare un indice, caricare dati ed eseguire query con C# e .NET SDK di Ricerca di Azure.
author: heidisteen
manager: nitinme
ms.author: heidist
tags: azure-portal
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/11/2019
ms.openlocfilehash: 6a77ecd91c0d04f0b449d25686578784921987bf
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69649806"
---
# <a name="quickstart-create-an-azure-search-index-in-c-using-the-net-sdk"></a>Guida introduttiva: Creare un indice di Ricerca di Azure in C# con .NET SDK
> [!div class="op_single_selector"]
> * [C#](search-get-started-dotnet.md)
> * [Portale](search-get-started-portal.md)
> * [PowerShell](search-create-index-rest-api.md)
> * [Python](search-get-started-python.md)
> * [Postman](search-get-started-postman.md)
>*

Per creare un'applicazione console C# per .NET Core che crea, carica ed esegue query su un indice di Ricerca di Azure, è possibile usare Visual Studio e [.NET SDK di Ricerca di Azure](https://aka.ms/search-sdk). Questo articolo illustra tutti i passaggi della creazione dell'applicazione. In alternativa, è possibile [scaricare ed eseguire l'applicazione completa](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/Quickstart).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

> [!NOTE]
> Per semplicità, nel codice di esempio incluso in questo articolo si usano i metodi sincroni di .NET SDK di Ricerca di Azure. Per scenari di produzione, è però consigliabile usare i metodi asincroni nelle proprie applicazioni per mantenerle scalabili e reattive. È ad esempio possibile usare `CreateAsync` e `DeleteAsync` invece di `Create` e `Delete`.

## <a name="prerequisites"></a>Prerequisiti

Per questa guida di avvio rapido sono richiesti i servizi e gli strumenti seguenti.

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/), qualsiasi edizione. Il codice di esempio e le istruzioni sono stati testati nell'edizione Community Edition gratuita.

+ [Creare un servizio Ricerca di Azure](search-create-service-portal.md) o [trovare un servizio esistente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) nella sottoscrizione corrente. È possibile usare un servizio gratuito per questo avvio rapido.

<a name="get-service-info"></a>

## <a name="get-a-key-and-url"></a>Ottenere una chiave e un URL

Le chiamate al servizio richiedono un URL endpoint e una chiave di accesso per ogni richiesta. Con entrambi gli elementi viene creato un servizio di ricerca, quindi se si è aggiunto Ricerca di Azure alla sottoscrizione, seguire questi passaggi per ottenere le informazioni necessarie:

1. [Accedere al portale di Azure](https://portal.azure.com/) e ottenere l'URL nella pagina **Panoramica** del servizio di ricerca. Un endpoint di esempio potrebbe essere simile a `https://mydemo.search.windows.net`.

2. In **Impostazioni** > **Chiavi** ottenere una chiave amministratore per diritti completi sul servizio. Sono disponibili due chiavi amministratore interscambiabili, fornite per continuità aziendale nel caso in cui sia necessario eseguire il rollover di una di esse. È possibile usare la chiave primaria o secondaria nelle richieste per l'aggiunta, la modifica e l'eliminazione di oggetti.

   Ottenere anche la chiave di query. È consigliabile inviare richieste di query con accesso di sola lettura.

![Ottenere una chiave di accesso e un endpoint HTTP](media/search-get-started-postman/get-url-key.png "Ottenere una chiave di accesso e un endpoint HTTP")

Per ogni richiesta inviata al servizio è necessario specificare una chiave API. La presenza di una chiave valida stabilisce una relazione di trust, in base alle singole richieste, tra l'applicazione che invia la richiesta e il servizio che la gestisce.

## <a name="set-up-your-environment"></a>Configurazione dell'ambiente

Iniziare aprendo Visual Studio e creando un nuovo progetto di app console che può essere eseguito in .NET Core.

### <a name="install-nuget-packages"></a>Installare i pacchetti NuGet

[.NET SDK di Ricerca di Azure](https://aka.ms/search-sdk) è costituito da alcune librerie client che vengono distribuite come pacchetti NuGet.

Per questo progetto usare la versione 9 del pacchetto NuGet `Microsoft.Azure.Search` e il pacchetto `Microsoft.Extensions.Configuration.Json` più recente.

1. In **Strumenti** > **Gestione pacchetti NuGet**, selezionare **Gestisci pacchetti NuGet per la soluzione**. 

1. Fare clic su **Sfoglia**.

1. Cercare `Microsoft.Azure.Search` e selezionare la versione 9.0.1 o successiva.

1. Fare clic su **Installa** a destra per aggiungere l'assembly al progetto e alla soluzione.

1. Ripetere l'operazione per `Microsoft.Extensions.Configuration.Json`, selezionando la versione 2.2.0 o successiva.


### <a name="add-azure-search-service-information"></a>Aggiungere informazioni sul servizio Ricerca di Azure

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto e selezionare **Aggiungi** > **Nuovo elemento**. 

1. In Aggiungi nuovo elemento cercare "JSON" per restituire un elenco di tipi di elemento correlati a JSON.

1. Scegliere **File JSON**, assegnare al file il nome "appsettings.json" e fare clic su **Aggiungi**. 

1. Aggiungere il file alla directory di output. Fare clic con il pulsante destro del mouse su appsettings.json e scegliere **Proprietà**. In **Copia nella directory di output** selezionare **Copia se più recente**.

1. Copiare il codice JSON seguente nel nuovo file JSON. Sostituire il nome del servizio di ricerca (YOUR-SEARCH-SERVICE-NAME) e la chiave API dell'amministratore (YOUR-ADMIN-API-KEY) con valori validi. Se l'endpoint servizio è `https://mydemo.search.windows.net`, il nome del servizio sarà "mydemo".

```json
{
  "SearchServiceName": "<YOUR-SEARCH-SERVICE-NAME>",
  "SearchServiceAdminApiKey": "<YOUR-ADMIN-API-KEY>",
  "SearchIndexName": "hotels-quickstart"
}
```

### <a name="add-class-method-files-to-your-project"></a>Aggiungere i file ".Method" della classe al progetto

Quando si stampano risultati nella finestra della console, i singoli campi dell'oggetto Hotel devono essere restituiti come stringhe. Per eseguire questa attività, è possibile implementare [ToString()](https://docs.microsoft.com/dotnet/api/system.object.tostring?view=netframework-4.8), copiando il codice necessario nei due nuovi file.

1. Aggiungere due definizioni di classe vuota al progetto: Address.Methods.cs, Hotel.Methods.cs

1. In Address.Methods.cs sovrascrivere il contenuto predefinito con il codice seguente alle [righe 1-32](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/Quickstart/AzureSearchQuickstart/Address.Methods.cs/#L1-L32).

1. In Hotel.Methods.cs copiare le [righe 1-66](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/Quickstart/AzureSearchQuickstart/Hotel.Methods.cs/#L1-L66).


## <a name="1---create-index"></a>1 - Creare l'indice

L'indice degli alberghi è costituito da campi semplici e complessi, in cui i campi semplici sono "HotelName" o "Description" e i campi complessi sono un indirizzo con campi secondari oppure una raccolta di camere. Quando un indice include i tipi complessi, isolare le definizioni di campo complesse in classi separate.

1. Aggiungere due definizioni di classe vuota al progetto: Address.cs, Hotel.cs

1. In Address.cs sovrascrivere il contenuto predefinito con il codice seguente:

    ```csharp
    using System;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Newtonsoft.Json;

    namespace AzureSearchQuickstart
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

1. In Hotel.cs la classe definisce la struttura generale dell'indice, inclusi i riferimenti alla classe dell'indirizzo.

    ```csharp
    namespace AzureSearchQuickstart
    {
        using System;
        using Microsoft.Azure.Search;
        using Microsoft.Azure.Search.Models;
        using Newtonsoft.Json;

        public partial class Hotel
        {
            [System.ComponentModel.DataAnnotations.Key]
            [IsFilterable]
            public string HotelId { get; set; }

            [IsSearchable, IsSortable]
            public string HotelName { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
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

            [IsFilterable, IsSortable, IsFacetable]
            public DateTimeOffset? LastRenovationDate { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public double? Rating { get; set; }

            public Address Address { get; set; }
        }
    }
    ```

    Gli attributi nel campo ne determinano l'uso in un'applicazione. Ad esempio, l'attributo `IsSearchable` deve essere assegnato a ogni campo che deve essere incluso in una ricerca full-text. 
    
    > [!NOTE]
    > In .NET SDK i campi devono essere attribuiti in modo esplicito come [`IsSearchable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.issearchable?view=azure-dotnet), [`IsFilterable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfilterable?view=azure-dotnet), [`IsSortable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.issortable?view=azure-dotnet) e [`IsFacetable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfacetable?view=azure-dotnet). Questo comportamento si differenzia dall'API REST che consente implicitamente l'attribuzione in base al tipo di dati (ad esempio, i campi stringa semplici sono automaticamente disponibili per la ricerca).

    Esattamente un campo di tipo `string` nell'indice deve essere il campo *chiave*, che identifica in modo univoco ogni documento. In questo schema la chiave è `HotelId`.

    In questo indice i campi della descrizione usano la proprietà facoltativa [`analyzer`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.analyzer?view=azure-dotnet), specificata quando si vuole sostituire l'analizzatore standard predefinito Lucene. Il campo `description_fr` usa l'analizzatore Lucene per il francese ([FrLucene](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername.frlucene?view=azure-dotnet)) perché archivia testo in francese. Il campo `description` usa l'analizzatore di lingua Microsoft facoltativo ([EnMicrosoft](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername.enmicrosoft?view=azure-dotnet)).

1. In Program.cs creare un'istanza della classe [`SearchServiceClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient?view=azure-dotnet) da connettere al servizio, usando valori archiviati nel file config dell'applicazione (appsettings.json). 

   `SearchServiceClient` include una proprietà [`Indexes`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexes?view=azure-dotnet), che fornisce tutti i metodi necessari per creare, elencare, aggiornare o eliminare indici di Ricerca di Azure. 

    ```csharp
    using System;
    using System.Linq;
    using System.Threading;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Extensions.Configuration;

    namespace AzureSearchQuickstart
    {
        class Program
            // Demonstrates index delete, create, load, and query
            // Commented-out code is uncommented in later steps
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

                // Uncomment next 3 lines in "2 - Load documents"
                // ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
                // Console.WriteLine("{0}", "Uploading documents...\n");
                // UploadDocuments(indexClient);

                // Uncomment next 2 lines in "3 - Search an index"
                // Console.WriteLine("{0}", "Searching index...\n");
                // RunQueries(indexClient);

                Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
                Console.ReadKey();
            }

            // Create the search service client
            private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
            {
                string searchServiceName = configuration["SearchServiceName"];
                string adminApiKey = configuration["SearchServiceAdminApiKey"];

                SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
                return serviceClient;
            }

            // Delete an existing index to reuse its name
            private static void DeleteIndexIfExists(string indexName, SearchServiceClient serviceClient)
            {
                if (serviceClient.Indexes.Exists(indexName))
                {
                    serviceClient.Indexes.Delete(indexName);
                }
            }

            // Create an index whose fields correspond to the properties of the Hotel class.
            // The Address property of Hotel will be modeled as a complex field.
            // The properties of the Address class in turn correspond to sub-fields of the Address complex field.
            // The fields of the index are defined by calling the FieldBuilder.BuildForType() method.
            private static void CreateIndex(string indexName, SearchServiceClient serviceClient)
            {
                var definition = new Index()
                {
                    Name = indexName,
                    Fields = FieldBuilder.BuildForType<Hotel>()
                };

                serviceClient.Indexes.Create(definition);
            }
        }
    }    
    ```

    Per evitare l'apertura di un numero eccessivo di connessioni, se possibile condividere una singola istanza di `SearchServiceClient` nell'applicazione. I metodi della classe per abilitare tale condivisione sono thread-safe.

   La classe include diversi costruttori. Il costruttore in uso accetta il nome del servizio di ricerca e un oggetto `SearchCredentials` come parametri. `SearchCredentials` esegue il wrapping della chiave API.

    Nella definizione dell'indice il modo più semplice per creare gli oggetti `Field` consiste nel chiamare il metodo `FieldBuilder.BuildForType` passando una classe modello per il parametro di tipo. Una classe modello dispone di proprietà corrispondenti ai campi dell'indice. Questo mapping consente di associare documenti dell'indice di ricerca a istanze della classe modello.

    > [!NOTE]
    > Se non si prevede di usare una classe modello, è comunque possibile definire l'indice mediante la creazione diretta di oggetti `Field`. È possibile fornire il nome del campo al costruttore, insieme al tipo di dati (o all'analizzatore per i campi stringa). È inoltre possibile impostare altre proprietà, come ad esempio `IsSearchable` e `IsFilterable`.
    >

1. Premere F5 per compilare l'app e creare l'indice. 

    Se la compilazione del progetto viene completata, viene aperta una finestra della console in cui vengono scritti messaggi di stato relativi all'eliminazione e alla creazione dell'indice. 

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - Caricare i documenti

In Ricerca di Azure i documenti sono strutture dei dati che costituiscono sia l'input per l'indicizzazione che l'output restituito dalle query. In quanto ottenuti da un'origine dati esterna, gli input dei documenti possono essere righe in un database, BLOB nell'archiviazione BLOB o documenti JSON nel disco. Per brevità, in questo esempio i documenti JSON per i quattro alberghi verranno incorporati nel codice stesso. 

Quando si caricano documenti, è necessario usare un oggetto [`IndexBatch`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet). Un oggetto `IndexBatch` contiene una raccolta di oggetti [`IndexAction`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet), ognuno dei quali contiene un documento e una proprietà che indica a Ricerca di Azure quale azione eseguire ([upload, merge, delete e mergeOrUpload](search-what-is-data-import.md#indexing-actions)).

1. In Program.cs creare una matrice di documenti e azioni di indice e quindi passarla a `IndexBatch`. I documenti seguenti sono conformi all'indice hotel-quickstart, come definito dalle classi hotel e address.

    ```csharp
    // Upload documents as a batch
    private static void UploadDocuments(ISearchIndexClient indexClient)
    {
        var actions = new IndexAction<Hotel>[]
        {
            IndexAction.Upload(
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
                }
            ),
            IndexAction.Upload(
                new Hotel()
                {
                    HotelId = "2",
                    HotelName = "Twin Dome Motel",
                    Description = "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "free wifi", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate =  new DateTimeOffset(1979, 2, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.60,
                    Address = new Address()
                    {
                        StreetAddress = "140 University Town Center Dr",
                        City = "Sarasota",
                        StateProvince = "FL",
                        PostalCode = "34243",
                        Country = "USA"
                    }
                }
            ),
            IndexAction.Upload(
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
                }
            ),
            IndexAction.Upload(
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
                    Rating = 4.6,
                    Address = new Address()
                    {
                        StreetAddress = "7400 San Pedro Ave",
                        City = "San Antonio",
                        StateProvince = "TX",
                        PostalCode = "78216",
                        Country = "USA"
                    }
                }
            ),
        };

        var batch = IndexBatch.New(actions);

        try
        {
            indexClient.Documents.Index(batch);
        }
        catch (IndexBatchException e)
        {
            // When a service is under load, indexing might fail for some documents in the batch. 
            // Depending on your application, you can compensate by delaying and retrying. 
            // For this simple demo, we just log the failed document keys and continue.
            Console.WriteLine(
                "Failed to index some of the documents: {0}",
                String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
        }

        // Wait 2 seconds before starting queries 
        Console.WriteLine("Waiting for indexing...\n");
        Thread.Sleep(2000);
    }
    ```

    Dopo aver inizializzato l'oggetto `IndexBatch`, è possibile inviarlo all'indice chiamando [`Documents.Index`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.index?view=azure-dotnet) sull'oggetto [`SearchIndexClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet). `Documents` è una proprietà di `SearchIndexClient` che fornisce metodi per aggiungere, modificare, eliminare o eseguire query su documenti nell'indice.

    Il blocco `try`/`catch` che racchiude la chiamata al metodo `Index` acquisisce gli errori di indicizzazione, che possono verificarsi se il servizio è soggetto a un carico notevole. Nel codice di produzione è possibile ritardare e quindi ripetere l'indicizzazione dei documenti non indicizzati oppure registrare l'errore e continuare, come avviene nell'esempio, o gestire l'errore in modo diverso, ma conforme ai requisiti di coerenza dei dati dell'applicazione.

    Il ritardo di due secondi compensa l'indicizzazione, che è asincrona, in modo che tutti i documenti possano essere indicizzati prima dell'esecuzione delle query. La scrittura di codice in un ritardo è in genere necessaria solo in applicazioni di esempio, test e demo.

1. Nel metodo main di Program.cs rimuovere il commento per le righe relative a "2 - Load documents". 

    ```csharp
    // Uncomment next 3 lines in "2 - Load documents"
    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(indexClient);
    ```
1. Premere F5 per ricompilare l'app. 

    Se la compilazione del progetto viene completata, viene aperta una finestra della console in cui vengono scritti messaggi di stato, questa volta relativi al caricamento dei documenti. Nella pagina **Panoramica** del servizio di ricerca nel portale di Azure l'indice hotels-quickstart dovrebbe ora includere quattro documenti.

Per altre informazioni sull'elaborazione di documenti, vedere ["Modalità di gestione dei documenti in .NET SDK"](search-howto-dotnet-sdk.md#how-dotnet-handles-documents).

## <a name="3---search-an-index"></a>3 - Eseguire la ricerca in un indice

È possibile ottenere risultati della query subito dopo l'indicizzazione del primo documento, ma per il test effettivo dell'indice è necessario attendere il completamento dell'indicizzazione di tutti i documenti. 

In questa sezione vengono aggiunte elementi di funzionalità, ovvero la logica di query e i risultati. Per le query usare il metodo [`Search`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.search?view=azure-dotnet
). Questo metodo accetta il testo da cercare nonché altri [parametri](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters?view=azure-dotnet). 

La classe [`DocumentsSearchResult`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1?view=azure-dotnet) rappresenta i risultati.


1. In Program.cs creare un metodo WriteDocuments che stampa i risultati della ricerca nella console.

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

1. Creare un metodo RunQueries per eseguire le query e restituire i risultati. I risultati sono oggetti Hotel. È possibile usare il parametro Select per esplorare singoli campi. Se un campo non è incluso nel parametro Select, la proprietà Hotel corrispondente sarà Null.

    ```csharp
    private static void RunQueries(ISearchIndexClient indexClient)
    {
        SearchParameters parameters;
        DocumentSearchResult<Hotel> results;

        // Query 1 
        Console.WriteLine("Query 1: Search for term 'Atlanta' with no result trimming");
        parameters = new SearchParameters();
        results = indexClient.Documents.Search<Hotel>("Atlanta", parameters);
        WriteDocuments(results);

        // Query 2
        Console.WriteLine("Query 2: Search on the term 'Atlanta', with trimming");
        Console.WriteLine("Returning only these fields: HotelName, Tags, Address:\n");
        parameters =
            new SearchParameters()
            {
                Select = new[] { "HotelName", "Tags", "Address" },
            };
        results = indexClient.Documents.Search<Hotel>("Atlanta", parameters);
        WriteDocuments(results);

        // Query 3
        Console.WriteLine("Query 3: Search for the terms 'restaurant' and 'wifi'");
        Console.WriteLine("Return only these fields: HotelName, Description, and Tags:\n");
        parameters =
            new SearchParameters()
            {
                Select = new[] { "HotelName", "Description", "Tags" }
            };
        results = indexClient.Documents.Search<Hotel>("restaurant, wifi", parameters);
        WriteDocuments(results);

        // Query 4 -filtered query
        Console.WriteLine("Query 4: Filter on ratings greater than 4");
        Console.WriteLine("Returning only these fields: HotelName, Rating:\n");
        parameters =
            new SearchParameters()
            {
                Filter = "Rating gt 4",
                Select = new[] { "HotelName", "Rating" }
            };
        results = indexClient.Documents.Search<Hotel>("*", parameters);
        WriteDocuments(results);

        // Query 5 - top 2 results
        Console.WriteLine("Query 5: Search on term 'boutique'");
        Console.WriteLine("Sort by rating in descending order, taking the top two results");
        Console.WriteLine("Returning only these fields: HotelId, HotelName, Category, Rating:\n");
        parameters =
            new SearchParameters()
            {
                OrderBy = new[] { "Rating desc" },
                Select = new[] { "HotelId", "HotelName", "Category", "Rating" },
                Top = 2
            };
        results = indexClient.Documents.Search<Hotel>("boutique", parameters);
        WriteDocuments(results);
    }
    ```

    Sono disponibili due [modi per cercare termini in una query](search-query-overview.md#types-of-queries): la ricerca full-text e i filtri. Una query di ricerca full-text esegue la ricerca di uno o più termini nei campi `IsSearchable` dell'indice. Un filtro è un'espressione booleana che viene valutata in base ai campi `IsFilterable` in un indice. È possibile usare le ricerche full-text e i filtri insieme o separatamente.

    Ricerche e filtri vengono eseguiti usando il metodo `Documents.Search` . Una query di ricerca può essere passata nel parametro `searchText`, mentre un'espressione di filtro può essere passata nella proprietà `Filter` della classe `SearchParameters`. Per filtrare senza eseguire ricerche, passare semplicemente `"*"` per il parametro `searchText`. Per eseguire una ricerca senza filtrare, lasciare la proprietà `Filter` non impostata oppure non passare un'istanza di `SearchParameters`.

1. Nel metodo main di Program.cs rimuovere il commento per le righe relative a "3 - Search". 

    ```csharp
    // Uncomment next 2 lines in "3 - Search an index"
    Console.WriteLine("{0}", "Searching documents...\n");
    RunQueries(indexClient);
    ```
1. La soluzione è stata completata. Premere F5 per ricompilare l'app ed eseguire il programma completo. 

    L'output include gli stessi messaggi restituiti in precedenza, con l'aggiunta di informazioni sulle query e risultati.

## <a name="clean-up"></a>Eseguire la pulizia

Quando si lavora nella propria sottoscrizione, alla fine di un progetto è opportuno verificare se le risorse create sono ancora necessarie. L'esecuzione continua delle risorse può avere un costo. È possibile eliminare le singole risorse oppure il gruppo di risorse per eliminare l'intero set di risorse.

Per trovare e gestire le risorse nel portale, usare il collegamento **Tutte le risorse** o **Gruppi di risorse** nel riquadro di spostamento a sinistra.

Se si usa un servizio gratuito, tenere presente che il numero di indicizzatori e origini dati è limitato a tre. Per non superare il limite, è possibile eliminare i singoli elementi nel portale. 

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido per C# sono state eseguite le attività per creare un indice, caricarvi documenti ed eseguire query. In diverse fasi sono state adottate delle scorciatoie per semplificare il codice e renderlo più leggibile e comprensibile. Se si conoscono già i concetti di base, è consigliabile leggere l'articolo successivo in cui vengono esaminati concetti e approcci alternativi per approfondire l'argomento. 

Il codice di esempio e l'indice forniti sono versioni espanse rispetto a quelli presentati in questo articolo. Nell'esempio successivo viene aggiunta una raccolta Rooms, vengono usate classi e azioni diverse e viene esaminato in modo approfondito il funzionamento dell'elaborazione.

> [!div class="nextstepaction"]
> [Come sviluppare in .NET](search-howto-dotnet-sdk.md)