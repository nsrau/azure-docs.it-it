---
title: 'Guida introduttiva: Libreria di Archiviazione BLOB di Azure v12 - JavaScript'
description: In questa guida di avvio rapido verrà illustrato come usare la libreria client di Archiviazione BLOB di Azure versione 12 per JavaScript per creare un contenitore e un BLOB nell'archiviazione (oggetto) BLOB. Verrà successivamente illustrato come scaricare il BLOB nel computer locale e come elencare tutti i BLOB in un contenitore.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/05/2019
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 2e5a5f2a4de4e01d2e4fa66f819e55839959afd0
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2019
ms.locfileid: "74130703"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-javascript"></a>Guida introduttiva: Libreria client di Archiviazione BLOB di Azure v12 per JavaScript

Iniziare a usare la libreria client di Archiviazione BLOB di Azure v12 per JavaScript. L'archivio BLOB di Azure è la soluzione di archiviazione di oggetti Microsoft per il cloud. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base. L'archiviazione BLOB è ottimizzata per archiviare enormi quantità di dati non strutturati.

> [!NOTE]
> Per iniziare a usare la versione precedente dell'SDK, vedere [Avvio rapido: Libreria client di Archiviazione BLOB di Azure per JavaScript](storage-quickstart-blobs-nodejs-v10.md).

Libreria client di Archiviazione BLOB di Azure v12 per JavaScript:

* Creare un contenitore
* Caricare un oggetto BLOB in Archiviazione di Azure
* Elencare tutti i BLOB in un contenitore
* Scaricare il BLOB nel computer locale
* Eliminare un contenitore

