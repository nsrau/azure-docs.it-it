<properties
	pageTitle="Come usare l'archiviazione BLOB da Node.js | Microsoft Azure"
	description="Informazioni su come usare il servizio BLOB di Azure per caricare, scaricare, elencare ed eliminare contenuti BLOB. Gli esempi sono scritti in Node.js."
	services="storage"
	documentationCenter="nodejs"
	authors="rmcmurray"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="article"
	ms.date="12/01/2015"
	ms.author="robmcm"/>



# Come usare l'archiviazione BLOB da Node.js

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Panoramica

Questa guida illustra diversi scenari d'uso comuni del servizio BLOB di Azure. Gli esempi sono scritti usando l'API Node.js. Gli scenari presentati illustrano come caricare, elencare, scaricare ed eliminare i BLOB.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Creare un'applicazione Node.js

Per istruzioni sulla creazione di un'applicazione Node.js, vedere [Creazione e distribuzione di un'applicazione Node.js in un sito Web di Azure], [di un'applicazione Node.js in un servizio cloud ][Node.js Cloud Service] (con Windows PowerShell) o [di un'app Web con WebMatrix].

## Configurare l'applicazione per l'accesso all'archiviazione

Per usare Archiviazione di Azure, è necessario disporre di Azure Storage SDK per Node.js, che comprende un set di pratiche librerie che comunicano con i servizi di archiviazione REST.

### Usare Node Package Manager (NPM) per ottenere il pacchetto

1.  Usare un'interfaccia della riga di comando come **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Unix) per passare alla cartella in cui è stata creata l'applicazione di esempio.

2.  Digitare **npm install azure-storage** nella finestra di comando. L'output da questo comando sarà simile all'esempio di codice seguente.

		azure-storage@0.5.0 node_modules\azure-storage
		+-- extend@1.2.1
		+-- xmlbuilder@0.4.3
		+-- mime@1.2.11
		+-- node-uuid@1.4.3
		+-- validator@3.22.2
		+-- underscore@1.4.4
		+-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
		+-- xml2js@0.2.7 (sax@0.5.2)
		+-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)

3.  È possibile eseguire manualmente il comando **ls** per verificare che sia stata creata una cartella **node\_modules**. All'interno di tale cartella trovare il pacchetto **azure-storage**, che contiene le librerie necessarie per accedere all'archiviazione.

### Importare il pacchetto

Usando il Blocco note o un altro editor di testo, aggiungere quanto segue alla parte superiore del file **server.js** dell'applicazione in cui si intende usare l'archiviazione:

    var azure = require('azure-storage');

## Configurare una connessione di archiviazione di Azure

Il modulo di Azure leggerà le variabili di ambiente `AZURE_STORAGE_ACCOUNT` e `AZURE_STORAGE_ACCESS_KEY` o `AZURE_STORAGE_CONNECTION_STRING` per ottenere le informazioni necessarie per la connessione all'account di archiviazione di Azure. Se queste variabili di ambiente non sono impostate, sarà necessario specificare le informazioni relative all'account quando si chiama **createBlobService**.

Per un esempio di impostazione delle variabili di ambiente nel [portale di Azure](portal.azure.com) per un'app Web di Azure, vedere [App Web Node.js con archiviazione]

## Creare un contenitore

L'oggetto **BlobService** consente di lavorare con contenitori e BLOB. Il codice seguente consente di creare un oggetto **BlobService**. Aggiungere il codice seguente nella parte superiore di **server.js**:

    var blobSvc = azure.createBlobService();

> [AZURE.NOTE]È possibile accedere a un BLOB in modo anonimo usando **createBlobServiceAnonymous** e specificando l'indirizzo host. Ad esempio, usare `var blobSvc = azure.createBlobServiceAnonymous('https://myblob.blob.core.windows.net/');`.

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

