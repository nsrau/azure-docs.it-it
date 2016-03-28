<properties
pageTitle="Indicizzazione in Archiviazione BLOB di Azure con Ricerca di Azure"
description="Informazioni su come indicizzare Archiviazione BLOB di Azure ed estrarre il testo dai documenti con Ricerca di Azure"
services="search"
documentationCenter=""
authors="chaosrealm"
manager="pablocas"
editor="" />

<tags
ms.service="search"
ms.devlang="rest-api"
ms.workload="search" ms.topic="article"  
ms.tgt_pltfrm="na"
ms.date="03/08/2016"
ms.author="eugenesh" />

# Indicizzazione di documenti in Archiviazione BLOB di Azure con Ricerca di Azure

Questo articolo illustra come usare Ricerca di Azure per indicizzare documenti (ad esempio, PDF o file di Office) salvati nell'archivio BLOB di Azure. Con il nuovo indicizzatore BLOB Ricerca di Azure questo processo diventa rapido e facile.

> [AZURE.IMPORTANT] Questa funzionalità è attualmente in anteprima. È disponibile solo nell'API REST con la versione **2015-02-28-Preview**. Si ricordi che le API di anteprima servono per il test e la valutazione e non devono essere usate negli ambienti di produzione.

## Configurazione dell'indicizzazione BLOB

Per impostare e configurare un indicizzatore di Archiviazione BLOB di Azure, è possibile usare l'API REST di Ricerca di Azure per creare e gestire **indicizzatori** e **origini dati**, come descritto in [questo articolo](https://msdn.microsoft.com/library/azure/dn946891.aspx). In futuro, il supporto per l'indicizzazione BLOB verrà aggiunto ad Azure Search .NET SDK e al portale di Azure.

Un'origine dati specifica i dati da indicizzare, le credenziali necessarie per accedere ai dati e i criteri che consentono a Ricerca di Azure di identificare in modo efficace le modifiche apportate ai dati (righe nuove, modificate o eliminate). Un'origine dati è definita come risorsa indipendente perché possa essere usata da più indicizzatori.

Un indicizzatore è una risorsa che connette le origini dati agli indici di ricerca di destinazione.

Per configurare un indicizzatore BLOB, eseguire queste operazioni:

1. Creare un'origine dati di tipo `azureblob` che faccia riferimento a un contenitore (e, facoltativamente, a una cartella in tale contenitore) in un account di archiviazione di Azure
	- Passare la stringa di connessione dell'account di archiviazione come parametro `credentials.connectionString`
	- Specificare un nome del contenitore. È facoltativamente possibile includere anche una cartella usando il parametro `query`
2. Creare l'indicizzatore connettendo l'origine dati a un indice di destinazione esistente (creare l'indice, se necessario)

Di seguito è illustrato un esempio:

	POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	    "name" : "blob-datasource",
	    "type" : "azureblob",
	    "credentials" : { "connectionString" : "<my storage connection string>" },
	    "container" : { "name" : "my-container", "query" : "my-folder" }
	}   

Creare poi un indicizzatore che faccia riferimento all'origine dati e a un indice di destinazione. Ad esempio:

	POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  "name" : "blob-indexer",
	  "dataSourceName" : "blob-datasource",
	  "targetIndexName" : "my-target-index",
	  "schedule" : { "interval" : "PT2H" }
	}


## Formati di documento supportati

L'indicizzatore BLOB può estrarre il testo dai formati di documento seguenti:

- PDF
- Formati di Microsoft Office: DOCX/DOC, XLSX/XLS, PPTX/PPT, MSG (messaggi di posta elettronica di Outlook)  
- HTML
- XML
- ZIP
- EML
- File di testo normale  

## Processo di estrazione dei documenti

Ricerca di Azure indicizza ogni documento (BLOB) come segue:

- L'intero contenuto del testo del documento viene estratto in un campo di tipo stringa denominato `content`. Si noti che attualmente non viene fornito il supporto per l'estrazione di più documenti da un singolo BLOB:
	- Ad esempio, un file con estensione CSV viene indicizzato come documento singolo.
	- Anche un documento composito o incorporato (ad esempio, un archivio ZIP o un documento di Word con un messaggio di posta elettronica di Outlook incorporato con un allegato PDF) viene indicizzato come documento singolo.

