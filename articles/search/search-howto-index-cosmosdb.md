---
title: Indicizzare un'origine dati di Azure Cosmos DB - Ricerca di Azure
description: Eseguire la ricerca per indicizzazione in un'origine dati di Azure Cosmos DB e inserire dati in un indice di ricerca full-text in Ricerca di Azure. Gli indicizzatori automatizzano l'inserimento di dati per alcune origini dati come Azure Cosmos DB.
ms.date: 05/02/2019
author: mgottein
manager: nitinme
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 802a4e9c6191d33051eb075543691845595bc9c3
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "69656685"
---
# <a name="how-to-index-cosmos-db-using-an-azure-search-indexer"></a>Come indicizzare Cosmos DB usando un indicizzatore di ricerca di Azure


> [!Note]
> Il supporto dell'API MongoDB è in anteprima e non è destinato all'uso in produzione. Questa funzionalità viene fornita dall'[API REST versione 2019-05-06-Preview](search-api-preview.md). Al momento non è disponibile alcun supporto per il portale o .NET SDK.
>
> L'API SQL è disponibile a livello generale.

Questo articolo illustra come configurare un [indicizzatore](search-indexer-overview.md) di Azure Cosmos DB per estrarre il contenuto e renderlo ricercabile in ricerca di Azure. Questo flusso di lavoro crea un indice di ricerca di Azure e lo carica con il testo esistente Estratto da Azure Cosmos DB. 