Per creare un nuovo contenitore, usare **createContainerIfNotExists**. L'esempio di codice seguente crea un nuovo contenitore denominato "mycontainer":

	blobSvc.createContainerIfNotExists('mycontainer', function(error, result, response){
      if(!error){
        // Container exists and allows
        // anonymous read access to blob
        // content and metadata within this container
      }
	});

Se il contenitore viene creato, `result` è true. Se il contenitore esiste già, `result` è false. `response` contiene informazioni sull'operazione, incluse le informazioni sull'[ETag](http://en.wikipedia.org/wiki/HTTP_ETag) per il contenitore.

### Sicurezza del contenitore

Per impostazione predefinita, i nuovi contenitori sono privati e non è possibile accedervi in modo anonimo. Per rendere pubblico il contenitore affinché sia accessibile in modo anonimo, è possibile impostarne il livello di accesso su **blob** o **container**.

* L'impostazione **blob** consente l'accesso anonimo in lettura al contenuto e ai metadati del BLOB all'interno di quel contenitore, ma non ai metadati del contenitore, ad esempio l'elenco di tutti i BLOB all'interno di un contenitore.

* L'impostazione **container** consente l'accesso anonimo in lettura al contenuto e ai metadati del BLOB, nonché ai metadati del contenitore.

L'esempio di codice seguente illustra l'impostazione del livello di accesso su **blob**:

    blobSvc.createContainerIfNotExists('mycontainer', {publicAccessLevel : 'blob'}, function(error, result, response){
      if(!error){
        // Container exists and is private
      }
	});

In alternativa, è possibile modificare il livello di accesso di un contenitore utilizzando **setContainerAcl** per specificare il livello di accesso. L'esempio di codice seguente illustra la modifica del livello di accesso al contenitore:

    blobSvc.setContainerAcl('mycontainer', null /* signedIdentifiers */, 'container' /* publicAccessLevel*/, function(error, result, response){
	  if(!error){
		// Container access level set to 'container'
	  }
	});

Il risultato contiene informazioni sull'operazione, incluso l'**ETag** corrente per il contenitore.

### Filtri

È possibile applicare operazioni di filtro facoltative alle operazioni eseguite usando **BlobService**. Le operazioni di filtro possono includere la registrazione, la ripetizione automatica dei tentativi e così via. I filtri sono oggetti che implementano un metodo con la firma:

		function handle (requestOptions, next)

Dopo aver eseguito la pre-elaborazione sulle opzioni della richiesta, il metodo deve chiamare "next" passando un callback con la firma seguente:

		function (returnObject, finalCallback, next)

In questo callback, e dopo l'elaborazione del returnObject (la risposta della richiesta al server), il callback deve richiamare "next", se questo esiste, per continuare a elaborare altri filtri oppure semplicemente richiamare finalCallback per concludere la chiamata al servizio.

Sono inclusi due filtri che implementano la logica di ripetizione dei tentativi con Azure SDK per Node.js: **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. Il codice seguente consente di creare un oggetto **BlobService** che utilizza **ExponentialRetryPolicyFilter**:

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var blobSvc = azure.createBlobService().withFilter(retryOperations);

## Caricare un BLOB in un contenitore

Un BLOB può essere basato su blocchi o su pagine. I BLOB in blocchi consentono di caricare dati di grandi dimensioni in modo più efficiente, mentre i BLOB di pagine sono ottimizzati per le operazioni in lettura e scrittura. Per altre informazioni, vedere [Informazioni sui Blob in blocchi e sui Blob di pagine](http://msdn.microsoft.com/library/azure/ee691964.aspx).

### BLOB in blocchi

Per caricare i dati in un BLOB in blocchi, usare le operazioni seguenti:

* **createBlockBlobFromLocalFile**: crea un nuovo BLOB in blocchi e carica il contenuto di un file

* **createBlockBlobFromStream**: crea un nuovo BLOB in blocchi e carica il contenuto di un flusso

* **createBlockBlobFromText**: crea un nuovo BLOB in blocchi e carica il contenuto di una stringa

* **createWriteStreamToBlockBlob**: fornisce un flusso di scrittura a un BLOB in blocchi

L'esempio di codice seguente carica il contenuto del file **test.txt** nel BLOB **myblob**.

	blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', function(error, result, response){
	  if(!error){
	    // file uploaded
	  }
	});

