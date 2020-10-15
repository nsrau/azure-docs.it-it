---
title: Configurare un indicizzatore BLOB
titleSuffix: Azure Cognitive Search
description: Configurare un indicizzatore BLOB di Azure per automatizzare l'indicizzazione del contenuto BLOB per le operazioni di ricerca full-text in Azure ricerca cognitiva.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: e3419711c9a7358914f85574f6dbd5af29def1cf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91403612"
---
# <a name="how-to-configure-a-blob-indexer-in-azure-cognitive-search"></a>Come configurare un indicizzatore BLOB in Azure ricerca cognitiva

Questo articolo illustra come usare ricerca cognitiva di Azure per indicizzare documenti basati su testo (ad esempio, PDF, documenti Microsoft Office e altri formati comuni) archiviati nell'archivio BLOB di Azure. In primo luogo, vengono illustrate le nozioni di base per l'impostazione e la configurazione di un indicizzatore BLOB. Vengono inoltre descritti in modo più dettagliato i comportamenti e gli scenari che possono verificarsi.

<a name="SupportedFormats"></a>

## <a name="supported-formats"></a>Formati supportati

L'indicizzatore BLOB può estrarre il testo dai formati di documento seguenti:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="set-up-blob-indexing"></a>Configurare l'indicizzazione BLOB

È possibile impostare un indicizzatore dell'Archiviazione BLOB di Azure usando:

* [Portale di Azure](https://ms.portal.azure.com)
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
* * * le credenziali forniscono la stringa di connessione dell'account di archiviazione come `credentials.connectionString` parametro. Per informazioni dettagliate, vedere [Come specificare le credenziali](#Credentials) più avanti.
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

#### <a name="how-to-specify-credentials"></a>Come specificare le credenziali

Per specificare le credenziali per il contenitore BLOB, sono disponibili questi modi:

* **Stringa di connessione identità gestita**: `ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.Storage/storageAccounts/<your storage account name>/;` 

  Questa stringa di connessione non richiede una chiave dell'account, ma è necessario seguire le istruzioni per la [configurazione di una connessione a un account di archiviazione di Azure usando un'identità gestita](search-howto-managed-identities-storage.md).

* **Stringa di connessione dell'account di archiviazione con accesso completo**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>`

  Per ottenere la stringa di connessione dal portale di Azure, passare al pannello dell'account di archiviazione e quindi selezionare Impostazioni > Chiavi (per gli account di archiviazione della versione classica) oppure Impostazioni > Chiavi di accesso (per gli account di archiviazione di Azure Resource Manager).

* Stringa di connessione della **firma di accesso condiviso** (SAS) dell'account di archiviazione:`BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl`

  La firma di accesso condiviso deve avere le autorizzazioni per le operazioni di elenco e lettura per i contenitori e gli oggetti (BLOB).

* **Firma di accesso condiviso del contenitore**: `ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl`

  La firma di accesso condiviso deve avere le autorizzazioni per le operazioni di elenco e lettura sul contenitore.

Per altre informazioni sulle firme di accesso condiviso di archiviazione, vedere [uso delle firme di accesso condiviso](../storage/common/storage-sas-overview.md).

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

Per altre informazioni, vedere [create index (API REST)](/rest/api/searchservice/create-index).

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

Per altre informazioni, vedere [creare un indicizzatore (API REST)](/rest/api/searchservice/create-indexer). Per altre informazioni sulla definizione delle pianificazioni degli indicizzatori, vedere [Come pianificare gli indicizzatori per Ricerca cognitiva di Azure](search-howto-schedule-indexers.md).

<a name="how-azure-search-indexes-blobs"></a>

## <a name="how-blobs-are-indexed"></a>Modalità di indicizzazione di BLOB

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

  * **metadata\_storage\_path** (Edm.String): URI completo del BLOB, incluso l'account di archiviazione. Ad esempio: `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`

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

<a name="DocumentKeys"></a>

### <a name="defining-document-keys-and-field-mappings"></a>Definizione di chiavi di documento e dei mapping dei campi

In Azure ricerca cognitiva la chiave del documento identifica un documento in modo univoco. Ogni indice di ricerca deve avere esclusivamente un campo chiave di tipo Edm.String. Il campo chiave è necessario per ogni documento da aggiungere all'indice (è di fatto l'unico campo obbligatorio).  

È necessario valutare attentamente di quale campo estratto eseguire il mapping al campo chiave per l'indice. I candidati sono:

* **metadata\_storage\_name**: può essere un candidato valido, tuttavia è bene notare che 1) è possibile che i nomi non siano univoci, perché potrebbero esserci BLOB con lo stesso nome in cartelle diverse e 2) è possibile che il nome contenga caratteri non validi nelle chiavi dei documenti, ad esempio trattini. È possibile gestire i caratteri non validi usando la  [funzione di mapping dei campi](search-indexer-field-mappings.md#base64EncodeFunction)`base64Encode`. In questo caso, è necessario ricordarsi di codificare le chiavi dei documenti quando si passano nelle chiamate API, ad esempio in una ricerca. In .NET, ad esempio, è possibile usare il metodo [UrlTokenEncode method](/dotnet/api/system.web.httpserverutility.urltokenencode) a tale scopo.

* **metadata\_storage\_path**: l'uso del percorso completo garantisce l'univocità, ma il percorso contiene sicuramente caratteri `/` che [non sono validi nella chiave di un documento](/rest/api/searchservice/naming-rules).  Come prima, è possibile codificare le chiavi usando la  [funzione](search-indexer-field-mappings.md#base64EncodeFunction)`base64Encode`.

* Una terza opzione consiste nell'aggiungere una proprietà dei metadati personalizzata ai BLOB. Questa opzione, tuttavia, richiede che il processo di caricamento BLOB aggiunga tale proprietà dei metadati a tutti i BLOB. Poiché la chiave è una proprietà obbligatoria, tutti i BLOB privi di tale proprietà non potranno essere indicizzati.

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

Per ulteriori informazioni, vedere [mapping dei campi e trasformazioni](search-indexer-field-mappings.md).

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

## <a name="index-by-file-type"></a>Indice per tipo di file

È possibile controllare quali BLOB vengono indicizzati e quali vengono ignorati.

### <a name="include-blobs-having-specific-file-extensions"></a>Includi BLOB con estensioni di file specifiche

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

### <a name="exclude-blobs-having-specific-file-extensions"></a>Escludere BLOB con estensioni di file specifiche

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

## <a name="index-parts-of-a-blob"></a>Indicizzare parti di un BLOB

Il parametro di configurazione `dataToExtract` permette di controllare quali parti dei BLOB vengono indicizzate. I valori possibili sono i seguenti:

* `storageMetadata`: specifica che vengono indicizzati solo [i metadati specificati dall'utente e le proprietà BLOB standard](../storage/blobs/storage-blob-container-properties-metadata.md).
* `allMetadata`: specifica che vengono indicizzati i metadati di archiviazione e i [metadati specifici del tipo di contenuto](#ContentSpecificMetadata) estratti dal contenuto BLOB.
* `contentAndMetadata`: specifica che vengono indicizzati tutti i metadati e il contenuto di testo estratti dal BLOB. Rappresenta il valore predefinito.

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

| Nome proprietà | Valore proprietà | Spiegazione |
| --- | --- | --- |
| AzureSearch_Skip |"true" |Indica all'indicizzatore BLOB di ignorare completamente il BLOB. Non verrà tentata l'estrazione dei metadati né del contenuto. È utile quando un determinato BLOB ha ripetutamente esito negativo e interrompe il processo di indicizzazione. |
| AzureSearch_SkipContent |"true" |Equivale all'impostazione `"dataToExtract" : "allMetadata"` descritta [in precedenza](#PartsOfBlobToIndex) nell'ambito di un BLOB specifico. |

## <a name="index-from-multiple-sources"></a>Indice da più origini

Si consiglia di "comporre" i documenti da più origini nell'indice. Ad esempio, è possibile unire testo dagli oggetti binari di grandi dimensioni con altri metadati archiviati in Cosmos DB. È anche possibile utilizzare l'API di indicizzazione push insieme a diversi indicizzatori per compilare i documenti di ricerca da più parti.

Per funzionare, tutti gli indicizzatori e altri componenti devono concordare sulla chiave del documento. Per altri dettagli su questo argomento, fare riferimento a [indicizzare più origini dati di Azure](./tutorial-multiple-data-sources.md) o questo post di Blog, [combinare documenti con altri dati in ricerca cognitiva di Azure](https://blog.lytzen.name/2017/01/combine-documents-with-other-data-in.html).

## <a name="index-large-datasets"></a>Indicizzare set di impostazioni di grandi dimensioni

L'indicizzazione di BLOB può richiedere molto tempo. Quando si hanno milioni di BLOB da indicizzare, è possibile velocizzare l'operazione partizionando i dati e usando più indicizzatori per elaborare i dati in parallelo. A tale scopo, è possibile procedere come segue:

* Partizionare i dati in più contenitori BLOB o cartelle virtuali.

* Configurare diverse origini dati di Azure ricerca cognitiva, una per ogni contenitore o cartella. Per puntare a una cartella BLOB, usare il parametro `query`:

    ```json
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

* Creare un indicizzatore corrispondente per ogni origine dati. Tutti gli indicizzatori possono puntare allo stesso indice di ricerca di destinazione.  

* Un'unità di ricerca del servizio permette di eseguire un indicizzatore in qualsiasi momento. La creazione di più indicizzatori come descritto in precedenza è utile solo se effettivamente eseguiti in parallelo. Per eseguire più indicizzatori in parallelo, scalare orizzontalmente il servizio di ricerca mediante la creazione di un numero appropriato di partizioni e repliche. Ad esempio, se il servizio di ricerca dispone di 6 unità di ricerca (ad esempio 2 partizioni x 3 repliche), con 6 indicizzatori che possono quindi essere eseguiti contemporaneamente, viene determinato un aumento della velocità effettiva di indicizzazione pari a sei volte. Per altre informazioni sulla scalabilità e sulla pianificazione della capacità, vedere [modificare la capacità di un servizio ricerca cognitiva di Azure](search-capacity-planning.md).

<a name="DealingWithErrors"></a>

## <a name="handle-errors"></a>Gestire gli errori

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

<a name="ContentSpecificMetadata"></a>

## <a name="content-type-specific-metadata-properties"></a>Proprietà di metadati specifiche del tipo di contenuto

La tabella seguente riepiloga l'elaborazione eseguita per ogni formato di documento e descrive le proprietà dei metadati estratte da Azure ricerca cognitiva.

| Formato documento/tipo di contenuto | Metadati estratti | Dettagli elaborazione |
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

## <a name="see-also"></a>Vedere anche

* [Indicizzatori in Ricerca cognitiva di Azure](search-indexer-overview.md)
* [Informazioni sui BLOB con intelligenza artificiale](search-blob-ai-integration.md)
* [Panoramica dell'indicizzazione BLOB](search-blob-storage-integration.md)