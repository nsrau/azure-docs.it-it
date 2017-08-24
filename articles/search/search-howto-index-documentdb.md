---
title: Indicizzazione di un'origine dati Cosmos DB per Ricerca di Azure | Microsoft Docs
description: Questo articolo illustra come creare l'indicizzatore di Ricerca di Azure con Cosmos DB come origine dati.
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 
ms.service: search
ms.devlang: rest-api
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: search
ms.date: 05/01/2017
ms.author: eugenesh
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 333f8320820a1729a14ffc2e29446e7452aa768e
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017

---
# <a name="connecting-cosmos-db-with-azure-search-using-indexers"></a>Connessione di Cosmos DB con Ricerca di Azure tramite indicizzatori

Per ottenere un'esperienza di ricerca ottimale sui dati di Cosmos DB, è possibile usare un indicizzatore di Ricerca di Azure per inserire dati in un indice di Ricerca di Azure. Questo articolo illustra come integrare Azure Cosmos DB con Ricerca di Azure senza dover scrivere codice per gestire l'infrastruttura di indicizzazione.

Per configurare un indicizzatore di Cosmos DB, è necessario avere un [servizio Ricerca di Azure](search-create-service-portal.md), creare un indice, l'origine dati e infine l'indicizzatore. È possibile creare questi oggetti usando il [portale](search-import-data-portal.md), [.NET SDK](/dotnet/api/microsoft.azure.search) o l'[API REST](/rest/api/searchservice/) per tutti i linguaggi diversi da .NET. 

Se si sceglie di usare il portale, l'[Importazione guidata dati](search-import-data-portal.md) consente di creare tutte queste risorse.

> [!NOTE]
> Cosmos DB è la nuova generazione di DocumentDB. Anche se il nome del prodotto è cambiato, la sintassi è immutata. Continuare a specificare `documentdb` come indicato in questo articolo sull'indicizzatore. 

> [!TIP]
> È possibile avviare la procedura guidata **Importa dati** dal dashboard di Cosmos DB per semplificare l'indicizzazione dell'origine dati. Nel riquadro di spostamento a sinistra passare a **Raccolte** > **Aggiungi Ricerca di Azure** per iniziare.

<a name="Concepts"></a>
## <a name="azure-search-indexer-concepts"></a>Concetti relativi all'indicizzatore di Ricerca di Azure
Ricerca di Azure supporta la creazione e la gestione di origini dati (incluso Cosmos DB) e di indicizzatori che operano su tali origini dati.

Un'**origine dati** specifica i dati per l'indice, le credenziali e i criteri per l'identificazione delle modifiche apportate ai dati (ad esempio documenti modificati o eliminati nella raccolta). L'origine dati è definita come risorsa indipendente affinché possa essere usata da più indicizzatori.

Un **indicizzatore** descrive la modalità di flusso dei dati dall'origine dati a un indice di ricerca di destinazione. Un indicizzatore consente di:

* Eseguire una copia occasionale dei dati per popolare un indice.
* Sincronizzare un indice con le modifiche nell'origine dati in base a una pianificazione. La pianificazione fa parte della definizione dell'indicizzatore.
* Richiamare aggiornamenti su richiesta in un indice in base alle necessità.

<a name="CreateDataSource"></a>
## <a name="step-1-create-a-data-source"></a>Passaggio 1: Creare un'origine dati
Per creare un'origine dati, creare un POST:

    POST https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myDocDbEndpoint.documents.azure.com;AccountKey=myDocDbAuthKey;Database=myDocDbDatabaseId"
        },
        "container": { "name": "myDocDbCollectionId", "query": null },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        }
    }

Il corpo della richiesta contiene la definizione dell'origine dati, che deve includere i campi seguenti:

* **name**: scegliere qualsiasi nome per rappresentare il database di Cosmos DB.
* **type**: deve essere `documentdb`.
* **Credenziali**
  
  * **connectionString**: obbligatorio. Specificare le informazioni di connessione al database di Azure Cosmos DB nel formato seguente: `AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`
* **contenitore**:
  
  * **name**: obbligatorio. Specificare l'ID della raccolta di Cosmos DB da indicizzare.
  * **query**: facoltativa. È possibile specificare una query per rendere flat un documento JSON arbitrario in modo da ottenere uno schema flat che può essere indicizzato da Ricerca di Azure.