L'oggetto `result` restituito da questi metodi contiene informazioni sull'operazione, ad esempio l'**ETag** del BLOB.

### BLOB di pagine

Per caricare i dati in un BLOB di pagine, usare le operazioni seguenti:

* **createPageBlob**: crea un nuovo BLOB di pagine con una lunghezza specifica

* **createPageBlobFromLocalFile**: crea un nuovo BLOB di pagine e carica il contenuto di un file

* **createPageBlobFromStream**: crea un nuovo BLOB di pagine e carica il contenuto di un flusso

* **createWriteStreamToExistingPageBlob**: fornisce un flusso di scrittura a un BLOB di pagine esistente

* **createWriteStreamToNewPageBlob**: crea un nuovo BLOB e quindi fornisce un flusso per la scrittura nel BLOB stesso

L'esempio di codice seguente carica il contenuto del file **test.txt** nel BLOB **mypageblob**.

	blobSvc.createPageBlobFromLocalFile('mycontainer', 'mypageblob', 'test.txt', function(error, result, response){
	  if(!error){
	    // file uploaded
	  }
	});

> [AZURE.NOTE]I BLOB di pagine sono costituiti da "pagine" da 512 byte. Potrebbe essere visualizzato un errore quando si caricano dati con una dimensione diversa da un multiplo di 512.

## Elencare i BLOB in un contenitore

Per elencare i BLOB all'interno di un contenitore, usare il metodo **listBlobsSegmented**. Per fare in modo che vengano restituiti i BLOB con un prefisso specifico, usare il metodo **listBlobsSegmentedWithPrefix**.

    blobSvc.listBlobsSegmented('mycontainer', null, function(error, result, response){
      if(!error){
        // result.entries contains the entries
        // If not all blobs were returned, result.continuationToken has the continuation token.
	  }
	});

`result` contiene una raccolta di `entries`, ovvero una matrice di oggetti che descrivono ogni BLOB. Se non vengono restituiti tutti i BLOB, `result` fornisce anche un `continuationToken`, che può essere usato come secondo parametro per recuperare voci aggiuntive.

## Scaricare BLOB

Per scaricare i dati da un BLOB, usare le operazioni seguenti:

* **getBlobToLocalFile** - scrive i contenuti del BLOB in un file.

* **getBlobToStream**: scrive il contenuto del BLOB in un flusso

* **getBlobToText**: scrive il contenuto del BLOB in una stringa

* **createReadStream**: fornisce un flusso per la lettura dal BLOB.

L'esempio di codice seguente illustra l'uso di **getBlobToStream** per scaricare il contenuto del BLOB **myblob** e archiviarlo nel file **output.txt** usando un flusso:

    var fs = require('fs');
	blobSvc.getBlobToStream('mycontainer', 'myblob', fs.createWriteStream('output.txt'), function(error, result, response){
	  if(!error){
	    // blob retrieved
	  }
	});

`result` contiene informazioni sul BLOB, incluse le informazioni sull'**ETag**.

## Eliminare un BLOB

Per eliminare un BLOB, infine, chiamare **deleteBlob**. L'esempio di codice seguente elimina il BLOB denominato **myblob**.

    blobSvc.deleteBlob(containerName, 'myblob', function(error, response){
	  if(!error){
		// Blob has been deleted
	  }
	});

## Accesso simultaneo

Per supportare l'accesso simultaneo a un BLOB da più client o da più istanze di processo, è possibile usare gli **ETag** o **lease**

* **Etag**: consente di rilevare se il BLOB o il contenitore è stato modificato da un altro processo

