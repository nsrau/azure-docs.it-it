---
title: Come usare l&quot;archiviazione BLOB da Node.js | Microsoft Docs
description: Archiviare i dati non strutturati nel cloud con l&quot;archivio BLOB (archivio di oggetti) di Azure.
services: storage
documentationcenter: nodejs
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 8b0df222-1ca8-4967-8248-6d6d720947b8
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 12/08/2016
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: 931503f56b32ce9d1b11283dff7224d7e2f015ae
ms.openlocfilehash: 15485bc6032fb12b18cdac171ec5d6e6c617f4e7


---
# <a name="how-to-use-blob-storage-from-nodejs"></a>Come usare l'archiviazione BLOB da Node.js
[!INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Panoramica
Questo articolo illustra scenari comuni relativi all'uso dell'archiviazione BLOB. Gli esempi sono scritti usando l'API Node.js. Gli scenari presentati illustrano come caricare, elencare, scaricare ed eliminare i BLOB.

[!INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Creare un'applicazione Node.js
Per istruzioni su come creare un'applicazione Node.js, vedere [Creare un'app Web Node.js nel servizio app di Azure], [Creazione e distribuzione di un'applicazione Node.js a un servizio cloud di Azure] -- con Windows PowerShell, o [Creazione e distribuzione di un sito Web Node.js in Azure con WebMatrix].

## <a name="configure-your-application-to-access-storage"></a>Configurare l'applicazione per l'accesso all'archiviazione
Per usare Archiviazione di Azure, è necessario disporre di Azure Storage SDK per Node.js, che comprende un set di pratiche librerie che comunicano con i servizi di archiviazione REST.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Usare Node Package Manager (NPM) per ottenere il pacchetto
1. Usare un'interfaccia della riga di comando come **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Unix) per spostarsi nella cartella in cui è stata creata l'applicazione di esempio.
2. Digitare **npm install azure-storage** nella finestra di comando. L'output da questo comando sarà simile all'esempio di codice seguente.

  azure-storage@0.5.0 node_modules\azure-storage +-- extend@1.2.1 +-- xmlbuilder@0.4.3 +-- mime@1.2.11 +-- node-uuid@1.4.3 +-- validator@3.22.2 +-- underscore@1.4.4 +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1) +-- xml2js@0.2.7 (sax@0.5.2) +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)

3. È possibile eseguire manualmente il comando **ls** per verificare che sia stata creata una cartella **node\_modules**. All'interno di tale cartella trovare il pacchetto **azure-storage** , che contiene le librerie necessarie per accedere all'archiviazione.

### <a name="import-the-package"></a>Importare il pacchetto
Usando il Blocco note o un altro editor di testo, aggiungere quanto segue alla parte superiore del file **server.js** dell'applicazione in cui si intende usare l'archiviazione:

```nodejs
var azure = require('azure-storage');
```

## <a name="set-up-an-azure-storage-connection"></a>Configurare una connessione di archiviazione di Azure
Il modulo di Azure leggerà le variabili di ambiente `AZURE_STORAGE_ACCOUNT` e `AZURE_STORAGE_ACCESS_KEY` o `AZURE_STORAGE_CONNECTION_STRING` per ottenere le informazioni necessarie per la connessione all'account di archiviazione di Azure. Se queste variabili di ambiente non sono impostate, sarà necessario specificare le informazioni relative all'account quando si chiama **createBlobService**.

