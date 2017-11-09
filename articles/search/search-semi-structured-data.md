---
title: Cercare dati semistrutturati nell'archiviazione cloud di Azure
description: Ricerca di dati BLOB semistrutturati tramite Ricerca di Azure.
author: roygara
manager: timlt
ms.service: search
ms.topic: tutorial
ms.date: 10/12/2017
ms.author: v-rogara
ms.custom: mvc
ms.openlocfilehash: ea57fa35f09299f95cdfd3c11b44657d35972295
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2017
---
# <a name="search-semi-structured-data-in-cloud-storage"></a>Cercare dati semistrutturati nell'archiviazione cloud

In questa serie di esercitazioni in due parti, si scoprirà come eseguire la ricerca di dati semistrutturati e non strutturati tramite la ricerca di Azure. In questa esercitazione viene illustrato come eseguire una ricerca di dati semistrutturati, ad esempio JSON, archiviati in BLOB di Azure. I dati semistrutturati contengono tag o contrassegni che separano il contenuto all'interno dei dati, diversamente dai dati non strutturati che non hanno una struttura formale basata su un modello di dati, come uno schema di database relazionale.

In questa parte vengono illustrate le procedure per le attività seguenti:

> [!div class="checklist"]
> * Creare e popolare un indice all'interno di un servizio di ricerca di Azure
> * Usare il servizio Ricerca di Azure per eseguire una ricerca nell'indice

> [!NOTE]
> "Il supporto di matrici JSON è una funzionalità in anteprima di Ricerca di Azure. Non è attualmente disponibile nel portale. Per questo motivo, si usano l'API REST in anteprima che fornisce questa funzionalità e uno strumento client REST per chiamare l'API."

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:
* Completare l'[esercitazione precedente](../storage/blobs/storage-unstructured-search.md)
    * Questa esercitazione usa l'account di archiviazione e il servizio di ricerca creati nell'esercitazione precedente
* Installare un client REST e raccogliere informazioni su come costruire una richiesta HTTP


## <a name="set-up-the-rest-client"></a>Configurare il client REST

Per completare l'esercitazione, è necessario un client REST. Per gli scopi di questa esercitazione viene usato [Postman](https://www.getpostman.com/). Ritenersi liberi di usare un altro client REST con cui si ha già familiarità.

Dopo aver installato Postman, avviarlo.

Se questa è la prima volta che si eseguono chiamate REST in Azure, di seguito viene fornita una breve introduzione dei componenti importanti per questa esercitazione: il metodo di richiesta per ogni chiamata in questa esercitazione è "POST". Le chiavi di intestazione sono "Content-type" e "api-key". I valori delle chiavi di intestazione sono rispettivamente "application/json" e la chiave di amministrazione, ovvero un segnaposto per la chiave primaria di ricerca. Il corpo è l'area in cui si posiziona il contenuto effettivo della chiamata. A seconda del client in uso, la procedura per la creazione della query potrebbe essere leggermente diversa, ma questi sono i criteri di base.

  ![Ricerca su dati semistrutturati](media/search-semi-structured-data/postmanoverview.png)

Per le chiamate REST illustrate in questa esercitazione, la chiave API per la ricerca è obbligatoria. È possibile trovare la chiave API in **Chiavi** all'interno del servizio di ricerca. Questa chiave API deve essere nell'intestazione di ogni chiamata API (sostituire la chiave di amministrazione nello screenshot precedente con questa chiave) che dovrà essere eseguita seguendo le istruzioni in questa esercitazione. Conservare la chiave perché è necessaria per ogni chiamata.

  ![Ricerca su dati semistrutturati](media/search-semi-structured-data/keys.png)

## <a name="download-the-sample-data"></a>Scaricare i dati di esempio

È disponibile un set di dati di esempio. **Scaricare [clinical-trials-json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip)** e decomprimerlo nella relativa cartella.