[Documentazione di riferimento delle API](/javascript/api/@azure/storage-blob) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob) | [Pacchetto (Gestione pacchetti del nodo)](https://www.npmjs.com/package/@azure/storage-blob/v/12.0.0) | [Esempi](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
* Account di archiviazione di Azure: [creare un account di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Versione aggiornata di [Node.js](https://nodejs.org/en/download/) per il sistema operativo in uso.

## <a name="setting-up"></a>Configurazione

Questa sezione illustra come preparare un progetto da usare con la libreria client di Archiviazione BLOB di Azure v12 per JavaScript.

### <a name="create-the-project"></a>Creare il progetto

Creare un'applicazione JavaScript denominata *blob-quickstart-v12*.

1. In una finestra della console, ad esempio cmd, PowerShell o Bash, creare una nuova directory per il progetto.

    ```console
    mkdir blob-quickstart-v12
    ```

1. Passare alla directory *blob-quickstart-v12* appena creata.

    ```console
    cd blob-quickstart-v12
    ```

1. Creare un nuovo file di testo denominato *package.json*. Questo file definisce il progetto node.js. Salvare il file nella directory *blob-quickstart-v12*. Di seguito è riportato il contenuto del file:

    ```json
    {
        "name": "blob-quickstart-v12",
        "version": "1.0.0",
        "description": "Use the @azure/storage-blob SDK version 12 to interact with Azure Blob storage",
        "main": "blob-quickstart-v12.js",
        "scripts": {
            "start": "node blob-quickstart-v12.js"
        },
        "author": "Your Name",
        "license": "MIT",
        "dependencies": {
            "@azure/storage-blob": "^12.0.0",
            "@types/dotenv": "^4.0.3",
            "dotenv": "^6.0.0"
        }
    }
    ```
    
    Se si vuole, è possibile inserire il proprio nome in per il campo `author`.
   
### <a name="install-the-package"></a>Installare il pacchetto

Sempre nella directory *blob-quickstart-v12*, installare la libreria client di Archiviazione BLOB di Azure per il pacchetto JavaScript usando il comando `npm install`. Questo comando legge il file *package.json* e installa la libreria client di Archiviazione BLOB di Azure v12 per il pacchetto JavaScript e tutte le librerie da cui dipende.

```console
npm install
```

### <a name="set-up-the-app-framework"></a>Configurare il framework dell'app

Dalla directory del progetto:

1. Aprire un altro nuovo file di testo nell'editor del codice
1. Aggiungere chiamate `require` per caricare i moduli di Azure e node.js
1. Creare la struttura per il programma, inclusa la gestione delle eccezioni di base

    Ecco il codice:

    ```javascript
    const { BlobServiceClient } = require('@azure/storage-blob');
    const uuidv1 = require('uuid/v1');
    
    async function main() {
        console.log('Azure Blob storage v12 - Javascript quickstart sample');
        // Quick start code goes here
    }
    
    main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));
    ```

1. Salvare il nuovo file come *blob-quickstart-v12.js* nella directory *blob-quickstart-v12*.

### <a name="copy-your-credentials-from-the-azure-portal"></a>Copiare le credenziali dal portale di Azure

Le richieste effettuate dall'applicazione di esempio ad Archiviazione di Azure devono essere autorizzate. Per autorizzare una richiesta, aggiungere all'applicazione le credenziali dell'account di archiviazione sotto forma di stringa di connessione. Visualizzare le credenziali dell'account di archiviazione seguendo questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Individuare l'account di archiviazione.
3. Nella sezione **Impostazioni** della panoramica dell'account di archiviazione selezionare **Chiavi di accesso**. A questo punto è possibile visualizzare le chiavi di accesso dell'account e la stringa di connessione completa per ogni chiave.
4. Trovare il valore **Stringa di connessione** in **key1** e selezionare il pulsante **Copia** per copiare la stringa di connessione. Il valore della stringa di connessione verrà aggiunto a una variabile di ambiente nel passaggio successivo.

    ![Screenshot che mostra come copiare una stringa di connessione dal portale di Azure](../../../includes/media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>Configurare la stringa di connessione di archiviazione

Dopo aver copiato la stringa di connessione, scriverla in una nuova variabile di ambiente nel computer locale che esegue l'applicazione. Per impostare la variabile di ambiente, aprire una finestra della console e seguire le istruzioni per il sistema operativo specifico. Sostituire `<yourconnectionstring>` con la stringa di connessione effettiva.

#### <a name="windows"></a>Windows

```cmd
setx CONNECT_STR "<yourconnectionstring>"
```

Dopo aver aggiunto la variabile di ambiente in Windows, è necessario avviare una nuova istanza della finestra di comando.

#### <a name="linux"></a>Linux

```bash
export CONNECT_STR="<yourconnectionstring>"
```

#### <a name="macos"></a>macOS

```bash
export CONNECT_STR="<yourconnectionstring>"
```

#### <a name="restart-programs"></a>Riavviare i programmi

Dopo aver aggiunto la variabile di ambiente, riavviare gli eventuali programmi in esecuzione che dovranno leggerla. Ad esempio, riavviare l'ambiente di sviluppo o l'editor prima di continuare.

## <a name="object-model"></a>Modello a oggetti

Archiviazione BLOB è ottimizzata per archiviare enormi quantità di dati non strutturati. I dati non strutturati sono dati che non seguono una definizione o un modello di dati specifico, ad esempio dati di testo o binari. L’archiviazione BLOB offre tre tipi di risorse:

* L'account di archiviazione
* Un contenitore nell'account di archiviazione
* Un oggetto BLOB in un contenitore

Il diagramma seguente mostra la relazione tra queste risorse.

![Diagramma dell'architettura dell'archivio BLOB](./media/storage-blob-introduction/blob1.png)

Per interagire con queste risorse, usare le classi JavaScript seguenti:

* [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient): la classe `BlobServiceClient` consente di modificare le risorse di Archiviazione di Azure e i contenitori BLOB.
* [ContainerClient](/javascript/api/@azure/storage-blob/containerclient): la classe `ContainerClient` consente di modificare i contenitori di Archiviazione di Azure e i relativi oggetti BLOB.
* [BlobClient](/javascript/api/@azure/storage-blob/blobclient): la classe `BlobClient` consente di modificare gli oggetti BLOB di Archiviazione di Azure.

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice di esempio illustrano come eseguire queste operazioni con la libreria client di Archiviazione BLOB di Azure per JavaScript:

* [Ottenere la stringa di connessione](#get-the-connection-string)
* [Creare un contenitore](#create-a-container)
* [Caricare BLOB in un contenitore](#upload-blobs-to-a-container)
* [Elencare i BLOB in un contenitore](#list-the-blobs-in-a-container)
* [Scaricare BLOB](#download-blobs)
* [Eliminare un contenitore](#delete-a-container)

### <a name="get-the-connection-string"></a>Ottenere la stringa di connessione

Il codice seguente recupera la stringa di connessione per l'account di archiviazione dalla variabile di ambiente creata nella sezione [Configurare la stringa di connessione di archiviazione](#configure-your-storage-connection-string).

Aggiungere questo codice all'interno della funzione `main`:

```javascript
// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called CONNECT_STR. If the environment variable is
// created after the application is launched in a console or with Visual Studio,
// the shell or application needs to be closed and reloaded to take the
// environment variable into account.
const CONNECT_STR = process.env.CONNECT_STR;
```

### <a name="create-a-container"></a>Creare un contenitore

Decidere un nome per il nuovo contenitore. Il codice seguente aggiunge un valore UUID al nome del contenitore per garantirne l'univocità.

> [!IMPORTANT]
> I nomi dei contenitori devono essere in minuscolo. Per altre informazioni sulla denominazione di contenitori e BLOB, vedere [Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Assegnazione di nome e riferimento a contenitori, BLOB e metadati).

Creare un'istanza della classe [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient) chiamando il metodo [fromConnectionString](/javascript/api/@azure/storage-blob/blobserviceclient#fromconnectionstring-string--storagepipelineoptions-). Chiamare quindi il metodo [getContainerClient](/javascript/api/@azure/storage-blob/blobserviceclient#getcontainerclient-string-) per ottenere un riferimento a un contenitore. Infine, chiamare [create](/javascript/api/@azure/storage-blob/containerclient#create-containercreateoptions-) per creare effettivamente il contenitore nell'account di archiviazione.

Aggiungere questo codice alla fine della funzione `main`:

```javascript
// Create the BlobServiceClient object which will be used to create a container client
const blobServiceClient = await new BlobServiceClient.fromConnectionString(CONNECT_STR);

// Create a unique name for the container
const containerName = 'quickstart' + uuidv1();

console.log('\nCreating container...');
console.log('\t', containerName);

// Get a reference to a container
const containerClient = await blobServiceClient.getContainerClient(containerName);

// Create the container
await containerClient.create();
```

### <a name="upload-blobs-to-a-container"></a>Caricare BLOB in un contenitore

Il frammento di codice seguente consente di:

1. Creare una stringa di testo da caricare in un oggetto BLOB.
1. Ottenere un riferimento a un oggetto [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient) chiamando il metodo [getBlockBlobClient](/javascript/api/@azure/storage-blob/containerclient#getblockblobclient-string-) su [ContainerClient](/javascript/api/@azure/storage-blob/containerclient) dalla sezione [Creare un contenitore](#create-a-container).
1. Caricare i dati della stringa di testo nell'oggetto BLOB chiamando il metodo [upload](/javascript/api/@azure/storage-blob/blockblobclient#upload-httprequestbody--number--blockblobuploadoptions-).

Aggiungere questo codice alla fine della funzione `main`:

```javascript
// Create a unique name for the blob
const blobName = 'quickstart' + uuidv1() + '.txt';

// Get a block blob client
const blockBlobClient = containerClient.getBlockBlobClient(blobName);

console.log('\nUploading to Azure Storage as blob:\n\t', blobName);

// Upload data to the blob
const data = 'Hello, World!';
await blockBlobClient.upload(data, data.length);
```

### <a name="list-the-blobs-in-a-container"></a>Elencare i BLOB in un contenitore

Elencare i BLOB presenti nel contenitore chiamando il metodo [listBlobsFlat](/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-). In questo caso, al contenitore è stato aggiunto un unico BLOB, quindi l'operazione di recupero dell'elenco restituisce solo tale BLOB.

Aggiungere questo codice alla fine della funzione `main`:

```javascript
console.log('\nListing blobs...');

// List the blob(s) in the container.
for await (const blob of containerClient.listBlobsFlat()) {
    console.log('\t', blob.name);
}
```

### <a name="download-blobs"></a>Scaricare BLOB

Scaricare il BLOB creato in precedenza chiamando il metodo [download](/javascript/api/@azure/storage-blob/blockblobclient#download-undefined---number--undefined---number--blobdownloadoptions-). Il codice di esempio include una funzione helper denominata `streamToString` usata per leggere un flusso leggibile di Node.js in una stringa.

Aggiungere questo codice alla fine della funzione `main`:

```javascript
// Get blob content from position 0 to the end
// In Node.js, get downloaded data by accessing downloadBlockBlobResponse.readableStreamBody
// In browsers, get downloaded data by accessing downloadBlockBlobResponse.blobBody
const downloadBlockBlobResponse = await blockBlobClient.download(0);
console.log('\nDownloaded blob content...');
console.log('\t', await streamToString(downloadBlockBlobResponse.readableStreamBody));
```

Aggiungere questa funzione helper *dopo* la funzione `main`:

```javascript
// A helper function used to read a Node.js readable stream into a string
async function streamToString(readableStream) {
  return new Promise((resolve, reject) => {
    const chunks = [];
    readableStream.on("data", (data) => {
      chunks.push(data.toString());
    });
    readableStream.on("end", () => {
      resolve(chunks.join(""));
    });
    readableStream.on("error", reject);
  });
}
```

### <a name="delete-a-container"></a>Eliminare un contenitore

Il codice seguente pulisce le risorse create dall'app eliminando l'intero contenitore tramite il metodo [delete](/javascript/api/@azure/storage-blob/containerclient#delete-containerdeletemethodoptions-). È anche possibile eliminare i file locali, se si vuole.

Aggiungere questo codice alla fine della funzione `main`:

```javascript
console.log('\nDeleting container...');

// Delete container
await containerClient.delete();
```

## <a name="run-the-code"></a>Eseguire il codice

Questa app crea una stringa di testo e la carica nell'archivio BLOB. L'esempio elenca quindi i BLOB nel contenitore, scarica il BLOB e visualizza i dati scaricati.

Da un prompt della console, passare alla directory contenente il file *blob-quickstart-v12.py*, quindi usare questo comando `node` per eseguire l'app.

```console
node blob-quickstart-v12.js
```

L'output dell'app è simile all'esempio seguente:

```output
Azure Blob storage v12 - JavaScript quickstart sample

Creating container...
         quickstart4a0780c0-fb72-11e9-b7b9-b387d3c488da

Uploading to Azure Storage as blob:
         quickstart4a3128d0-fb72-11e9-b7b9-b387d3c488da.txt

Listing blobs...
         quickstart4a3128d0-fb72-11e9-b7b9-b387d3c488da.txt

Downloaded blob content...
         Hello, World!

Deleting container...
Done
```

Scorrere il codice nel debugger e controllare il portale di Azure durante il processo. Verificare che il contenitore venga creato. È possibile aprire il BLOB all'interno del contenitore e visualizzarne il contenuto.

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come caricare, scaricare ed elencare i BLOB con JavaScript.

Per visualizzare le app di esempio di Archiviazione BLOB, procedere con:

> [!div class="nextstepaction"]
> [Esempi dell'SDK di Archiviazione BLOB di Azure v12 per JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples)

* Per altre informazioni, vedere [Azure SDK per JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/README.md).
* Per esercitazioni, esempi, guide di avvio rapido e altra documentazione, vedere la [documentazione di Azure SDK per JavaScript](/azure/javascript/).
