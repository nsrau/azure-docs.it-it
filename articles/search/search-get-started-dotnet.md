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
ms.date: 08/05/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 7901254463ef052f3c13b2c9fc49c31bd8ebc454
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "89020865"
---
# <a name="quickstart-create-a-search-index-using-the-azuresearchdocuments-client-library"></a>Avvio rapido: Creare un indice di ricerca con la libreria client Azure.Search.Documents

Usare la nuova [libreria client Azure.Search.Documents (versione 11)](/dotnet/api/overview/azure/search.documents-readme?view=azure-dotnet) per creare un'applicazione console .NET Core in C# che crea e carica un indice di ricerca ed esegue query su di esso.

[Scaricare il codice sorgente](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart-v11) per iniziare con un progetto finito oppure seguire la procedura descritta in questo articolo per crearne uno.

> [!NOTE]
> Per una versione precedente, vedere invece [Creare un indice di ricerca con Microsoft.Azure.Search v10](search-get-started-dotnet-v10.md).

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, è necessario avere gli strumenti e i servizi seguenti:

+ Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/).

+ Un'istanza del servizio Ricerca cognitiva di Azure. [Creare un servizio](search-create-service-portal.md) o [trovarne uno esistente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). È possibile usare un servizio gratuito per questo avvio rapido. 

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/), qualsiasi edizione. Il codice di esempio è stato testato nell'edizione Community gratuita di Visual Studio 2019.

<a name="get-service-info"></a>

## <a name="get-a-key-and-endpoint"></a>Ottenere una chiave e un endpoint

Le chiamate al servizio richiedono un URL endpoint e una chiave di accesso per ogni richiesta. Con entrambi gli elementi viene creato un servizio di ricerca, quindi se il servizio Ricerca cognitiva di Azure è stato aggiunto alla sottoscrizione, seguire questi passaggi per ottenere le informazioni necessarie:

1. [Accedere al portale di Azure](https://portal.azure.com/) e ottenere l'URL nella pagina **Panoramica** del servizio di ricerca. Un endpoint di esempio potrebbe essere simile a `https://mydemo.search.windows.net`.

2. In **Impostazioni** > **Chiavi** ottenere una chiave amministratore per avere diritti completi sul servizio, necessari in caso di creazione o eliminazione di oggetti. Vengono visualizzate due chiavi intercambiabili: la chiave primaria e quella secondaria. È possibile usare una qualsiasi delle due.

   ![Ottenere un endpoint HTTP e una chiave di accesso](media/search-get-started-postman/get-url-key.png "Ottenere un endpoint HTTP e una chiave di accesso")

Per ogni richiesta inviata al servizio è necessario specificare una chiave API. La presenza di una chiave valida stabilisce una relazione di trust, in base alle singole richieste, tra l'applicazione che invia la richiesta e il servizio che la gestisce.

## <a name="set-up-your-project"></a>Configurare il progetto

Avviare Visual Studio e creare un nuovo progetto di app console eseguibile in .NET Core. 

### <a name="install-the-nuget-package"></a>Installare il pacchetto NuGet

Dopo aver creato il progetto, aggiungere la libreria client. Il [pacchetto Azure.Search.Documents](https://www.nuget.org/packages/Azure.Search.Documents/) è costituito da una libreria client che include tutte le API che consentono di usare un servizio di ricerca in .NET.

1. In **Strumenti** > **Gestione pacchetti NuGet**, selezionare **Gestisci pacchetti NuGet per la soluzione**. 

1. Fare clic su **Sfoglia**.

1. Cercare `Azure.Search.Documents` e selezionare la versione 11.0.0.

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

1. Creare due client. [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) crea l'indice, mentre [SearchClient](/dotnet/api/azure.search.documents.searchclient) usa un indice esistente. Per entrambi è necessario l'endpoint di servizio e una chiave API amministratore per l'autenticazione con diritti di creazione/eliminazione.

   ```csharp
   static void Main(string[] args)
   {
       string serviceName = "<YOUR-SERVICE-NAME>";
       string indexName = "hotels-quickstart-v11";
       string apiKey = "<YOUR-ADMIN-API-KEY>";

       // Create a SearchIndexClient to send create/delete index commands
       Uri serviceEndpoint = new Uri($"https://{serviceName}.search.windows.net/");
       AzureKeyCredential credential = new AzureKeyCredential(apiKey);
       SearchIndexClient idxclient = new SearchIndexClient(serviceEndpoint, credential);

       // Create a SearchClient to load and query documents
       SearchClient qryclient = new SearchClient(serviceEndpoint, indexName, credential);
    ```

## <a name="1---create-an-index"></a>1 - Creare un indice

Questa guida di avvio rapido compila un indice di hotel in cui si caricheranno i dati relativi agli hotel e si eseguiranno query. In questo passaggio definire i campi dell'indice. Ogni definizione di campo include un nome, un tipo di dati e gli attributi che determinano come viene usato il campo.

In questo esempio, per una maggiore leggibilità e semplicità vengono usati i metodi sincroni della libreria Azure.Search.Documents. Per scenari di produzione, tuttavia, è consigliabile usare metodi asincroni per mantenere la scalabilità e la reattività dell'app. Ad esempio, usare [CreateIndexAsync](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindexasync) invece di [CreateIndex](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex).

1. Aggiungere una definizione di classe vuota al progetto: **Hotel.cs**.

1. In **Hotel.cs** definire la struttura di un documento di un hotel.

    ```csharp
    using System;
    using System.Text.Json.Serialization;

    namespace AzureSearch.SDK.Quickstart.v11
    {
        public class Hotel
        {
            [JsonPropertyName("hotelId")]
            public string Id { get; set; }

            [JsonPropertyName("hotelName")]
            public string Name { get; set; }

            [JsonPropertyName("hotelCategory")]
            public string Category { get; set; }

            [JsonPropertyName("baseRate")]
            public Int32 Rate { get; set; }

            [JsonPropertyName("lastRenovationDate")]
            public DateTime Updated { get; set; }
        }
    }
    ```

1. In **Program.cs** specificare i campi e gli attributi. Per creare un indice vengono usati [SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex) e [CreateIndex](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex).

   ```csharp
    // Define an index schema using SearchIndex
    // Create the index using SearchIndexClient
    SearchIndex index = new SearchIndex(indexName)
    {
        Fields =
            {
                new SimpleField("hotelId", SearchFieldDataType.String) { IsKey = true, IsFilterable = true, IsSortable = true },
                new SearchableField("hotelName") { IsFilterable = true, IsSortable = true },
                new SearchableField("hotelCategory") { IsFilterable = true, IsSortable = true },
                new SimpleField("baseRate", SearchFieldDataType.Int32) { IsFilterable = true, IsSortable = true },
                new SimpleField("lastRenovationDate", SearchFieldDataType.DateTimeOffset) { IsFilterable = true, IsSortable = true }
            }
    };

    Console.WriteLine("{0}", "Creating index...\n");
    idxclient.CreateIndex(index);
   ```

Gli attributi nel campo ne determinano l'uso in un'applicazione. L'attributo `IsFilterable`, ad esempio, deve essere assegnato a ogni campo che supporta un'espressione filtro.

A differenza delle versioni precedenti di .NET SDK, che richiedono [IsSearchable](/dotnet/api/microsoft.azure.search.models.field.issearchable) nei campi stringa ricercabili, è possibile usare [SearchableField](/dotnet/api/azure.search.documents.indexes.models.searchablefield) e [SimpleField](/dotnet/api/azure.search.documents.indexes.models.simplefield) per semplificare le definizioni dei campi.

Analogamente alle versioni precedenti, altri attributi sono ancora obbligatori nella definizione stessa. Ad esempio, [IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable), [IsSortable](/dotnet/api/azure.search.documents.indexes.models.searchfield.issortable) e [IsFacetable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfacetable) devono essere attribuiti in modo esplicito come illustrato nell'esempio precedente. 

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - Caricare i documenti

Ricerca cognitiva di Azure esegue ricerche sul contenuto archiviato nel servizio. In questo passaggio si caricheranno documenti JSON conformi all'indice di hotel appena creato.

In Ricerca cognitiva di Azure i documenti sono strutture dei dati che costituiscono sia l'input per l'indicizzazione che l'output restituito dalle query. In quanto ottenuti da un'origine dati esterna, gli input dei documenti possono essere righe in un database, BLOB nell'archiviazione BLOB o documenti JSON nel disco. Per brevità, in questo esempio si incorporeranno i documenti JSON per cinque alberghi nel codice stesso. 

Quando si caricano i documenti, è necessario usare un oggetto [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1). Un oggetto IndexDocumentsBatch contiene una raccolta di oggetti [Action](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1.actions), ognuno contenente un documento e una proprietà che indica a Ricerca cognitiva di Azure l'azione da eseguire ([upload, merge, delete e mergeOrUpload](search-what-is-data-import.md#indexing-actions)).

1. In **Program.cs** creare una matrice di documenti e azioni di indice e quindi passarla a `ndexDocumentsBatch`. I documenti riportati di seguito sono conformi all'indice hotels-quickstart-v11, in base a quanto definito dalla classe Hotel.

    ```csharp
    // Load documents (using a subset of fields for brevity)
    IndexDocumentsBatch<Hotel> batch = IndexDocumentsBatch.Create(
        IndexDocumentsAction.Upload(new Hotel { Id = "78", Name = "Upload Inn", Category = "hotel", Rate = 279, Updated = new DateTime(2018, 3, 1, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "54", Name = "Breakpoint by the Sea", Category = "motel", Rate = 162, Updated = new DateTime(2015, 9, 12, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "39", Name = "Debug Motel", Category = "motel", Rate = 159, Updated = new DateTime(2016, 11, 11, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "48", Name = "NuGet Hotel", Category = "hotel", Rate = 238, Updated = new DateTime(2016, 5, 30, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "12", Name = "Renovated Ranch", Category = "motel", Rate = 149, Updated = new DateTime(2020, 1, 24, 7, 0, 0) }));

    IndexDocumentsOptions idxoptions = new IndexDocumentsOptions { ThrowOnAnyError = true };

    Console.WriteLine("{0}", "Loading index...\n");
    qryclient.IndexDocuments(batch, idxoptions);
    ```

    Dopo aver inizializzato l'oggetto [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1), è possibile inviarlo all'indice chiamando [IndexDocuments](/dotnet/api/azure.search.documents.searchclient.indexdocuments) nell'oggetto [SearchClient](/dotnet/api/azure.search.documents.searchclient).

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

1. In **Program.cs** creare un metodo WriteDocuments che stampa i risultati della ricerca nella console.

    ```csharp
    private static void WriteDocuments(SearchResults<Hotel> searchResults)
    {
        foreach (SearchResult<Hotel> response in searchResults.GetResults())
        {
            Hotel doc = response.Document;
            var score = response.Score;
            Console.WriteLine($"Name: {doc.Name}, Type: {doc.Category}, Rate: {doc.Rate}, Last-update: {doc.Updated}, Score: {score}");
        }

        Console.WriteLine();
    }
    ```

1. Creare un metodo RunQueries per eseguire le query e restituire i risultati. I risultati sono oggetti Hotel.

    ```csharp
    private static void RunQueries(SearchClient qryclient)
    {
        SearchOptions options;
        SearchResults<Hotel> response;

        Console.WriteLine("Query #1: Search on the term 'motel' and list the relevance score for each match...\n");

        options = new SearchOptions()
        {
            Filter = "",
            OrderBy = { "" }
        };

        response = qryclient.Search<Hotel>("motel", options);
        WriteDocuments(response);

        Console.WriteLine("Query #2: Find hotels where 'type' equals hotel...\n");

        options = new SearchOptions()
        {
            Filter = "hotelCategory eq 'hotel'",
        };

        response = qryclient.Search<Hotel>("*", options);
        WriteDocuments(response);

        Console.WriteLine("Query #3: Filter on rates less than $200 and sort by when the hotel was last updated...\n");

        options = new SearchOptions()
        {
            Filter = "baseRate lt 200",
            OrderBy = { "lastRenovationDate desc" }
        };

        response = qryclient.Search<Hotel>("*", options);
        WriteDocuments(response);
    }
    ```

Questo esempio illustra due [modi per cercare termini in una query](search-query-overview.md#types-of-queries): la ricerca full-text e i filtri.

+ La ricerca full-text esegue query per uno o più termini nei campi ricercabili dell'indice. La prima query è una ricerca full-text. La ricerca full-text genera punteggi di pertinenza usati per classificare i risultati.

+ Un filtro è un'espressione booleana che viene valutata sui campi [IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable) di un indice. Le query di filtro includono o escludono valori. Di conseguenza, a una query di filtro non è associato alcun punteggio di pertinenza. Le ultime due query illustrano la ricerca con filtri.

È possibile usare le ricerche full-text e i filtri insieme o separatamente.

Sia le ricerche che i filtri vengono eseguiti usando il metodo [SearchClient.Search](/dotnet/api/azure.search.documents.searchclient.search). Una query di ricerca può essere passata nella stringa `searchText`, mentre un'espressione filtro può essere passata nella proprietà [Filter](/dotnet/api/azure.search.documents.searchoptions.filter) della classe [SearchOptions](/dotnet/api/azure.search.documents.searchoptions). Per filtrare senza eseguire ricerche, passare soltanto `"*"` per il parametro `searchText` del metodo [Search](/dotnet/api/azure.search.documents.searchclient.search). Per eseguire una ricerca senza filtrare, lasciare la proprietà `Filter` non impostata oppure non passare un'istanza di `SearchOptions`.

## <a name="run-the-program"></a>Eseguire il programma

Premere F5 per ricompilare l'app ed eseguire il programma completo. 

L'output include i messaggi restituiti da [Console.WriteLIne](/dotnet/api/system.console.writeline), con l'aggiunta di informazioni sulle query e risultati.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando si lavora nella propria sottoscrizione, alla fine di un progetto è opportuno verificare se le risorse create sono ancora necessarie. L'esecuzione continua delle risorse può avere un costo. È possibile eliminare le singole risorse oppure il gruppo di risorse per eliminare l'intero set di risorse.

Per trovare e gestire le risorse nel portale, usare il collegamento **Tutte le risorse** o **Gruppi di risorse** nel riquadro di spostamento a sinistra.

Se si usa un servizio gratuito, tenere presente che il numero di indicizzatori e origini dati è limitato a tre. Per non superare il limite, è possibile eliminare i singoli elementi nel portale. 

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido per C# sono state eseguite le attività per creare un indice, caricarvi documenti ed eseguire query. In diverse fasi sono state adottate delle scorciatoie per semplificare il codice e renderlo più leggibile e comprensibile. Se si conoscono già i concetti di base, è consigliabile leggere l'articolo successivo in cui vengono esaminati concetti e approcci alternativi per approfondire l'argomento. 

> [!div class="nextstepaction"]
> [Come sviluppare in .NET](search-howto-dotnet-sdk.md)

Si vuole ottimizzare e risparmiare sulla spesa per il cloud?

> [!div class="nextstepaction"]
> [Per iniziare, analizzare i costi con Gestione costi](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)