L'esempio contiene file JSON di esempio, ovvero file di testo ottenuti originariamente da [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results). Questi file sono stati convertiti in formato JSON per comodità.

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere al [Portale di Azure](http://portal.azure.com).

## <a name="upload-the-sample-data"></a>Caricare i dati di esempio

Nel portale di Azure tornare all'account di archiviazione creato nell'[esercitazione precedente](../storage/blobs/storage-unstructured-search.md). Aprire quindi il contenitore **data** e fare clic su **Carica**.

Fare clic su **Avanzate**, immettere "clinical-trials-json" e quindi caricare tutti i file JSON scaricati.

  ![Ricerca su dati semistrutturati](media/search-semi-structured-data/clinicalupload.png)

Dopo aver completato il caricamento, i file dovrebbero essere visualizzati nella rispettiva sottocartella all'interno del contenitore dei dati.

## <a name="connect-your-search-service-to-your-container"></a>Connettere il servizio di ricerca al contenitore

Viene usato Postman per effettuare tre chiamate API al servizio di ricerca per creare un'origine dati, un indice e un indicizzatore. L'origine dati include un puntatore all'account di archiviazione e ai dati JSON. Il servizio di ricerca stabilisce la connessione durante il caricamento dei dati.

La stringa di query deve contenere **api-version=2016-09-01-Preview** e ogni chiamata deve restituire **201 Creato**. La versione dell'API disponibile a livello generale non ha ancora la capacità di gestire elementi JSON come jsonArray, funzionalità attualmente disponibile solo nella versione di anteprima dell'API.

Eseguire le tre chiamate dell'API seguenti dal client REST.

### <a name="create-a-datasource"></a>Creare un'origine dati

Un'origine dati specifica i dati da indicizzare.

L'endpoint di questa chiamata è `https://[service name].search.windows.net/datasources?api-version=2016-09-01-Preview`. Sostituire `[service name]` con il nome del servizio di ricerca.

Per questa chiamata, sono necessari il nome dell'account di archiviazione e la chiave dell'account di archiviazione. La chiave dell'account di archiviazione è reperibile nel portale di Azure tra le **chiavi di accesso** dell'account di archiviazione. La posizione è indicata nell'immagine seguente:

  ![Ricerca su dati semistrutturati](media/search-semi-structured-data/storagekeys.png)

Assicurarsi di sostituire `[storage account name]` e `[storage account key]` nel corpo della chiamata prima di eseguire la chiamata.

```json
{
    "name" : "clinical-trials-json",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=[storage account name];AccountKey=[storage account key];" },
    "container" : { "name" : "data", "query" : "clinical-trials-json" }
}
```

La risposta dovrebbe essere simile alla seguente:

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#datasources/$entity",
    "@odata.etag": "\"0x8D505FBC3856C9E\"",
    "name": "clinical-trials-json",
    "description": null,
    "type": "azureblob",
    "subtype": null,
    "credentials": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=[mystorageaccounthere];AccountKey=[[myaccountkeyhere]]];"
    },
    "container": {
        "name": "data",
        "query": "clinical-trials-json"
    },
    "dataChangeDetectionPolicy": null,
    "dataDeletionDetectionPolicy": null
}
```

### <a name="create-an-index"></a>Creare un indice
    
La seconda chiamata dell'API crea un indice. Un indice specifica tutti i parametri e i relativi attributi.

L'URL per questa chiamata è `https://[service name].search.windows.net/indexes?api-version=2016-09-01-Preview`. Sostituire `[service name]` con il nome del servizio di ricerca.

Sostituire prima di tutto l'URL, quindi copiare e incollare il codice seguente nel corpo ed eseguire la query.

```json
{
  "name": "clinical-trials-json-index",  
  "fields": [
  {"name": "FileName", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
  {"name": "Description", "type": "Edm.String", "searchable": true, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
  {"name": "MinimumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
  {"name": "Title", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
  {"name": "URL", "type": "Edm.String", "searchable": false, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
  {"name": "MyURL", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
  {"name": "Gender", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "MaximumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
  {"name": "Summary", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
  {"name": "NCTID", "type": "Edm.String", "key": true, "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
  {"name": "Phase", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "Date", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
  {"name": "OverallStatus", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "OrgStudyId", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": false},
  {"name": "HealthyVolunteers", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "Keywords", "type": "Collection(Edm.String)", "searchable": true, "retrievable": true, "facetable": true, "filterable": false, "sortable": false},
  {"name": "metadata_storage_last_modified", "type":"Edm.DateTimeOffset", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
  {"name": "metadata_storage_size", "type":"Edm.String", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
  {"name": "metadata_content_type", "type":"Edm.String", "searchable": true, "retrievable": true, "filterable": true, "sortable": false}
  ],
  "suggesters": [
  {
    "name": "sg",
    "searchMode": "analyzingInfixMatching",
    "sourceFields": ["Title"]
  }
  ]
}
```