* **Lease**: consente di ottenere accesso esclusivo, rinnovabile, in scrittura o eliminazione a un BLOB per un periodo di tempo

### ETag

Usare gli ETag se è necessario consentire a più client o istanze di scrivere simultaneamente nel BLOB. L'ETag consente di determinare se il contenitore o il BLOB è stato modificato dalla data di creazione o dell'ultima lettura per evitare di sovrascrivere le modifiche già sottoposte a commit da un altro client o processo.

È possibile impostare le condizioni degli ETag usando il parametro `options.accessConditions` facoltativo. L'esempio di codice seguente carica il file **test.txt** solo se il BLOB è già presente e include il valore di ETag contenuto in `etagToMatch`.

	blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', { accessConditions: { 'if-match': etagToMatch} }, function(error, result, response){
      if(!error){
	    // file uploaded
	  }
	});

Il modello generale per l'uso degli ETag è il seguente:

1. Ottenere l'ETag in seguito a un'operazione create, list o get.

2. Eseguire un'azione verificando che il valore ETag non sia stato modificato.

Se il valore è stato modificato, significa che un altro client o un'altra istanza ha modificato il BLOB o il contenitore in un momento successivo a quello in cui è stato ottenuto il valore ETag.

### Lease

È possibile acquisire un nuovo lease usando il metodo **acquireLease** e specificando il BLOB o il contenitore per il quale si vuole ottenere il lease. Il codice seguente ad esempio acquisisce un lease sul BLOB **myblob**.

	blobSvc.acquireLease('mycontainer', 'myblob', function(error, result, response){
	  if(!error) {
	    console.log('leaseId: ' + result.id);
	  }
	});

Le operazioni successive sul BLOB **myblob** devono fornire il parametro `options.leaseId`. L'ID del lease viene restituito come `result.id` da **acquireLease**.

> [AZURE.NOTE]Per impostazione predefinita, la durata del lease è infinita. Per definire una durata non infinita (compresa tra 15 e 60 secondi), specificare il parametro `options.leaseDuration`.

Per rimuovere un lease, usare il metodo **releaseLease**. Per interrompere un lease e impedire ad altri di ottenere un nuovo lease fintanto che la durata originale non scade, usare il metodo **breakLease**.

## Usare le firme di accesso condiviso di Azure

Le firme di accesso condiviso rappresentano un modo sicuro per fornire accesso granulare a BLOB e contenitori senza specificare il nome o le chiavi dell'account di archiviazione. Tali firme vengono spesso usate per fornire accesso limitato ai dati, ad esempio per consentire a un'app per dispositivi mobili di accedere ai BLOB.

> [AZURE.NOTE]Benché sia anche possibile consentire l'accesso anonimo ai BLOB, le firme di accesso condiviso garantiscono un accesso più controllato, in quanto devono essere generate.

Un'applicazione attendibile, ad esempio un servizio basato sul cloud, genera una firma di accesso condiviso tramite il metodo **generateSharedAccessSignature** dell'oggetto **BlobService** e la fornisce a un'applicazione non attendibile o parzialmente attendibile, ad esempio a un'app per dispositivi mobili. La firma di accesso condiviso viene generata tramite un criterio che indica le date di inizio e di fine del periodo di validità della firma, nonché il livello di accesso concesso al titolare della firma.

L'esempio di codice seguente genera un nuovo criterio di accesso condiviso che consente al titolare della firma di accesso condiviso di eseguire operazioni di lettura nel BLOB **myblob** e che scadrà 100 minuti dopo la data di creazione.

	var startDate = new Date();
	var expiryDate = new Date(startDate);
	expiryDate.setMinutes(startDate.getMinutes() + 100);
	startDate.setMinutes(startDate.getMinutes() - 100);

	var sharedAccessPolicy = {
	  AccessPolicy: {
	    Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
	    Start: startDate,
	    Expiry: expiryDate
	  },
	};

	var blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', 'myblob', sharedAccessPolicy);
	var host = blobSvc.host;