Poiché la terminologia può creare confusione, vale la pena notare che [Azure Cosmos DB indicizzazione](https://docs.microsoft.com/azure/cosmos-db/index-overview) e l' [indicizzazione di ricerca di Azure](search-what-is-an-index.md) sono operazioni distinte, univoche per ogni servizio. Prima di iniziare l'indicizzazione di ricerca di Azure, è necessario che il database Azure Cosmos DB esista già e contenga dati.

È possibile usare il [portale](#cosmos-indexer-portal), le API REST o .NET SDK per indicizzare il contenuto di Cosmos. Il Cosmos DB indicizzatore in ricerca di Azure può eseguire la ricerca per indicizzazione [degli elementi di Azure Cosmos](https://docs.microsoft.com/azure/cosmos-db/databases-containers-items#azure-cosmos-items) accessibili tramite questi protocolli:

* [API SQL](https://docs.microsoft.com/azure/cosmos-db/sql-api-query-reference) 
* [API MongoDB (anteprima)](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction)

> [!Note]
> User Voice include elementi esistenti per il supporto di API aggiuntive. È possibile eseguire il cast di un voto per le API Cosmos da visualizzare come supportate in ricerca di Azure: [API tabella](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab), [API Graph](https://feedback.azure.com/forums/263029-azure-search/suggestions/13285011-add-graph-databases-to-your-data-sources-eg-neo4), [API Apache Cassandra](https://feedback.azure.com/forums/263029-azure-search/suggestions/32857525-indexer-crawler-for-apache-cassandra-api-in-azu).
>

<a name="cosmos-indexer-portal"></a>

## <a name="use-the-portal"></a>Usare il portale

Il metodo più semplice per indicizzare gli elementi di Azure Cosmos consiste nell'usare una procedura guidata nel [portale di Azure](https://portal.azure.com/). Eseguendo il campionamento dei dati e leggendo i metadati nel contenitore, la procedura guidata [**Importa dati**](search-import-data-portal.md) in ricerca di Azure può creare un indice predefinito, eseguire il mapping dei campi di origine ai campi dell'indice di destinazione e caricare l'indice in un'unica operazione. A seconda delle dimensioni e della complessità dei dati di origine, si può ottenere un indice di ricerca full-text operativo in pochi minuti.

È consigliabile usare la stessa sottoscrizione di Azure per ricerca di Azure e Azure Cosmos DB, preferibilmente nella stessa area.

### <a name="1---prepare-source-data"></a>1 - Preparare i dati di origine

È necessario avere un account Cosmos, un database di Azure Cosmos mappato all'API SQL o l'API MongoDB e un contenitore di documenti JSON. 

Verificare che il database di Cosmos DB contenga dati. La [procedura guidata Importa dati](search-import-data-portal.md) legge i metadati ed esegue il campionamento dei dati per dedurre uno schema di indice, ma carica anche i dati da Cosmos DB. Se i dati risultano mancanti, la procedura guidata viene arrestata con l'errore "errore durante il rilevamento dello schema dell'indice dall'origine dati: Impossibile compilare un indice del prototipo perché DataSource ' emptycollection ' non ha restituito dati".

### <a name="2---start-import-data-wizard"></a>2 - Avviare la procedura guidata Importa dati

È possibile [avviare la procedura guidata](search-import-data-portal.md) dalla barra dei comandi nella pagina del servizio ricerca di Azure oppure facendo clic su **Aggiungi ricerca di Azure** nella sezione **Impostazioni** del riquadro di spostamento a sinistra dell'account di archiviazione.

   ![Comando Importa dati nel portale](./media/search-import-data-portal/import-data-cmd2.png "Avviare la procedura guidata Importa dati")

### <a name="3---set-the-data-source"></a>3 - Impostare l'origine dati

> [!NOTE] 
> Attualmente, non è possibile creare o modificare origini dati **MongoDB** usando portale di Azure o .NET SDK. Tuttavia, è **possibile** monitorare la cronologia di esecuzione di indicizzatori MongoDB nel portale.

Nella pagina **origine dati** , l'origine deve essere **Cosmos DB**, con le specifiche seguenti:

+ **Nome** è il nome dell'oggetto origine dati. Una volta creato, è possibile sceglierlo per altri carichi di lavoro.

+ **Cosmos DB account** deve essere la stringa di connessione primaria o secondaria di Cosmos DB, con un `AccountEndpoint` e un `AccountKey`. L'account determina se viene eseguito il cast dei dati come API SQL o l'API Mongo DB

+ Il **database** è un database esistente dall'account. 

+ La **raccolta** è un contenitore di documenti. Per consentire l'importazione, è necessario che i documenti esistano. 

+ La **query** può essere vuota se si desiderano tutti i documenti. in caso contrario, è possibile immettere una query che seleziona un subset del documento. 

   ![Definizione dell'origine dati Cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-datasource.png "Definizione dell'origine dati Cosmos DB")

### <a name="4---skip-the-add-cognitive-search-page-in-the-wizard"></a>4 - Saltare la pagina "Aggiungi ricerca cognitiva" nella procedura guidata

L'aggiunta di competenze cognitive non è necessaria per l'importazione di documenti. A meno che non si abbia la specifica esigenza di [includere trasformazioni e API Servizi cognitivi](cognitive-search-concept-intro.md) alla pipeline di indicizzazione, saltare questo passaggio.

Per ignorare il passaggio, passare prima alla pagina successiva.

   ![Pulsante Pagina successiva per la ricerca cognitiva](media/search-get-started-portal/next-button-add-cog-search.png)

Da questa pagina è possibile passare alla personalizzazione degli indici.

   ![Ignorare il passaggio delle competenze cognitive](media/search-get-started-portal/skip-cog-skill-step.png)

### <a name="5---set-index-attributes"></a>5 - Impostare gli attributi dell'indice

Nella pagina **Indice** dovrebbe essere presente un elenco di campi con un tipo di dati e una serie di caselle di controllo per l'impostazione degli attributi di indice. La procedura guidata può generare un elenco di campi in base ai metadati e al campionamento dei dati di origine. 

È possibile selezionare in blocco gli attributi facendo clic sulla casella di controllo nella parte superiore di una colonna attributo. Scegliere **recuperabile** e **ricercabile** per ogni campo che deve essere restituito a un'app client e soggetto all'elaborazione della ricerca full-text. Si noterà che gli Integer non sono full-text o ricerca fuzzy (i numeri vengono valutati Verbatim e spesso sono utili nei filtri).

Per ulteriori informazioni, vedere la descrizione degli [attributi degli indici](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) e degli [analizzatori della lingua](https://docs.microsoft.com/rest/api/searchservice/language-support) . 

Dedicare qualche momento alla revisione delle selezioni. Con l'esecuzione della procedura guidata vengono create strutture dei dati fisiche e non è possibile eliminare questi campi senza eliminare e ricreare tutti gli oggetti.

   ![Definizione dell'indice Cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-index-schema.png "Definizione dell'indice Cosmos DB")

### <a name="6---create-indexer"></a>6 - Creare l'indicizzatore

Nella sua specifica completa, la procedura guidata crea tre oggetti distinti nel servizio di ricerca. Un oggetto origine dati e un oggetto indice vengono salvati come risorse denominate nel servizio Ricerca di Azure. L'ultimo passaggio crea un oggetto indicizzatore. La denominazione dell'indicizzatore ne consente l'esistenza come risorsa autonoma, che può essere pianificata e gestita in modo indipendente dagli oggetti indice e origine dati, creati nella stessa sequenza della procedura guidata.

Per chi non lo sapesse, un *indicizzatore* è una risorsa di Ricerca di Azure che esegue una ricerca per indicizzazione in un'origine dati esterna per individuare contenuto ricercabile. L'output della procedura guidata **Importa dati** è un indicizzatore che esegue la ricerca per indicizzazione dell'origine dati Cosmos DB, estrae contenuto ricercabile e lo importa in un indice in ricerca di Azure.

La schermata seguente illustra la configurazione dell'indicizzatore predefinita. È possibile passare a **una sola volta** se si desidera eseguire l'indicizzatore una sola volta. Fare clic su **Invia** per eseguire la procedura guidata e creare tutti gli oggetti. L'indicizzazione inizia immediatamente.

   ![Definizione dell'indicizzatore Cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-indexer.png "Definizione dell'indicizzatore Cosmos DB")

È possibile monitorare l'importazione dei dati nelle pagine del portale. Le notifiche di stato indicano lo stato dell'indicizzazione e il numero di documenti caricati. 

Al termine dell'indicizzazione, è possibile usare [Esplora ricerche](search-explorer.md) per eseguire query sull'indice.

> [!NOTE]
> Se non vengono visualizzati i dati previsti, potrebbe essere necessario impostare più attributi in altri campi. Eliminare l'indice e l'indicizzatore appena creati, quindi eseguire di nuovo la procedura guidata modificando le selezioni per gli attributi di indice nel passaggio 5. 

<a name="cosmosdb-indexer-rest"></a>

## <a name="use-rest-apis"></a>Usare le API REST

È possibile usare l'API REST per indicizzare i dati Azure Cosmos DB, seguendo un flusso di lavoro in tre parti comune a tutti gli indicizzatori in ricerca di Azure: creare un'origine dati, creare un indice, creare un indicizzatore. L'estrazione dei dati dall'archiviazione Cosmos si verifica quando si invia la richiesta create Indexer. Al termine di questa richiesta, sarà presente un indice Queryable. 

Se si sta valutando MongoDB, è necessario utilizzare il `api-version=2019-05-06-Preview` REST per creare l'origine dati.

Nell'account Cosmos DB è possibile specificare se la raccolta deve indicizzare automaticamente tutti i documenti. Per impostazione predefinita, tutti i documenti vengono indicizzati automaticamente, ma è possibile disattivare l'indicizzazione automatica. Quando l'indicizzazione è disattivata, i documenti sono accessibili solo tramite i rispettivi collegamenti automatici o tramite query usando l'ID documento. Il servizio Ricerca di Azure richiede l'attivazione dell'indicizzazione automatica di Cosmos DB nella raccolta che verrà indicizzata da Ricerca di Azure. 

> [!WARNING]
> Azure Cosmos DB è la nuova generazione di DocumentDB. In precedenza con l'API versione **2017-11-11** è possibile usare la sintassi `documentdb`. Ciò significava che è possibile specificare il tipo di origine dati come `cosmosdb` o `documentdb`. A partire dalla versione API **2019-05-06** , le API di ricerca di Azure e il portale supportano solo la sintassi `cosmosdb` come indicato in questo articolo. Questo significa che il tipo di origine dati deve `cosmosdb` se si desidera connettersi a un endpoint di Cosmos DB.

### <a name="1---assemble-inputs-for-the-request"></a>1-assemblare gli input per la richiesta

Per ogni richiesta, è necessario fornire il nome del servizio e la chiave amministratore per ricerca di Azure (nell'intestazione POST) e il nome e la chiave dell'account di archiviazione per l'archiviazione BLOB. È possibile usare il [post](search-get-started-postman.md) per inviare richieste HTTP a ricerca di Azure.

Copiare i quattro valori seguenti nel blocco note in modo che sia possibile incollarli in una richiesta:

+ Nome del servizio ricerca di Azure
+ Chiave amministratore di ricerca di Azure
+ Stringa di connessione Cosmos DB

È possibile trovare questi valori nel portale:

1. Nelle pagine del portale per ricerca di Azure copiare l'URL del servizio di ricerca dalla pagina panoramica.

2. Nel riquadro di spostamento a sinistra fare clic su **chiavi** e quindi copiare la chiave primaria o secondaria (sono equivalenti).

3. Passare alle pagine del portale per l'account di archiviazione Cosmos. Nel riquadro di spostamento a sinistra, in **Impostazioni**, fare clic su **chiavi**. Questa pagina fornisce un URI, due set di stringhe di connessione e due set di chiavi. Copiare una delle stringhe di connessione nel blocco note.

### <a name="2---create-a-data-source"></a>2-creare un'origine dati

Un'**origine dati** specifica i dati per l'indice, le credenziali e i criteri per l'identificazione delle modifiche apportate ai dati (ad esempio documenti modificati o eliminati nella raccolta). L'origine dati è definita come risorsa indipendente affinché possa essere usata da più indicizzatori.

Per creare un'origine dati, formulare una richiesta POST:

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCollection", "query": null },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        }
    }

Il corpo della richiesta contiene la definizione dell'origine dati, che deve includere i campi seguenti:

| Campo   | Description |
|---------|-------------|
| **nome** | Richiesto. Scegliere un nome qualsiasi per rappresentare l'oggetto origine dati. |
|**type**| Richiesto. Deve essere `cosmosdb`. |
|**credentials** | Richiesto. Deve essere una stringa di connessione Cosmos DB.<br/>Per le raccolte SQL, le stringhe di connessione sono in questo formato: `AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`<br/>Per le raccolte MongoDB, aggiungere **tipologia API = MongoDB** alla stringa di connessione:<br/>`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`<br/>Evitare i numeri di porta nell'URL dell'endpoint. Se si include il numero di porta, Ricerca di Azure non potrà indicizzare il database di Azure Cosmos DB.|
| **container** | Contiene gli elementi seguenti: <br/>**name**: obbligatorio. Consente di specificare l'ID della raccolta di database da indicizzare.<br/>**query**: facoltativa. È possibile specificare una query per rendere flat un documento JSON arbitrario in modo da ottenere uno schema flat che può essere indicizzato da Ricerca di Azure.<br/>Per le raccolte di MongoDB, le query non sono supportate. |
| **dataChangeDetectionPolicy** | Consigliato. Vedere la sezione [Indicizzazione di documenti modificati](#DataChangeDetectionPolicy).|
|**dataDeletionDetectionPolicy** | facoltativo. Vedere la sezione [Indicizzazione di documenti eliminati](#DataDeletionDetectionPolicy).|

### <a name="using-queries-to-shape-indexed-data"></a>Utilizzo di query per formare dati indicizzati
È possibile specificare una query di SQL per appiattire le matrici o le proprietà annidate, progettare le proprietà JSON e filtrare i dati da indicizzare. 

> [!WARNING]
> le query personalizzate non sono supportate per le raccolte di **MongoDB**: il parametro `container.query` deve essere impostato su null o omesso. Se è necessario usare una query personalizzata, inviare un messaggio su [User Voice](https://feedback.azure.com/forums/263029-azure-search).

Documento di esempio:

    {
        "userId": 10001,
        "contact": {
            "firstName": "andy",
            "lastName": "hoh"
        },
        "company": "microsoft",
        "tags": ["azure", "cosmosdb", "search"]
    }

Query di filtro:

    SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark ORDER BY c._ts

Query di appiattimento:

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
    
    
Query di proiezione:

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts


Query di appiattimento matrici:

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts


### <a name="3---create-a-target-search-index"></a>3: creare un indice di ricerca di destinazione 

[Creare un indice di ricerca di Azure di destinazione](/rest/api/searchservice/create-index) se non ne è già presente uno. Nell'esempio seguente viene creato un indice con un campo ID e Descrizione:

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [Search service admin key]

    {
       "name": "mysearchindex",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "sortable": false,
         "facetable": false,
         "suggestions": true
       }]
     }

Assicurarsi che lo schema dell'indice di destinazione sia compatibile con lo schema dei documenti JSON di origine oppure con l'output della proiezione di query personalizzata.

> [!NOTE]
> Per le raccolte partizionate, la chiave di documento predefinita è la proprietà `_rid` di Azure Cosmos DB, che Ricerca di Azure rinomina automaticamente `rid` perché i nomi di campo non possono iniziare con un carattere undescore. I valori `_rid` di Azure Cosmos DB contengono anche caratteri non validi per le chiavi di ricerca di Azure. Per questo motivo, i valori `_rid` presentano la codificata Base64.
> 
> Per le raccolte di MongoDB, Ricerca di Azure viene automaticamente rinominata come la proprietà `_id` di `doc_id`.  

### <a name="mapping-between-json-data-types-and-azure-search-data-types"></a>Mapping tra tipi di dati JSON e tipi di dati di Ricerca di Azure
| Tipo di dati JSON | Tipi di campi dell'indice di destinazione compatibili |
| --- | --- |
| Booleano |Edm.Boolean, Edm.String |
| Numeri che rappresentano numeri interi |Edm.Int32, Edm.Int64, Edm.String |
| Numeri che rappresentano numeri a virgola mobile |Edm.Double, Edm.String |
| Stringa |Edm.String |
| Matrici di tipi primitivi, ad esempio ["a", "b", "c"] |Collection(Edm.String) |
| Stringhe che rappresentano date |Edm.DateTimeOffset, Edm.String |
| Oggetti GeoJSON, ad esempio { "type": "Point", "coordinates": [long, lat] } |Edm.GeographyPoint |
| Altri oggetti JSON |N/D |

### <a name="4---configure-and-run-the-indexer"></a>4-configurare ed eseguire l'indicizzatore

Dopo aver creato l'indice e l'origine dati, è possibile creare l'indicizzatore:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mycosmosdbindexer",
      "dataSourceName" : "mycosmosdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }

L'indicizzatore verrà eseguito ogni due ore (l'intervallo di pianificazione è impostato su "PT2H"). Per eseguire un indicizzatore ogni 30 minuti, impostare l'intervallo su "PT30M". L'intervallo minimo supportato è di 5 minuti. La pianificazione è facoltativa: se omessa, l'indicizzatore viene eseguito una sola volta al momento della creazione. Tuttavia, è possibile eseguire un indicizzatore su richiesta in qualsiasi momento.   

Per altre informazioni sull'API di creazione di un indicizzatore, vedere [Creare un indicizzatore](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Per altre informazioni sulla definizione delle pianificazioni degli indicizzatori [, vedere come pianificare gli indicizzatori per ricerca di Azure](search-howto-schedule-indexers.md).

## <a name="use-net"></a>Usare .NET

.NET SDK disponibile a livello generale ha una parità completa con l'API REST disponibile a livello generale. È consigliabile rivedere la sezione relativa dall'API REST per apprenderne i concetti, il flusso di lavoro e i requisiti. Consultare quindi la seguente documentazione di riferimento sull'API .NET per implementare un indicizzatore JSON nel codice gestito.

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="DataChangeDetectionPolicy"></a>

## <a name="indexing-changed-documents"></a>Indicizzazione di documenti modificati

Lo scopo di un criterio di rilevamento delle modifiche dei dati è quello di identificare in modo efficace gli elementi di dati modificati. L'unico tipo di criteri attualmente supportato è `High Water Mark` che usa la proprietà `_ts` (timestamp) fornita da Azure Cosmos DB, specificato come indicato di seguito:

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

L'uso di questi criteri è consigliato per garantire elevate prestazioni dell'indicizzatore. 

Se si usa una query personalizzata, assicurarsi che la proprietà `_ts` sia progettata dalla query.

<a name="IncrementalProgress"></a>

### <a name="incremental-progress-and-custom-queries"></a>Avanzamento incrementale e query personalizzate

L'avanzamento incrementale durante l'indicizzazione assicura che, in caso di interruzione dell'esecuzione dell'indicizzatore a causa di errori temporanei o del limite del tempo di esecuzione, l'indicizzatore possa riprendere dal punto in cui è stato interrotto all'esecuzione successiva, invece di dovere ripetere dall'inizio l'indicizzazione dell'intera raccolta. Questo approccio risulta particolarmente importante in caso di indicizzazione di raccolte di grandi dimensioni. 

Per abilitare l'avanzamento incrementale quando si usa una query personalizzata, assicurarsi che la query ordini i risultati in base alla colonna `_ts`. Ciò consente la creazione di checkpoint periodici, che vengono usati da Ricerca di Azure per fornire l'avanzamento incrementale in caso di errori.   

In alcuni casi, anche se la query contiene una clausola `ORDER BY [collection alias]._ts`, è possibile che Ricerca di Azure non deduca che la query è ordinata in base a `_ts`. È possibile indicare a Ricerca di Azure che i risultati sono ordinati in base a un valore usando la proprietà `assumeOrderByHighWaterMarkColumn` della configurazione. Per specificare questo hint, creare o aggiornare l'indicizzatore come indicato di seguito: 

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 

<a name="DataDeletionDetectionPolicy"></a>

## <a name="indexing-deleted-documents"></a>Indicizzazione di documenti eliminati

Quando si eliminano righe dalla raccolta, in genere le si elimina anche dall'indice di ricerca. Scopo dei criteri di rilevamento dell'eliminazione dei dati è quello di identificare in modo efficace gli elementi di dati eliminati. Attualmente, l'unico criterio supportato è il criterio `Soft Delete` (l'eliminazione è contrassegnata da un tipo di flag), specificato come indicato sotto:

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

Se si usa una query personalizzata, assicurarsi che la proprietà a cui fa riferimento `softDeleteColumnName` sia progettata dalla query.

L'esempio seguente crea un'origine dati con criteri di eliminazione temporanea:

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCosmosDbCollectionId" },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }

## <a name="NextSteps"></a>Passaggi successivi

Congratulazioni. Si è appena appreso come integrare Azure Cosmos DB con Ricerca di Azure usando un indicizzatore.

* Per altre informazioni su Azure Cosmos DB, vedere la [pagina del servizio Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).
* Per altre informazioni su Ricerca di Azure, vedere la [pagina del servizio ricerca](https://azure.microsoft.com/services/search/).