* **dataChangeDetectionPolicy**: consigliato. Vedere la sezione [Indicizzazione di documenti modificati](#DataChangeDetectionPolicy).
* **dataDeletionDetectionPolicy**: facoltativo. Vedere la sezione [Indicizzazione di documenti eliminati](#DataDeletionDetectionPolicy).

### <a name="using-queries-to-shape-indexed-data"></a>Utilizzo di query per formare dati indicizzati
È possibile specificare una query di Cosmos DB per appiattire le matrici o le proprietà annidate, progettare le proprietà JSON e filtrare i dati da indicizzare. 

Documento di esempio:

    {
        "userId": 10001,
        "contact": {
            "firstName": "andy",
            "lastName": "hoh"
        },
        "company": "microsoft",
        "tags": ["azure", "documentdb", "search"]
    }

Query di filtro:

    SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark

Query di appiattimento:

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark
    
    
Query di proiezione:

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark


Query di appiattimento matrici:

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark

<a name="CreateIndex"></a>
## <a name="step-2-create-an-index"></a>Passaggio 2: Creare un indice
Creare un indice di Ricerca di Azure di destinazione, se non ne è già disponibile uno. È possibile creare un indice usando l'[interfaccia utente del portale di Azure](search-create-index-portal.md), l'[API REST di creazione dell'indice](/rest/api/searchservice/create-index) o la [classe Index](/dotnet/api/microsoft.azure.search.models.index).

L'esempio seguente crea un indice con campo descrizione e ID:

    POST https://[service name].search.windows.net/indexes?api-version=2016-09-01
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
> Per le raccolte partizionate, la chiave di documento predefinita è la proprietà `_rid` di Cosmos DB, che viene rinominata `rid` in Ricerca di Azure. I valori `_rid` di Cosmos DB contengono inoltre caratteri non validi per le chiavi di Ricerca di Azure. Per questo motivo, i valori `_rid` presentano la codificata Base64.
> 
> 

### <a name="mapping-between-json-data-types-and-azure-search-data-types"></a>Mapping tra tipi di dati JSON e tipi di dati di Ricerca di Azure
| TIPO DI DATI JSON | TIPI DI CAMPI DELL'INDICE DI DESTINAZIONE COMPATIBILI |
| --- | --- |
| Booleano |Edm.Boolean, Edm.String |
| Numeri che rappresentano numeri interi |Edm.Int32, Edm.Int64, Edm.String |
| Numeri che rappresentano numeri a virgola mobile |Edm.Double, Edm.String |
| String |Edm.String |
| Matrici di tipi primitivi, ad esempio ["a", "b", "c"] |Collection(Edm.String) |
| Stringhe che rappresentano date |Edm.DateTimeOffset, Edm.String |
| Oggetti GeoJSON, ad esempio { "type": "Point", "coordinates": [long, lat] } |Edm.GeographyPoint |
| Altri oggetti JSON |N/D |

<a name="CreateIndexer"></a>
## <a name="step-3-create-an-indexer"></a>Passaggio 3: Creare un indicizzatore

Dopo aver creato l'indice e l'origine dati, è possibile creare l'indicizzatore:

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mydocdbindexer",
      "dataSourceName" : "mydocdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }

L'indicizzatore verrà eseguito ogni due ore (l'intervallo di pianificazione è impostato su "PT2H"). Per eseguire un indicizzatore ogni 30 minuti, impostare l'intervallo su "PT30M". L'intervallo minimo supportato è di 5 minuti. La pianificazione è facoltativa: se omessa, l'indicizzatore viene eseguito una sola volta al momento della creazione. Tuttavia, è possibile eseguire un indicizzatore su richiesta in qualsiasi momento.   

Per altre informazioni sull'API di creazione di un indicizzatore, vedere [Creare un indicizzatore](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

<a id="RunIndexer"></a>
### <a name="running-indexer-on-demand"></a>Esecuzione di un indicizzatore su richiesta
Oltre a essere eseguito periodicamente in base a una pianificazione, un indicizzatore può anche essere chiamato su richiesta:

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2016-09-01
    api-key: [Search service admin key]

> [!NOTE]
> Quando l'API viene eseguita correttamente, la chiamata all'indicizzatore è stata pianificata, ma l'elaborazione effettiva si verifica in modo asincrono. 

È possibile monitorare lo stato dell'indicizzatore nel portale o tramite l'API Get Indexer Status, che viene descritta di seguito. 

<a name="GetIndexerStatus"></a>
### <a name="getting-indexer-status"></a>Ottenere lo stato dell'indicizzatore
È possibile recuperare lo stato e la cronologia di esecuzione di un indicizzatore:

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2016-09-01
    api-key: [Search service admin key]

La risposta contiene lo stato globale dell'indicizzatore, la chiamata all'indicizzatore ultimo (o in corso) e la cronologia delle chiamate recenti.

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

La cronologia di esecuzione contiene fino alle 50 più recenti esecuzioni completate, in ordine cronologico inverso (in modo che l'esecuzione più recente venga visualizzata per prima nella risposta).

<a name="DataChangeDetectionPolicy"></a>
## <a name="indexing-changed-documents"></a>Indicizzazione di documenti modificati
Lo scopo di un criterio di rilevamento delle modifiche dei dati è quello di identificare in modo efficace gli elementi di dati modificati. L'unico tipo di criteri attualmente supportato è `High Water Mark` che usa la proprietà `_ts` (timestamp) fornita da Cosmos DB, specificato come indicato di seguito:

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

L'uso di questi criteri è consigliato per garantire elevate prestazioni dell'indicizzatore. 

Se si usa una query personalizzata, assicurarsi che la proprietà `_ts` sia progettata dalla query. 

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

    POST https://[Search service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myDocDbEndpoint.documents.azure.com;AccountKey=myDocDbAuthKey;Database=myDocDbDatabaseId"
        },
        "container": { "name": "myDocDbCollectionId" },
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
Congratulazioni. Si è appena appreso come integrare Azure Cosmos DB con Ricerca di Azure usando l'indicizzatore per Cosmos DB.

* Per altre informazioni su Azure Cosmos DB, vedere la [pagina del servizio Cosmos DB](https://azure.microsoft.com/services/documentdb/).
* Per altre informazioni su Ricerca di Azure, vedere la [pagina del servizio Ricerca](https://azure.microsoft.com/services/search/).