Si noti che devono essere fornite anche le informazioni sull'host, in quanto sono necessarie quando il titolare della firma di accesso condiviso tenta di accedere al contenitore.

L'applicazione client usa quindi la firma di accesso condiviso con **BlobServiceWithSAS** per eseguire operazioni sul BLOB. L'operazione seguente consente di recuperare informazioni sul BLOB **myblob**.

	var sharedBlobSvc = azure.createBlobServiceWithSas(host, blobSAS);
	sharedBlobSvc.getBlobProperties('mycontainer', 'myblob', function (error, result, response) {
	  if(!error) {
	    // retrieved info
	  }
	});

Poiché la firma di accesso condiviso è stata generata con l'accesso in sola lettura, verrà restituito un errore se viene eseguito un tentativo di modificare il BLOB.

### Elenchi di controllo di accesso

Per impostare i criteri di accesso per una firma di accesso condiviso, è anche possibile usare un elenco di controllo di accesso. Questa soluzione è utile quando si desidera consentire a più client di accedere a un contenitore, impostando tuttavia criteri di accesso diversi per ogni client.

Un elenco di controllo di accesso viene implementato usando una matrice di criteri di accesso, con un ID associato a ogni criterio. L'esempio di codice seguente definisce due criteri, uno per 'user1' e uno per 'user2':

	var sharedAccessPolicy = [
	  {
	    AccessPolicy: {
	      Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
	      Start: startDate,
	      Expiry: expiryDate
	    },
	    Id: 'user1'
	  },
	  {
	    AccessPolicy: {
	      Permissions: azure.BlobUtilities.SharedAccessPermissions.WRITE,
	      Start: startDate,
	      Expiry: expiryDate
	    },
	    Id: 'user2'
	  }
	];

L'esempio di codice seguente recupera l'elenco di controllo di accesso corrente per **mycontainer** e quindi aggiunge i nuovi criteri tramite **setBlobAcl**. Risultato:

	blobSvc.getBlobAcl('mycontainer', function(error, result, response) {
      if(!error){
		//push the new policy into signedIdentifiers
		result.signedIdentifiers.push(sharedAccessPolicy);
		blobSvc.setBlobAcl('mycontainer', result, function(error, result, response){
	  	  if(!error){
	    	// ACL set
	  	  }
		});
	  }
	});

Dopo aver impostato l'elenco di controllo di accesso, è possibile creare una firma di accesso condiviso in base all'ID di un criterio. L'esempio di codice seguente crea una nuova firma di accesso condiviso per 'user2':

	blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', { Id: 'user2' });

## Passaggi successivi

Per altre informazioni, vedere le risorse seguenti:

-   [Riferimento per le API di Azure Storage SDK per Node.js][]
-   [Blog del team di Archiviazione di Azure][]
-   Repository [Azure Storage SDK per Node][] su GitHub
-   [Centro per sviluppatori di Node. js](/develop/nodejs/)
-   [Trasferire dati con l'utilità della riga di comando AzCopy](storage-use-azcopy)

[Azure Storage SDK per Node]: https://github.com/Azure/azure-storage-node
[Create and deploy a Node.js application to an Azure Web Site]: /develop/nodejs/tutorials/create-a-website-(mac)/
[Node.js Cloud Service with Storage]: ../storage-nodejs-use-table-storage-cloud-service-app.md
[App Web Node.js con archiviazione]: ../storage-nodejs-use-table-storage-web-site.md
[di un'app Web con WebMatrix]: ../web-sites-nodejs-use-webmatrix.md
[Using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx
[Azure Portal]: portal.azure.com
[Node.js Cloud Service]: ../cloud-services-nodejs-develop-deploy-app.md
[Blog del team di Archiviazione di Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[Riferimento per le API di Azure Storage SDK per Node.js]: http://dl.windowsazure.com/nodestoragedocs/index.html

<!---HONumber=AcomDC_1217_2015-->