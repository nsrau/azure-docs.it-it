---
title: Eseguire ricerche nel contenuto di archiviazione BLOB di Azure
titleSuffix: Azure Cognitive Search
description: Informazioni su come indicizzare i documenti nell'archivio BLOB di Azure ed estrarre il testo dai documenti con ricerca cognitiva di Azure.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/11/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 2ba511d3747ba308ae04ab1bbe3dcb89bca6a8a8
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91328293"
---
# <a name="how-to-index-documents-in-azure-blob-storage-with-azure-cognitive-search"></a>Come indicizzare i documenti nell'archivio BLOB di Azure con Azure ricerca cognitiva

Questo articolo illustra come usare ricerca cognitiva di Azure per indicizzare i documenti, ad esempio i file PDF, i documenti Microsoft Office e diversi altri formati comuni, archiviati nell'archivio BLOB di Azure. In primo luogo, vengono illustrate le nozioni di base per l'impostazione e la configurazione di un indicizzatore BLOB. Vengono inoltre descritti in modo più dettagliato i comportamenti e gli scenari che possono verificarsi.

<a name="SupportedFormats"></a>

## <a name="supported-document-formats"></a>Formati di documento supportati
L'indicizzatore BLOB può estrarre il testo dai formati di documento seguenti:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="setting-up-blob-indexing"></a>Configurazione dell'indicizzazione BLOB
È possibile impostare un indicizzatore dell'Archiviazione BLOB di Azure usando:

* [Azure portal](https://ms.portal.azure.com)
* [API REST](/rest/api/searchservice/Indexer-operations) di Azure ricerca cognitiva
* Azure ricerca cognitiva [.NET SDK](/dotnet/api/overview/azure/search)

> [!NOTE]
> Alcune funzionalità (ad esempio, i mapping dei campi) non sono ancora disponibili nel portale e devono essere usate in modo programmatico.
>

In questo caso viene illustrato il flusso tramite l'API REST.

### <a name="step-1-create-a-data-source"></a>Passaggio 1: Creare un'origine dati
Un'origine dati specifica i dati da indicizzare, le credenziali necessarie per accedere ai dati e i criteri che consentono di identificare in modo efficace le modifiche apportate ai dati (righe nuove, modificate o eliminate). Un'origine dati può essere usata da più indicizzatori nello stesso servizio di ricerca.

Per l'indicizzazione BLOB, l'origine dati deve avere le proprietà obbligatorie seguenti:

* **name** è il nome univoco dell'origine dati all'interno del servizio di ricerca.
* **type** deve essere `azureblob`.
* **credentials** fornisce la stringa di connessione dell'account di archiviazione come parametro `credentials.connectionString`. Per informazioni dettagliate, vedere [Come specificare le credenziali](#Credentials) più avanti.
* **container** specifica un contenitore nell'account di archiviazione. Per impostazione predefinita, tutti i BLOB all'interno del contenitore sono recuperabili. Per indicizzare soltanto i BLOB in una determinata directory virtuale, è possibile specificare la directory usando il parametro facoltativo **query**.

Per creare un'origine dati:

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
    }   
```

Per altre informazioni sull'API di creazione dell'origine dati, vedere [Creare un'origine dati](/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>
#### <a name="how-to-specify-credentials"></a>Come specificare le credenziali ####

Per specificare le credenziali per il contenitore BLOB, sono disponibili questi modi:

- **Stringa di connessione identità gestita**: `ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.Storage/storageAccounts/<your storage account name>/;` questa stringa di connessione non richiede una chiave dell'account, ma è necessario seguire le istruzioni per la [configurazione di una connessione a un account di archiviazione di Azure usando un'identità gestita](search-howto-managed-identities-storage.md).
- **Stringa di connessione dell'account di archiviazione con accesso completo**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` è possibile ottenere la stringa di connessione dal portale di Azure passando al pannello dell'account di archiviazione > impostazioni > chiavi (per gli account di archiviazione classici) o impostazioni > chiavi di accesso (per Azure Resource Manager account di archiviazione).
- **Stringa di connessione della firma di accesso condiviso (SAS) dell'account di archiviazione**: `BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl` la SAS deve avere le autorizzazioni per le operazioni di elenco e lettura per i contenitori e gli oggetti (oggetti binario di grandi dimensioni).
-  **Firma di accesso condiviso del contenitore: la firma di accesso condiviso** `ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl` deve avere le autorizzazioni di elenco e lettura per il contenitore.