Per un esempio di impostazione delle variabili di ambiente nel [portale di Azure](https://portal.azure.com) per un'app Web di Azure, vedere [App Web Node.js con il servizio tabelle di Azure].

## <a name="create-a-container"></a>Creare un contenitore
L'oggetto **BlobService** consente di lavorare con contenitori e BLOB. Il codice seguente consente di creare un oggetto **BlobService** . Aggiungere il codice seguente nella parte superiore di **server.js**:

```nodejs
var blobSvc = azure.createBlobService();
```

> [!NOTE]
> È possibile accedere a un BLOB in modo anonimo usando **createBlobServiceAnonymous** e specificando l'indirizzo host. Ad esempio, usare `var blobSvc = azure.createBlobServiceAnonymous('https://myblob.blob.core.windows.net/');`.
>
>

[!INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

Per creare un nuovo contenitore, usare **createContainerIfNotExists**. L'esempio di codice seguente crea un nuovo contenitore denominato "mycontainer":

```nodejs
blobSvc.createContainerIfNotExists('mycontainer', function(error, result, response){
    if(!error){
      // Container exists and is private
    }
});
```

Se il contenitore viene creato, `result.created` è true. Se il contenitore esiste già, `result.created` è false. `response` contiene informazioni sull'operazione, incluse le informazioni sull'ETag per il contenitore.

### <a name="container-security"></a>Sicurezza del contenitore
Per impostazione predefinita, i nuovi contenitori sono privati e non è possibile accedervi in modo anonimo. Per rendere pubblico il contenitore affinché sia accessibile in modo anonimo, è possibile impostarne il livello di accesso su **blob** o **container**.

* **blob** consente l'accesso anonimo in lettura al contenuto e ai metadati del BLOB all'interno di quel contenitore, ma non ai metadati del contenitore, ad esempio l'elenco di tutti i BLOB all'interno di un contenitore.
* **container** consente l'accesso anonimo in lettura al contenuto e ai metadati del BLOB, nonché ai metadati del contenitore.

L'esempio di codice seguente illustra l'impostazione del livello di accesso su **blob**:

```nodejs
blobSvc.createContainerIfNotExists('mycontainer', {publicAccessLevel : 'blob'}, function(error, result, response){
    if(!error){
      // Container exists and allows
      // anonymous read access to blob
      // content and metadata within this container
    }
});
```

In alternativa, è possibile modificare il livello di accesso di un contenitore usando **setContainerAcl** per specificare il livello di accesso. L'esempio di codice seguente illustra la modifica del livello di accesso al contenitore:

```nodejs
blobSvc.setContainerAcl('mycontainer', null /* signedIdentifiers */, {publicAccessLevel : 'container'} /* publicAccessLevel*/, function(error, result, response){
  if(!error){
    // Container access level set to 'container'
  }
});
```

Il risultato contiene informazioni sull'operazione, incluso l' **ETag** corrente per il contenitore.

### <a name="filters"></a>Filtri
È possibile applicare operazioni di filtro facoltative alle operazioni eseguite usando **BlobService**. Le operazioni di filtro possono includere la registrazione, la ripetizione automatica dei tentativi e così via. I filtri sono oggetti che implementano un metodo con la firma:

```nodejs
function handle (requestOptions, next)
```

Dopo aver eseguito la pre-elaborazione sulle opzioni della richiesta, il metodo deve chiamare "next" passando un callback con la firma seguente:

```nodejs
function (returnObject, finalCallback, next)
```

In questo callback, e dopo l'elaborazione del returnObject (la risposta della richiesta al server), il callback deve richiamare "next", se questo esiste, per continuare a elaborare altri filtri oppure semplicemente richiamare finalCallback per concludere la chiamata al servizio.

In Azure SDK per Node.js sono inclusi due filtri che implementano la logica di ripetizione dei tentativi. Sono **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. Il codice seguente consente di creare un oggetto **BlobService** che usa **ExponentialRetryPolicyFilter**:

```nodejs
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var blobSvc = azure.createBlobService().withFilter(retryOperations);
```

## <a name="upload-a-blob-into-a-container"></a>Caricare un BLOB in un contenitore
Esistono tre tipi di BLOB: BLOB in blocchi, BLOB di pagine e BLOB di accodamento. I BLOB in blocchi consentono di caricare in modo più efficiente dati di grandi dimensioni. I BLOB di accodamento sono ottimizzati per le operazioni di accodamento. I BLOB di pagine sono ottimizzati per le operazioni di lettura/scrittura. Per altre informazioni, vedere [Informazioni sui BLOB in blocchi, sui BLOB di aggiunta e sui BLOB di pagine](http://msdn.microsoft.com/library/azure/ee691964.aspx).

### <a name="block-blobs"></a>BLOB in blocchi
Per caricare i dati in un BLOB in blocchi, usare le operazioni seguenti:

* **createBlockBlobFromLocalFile** : crea un nuovo BLOB in blocchi e carica il contenuto di un file
* **createBlockBlobFromStream** : crea un nuovo BLOB in blocchi e carica il contenuto di un flusso
* **createBlockBlobFromText** : crea un nuovo BLOB in blocchi e carica il contenuti di una stringa
* **createWriteStreamToBlockBlob** : fornisce un flusso di scrittura a un BLOB in blocchi

L'esempio di codice seguente carica il contenuto del file **test.txt** nel BLOB **myblob**.

```nodejs
blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', function(error, result, response){
  if(!error){
    // file uploaded
  }
});
```

L'oggetto `result` restituito da questi metodi contiene informazioni sull'operazione, ad esempio l' **ETag** del BLOB.

### <a name="append-blobs"></a>BLOB di accodamento
Per caricare i dati in un nuovo BLOB di accodamento, usare le API seguenti:

* **createAppendBlobFromLocalFile** : crea un nuovo BLOB di accodamento e carica i contenuti di un file
* **createAppendBlobFromStream** : crea un nuovo BLOB di accodamento e carica i contenuti di un flusso
* **createAppendBlobFromText** : crea un nuovo BLOB di accodamento e carica i contenuti di una stringa
* **createWriteStreamToNewAppendBlob** : crea un nuovo BLOB di accodamento e quindi fornisce un flusso per la scrittura nel BLOB stesso

L'esempio di codice seguente carica il contenuto del file **test.txt** nel BLOB **myappendblob**.

```nodejs
blobSvc.createAppendBlobFromLocalFile('mycontainer', 'myappendblob', 'test.txt', function(error, result, response){
  if(!error){
    // file uploaded
  }
});
```

Per accodare un blocco a un blob di accodamento esistente, usare quanto segue:

* **appendFromLocalFile** : consente di accodare i contenuti di un file a un BLOB di accodamento esistente
* **appendFromStream** : consente di accodare i contenuti di un flusso a un BLOB di accodamento esistente
* **appendFromText** : consente di accodare i contenuti di una stringa a un BLOB di accodamento esistente
* **appendBlockFromStream** : consente di accodare i contenuti di un flusso a un BLOB di accodamento esistente
* **appendBlockFromText** : consente di accodare i contenuti di una stringa a un BLOB di accodamento esistente

> [!NOTE]
> Le API appendFromXXX consentono di eseguire rapidamente alcune convalide sul lato client per evitare chiamate al server non necessarie, contrariamente alle API appendBlockFromXXX.
>
>

L'esempio di codice seguente carica il contenuto del file **test.txt** nel BLOB **myappendblob**.

```nodejs
blobSvc.appendFromText('mycontainer', 'myappendblob', 'text to be appended', function(error, result, response){
  if(!error){
    // text appended
  }
});
```

### <a name="page-blobs"></a>BLOB di pagine
Per caricare i dati in un BLOB di pagine, usare le operazioni seguenti:

* **createPageBlob** : crea un nuovo BLOB di pagine con una lunghezza specifica
* **createPageBlobFromLocalFile** : crea un nuovo BLOB di pagine e carica i contenuti di un file
* **createPageBlobFromStream** : crea un nuovo BLOB di pagine e carica i contenuti di un flusso
* **createWriteStreamToExistingPageBlob** : fornisce un flusso di scrittura a un BLOB di pagine esistente
* **createWriteStreamToNewPageBlob** : crea un nuovo BLOB di pagine e quindi fornisce un flusso per la scrittura nel BLOB stesso

L'esempio di codice seguente carica il contenuto del file **test.txt** nel BLOB **mypageblob**.

```nodejs
blobSvc.createPageBlobFromLocalFile('mycontainer', 'mypageblob', 'test.txt', function(error, result, response){
  if(!error){
    // file uploaded
  }
});
```

> [!NOTE]
> I BLOB di pagine sono costituiti da "pagine" da 512 byte. Quando si caricano dati con una dimensione che non corrisponde a un multiplo di 512, viene visualizzato un errore.
>
>

## <a name="list-the-blobs-in-a-container"></a>Elencare i BLOB in un contenitore
Per elencare i BLOB all'interno di un contenitore, usare il metodo **listBlobsSegmented** . Per fare in modo che vengano restituiti i BLOB con un prefisso specifico, usare il metodo **listBlobsSegmentedWithPrefix**.

```nodejs
blobSvc.listBlobsSegmented('mycontainer', null, function(error, result, response){
  if(!error){
      // result.entries contains the entries
      // If not all blobs were returned, result.continuationToken has the continuation token.
  }
});
```

`result` contiene una raccolta di `entries`, ovvero una matrice di oggetti che descrivono ogni BLOB. Se non vengono restituiti tutti i BLOB, `result` fornisce anche un `continuationToken`, che può essere usato come secondo parametro per recuperare voci aggiuntive.

## <a name="download-blobs"></a>Scaricare BLOB
Per scaricare i dati da un BLOB, usare le operazioni seguenti:

* **getBlobToLocalFile** - scrive i contenuti del BLOB in un file.
* **getBlobToStream** : scrive il contenuto del BLOB in un flusso
* **getBlobToText** : scrive il contenuto del BLOB in una stringa
* **createReadStream** : fornisce un flusso per la lettura dal BLOB.

L'esempio di codice seguente illustra l'uso di **getBlobToStream** per scaricare il contenuto del BLOB **myblob** e archiviarlo nel file **output.txt** usando un flusso:

```nodejs
var fs = require('fs');
blobSvc.getBlobToStream('mycontainer', 'myblob', fs.createWriteStream('output.txt'), function(error, result, response){
  if(!error){
    // blob retrieved
  }
});
```

`result` contiene informazioni sul BLOB, incluse le informazioni sull' **ETag** .

## <a name="delete-a-blob"></a>Eliminare un BLOB
Per eliminare un BLOB, infine, chiamare **deleteBlob**. L'esempio di codice seguente elimina il BLOB denominato **myblob**.

```nodejs
blobSvc.deleteBlob(containerName, 'myblob', function(error, response){
  if(!error){
    // Blob has been deleted
  }
});
```

## <a name="concurrent-access"></a>Accesso simultaneo
Per supportare l'accesso simultaneo a un BLOB da più client o da più istanze di processo, è possibile usare gli **ETag** o **lease**.

* **Etag** : fornisce un modo per rilevare le eventuali modifiche apportate al BLOB o contenitore da un altro processo
* **Lease** : consente di ottenere accesso esclusivo, rinnovabile, in scrittura o eliminazione a un BLOB per un periodo di tempo

### <a name="etag"></a>ETag
Usare gli ETag se è necessario consentire a più client o istanze di scrivere simultaneamente nel BLOB in blocchi o nel BLOB di pagine. L'ETag consente di determinare se il contenitore o il BLOB è stato modificato dalla data di creazione o dell'ultima lettura per evitare di sovrascrivere le modifiche già sottoposte a commit da un altro client o processo.

È possibile impostare le condizioni degli ETag usando il parametro `options.accessConditions` facoltativo. L'esempio di codice seguente carica il file **test.txt** solo se il BLOB è già presente e include il valore di ETag contenuto in `etagToMatch`.

```nodejs
blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', { accessConditions: { EtagMatch: etagToMatch} }, function(error, result, response){
    if(!error){
    // file uploaded
  }
});
```

Il modello generale per l'uso degli ETag è il seguente:

1. Ottenere l'ETag in seguito a un'operazione create, list o get.
2. Eseguire un'azione verificando che il valore ETag non sia stato modificato.

Se il valore è stato modificato, significa che un altro client o un'altra istanza ha modificato il BLOB o il contenitore in un momento successivo a quello in cui è stato ottenuto il valore ETag.

### <a name="lease"></a>Lease
È possibile acquisire un nuovo lease usando il metodo **acquireLease** e specificando il BLOB o il contenitore per il quale si vuole ottenere il lease. Il codice seguente ad esempio acquisisce un lease sul BLOB **myblob**.

```nodejs
blobSvc.acquireLease('mycontainer', 'myblob', function(error, result, response){
  if(!error) {
    console.log('leaseId: ' + result.id);
  }
});
```

Le operazioni successive sul BLOB **myblob** devono fornire il parametro `options.leaseId`. L'ID del lease viene restituito come `result.id` da **acquireLease**.

> [!NOTE]
> Per impostazione predefinita, la durata del lease è infinita. Per definire una durata non infinita (compresa tra 15 e 60 secondi), specificare il parametro `options.leaseDuration` .
>
>

Per rimuovere un lease, usare il metodo **releaseLease**. Per interrompere un lease e impedire ad altri di ottenere un nuovo lease fintanto che la durata originale non scade, usare il metodo **breakLease**.

## <a name="work-with-shared-access-signatures"></a>Usare le firme di accesso condiviso di Azure
Le firme di accesso condiviso rappresentano un modo sicuro per fornire accesso granulare a BLOB e contenitori senza specificare il nome o le chiavi dell'account di archiviazione. Tali firme vengono spesso usate per fornire accesso limitato ai dati, ad esempio per consentire a un'app per dispositivi mobili di accedere ai BLOB.

> [!NOTE]
> Benché sia anche possibile consentire l'accesso anonimo ai BLOB, le firme di accesso condiviso garantiscono un accesso più controllato, in quanto devono essere generate.
>
>

Un'applicazione attendibile, ad esempio un servizio basato sul cloud, genera una firma di accesso condiviso tramite il metodo **generateSharedAccessSignature** dell'oggetto **BlobService** e la fornisce a un'applicazione non attendibile o parzialmente attendibile, ad esempio a un'app per dispositivi mobili. La firma di accesso condiviso viene generata tramite un criterio che indica le date di inizio e di fine del periodo di validità della firma, nonché il livello di accesso concesso al titolare della firma.

L'esempio di codice seguente genera un nuovo criterio di accesso condiviso che consente al titolare della firma di accesso condiviso di eseguire operazioni di lettura nel BLOB **myblob** e che scadrà 100 minuti dopo la data di creazione.

```nodejs
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
```

Si noti che devono essere fornite anche le informazioni sull'host, in quanto sono necessarie quando il titolare della firma di accesso condiviso tenta di accedere al contenitore.

L'applicazione client usa quindi la firma di accesso condiviso con **BlobServiceWithSAS** per eseguire operazioni sul BLOB. L'operazione seguente consente di recuperare informazioni sul BLOB **myblob**.

```nodejs
var sharedBlobSvc = azure.createBlobServiceWithSas(host, blobSAS);
sharedBlobSvc.getBlobProperties('mycontainer', 'myblob', function (error, result, response) {
  if(!error) {
    // retrieved info
  }
});
```

Poiché la firma di accesso condiviso è stata generata con l'accesso in sola lettura, verrà restituito un errore se viene eseguito un tentativo di modificare il BLOB.

### <a name="access-control-lists"></a>Elenchi di controllo di accesso
Per impostare i criteri di accesso per una firma di accesso condiviso, è anche possibile usare un elenco di controllo di accesso. Questa soluzione è utile quando si desidera consentire a più client di accedere a un contenitore, impostando tuttavia criteri di accesso diversi per ogni client.

Un elenco di controllo di accesso viene implementato usando una matrice di criteri di accesso, con un ID associato a ogni criterio. L'esempio di codice seguente definisce due criteri, uno per 'user1' e uno per 'user2':

```nodejs
var sharedAccessPolicy = {
  user1: {
    Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
    Start: startDate,
    Expiry: expiryDate
  },
  user2: {
    Permissions: azure.BlobUtilities.SharedAccessPermissions.WRITE,
    Start: startDate,
    Expiry: expiryDate
  }
};
```

L'esempio di codice seguente recupera l'elenco di controllo di accesso corrente per **mycontainer** e quindi aggiunge i nuovi criteri tramite **setBlobAcl**. Risultato:

```nodejs
var extend = require('extend');
blobSvc.getBlobAcl('mycontainer', function(error, result, response) {
  if(!error){
    var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
    blobSvc.setBlobAcl('mycontainer', newSignedIdentifiers, function(error, result, response){
      if(!error){
        // ACL set
      }
    });
  }
});
```

Dopo aver impostato l'elenco di controllo di accesso, è possibile creare una firma di accesso condiviso in base all'ID di un criterio. L'esempio di codice seguente crea una nuova firma di accesso condiviso per 'user2':

```nodejs
blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', { Id: 'user2' });
```

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, vedere le risorse seguenti:

* [Riferimento per le API di Azure Storage SDK per Node][Azure Storage SDK for Node API Reference]
* [Blog del team di Archiviazione di Azure][Azure Storage Team Blog]
* Repository di [Azure Storage SDK per Node][Azure Storage SDK for Node] su GitHub
* [Centro per sviluppatori di Node. js](/develop/nodejs/)
* [Trasferire dati con l'utilità della riga di comando AzCopy](storage-use-azcopy.md)

[Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node

[Creare un'app Web Node.js nel servizio app di Azure]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
[Node.js Cloud Service with Storage]: ../cloud-services/storage-nodejs-use-table-storage-cloud-service-app.md
[App Web Node.js con il servizio tabelle di Azure]: ../app-service-web/storage-nodejs-use-table-storage-web-site.md
[Creazione e distribuzione di un sito Web Node.js in Azure con WebMatrix]: ../app-service-web/web-sites-nodejs-use-webmatrix.md
[Using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx
[Azure portal]: https://portal.azure.com
[Creazione e distribuzione di un'applicazione Node.js a un servizio cloud di Azure]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
[Azure Storage SDK for Node API Reference]: http://dl.windowsazure.com/nodestoragedocs/index.html



<!--HONumber=Dec16_HO2-->


