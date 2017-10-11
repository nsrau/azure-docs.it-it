---
title: Indicizzazione in Archiviazione BLOB di Azure con Ricerca di Azure
description: Informazioni su come indicizzare Archiviazione BLOB di Azure ed estrarre il testo dai documenti con Ricerca di Azure
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 2a5968f4-6768-4e16-84d0-8b995592f36a
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/22/2017
ms.author: eugenesh
ms.translationtype: HT
ms.sourcegitcommit: d24c6777cc6922d5d0d9519e720962e1026b1096
ms.openlocfilehash: 97c1fc602ba27472fed2f11fd634e617ae9c636f
ms.contentlocale: it-it
ms.lasthandoff: 09/14/2017

---

# <a name="indexing-documents-in-azure-blob-storage-with-azure-search"></a>Indicizzazione di documenti in Archiviazione BLOB di Azure con Ricerca di Azure
Questo articolo illustra come usare Ricerca di Azure per indicizzare documenti (ad esempio PD, documenti di Microsoft Office e numerosi altri formati comuni) salvati nell'archivio BLOB di Azure. In primo luogo, vengono illustrate le nozioni di base per l'impostazione e la configurazione di un indicizzatore BLOB. Vengono inoltre descritti in modo più dettagliato i comportamenti e gli scenari che possono verificarsi.

## <a name="supported-document-formats"></a>Formati di documento supportati
L'indicizzatore BLOB può estrarre il testo dai formati di documento seguenti:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="setting-up-blob-indexing"></a>Configurazione dell'indicizzazione BLOB
È possibile impostare un indicizzatore dell'Archiviazione BLOB di Azure usando:

