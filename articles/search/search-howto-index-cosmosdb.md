---
title: Ricerca nei dati del database Cosmos di AzureSearch over Azure Cosmos DB data
titleSuffix: Azure Cognitive Search
description: Importare dati da Azure Cosmos DB in un indice ricercabile in Ricerca cognitiva di Azure.Import data from Azure Cosmos DB into a searchable index in Azure Cognitive Search. Gli indicizzatori automatizzano l'inserimento di dati per alcune origini dati come Azure Cosmos DB.
author: mgottein
manager: nitinme
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/02/2020
ms.openlocfilehash: d1723b6c5d56554fbff576f6a07e37455845bda4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283004"
---
# <a name="how-to-index-cosmos-db-data-using-an-indexer-in-azure-cognitive-search"></a>Come indicizzare i dati di Cosmos DB usando un indicizzatore in Ricerca cognitiva di Azure 

> [!IMPORTANT] 
> L'API SQL è generalmente disponibile.
> Il supporto API MongoDB, Gremlin e Cassandra è attualmente disponibile nell'anteprima pubblica. La funzionalità di anteprima viene fornita senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). È possibile richiedere l'accesso alle anteprime compilando [questo modulo](https://aka.ms/azure-cognitive-search/indexer-preview). Queste funzionalità di anteprima vengono fornite dall'[API REST versione 2019-05-06-Preview](search-api-preview.md). Il supporto del portale è attualmente limitato e non è disponibile alcun supporto per .NET SDK.

> [!WARNING]
> Solo le raccolte cosmo DB con un criterio di indicizzazione impostato su Coerente sono supportate da Ricerca cognitiva di Azure.Only Cosmos DB collections with an [indexing policy](https://docs.microsoft.com/azure/cosmos-db/index-policy) set to Consistent are [supported](https://docs.microsoft.com/azure/cosmos-db/index-policy#indexing-mode) by Azure Cognitive Search. L'indicizzazione delle raccolte con criteri di indicizzazione lazy non è consigliata e può causare la mancanza di dati. Le raccolte con indicizzazione disabilitata non sono supportate.

Questo articolo illustra come configurare un [indicizzatore](search-indexer-overview.md) di database Cosmos di Azure per estrarre il contenuto e renderlo ricercabile in Ricerca cognitiva di Azure.This article shows you how to configure an Azure Cosmos DB indexer to extract content and make it searchable in Azure Cognitive Search. Questo flusso di lavoro crea un indice di Ricerca cognitiva di Azure e lo carica con il testo esistente estratto da Azure Cosmos DB. 

Poiché la terminologia può creare confusione, vale la pena notare che l'indicizzazione di [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/index-overview) e [l'indicizzazione](search-what-is-an-index.md) di Ricerca cognitiva di Azure sono operazioni distinte, univoche per ogni servizio. Prima di avviare l'indicizzazione di Ricerca cognitiva di Azure, il database del database Cosmos di Azure deve già esistere e contenere dati.

L'indicizzatore Cosmos DB in Ricerca cognitiva di Azure può eseguire la ricerca per indicizzazione degli [elementi di Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/databases-containers-items#azure-cosmos-items) a cui si accede tramite protocolli diversi. 

+ Per [l'API SQL](https://docs.microsoft.com/azure/cosmos-db/sql-api-query-reference), generalmente disponibile, è possibile usare [il portale](#cosmos-indexer-portal), l'API [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations)o [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet) per creare l'origine dati e l'indicizzatore.

+ Per [l'API MongoDB (anteprima),](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction)è possibile usare il [portale](#cosmos-indexer-portal) o la [versione dell'API REST 2019-05-06-Preview](search-api-preview.md) per creare l'origine dati e l'indicizzatore.

+ Per [l'API Cassandra (anteprima)](https://docs.microsoft.com/azure/cosmos-db/cassandra-introduction) e [l'API Gremlin (anteprima),](https://docs.microsoft.com/azure/cosmos-db/graph-introduction)è possibile utilizzare solo la [versione dell'API REST 2019-05-06-Preview](search-api-preview.md) per creare l'origine dati e l'indicizzatore.


> [!Note]
> È possibile votare Voce utente per [l'API tabella](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab) se si vuole vederla supportata in Ricerca cognitiva di Azure.You can vote on User Voice for the Table API if you's see it supported in Azure Cognitive Search.
>

<a name="cosmos-indexer-portal"></a>

## <a name="use-the-portal"></a>Usare il portale

> [!Note]
> Il portale supporta attualmente l'API SQL e l'API MongoDB (anteprima).

Il metodo più semplice per l'indicizzazione degli elementi di Azure Cosmos DB consiste nell'usare una procedura guidata nel portale di Azure.The easiest method for indexing Azure Cosmos DB items is to use a wizard in the [Azure portal](https://portal.azure.com/). Eseguendo il campionamento dei dati e leggendo i metadati nel contenitore, la procedura guidata [**Importa dati**](search-import-data-portal.md) in Ricerca cognitiva di Azure può creare un indice predefinito, eseguire il mapping dei campi di origine ai campi dell'indice di destinazione e caricare l'indice in un'unica operazione. A seconda delle dimensioni e della complessità dei dati di origine, si può ottenere un indice di ricerca full-text operativo in pochi minuti.

È consigliabile usare la stessa area o posizione sia per Ricerca cognitiva di Azure che per il database cosmo di Azure per ridurre la latenza ed evitare costi di larghezza di banda.

### <a name="1---prepare-source-data"></a>1 - Preparare i dati di origine

È necessario disporre di un account cosmo DBCosmos, di un database cosmo di Azure mappato all'API SQL, all'API MongoDB (anteprima) o all'API Gremlin (anteprima) e al contenuto del database.

Assicurarsi che il database Cosmos DB contenga dati. [L'Importazione guidata dati](search-import-data-portal.md) legge i metadati ed esegue il campionamento dei dati per dedurre uno schema di indice, ma carica anche i dati da Cosmos DB. Se i dati non sono presenti, la procedura guidata si interrompe con questo errore "Errore durante il rilevamento dello schema dell'indice dall'origine dati: Impossibile compilare un indice prototipo perché l'origine dati 'emptycollection' non ha restituito dati".

### <a name="2---start-import-data-wizard"></a>2 - Avviare la procedura guidata Importa dati

È possibile [avviare la procedura guidata](search-import-data-portal.md) dalla barra dei comandi nella pagina servizio Ricerca cognitiva di Azure oppure, se ci si connette all'API SQL Cosmos DB, è possibile fare clic su **Aggiungi Ricerca cognitiva** di Azure nella sezione **Impostazioni** del riquadro di spostamento sinistro dell'account Cosmos DB.

   ![Comando Importa dati nel portale](./media/search-import-data-portal/import-data-cmd2.png "Avviare la procedura guidata Importa dati")

### <a name="3---set-the-data-source"></a>3 - Impostare l'origine dati

Nella pagina **di origine dati,** l'origine deve essere **Cosmos DB**, con le seguenti specifiche:

+ **Nome** è il nome dell'oggetto origine dati. Una volta creato, è possibile sceglierlo per altri carichi di lavoro.

+ **L'account Cosmos DB** deve essere la stringa di `AccountEndpoint` connessione `AccountKey`primaria o secondaria di Cosmos DB, con un e un file . Per le raccolte MongoDB, aggiungere **ApiKind-MongoDb** alla fine della stringa di connessione e separarla dalla stringa di connessione con un punto e virgola. Per l'API Gremlin e l'API Cassandra, usare le istruzioni per [l'API REST.](#cosmosdb-indexer-rest)

+ **Database** è un database esistente dall'account. 

+ **La raccolta** è un contenitore di documenti. Affinché l'importazione abbia esito positivo, è necessario che i documenti esistano. 

+ **La query** può essere vuota se si desidera che tutti i documenti, altrimenti è possibile immettere una query che seleziona un sottoinsieme di documenti. **Query** è disponibile solo per l'API SQL.

   ![Definizione dell'origine dati Cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-datasource.png "Definizione dell'origine dati Cosmos DB")

### <a name="4---skip-the-enrich-content-page-in-the-wizard"></a>4 - Saltare la pagina "Arricchisci contenuto" nella procedura guidata

L'aggiunta di competenze cognitive (o arricchimento) non è un requisito di importazione. A meno che non sia necessario [aggiungere l'arricchimento dell'iatura](cognitive-search-concept-intro.md) alla pipeline di indicizzazione, è consigliabile ignorare questo passaggio.

Per saltare il passaggio, fare clic sui pulsanti blu nella parte inferiore della pagina per "Avanti" e "Salta".

### <a name="5---set-index-attributes"></a>5 - Impostare gli attributi dell'indice

Nella pagina **Indice** dovrebbe essere presente un elenco di campi con un tipo di dati e una serie di caselle di controllo per l'impostazione degli attributi di indice. La procedura guidata può generare un elenco di campi in base ai metadati e campionando i dati di origine. 

È possibile selezionare in blocco gli attributi facendo clic sulla casella di controllo nella parte superiore di una colonna attributo. Scegliere **Recuperabile** e **Ricercabile** per ogni campo che deve essere restituito a un'app client e soggetto all'elaborazione della ricerca full-text. Si noterà che i numeri interi non sono full text o fuzzy ricercabile (i numeri vengono valutati verbatim e sono spesso utili nei filtri).

Per ulteriori informazioni, vedere la descrizione degli attributi di [indice](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) e [degli analizzatori del linguaggio.](https://docs.microsoft.com/rest/api/searchservice/language-support) 

Dedicare qualche momento alla revisione delle selezioni. Con l'esecuzione della procedura guidata vengono create strutture dei dati fisiche e non è possibile eliminare questi campi senza eliminare e ricreare tutti gli oggetti.

   ![Definizione dell'indice Cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-index-schema.png "Definizione dell'indice Cosmos DB")

### <a name="6---create-indexer"></a>6 - Creare l'indicizzatore

Nella sua specifica completa, la procedura guidata crea tre oggetti distinti nel servizio di ricerca. Un oggetto origine dati e un oggetto indice vengono salvati come risorse denominate nel servizio Ricerca cognitiva di Azure.A data source object and index object are saved as named resources in your Azure Cognitive Search service. L'ultimo passaggio crea un oggetto indicizzatore. La denominazione dell'indicizzatore ne consente l'esistenza come risorsa autonoma, che può essere pianificata e gestita in modo indipendente dagli oggetti indice e origine dati, creati nella stessa sequenza della procedura guidata.

Se non si ha familiarità con gli indicizzatori, un *indicizzatore* è una risorsa in Ricerca cognitiva di Azure che esegue la ricerca per indicizzazione di un'origine dati esterna per il contenuto ricercabile. L'output **dell'Importazione** guidata dati è un indicizzatore che esegue la ricerca per indicizzazione dell'origine dati Cosmos DB, estrae il contenuto ricercabile e lo importa in un indice in Ricerca cognitiva di Azure.The output of the Import data wizard is an indexer that crawls your Cosmos DB data source, extracts searchable content, and imports it into an index on Azure Cognitive Search.

La schermata seguente mostra la configurazione predefinita dell'indicizzatore. È possibile passare a **Una volta** se si desidera eseguire l'indicizzatore una sola volta. Fare clic su **Invia** per eseguire la procedura guidata e creare tutti gli oggetti. L'indicizzazione inizia immediatamente.

   ![Definizione dell'indicizzatore Cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-indexer.png "Definizione dell'indicizzatore Cosmos DB")

È possibile monitorare l'importazione dei dati nelle pagine del portale. Le notifiche di stato indicano lo stato dell'indicizzazione e il numero di documenti caricati. 

Al termine dell'indicizzazione, è possibile usare [Esplora ricerche](search-explorer.md) per eseguire query sull'indice.

> [!NOTE]
> Se i dati previsti non vengono visualizzati, potrebbe essere necessario impostare più attributi in più campi. Eliminare l'indice e l'indicizzatore appena creati ed eseguire nuovamente la procedura guidata, modificando le selezioni per gli attributi di indice nel passaggio 5. 

<a name="cosmosdb-indexer-rest"></a>

## <a name="use-rest-apis"></a>Usare le API REST

È possibile usare l'API REST per indicizzare i dati del database Cosmos di Azure, seguendo un flusso di lavoro in tre parti comune a tutti gli indicizzatori in Ricerca cognitiva di Azure: creare un'origine dati, creare un indice, creare un indicizzatore. L'estrazione dei dati da Cosmos DB si verifica quando si invia la richiesta Crea indicizzatore. Al termine della richiesta, si dirà un indice queryable. 

> [!NOTE]
> Per l'indicizzazione dei dati da Cosmos DB Gremlin API o Cosmos DB Cassandra API è necessario prima richiedere l'accesso alle anteprime gated compilando [questo modulo](https://aka.ms/azure-cognitive-search/indexer-preview). Dopo l'elaborazione della richiesta, si riceveranno istruzioni su come usare la [versione dell'API REST 2019-05-06-Preview](search-api-preview.md) per creare l'origine dati.

In precedenza in questo articolo è stato menzionato che l'indicizzazione di [Database Cosmos](https://docs.microsoft.com/azure/cosmos-db/index-overview) di Azure e [l'indicizzazione](search-what-is-an-index.md) di Ricerca cognitiva di Azure sono operazioni distinte. Per l'indicizzazione di Cosmos DB, per impostazione predefinita tutti i documenti vengono indicizzati automaticamente tranne che con l'API Cassandra. Se si disattiva l'indicizzazione automatica, è possibile accedere ai documenti solo tramite i collegamenti automatici o tramite query utilizzando l'ID documento. Azure Cognitive Search indexing requires Cosmos DB automatic indexing to be turned on in the collection that will be indexed by Azure Cognitive Search. Quando si effettua l'iscrizione per l'anteprima dell'indicizzatore Cosmos DB Cassandra API, verranno fornite istruzioni su come configurare l'indicizzazione di Cosmos DB.

> [!WARNING]
> Azure Cosmos DB è la nuova generazione di DocumentDB. In precedenza con la versione API **2017-11-11** è possibile utilizzare la `documentdb` sintassi. Ciò significava che è possibile `cosmosdb` specificare il tipo di origine dati come o `documentdb`. A partire dalla versione API **2019-05-06,** sia le API `cosmosdb` di Ricerca cognitiva di Azure che il portale supportano solo la sintassi come indicato in questo articolo. Ciò significa che il `cosmosdb` tipo di origine dati deve se si desidera connettersi a un endpoint Cosmos DB.

### <a name="1---assemble-inputs-for-the-request"></a>1 - Assemblaggi per la richiesta

Per ogni richiesta, è necessario fornire il nome del servizio e la chiave di amministrazione per Ricerca cognitiva di Azure (nell'intestazione POST) e il nome dell'account di archiviazione e la chiave per l'archiviazione BLOB. È possibile usare Postman per inviare richieste HTTP a Ricerca cognitiva di Azure.You can use [Postman](search-get-started-postman.md) to send HTTP requests to Azure Cognitive Search.

Copiare i quattro valori seguenti nel Blocco note in modo da poterli incollare in una richiesta:

+ Nome del servizio Ricerca cognitiva di AzureAzure Cognitive Search service name
+ Chiave di amministrazione di Ricerca cognitiva di AzureAzure Cognitive Search admin key
+ Stringa di connessione Cosmos DB

È possibile trovare questi valori nel portale:You can find these values in the portal:

1. Nelle pagine del portale per Ricerca cognitiva di Azure copiare l'URL del servizio di ricerca dalla pagina Panoramica.In the portal pages for Azure Cognitive Search, copy the search service URL from the Overview page.

2. Nel riquadro di spostamento sinistro fare clic su **Chiavi** e quindi copiare la chiave primaria o secondaria (sono equivalenti).

3. Passare alle pagine del portale per l'account di archiviazione Cosmos. Nel riquadro di spostamento sinistro, in **Impostazioni**, fare clic su **Chiavi**. Questa pagina fornisce un URI, due set di stringhe di connessione e due set di chiavi. Copiare una delle stringhe di connessione nel Blocco note.

### <a name="2---create-a-data-source"></a>2 - Creare un'origine dati

Un'**origine dati** specifica i dati per l'indice, le credenziali e i criteri per l'identificazione delle modifiche apportate ai dati (ad esempio documenti modificati o eliminati nella raccolta). L'origine dati è definita come risorsa indipendente affinché possa essere usata da più indicizzatori.

Per creare un'origine dati, formulare una richiesta POST:To create a data source, formulate a POST request:

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

| Campo   | Descrizione |
|---------|-------------|
| **name** | Obbligatorio. Scegliere un nome per rappresentare l'oggetto origine dati. |
|**type**| Obbligatorio. Deve essere `cosmosdb`. |
|**Credenziali** | Obbligatorio. Deve essere una stringa di connessione Cosmos DB.<br/>Per le raccolte SQL, le stringhe di connessione sono nel seguente formato:For SQL collections, connection strings are in this format:`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`<br/><br/> Per le raccolte di MongoDB, aggiungere **ApiKind = MongoDb** alla stringa di connessione: <br/>`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`<br/><br/>Per i grafici Gremlin e le tabelle Cassandra, iscriviti [all'anteprima dell'indicizzatore gated](https://aka.ms/azure-cognitive-search/indexer-preview) per accedere all'anteprima e alle informazioni su come formattare le credenziali.<br/><br/>Evitare i numeri di porta nell'URL dell'endpoint. Se si include il numero di porta, Ricerca cognitiva di Azure non sarà in grado di indicizzare il database del database Cosmos di Azure.If you include the port number, Azure Cognitive Search will be unable to index your Azure Cosmos DB database.|
| **Contenitore** | Contiene i seguenti elementi: <br/>**name**: Obbligatorio. Specificare l'ID della raccolta di database da indicizzare.<br/>**query**: facoltativa. È possibile specificare una query per appiattire un documento JSON arbitrario in uno schema flat che può essere indicizzato da Ricerca cognitiva di Azure.You can specify a query to flatten an arbitrary JSON document into a flat schema that Azure Cognitive Search can index.<br/>Per l'API MongoDB, l'API Gremlin e l'API Cassandra, le query non sono supportate. |
| **dataChangeDetectionPolicy** | Consigliato. Vedere la sezione [Indicizzazione di documenti modificati](#DataChangeDetectionPolicy).|
|**dataDeletionDetectionPolicy** | Facoltativa. Vedere la sezione [Indicizzazione di documenti eliminati](#DataDeletionDetectionPolicy).|

### <a name="using-queries-to-shape-indexed-data"></a>Utilizzo di query per formare dati indicizzati
È possibile specificare una query di SQL per appiattire le matrici o le proprietà annidate, progettare le proprietà JSON e filtrare i dati da indicizzare. 

> [!WARNING]
> Le query personalizzate non sono supportate per **l'API MongoDB**, `container.query` l'API **Gremlin**e l'API **Cassandra**: il parametro deve essere impostato su null o omesso. Se è necessario usare una query personalizzata, inviare un messaggio su [User Voice](https://feedback.azure.com/forums/263029-azure-search).

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


### <a name="3---create-a-target-search-index"></a>3 - Creare un indice di ricerca di destinazione 

Creare un indice di [Ricerca cognitiva di Azure](/rest/api/searchservice/create-index) di destinazione, se non ne è già presente uno. L'esempio seguente crea un indice con un ID e un campo di descrizione:

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
> Per le raccolte partizionate, la chiave del `_rid` documento predefinita è la proprietà `rid` di Azure Cosmos DB, in cui Rinomina automaticamente Ricerca cognitiva di Azure perché i nomi dei campi non possono iniziare con un carattere di sottolineatura. Inoltre, i valori `_rid` di Azure Cosmos DB contengono caratteri non validi nelle chiavi di Ricerca cognitiva di Azure.Also, Azure Cosmos DB values contain characters that are invalid in Azure Cognitive Search keys. Per questo motivo, i valori `_rid` presentano la codificata Base64.
> 
> Per le raccolte MongoDB, Ricerca `_id` cognitiva `id`di Azure rinomina automaticamente la proprietà in .  

### <a name="mapping-between-json-data-types-and-azure-cognitive-search-data-types"></a>Mapping between JSON Data Types and Azure Cognitive Search Data Types
| Tipo di dati JSON | Tipi di campi dell'indice di destinazione compatibili |
| --- | --- |
| Bool |Edm.Boolean, Edm.String |
| Numeri che rappresentano numeri interi |Edm.Int32, Edm.Int64, Edm.String |
| Numeri che rappresentano numeri a virgola mobile |Edm.Double, Edm.String |
| string |Edm.String |
| Matrici di tipi primitivi, ad esempio ["a", "b", "c"] |Collection(Edm.String) |
| Stringhe che rappresentano date |Edm.DateTimeOffset, Edm.String |
| Oggetti GeoJSON, ad esempio { "type": "Point", "coordinates": [long, lat] } |Edm.GeographyPoint |
| Altri oggetti JSON |N/D |

### <a name="4---configure-and-run-the-indexer"></a>4 - Configurare ed eseguire l'indicizzatore

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

Per altre informazioni sulla definizione delle pianificazioni degli indicizzatori, vedere [Come pianificare gli indicizzatori per Ricerca cognitiva](search-howto-schedule-indexers.md)di Azure.For more information about defining indexer schedules, see How to schedule indexers for Azure Cognitive Search .

## <a name="use-net"></a>Usare .NET

L'SDK .NET disponibile in genere ha la parità completa con l'API REST generalmente disponibile. È consigliabile rivedere la sezione relativa dall'API REST per apprenderne i concetti, il flusso di lavoro e i requisiti. Consultare quindi la seguente documentazione di riferimento sull'API .NET per implementare un indicizzatore JSON nel codice gestito.

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="DataChangeDetectionPolicy"></a>

## <a name="indexing-changed-documents"></a>Indicizzazione di documenti modificati

Lo scopo di un criterio di rilevamento delle modifiche dei dati è quello di identificare in modo efficace gli elementi di dati modificati. Attualmente, l'unico criterio [`HighWaterMarkChangeDetectionPolicy`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.highwatermarkchangedetectionpolicy) supportato è l'utilizzo della `_ts` proprietà (timestamp) fornita da Azure Cosmos DB, che viene specificato come segue:Currently, the only supported policy is the using the (timestamp) property provided by Azure Cosmos DB, which is specified as follows:

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

L'uso di questi criteri è consigliato per garantire elevate prestazioni dell'indicizzatore. 

Se si usa una query personalizzata, assicurarsi che la proprietà `_ts` sia progettata dalla query.

<a name="IncrementalProgress"></a>

### <a name="incremental-progress-and-custom-queries"></a>Avanzamento incrementale e query personalizzate

L'avanzamento incrementale durante l'indicizzazione assicura che, in caso di interruzione dell'esecuzione dell'indicizzatore a causa di errori temporanei o del limite del tempo di esecuzione, l'indicizzatore possa riprendere dal punto in cui è stato interrotto all'esecuzione successiva, invece di dovere ripetere dall'inizio l'indicizzazione dell'intera raccolta. Questo approccio risulta particolarmente importante in caso di indicizzazione di raccolte di grandi dimensioni. 

Per abilitare l'avanzamento incrementale quando si usa una query personalizzata, assicurarsi che la query ordini i risultati in base alla colonna `_ts`. In questo modo vengono abilitati i punti di controllo periodici utilizzati da Ricerca cognitiva di Azure per fornire uno stato di avanzamento incrementale in presenza di errori.   

In alcuni casi, anche se `ORDER BY [collection alias]._ts` la query contiene una clausola, Ricerca cognitiva di Azure potrebbe non dedurre che la query è ordinata in base al `_ts`file . È possibile indicare a Ricerca cognitiva `assumeOrderByHighWaterMarkColumn` di Azure che i risultati vengono ordinati usando la proprietà di configurazione. Per specificare questo hint, creare o aggiornare l'indicizzatore come indicato di seguito: 

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

## <a name="next-steps"></a><a name="NextSteps"></a>Passaggi successivi

Congratulazioni! Si è appreso come integrare Azure Cosmos DB con Ricerca cognitiva di Azure usando un indicizzatore.

* Per altre informazioni su Azure Cosmos DB, vedere la [pagina del servizio Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).
* Per altre informazioni su Ricerca cognitiva di Azure, vedere la pagina Servizio di [ricerca.](https://azure.microsoft.com/services/search/)
