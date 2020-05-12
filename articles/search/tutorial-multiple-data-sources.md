---
title: Esercitazione per C# sull'indicizzazione di più origini dati di Azure
titleSuffix: Azure Cognitive Search
description: Informazioni su come importare i dati da più origini dati in un singolo indice di Ricerca cognitiva di Azure tramite gli indicizzatori. Questa esercitazione e il codice di esempio usano C#.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/28/2020
ms.openlocfilehash: eb1824e41fe9fc5185ae4e914b4828cddb2c42db
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780522"
---
# <a name="tutorial-index-from-multiple-data-sources-using-the-net-sdk"></a>Esercitazione: Indicizzazione da più origini dati con .NET SDK

Ricerca cognitiva di Azure consente di importare, analizzare e indicizzare i dati da più origini dati in un singolo indice di ricerca consolidato. Questa funzionalità supporta situazioni in cui i dati strutturati vengono aggregati in dati meno strutturati o anche in testo normale di altre origini, come documenti di testo, HTML o JSON.

Questa esercitazione descrive come indicizzare i dati di hotel provenienti da un'origine dati di Azure Cosmos DB e come unirli ai dettagli delle camere ricavati da documenti di Archiviazione BLOB di Azure. Il risultato sarà un indice di ricerca combinato degli hotel contenente tipi di dati complessi.

Questa esercitazione usa C# e [.NET SDK](https://aka.ms/search-sdk). In questa esercitazione verranno eseguite le attività seguenti:

> [!div class="checklist"]
> * Caricare dati di esempio e creare le origini dati
> * Identificare la chiave dei documenti
> * Definire e creare l'indice
> * Indicizzare i dati dell'hotel da Azure Cosmos DB
> * Unire i dati delle camere provenienti da Archiviazione BLOB

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

