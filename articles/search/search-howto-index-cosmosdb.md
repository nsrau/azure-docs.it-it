---
title: Cerca nei dati Azure Cosmos DB
titleSuffix: Azure Cognitive Search
description: Importa i dati da Azure Cosmos DB in un indice ricercabile in Azure ricerca cognitiva. Gli indicizzatori automatizzano l'inserimento di dati per alcune origini dati come Azure Cosmos DB.
author: mgottein
manager: nitinme
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/11/2020
ms.openlocfilehash: aed1aa03527481014a63c636181725b91b17a1e8
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94697306"
---
# <a name="how-to-index-cosmos-db-data-using-an-indexer-in-azure-cognitive-search"></a>Come indicizzare i dati di Cosmos DB usando un indicizzatore in Ricerca cognitiva di Azure 

> [!IMPORTANT] 
> L'API SQL è disponibile a livello generale.
> L'API MongoDB, l'API Gremlin e il supporto API Cassandra sono attualmente disponibili in anteprima pubblica. La funzionalità di anteprima viene fornita senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). È possibile richiedere l'accesso alle anteprime compilando [questo modulo](https://aka.ms/azure-cognitive-search/indexer-preview). 
> Le [versioni di anteprima dell'API REST](search-api-preview.md) forniscono queste funzionalità. Il supporto del portale è attualmente limitato e non è disponibile alcun supporto per .NET SDK.

> [!WARNING]
> Solo Cosmos DB raccolte con [criteri di indicizzazione](/azure/cosmos-db/index-policy) impostati su [coerente](/azure/cosmos-db/index-policy#indexing-mode) sono supportate da ricerca cognitiva di Azure. L'indicizzazione di raccolte con criteri di indicizzazione differita non è consigliata e può comportare la mancanza di dati. Le raccolte con indicizzazione disabilitata non sono supportate.

Questo articolo illustra come configurare un [indicizzatore](search-indexer-overview.md) di Azure Cosmos DB per estrarre il contenuto e renderlo ricercabile in Azure ricerca cognitiva. Questo flusso di lavoro crea un indice di ricerca cognitiva di Azure e lo carica con il testo esistente Estratto da Azure Cosmos DB. 

Poiché la terminologia può creare confusione, vale la pena notare che [Azure Cosmos DB indicizzazione](/azure/cosmos-db/index-overview) e l' [indicizzazione di Azure ricerca cognitiva](search-what-is-an-index.md) sono operazioni distinte, univoche per ogni servizio. Prima di avviare l'indicizzazione di Azure ricerca cognitiva, è necessario che il database di Azure Cosmos DB esista già e che contenga dati.

L'indicizzatore Cosmos DB in Azure ricerca cognitiva può eseguire la ricerca per indicizzazione di [Azure Cosmos DB elementi](../cosmos-db/databases-containers-items.md#azure-cosmos-items) a cui si accede tramite protocolli 

+ Per l' [API SQL](../cosmos-db/sql-query-getting-started.md), disponibile a livello generale, è possibile usare il [portale](#cosmos-indexer-portal), l' [API REST](/rest/api/searchservice/indexer-operations)o [.NET SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexer) per creare l'origine dati e l'indicizzatore.

+ Per l' [API MongoDB (anteprima)](../cosmos-db/mongodb-introduction.md), è possibile usare il [portale](#cosmos-indexer-portal) o l' [API REST versione 2020-06-30-Preview](search-api-preview.md) per creare l'origine dati e l'indicizzatore.

+ Per [API Cassandra (anteprima)](../cosmos-db/cassandra-introduction.md) e l' [API Gremlin (anteprima)](../cosmos-db/graph-introduction.md), è possibile usare solo l' [API REST versione 2020-06-30-Preview](search-api-preview.md) per creare l'origine dati e l'indicizzatore.


> [!Note]
> È possibile eseguire il cast di un voto sulla voce dell'utente per la [API tabella](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab) se si vuole visualizzarla come supportata in Azure ricerca cognitiva.
>

<a name="cosmos-indexer-portal"></a>

## <a name="use-the-portal"></a>Usare il portale

> [!Note]
> Il portale supporta attualmente l'API SQL e l'API MongoDB (anteprima).

Il metodo più semplice per indicizzare gli elementi Azure Cosmos DB consiste nell'utilizzare una procedura guidata nel [portale di Azure](https://portal.azure.com/). Eseguendo il campionamento dei dati e leggendo i metadati nel contenitore, la procedura guidata [**Importa dati**](search-import-data-portal.md) in Azure ricerca cognitiva possibile creare un indice predefinito, eseguire il mapping dei campi di origine ai campi dell'indice di destinazione e caricare l'indice in un'unica operazione. A seconda delle dimensioni e della complessità dei dati di origine, si può ottenere un indice di ricerca full-text operativo in pochi minuti.

È consigliabile usare la stessa area o la stessa località sia per ricerca cognitiva di Azure che per Azure Cosmos DB per una latenza più bassa ed evitare costi di larghezza di banda.

### <a name="1---prepare-source-data"></a>1 - Preparare i dati di origine

È necessario avere un account Cosmos DB, un database Azure Cosmos DB mappato all'API SQL, l'API MongoDB (anteprima) o l'API Gremlin (anteprima) e il contenuto nel database.

Verificare che il database di Cosmos DB contenga dati. La [procedura guidata Importa dati](search-import-data-portal.md) legge i metadati ed esegue il campionamento dei dati per dedurre uno schema di indice, ma carica anche i dati da Cosmos DB. Se i dati risultano mancanti, la procedura guidata viene arrestata con l'errore "errore durante il rilevamento dello schema dell'indice dall'origine dati: Impossibile compilare un indice del prototipo perché DataSource ' emptycollection ' non ha restituito dati".

### <a name="2---start-import-data-wizard"></a>2 - Avviare la procedura guidata Importa dati

È possibile [avviare la procedura guidata](search-import-data-portal.md) dalla barra dei comandi nella pagina del servizio ricerca cognitiva di Azure o, se ci si connette a Cosmos DB API SQL, è possibile fare clic su **Aggiungi ricerca cognitiva di Azure** nella sezione **Impostazioni** del riquadro di spostamento a sinistra dell'account di Cosmos DB.

   ![Comando Importa dati nel portale](./media/search-import-data-portal/import-data-cmd2.png "Avviare la procedura guidata Importa dati")

### <a name="3---set-the-data-source"></a>3 - Impostare l'origine dati

Nella pagina **origine dati** , l'origine deve essere **Cosmos DB**, con le specifiche seguenti:

+ **Nome** è il nome dell'oggetto origine dati. Una volta creato, è possibile sceglierlo per altri carichi di lavoro.

+ **Cosmos DB account** deve essere in uno dei formati seguenti:
    1. Stringa di connessione primaria o secondaria di Cosmos DB con il formato seguente: `AccountEndpoint=https://<Cosmos DB account name>.documents.azure.com;AccountKey=<Cosmos DB auth key>;` .
        + Per la versione 3,2 e la versione 3,6 le **raccolte MongoDB** usano il formato seguente per l'account Cosmos DB nel portale di Azure: `AccountEndpoint=https://<Cosmos DB account name>.documents.azure.com;AccountKey=<Cosmos DB auth key>;ApiKind=MongoDb`
        + Per i **grafici Gremlin e le tabelle Cassandra**, iscriversi all' [anteprima dell'indicizzatore gestita](https://aka.ms/azure-cognitive-search/indexer-preview) per ottenere l'accesso all'anteprima e informazioni su come formattare le credenziali.
    1.  Una stringa di connessione di identità gestita con il formato seguente che non include una chiave dell'account: `ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.DocumentDB/databaseAccounts/<your cosmos db account name>/;(ApiKind=[api-kind];)` . Per usare questo formato della stringa di connessione, seguire le istruzioni per la [configurazione di una connessione dell'indicizzatore a un database di Cosmos DB usando un'identità gestita](search-howto-managed-identities-cosmos-db.md).

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

Per ulteriori informazioni, vedere la descrizione degli [attributi degli indici](/rest/api/searchservice/create-index#bkmk_indexAttrib) e degli [analizzatori della lingua](/rest/api/searchservice/language-support) . 

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
> Per l'indicizzazione dei dati da Cosmos DB API Gremlin o Cosmos DB API Cassandra è necessario prima di tutto richiedere l'accesso alle anteprime gestite compilando [questo modulo](https://aka.ms/azure-cognitive-search/indexer-preview). Al termine dell'elaborazione della richiesta, si riceveranno istruzioni su come usare l' [API REST versione 2020-06-30-Preview](search-api-preview.md) per creare l'origine dati.

In precedenza in questo articolo si è indicato che [Azure Cosmos DB](/azure/cosmos-db/index-overview) indicizzazione e l'indicizzazione di [Azure ricerca cognitiva Indexing](search-what-is-an-index.md) sono operazioni distinte. Per l'indicizzazione Cosmos DB, per impostazione predefinita tutti i documenti vengono indicizzati automaticamente tranne che con il API Cassandra. Se si disattiva l'indicizzazione automatica, è possibile accedere ai documenti solo tramite i relativi collegamenti automatici o tramite query usando l'ID del documento. L'indicizzazione di Azure ricerca cognitiva richiede l'abilitazione dell'indicizzazione automatica Cosmos DB nella raccolta che verrà indicizzata da Azure ricerca cognitiva. Quando si effettua l'iscrizione per l'anteprima di Cosmos DB API Cassandra indexer, verranno fornite istruzioni su come configurare Cosmos DB l'indicizzazione.

> [!WARNING]
> Azure Cosmos DB è la nuova generazione di DocumentDB. In precedenza con l'API versione **2017-11-11** è possibile usare la `documentdb` sintassi. Ciò significava che è possibile specificare il tipo di origine dati come `cosmosdb` o `documentdb` . A partire dalla versione API **2019-05-06** , le API e il portale di Azure ricerca cognitiva supportano solo la `cosmosdb` sintassi come indicato in questo articolo. Questo significa che il tipo di origine dati deve essere `cosmosdb` connesso a un endpoint Cosmos DB.

### <a name="1---assemble-inputs-for-the-request"></a>1-assemblare gli input per la richiesta

Per ogni richiesta, è necessario fornire il nome del servizio e la chiave amministratore per Azure ricerca cognitiva (nell'intestazione POST) e il nome e la chiave dell'account di archiviazione per l'archiviazione BLOB. Per inviare richieste HTTP ad Azure ricerca cognitiva, è possibile usare il [post o il Visual Studio Code](search-get-started-rest.md) .

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

```http

    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
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
```

Il corpo della richiesta contiene la definizione dell'origine dati, che deve includere i campi seguenti:

| Campo   | Descrizione |
|---------|-------------|
| **nome** | Obbligatorio. Scegliere un nome qualsiasi per rappresentare l’oggetto origine dati. |
|**type**| Obbligatorio. Deve essere `cosmosdb`. |
|**credentials** | Obbligatorio. Deve seguire il formato della stringa di connessione Cosmos DB o un formato della stringa di connessione identità gestita.<br/><br/>Per le **raccolte SQL**, le stringhe di connessione possono seguire uno dei formati seguenti: <li>`AccountEndpoint=https://<Cosmos DB account name>.documents.azure.com;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`<li>Una stringa di connessione di identità gestita con il formato seguente che non include una chiave dell'account: `ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.DocumentDB/databaseAccounts/<your cosmos db account name>/;` . Per usare questo formato della stringa di connessione, seguire le istruzioni per la [configurazione di una connessione dell'indicizzatore a un database di Cosmos DB usando un'identità gestita](search-howto-managed-identities-cosmos-db.md).<br/><br/>Per la versione 3,2 e la versione 3,6 le **raccolte MongoDB** utilizzano uno dei formati seguenti per la stringa di connessione: <li>`AccountEndpoint=https://<Cosmos DB account name>.documents.azure.com;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`<li>Una stringa di connessione di identità gestita con il formato seguente che non include una chiave dell'account: `ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.DocumentDB/databaseAccounts/<your cosmos db account name>/;ApiKind=MongoDb;` . Per usare questo formato della stringa di connessione, seguire le istruzioni per la [configurazione di una connessione dell'indicizzatore a un database di Cosmos DB usando un'identità gestita](search-howto-managed-identities-cosmos-db.md).<br/><br/>Per i **grafici Gremlin e le tabelle Cassandra**, iscriversi all' [anteprima dell'indicizzatore gestita](https://aka.ms/azure-cognitive-search/indexer-preview) per ottenere l'accesso all'anteprima e informazioni su come formattare le credenziali.<br/><br/>Evitare i numeri di porta nell'URL dell'endpoint. Se si include il numero di porta, Azure ricerca cognitiva non sarà in grado di indicizzare il database Azure Cosmos DB.|
| **container** | Contiene gli elementi seguenti: <br/>**name**: Obbligatorio. Specificare l'ID della raccolta di database da indicizzare.<br/>**query**: Facoltativa. È possibile specificare una query per rendere flat un documento JSON arbitrario in modo da ottenere uno schema flat che possa essere indicizzato da Ricerca cognitiva di Azure.<br/>Per le API MongoDB, Gremlin e Apache Cassandra, le query non sono supportate. |
| **dataChangeDetectionPolicy** | Consigliato. Vedere la sezione [Indicizzazione di documenti modificati](#DataChangeDetectionPolicy).|
|**dataDeletionDetectionPolicy** | facoltativo. Vedere la sezione [Indicizzazione di documenti eliminati](#DataDeletionDetectionPolicy).|

### <a name="using-queries-to-shape-indexed-data"></a>Utilizzo di query per formare dati indicizzati
È possibile specificare una query di SQL per appiattire le matrici o le proprietà annidate, progettare le proprietà JSON e filtrare i dati da indicizzare. 

> [!WARNING]
> Le query personalizzate non sono supportate per l' **API MongoDB**, l' **api Gremlin** e **API Cassandra**: il `container.query` parametro deve essere impostato su null o omesso. Se è necessario usare una query personalizzata, inviare un messaggio su [User Voice](https://feedback.azure.com/forums/263029-azure-search).

Documento di esempio:

```http
    {
        "userId": 10001,
        "contact": {
            "firstName": "andy",
            "lastName": "hoh"
        },
        "company": "microsoft",
        "tags": ["azure", "cosmosdb", "search"]
    }
```

Query di filtro:

```sql
SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark ORDER BY c._ts
```

Query di appiattimento:

```sql
SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
```

Query di proiezione:

```sql
SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
```

Query di appiattimento matrici:

```sql
SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts
```

### <a name="3---create-a-target-search-index"></a>3: creare un indice di ricerca di destinazione 

[Creare un indice di ricerca cognitiva di Azure di destinazione](/rest/api/searchservice/create-index) se non ne è già presente uno. Nell'esempio seguente viene creato un indice con un campo ID e Descrizione:

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
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
```

Assicurarsi che lo schema dell'indice di destinazione sia compatibile con lo schema dei documenti JSON di origine oppure con l'output della proiezione di query personalizzata.

> [!NOTE]
> Per le raccolte partizionate, la chiave del documento predefinita è Azure Cosmos DB `_rid` proprietà, a cui Azure ricerca cognitiva automaticamente rinominare `rid` perché i nomi dei campi non possono iniziare con un carattere di sottolineatura. Inoltre, `_rid` i valori Azure Cosmos DB contengono caratteri non validi nelle chiavi di ricerca cognitiva di Azure. Per questo motivo, i valori `_rid` presentano la codificata Base64.
> 
> Per le raccolte MongoDB, Azure ricerca cognitiva Rinomina automaticamente la `_id` Proprietà in `id` .  

### <a name="mapping-between-json-data-types-and-azure-cognitive-search-data-types"></a>Mapping tra tipi di dati JSON e tipi di dati ricerca cognitiva di Azure
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

### <a name="4---configure-and-run-the-indexer"></a>4-configurare ed eseguire l'indicizzatore

Dopo aver creato l'indice e l'origine dati, è possibile creare l'indicizzatore:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mycosmosdbindexer",
      "dataSourceName" : "mycosmosdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }
```

L'indicizzatore verrà eseguito ogni due ore (l'intervallo di pianificazione è impostato su "PT2H"). Per eseguire un indicizzatore ogni 30 minuti, impostare l'intervallo su "PT30M". L'intervallo minimo supportato è di 5 minuti. La pianificazione è facoltativa: se omessa, l'indicizzatore viene eseguito una sola volta al momento della creazione. Tuttavia, è possibile eseguire un indicizzatore su richiesta in qualsiasi momento.   

Per altre informazioni sull'API di creazione di un indicizzatore, vedere [Creare un indicizzatore](/rest/api/searchservice/create-indexer).

Per ulteriori informazioni sulla definizione delle pianificazioni degli indicizzatori, vedere [How to Schedule Indexers for Azure ricerca cognitiva](search-howto-schedule-indexers.md).

## <a name="use-net"></a>Usare .NET

.NET SDK disponibile a livello generale ha una parità completa con l'API REST disponibile a livello generale. È consigliabile rivedere la sezione relativa dall'API REST per apprenderne i concetti, il flusso di lavoro e i requisiti. Consultare quindi la seguente documentazione di riferimento sull'API .NET per implementare un indicizzatore JSON nel codice gestito.

+ [azure.search.documents. indexes. Models. searchindexerdatasourceconnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection)
+ [azure.search.documents. indexes. Models. searchindexerdatasourcetype](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype)
+ [azure.search.documents. indexes. Models. Searchindex](/dotnet/api/azure.search.documents.indexes.models.searchindex)
+ [azure.search.documents. indexes. Models. searchindexer](/dotnet/api/azure.search.documents.indexes.models.searchindexer)

<a name="DataChangeDetectionPolicy"></a>

## <a name="indexing-changed-documents"></a>Indicizzazione di documenti modificati

Lo scopo di un criterio di rilevamento delle modifiche dei dati è quello di identificare in modo efficace gli elementi di dati modificati. Attualmente, l'unico criterio supportato è l' [`HighWaterMarkChangeDetectionPolicy`](/dotnet/api/azure.search.documents.indexes.models.highwatermarkchangedetectionpolicy) utilizzo della `_ts` Proprietà (timestamp) fornita da Azure Cosmos DB, specificata come indicato di seguito:

```http
    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }
```

L'uso di questi criteri è consigliato per garantire elevate prestazioni dell'indicizzatore. 

Se si usa una query personalizzata, assicurarsi che la proprietà `_ts` sia progettata dalla query.

<a name="IncrementalProgress"></a>

### <a name="incremental-progress-and-custom-queries"></a>Avanzamento incrementale e query personalizzate

L'avanzamento incrementale durante l'indicizzazione assicura che, in caso di interruzione dell'esecuzione dell'indicizzatore a causa di errori temporanei o del limite del tempo di esecuzione, l'indicizzatore possa riprendere dal punto in cui è stato interrotto all'esecuzione successiva, invece di dovere ripetere dall'inizio l'indicizzazione dell'intera raccolta. Questo approccio risulta particolarmente importante in caso di indicizzazione di raccolte di grandi dimensioni. 

Per abilitare l'avanzamento incrementale quando si usa una query personalizzata, assicurarsi che la query ordini i risultati in base alla colonna `_ts`. In questo modo viene abilitato il controllo periodico che Azure ricerca cognitiva USA per fornire lo stato di avanzamento incrementale in presenza di errori.   

In alcuni casi, anche se la query contiene una `ORDER BY [collection alias]._ts` clausola, Azure ricerca cognitiva potrebbe non dedurre che la query è ordinata in base a `_ts` . È possibile indicare ad Azure ricerca cognitiva che i risultati vengono ordinati usando la `assumeOrderByHighWaterMarkColumn` proprietà di configurazione. Per specificare questo hint, creare o aggiornare l'indicizzatore come indicato di seguito: 

```http
    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 
```

<a name="DataDeletionDetectionPolicy"></a>

## <a name="indexing-deleted-documents"></a>Indicizzazione di documenti eliminati

Quando si eliminano righe dalla raccolta, in genere le si elimina anche dall'indice di ricerca. Scopo dei criteri di rilevamento dell'eliminazione dei dati è quello di identificare in modo efficace gli elementi di dati eliminati. Attualmente, l'unico criterio supportato è il criterio `Soft Delete` (l'eliminazione è contrassegnata da un tipo di flag), specificato come indicato sotto:

```http
    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }
```

Se si usa una query personalizzata, assicurarsi che la proprietà a cui fa riferimento `softDeleteColumnName` sia progettata dalla query.

L'esempio seguente crea un'origine dati con criteri di eliminazione temporanea:

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
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
```

## <a name="next-steps"></a><a name="NextSteps"></a>Passaggi successivi

Congratulazioni! Si è appreso come integrare Azure Cosmos DB con ricerca cognitiva di Azure usando un indicizzatore.

* Per altre informazioni su Azure Cosmos DB, vedere la [pagina del servizio Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).
* Per ulteriori informazioni su Azure ricerca cognitiva, vedere la [pagina del servizio di ricerca](https://azure.microsoft.com/services/search/).