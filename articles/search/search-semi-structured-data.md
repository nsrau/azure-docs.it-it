---
title: 'Esercitazione: Indicizzare dati semistrutturati in BLOB JSON'
titleSuffix: Azure Cognitive Search
description: Informazioni su come indicizzare e cercare BLOB JSON semistrutturati di Azure con le API REST di Ricerca cognitiva di Azure e Postman.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 06/23/2020
ms.openlocfilehash: 0e6759837519feccf6069e805e3fe0f72562fb7b
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/30/2020
ms.locfileid: "85559017"
---
# <a name="tutorial-index-json-blobs-from-azure-storage-using-rest"></a>Esercitazione: Indicizzare i BLOB JSON da Archiviazione di Azure con le API REST

Ricerca cognitiva di Azure consente di indicizzare i documenti e le matrici JSON in archiviazione BLOB di Azure usando un [indicizzatore](search-indexer-overview.md) in grado di leggere dati semistrutturati. I dati semistrutturati contengono tag o contrassegni che separano il contenuto all'interno dei dati, Si differenziano dai dati non strutturati, che devono essere completamente indicizzati, e dai dati strutturati formalmente in base a un modello di dati, ad esempio uno schema di database relazionale, che può essere indicizzato campo per campo.

Questa esercitazione usa Postman e le [API REST per la ricerca](https://docs.microsoft.com/rest/api/searchservice/) per eseguire le attività seguenti:

> [!div class="checklist"]
> * Configurare un'origine dati di Ricerca cognitiva di Azure per un contenitore BLOB di Azure
> * Creare un indice di Ricerca cognitiva di Azure in cui includere contenuto ricercabile
> * Configurare ed eseguire un indicizzatore per leggere il contenitore ed estrarre contenuto ricercabile da archiviazione BLOB di Azure
> * Eseguire una ricerca nell'indice che appena creato

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

+ [Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
+ [App desktop Postman](https://www.getpostman.com/)
+ [Creare](search-create-service-portal.md) o [trovare un servizio di ricerca esistente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> È possibile usare il servizio gratuito per questa esercitazione. Un servizio di ricerca gratuito consente di usare solo tre indici, tre indicizzatori e tre origini dati. Questa esercitazione crea un elemento per ogni tipo. Prima di iniziare, assicurarsi che lo spazio nel servizio sia sufficiente per accettare le nuove risorse.

## <a name="download-files"></a>Scaricare i file

[Clinical-trials-json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip) contiene i dati usati in questa esercitazione. Scaricare e decomprimere il file nella propria cartella. I dati provengono da [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results) e sono stati convertiti in JSON per questa esercitazione.

## <a name="1---create-services"></a>1 - Creare i servizi

Questa esercitazione usa Ricerca cognitiva di Azure per l'indicizzazione e le query e Archiviazione BLOB di Azure per fornire i dati. 

Se possibile, crearli entrambi nella stessa area e nello stesso gruppo di risorse per motivi di prossimità e gestibilità. In pratica, l'account di archiviazione di Azure può trovarsi in qualsiasi area.

### <a name="start-with-azure-storage"></a>Iniziare con Archiviazione di Azure

1. [Accedere al portale di Azure](https://portal.azure.com/) e fare clic su **+ Crea una risorsa**.

1. Cercare *account di archiviazione* e selezionare l'offerta Account di archiviazione di Microsoft.

   ![Creare un account di archiviazione](media/cognitive-search-tutorial-blob/storage-account.png "Creare l'account di archiviazione")

1. Nella scheda Informazioni di base gli elementi seguenti sono obbligatori. Accettare le impostazioni predefinite per tutti gli altri elementi.

   + **Gruppo di risorse**. Selezionarne uno esistente o crearne uno nuovo, ma usare lo stesso gruppo per tutti i servizi in modo che sia possibile gestirli collettivamente.

   + **Nome account di archiviazione**. Se si ritiene che potrebbero esistere più risorse dello stesso tipo, usare il nome per distinguerle in base al tipo e all'area, ad esempio *blobstoragewestus*. 

   + **Località**. Se possibile, scegliere la stessa località usata per Ricerca cognitiva di Azure e Servizi cognitivi. La scelta di un'unica località consente di azzerare i costi correlati alla larghezza di banda.

   + **Tipologia account**. Scegliere l'impostazione predefinita *Archiviazione (utilizzo generico v2)* .

1. Fare clic su **Rivedi e crea** per creare il servizio.

1. Al termine dell'operazione, fare clic su **Vai alla risorsa** per aprire la pagina Panoramica.

1. Fare clic sul servizio **BLOB**.

1. [Creare un contenitore BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) per i dati di esempio. È possibile impostare il livello di accesso pubblico su uno qualsiasi dei relativi valori validi.

1. Dopo aver creato il contenitore, aprirlo e selezionare **Carica** nella barra dei comandi.

   ![Opzione Carica nella barra dei comandi](media/search-semi-structured-data/upload-command-bar.png "Opzione Carica nella barra dei comandi")

1. Passare alla cartella contenente i file di esempio. Selezionare tutti i file e quindi fare clic su **Carica**.

   ![Caricare file](media/search-semi-structured-data/clinicalupload.png "Caricare file")

Dopo aver completato il caricamento, i file dovrebbero essere visualizzati nella rispettiva sottocartella all'interno del contenitore dei dati.

### <a name="azure-cognitive-search"></a>Ricerca cognitiva di Azure

La risorsa successiva è Ricerca cognitiva di Azure, che è possibile [creare nel portale](search-create-service-portal.md). Per completare questa procedura dettagliata, è possibile usare il livello gratuito. 

Come per Archiviazione BLOB di Azure dedicare qualche istante alla raccolta della chiave di accesso. Più avanti, quando si inizierà a strutturare le richieste, sarà necessario specificare l'endpoint e la chiave API di amministrazione usati per autenticare ogni richiesta.

### <a name="get-a-key-and-url"></a>Ottenere una chiave e un URL

Le chiamate REST richiedono l'URL del servizio e una chiave di accesso per ogni richiesta. Con entrambi gli elementi viene creato un servizio di ricerca, quindi se il servizio Ricerca cognitiva di Azure è stato aggiunto alla sottoscrizione, seguire questi passaggi per ottenere le informazioni necessarie:

1. [Accedere al portale di Azure](https://portal.azure.com/) e ottenere l'URL nella pagina **Panoramica** del servizio di ricerca. Un endpoint di esempio potrebbe essere simile a `https://mydemo.search.windows.net`.

1. In **Impostazioni** > **Chiavi** ottenere una chiave amministratore per diritti completi sul servizio. Sono disponibili due chiavi amministratore interscambiabili, fornite per continuità aziendale nel caso in cui sia necessario eseguire il rollover di una di esse. È possibile usare la chiave primaria o secondaria nelle richieste per l'aggiunta, la modifica e l'eliminazione di oggetti.

![Ottenere un endpoint HTTP e una chiave di accesso](media/search-get-started-postman/get-url-key.png "Ottenere un endpoint HTTP e una chiave di accesso")

Per ogni richiesta inviata al servizio è necessario specificare una chiave API. La presenza di una chiave valida stabilisce una relazione di trust, in base alle singole richieste, tra l'applicazione che invia la richiesta e il servizio che la gestisce.

## <a name="2---set-up-postman"></a>2 - Configurare Postman

Avviare Postman e configurare una richiesta HTTP. Se non si ha familiarità con questo strumento, vedere [Esplorare le API REST di Ricerca cognitiva di Azure con Postman](search-get-started-postman.md) per altre informazioni.

I metodi di richiesta usati in questa esercitazione sono **POST** e **GET**. Verranno effettuate tre chiamate API al servizio di ricerca per creare un'origine dati, un indice e un indicizzatore. L'origine dati include un puntatore all'account di archiviazione e ai dati JSON. Il servizio di ricerca stabilisce la connessione durante il caricamento dei dati.

In Headers (Intestazioni) impostare "Content-type" su `application/json` e `api-key` sulla chiave API di amministrazione del servizio Ricerca cognitiva di Azure. Dopo aver impostato le intestazioni, è possibile usarle per ogni richiesta in questo esercizio.

  ![Intestazione e URL della richiesta Postman](media/search-get-started-postman/postman-url.png "Intestazione e URL della richiesta Postman")

Gli URI devono specificare una versione API e ogni chiamata deve restituire un messaggio di creazione riuscita con codice **201**. La versione API disponibile a livello generale per l'uso delle matrici JSON è `2020-06-30`.

## <a name="3---create-a-data-source"></a>3 - Creare un'origine dati

L'[API di creazione dell'origine dati](https://docs.microsoft.com/rest/api/searchservice/create-data-source) crea un oggetto Ricerca cognitiva di Azure che specifica quali dati indicizzare.

1. Impostare l'endpoint di questa chiamata su `https://[service name].search.windows.net/datasources?api-version=2020-06-30`. Sostituire `[service name]` con il nome del servizio di ricerca. 

1. Copiare il codice JSON seguente nel corpo della richiesta.

    ```json
    {
        "name" : "clinical-trials-json-ds",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=[storage account name];AccountKey=[storage account key];" },
        "container" : { "name" : "[blob container name]"}
    }
    ```

1. Sostituire la stringa di connessione con una stringa valida per l'account in uso.

1. Sostituire "[blob container name]" con il contenitore creato per i dati di esempio. 

1. Inviare la richiesta. La risposta dovrebbe essere simile alla seguente:

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/$metadata#datasources/$entity",
        "@odata.etag": "\"0x8D505FBC3856C9E\"",
        "name": "clinical-trials-json-ds",
        "description": null,
        "type": "azureblob",
        "subtype": null,
        "credentials": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=[mystorageaccounthere];AccountKey=[[myaccountkeyhere]]];"
        },
        "container": {
            "name": "[mycontainernamehere]",
            "query": null
        },
        "dataChangeDetectionPolicy": null,
        "dataDeletionDetectionPolicy": null
    }
    ```

## <a name="4---create-an-index"></a>4 - Creare un indice
    
La seconda chiamata è l'[API di creazione dell'indice](https://docs.microsoft.com/rest/api/searchservice/create-index) che crea un indice di Ricerca cognitiva di Azure che archivia tutti i dati ricercabili. Un indice specifica tutti i parametri e i relativi attributi.

1. Impostare l'endpoint di questa chiamata su `https://[service name].search.windows.net/indexes?api-version=2020-06-30`. Sostituire `[service name]` con il nome del servizio di ricerca.

1. Copiare il codice JSON seguente nel corpo della richiesta.

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
      ]
    }
   ```

1. Inviare la richiesta. La risposta dovrebbe essere simile alla seguente:

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
          }
    ```

## <a name="5---create-and-run-an-indexer"></a>5 - Creare ed eseguire un indicizzatore

Un indicizzatore si connette all'origine dati, importa i dati nell'indice di ricerca di destinazione e facoltativamente fornisce una pianificazione per automatizzare l'aggiornamento dei dati. L'API REST è [Creare un indicizzatore](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

1. Impostare l'URI per questa chiamata su `https://[service name].search.windows.net/indexers?api-version=2020-06-30`. Sostituire `[service name]` con il nome del servizio di ricerca.

1. Copiare il codice JSON seguente nel corpo della richiesta.

    ```json
    {
      "name" : "clinical-trials-json-indexer",
      "dataSourceName" : "clinical-trials-json-ds",
      "targetIndexName" : "clinical-trials-json-index",
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }
    ```

1. Inviare la richiesta. La richiesta viene elaborata immediatamente. Quando viene restituita la risposta, si avrà un indice da usare per la ricerca full-text. La risposta dovrebbe essere simile alla seguente:

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexers/$entity",
        "@odata.etag": "\"0x8D505FDE143D164\"",
        "name": "clinical-trials-json-indexer",
        "description": null,
        "dataSourceName": "clinical-trials-json-ds",
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

## <a name="6---search-your-json-files"></a>6 - Cercare i file JSON

È possibile iniziare a eseguire ricerche subito dopo aver caricato il primo documento.

1. Cambiare il verbo in **GET**.

1. Impostare l'URI per questa chiamata su `https://[service name].search.windows.net/indexes/clinical-trials-json-index/docs?search=*&api-version=2020-06-30&$count=true`. Sostituire `[service name]` con il nome del servizio di ricerca.

1. Inviare la richiesta. Si tratta di una query di ricerca full-text non specificata che restituisce tutti i campi contrassegnati come recuperabili nell'indice, insieme al numero di documenti. La risposta dovrebbe essere simile alla seguente:

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/indexes('clinical-trials-json-index')/$metadata#docs(*)",
        "@odata.count": 100,
        "value": [
            {
                "@search.score": 1.0,
                "FileName": "NCT00000102.txt",
                "MinimumAge": 14,
                "Title": "Congenital Adrenal Hyperplasia: Calcium Channels as Therapeutic Targets",
                "MyURL": "https://azure.storagedemos.com/clinical-trials/NCT00000102.txt",
                "Gender": "Both",
                "MaximumAge": 35,
                "Summary": "This study will test the ability of extended release nifedipine (Procardia XL), a blood pressure medication, to permit a decrease in the dose of glucocorticoid medication children take to treat congenital adrenal hyperplasia (CAH).",
                "NCTID": "NCT00000102",
                "Phase": "Phase 1/Phase 2",
                "Date": "ClinicalTrials.gov processed this data on October 25, 2016",
                "OverallStatus": "Completed",
                "OrgStudyId": "NCRR-M01RR01070-0506",
                "HealthyVolunteers": "No",
                "Keywords": [],
                "metadata_storage_last_modified": "2019-04-09T18:16:24Z",
                "metadata_storage_size": "33060",
                "metadata_content_type": null
            },
            . . . 
    ```

1. Aggiungere il parametro di query `$select` per limitare i risultati a un minor numero di campi: `https://[service name].search.windows.net/indexes/clinical-trials-json-index/docs?search=*&$select=Gender,metadata_storage_size&api-version=2020-06-30&$count=true`.  Per questa query, anche se viene trovata la corrispondenza per 100 documenti, per impostazione predefinita Ricerca cognitiva di Azure ne restituisce solo 50 nei risultati.

   ![Query con parametri](media/search-semi-structured-data/lastquery.png "Query con parametri")

1. Un esempio di query più complessa può essere `$filter=MinimumAge ge 30 and MaximumAge lt 75`, che restituisce solo i risultati in cui il parametro MinimumAge è maggiore o uguale a 30 e il parametro MaximumAge è minore di 75. Sostituire l'espressione `$select` con l'espressione `$filter`.

   ![Ricerca su dati semistrutturati](media/search-semi-structured-data/metadatashort.png)

È anche possibile usare gli operatori logici (and, or e not) e gli operatori di confronto (eq, ne, gt, lt, ge e le). Per i confronti tra stringhe viene fatta distinzione tra maiuscole e minuscole. Per altre informazioni ed esempi, vedere [Creare una query semplice](search-query-simple-examples.md).

> [!NOTE]
> Il parametro `$filter` funziona solo con i metadati contrassegnati come filtrabili al momento della creazione dell'indice.

## <a name="reset-and-rerun"></a>Reimpostare ed eseguire di nuovo

Nelle prime fasi sperimentali di sviluppo l'approccio più pratico per le iterazioni di progettazione consiste nell'eliminare gli oggetti da Ricerca cognitiva di Azure e consentire al codice di ricompilarli. I nomi di risorsa sono univoci. L'eliminazione di un oggetto consente di ricrearlo usando lo stesso nome.

È possibile usare il portale per eliminare indici, indicizzatori e origini dati. In alternativa, usare il comando **DELETE** e specificare gli URL per ogni oggetto. Il comando seguente elimina un indicizzatore.

```http
DELETE https://[YOUR-SERVICE-NAME].search.windows.net/indexers/clinical-trials-json-indexer?api-version=2020-06-30
```

In caso di corretto completamento dell'eliminazione viene restituito il codice di stato 204.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando si lavora nella propria sottoscrizione, alla fine di un progetto è opportuno rimuovere le risorse che non sono più necessarie. L'esecuzione continua delle risorse può avere un costo. È possibile eliminare le singole risorse oppure il gruppo di risorse per eliminare l'intero set di risorse.

Per trovare e gestire le risorse nel portale, usare il collegamento Tutte le risorse o Gruppi di risorse nel riquadro di spostamento a sinistra.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver acquisito familiarità con i concetti di base dell'indicizzazione BLOB di Azure, si esaminerà in dettaglio la configurazione dell'indicizzatore per i BLOB JSON in Archiviazione di Azure.

> [!div class="nextstepaction"]
> [Configurare l'indicizzazione dei BLOB JSON](search-howto-index-json-blobs.md)