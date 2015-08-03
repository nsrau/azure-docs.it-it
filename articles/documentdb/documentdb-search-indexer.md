<properties 
    pageTitle="Connessione di DocumentDB con Ricerca di Azure tramite indicizzatori | Azure" 
    description="Questo articolo illustra come usare l'indicizzatore di Ricerca di Azure con DocumentDB come origine dati."
    services="documentdb" 
    documentationCenter="" 
    authors="aliuy" 
    manager="jhubbard" 
    editor="mimig"/>

<tags 
    ms.service="documentdb" 
    ms.devlang="rest-api" 
    ms.topic="article" 
    ms.tgt_pltfrm="NA" 
    ms.workload="data-services" 
    ms.date="06/16/2015" 
    ms.author="andrl"/>

#Connessione di DocumentDB con Ricerca di Azure tramite indicizzatori

Chi desidera implementare esperienze di ricerca straordinarie nei dati di DocumentDB, può usare l'indicizzatore di Ricerca di Azure per DocumentDB. Questo articolo illustra come integrare Azure DocumentDB con Ricerca di Azure senza dover scrivere codice per gestire l'infrastruttura di indicizzazione.

A tale scopo, è necessario [impostare un account di Ricerca di Azure](../search-get-started.md#start-with-the-free-service) (non è necessario eseguire l'aggiornamento al servizio di ricerca standard) e quindi chiamare l'[API REST di Ricerca di Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx) per creare un'**origine dati** DocumentDB e un **indicizzatore** per tale origine.

##<a id="Concepts"></a>Concetti relativi all'indicizzatore di Ricerca di Azure

Ricerca di Azure supporta la creazione e la gestione di origini dati (incluso DocumentDB) e di indicizzatori che operano su tali origini dati.

Un'**origine dati** specifica quali dati devono essere indicizzati, le credenziali per accedere ai dati e i criteri per consentire a Ricerca di Azure di identificare in modo efficace le modifiche nei dati, ad esempio documenti modificati o eliminati nella raccolta. L'origine dati è definita come risorsa indipendente affinché possa essere usata da più indicizzatori.

Un **indicizzatore** descrive la modalità di flusso dei dati dall'origine dati a un indice di ricerca di destinazione. È consigliabile pianificare la creazione di un indicizzatore per ogni combinazione di indice di destinazione e origine dati. Sebbene vi possano essere più indicizzatori che scrivono nello stesso indice, un indicizzatore può scrivere solo in un unico indice. Un indicizzatore consente di:

- Eseguire una copia occasionale dei dati per popolare un indice.
- Sincronizzare un indice con le modifiche nell'origine dati in base a una pianificazione. La pianificazione fa parte della definizione dell'indicizzatore.
- Richiamare aggiornamenti su richiesta in un indice in base alle necessità. 

##<a id="CreateDataSource"></a>Passaggio 1: Creare un'origine dati

Inviare una richiesta HTTP POST per creare una nuova origine dati nel servizio Ricerca di Azure, includendo le intestazioni di richiesta seguenti.
    
    POST https://[Search service name].search.windows.net/datasources?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]

L'elemento `api-version` è obbligatorio. I valori validi includono `2015-02-28` o versioni successive.

Il corpo della richiesta contiene la definizione dell'origine dati, che deve includere i campi seguenti:

- **name**: nome dell'origine dati.

- **type**: Uso`documentdb`.

- **Credenziali**

    - **connectionString**: richiesto. Specificare le informazioni di connessione al database di Azure DocumentDB nel formato seguente: `AccountEndpoint=<DocumentDB endpoint url>;AccountKey=<DocumentDB auth key>;Database=<DocumentDB database id>`

- **contenitore**:

    - **name**: richiesto. Specificare la raccolta di DocumentDB da indicizzare. 

    - **query**: facoltativa. È possibile specificare una query per rendere flat un documento JSON arbitrario in modo da ottenere uno schema flat che può essere indicizzato da Ricerca di Azure.

- **dataChangeDetectionPolicy**: facoltativa. Vedere la sezione relativa ai [criteri di rilevamento delle modifiche dei dati](#DataChangeDetectionPolicy) di seguito.

- **dataDeletionDetectionPolicy**: facoltativa Vedere la sezione relativa ai [criteri di rilevamento dell'eliminazione dei dati](#DataDeletionDetectionPolicy) di seguito.

###<a id="DataChangeDetectionPolicy"></a>Acquisizione di documenti modificati

Lo scopo di un criterio di rilevamento delle modifiche dei dati è quello di identificare in modo efficace gli elementi di dati modificati. Attualmente, l'unico criterio supportato è il criterio che usa la proprietà `High Water Mark` del timestamp dell'ultima modifica fornita `_ts` da DocumentDB, specificato come indicato sotto:

    { 
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts" 
    } 

Sarà anche necessario aggiungere `_ts` nella proiezione e la clausola `WHERE` per la query. ad esempio:

    SELECT s.id, s.Title, s.Abstract, s._ts FROM Sessions s WHERE s._ts > @HighWaterMark


###<a id="DataDeletionDetectionPolicy"></a>Acquisizione di documenti eliminati

Quando le righe vengono eliminate dalla tabella di origine, devono essere eliminate anche dall'indice di ricerca. Scopo dei criteri di rilevamento dell'eliminazione dei dati è quello di identificare in modo efficace gli elementi di dati eliminati. Attualmente, l'unico criterio supportato è il criterio `Soft Delete` (l'eliminazione è contrassegnata da un tipo di flag), specificato come indicato sotto:

    { 
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted", 
        "softDeleteMarkerValue" : "the value that identifies a document as deleted" 
    }

> [AZURE.NOTE]Sarà necessario includere la proprietà nella clausola SELECT se si usa una proiezione personalizzata.

###<a id="CreateDataSourceExample"></a>Esempio di corpo della richiesta

L'esempio seguente crea un'origine dati con una query personalizzata e hint di criteri:

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myDocDbEndpoint.documents.azure.com;AccountKey=myDocDbAuthKey;Database=myDocDbDatabaseId"
        },
        "container": {
            "name": "myDocDbCollectionId",
            "query": "SELECT s.id, s.Title, s.Abstract, s._ts FROM Sessions s WHERE s._ts > @HighWaterMark" 
        },
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

###Response

Se l'origine dati è stata creata correttamente, si riceve una risposta HTTP 201 - Creato.

##<a id="CreateIndex"></a>Passaggio 2: Creare un indice

Creare un indice di Ricerca di Azure di destinazione, se non ne è già disponibile uno. È possibile eseguire questa operazione dall'[interfaccia utente del portale di Azure](../search-get-started.md#test-service-operations) o usando l'[API di creazione dell'](https://msdn.microsoft.com/library/azure/dn798941.aspx)indice.

	POST https://[Search service name].search.windows.net/indexes?api-version=[api-version]
	Content-Type: application/json
	api-key: [Search service admin key]


Assicurarsi che lo schema dell'indice di destinazione sia compatibile con lo schema dei documenti JSON di origine oppure con l'output della proiezione di query personalizzata.

###Figura A: mapping tra tipi di dati JSON e tipi di dati di Ricerca di Azure

<table style="font-size:12">
    <tr>
        <th>Tipo di dati JSON</th>
        <th>Tipi di campi dell'indice di destinazione compatibili</th>
    </tr>
    <tr>
        <td>Booleano</td>
        <td>Edm.Boolean, Edm.String</td>
    </tr>
    <tr>
        <td>Numeri che rappresentano numeri interi</td>
        <td>Edm.Int32, Edm.Int64, Edm.String</td>
    </tr>
    <tr>
        <td>Numeri che rappresentano numeri a virgola mobile</td>
        <td>Edm.Double, Edm.String</td>
    </tr>
    <tr>
        <td>Stringa</td>
        <td>Edm.String</td>
    </tr>
    <tr>
        <td>
            Matrici di tipi primitivi<br/>
            ad esempio, ["a", "b", "c"]
        </td>
        <td>Collection(Edm.String)</td>
    </tr>
    <tr>
        <td>Stringhe che rappresentano date</td>
        <td>Edm.DateTimeOffset, Edm.String</td>
    </tr>
    <tr>
        <td>
            Oggetti GeoJSON<br/>
            ad esempio, {"type": "Punto", "coordinate": [lunghi, lat]}
        </td>
        <td>Edm.GeographyPoint</td>
    </tr>
    <tr>
        <td>Altri oggetti JSON</td>
        <td>N/D</td>
    </tr>
</table>

###<a id="CreateIndexExample"></a>Esempio di corpo della richiesta

L'esempio seguente crea un indice con campo descrizione e ID:

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

###Response

Se l'indice è stato creato correttamente, si riceve una risposta HTTP 201 - Creato.

##<a id="CreateIndexer"></a>Passaggio 3: Creare un indicizzatore

È possibile creare un nuovo indicizzatore in un servizio Ricerca di Azure usando una richiesta HTTP POST con le intestazioni seguenti.
    
    POST https://[Search service name].search.windows.net/indexers?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]

Il corpo della richiesta contiene la definizione dell'indicizzatore, che deve includere i campi seguenti:

- **name**: richiesto. Nome dell'indicizzatore.

- **dataSourceName**: richiesto. Nome di un'origine dati esistente.

- **targetIndexName**: richiesto. Nome di un indice esistente.

- **pianificazione**: facoltativa. Vedere la sezione relativa alla [pianificazione dell'indicizzazione](#IndexingSchedule) di seguito.

###<a id="IndexingSchedule"></a>Esecuzione di indicizzatori in base a una pianificazione

Facoltativamente, un indicizzatore può specificare una pianificazione. Se è presente una pianificazione, l'indicizzatore verrà eseguito periodicamente in base alla pianificazione. La pianificazione ha gli attributi seguenti:

- **intervallo**: richiesto. Valore di durata che specifica un intervallo o un periodo per l'esecuzione dell'indicizzatore. L'intervallo minimo consentito è di 5 minuti, quello massimo di un giorno. Il valore deve essere formattato come valore XSD "dayTimeDuration" (un subset limitato di un valore [duration ISO 8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)). Il modello è: `P(nD)(T(nH)(nM))`. Esempi: `PT15M` ogni 15 minuti, `PT2H` ogni due ore. 

- **startTime**: richiesto. Valore datetime UTC che specifica quando deve iniziare l'esecuzione dell'indicizzatore.

###<a id="CreateIndexerExample"></a>Esempio di corpo della richiesta

L'esempio seguente crea un indicizzatore che copia i dati dalla raccolta a cui fa riferimento l'origine dati `myDocDbDataSource` all'indice `mySearchIndex` in base a una pianificazione che inizia l'1 gennaio 2015 UTC e viene eseguita ogni ora.

    {
        "name" : "mysearchindexer",
        "dataSourceName" : "mydocdbdatasource",
        "targetIndexName" : "mysearchindex",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" }
    }

###Response

Se l'indicizzatore è stato creato correttamente, si riceve una risposta HTTP 201 - Creato.

##<a id="RunIndexer"></a>Passaggio 4: Eseguire un indicizzatore

Oltre a essere eseguito periodicamente in base a una pianificazione, un indicizzatore può anche essere richiamato su richiesta inviando la richiesta HTTP POST seguente:

    POST https://[Search service name].search.windows.net/indexers/[indexer name]/run?api-version=[api-version]
    api-key: [Search service admin key]

###Response

Se l'indicizzatore è stato richiamato correttamente, si riceve una risposta HTTP 202 - Accettato.

##<a name="GetIndexerStatus"></a>Passaggio 5: Ottenere lo stato dell'indicizzatore

È possibile inviare una richiesta HTTP GET per recuperare lo stato corrente e la cronologia di esecuzione di un indicizzatore:

    GET https://[Search service name].search.windows.net/indexers/[indexer name]/status?api-version=[api-version]
    api-key: [Search service admin key]

###Response

Verrà restituita una risposta HTTP 200 - OK con un corpo della risposta che contiene informazioni sullo stato di integrità globale dell'indicizzatore, l'ultima chiamata all'indicizzatore, nonché la cronologia delle chiamate recenti, se presenti.

La risposta sarà simile alla seguente:

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

##<a name="NextSteps"></a>Passaggi successivi

Congratulazioni. Si è appena appreso come integrare Azure DocumentDB con Ricerca di Azure usando l'indicizzatore per DocumentDB.

 - Per altre informazioni su Azure DocumentDB, fare clic [qui](/services/documentdb/).

 - Per altre informazioni su Ricerca di Azure, fare clic [qui](/services/search/).
 

<!---HONumber=July15_HO4-->