- Le proprietà dei metadati specificate dall'utente eventualmente presenti nel BLOB vengono estratte letteralmente. Le proprietà dei metadati possono essere usate anche per controllare alcuni aspetti del processo di estrazione dei documenti. Per altri dettagli, vedere [Uso di metadati personalizzati per controllare l'estrazione dei documenti](#CustomMetadataControl).

- Le proprietà dei metadati BLOB standard vengono estratte nei campi seguenti:

	- **metadata\_storage\_name** (Edm.String): nome file del BLOB. Se, ad esempio, è presente un BLOB /my-container/my-folder/subfolder/resume.pdf, il valore di questo campo è `resume.pdf`.

	- **metadata\_storage\_path** (Edm.String): URI completo del BLOB, incluso l'account di archiviazione. Ad esempio, `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`

	- **metadata\_storage\_content\_type** (Edm.String): tipo di contenuto specificato dal codice usato per caricare il BLOB. Ad esempio: `application/octet-stream`.

	- **metadata\_storage\_last\_modified** (Edm.DateTimeOffset): timestamp dell'ultima modifica per il BLOB. Ricerca di Azure usa questo timestamp per identificare i BLOB modificati, per evitare di reindicizzare tutto dopo l'indicizzazione iniziale.

	- **metadata\_storage\_size** (Edm.Int64): dimensione del BLOB in byte.

	- **metadata\_storage\_content\_md5** (Edm.String): hash MD5 del contenuto del BLOB, se disponibile.

- Le proprietà dei metadati specifiche di ogni formato di documento vengono estratte nei campi elencati [qui](#ContentSpecificMetadata).

Non è necessario definire i campi per tutte le proprietà precedenti nell'indice di ricerca, ma solo acquisire le proprietà necessarie per l'applicazione.

> [AZURE.NOTE] I nomi dei campi nell'indice esistente saranno spesso diversi da quelli generati durante l'estrazione della documentazione. È possibile usare i **mapping dei campi** per eseguire il mapping dei nomi di proprietà forniti da Ricerca di Azure ai nomi di campo nell'indice di ricerca. Di seguito verrà visualizzato un esempio di mapping dei campi.

## Selezione del campo chiave del documento e gestione di nomi di campo diversi

In Ricerca di Azure la chiave del documento identifica un documento in modo univoco. Ogni indice di ricerca deve avere esclusivamente un campo chiave di tipo Edm.String. Il campo chiave è necessario per ogni documento da aggiungere all'indice (è di fatto l'unico campo obbligatorio).
   
È necessario valutare attentamente di quale campo estratto eseguire il mapping al campo chiave per l'indice. I candidati sono:

- **metadata\_storage\_name**: può essere un valido candidato, ma si noti che 1) è possibile che i nomi non siano univoci, perché potrebbero esserci BLOB con lo stesso nome in cartelle diverse e 2) che è possibile che il nome contenga caratteri non validi nelle chiavi dei documenti, ad esempio trattini. È possibile gestire i caratteri non validi abilitando l'opzione `base64EncodeKeys` nelle proprietà dell'indicizzatore. In questo caso, si ricordi di codificare le chiavi dei documenti quando le si passa nelle chiamate API, ad esempio in una ricerca. In .NET, ad esempio, è possibile usare il metodo [UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) a tale scopo.

