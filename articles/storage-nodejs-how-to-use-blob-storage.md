<properties urlDisplayName="Blob Service" pageTitle="Come usare il servizio di archiviazione BLOB (Node.js) | Microsoft Azure" metaKeywords="Get started Azure blob, Azure unstructured data, Azure unstructured storage, Azure blob, Azure blob storage, Azure blob Node.js" description="Informazioni su come usare il servizio BLOB di Azure per caricare, scaricare, elencare ed eliminare contenuti BLOB. Gli esempi sono scritti in Node.js." metaCanonical="" services="storage" documentationCenter="nodejs" title="How to Use the Blob Service from Node.js" authors="larryfr" solutions="" manager="wpickett" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="mwasson" />





# Come usare il servizio BLOB da Node.js

In questa guida verranno illustrati diversi scenari comuni di uso del servizio BLOB di Azure. Gli esempi sono scritti usando l'API Node.js. Gli scenari presentati includono **caricamento**, **visualizzazione in elenchi**, **download** ed **eliminazione** di BLOB. Per altre informazioni sui BLOB,
vedere la sezione [Passaggi successivi][].

## Sommario

* [Informazioni sul servizio Blob][]    
* [Concetti][]    
* [Creare un account di archiviazione di Azure][]    
* [Creare un'applicazione Node.js][]  
* [Configurare l'applicazione per l'accesso all'archiviazione][]     
* [Configurare una stringa di connessione di archiviazione di Azure][]  
* [Procedura: Creare un contenitore][]  
* [Procedura: Caricare un BLOB in un contenitore][]  
* [Procedura: Elencare i BLOB in un contenitore][]  
* [Procedura: Scaricare BLOB][]  
* [Procedura: Eliminare un BLOB][]  
* [Procedura: Accesso simultaneo][]     
* [Procedura: Usare le firme di accesso condiviso di Azure][]     
* [Passaggi successivi][]

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

##<a name="create-account"></a>Creare un account di archiviazione di Azure
[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="create-app"> </a>Creare un'applicazione Node.js

Creare un'applicazione Node.js vuota. Per istruzioni sulla creazione di un'applicazione Node.js, vedere [Creazione e distribuzione di un'applicazione Node.js in un sito Web di Azure], [Servizio cloud Node.js][Node.js Cloud Service] (usando Windows PowerShell) o [Sito Web con WebMatrix].

## <a name="configure-access"> </a>Configurare l'applicazione per l'accesso all'archiviazione

Per usare l'archiviazione di Azure, è necessario scaricare Azure Storage SDK per Node.js, che comprende un set di pratiche librerie che comunicano con i servizi di archiviazione REST.

### Usare Node Package Manager (NPM) per ottenere il pacchetto

1.  Usare un'interfaccia della riga di comando come **PowerShell** (Windows), **Terminale** (Mac) o **Bash** (Unix) e passare alla cartella in cui è stata creata l'applicazione di esempio.

2.  Digitare **npm install azure-storage** nella finestra di comando, che dovrebbe restituire questo output:

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

Usando il Blocco note o un altro editor di testo, aggiungere quanto segue nella parte superiore del file **server.js** dell'applicazione in cui si intende usare l'archiviazione:

    var azure = require('azure-storage');

## <a name="setup-connection-string"> </a>Configurare una connessione di archiviazione di Azure

Il modulo azure leggerà le variabili di ambiente AZURE\_STORAGE\_ACCOUNT e AZURE\_STORAGE\_ACCESS\_KEY o AZURE\_STORAGE\_CONNECTION\_STRING per ottenere le informazioni necessarie per connettersi all'account di archiviazione di Azure. Se queste variabili di ambiente non sono impostate, è necessario specificare le informazioni relative all'account quando si chiama **createBlobService**.

Per un esempio di impostazione delle variabili di ambiente nel portale di gestione per un sito Web di Azure, vedere [Applicazione Web Node.js con archiviazione].

## <a name="create-container"> </a>Procedura: Creare un contenitore

L'oggetto **BlobService** permette di usare contenitori e BLOB. Esempio
Il codice seguente crea un oggetto **BlobService**. Aggiungere il codice seguente nella
parte superiore di **server.js**:

    var blobSvc = azure.createBlobService();

> [WACOM.NOTE] È possibile accedere a un BLOB in modo anonimo usando **createBlobServiceAnonymous** e specificando l'indirizzo host. Ad esempio, `var blobSvc = azure.createBlobService('https://myblob.blob.core.windows.net/');`.

Tutti i BLOB risiedono in un contenitore. Per creare un nuovo contenitore, usare **createContainerIfNotExists**. L'operazione seguente consente di creare un nuovo contenitore denominato 'mycontainer'

	blobSvc.createContainerIfNotExists('mycontainer', function(error, result, response){
      if(!error){
        // Container exists and allows 
        // anonymous read access to blob 
        // content and metadata within this container
      }
	});

Se il contenitore viene creato, `result` sarà true. Se il contenitore è già presente, `result` sarà false. `response` conterrà informazioni sull'operazione, incluse le informazioni sull'[ETag](http://en.wikipedia.org/wiki/HTTP_ETag) per il contenitore.

###Sicurezza del contenitore

Per impostazione predefinita, i nuovi contenitori sono privati e non è possibile accedervi in modo anonimo. Per rendere pubblico il contenitore perché sia accessibile in modo anonimo, è possibile impostare il livello di accesso del contenitore su **blob** o **container**.

* **blob**: permette l'accesso anonimo in lettura al contenuto e ai metadati del BLOB all'interno del contenitore, ma non ai metadati del contenitore, come l'elenco di tutti i BLOB all'interno di un contenitore. 

* **container**: permette l'accesso anonimo in lettura al contenuto e ai metadati del BLOB, nonché ai metadati del contenitore. 

Ecco un esempio che mostra l'impostazione del livello di accesso su **blob**: 

    blobSvc.createContainerIfNotExists('mycontainer', {publicAccessLevel : 'blob'}, function(error, result, response){
      if(!error){
        // Container exists and is private
      }
	});

In alternativa, è possibile modificare il livello di accesso di un contenitore usando **setContainerAcl** per specificare il livello di accesso. Nell'esempio seguente viene illustrata la modifica del livello di accesso al contenitore:

    blobSvc.setContainerAcl('mycontainer', null, 'container', function(error, result, response){
	  if(!error){
		// Container access level set to 'container'
	  }
	});

Il risultato conterrà informazioni sull'operazione, incluso l'**ETag** corrente per il contenitore.

###Filtri

È possibile applicare operazioni di filtro facoltative alle operazioni eseguite usando **BlobService**. Le operazioni di filtro possono includere registrazione, ripetizione automatica di tentativi e così via. I filtri sono oggetti che implementano un metodo con la firma:

		function handle (requestOptions, next)

Dopo avere eseguito la pre-elaborazione sulle opzioni della richiesta, il metodo deve chiamare "next" passando un callback con la firma seguente:

		function (returnObject, finalCallback, next)

In questo callback, e dopo l'elaborazione del returnObject (la risposta della richiesta al server), il callback deve richiamare "next", se questo esiste, per continuare a elaborare altri filtri oppure semplicemente richiamare finalCallback per concludere la chiamata al servizio.

In Azure SDK per Node.js sono inclusi due filtri che implementano la logica di ripetizione dei tentativi, **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. Questo codice crea un oggetto **BlobService** che usa **ExponentialRetryPolicyFilter**:

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var blobSvc = azure.createBlobService().withFilter(retryOperations);

## <a name="upload-blob"> </a>Procedura: Caricare un BLOB in un contenitore

Un BLOB può essere basato su blocchi o su pagine. I BLOB in blocchi consentono di caricare dati di grandi dimensioni in modo più efficiente, mentre i BLOB di pagine sono ottimizzati per le operazioni in lettura e scrittura. Per altre informazioni, vedere [Informazioni sui Blob in blocchi e sui Blob di pagine](http://msdn.microsoft.com/it-it/library/azure/ee691964.aspx).

###BLOB in blocchi

Per caricare i dati in un BLOB in blocchi, usare le operazioni seguenti:

* **createBlockBlobFromLocalFile**: crea un nuovo BLOB in blocchi e carica il contenuto di un file.

* **createBlockBlobFromStream**: crea un nuovo BLOB in blocchi e carica il contenuto di un flusso.

* **createBlockBlobFromText**: crea un nuovo BLOB in blocchi e carica il contenuto di una stringa.

* **createWriteStreamToBlockBlob**: fornisce un flusso di scrittura a un BLOB in blocchi.

Nell'esempio seguente il contenuto del file **test.txt** viene caricato in **myblob**.

	blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', function(error, result, response){
	  if(!error){
	    // file uploaded
	  }
	});

L'oggetto `result` restituito da questi metodi conterrà informazioni sull'operazione, come l'**ETag** del BLOB.

###BLOB di pagine

Per caricare i dati in un BLOB di pagine, usare le operazioni seguenti:

* **createPageBlob**: crea un nuovo BLOB di pagine con una lunghezza specifica.

* **createPageBlobFromLocalFile**: crea un nuovo BLOB di pagine e carica il contenuto di un file.

* **createPageBlobFromStream**: crea un nuovo BLOB di pagine e carica il contenuto di un flusso.

* **createWriteStreamToExistingPageBlob**: fornisce un flusso di scrittura a un BLOB di pagine esistente.

* **createWriteStreamToNewPageBlob**: crea un nuovo BLOB e quindi fornisce un flusso per la scrittura nel BLOB.

Nell'esempio seguente il contenuto del file **test.txt** viene caricato in **mypageblob**.

	blobSvc.createPageBlobFromLocalFile('mycontainer', 'mypageblob', 'test.txt', function(error, result, response){
	  if(!error){
	    // file uploaded
	  }
	});

> [WACOM.NOTE] I BLOB di pagine sono costituiti da "pagine" da 512 byte. Potrebbe essere visualizzato un errore quando si caricano dati con una dimensione diversa da un multiplo di 512.

## <a name="list-blob"> </a>Procedura: Elencare i BLOB in un contenitore

Per elencare i BLOB in un contenitore, usare il metodo **listBlobsSegmented**. Per fare in modo che vengano restituiti i BLOB con un prefisso specifico, usare **listBlobsSegmentedWithPrefix**.

    blobSvc.listBlobsSegmented('mycontainer', null, function(error, result, response){
      if(!error){
        // result contains the entries
	  }
	});

`result` conterrà una raccolta `entries`, ovvero una matrice di oggetti che descrivono ogni BLOB. Se non possono essere restituiti tutti i BLOB, `result` fornirà anche un oggetto `continuationToken`, che può essere usato come secondo parametro per recuperare entità aggiuntive.

## <a name="download-blob"> </a>Procedura: Scaricare BLOB

Per scaricare i dati da un BLOB, usare le operazioni seguenti:

* **getBlobToFile**: scrive i contenuti del BLOB in un file.

* **getBlobToStream**: scrive i contenuti del BLOB in un flusso.

* **getBlobToText**: scrive i contenuti del BLOB in una stringa. 

* **createReadStream**: fornisce un flusso per la lettura dal BLOB.

Ecco un esempio che mostra l'uso di **getBlobToStream** per scaricare il contenuto del BLOB **myblob** e archiviarlo nel file **output.txt** mediante un flusso:

    var fs=require('fs');
	blobSvc.getBlobToStream('mycontainer', 'myblob', fs.createWriteStream('output.txt'), function(error, result, response){
	  if(!error){
	    // blob retrieved
	  }
	});

`result` conterrà informazioni sul BLOB, incluse le informazioni sull'**ETag**.

## <a name="delete-blob"> </a>Procedura: Eliminare un BLOB

Per eliminare un BLOB, infine, chiamare **deleteBlob**. Nell'esempio seguente viene eliminato il BLOB denominato **myblob**.

    blobSvc.deleteBlob(containerName, 'myblob', function(error, response){
	  if(!error){
		// Blob has been deleted
	  }
	});

##<a name="concurrent-access"></a>Procedura: Accesso simultaneo

Per supportare l'accesso simultaneo a un BLOB da più client o da più istanze di processo, è possibile usare **ETag** o **lease**.

* **Etag**: permette di rilevare se il BLOB o il contenitore è stato modificato da un altro processo.

* **Lease**: permette di ottenere accesso esclusivo e rinnovabile in scrittura o eliminazione a un BLOB per un certo periodo di tempo.

###ETag

È consigliabile usare gli ETag se è necessario consentire a più client o istanze di scrivere simultaneamente nel BLOB. L'ETag consente di determinare se il contenitore o il BLOB è stato modificato dalla data di creazione o dell'ultima lettura per evitare di sovrascrivere le modifiche già sottoposte a commit da un altro client o processo.

È possibile impostare le condizioni degli ETag usando il parametro facoltativo `options.accessConditions`. Nell'esempio seguente il file **test.txt** viene caricato solo se il BLOB è già presente e include il valore di ETag contenuto in `etagToMatch`.

	blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', { accessConditions: { 'if-match': etagToMatch} }, function(error, result, response){
      if(!error){
	    // file uploaded
	  }
	});

Il modello generale per l'uso degli ETag è il seguente:

1. Ottenere l'ETag in seguito a un'operazione create, list o get.

2. Eseguire un'azione verificando che il valore ETag non sia stato modificato.

Se il valore è stato modificato, significa che un altro client o un'altra istanza ha modificato il BLOB o il contenitore in un momento successivo a quello in cui è stato ottenuto il valore ETag.

###Lease

È possibile acquisire un nuovo lease usando il metodo **acquireLease** e specificando il BLOB o il contenitore per cui si vuole ottenere un lease. Ad esempio, questa operazione acquisisce un lease su **myblob**.

	blobSvc.acquireLease('mycontainer', 'myblob', function(error, result, response){
	  if(!error) {
	    console.log(result);
	  }
	});

Le operazioni successive su **myblob** devono specificare il parametro `options.leaseId`. L'ID lease viene restituito come `result.id` da **acquireLease**.

> [WACOM.NOTE] Per impostazione predefinita, la durata del lease è infinita. Per definire una durata non infinita (compresa tra 15 e 60 secondi), specificare `options.leaseDuration`.

Per rimuovere un lease, usare **releaseLease**. Per interrompere un lease impedendo ad altri di ottenere un nuovo lease fino allo scadere della durata originale, usare **breakLease**.

## <a name="sas"></a>Procedura: Usare le firme di accesso condiviso di Azure

Le firme di accesso condiviso rappresentano un modo sicuro per fornire accesso granulare a BLOB e contenitori senza specificare il nome o le chiavi dell'account di archiviazione. Le firme di accesso condiviso vengono spesso usate per fornire accesso limitato ai dati, ad esempio per consentire a un'app per dispositivi mobili di accedere ai BLOB.

> [WACOM.NOTE] Benché sia anche possibile consentire l'accesso anonimo ai BLOB, le firme di accesso condiviso garantiscono un accesso più controllato, in quanto devono essere generate.

Un'applicazione attendibile, come un servizio basato sul cloud, genera una firma di accesso condiviso tramite il metodo **generateSharedAccessSignature** dell'oggetto **BlobService** e la fornisce a un'applicazione non attendibile o parzialmente attendibile. Si prenda ad esempio un'app per dispositivi mobili. La firma di accesso condiviso viene generata tramite un criterio che indica le date di inizio e di fine del periodo di validità della firma, nonché il livello di accesso concesso al titolare della firma di accesso condiviso.

Nell'esempio seguente vengono generati nuovi criteri di accesso condiviso che permetteranno al proprietario della firma di accesso condiviso di eseguire operazioni di lettura sul BLOB **myblob** e che scadranno 100 minuti dopo la data di creazione.

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

Si noti che è necessario fornire anche le informazioni sull'host, in quanto sono necessarie quando il proprietario della firma di accesso condiviso tenta di accedere al contenitore.

L'applicazione client usa quindi la firma di accesso condiviso con **BlobServiceWithSAS** per eseguire operazioni sul BLOB. L'operazione seguente recupera informazioni su **myblob**.

	var sharedBlobSvc = azure.createBlobServiceWithSas(host, blobSAS);
	sharedBlobSvc.getBlobProperties('mycontainer', 'myblob', function (error, result, response) {
	  if(!error) {
	    // retrieved info
	  }
	});

Poiché la firma di accesso condiviso è stata generata con accesso di sola lettura, se si tentasse di modificare il BLOB verrebbe restituito un errore.

###Elenchi di controllo di accesso

Per impostare i criteri di accesso per una firma di accesso condiviso, è anche possibile usare un elenco di controllo di accesso. Questa soluzione è utile quando si vuole consentire a più client di accedere a un contenitore, impostando tuttavia criteri di accesso diversi per ogni client.

Un elenco di controllo di accesso viene implementato usando una matrice di criteri di accesso, con un ID associato a ogni criterio. In questo esempio vengono definiti due criteri, uno per 'user1' e uno per 'user2':

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

Dopo avere impostato l'elenco di controllo di accesso, è possibile creare una firma di accesso condiviso basata sull'ID di un criterio. Nell'esempio seguente viene creata una nuova firma di accesso condiviso per 'user2':

	blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', { Id: 'user2' });

## <a name="next-steps"> </a>Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base dell'archiviazione BLOB, è possibile usare i collegamenti seguenti
per informazioni su come eseguire attività di archiviazione più complesse.

-   Vedere le informazioni di riferimento in MSDN: [Archiviazione e accesso ai dati in Azure][].
-   Visitare il [blog del team del servizio Archiviazione di Azure][].
-   Visitare il repository per [Azure Storage SDK per Node.js][] su GitHub.

  [Azure Storage SDK per Node.js]: https://github.com/Azure/azure-storage-node
  [Passaggi successivi]: #next-steps
  [Informazioni sul servizio Blob]: #what-is
  [Concetti]: #concepts
  [Creare un account di archiviazione di Azure]: #create-account
  [Creare un'applicazione Node.js]: #create-app
  [Configurare l'applicazione per l'accesso all'archiviazione]: #configure-access
  [Configurare una stringa di connessione di archiviazione di Azure]: #setup-connection-string
  [Procedura: Creare un contenitore]: #create-container
  [Procedura: Caricare un BLOB in un contenitore]: #upload-blob
  [Procedura: Elencare i BLOB in un contenitore]: #list-blob
  [Procedura: Scaricare BLOB]: #download-blobs
  [Procedura: Eliminare un BLOB]: #delete-blobs
  [Procedura: Accesso simultaneo]: #concurrent-access
  [Procedura: Usare le firme di accesso condiviso di Azure]: #sas
[Creazione e distribuzione di un'applicazione Node.js in un Sito Web di Azure]: /it-it/develop/nodejs/tutorials/create-a-website-(mac)/
  [Servizio cloud Node.js con archiviazione]: /it-it/documentation/articles/storage-nodejs-use-table-storage-cloud-service-app/
  [Applicazione Web Node.js con archiviazione]: /it-it/documentation/articles/storage-nodejs-use-table-storage-web-site/
 [Sito Web con WebMatrix]: /it-it/documentation/articles/web-sites-nodejs-use-webmatrix/
  [Uso dell'API REST]: http://msdn.microsoft.com/it-it/library/windowsazure/hh264518.aspx
  [Portale di gestione di Azure]: http://manage.windowsazure.com
  [Servizio cloud Node.js]: /it-it/documentation/articles/cloud-services-nodejs-develop-deploy-app/
  [Archiviazione e accesso ai dati in Azure]: http://msdn.microsoft.com/it-it/library/windowsazure/gg433040.aspx
  [Blog del team di Archiviazione di Azure]: http://blogs.msdn.com/b/windowsazurestorage/

<!--HONumber=35.1-->