La risposta dovrebbe essere simile alla seguente:

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexes/$entity",
    "@odata.etag": "\"0x8D505FC00EDD5FA\"",
    "name": "clinical-trials-json-index",
    "fields": [
        {
            "name": "FileName",
            "type": "Edm.String",
            "searchable": false,
            "filterable": false,
            "retrievable": true,
            "sortable": true,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        {
            "name": "Description",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "retrievable": false,
            "sortable": false,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        ...
          "scoringProfiles": [],
    "defaultScoringProfile": null,
    "corsOptions": null,
    "suggesters": [],
    "analyzers": [],
    "tokenizers": [],
    "tokenFilters": [],
    "charFilters": []
}
```

### <a name="create-an-indexer"></a>Creare un indicizzatore

Un indicizzatore connette l'origine dati all'indice di ricerca di destinazione e consente facoltativamente di pianificare l'automatizzazione dell'aggiornamento dei dati.

L'URL per questa chiamata è `https://[service name].search.windows.net/indexers?api-version=2016-09-01-Preview`. Sostituire `[service name]` con il nome del servizio di ricerca.

Sostituire prima di tutto l'URL, quindi copiare e incollare il codice seguente nel corpo ed eseguire la query.

```json
{
  "name" : "clinical-trials-json-indexer",
  "dataSourceName" : "clinical-trials-json",
  "targetIndexName" : "clinical-trials-json-index",
  "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
}
```

La risposta dovrebbe essere simile alla seguente:

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexers/$entity",
    "@odata.etag": "\"0x8D505FDE143D164\"",
    "name": "clinical-trials-json-indexer",
    "description": null,
    "dataSourceName": "clinical-trials-json",
    "targetIndexName": "clinical-trials-json-index",
    "schedule": null,
    "parameters": {
        "batchSize": null,
        "maxFailedItems": null,
        "maxFailedItemsPerBatch": null,
        "base64EncodeKeys": null,
        "configuration": {
            "parsingMode": "jsonArray"
        }
    },
    "fieldMappings": [],
    "enrichers": [],
    "disabled": null
}
```

## <a name="search-your-json-files"></a>Cercare i file JSON

Ora che il servizio di ricerca è stato connesso al contenitore di dati, è possibile iniziare la ricerca dei file.

Aprire il portale di Azure e tornare al servizio di ricerca, come nell'esercitazione precedente.

  ![Ricerca su dati non strutturati](media/search-semi-structured-data/indexespane.png)

### <a name="user-defined-metadata-search"></a>Ricerca nei metadati definiti dall'utente

Come in precedenza, è possibile eseguire ricerche nei dati in vari modi: ricerca full-text, proprietà di sistema o metadati definiti dall'utente. Le ricerche nelle proprietà di sistema e nei metadati definiti dall'utente possono essere eseguite solo con il parametro `$select` se questi elementi sono stati contrassegnati come **recuperabili** durante la creazione dell'indice di destinazione. I parametri nell'indice non possono essere modificati dopo la creazione. È comunque possibile aggiungere ulteriori parametri.

Un esempio di query semplice è `$select=Gender,metadata_storage_size`, che limita i risultati a questi due parametri.

  ![Ricerca su dati semistrutturati](media/search-semi-structured-data/lastquery.png)

Un esempio di query più complessa può essere `$filter=MinimumAge ge 30 and MaximumAge lt 75`, che restituisce solo i risultati in cui il parametro MinimumAge è maggiore o uguale a 30 e il parametro MaximumAge è minore di 75.

  ![Ricerca su dati semistrutturati](media/search-semi-structured-data/metadatashort.png)

È possibile sperimentare e provare altre query in autonomia, se lo si desidera. Tenere presente che è possibile usare gli operatori logici (and, or e not) e gli operatori di confronto (eq, ne, gt, lt, ge e le). Per i confronti tra stringhe viene fatta distinzione tra maiuscole e minuscole.

Il parametro `$filter` funziona solo con i metadati contrassegnati come filtrabili al momento della creazione dell'indice.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato descritto come eseguire una ricerca di dati semistrutturati tramite Ricerca di Azure, ad esempio come:

> [!div class="checklist"]
> * Creare un servizio di ricerca di Azure con l'API REST
> * Usare il servizio Ricerca di Azure per eseguire una ricerca nel contenitore

Seguire questo collegamento per altre informazioni sulla ricerca.

> [!div class="nextstepaction"]
> [Indicizzazione di documenti in Archiviazione BLOB di Azure](search-howto-indexing-azure-blob-storage.md)