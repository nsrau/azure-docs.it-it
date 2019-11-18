---
title: Cerca nei dati Azure Cosmos DB
titleSuffix: Azure Cognitive Search
description: Eseguire la ricerca per indicizzazione di un'origine dati Azure Cosmos DB e inserire dati in un indice di ricerca full-text in Azure ricerca cognitiva. Gli indicizzatori automatizzano l'inserimento di dati per alcune origini dati come Azure Cosmos DB.
author: mgottein
manager: nitinme
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 7e4d51701fd8614831585aac03f2c8a909b2b847
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112748"
---
# <a name="how-to-index-cosmos-db-data-using-an-indexer-in-azure-cognitive-search"></a>Come indicizzare i dati Cosmos DB usando un indicizzatore in Azure ricerca cognitiva 

> [!IMPORTANT] 
> L'API SQL è disponibile a livello generale.
> L'API MongoDB, l'API Gremlin e il supporto API Cassandra sono attualmente disponibili in anteprima pubblica. La funzionalità di anteprima viene fornita senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). È possibile richiedere l'accesso alle anteprime compilando [questo modulo](https://aka.ms/azure-cognitive-search/indexer-preview). Queste funzionalità di anteprima vengono fornite dall'[API REST versione 2019-05-06-Preview](search-api-preview.md). Il supporto del portale è attualmente limitato e non è disponibile alcun supporto per .NET SDK.

Questo articolo illustra come configurare un [indicizzatore](search-indexer-overview.md) di Azure Cosmos DB per estrarre il contenuto e renderlo ricercabile in Azure ricerca cognitiva. Questo flusso di lavoro crea un indice di ricerca cognitiva di Azure e lo carica con il testo esistente Estratto da Azure Cosmos DB. 