Per altre informazioni sulle firme di accesso condiviso per l'archiviazione, vedere [Uso delle firme di accesso condiviso](../storage/common/storage-sas-overview.md).

> [!NOTE]
> Se si usano le credenziali di firma di accesso condiviso, sarà necessario aggiornare periodicamente le credenziali dell'origine dati con firme rinnovate per impedire che scadano. Se le credenziali di firma di accesso condiviso scadono, l'indicizzatore avrà esito negativo e restituirà un messaggio di errore simile al seguente: `Credentials provided in the connection string are invalid or have expired.`.  

### <a name="step-2-create-an-index"></a>Passaggio 2: Creare un indice
L'indice consente di specificare i campi in un documento, gli attributi e altri costrutti che danno forma all'esperienza della ricerca.

Di seguito viene illustrato come creare un indice con un campo `content` ricercabile per archiviare il testo estratto dagli oggetti BLOB:   

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }
```

Per altre informazioni sulla creazione di indici, vedere [Creare un indice](/rest/api/searchservice/create-index)

### <a name="step-3-create-an-indexer"></a>Passaggio 3: Creare un indicizzatore
Un indicizzatore si connette a un'origine dati con un indice di ricerca di destinazione e consente di pianificare l'automatizzazione dell'aggiornamento dei dati.

Dopo aver creato l'indice e l'origine dati, è possibile creare l'indicizzatore:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

Questo indicizzatore verrà eseguito ogni due ore (l'intervallo di pianificazione è impostato su "PT2H"). Per eseguire un indicizzatore ogni 30 minuti, impostare l'intervallo su "PT30M". L'intervallo minimo supportato è di 5 minuti. La pianificazione è facoltativa: se omessa, l'indicizzatore viene eseguito una sola volta al momento della creazione. Tuttavia, è possibile eseguire un indicizzatore su richiesta in qualsiasi momento.   

Per altre informazioni sull'API di creazione di un indicizzatore, vedere [Creare un indicizzatore](/rest/api/searchservice/create-indexer).

Per altre informazioni sulla definizione delle pianificazioni degli indicizzatori, vedere [Come pianificare gli indicizzatori per Ricerca cognitiva di Azure](search-howto-schedule-indexers.md).

<a name="how-azure-search-indexes-blobs"></a>

## <a name="how-azure-cognitive-search-indexes-blobs"></a>Modalità di indicizzazione di BLOB in Azure ricerca cognitiva

A seconda della relativa [configurazione](#PartsOfBlobToIndex), l'indicizzatore BLOB può indicizzare solo i metadati di archiviazione, opzione utile quando si è interessati solo ai metadati e non è necessario indicizzare il contenuto dei BLOB, indicizzare i metadati del contenuto e di archiviazione o indicizzare sia i metadati che il contenuto di testo. Per impostazione predefinita, l'indicizzatore estrae sia i metadati che il contenuto.

> [!NOTE]
> Per impostazione predefinita, i BLOB con contenuto strutturato, come quelli in formato JSON o CSV, vengono indicizzati come un unico blocco di testo. Se si vuole indicizzare i BLOB JSON e CSV in modo strutturato, vedere indicizzazione di [BLOB JSON](search-howto-index-json-blobs.md) e [indicizzazione di BLOB CSV](search-howto-index-csv-blobs.md) per altre informazioni.
>
> Documento composto o incorporato, ad esempio un archivio ZIP, un documento di Word con messaggi di posta elettronica di Outlook incorporati che contengono allegati o. Il file MSG con allegati viene inoltre indicizzato come un singolo documento. Ad esempio, tutte le immagini estratte dagli allegati di un oggetto. Il file MSG verrà restituito nel campo normalized_images.

* Il contenuto di testo del documento viene estratto in un campo di tipo stringa denominato `content`.

> [!NOTE]
> Azure ricerca cognitiva limita la quantità di testo da estrarre a seconda del piano tariffario: 32.000 caratteri per il livello gratuito, 64.000 per Basic, 4 milioni per standard, 8 milioni per standard S2 e 16 milioni per S3 standard. Un avviso è incluso nella risposta dello stato dell'indicizzatore per i documenti troncati.  

* Le proprietà dei metadati specificate dall'utente eventualmente presenti nel BLOB vengono estratte letteralmente. Si noti che è necessario definire un campo nell'indice con lo stesso nome della chiave di metadati del BLOB. Ad esempio, se il BLOB contiene una chiave di metadati `Sensitivity` con valore `High` , è necessario definire un campo denominato `Sensitivity` nell'indice di ricerca che verrà popolato con il valore `High` .
* Le proprietà dei metadati BLOB standard vengono estratte nei campi seguenti:

  * **metadata\_storage\_name** (Edm.String): nome file del BLOB. Se, ad esempio, è presente un BLOB /my-container/my-folder/subfolder/resume.pdf, il valore di questo campo è `resume.pdf`.
  * **metadata\_storage\_path** (Edm.String): URI completo del BLOB, incluso l'account di archiviazione. Ad esempio, usare `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`
  * **metadata\_storage\_content\_type** (Edm.String): tipo di contenuto specificato dal codice usato per caricare il BLOB. Ad esempio: `application/octet-stream`.
  * **metadata\_storage\_last\_modified** (Edm.DateTimeOffset): ultimo timestamp modificato per il BLOB. Azure ricerca cognitiva usa questo timestamp per identificare i BLOB modificati, in modo da evitare di reindicizzare tutto dopo l'indicizzazione iniziale.
  * **metadata\_storage\_size** (Edm.Int64): dimensioni del BLOB in byte.
  * **metadata\_storage\_content\_md5** (Edm.String): hash MD5 dei contenuti del BLOB, se disponibile.
  * ** \_ \_ \_ token SAS di archiviazione dei metadati** (EDM. String): token di firma di accesso condiviso temporaneo che può essere usato dalle [competenze personalizzate](cognitive-search-custom-skill-interface.md) per ottenere l'accesso al BLOB. Questo token non deve essere archiviato per un uso successivo, perché potrebbe scadere.

* Le proprietà dei metadati specifiche di ogni formato di documento vengono estratte nei campi elencati [qui](#ContentSpecificMetadata).

Non è necessario definire i campi per tutte le proprietà precedenti nell'indice di ricerca, ma solo acquisire le proprietà necessarie per l'applicazione.

> [!NOTE]
> I nomi dei campi nell'indice esistente saranno spesso diversi da quelli generati durante l'estrazione della documentazione. È possibile usare i **mapping dei campi** per eseguire il mapping dei nomi di proprietà forniti da Azure ricerca cognitiva ai nomi dei campi nell'indice di ricerca. Di seguito verrà visualizzato un esempio di mapping dei campi.
>
>

<a name="DocumentKeys"></a>
### <a name="defining-document-keys-and-field-mappings"></a>Definizione di chiavi di documento e dei mapping dei campi
In Azure ricerca cognitiva la chiave del documento identifica un documento in modo univoco. Ogni indice di ricerca deve avere esclusivamente un campo chiave di tipo Edm.String. Il campo chiave è necessario per ogni documento da aggiungere all'indice (è di fatto l'unico campo obbligatorio).  

È necessario valutare attentamente di quale campo estratto eseguire il mapping al campo chiave per l'indice. I candidati sono:

* **metadata\_storage\_name**: può essere un candidato valido, tuttavia è bene notare che 1) è possibile che i nomi non siano univoci, perché potrebbero esserci BLOB con lo stesso nome in cartelle diverse e 2) è possibile che il nome contenga caratteri non validi nelle chiavi dei documenti, ad esempio trattini. È possibile gestire i caratteri non validi usando la  [funzione di mapping dei campi](search-indexer-field-mappings.md#base64EncodeFunction)`base64Encode`. In questo caso, è necessario ricordarsi di codificare le chiavi dei documenti quando si passano nelle chiamate API, ad esempio in una ricerca. In .NET, ad esempio, è possibile usare il metodo [UrlTokenEncode method](/dotnet/api/system.web.httpserverutility.urltokenencode?view=netframework-4.8) a tale scopo.
* **metadata\_storage\_path**: l'uso del percorso completo garantisce l'univocità, ma il percorso contiene sicuramente caratteri `/` che [non sono validi nella chiave di un documento](/rest/api/searchservice/naming-rules).  Come prima, è possibile codificare le chiavi usando la  [funzione](search-indexer-field-mappings.md#base64EncodeFunction)`base64Encode`.
* Se nessuna delle opzioni elencate è appropriata, è possibile aggiungere una proprietà di metadati personalizzati ai BLOB. Questa opzione, tuttavia, richiede che il processo di caricamento del BLOB aggiunga la proprietà dei metadati a tutti i BLOB. Poiché la chiave è una proprietà obbligatoria, tutti i BLOB privi di tale proprietà non potranno essere indicizzati.

> [!IMPORTANT]
> Se non è presente alcun mapping esplicito per il campo chiave nell'indice, Azure ricerca cognitiva usa automaticamente `metadata_storage_path` come chiave e con base 64 codifica i valori di chiave (la seconda opzione precedente).
>
>

Per questo esempio, si seleziona il campo `metadata_storage_name` come chiave del documento. Si supponga anche che l'indice includa un campo chiave denominato `key` e un campo `fileSize` in cui archiviare le dimensioni del documento. Per collegare gli elementi come si vuole, specificare i mapping di campo seguenti quando si crea o si aggiorna l'indicizzatore:

```http
    "fieldMappings" : [
      { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
      { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
    ]
```

Per unire il tutto, ecco come è possibile aggiungere i mapping di campo e abilitare la codifica in base 64 delle chiavi per un indicizzatore esistente:

```http
    PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "dataSourceName" : " blob-datasource ",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "fieldMappings" : [
        { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
        { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
      ]
    }
```

> [!NOTE]
> Per altre informazioni sui mapping dei campi, leggere [questo articolo](search-indexer-field-mappings.md).
>
>

#### <a name="what-if-you-need-to-encode-a-field-to-use-it-as-a-key-but-you-also-want-to-search-it"></a>Cosa accade se è necessario codificare un campo per usarlo come chiave, ma si vuole anche eseguirne la ricerca?

In alcuni casi è necessario usare una versione codificata di un campo come metadata_storage_path come chiave, ma è anche necessario che il campo sia disponibile per la ricerca (senza codifica). Per risolvere questo problema, è possibile eseguirne il mapping in due campi; una che verrà usata per la chiave e un'altra che verrà usata per scopi di ricerca. Nell'esempio seguente il campo *chiave* contiene il percorso codificato, mentre il campo *percorso* non è codificato e verrà usato come campo ricercabile nell'indice.

```http
    PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "dataSourceName" : " blob-datasource ",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "fieldMappings" : [
        { "sourceFieldName" : "metadata_storage_path", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
        { "sourceFieldName" : "metadata_storage_path", "targetFieldName" : "path" }
      ]
    }
```
<a name="WhichBlobsAreIndexed"></a>
## <a name="controlling-which-blobs-are-indexed"></a>Controllo dei BLOB da indicizzare
È possibile controllare quali BLOB vengono indicizzati e quali vengono ignorati.

### <a name="index-only-the-blobs-with-specific-file-extensions"></a>Indicizzare solo i BLOB con estensioni di file specifiche
È possibile indicizzare solo i BLOB con le estensioni di file specificate tramite il parametro di configurazione dell'indicizzatore `indexedFileNameExtensions`. Il valore è una stringa contenente un elenco delimitato da virgole di estensioni di file (precedute da un punto). Ad esempio, per indicizzare solo i BLOB .PDF e .DOCX eseguire questa operazione:

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
    }
```

### <a name="exclude-blobs-with-specific-file-extensions"></a>Escludere BLOB con estensioni di file specifiche
È possibile escludere dall'indicizzazione i BLOB con estensioni di file specifiche usando il parametro di configurazione `excludedFileNameExtensions`. Il valore è una stringa contenente un elenco delimitato da virgole di estensioni di file (precedute da un punto). Ad esempio, per indicizzare tutti i BLOB ad eccezione di quelli con le estensioni .PNG e .JPEG eseguire questa operazione:

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
    }
```

Se `indexedFileNameExtensions` `excludedFileNameExtensions` sono presenti entrambi i parametri e, Azure ricerca cognitiva esamina prima di tutto `indexedFileNameExtensions` , quindi all'indirizzo `excludedFileNameExtensions` . Ciò significa che se la stessa estensione di file è presente in entrambi gli elenchi, verrà esclusa dall'indicizzazione.

<a name="PartsOfBlobToIndex"></a>
## <a name="controlling-which-parts-of-the-blob-are-indexed"></a>Controllo delle parti di BLOB da indicizzare

Il parametro di configurazione `dataToExtract` permette di controllare quali parti dei BLOB vengono indicizzate. I valori possibili sono i seguenti:

* `storageMetadata`: specifica che vengono indicizzati solo [i metadati specificati dall'utente e le proprietà BLOB standard](../storage/blobs/storage-blob-container-properties-metadata.md).
* `allMetadata`: specifica che vengono indicizzati i metadati di archiviazione e i [metadati specifici del tipo di contenuto](#ContentSpecificMetadata) estratti dal contenuto BLOB.
* `contentAndMetadata`: specifica che vengono indicizzati tutti i metadati e il contenuto di testo estratti dal BLOB. Si tratta del valore predefinito.

Ad esempio, per indicizzare solo i metadati di archiviazione, usare:

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
    }
```

### <a name="using-blob-metadata-to-control-how-blobs-are-indexed"></a>Usare i metadati dei BLOB per controllare il modo in cui vengono indicizzati i BLOB

I parametri di configurazione descritti in precedenza si applicano a tutti i BLOB. In alcuni casi è consigliabile controllare il modo in cui vengono indicizzati i *singoli BLOB*. A tale scopo è possibile aggiungere i valori e le proprietà seguenti dei metadati del BLOB:

| Nome della proprietà | Valore proprietà | Spiegazione |
| --- | --- | --- |
| AzureSearch_Skip |"true" |Indica all'indicizzatore BLOB di ignorare completamente il BLOB. Non verrà tentata l'estrazione dei metadati né del contenuto. È utile quando un determinato BLOB ha ripetutamente esito negativo e interrompe il processo di indicizzazione. |
| AzureSearch_SkipContent |"true" |Equivale all'impostazione `"dataToExtract" : "allMetadata"` descritta [in precedenza](#PartsOfBlobToIndex) nell'ambito di un BLOB specifico. |

<a name="DealingWithErrors"></a>
## <a name="dealing-with-errors"></a>Gestione degli errori

Per impostazione predefinita, l'indicizzatore BLOB viene arrestato non appena viene rilevato un BLOB con un tipo di contenuto non supportato, ad esempio un'immagine. Naturalmente, è possibile usare il parametro `excludedFileNameExtensions` per ignorare determinati tipi di contenuto. Potrebbe tuttavia essere necessario indicizzare BLOB senza conoscere in anticipo tutti i tipi di contenuto possibili. Per continuare l'indicizzazione quando viene rilevato un tipo di contenuto non supportato, impostare il parametro di configurazione `failOnUnsupportedContentType` su `false`:

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
    }
```

Per alcuni BLOB, Azure ricerca cognitiva non è in grado di determinare il tipo di contenuto o non è in grado di elaborare un documento di tipo di contenuto altrimenti supportato. Per ignorare la modalità di errore, impostare il parametro di configurazione `failOnUnprocessableDocument` su False:

```http
      "parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }
```

Azure ricerca cognitiva limita le dimensioni dei BLOB indicizzati. Questi limiti sono documentati in [limiti di servizio in Azure ricerca cognitiva](./search-limits-quotas-capacity.md). I BLOB sovradimensionati vengono gestiti come errori per impostazione predefinita. È comunque possibile indicizzare i metadati di archiviazione dei BLOB sovradimensionati se si imposta il parametro di configurazione `indexStorageMetadataOnlyForOversizedDocuments` su true: 

```http
    "parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }
```

È anche possibile continuare l'indicizzazione se si verificano errori in qualsiasi momento dell'elaborazione, durante l'analisi dei BLOB o durante l'aggiunta di documenti a un indice. Per ignorare un determinato numero di errori, impostare i parametri di configurazione `maxFailedItems` e `maxFailedItemsPerBatch` sui valori desiderati. Ad esempio:

```http
    {
      ... other parts of indexer definition
      "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
    }
```

## <a name="incremental-indexing-and-deletion-detection"></a>Indicizzazione incrementale e rilevamento delle eliminazioni

Quando si configura un indicizzatore BLOB per l'esecuzione in base a una pianificazione, vengono reindicizzati solo i BLOB modificati, come determinato dal timestamp `LastModified` del BLOB.

> [!NOTE]
> Non è necessario specificare un criterio di rilevamento delle modifiche perché l'indicizzazione incrementale viene abilitata automaticamente.

Per supportare l'eliminazione di documenti, usare un approccio di "eliminazione temporanea". Se si eliminano completamente i BLOB, i documenti corrispondenti non verranno rimossi dall'indice della ricerca.

Esistono due modi per implementare l'approccio di eliminazione temporanea. Entrambi sono descritti di seguito.

### <a name="native-blob-soft-delete-preview"></a>Eliminazione temporanea BLOB nativa (anteprima)

> [!IMPORTANT]
> Il supporto per l'eliminazione temporanea dei BLOB nativi è in anteprima. La funzionalità di anteprima viene fornita senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Questa funzionalità è disponibile nell' [API REST versione 2020-06-30-Preview](./search-api-preview.md) . Attualmente non è disponibile alcun portale o supporto per .NET SDK.

> [!NOTE]
> Quando si usa il criterio di eliminazione temporanea BLOB nativo, le chiavi del documento per i documenti nell'indice devono essere una proprietà BLOB o i metadati del BLOB.

In questo metodo si userà la funzionalità di [eliminazione temporanea BLOB nativa](../storage/blobs/soft-delete-blob-overview.md) offerta dall'archiviazione BLOB di Azure. Se l'eliminazione temporanea del BLOB nativo è abilitata nell'account di archiviazione, l'origine dati include un set di criteri nativi di eliminazione temporanea e l'indicizzatore trova un BLOB che è stato passato a uno stato di eliminazione temporanea, l'indicizzatore rimuoverà tale documento dall'indice. I criteri di eliminazione temporanea BLOB nativi non sono supportati durante l'indicizzazione di BLOB da Azure Data Lake Storage Gen2.

Eseguire i passaggi seguenti:
1. Abilitare l' [eliminazione temporanea nativa per l'archiviazione BLOB di Azure](../storage/blobs/soft-delete-blob-overview.md). È consigliabile impostare i criteri di conservazione su un valore molto superiore rispetto alla pianificazione dell'intervallo dell'indicizzatore. In questo modo, se si verifica un problema durante l'esecuzione dell'indicizzatore o se si dispone di un numero elevato di documenti da indicizzare, l'indicizzatore potrebbe elaborare i BLOB eliminati temporaneamente. Gli indicizzatori di Azure ricerca cognitiva elimineranno un documento dall'indice solo se elabora il BLOB mentre si trova in uno stato di eliminazione temporanea.
1. Configurare i criteri di rilevamento dell'eliminazione temporanea dei BLOB nativi nell'origine dati. Di seguito è illustrato un esempio. Poiché questa funzionalità è in anteprima, è necessario usare l'API REST di anteprima.
1. Eseguire l'indicizzatore o impostare l'indicizzatore per l'esecuzione in base a una pianificazione. Quando l'indicizzatore viene eseguito ed elabora il BLOB, il documento verrà rimosso dall'indice.

    ```
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.NativeBlobSoftDeleteDeletionDetectionPolicy"
        }
    }
    ```

#### <a name="reindexing-undeleted-blobs"></a>Reindicizzazione di BLOB non eliminati

Se si elimina un BLOB dall'archiviazione BLOB di Azure con l'eliminazione temporanea nativa abilitata nell'account di archiviazione, il BLOB passerà a uno stato di eliminazione temporanea che consente di annullare l'eliminazione del BLOB entro il periodo di memorizzazione. Quando un'origine dati di Azure ricerca cognitiva dispone di un criterio di eliminazione temporanea BLOB nativo e l'indicizzatore elabora un BLOB eliminato temporaneamente, questo documento verrà rimosso dall'indice. Se il BLOB viene annullato in un secondo momento, l'indicizzatore non Reindicizza sempre tale BLOB. Questo perché l'indicizzatore determina i BLOB da indicizzare in base al timestamp del BLOB `LastModified` . Quando viene annullato l'eliminazione di un BLOB eliminato temporaneamente, il timestamp non viene `LastModified` aggiornato, quindi se l'indicizzatore ha già elaborato BLOB con `LastModified` timestamp più recenti rispetto al BLOB non eliminato, il BLOB non eliminato non verrà reindicizzato. Per assicurarsi che un BLOB non eliminato venga reindicizzato, sarà necessario aggiornare il timestamp del BLOB `LastModified` . Un modo per eseguire questa operazione consiste nel risalvare i metadati del BLOB. Non è necessario modificare i metadati, ma il Risalvataggio dei metadati aggiornerà il timestamp del BLOB in `LastModified` modo che l'indicizzatore sappia che è necessario reindicizzare questo BLOB.

### <a name="soft-delete-using-custom-metadata"></a>Eliminazione temporanea mediante metadati personalizzati

In questo metodo verranno utilizzati i metadati di un BLOB per indicare quando è necessario rimuovere un documento dall'indice di ricerca.

Eseguire i passaggi seguenti:

1. Aggiungere una coppia chiave-valore dei metadati personalizzata al BLOB per indicare ad Azure ricerca cognitiva che è stata eliminata logicamente.
1. Configurare un criterio di rilevamento della colonna di eliminazione temporanea nell'origine dati. Di seguito è illustrato un esempio.
1. Dopo che l'indicizzatore ha elaborato il BLOB ed eliminato il documento dall'indice, è possibile eliminare il BLOB per l'archiviazione BLOB di Azure.

Il criterio illustrato sotto, ad esempio, considera l'eliminazione di un BLOB se ha una proprietà di metadati `IsDeleted` con il valore `true`:

```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",     
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }
```

#### <a name="reindexing-undeleted-blobs"></a>Reindicizzazione di BLOB non eliminati

Se si imposta un criterio di rilevamento della colonna di eliminazione temporanea nell'origine dati, quindi si aggiungono i metadati personalizzati a un BLOB con il valore del marcatore, quindi si esegue l'indicizzatore, l'indicizzatore rimuoverà il documento dall'indice. Se si vuole reindicizzare il documento, è sufficiente modificare il valore dei metadati dell'eliminazione temporanea per il BLOB ed eseguire di nuovo l'indicizzatore.

## <a name="indexing-large-datasets"></a>Indicizzazione di set di dati di grandi dimensioni

L'indicizzazione di BLOB può richiedere molto tempo. Quando si hanno milioni di BLOB da indicizzare, è possibile velocizzare l'operazione partizionando i dati e usando più indicizzatori per elaborare i dati in parallelo. A tale scopo, è possibile procedere come segue:

- Partizionare i dati in più contenitori BLOB o cartelle virtuali.
- Configurare diverse origini dati di Azure ricerca cognitiva, una per ogni contenitore o cartella. Per puntare a una cartella BLOB, usare il parametro `query`:

    ```
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

