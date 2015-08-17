<properties 
	pageTitle="Come usare l'archiviazione BLOB da Node.js | Microsoft Azure" 
	description="Informazioni su come usare il servizio BLOB di Azure per caricare, scaricare, elencare ed eliminare contenuti BLOB. Gli esempi sono scritti in Node.js." 
	services="storage" 
	documentationCenter="nodejs" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="05/11/2015" 
	ms.author="mwasson"/>



# Come usare l'archiviazione BLOB da Node.js

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Panoramica

Questa guida illustra diversi scenari di utilizzo comuni del servizio BLOB di Azure. Gli esempi sono scritti utilizzando l'API Node.js. Gli scenari presentati includono **caricamento**, **visualizzazione dell'elenco**, **download** ed **eliminazione** di BLOB.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Creare un'applicazione Node.js

Creare un'applicazione Node.js vuota. Per istruzioni sulla creazione di un'applicazione Node.js, vedere [Creazione e distribuzione di un'applicazione Node.js in un sito Web di Azure], [Servizio cloud Node.js][Node.js Cloud Service] (usando Windows PowerShell) o [Sito Web con WebMatrix].

## Configurare l'applicazione per l'accesso all'archiviazione

Per usare l'archiviazione di Azure, è necessario scaricare Azure Storage SDK per Node.js, che comprende un set di pratiche librerie che comunicano con i servizi di archiviazione REST.

### Usare Node Package Manager (NPM) per ottenere il pacchetto

1.  Usare un'interfaccia della riga di comando come **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Unix) e spostarsi nella cartella in cui è stata creata l'applicazione di esempio.

2.  Digitare **npm install azure-storage** nella finestra di comando, che dovrebbe restituire il seguente output:

        azure-storage@0.1.0 node_modules\azure-storage
		├── extend@1.2.1
		├── xmlbuilder@0.4.3
		├── mime@1.2.11
		├── underscore@1.4.4
		├── validator@3.1.0
		├── node-uuid@1.4.1
		├── xml2js@0.2.7 (sax@0.5.2)
		└── request@2.27.0 (json-stringify-safe@5.0.0, tunnel-agent@0.3.0, aws-sign@0.3.0, forever-agent@0.5.2, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, hawk@1.0.0, form-data@0.1.3, http-signature@0.10.0)

3.  È possibile eseguire manualmente il comando **ls** per verificare che sia stata creata una cartella **node\_modules**. All'interno di tale cartella individuare il pacchetto **azure-storage**, che contiene le librerie necessarie per accedere all'archiviazione.

### Importare il pacchetto

Utilizzando il Blocco note o un altro editor di testo, aggiungere quanto segue alla parte superiore del file **server.js** dell'applicazione dove si intende utilizzare l'archiviazione:

    var azure = require('azure-storage');

## Configurare una connessione di archiviazione di Azure

Il modulo di Azure leggerà le variabili di ambiente `AZURE_STORAGE_ACCOUNT` e `AZURE_STORAGE_ACCESS_KEY` o `AZURE_STORAGE_CONNECTION_STRING` per ottenere le informazioni necessarie per la connessione all'account di archiviazione di Azure. Se queste variabili di ambiente non sono impostate, sarà necessario specificare le informazioni relative all'account quando si chiama **createBlobService**.

Per un esempio di impostazione delle variabili di ambiente nel portale di gestione per un sito Web di Azure, vedere [Applicazione Web Node.js con archiviazione]

## Come creare un contenitore

L'oggetto **BlobService** consente di lavorare con contenitori e BLOB. Il codice seguente consente di creare un oggetto **BlobService**. Aggiungere il codice seguente nella parte superiore di **server.js**:

    var blobSvc = azure.createBlobService();

> [AZURE.NOTE]È possibile accedere a un BLOB in modo anonimo usando **createBlobServiceAnonymous** e specificando l'indirizzo host. Ad esempio: `var blobSvc = azure.createBlobServiceAnonymous('https://myblob.blob.core.windows.net/');`.

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

Per creare un nuovo contenitore, usare **createContainerIfNotExists**. Il codice seguente crea un nuovo contenitore denominato "mycontainer"

	blobSvc.createContainerIfNotExists('mycontainer', function(error, result, response){
      if(!error){
        // Container exists and allows 
        // anonymous read access to blob 
        // content and metadata within this container
      }
	});

Se il contenitore viene creato, `result` sarà true. Se il contenitore esiste già, `result` sarà false. `response` conterrà informazioni sull'operazione, incluse le informazioni sull'[ETag](http://en.wikipedia.org/wiki/HTTP_ETag) per il contenitore.

### Sicurezza del contenitore

Per impostazione predefinita, i nuovi contenitori sono privati e non è possibile accedervi in modo anonimo. Per rendere pubblico il contenitore affinché sia accessibile in modo anonimo, è possibile impostare il livello di accesso del contenitore su **blob** o **container**.

* L'impostazione **blob** consente l'accesso anonimo in lettura al contenuto e ai metadati del BLOB all'interno di quel contenitore, ma non ai metadati del contenitore, come ad esempio l'elenco di tutti i BLOB all'interno di un contenitore. 

* L'impostazione **container** consente l'accesso anonimo in lettura al contenuto e ai metadati del BLOB nonché ai metadati del contenitore.

Nell'esempio seguente viene illustrata l'impostazione del livello di accesso su **blob**:

    blobSvc.createContainerIfNotExists('mycontainer', {publicAccessLevel : 'blob'}, function(error, result, response){
      if(!error){
        // Container exists and is private
      }
	});

In alternativa, è possibile modificare il livello di accesso di un contenitore usando **setContainerAcl** per specificare il livello di accesso. Nell'esempio seguente viene illustrata la modifica del livello di accesso al contenitore:

    blobSvc.setContainerAcl('mycontainer', null /* signedIdentifiers */, 'container' /* publicAccessLevel*/, function(error, result, response){
	  if(!error){
		// Container access level set to 'container'
	  }
	});

Il risultato conterrà informazioni sull'operazione, incluse le informazioni **ETag** per il contenitore.

### Filtri

Le operazioni di filtro facoltative possono essere applicate alle operazioni eseguite utilizzando **BlobService**. Le operazioni di filtro possono includere la registrazione, la ripetizione automaticamente e così via. I filtri sono oggetti che implementano un metodo con la firma:

		function handle (requestOptions, next)

Dopo avere eseguito la pre-elaborazione sulle opzioni della richiesta, il metodo deve chiamare "next" passando un callback con la seguente firma:

		function (returnObject, finalCallback, next)

In questo callback, e dopo l'elaborazione del returnObject (la risposta della richiesta al server), il callback deve richiamare "next", se questo esiste, per continuare a elaborare altri filtri oppure semplicemente richiamare finalCallback per concludere la chiamata al servizio.

Sono inclusi due filtri che implementano la logica di ripetizione dei tentativi con Azure SDK per Node.js: **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. Il codice seguente consente di creare un oggetto **BlobService** che utilizza **ExponentialRetryPolicyFilter**:

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var blobSvc = azure.createBlobService().withFilter(retryOperations);

## Procedura: caricare un BLOB in un contenitore

Un BLOB può essere basato su blocchi o su pagine. I BLOB in blocchi consentono di caricare dati di grandi dimensioni in modo più efficiente, mentre i BLOB di pagine sono ottimizzati per le operazioni in lettura e scrittura. Per altre informazioni, vedere [Informazioni sui Blob in blocchi e sui Blob di pagine](http://msdn.microsoft.com/library/azure/ee691964.aspx).

### BLOB in blocchi

Per caricare i dati in un BLOB in blocchi, usare le operazioni seguenti:

* **createBlockBlobFromLocalFile**: crea un nuovo BLOB in blocchi e carica il contenuti di un file.

* **createBlockBlobFromStream**: crea un nuovo BLOB in blocchi e carica il contenuti di un flusso.

* **createBlockBlobFromText**: crea un nuovo BLOB in blocchi e carica il contenuti di una stringa.

* **createWriteStreamToBlockBlob**: fornisce un flusso di scrittura a un BLOB in blocchi.

Nell'esempio seguente viene caricato il contenuto del file **test.txt** nel BLOB **myblob**.

	blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', function(error, result, response){
	  if(!error){
	    // file uploaded
	  }
	});

L'oggetto `result` restituito da questi metodi conterrà informazioni sull'operazione, ad esempio l'**ETag** del BLOB.

### BLOB di pagine

Per caricare i dati in un BLOB di pagine, usare le operazioni seguenti:

* **createPageBlob**: crea un nuovo BLOB di pagine con una lunghezza specifica.

* **createPageBlobFromLocalFile**: crea un nuovo BLOB di pagine e carica i contenuti di un file.

* **createPageBlobFromStream**: crea un nuovo BLOB di pagine e carica i contenuti di un flusso.

* **createWriteStreamToExistingPageBlob**: fornisce un flusso di scrittura a un BLOB di pagine esistente.

* **createWriteStreamToNewPageBlob**: crea un nuovo BLOB e quindi fornisce un flusso per la scrittura nel BLOB stesso.

Nell'esempio seguente viene caricato il contenuto del file **test.txt** nel BLOB **mypageblob**.

	blobSvc.createPageBlobFromLocalFile('mycontainer', 'mypageblob', 'test.txt', function(error, result, response){
	  if(!error){
	    // file uploaded
	  }
	});

> [AZURE.NOTE]I BLOB di pagine sono costituiti da "pagine" da 512 byte. Potrebbe essere visualizzato un errore quando si caricano dati con una dimensione diversa da un multiplo di 512.

## Procedura: elencare i BLOB in un contenitore

Per elencare i BLOB all'interno di un contenitore, usare il metodo **listBlobsSegmented**. Per fare in modo che vengano restituiti i BLOB con un prefisso specifico, usare il metodo **listBlobsSegmentedWithPrefix**.

    blobSvc.listBlobsSegmented('mycontainer', null, function(error, result, response){
      if(!error){
        // result contains the entries
	  }
	});

`result` conterrà una raccolta di `entries`, ovvero una matrice di oggetti che descrivono ogni BLOB. Se non vengono restituiti tutti i BLOB, `result` fornirà anche un `continuationToken`, che può essere usato come secondo parametro per recuperare voci aggiuntive.

## Procedura: scaricare i BLOB

Per scaricare i dati da un BLOB, usare le operazioni seguenti:

* **getBlobToFile**: scrive i contenuti del BLOB in un file.

* **getBlobToStream**: scrive i contenuti del BLOB in un flusso.

* **getBlobToText**: scrive i contenuti del BLOB in una stringa.

* **createReadStream**: fornisce un flusso per la lettura dal BLOB.

Nell'esempio seguente viene illustrato l'utilizzo di **getBlobToStream** per scaricare il contenuto del BLOB **myblob** e archiviarlo nel file **output.txt** usando un flusso:

    var fs = require('fs');
	blobSvc.getBlobToStream('mycontainer', 'myblob', fs.createWriteStream('output.txt'), function(error, result, response){
	  if(!error){
	    // blob retrieved
	  }
	});

`result` conterrà informazioni sul BLOB, incluse le informazioni sull'**ETag**.

## Procedura: eliminare un BLOB

Per eliminare un BLOB, infine, chiamare **deleteBlob**. Nell'esempio seguente viene eliminato il BLOB denominato **myblob**.

    blobSvc.deleteBlob(containerName, 'myblob', function(error, response){
	  if(!error){
		// Blob has been deleted
	  }
	});

## Procedura: Accesso simultaneo

Per supportare l'accesso simultaneo a un BLOB da più client o da più istanze di processo, è possibile usare gli **ETag** o **lease**

* **Etag**: fornisce un modo per rilevare le eventuali modifiche apportate al BLOB o contenitore da un altro processo.

* **Lease**: fornisce un modo per ottenere accesso esclusivo, rinnovabile in scrittura ed eliminazione a un BLOB per un periodo di tempo.

### ETag

È consigliabile usare gli ETag se è necessario consentire a più client o istanze di scrivere simultaneamente nel BLOB. L'ETag consente di determinare se il contenitore o il BLOB è stato modificato dalla data di creazione o dell'ultima lettura per evitare di sovrascrivere le modifiche già sottoposte a commit da un altro client o processo.

È possibile impostare le condizioni degli ETag usando il parametro `options.accessConditions` facoltativo. Nell'esempio seguente il file **test.txt** viene caricato solo se il BLOB è già presente e include il valore di ETag contenuto in `etagToMatch`.

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

È possibile acquisire un nuovo lease usando il metodo **acquireLease** e specificando il BLOB o il contenitore per il quale si vuole ottenere il lease. Ad esempio, l'operazione seguente consente di acquisire un lease sul BLOB **myblob**.

	blobSvc.acquireLease('mycontainer', 'myblob', function(error, result, response){
	  if(!error) {
	    console.log('leaseId: ' + result.id);
	  }
	});

Le operazioni successive sul BLOB **myblob** devono fornire il parametro `options.leaseId`. L'ID del lease è restituito come `result.id` dal metodo **acquireLease**.

> [AZURE.NOTE]Per impostazione predefinita, la durata del lease è infinita. Per definire una durata non infinita (compresa tra 15 e 60 secondi), specificare il parametro `options.leaseDuration`.

Per rimuovere un lease, usare il metodo **releaseLease**. Per interrompere un lease e impedire ad altri di ottenere un nuovo lease fintanto che la durata originale non scade, usare il metodo **breakLease**.

## Procedura: usare le firme di accesso condiviso di Azure

Le firme di accesso condiviso rappresentano un modo sicuro per fornire accesso granulare a BLOB e contenitori senza specificare il nome o le chiavi dell'account di archiviazione. Le firme di accesso condiviso vengono spesso usate per fornire accesso limitato ai dati, ad esempio per consentire a un'app per dispositivi mobili di accedere ai BLOB.

> [AZURE.NOTE]Benché sia anche possibile consentire l'accesso anonimo ai BLOB, le firme di accesso condiviso garantiscono un accesso più controllato, in quanto devono essere generate.

Un'applicazione attendibile, ad esempio un servizio basato sul cloud, genera una firma di accesso condiviso tramite il metodo **generateSharedAccessSignature** dell'oggetto **BlobService** e la fornisce a un'applicazione non attendibile o parzialmente attendibile. Si prenda ad esempio un'app per dispositivi mobili. La firma di accesso condiviso viene generata tramite un criterio che indica le date di inizio e di fine del periodo di validità della firma, nonché il livello di accesso concesso al titolare della firma di accesso condiviso.

Nell'esempio seguente viene generato un nuovo criterio di accesso condiviso che consentirà al titolare della firma di accesso condiviso di eseguire operazioni di lettura nel BLOB **myblob** e che scadrà 100 minuti dopo la data di creazione.

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

Si noti che è necessario fornire anche le informazioni sull'host, in quanto sono necessarie quando il titolare della firma di accesso condiviso tenta di accedere al contenitore.

L'applicazione client usa quindi la firma di accesso condiviso con il metodo **BlobServiceWithSAS** per eseguire operazioni sul BLOB. L'operazione seguente consente di recuperare informazioni sul BLOB **myblob**.

	var sharedBlobSvc = azure.createBlobServiceWithSas(host, blobSAS);
	sharedBlobSvc.getBlobProperties('mycontainer', 'myblob', function (error, result, response) {
	  if(!error) {
	    // retrieved info
	  }
	});

Poiché la firma di accesso condiviso è stata generata con accesso di sola lettura, se si tentasse di modificare il BLOB verrebbe restituito un errore.

### Elenchi di controllo di accesso

Per impostare i criteri di accesso per una firma di accesso condiviso è anche possibile usare un elenco di controllo di accesso. Questa soluzione è utile quando si vuole consentire a più client di accedere a un contenitore, impostando tuttavia criteri di accesso diversi per ogni client.

Un elenco di controllo di accesso viene implementato usando una matrice di criteri di accesso, con un ID associato a ogni criterio. Nell'esempio seguente vengono definiti due criteri, uno per 'user1' e uno per 'user2':

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

Nell'esempio seguente viene recuperato l'elenco di controllo di accesso corrente per **mycontainer** e vengono aggiunti i nuovi criteri tramite **setBlobAcl**. Risultato:

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

Dopo avere impostato l'elenco di controllo di accesso, è possibile creare una firma di accesso condiviso in base all'ID di un criterio. Nell'esempio seguente viene creata una nuova firma di accesso condiviso per 'user2':

	blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', { Id: 'user2' });

## Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base dell'archiviazione BLOB, visitare i collegamenti seguenti per altre informazioni sulle attività di archiviazione più complesse.

-   Leggere il [riferimento per le API di Azure Storage SDK per Node.js][]
-   Vedere la documentazione MSDN: [Archiviazione e accesso ai dati in Azure][].
-   [Blog del team di Archiviazione di Azure][].
-   Archivio [Azure SDK for Node][] su GitHub.

[Azure SDK for Node]: https://github.com/Azure/azure-storage-node
[Creazione e distribuzione di un'applicazione Node.js in un sito Web di Azure]: /develop/nodejs/tutorials/create-a-website-(mac)/
[Node.js Cloud Service with Storage]: ../storage-nodejs-use-table-storage-cloud-service-app.md
[Applicazione Web Node.js con archiviazione]: ../storage-nodejs-use-table-storage-web-site.md
[Sito Web con WebMatrix]: ../web-sites-nodejs-use-webmatrix.md
[using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx
[Azure Management Portal]: http://manage.windowsazure.com
[Node.js Cloud Service]: ../cloud-services-nodejs-develop-deploy-app.md
[Archiviazione e accesso ai dati in Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
[Blog del team di Archiviazione di Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[riferimento per le API di Azure Storage SDK per Node.js]: http://dl.windowsazure.com/nodestoragedocs/index.html
 

<!---HONumber=August15_HO6-->