- **metadata\_storage\_path**: l'uso del percorso completo garantisce l'univocità, ma il percorso contiene sicuramente caratteri `/` che [non sono validi nella chiave di un documento](https://msdn.microsoft.com/library/azure/dn857353.aspx). Come prima, è possibile codificare le chiavi usando l'opzione `base64EncodeKeys`.

- Se nessuna delle opzioni elencate è appropriata, esiste sempre la possibilità di aggiungere una proprietà di metadati personalizzati al BLOB. Questa opzione, tuttavia, richiede che il processo di caricamento del BLOB aggiunga la proprietà dei metadati a tutti i BLOB. Poiché la chiave è una proprietà obbligatoria, tutti i BLOB privi di tale proprietà non potranno essere indicizzati.

> [AZURE.IMPORTANT] Se non esiste alcun mapping esplicito per il campo chiave nell'indice, Ricerca di Azure userà automaticamente `metadata_storage_path` (la seconda opzione sopra) come chiave e abiliterà la codifica in base 64 delle chiavi.

Per questo esempio, si seleziona il campo `metadata_storage_name` come chiave del documento. Si supponga anche che l'indice includa un campo chiave denominato `key` e un campo `fileSize` in cui archiviare le dimensioni del documento. Per collegare gli elementi come si vuole, specificare i mapping di campo seguenti quando si crea o si aggiorna l'indicizzatore:

	"fieldMappings" : [
	  { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key" },
	  { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
	]

Per unire il tutto, ecco come è possibile aggiungere i mapping di campo e abilitare la codifica in base 64 delle chiavi per un indicizzatore esistente:

	PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  "dataSourceName" : " blob-datasource ",
	  "targetIndexName" : "my-target-index",
	  "schedule" : { "interval" : "PT2H" },
	  "fieldMappings" : [
	    { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key" },
	    { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
	  ],
	  "parameters" : { "base64EncodeKeys": true }
	}

> [AZURE.NOTE] Per ulteriori informazioni sui mapping dei campi, leggere [ questo articolo](search-indexers-customization.md).

## Indicizzazione incrementale e rilevamento delle eliminazioni

Quando si configura un indicizzatore BLOB per l'esecuzione in base a una pianificazione, vengono reindicizzati solo i BLOB modificati, come determinato dal timestamp `LastModified` del BLOB.

> [AZURE.NOTE] Non è necessario specificare un criterio di rilevamento delle modifiche perché l'indicizzazione incrementale viene abilitata automaticamente.

Per indicare che alcuni documenti devono essere rimossi dall'indice, è consigliabile usare una strategia di eliminazione temporanea (invece di eliminare i BLOB corrispondenti), aggiungere una proprietà di metadati personalizzati per indicare che sono stati eliminati e configurare un criterio di rilevamento dell'eliminazione temporanea nell'origine dati.

> [AZURE.WARNING] Se si eliminano solo i BLOB invece di usare un criterio di rilevamento delle eliminazioni, i documenti corrispondenti non verranno rimossi dall'indice di ricerca.

Il criterio illustrato sotto, ad esempio, considererà che un BLOB è stato eliminato se ha una proprietà di metadati `IsDeleted` con il valore `true`:

	PUT https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
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

<a name="ContentSpecificMetadata"></a>
## Proprietà di metadati specifiche del tipo di contenuto

La tabella seguente riepiloga l'elaborazione eseguita per ogni formato di documento e descrive le proprietà dei metadati estratte da Ricerca di Azure.

Formato documento/tipo di contenuto | Proprietà di metadati specifiche del tipo di contenuto | Dettagli elaborazione
-------------------------------|-------------------------------------------|-------------------
HTML (`text/html`) | `metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` | Rimozione del markup HTML ed estrazione del testo
PDF (`application/pdf`) | `metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title`| Estrazione del testo, inclusi i documenti incorporati (escluse le immagini)
DOCX (application/vnd.openxmlformats-officedocument.wordprocessingml.document) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` | Estrazione del testo, inclusi i documenti incorporati
DOC (application/msword) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` | Estrazione del testo, inclusi i documenti incorporati
XLSX (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` | Estrazione del testo, inclusi i documenti incorporati
XLS (application/vnd.ms-excel) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` | Estrazione del testo, inclusi i documenti incorporati
PPTX (application/vnd.openxmlformats-officedocument.presentationml.presentation) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` | Estrazione del testo, inclusi i documenti incorporati
PPT (application/vnd.ms-powerpoint) | `metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` | Estrazione del testo, inclusi i documenti incorporati
MSG (application/vnd.ms-outlook) | `metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_message_bcc`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` | Estrazione del testo, inclusi gli allegati
ZIP (application/zip) | `metadata_content_type` | Estrazione del testo da tutti i documenti nell'archivio
XML (application/xml) | `metadata_content_type`</br>`metadata_content_encoding`</br> | Rimozione del markup XML ed estrazione del testo
JSON (application/json) | `metadata_content_type`</br>`metadata_content_encoding` | Estrazione del testo<br/>NOTA: per estrarre più campi di documento da un blob JSON, votare per [questo suggerimento di UserVoice](https://feedback.azure.com/forums/263029-azure-search/suggestions/11113539-extract-document-structure-from-json-blobs/)
EML (message/rfc822) | `metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` | Estrazione del testo, inclusi gli allegati
Testo normale (text/plain) | `metadata_content_type`</br>`metadata_content_encoding`</br> | 

<a name="CustomMetadataControl"></a>
## Uso di metadati personalizzati per controllare l'estrazione dei documenti

È possibile aggiungere proprietà di metadati a un BLOB per controllare alcuni aspetti del processo di indicizzazione BLOB e di estrazione dei documenti. Attualmente sono supportate le proprietà seguenti:

Nome proprietà | Valore proprietà | Spiegazione
--------------|----------------|------------
AzureSearch\_Skip | "true" | Indica all'indicizzatore BLOB di ignorare completamente il BLOB. Non verrà tentata l'estrazione dei metadati né del contenuto. È utile quando si vuole ignorare alcuni tipi di contenuto o quando un determinato BLOB ha ripetutamente esito negativo e interrompe il processo di indicizzazione.
AzureSearch\_SkipContent | "true" | Indica all'indicizzatore BLOB di indicizzare solo i metadati e di ignorare l'estrazione del contenuto del BLOB. Ciò è utile se il contenuto del BLOB non interessa, ma si vuole indicizzare i metadati associati al BLOB.

<a name="IndexerParametersConfigurationControl"></a>
## Uso dei parametri dell'indicizzatore per controllare l'estrazione dei documenti

Per estrarre i metadati ma ignorare l'estrazione del contenuto per tutti i BLOB, è possibile richiedere questo comportamento tramite la configurazione dell'indicizzatore, invece di aggiungere metadati `AzureSearch_SkipContent` a ogni singolo BLOB. A tale scopo, impostare la proprietà di configurazione `skipContent` su `true` nell'oggetto `parameters`:

 	PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2015-02-28-Preview
	Content-Type: application/json
	api-key: [admin key]

	{
	  ... other parts of indexer definition
	  "parameters" : { "configuration" : { "skipContent" : true } }
	}

## Come contribuire al miglioramento di Ricerca di Azure

Se si hanno domande sulle funzionalità o idee per apportare miglioramenti, contattare Microsoft sul [sito UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

<!---HONumber=AcomDC_0316_2016-->