- Creare un indicizzatore corrispondente per ogni origine dati. Tutti gli indicizzatori possono puntare allo stesso indice di ricerca di destinazione.  

- Un'unità di ricerca del servizio permette di eseguire un indicizzatore in qualsiasi momento. La creazione di più indicizzatori come descritto in precedenza è utile solo se effettivamente eseguiti in parallelo. Per eseguire più indicizzatori in parallelo, scalare orizzontalmente il servizio di ricerca mediante la creazione di un numero appropriato di partizioni e repliche. Ad esempio, se il servizio di ricerca dispone di 6 unità di ricerca (ad esempio 2 partizioni x 3 repliche), con 6 indicizzatori che possono quindi essere eseguiti contemporaneamente, viene determinato un aumento della velocità effettiva di indicizzazione pari a sei volte. Per altre informazioni sulla scalabilità e sulla pianificazione della capacità, vedere [ridimensionare i livelli di risorse per i carichi di lavoro di query e indicizzazione in Azure ricerca cognitiva](search-capacity-planning.md).

## <a name="indexing-documents-along-with-related-data"></a>Indicizzazione di documenti con dati correlati

Si consiglia di "comporre" i documenti da più origini nell'indice. Ad esempio, è possibile unire testo dagli oggetti binari di grandi dimensioni con altri metadati archiviati in Cosmos DB. È anche possibile utilizzare l'API di indicizzazione push insieme a diversi indicizzatori per compilare i documenti di ricerca da più parti. 