+ [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal)
+ [Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
+ [Visual Studio 2019](https://visualstudio.microsoft.com/)
+ [Creare](search-create-service-portal.md) o [trovare un servizio di ricerca esistente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> È possibile usare il servizio gratuito per questa esercitazione. Un servizio di ricerca gratuito consente di usare solo tre indici, tre indicizzatori e tre origini dati. Questa esercitazione crea un elemento per ogni tipo. Prima di iniziare, assicurarsi che lo spazio nel servizio sia sufficiente per accettare le nuove risorse.

## <a name="download-files"></a>Scaricare i file

Il codice sorgente per questa esercitazione si trova nel repository GitHub [azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) nella cartella [multiple-data-sources](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources).

## <a name="1---create-services"></a>1 - Creare i servizi

Questa esercitazione usa Ricerca cognitiva di Azure per l'indicizzazione e le query, Azure Cosmos DB per un set di dati e archiviazione BLOB di Azure per il secondo set di dati. 

Se possibile, creare tutti i servizi nella stessa area e nello stesso gruppo di risorse per motivi di prossimità e gestibilità. In pratica, i servizi possono risiedere in qualsiasi area.

Questo esempio usa due piccoli set di dati che descrivono sette hotel fittizi. Un set descrive gli hotel stessi e verrà caricato in un database Azure Cosmos DB. L'altro set contiene i dettagli delle camere di hotel ed è suddiviso in sette file JSON separati da caricare in Archiviazione BLOB di Azure.

### <a name="start-with-cosmos-db"></a>Iniziare a usare Cosmos DB

1. Accedere al [portale di Azure](https://portal.azure.com) e passare alla pagina Panoramica dell'account Azure Cosmos DB.

1. Selezionare **Esplora dati**, quindi selezionare **Nuovo database**.

   ![Creare un nuovo database](media/tutorial-multiple-data-sources/cosmos-newdb.png "Creare un nuovo database")

1. Immettere il nome **hotel-rooms-db**. Accettare i valori predefiniti per le impostazioni rimanenti.

   ![Configurare il database](media/tutorial-multiple-data-sources/cosmos-dbname.png "Configurare il database")

1. Creare un nuovo contenitore. Usare il database esistente appena creato. Immettere **hotels** come nome del contenitore e usare **/HotelId** come chiave di partizione.

   ![Aggiungere il contenitore](media/tutorial-multiple-data-sources/cosmos-add-container.png "Aggiungere il contenitore")

1. In **hotels** selezionare **Elementi** e quindi fare clic su **Carica elemento** sulla barra dei comandi. Passare alla cartella di progetto e selezionare il file **cosmosdb/HotelsDataSubset_CosmosDb.json**.

   ![Caricare nella raccolta di Azure Cosmos DB](media/tutorial-multiple-data-sources/cosmos-upload.png "Caricare nella raccolta di Azure Cosmos DB")

1. Premere Aggiorna per aggiornare la visualizzazione degli elementi nella raccolta di hotel. Dovrebbero essere visualizzati sette nuovi documenti di database.

### <a name="azure-blob-storage"></a>Archiviazione BLOB di Azure

1. Accedere al [portale di Azure](https://portal.azure.com), passare all'account di archiviazione di Azure, fare clic su **BLOB** e quindi su **+ Contenitore**.

1. [Creare un contenitore BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) denominato **hotel-rooms** in cui archiviare i file JSON delle camere di hotel di esempio. È possibile impostare il livello di accesso pubblico su uno qualsiasi dei relativi valori validi.

   ![Creare un contenitore BLOB](media/tutorial-multiple-data-sources/blob-add-container.png "Creare un contenitore BLOB")

1. Dopo aver creato il contenitore, aprirlo e selezionare **Carica** nella barra dei comandi. Passare alla cartella contenente i file di esempio. Selezionare tutti i file e quindi fare clic su **Carica**.

   ![Caricare file](media/tutorial-multiple-data-sources/blob-upload.png "Caricare file")

Dopo aver completato il caricamento, i file dovrebbero essere visualizzati nell'elenco relativo al contenitore di dati.

### <a name="azure-cognitive-search"></a>Ricerca cognitiva di Azure

Il terzo componente è Ricerca cognitiva di Azure, che è [possibile creare nel portale](search-create-service-portal.md). Per completare questa procedura dettagliata, è possibile usare il livello gratuito. 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Ottenere un URL e una chiave API di amministrazione per Ricerca cognitiva di Azure

Per interagire con il servizio Ricerca cognitiva di Azure, sono necessari l'URL del servizio e una chiave di accesso. Con entrambi gli elementi viene creato un servizio di ricerca, quindi se il servizio Ricerca cognitiva di Azure è stato aggiunto alla sottoscrizione, seguire questi passaggi per ottenere le informazioni necessarie:

1. [Accedere al portale di Azure](https://portal.azure.com/) e ottenere l'URL nella pagina **Panoramica** del servizio di ricerca. Un endpoint di esempio potrebbe essere simile a `https://mydemo.search.windows.net`.

1. In **Impostazioni** > **Chiavi** ottenere una chiave amministratore per diritti completi sul servizio. Sono disponibili due chiavi amministratore interscambiabili, fornite per continuità aziendale nel caso in cui sia necessario eseguire il rollover di una di esse. È possibile usare la chiave primaria o secondaria nelle richieste per l'aggiunta, la modifica e l'eliminazione di oggetti.

   Ottenere anche la chiave di query. È consigliabile inviare richieste di query con accesso di sola lettura.

   ![Ottenere il nome del servizio e le chiavi amministratore e di query](media/search-get-started-nodejs/service-name-and-keys.png)

La presenza di una chiave valida stabilisce una relazione di trust, in base alle singole richieste, tra l'applicazione che invia la richiesta e il servizio che la gestisce.

## <a name="2---set-up-your-environment"></a>2 - Configurare l'ambiente

1. Avviare Visual Studio 2019, scegliere **Gestione pacchetti NuGet** dal menu **Strumenti** e quindi **Gestisci pacchetti NuGet per la soluzione**. 

1. Nella scheda **Sfoglia** trovare e quindi installare **Microsoft.Azure.Search** (versione 9.0.1 o successiva). Sarà necessario completare altre finestra di dialogo per terminare l'installazione.

    ![Uso di NuGet per aggiungere librerie di Azure](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

1. Cercare il pacchetto NuGet **Microsoft.Extensions.Configuration.Json** e installarlo.

1. Aprire il file di soluzione **AzureSearchMultipleDataSources.sln**.

1. In Esplora soluzioni modificare il file **appsettings.json** per aggiungervi le informazioni sulla connessione.  

    ```json
    {
      "SearchServiceName": "Put your search service name here",
      "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
      "BlobStorageAccountName": "Put your Azure Storage account name here",
      "BlobStorageConnectionString": "Put your Azure Blob Storage connection string here",
      "CosmosDBConnectionString": "Put your Cosmos DB connection string here",
      "CosmosDBDatabaseName": "hotel-rooms-db"
    }
    ```

Per le prime due voci usare l'URL e le chiavi amministratore per il servizio Ricerca cognitiva di Azure. Con `https://mydemo.search.windows.net` come endpoint, ad esempio, il nome del servizio da specificare sarà `mydemo`.

Per le voci successive specificare i nomi di account e le informazioni sulla stringa di connessione per le origini dati di Archiviazione BLOB di Azure e Azure Cosmos DB.

## <a name="3---map-key-fields"></a>3 - Eseguire il mapping dei campi delle chiavi

Per unire il contenuto è necessario che entrambi i flussi di dati siano destinati agli stessi documenti nell'indice di ricerca. 

In Ricerca cognitiva di Azure il campo della chiave identifica in modo univoco ogni documento. Ogni indice di ricerca deve avere esclusivamente un campo chiave di tipo `Edm.String`. Questo campo deve essere presente per ogni documento di un'origine dati aggiunto all'indice. È l'unico campo obbligatorio.

Durante l'indicizzazione dei dati da più origini dati, assicurarsi che ogni riga o documento in ingresso contenga una chiave del documento comune per unire i dati da due documenti di origine fisicamente distinti in un nuovo documento di ricerca nell'indice combinato. 

È spesso necessaria una pianificazione anticipata per identificare una chiave del documento significativa per l'indice e assicurarsi che sia presente in entrambe le origini dati. In questa demo la chiave `HotelId` per ogni hotel in Cosmos DB è presente anche nei BLOB JSON delle camere nell'archivio BLOB.

Gli indicizzatori di Ricerca cognitiva di Azure possono usare i mapping dei campi per rinominare e anche riformattare i campi dati durante il processo di indicizzazione, affinché l'origine dati venga indirizzata al campo di indice corretto. Ad esempio, in Cosmos DB, l'identificatore dell'hotel viene chiamato **`HotelId`** . Nei file BLOB JSON relativi alle camere di hotel, l'identificatore di hotel è denominato **`Id`** . Il programma gestisce questa situazione eseguendo il mapping del campo **`Id`** dei BLOB al campo della chiave **`HotelId`** dell'indice.

> [!NOTE]
> Nella maggior parte dei casi, le chiavi dei documenti generate automaticamente, come quelle create per impostazione predefinita da alcuni indicizzatori, non costituiscono la scelta ottimale per gli indici combinati. In generale, è consigliabile usare un valore di chiave univoco e significativo che esiste già nelle origini dati o può essere aggiunto facilmente.

## <a name="4---explore-the-code"></a>4 - Esplorare il codice

Dopo aver definito dati e impostazioni di configurazione, dovrebbe essere possibile compilare ed eseguire il programma di esempio in **AzureSearchMultipleDataSources.sln**.

Questa semplice app console in C#/.NET esegue le attività seguenti:

* Crea un nuovo indice basato sulla struttura dei dati della classe Hotel in C# (che fa anche riferimento alle classi Address e Room).
* Crea una nuova origine dati e un indicizzatore che esegue il mapping dei dati di Azure Cosmos DB ai campi dell'indice. Entrambi questi elementi sono oggetti in Ricerca cognitiva di Azure.
* Esegue l'indicizzatore per caricare i dati degli hotel da Cosmos DB.
* Crea una seconda origine e un indicizzatore per il mapping dei dati BLOB JSON ai campi dell'indice.
* Esegue il secondo indicizzatore per caricare i dati delle camere dall'archivio BLOB.

 Prima di eseguire il programma, esaminare il codice e le definizioni di indice e indicizzatore per questo esempio. Il codice rilevante è disponibile in due file:

  + **Hotel.cs** contiene lo schema che definisce l'indice
  + **Program.cs** contiene le funzioni che creano l'indice di Ricerca cognitiva di Azure, le origini dati e gli indicizzatori, quindi carica i risultati combinati nell'indice.

### <a name="create-an-index"></a>Creare un indice

Questo programma di esempio usa .NET SDK per definire e creare un indice di Ricerca cognitiva di Azure. Sfrutta la classe [FieldBuilder](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder) per generare una struttura di indice da una classe di modello di dati C#.

Il modello di dati è definito dalla classe Hotel, che contiene anche i riferimenti alle classi Address e Rooms. FieldBuilder esegue il drill-down attraverso più definizioni di classi per generare una struttura dei dati complessa per l'indice. I tag di metadati vengono usati per definire gli attributi di ogni campo, ad esempio se è ricercabile o ordinabile.

I frammenti di codice seguenti del file **Hotel.cs** illustrano come sia possibile specificare un singolo campo e un riferimento a un'altra classe di modello di dati.

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
public Room[] Rooms { get; set; }
. . .
```

Nel file **Program.cs** l'indice è definito con un nome e una raccolta di campi generata dal metodo `FieldBuilder.BuildForType<Hotel>()` e viene quindi creato come segue:

```csharp
private static async Task CreateIndex(string indexName, SearchServiceClient searchService)
{
    // Create a new search index structure that matches the properties of the Hotel class.
    // The Address and Room classes are referenced from the Hotel class. The FieldBuilder
    // will enumerate these to create a complex data structure for the index.
    var definition = new Index()
    {
        Name = indexName,
        Fields = FieldBuilder.BuildForType<Hotel>()
    };
    await searchService.Indexes.CreateAsync(definition);
}
```

### <a name="create-azure-cosmos-db-data-source-and-indexer"></a>Creare un'origine dati e un indicizzatore di Azure Cosmos DB

Il programma principale include la logica per creare l'origine dati di Azure Cosmos DB per i dati degli hotel.

Concatena prima di tutto il nome del database Azure Cosmos DB alla stringa di connessione. Quindi definisce l'oggetto origine dati, incluse le impostazioni specifiche per le origini di Azure Cosmos DB, ad esempio la proprietà [useChangeDetection].

  ```csharp
private static async Task CreateAndRunCosmosDbIndexer(string indexName, SearchServiceClient searchService)
{
    // Append the database name to the connection string
    string cosmosConnectString = 
        configuration["CosmosDBConnectionString"]
        + ";Database=" 
        + configuration["CosmosDBDatabaseName"];

    DataSource cosmosDbDataSource = DataSource.CosmosDb(
        name: configuration["CosmosDBDatabaseName"], 
        cosmosDbConnectionString: cosmosConnectString,
        collectionName: "hotels",
        useChangeDetection: true);

    // The Azure Cosmos DB data source does not need to be deleted if it already exists,
    // but the connection string might need to be updated if it has changed.
    await searchService.DataSources.CreateOrUpdateAsync(cosmosDbDataSource);
  ```

Dopo aver creato l'origine dati, il programma configura un indicizzatore di Azure Cosmos DB denominato **hotel-rooms-cosmos-indexer**.

```csharp
    Indexer cosmosDbIndexer = new Indexer(
        name: "hotel-rooms-cosmos-indexer",
        dataSourceName: cosmosDbDataSource.Name,
        targetIndexName: indexName,
        schedule: new IndexingSchedule(TimeSpan.FromDays(1)));
    
    // Indexers keep metadata about how much they have already indexed.
    // If we already ran this sample, the indexer will remember that it already
    // indexed the sample data and not run again.
    // To avoid this, reset the indexer if it exists.
    bool exists = await searchService.Indexers.ExistsAsync(cosmosDbIndexer.Name);
    if (exists)
    {
        await searchService.Indexers.ResetAsync(cosmosDbIndexer.Name);
    }
    await searchService.Indexers.CreateOrUpdateAsync(cosmosDbIndexer);
```
Il programma eliminerà eventuali indicizzatori esistenti con lo stesso nome prima di creare quello nuovo, nel caso si voglia eseguire questo esempio più volte.

Questo esempio definisce una pianificazione per l'indicizzatore, in modo che venga eseguito una volta al giorno. È possibile rimuovere la proprietà relativa alla pianificazione da questa chiamata se non si vuole che l'indicizzatore venga eseguito di nuovo automaticamente in futuro.

### <a name="index-azure-cosmos-db-data"></a>Indicizzare i dati di Azure Cosmos DB

Dopo la creazione dell'origine dati e dell'indicizzatore, il codice che esegue l'indicizzatore è semplice:

```csharp
    try
    {
        await searchService.Indexers.RunAsync(cosmosDbIndexer.Name);
    }
    catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
    {
        Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
    }
```

Questo esempio include un semplice blocco try-catch per segnalare gli eventuali errori che potrebbero verificarsi durante l'esecuzione.

Dopo l'esecuzione dell'indicizzatore di Azure Cosmos DB, l'indice di ricerca conterrà un set completo di documenti di hotel di esempio. Tuttavia, il campo delle camere per ogni hotel sarà una matrice vuota, perché l'origine dati di Azure Cosmos DB non contiene i relativi dettagli. Quindi, il programma esegue il pull da Archiviazione BLOB per caricare e unire i dati delle camere.

### <a name="create-blob-storage-data-source-and-indexer"></a>Creare l'origine dati e l'indicizzatore di Archiviazione BLOB

Per ottenere i dettagli delle camere, il programma configura prima di tutto un'origine dati di Archiviazione BLOB per fare riferimento a un set di singoli file BLOB JSON.

```csharp
private static async Task CreateAndRunBlobIndexer(string indexName, SearchServiceClient searchService)
{
    DataSource blobDataSource = DataSource.AzureBlobStorage(
        name: configuration["BlobStorageAccountName"],
        storageConnectionString: configuration["BlobStorageConnectionString"],
        containerName: "hotel-rooms");

    // The blob data source does not need to be deleted if it already exists,
    // but the connection string might need to be updated if it has changed.
    await searchService.DataSources.CreateOrUpdateAsync(blobDataSource);
```

Dopo aver creato l'origine dati, il programma configura un indicizzatore BLOB denominato **hotel-rooms-blob-indexer**.

```csharp
    // Add a field mapping to match the Id field in the documents to 
    // the HotelId key field in the index
    List<FieldMapping> map = new List<FieldMapping> {
        new FieldMapping("Id", "HotelId")
    };

    Indexer blobIndexer = new Indexer(
        name: "hotel-rooms-blob-indexer",
        dataSourceName: blobDataSource.Name,
        targetIndexName: indexName,
        fieldMappings: map,
        parameters: new IndexingParameters().ParseJson(),
        schedule: new IndexingSchedule(TimeSpan.FromDays(1)));

    // Reset the indexer if it already exists
    bool exists = await searchService.Indexers.ExistsAsync(blobIndexer.Name);
    if (exists)
    {
        await searchService.Indexers.ResetAsync(blobIndexer.Name);
    }
    await searchService.Indexers.CreateOrUpdateAsync(blobIndexer);
```

I BLOB JSON contengono un campo della chiave denominato **`Id`** anziché **`HotelId`** . Il codice usa la classe `FieldMapping` per indicare all'indicizzatore di indirizzare il valore del campo **`Id`** alla chiave del documento **`HotelId`** nell'indice.

Gli indicizzatori di Archiviazione BLOB possono usare parametri che identificano la modalità di analisi da usare. La modalità di analisi per i BLOB che rappresentano un singolo documento è diversa da quella per più documenti all'interno dello stesso BLOB. In questo esempio ogni BLOB rappresenta un singolo documento di indice, quindi il codice usa il parametro `IndexingParameters.ParseJson()`.

Per altre informazioni sui parametri di analisi dell'indicizzatore per i BLOB JSON, vedere [Indicizzare i BLOB JSON](search-howto-index-json-blobs.md). Per altre informazioni su come specificare questi parametri con .NET SDK, vedere la classe [IndexerParametersExtension](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingparametersextensions).

Il programma eliminerà eventuali indicizzatori esistenti con lo stesso nome prima di creare quello nuovo, nel caso si voglia eseguire questo esempio più volte.

Questo esempio definisce una pianificazione per l'indicizzatore, in modo che venga eseguito una volta al giorno. È possibile rimuovere la proprietà relativa alla pianificazione da questa chiamata se non si vuole che l'indicizzatore venga eseguito di nuovo automaticamente in futuro.

### <a name="index-blob-data"></a>Indicizzare i dati BLOB

Dopo la creazione dell'origine dati e dell'indicizzatore di Archiviazione BLOB, il codice che esegue l'indicizzatore è semplice:

```csharp
    try
    {
        await searchService.Indexers.RunAsync(cosmosDbIndexer.Name);
    }
    catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
    {
        Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
    }
```

Poiché l'indice è già stato popolato con i dati degli hotel del database Azure Cosmos DB, l'indicizzatore BLOB aggiorna i documenti esistenti nell'indice e aggiunge i dettagli delle camere.

> [!NOTE]
> Se entrambe le origini dati contengono gli stessi campi non di chiave e i dati all'interno di tali campi non corrispondono, l'indice conterrà i valori di qualsiasi indicizzatore eseguito più di recente. In questo esempio entrambe le origini dati contengono un campo **HotelName**. Se per qualche motivo i dati di questo campo sono diversi per i documenti con lo stesso valore di chiave, il valore archiviato nell'indice corrisponderà ai dati **HotelName** dell'origine dati data indicizzata più di recente.

## <a name="5---search"></a>5 - Eseguire ricerche

È possibile esaminare l'indice di ricerca popolato dopo l'esecuzione del programma usando [**Esplora ricerche**](search-explorer.md) nel portale.

Nel portale di Azure aprire la pagina **Panoramica** del servizio di ricerca e individuare l'indice **hotel-rooms-sample** nell'elenco **Indici**.

  ![Elenco di indici di Ricerca cognitiva di Azure](media/tutorial-multiple-data-sources/index-list.png "Elenco di indici di Ricerca cognitiva di Azure")

Fare clic sull'indice hotel-rooms-sample nell'elenco. Verrà visualizzata un'interfaccia di Esplora ricerche per l'indice. Immettere una query per un termine tipo "Luxury". I risultati dovrebbero contenere almeno un documento, che dovrebbe mostrare un elenco di oggetti camera nella matrice di camere.

## <a name="reset-and-rerun"></a>Reimpostare ed eseguire di nuovo

Nelle prime fasi sperimentali di sviluppo l'approccio più pratico per le iterazioni di progettazione consiste nell'eliminare gli oggetti da Ricerca cognitiva di Azure e consentire al codice di ricompilarli. I nomi di risorsa sono univoci. L'eliminazione di un oggetto consente di ricrearlo usando lo stesso nome.

Il codice di esempio per questa esercitazione verifica la presenza di oggetti esistenti e li elimina in modo da poter eseguire nuovamente il codice.

È anche possibile usare il portale per eliminare indici, indicizzatori e origini dati.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando si lavora nella propria sottoscrizione, alla fine di un progetto è opportuno rimuovere le risorse che non sono più necessarie. L'esecuzione continua delle risorse può avere un costo. È possibile eliminare le singole risorse oppure il gruppo di risorse per eliminare l'intero set di risorse.

Per trovare e gestire le risorse nel portale, usare il collegamento Tutte le risorse o Gruppi di risorse nel riquadro di spostamento a sinistra.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver acquisito familiarità con il concetto di inserimento di dati da più origini, si esaminerà più in dettaglio la configurazione dell'indicizzatore, a partire da Cosmos DB.

> [!div class="nextstepaction"]
> [Configurare un indicizzatore di Azure Cosmos DB](search-howto-index-cosmosdb.md)