* [Portale di Azure](https://ms.portal.azure.com)
* [API REST](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations) Ricerca di Azure
* [.NET SDK](https://aka.ms/search-sdk) Ricerca di Azure

> [!NOTE]
> Alcune funzionalità (ad esempio, i mapping dei campi) non sono ancora disponibili nel portale e devono essere usate in modo programmatico.
>
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

    POST https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
    }   

Per altre informazioni sull'API di creazione dell'origine dati, vedere [Creare un'origine dati](https://docs.microsoft.com/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>
#### <a name="how-to-specify-credentials"></a>Come specificare le credenziali ####

Per specificare le credenziali per il contenitore BLOB, sono disponibili questi modi:

- **Stringa di connessione dell'account di archiviazione per accesso completo**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>`. Per ottenere la stringa di connessione dal portale di Azure, passare al pannello dell'account di archiviazione e quindi selezionare Impostazioni > Chiavi (per gli account di archiviazione della versione classica) oppure Impostazioni > Chiavi di accesso (per gli account di archiviazione di Azure Resource Manager).
- **Stringa di connessione della firma di accesso condiviso (SAS) dell'account di archiviazione**: `BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl` la SAS deve avere le autorizzazioni per le operazioni di elenco e lettura per i contenitori e gli oggetti (oggetti binario di grandi dimensioni).
-  **Firma di accesso condiviso**: `ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl`la firma di accesso condiviso (SAS) deve avere le autorizzazioni per le operazioni di elenco e lettura sul contenitore.

Per altre informazioni sulle firme di accesso condiviso per l'archiviazione, vedere [Uso delle firme di accesso condiviso](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Se si usano le credenziali di firma di accesso condiviso, sarà necessario aggiornare periodicamente le credenziali dell'origine dati con firme rinnovate per impedire che scadano. Se le credenziali di firma di accesso condiviso scadono, l'indicizzatore avrà esito negativo e restituirà un messaggio di errore simile al seguente: `Credentials provided in the connection string are invalid or have expired.`.  

### <a name="step-2-create-an-index"></a>Passaggio 2: Creare un indice
L'indice consente di specificare i campi in un documento, gli attributi e altri costrutti che danno forma all'esperienza della ricerca.

Di seguito viene illustrato come creare un indice con un campo `content` ricercabile per archiviare il testo estratto dagli oggetti BLOB:   

    POST https://[service name].search.windows.net/indexes?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }

Per altre informazioni sulla creazione di indici, vedere [Creare un indice](https://docs.microsoft.com/rest/api/searchservice/create-index)

### <a name="step-3-create-an-indexer"></a>Passaggio 3: Creare un indicizzatore
Un indicizzatore si connette a un'origine dati con un indice di ricerca di destinazione e consente di pianificare l'automatizzazione dell'aggiornamento dei dati.

Dopo aver creato l'indice e l'origine dati, è possibile creare l'indicizzatore:

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Questo indicizzatore verrà eseguito ogni due ore (l'intervallo di pianificazione è impostato su "PT2H"). Per eseguire un indicizzatore ogni 30 minuti, impostare l'intervallo su "PT30M". L'intervallo minimo supportato è di 5 minuti. La pianificazione è facoltativa: se omessa, l'indicizzatore viene eseguito una sola volta al momento della creazione. Tuttavia, è possibile eseguire un indicizzatore su richiesta in qualsiasi momento.   

Per altre informazioni sull'API di creazione di un indicizzatore, vedere [Creare un indicizzatore](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

## <a name="how-azure-search-indexes-blobs"></a>Indicizzazione dei BLOB con Ricerca di Azure

A seconda della relativa [configurazione](#PartsOfBlobToIndex), l'indicizzatore BLOB può indicizzare solo i metadati di archiviazione, opzione utile quando si è interessati solo ai metadati e non è necessario indicizzare il contenuto dei BLOB, indicizzare i metadati del contenuto e di archiviazione o indicizzare sia i metadati che il contenuto di testo. Per impostazione predefinita, l'indicizzatore estrae sia i metadati che il contenuto.

> [!NOTE]
> Per impostazione predefinita, i BLOB con contenuto strutturato, come quelli in formato JSON o CSV, vengono indicizzati come un unico blocco di testo. Per indicizzare i BLOB JSON e CSV in modo strutturato, vedere le funzionalità in anteprima [Indicizzazione di BLOB JSON](search-howto-index-json-blobs.md) e [Indicizzazione di BLOB CSV](search-howto-index-csv-blobs.md).
>
> Anche un documento composito o incorporato (ad esempio, un archivio ZIP o un documento di Word con una e-mail di Outlook incorporata con allegati) viene indicizzato come documento singolo.

* Il contenuto di testo del documento viene estratto in un campo di tipo stringa denominato `content`.

> [!NOTE]
> Ricerca di Azure limita il testo estratto in base al piano tariffario: 32.000 caratteri per il livello gratuito, 64.000 per il livello Basic e 4 milioni per i livelli Standard, Standard S2 e Standard S3. Un avviso è incluso nella risposta dello stato dell'indicizzatore per i documenti troncati.  

* Le proprietà dei metadati specificate dall'utente eventualmente presenti nel BLOB vengono estratte letteralmente.
* Le proprietà dei metadati BLOB standard vengono estratte nei campi seguenti:

  * **metadata\_storage\_name** (Edm.String): nome file del BLOB. Se, ad esempio, è presente un BLOB /my-container/my-folder/subfolder/resume.pdf, il valore di questo campo è `resume.pdf`.
  * **metadata\_storage\_path** (Edm.String): URI completo del BLOB, incluso l'account di archiviazione. Ad esempio, `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`
  * **metadata\_storage\_content\_type** (Edm.String): tipo di contenuto specificato dal codice usato per caricare il BLOB. Ad esempio: `application/octet-stream`.
  * **metadata\_storage\_last\_modified** (Edm.DateTimeOffset): ultimo timestamp modificato per il BLOB. Ricerca di Azure usa questo timestamp per identificare i BLOB modificati, in modo da evitare di reindicizzare tutto dopo l'indicizzazione iniziale.
  * **metadata\_storage\_size** (Edm.Int64): dimensioni del BLOB in byte.
  * **metadata\_storage\_content\_md5** (Edm.String): hash MD5 dei contenuti del BLOB, se disponibile.
* Le proprietà dei metadati specifiche di ogni formato di documento vengono estratte nei campi elencati [qui](#ContentSpecificMetadata).

Non è necessario definire i campi per tutte le proprietà precedenti nell'indice di ricerca, ma solo acquisire le proprietà necessarie per l'applicazione.

> [!NOTE]
> I nomi dei campi nell'indice esistente saranno spesso diversi da quelli generati durante l'estrazione della documentazione. È possibile usare i **mapping dei campi** per eseguire il mapping dei nomi di proprietà forniti da Ricerca di Azure ai nomi di campo nell'indice di ricerca. Di seguito verrà visualizzato un esempio di mapping dei campi.
>
>

<a name="DocumentKeys"></a>
### <a name="defining-document-keys-and-field-mappings"></a>Definizione di chiavi di documento e dei mapping dei campi
In Ricerca di Azure la chiave del documento identifica un documento in modo univoco. Ogni indice di ricerca deve avere esclusivamente un campo chiave di tipo Edm.String. Il campo chiave è necessario per ogni documento da aggiungere all'indice (è di fatto l'unico campo obbligatorio).  

È necessario valutare attentamente di quale campo estratto eseguire il mapping al campo chiave per l'indice. I candidati sono:

* **metadata\_storage\_name**: può essere un candidato valido, tuttavia è bene notare che 1) è possibile che i nomi non siano univoci, perché potrebbero esserci BLOB con lo stesso nome in cartelle diverse e 2) è possibile che il nome contenga caratteri non validi nelle chiavi dei documenti, ad esempio trattini. È possibile gestire i caratteri non validi usando la [funzione di mapping dei campi](search-indexer-field-mappings.md#base64EncodeFunction) `base64Encode`. In questo caso, è necessario ricordarsi di codificare le chiavi dei documenti quando si passano nelle chiamate API, ad esempio in una ricerca. In .NET, ad esempio, è possibile usare il metodo [UrlTokenEncode method](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) a tale scopo.
* **metadata\_storage\_path**: l'uso del percorso completo garantisce l'univocità, ma il percorso contiene sicuramente caratteri `/` che [non sono validi nella chiave di un documento](https://docs.microsoft.com/rest/api/searchservice/naming-rules).  Come prima, è possibile codificare le chiavi usando la [funzione](search-indexer-field-mappings.md#base64EncodeFunction) `base64Encode`.
* Se nessuna delle opzioni elencate è appropriata, è possibile aggiungere una proprietà di metadati personalizzati ai BLOB. Questa opzione, tuttavia, richiede che il processo di caricamento del BLOB aggiunga la proprietà dei metadati a tutti i BLOB. Poiché la chiave è una proprietà obbligatoria, tutti i BLOB privi di tale proprietà non potranno essere indicizzati.

> [!IMPORTANT]
> Se non esiste alcun mapping esplicito per il campo chiave nell'indice, Ricerca di Azure usa automaticamente `metadata_storage_path` come chiave e codifica i valori delle chiavi in base 64 (la seconda opzione illustrata sopra).
>
>

Per questo esempio, si seleziona il campo `metadata_storage_name` come chiave del documento. Si supponga anche che l'indice includa un campo chiave denominato `key` e un campo `fileSize` in cui archiviare le dimensioni del documento. Per collegare gli elementi come si vuole, specificare i mapping di campo seguenti quando si crea o si aggiorna l'indicizzatore:

    "fieldMappings" : [
      { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
      { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
    ]

Per unire il tutto, ecco come è possibile aggiungere i mapping di campo e abilitare la codifica in base 64 delle chiavi per un indicizzatore esistente:

    PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2016-09-01
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

> [!NOTE]
> Per altre informazioni sui mapping dei campi, leggere [questo articolo](search-indexer-field-mappings.md).
>
>

<a name="WhichBlobsAreIndexed"></a>
## <a name="controlling-which-blobs-are-indexed"></a>Controllo dei BLOB da indicizzare
È possibile controllare quali BLOB vengono indicizzati e quali vengono ignorati.

### <a name="index-only-the-blobs-with-specific-file-extensions"></a>Indicizzare solo i BLOB con estensioni di file specifiche
È possibile indicizzare solo i BLOB con le estensioni di file specificate tramite il parametro di configurazione dell'indicizzatore `indexedFileNameExtensions`. Il valore è una stringa contenente un elenco delimitato da virgole di estensioni di file (precedute da un punto). Ad esempio, per indicizzare solo i BLOB .PDF e .DOCX eseguire questa operazione:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
    }

### <a name="exclude-blobs-with-specific-file-extensions"></a>Escludere BLOB con estensioni di file specifiche
È possibile escludere dall'indicizzazione i BLOB con estensioni di file specifiche usando il parametro di configurazione `excludedFileNameExtensions`. Il valore è una stringa contenente un elenco delimitato da virgole di estensioni di file (precedute da un punto). Ad esempio, per indicizzare tutti i BLOB ad eccezione di quelli con le estensioni .PNG e .JPEG eseguire questa operazione:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
    }

Se sono presenti entrambi i parametri `indexedFileNameExtensions` e `excludedFileNameExtensions`, Ricerca di Azure esamina in primo luogo `indexedFileNameExtensions`, quindi `excludedFileNameExtensions`. Ciò significa che se la stessa estensione di file è presente in entrambi gli elenchi, verrà esclusa dall'indicizzazione.

### <a name="dealing-with-unsupported-content-types"></a>Gestire tipi di contenuto non supportati

Per impostazione predefinita, l'indicizzatore BLOB viene arrestato non appena viene rilevato un BLOB con un tipo di contenuto non supportato, ad esempio un'immagine. Naturalmente, è possibile usare il parametro `excludedFileNameExtensions` per ignorare determinati tipi di contenuto. Potrebbe tuttavia essere necessario indicizzare BLOB senza conoscere in anticipo tutti i tipi di contenuto possibili. Per continuare l'indicizzazione quando viene rilevato un tipo di contenuto non supportato, impostare il parametro di configurazione `failOnUnsupportedContentType` su `false`:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
    }

### <a name="ignoring-parsing-errors"></a>Ignorare gli errori di analisi

La logica di estrazione documenti di Ricerca di Azure non è perfetta e talvolta può non riuscire ad analizzare i documenti con un tipo di contenuto supportato, ad esempio DOCX o PDF. Per evitare che l'indicizzazione venga interrotta in tali casi, impostare i parametri di configurazione `maxFailedItems` e `maxFailedItemsPerBatch` su valori accettabili. Ad esempio:

    {
      ... other parts of indexer definition
      "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
    }

<a name="PartsOfBlobToIndex"></a>
## <a name="controlling-which-parts-of-the-blob-are-indexed"></a>Controllo delle parti di BLOB da indicizzare

Il parametro di configurazione `dataToExtract` permette di controllare quali parti dei BLOB vengono indicizzate. I valori possibili sono i seguenti:

* `storageMetadata`: specifica che vengono indicizzati solo [i metadati specificati dall'utente e le proprietà BLOB standard](../storage/blobs/storage-properties-metadata.md).
* `allMetadata`: specifica che vengono indicizzati i metadati di archiviazione e i [metadati specifici del tipo di contenuto](#ContentSpecificMetadata) estratti dal contenuto BLOB.
* `contentAndMetadata`: specifica che vengono indicizzati tutti i metadati e il contenuto di testo estratti dal BLOB. Si tratta del valore predefinito.

Ad esempio, per indicizzare solo i metadati di archiviazione, usare:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
    }

### <a name="using-blob-metadata-to-control-how-blobs-are-indexed"></a>Usare i metadati dei BLOB per controllare il modo in cui vengono indicizzati i BLOB

I parametri di configurazione descritti in precedenza si applicano a tutti i BLOB. In alcuni casi è consigliabile controllare il modo in cui vengono indicizzati i *singoli BLOB*. A tale scopo è possibile aggiungere i valori e le proprietà seguenti dei metadati del BLOB:

| Nome proprietà | Valore proprietà | Spiegazione |
| --- | --- | --- |
| AzureSearch_Skip |"true" |Indica all'indicizzatore BLOB di ignorare completamente il BLOB. Non verrà tentata l'estrazione dei metadati né del contenuto. È utile quando un determinato BLOB ha ripetutamente esito negativo e interrompe il processo di indicizzazione. |
| AzureSearch_SkipContent |"true" |Equivale all'impostazione `"dataToExtract" : "allMetadata"` descritta [in precedenza](#PartsOfBlobToIndex) nell'ambito di un BLOB specifico. |

## <a name="incremental-indexing-and-deletion-detection"></a>Indicizzazione incrementale e rilevamento delle eliminazioni
Quando si configura un indicizzatore BLOB per l'esecuzione in base a una pianificazione, vengono reindicizzati solo i BLOB modificati, come determinato dal timestamp `LastModified` del BLOB.

> [!NOTE]
> Non è necessario specificare un criterio di rilevamento delle modifiche perché l'indicizzazione incrementale viene abilitata automaticamente.

Per supportare l'eliminazione di documenti, usare un approccio di "eliminazione temporanea". Se si eliminano completamente i BLOB, i documenti corrispondenti non verranno rimossi dall'indice della ricerca. Seguire invece questa procedura:  

1. Aggiungere al BLOB una proprietà di metadati personalizzata per indicare a Ricerca di Azure che viene eliminato in modo logico
2. Configurare un criterio di rilevamento eliminazione temporanea nell'origine dati
3. Dopo che l'indicizzatore ha elaborato il BLOB (come indicato dall'API di stato dell'indicizzatore), è possibile eliminare fisicamente il BLOB

Il criterio illustrato sotto, ad esempio, considera l'eliminazione di un BLOB se ha una proprietà di metadati `IsDeleted` con il valore `true`:

    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",     
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }   

## <a name="indexing-large-datasets"></a>Indicizzazione di set di dati di grandi dimensioni

L'indicizzazione di BLOB può richiedere molto tempo. Quando si hanno milioni di BLOB da indicizzare, è possibile velocizzare l'operazione partizionando i dati e usando più indicizzatori per elaborare i dati in parallelo. A tale scopo, è possibile procedere come segue:

- Partizionare i dati in più contenitori BLOB o cartelle virtuali.
- Impostare diverse origini dati di Ricerca di Azure, una per ogni contenitore o cartella. Per puntare a una cartella BLOB, usare il parametro `query`:

    ```
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

- Creare un indicizzatore corrispondente per ogni origine dati. Tutti gli indicizzatori possono puntare allo stesso indice di ricerca di destinazione.  

- Un'unità di ricerca del servizio permette di eseguire un indicizzatore in qualsiasi momento. La creazione di più indicizzatori come descritto in precedenza è utile solo se effettivamente eseguiti in parallelo. Per eseguire più indicizzatori in parallelo, scalare orizzontalmente il servizio di ricerca mediante la creazione di un numero appropriato di partizioni e repliche. Ad esempio, se il servizio di ricerca dispone di 6 unità di ricerca (ad esempio 2 partizioni x 3 repliche), con 6 indicizzatori che possono quindi essere eseguiti contemporaneamente, viene determinato un aumento della velocità effettiva di indicizzazione pari a sei volte. Per ulteriori informazioni sulla scalabilità e la pianificazione della capacità, vedere [Ridimensionare i livelli di risorse per i carichi di lavoro di indicizzazione e query in Ricerca di Azure](search-capacity-planning.md).

## <a name="indexing-documents-along-with-related-data"></a>Indicizzazione di documenti con dati correlati

Si consiglia di "comporre" i documenti da più origini nell'indice. Ad esempio, è possibile unire testo dagli oggetti binari di grandi dimensioni con altri metadati archiviati in Cosmos DB. È anche possibile utilizzare l'API di indicizzazione push insieme a diversi indicizzatori per compilare i documenti di ricerca da più parti. 

Per funzionare, tutti gli indicizzatori e altri componenti devono concordare sulla chiave del documento. Per una descrizione dettagliata, vedere l'articolo esterno: [Combinare documenti con altri dati in Ricerca di Azure ](http://blog.lytzen.name/2017/01/combine-documents-with-other-data-in.html).

<a name="IndexingPlainText"></a>
## <a name="indexing-plain-text"></a>Indicizzazione di testo normale 

Se tutti gli oggetti binari di grandi dimensioni contengono testo normale nella stessa codifica, è possibile migliorare in modo significativo le prestazioni di indicizzazione utilizzando la **modalità di analisi del testo**. Per utilizzare la modalità di analisi del testo, impostare la `parsingMode` proprietà di configurazione su `text`:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text" } }
    }

Per impostazione predefinita, verrà utilizzata la codifica `UTF-8`. Per specificare una codifica diversa, utilizzare la proprietà di configurazione `encoding`: 

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
    }


<a name="ContentSpecificMetadata"></a>
## <a name="content-type-specific-metadata-properties"></a>Proprietà di metadati specifiche del tipo di contenuto
La tabella seguente riepiloga l'elaborazione eseguita per ogni formato di documento e descrive le proprietà dei metadati estratte da Ricerca di Azure.

| Formato documento/tipo di contenuto | Proprietà di metadati specifiche del tipo di contenuto | Dettagli elaborazione |
| --- | --- | --- |
| HTML (`text/html`) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |Rimozione del markup HTML ed estrazione del testo |
| PDF (`application/pdf`) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |Estrazione del testo, inclusi i documenti incorporati (escluse le immagini) |
| DOCX (application/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Estrazione del testo, inclusi i documenti incorporati |
| DOC (application/msword) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Estrazione del testo, inclusi i documenti incorporati |
| XLSX (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Estrazione del testo, inclusi i documenti incorporati |
| XLS (application/vnd.ms-excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Estrazione del testo, inclusi i documenti incorporati |
| PPTX (application/vnd.openxmlformats-officedocument.presentationml.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Estrazione del testo, inclusi i documenti incorporati |
| PPT (application/vnd.ms-powerpoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Estrazione del testo, inclusi i documenti incorporati |
| MSG (application/vnd.ms-outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_message_bcc`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |Estrazione del testo, inclusi gli allegati |
| ZIP (application/zip) |`metadata_content_type` |Estrazione del testo da tutti i documenti nell'archivio |
| XML (application/xml) |`metadata_content_type`</br>`metadata_content_encoding`</br> |Rimozione del markup XML ed estrazione del testo |
| JSON (application/json) |`metadata_content_type`</br>`metadata_content_encoding` |Estrazione del testo<br/>NOTA: per conoscere i dettagli su come estrarre più campi documento da un BLOB JSON, vedere [Indicizzazione di BLOB JSON](search-howto-index-json-blobs.md) |
| EML (message/rfc822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |Estrazione del testo, inclusi gli allegati |
| RTF (application/rtf) |`metadata_content_type`</br>`metadata_author`</br>`metadata_character_count`</br>`metadata_creation_date`</br>`metadata_page_count`</br>`metadata_word_count`</br> | Estrazione del testo|
| Testo normale (text/plain) |`metadata_content_type`</br>`metadata_content_encoding`</br> | Estrazione del testo|


## <a name="help-us-make-azure-search-better"></a>Come contribuire al miglioramento di Ricerca di Azure
Per richieste di funzionalità o idee su miglioramenti da apportare, è possibile usare il [sito UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