Per funzionare, tutti gli indicizzatori e altri componenti devono concordare sulla chiave del documento. Per altri dettagli su questo argomento, vedere [indicizzare più origini dati di Azure](./tutorial-multiple-data-sources.md). Per una procedura dettagliata, vedere questo articolo esterno: [combinare documenti con altri dati in Azure ricerca cognitiva](https://blog.lytzen.name/2017/01/combine-documents-with-other-data-in.html).

<a name="IndexingPlainText"></a>
## <a name="indexing-plain-text"></a>Indicizzazione di testo normale 

Se tutti gli oggetti binari di grandi dimensioni contengono testo normale nella stessa codifica, è possibile migliorare in modo significativo le prestazioni di indicizzazione utilizzando la **modalità di analisi del testo**. Per utilizzare la modalità di analisi del testo, impostare la `parsingMode` proprietà di configurazione su `text`:

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text" } }
    }
```

Per impostazione predefinita, verrà utilizzata la codifica `UTF-8`. Per specificare una codifica diversa, utilizzare la proprietà di configurazione `encoding`: 

```http
    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
    }
```

<a name="ContentSpecificMetadata"></a>
## <a name="content-type-specific-metadata-properties"></a>Proprietà di metadati specifiche del tipo di contenuto
La tabella seguente riepiloga l'elaborazione eseguita per ogni formato di documento e descrive le proprietà dei metadati estratte da Azure ricerca cognitiva.

| Formato documento/tipo di contenuto | Proprietà di metadati specifiche del tipo di contenuto | Dettagli elaborazione |
| --- | --- | --- |
| HTML (testo/HTML) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |Rimozione del markup HTML ed estrazione del testo |
| PDF (applicazione/PDF) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |Estrazione del testo, inclusi i documenti incorporati (escluse le immagini) |
| DOCX (application/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Estrazione del testo, inclusi i documenti incorporati |
| DOC (application/msword) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Estrazione del testo, inclusi i documenti incorporati |
| DOCM (Application/vnd.ms-word.document. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Estrazione del testo, inclusi i documenti incorporati |
| XML di WORD (Application/vnd. ms-word2006ml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Rimozione del markup XML ed estrazione del testo |
| WORD 2003 XML (Application/vnd. ms-WordML) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date` |Rimozione del markup XML ed estrazione del testo |
| XLSX (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Estrazione del testo, inclusi i documenti incorporati |
| XLS (application/vnd.ms-excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Estrazione del testo, inclusi i documenti incorporati |
| XLSM (Application/vnd. MS-Excel. Sheet. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Estrazione del testo, inclusi i documenti incorporati |
| PPTX (application/vnd.openxmlformats-officedocument.presentationml.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Estrazione del testo, inclusi i documenti incorporati |
| PPT (application/vnd.ms-powerpoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Estrazione del testo, inclusi i documenti incorporati |
| PPTM (Application/vnd. MS-PowerPoint. Presentation. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Estrazione del testo, inclusi i documenti incorporati |
| MSG (application/vnd.ms-outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_from_email`<br/>`metadata_message_to`<br/>`metadata_message_to_email`<br/>`metadata_message_cc`<br/>`metadata_message_cc_email`<br/>`metadata_message_bcc`<br/>`metadata_message_bcc_email`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |Estrae il testo, incluso il testo estratto dagli allegati. `metadata_message_to_email`, `metadata_message_cc_email` e `metadata_message_bcc_email` sono raccolte di stringhe, il resto dei campi è costituito da stringhe.|
| ODT (Application/vnd. Oasis. OpenDocument. Text) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Estrazione del testo, inclusi i documenti incorporati |
| ODS (Application/vnd. Oasis. OpenDocument. Spreadsheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Estrazione del testo, inclusi i documenti incorporati |
| ODP (Application/vnd. Oasis. OpenDocument. Presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`title` |Estrazione del testo, inclusi i documenti incorporati |
| ZIP (application/zip) |`metadata_content_type` |Estrazione del testo da tutti i documenti nell'archivio |
| GZ (applicazione/gzip) |`metadata_content_type` |Estrazione del testo da tutti i documenti nell'archivio |
| EPUB (applicazione/EPUB + zip) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_title`<br/>`metadata_description`<br/>`metadata_language`<br/>`metadata_keywords`<br/>`metadata_identifier`<br/>`metadata_publisher` |Estrazione del testo da tutti i documenti nell'archivio |
| XML (application/xml) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> |Rimozione del markup XML ed estrazione del testo |
| JSON (application/json) |`metadata_content_type`<br/>`metadata_content_encoding` |Estrazione del testo<br/>NOTA: per conoscere i dettagli su come estrarre più campi documento da un BLOB JSON, vedere [Indicizzazione di BLOB JSON](search-howto-index-json-blobs.md) |
| EML (message/rfc822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |Estrazione del testo, inclusi gli allegati |
| RTF (application/rtf) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_page_count`<br/>`metadata_word_count`<br/> | Estrazione del testo|
| Testo normale (text/plain) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> | Estrazione del testo|


## <a name="help-us-make-azure-cognitive-search-better"></a>Aiutaci a migliorare Azure ricerca cognitiva
Per richieste di funzionalità o idee su miglioramenti da apportare, è possibile usare il [sito UserVoice](https://feedback.azure.com/forums/263029-azure-search/).