Poiché la terminologia può creare confusione, vale la pena notare che [Azure Cosmos DB indicizzazione](https://docs.microsoft.com/azure/cosmos-db/index-overview) e l' [indicizzazione di Azure ricerca cognitiva](search-what-is-an-index.md) sono operazioni distinte, univoche per ogni servizio. Prima di avviare l'indicizzazione di Azure ricerca cognitiva, è necessario che il database di Azure Cosmos DB esista già e che contenga dati.

L'indicizzatore Cosmos DB in Azure ricerca cognitiva può eseguire la ricerca per indicizzazione di [Azure Cosmos DB elementi](https://docs.microsoft.com/azure/cosmos-db/databases-containers-items#azure-cosmos-items) a cui si accede tramite protocolli

+ Per l' [API SQL](https://docs.microsoft.com/azure/cosmos-db/sql-api-query-reference), disponibile a livello generale, è possibile usare il [portale](#cosmos-indexer-portal), l' [API REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations)o [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet).

+ Per l'API [MongoDB (anteprima)](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction) e l' [API Gremlin (anteprima)](https://docs.microsoft.com/azure/cosmos-db/graph-introduction), è possibile usare il [portale](#cosmos-indexer-portal) o l' [API REST versione 2019-05-06-Preview](search-api-preview.md) in una chiamata a [create Indexer (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer) per creare l'indicizzatore.

+ Per [API Cassandra (anteprima)](https://docs.microsoft.com/azure/cosmos-db/cassandra-introduction), è possibile usare solo l' [API REST versione 2019-05-06-Preview](search-api-preview.md) in una chiamata a [create Indexer (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer) .


> [!Note]
> È possibile eseguire il cast di un voto sulla voce dell'utente per la [API tabella](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab) se si vuole visualizzarla come supportata in Azure ricerca cognitiva.
>

<a name="cosmos-indexer-portal"></a>

## <a name="use-the-portal"></a>Usare il portale

> [!Note]
> Il portale supporta attualmente l'API SQL e l'API MongoDB (anteprima).

Il metodo più semplice per indicizzare gli elementi Azure Cosmos DB consiste nell'utilizzare una procedura guidata nel [portale di Azure](https://portal.azure.com/). Eseguendo il campionamento dei dati e leggendo i metadati nel contenitore, la procedura guidata [**Importa dati**](search-import-data-portal.md) in Azure ricerca cognitiva possibile creare un indice predefinito, eseguire il mapping dei campi di origine ai campi dell'indice di destinazione e caricare l'indice in un'unica operazione. A seconda delle dimensioni e della complessità dei dati di origine, si può ottenere un indice di ricerca full-text operativo in pochi minuti.

Si consiglia di usare la stessa sottoscrizione di Azure per ricerca cognitiva e Azure Cosmos DB di Azure, preferibilmente nella stessa area.

### <a name="1---prepare-source-data"></a>1 - Preparare i dati di origine

È necessario avere un account Cosmos DB, un database Azure Cosmos DB mappato all'API SQL, l'API MongoDB (anteprima) o l'API Gremlin (anteprima) e il contenuto nel database.

Verificare che il database di Cosmos DB contenga dati. La [procedura guidata Importa dati](search-import-data-portal.md) legge i metadati ed esegue il campionamento dei dati per dedurre uno schema di indice, ma carica anche i dati da Cosmos DB. Se i dati risultano mancanti, la procedura guidata viene arrestata con l'errore "errore durante il rilevamento dello schema dell'indice dall'origine dati: Impossibile compilare un indice del prototipo perché DataSource ' emptycollection ' non ha restituito dati".

### <a name="2---start-import-data-wizard"></a>2 - Avviare la procedura guidata Importa dati

È possibile [avviare la procedura guidata](search-import-data-portal.md) dalla barra dei comandi nella pagina del servizio ricerca cognitiva di Azure o, se ci si connette a Cosmos DB API SQL, è possibile fare clic su **Aggiungi ricerca cognitiva di Azure** nella sezione **Impostazioni** della parte sinistra dell'account del Cosmos DB riquadro di spostamento.

   ![Comando Importa dati nel portale](./media/search-import-data-portal/import-data-cmd2.png "Avviare la procedura guidata Importa dati")

### <a name="3---set-the-data-source"></a>3 - Impostare l'origine dati

Nella pagina **origine dati** , l'origine deve essere **Cosmos DB**, con le specifiche seguenti:

+ **Nome** è il nome dell'oggetto origine dati. Una volta creato, è possibile sceglierlo per altri carichi di lavoro.

+ **Cosmos DB account** deve essere la stringa di connessione primaria o secondaria di Cosmos DB, con un `AccountEndpoint` e un `AccountKey`. Per le raccolte MongoDB, aggiungere **tipologia API = MongoDB** alla fine della stringa di connessione e separarla dalla stringa di connessione con un punto e virgola. Per l'API Gremlin e API Cassandra, usare le istruzioni per l' [API REST](#cosmosdb-indexer-rest).

+ Il **database** è un database esistente dall'account. 

+ La **raccolta** è un contenitore di documenti. Per consentire l'importazione, è necessario che i documenti esistano. 

+ La **query** può essere vuota se si desiderano tutti i documenti. in caso contrario, è possibile immettere una query che seleziona un subset del documento. La **query** è disponibile solo per l'API SQL.

   ![Definizione dell'origine dati Cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-datasource.png "Definizione dell'origine dati Cosmos DB")

### <a name="4---skip-the-enrich-content-page-in-the-wizard"></a>4-ignorare la pagina "arricchisci contenuto" della procedura guidata

L'aggiunta di competenze cognitive (o arricchimento) non è un requisito di importazione. A meno che non sia necessario aggiungere l' [arricchimento di intelligenza artificiale](cognitive-search-concept-intro.md) alla pipeline di indicizzazione, è consigliabile ignorare questo passaggio.

Per ignorare il passaggio, fare clic sui pulsanti blu nella parte inferiore della pagina per "Avanti" e "Ignora".

### <a name="5---set-index-attributes"></a>5 - Impostare gli attributi dell'indice

Nella pagina **Indice** dovrebbe essere presente un elenco di campi con un tipo di dati e una serie di caselle di controllo per l'impostazione degli attributi di indice. La procedura guidata può generare un elenco di campi in base ai metadati e al campionamento dei dati di origine. 

È possibile selezionare in blocco gli attributi facendo clic sulla casella di controllo nella parte superiore di una colonna attributo. Scegliere **recuperabile** e **ricercabile** per ogni campo che deve essere restituito a un'app client e soggetto all'elaborazione della ricerca full-text. Si noterà che gli Integer non sono full-text o ricerca fuzzy (i numeri vengono valutati Verbatim e spesso sono utili nei filtri).

Per ulteriori informazioni, vedere la descrizione degli [attributi degli indici](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) e degli [analizzatori della lingua](https://docs.microsoft.com/rest/api/searchservice/language-support) . 

Dedicare qualche momento alla revisione delle selezioni. Con l'esecuzione della procedura guidata vengono create strutture dei dati fisiche e non è possibile eliminare questi campi senza eliminare e ricreare tutti gli oggetti.

   ![Definizione dell'indice Cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-index-schema.png "Definizione dell'indice Cosmos DB")

### <a name="6---create-indexer"></a>6 - Creare l'indicizzatore

Nella sua specifica completa, la procedura guidata crea tre oggetti distinti nel servizio di ricerca. Un oggetto origine dati e un oggetto indice vengono salvati come risorse denominate nel servizio ricerca cognitiva di Azure. L'ultimo passaggio crea un oggetto indicizzatore. La denominazione dell'indicizzatore ne consente l'esistenza come risorsa autonoma, che può essere pianificata e gestita in modo indipendente dagli oggetti indice e origine dati, creati nella stessa sequenza della procedura guidata.

Se non si ha familiarità con gli indicizzatori, un *indicizzatore* è una risorsa in Azure ricerca cognitiva che esegue la ricerca per indicizzazione di un'origine dati esterna per il contenuto ricercabile. L'output della procedura guidata **Importa dati** è un indicizzatore che esegue la ricerca per indicizzazione dell'origine dati Cosmos DB, estrae contenuto ricercabile e lo importa in un indice in Azure ricerca cognitiva.

La schermata seguente illustra la configurazione dell'indicizzatore predefinita. È possibile passare a **una sola volta** se si desidera eseguire l'indicizzatore una sola volta. Fare clic su **Invia** per eseguire la procedura guidata e creare tutti gli oggetti. L'indicizzazione inizia immediatamente.

   ![Definizione dell'indicizzatore Cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-indexer.png "Definizione dell'indicizzatore Cosmos DB")

È possibile monitorare l'importazione dei dati nelle pagine del portale. Le notifiche di stato indicano lo stato dell'indicizzazione e il numero di documenti caricati. 

Al termine dell'indicizzazione, è possibile usare [Esplora ricerche](search-explorer.md) per eseguire query sull'indice.

> [!NOTE]
> Se non vengono visualizzati i dati previsti, potrebbe essere necessario impostare più attributi in altri campi. Eliminare l'indice e l'indicizzatore appena creati, quindi eseguire di nuovo la procedura guidata modificando le selezioni per gli attributi di indice nel passaggio 5. 

<a name="cosmosdb-indexer-rest"></a>

## <a name="use-rest-apis"></a>Usare le API REST

È possibile usare l'API REST per indicizzare i dati Azure Cosmos DB, seguendo un flusso di lavoro in tre parti comune a tutti gli indicizzatori in Azure ricerca cognitiva: creare un'origine dati, creare un indice, creare un indicizzatore. L'estrazione dei dati da Cosmos DB si verifica quando si invia la richiesta create Indexer. Al termine di questa richiesta, sarà presente un indice Queryable. 

> [!NOTE]
> Per l'indicizzazione dei dati da Cosmos DB API Gremlin o Cosmos DB API Cassandra è necessario prima di tutto richiedere l'accesso alle anteprime gestite compilando [questo modulo](https://aka.ms/azure-cognitive-search/indexer-preview). Al termine dell'elaborazione della richiesta, si riceveranno istruzioni su come usare l' [API REST versione 2019-05-06-Preview](search-api-preview.md) per creare l'origine dati.

In precedenza in questo articolo si è indicato che [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/index-overview) indicizzazione e l'indicizzazione di [Azure ricerca cognitiva Indexing](search-what-is-an-index.md) sono operazioni distinte. Per l'indicizzazione Cosmos DB, per impostazione predefinita tutti i documenti vengono indicizzati automaticamente tranne che con il API Cassandra. Se si disattiva l'indicizzazione automatica, è possibile accedere ai documenti solo tramite i relativi collegamenti automatici o tramite query usando l'ID del documento. L'indicizzazione di Azure ricerca cognitiva richiede l'abilitazione dell'indicizzazione automatica Cosmos DB nella raccolta che verrà indicizzata da Azure ricerca cognitiva. Quando si effettua l'iscrizione per l'anteprima di Cosmos DB API Cassandra indexer, verranno fornite istruzioni su come configurare Cosmos DB l'indicizzazione.

> [!WARNING]
> Azure Cosmos DB è la nuova generazione di DocumentDB. In precedenza con l'API versione **2017-11-11** è possibile usare la sintassi `documentdb`. Ciò significava che è possibile specificare il tipo di origine dati come `cosmosdb` o `documentdb`. A partire dalla versione API **2019-05-06** , le API e il portale di Azure ricerca cognitiva supportano solo la sintassi `cosmosdb`, come indicato in questo articolo. Questo significa che il tipo di origine dati deve `cosmosdb` se si desidera connettersi a un endpoint di Cosmos DB.

### <a name="1---assemble-inputs-for-the-request"></a>1-assemblare gli input per la richiesta

Per ogni richiesta, è necessario fornire il nome del servizio e la chiave amministratore per Azure ricerca cognitiva (nell'intestazione POST) e il nome e la chiave dell'account di archiviazione per l'archiviazione BLOB. È possibile usare il [post](search-get-started-postman.md) per inviare richieste HTTP ad Azure ricerca cognitiva.

Copiare i quattro valori seguenti nel blocco note in modo che sia possibile incollarli in una richiesta:

+ Nome del servizio ricerca cognitiva di Azure
+ Chiave amministratore di Azure ricerca cognitiva
+ Stringa di connessione Cosmos DB

È possibile trovare questi valori nel portale:

1. Nelle pagine del portale per ricerca cognitiva di Azure copiare l'URL del servizio di ricerca dalla pagina panoramica.

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

| Campo   | DESCRIZIONE |
|---------|-------------|
| **nome** | Obbligatorio. Scegliere un nome qualsiasi per rappresentare l'oggetto origine dati. |
|**type**| Obbligatorio. Deve essere `cosmosdb`. |
|**credentials** | Obbligatorio. Deve essere una stringa di connessione Cosmos DB.<br/>Per le raccolte SQL, le stringhe di connessione sono in questo formato: `AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`<br/><br/>Per le raccolte MongoDB, aggiungere **tipologia API = MongoDB** alla stringa di connessione:<br/>`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`<br/><br/>Per i grafici Gremlin e le tabelle Cassandra, iscriversi all' [anteprima dell'indicizzatore gestita](https://aka.ms/azure-cognitive-search/indexer-preview) per ottenere l'accesso all'anteprima e informazioni su come formattare le credenziali.<br/><br/>Evitare i numeri di porta nell'URL dell'endpoint. Se si include il numero di porta, Azure ricerca cognitiva non sarà in grado di indicizzare il database Azure Cosmos DB.|
| **container** | Contiene gli elementi seguenti: <br/>**name**: obbligatorio. Consente di specificare l'ID della raccolta di database da indicizzare.<br/>**query**: facoltativa. È possibile specificare una query per rendere flat un documento JSON arbitrario in uno schema flat che può essere indicizzato da Azure ricerca cognitiva.<br/>Per l'API MongoDB, l'API Gremlin e API Cassandra, le query non sono supportate. |
| **dataChangeDetectionPolicy** | Consigliato. Vedere la sezione [Indicizzazione di documenti modificati](#DataChangeDetectionPolicy).|
|**dataDeletionDetectionPolicy** | Facoltativa. Vedere la sezione [Indicizzazione di documenti eliminati](#DataDeletionDetectionPolicy).|

### <a name="using-queries-to-shape-indexed-data"></a>Utilizzo di query per formare dati indicizzati
È possibile specificare una query di SQL per appiattire le matrici o le proprietà annidate, progettare le proprietà JSON e filtrare i dati da indicizzare. 

> [!WARNING]
> Le query personalizzate non sono supportate per l' **API MongoDB**, l' **api Gremlin**e **API Cassandra**: `container.query` parametro deve essere impostato su null o omesso. Se è necessario usare una query personalizzata, inviare un messaggio su [User Voice](https://feedback.azure.com/forums/263029-azure-search).

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

[Creare un indice di ricerca cognitiva di Azure di destinazione](/rest/api/searchservice/create-index) se non ne è già presente uno. Nell'esempio seguente viene creato un indice con un campo ID e Descrizione:

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
> Per le raccolte partizionate, la chiave del documento predefinita è Azure Cosmos DB proprietà `_rid`, che ricerca cognitiva Azure Rinomina automaticamente `rid` perché i nomi dei campi non possono iniziare con un carattere di sottolineatura. Inoltre, i valori Azure Cosmos DB `_rid` contengono caratteri non validi nelle chiavi di ricerca cognitiva di Azure. Per questo motivo, i valori `_rid` presentano la codificata Base64.
> 
> Per le raccolte MongoDB, Azure ricerca cognitiva Rinomina automaticamente la proprietà `_id` in `doc_id`.  

### <a name="mapping-between-json-data-types-and-azure-cognitive-search-data-types"></a>Mapping tra tipi di dati JSON e tipi di dati ricerca cognitiva di Azure
| Tipo di dati JSON | Tipi di campi dell'indice di destinazione compatibili |
| --- | --- |
| Bool |Edm.Boolean, Edm.String |
| Numeri che rappresentano numeri interi |Edm.Int32, Edm.Int64, Edm.String |
| Numeri che rappresentano numeri a virgola mobile |Edm.Double, Edm.String |
| String |Edm.String |
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

Per ulteriori informazioni sulla definizione delle pianificazioni degli indicizzatori, vedere [How to Schedule Indexers for Azure ricerca cognitiva](search-howto-schedule-indexers.md).

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

Per abilitare l'avanzamento incrementale quando si usa una query personalizzata, assicurarsi che la query ordini i risultati in base alla colonna `_ts`. In questo modo viene abilitato il controllo periodico che Azure ricerca cognitiva USA per fornire lo stato di avanzamento incrementale in presenza di errori.   

In alcuni casi, anche se la query contiene una clausola `ORDER BY [collection alias]._ts`, Azure ricerca cognitiva potrebbe non dedurre che la query venga ordinata in base al `_ts`. È possibile indicare ad Azure ricerca cognitiva che i risultati vengono ordinati usando la proprietà di configurazione `assumeOrderByHighWaterMarkColumn`. Per specificare questo hint, creare o aggiornare l'indicizzatore come indicato di seguito: 

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

Congratulazioni! Si è appreso come integrare Azure Cosmos DB con ricerca cognitiva di Azure usando un indicizzatore.

* Per altre informazioni su Azure Cosmos DB, vedere la [pagina del servizio Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).
* Per ulteriori informazioni su Azure ricerca cognitiva, vedere la [pagina del servizio di ricerca](https://azure.microsoft.com/services/search/).
