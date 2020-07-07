---
title: 'Guida introduttiva: Libreria di Archiviazione BLOB di Azure v12 - JavaScript'
description: In questa guida di avvio rapido verrà illustrato come usare la libreria client di Archiviazione BLOB di Azure versione 12 per JavaScript per creare un contenitore e un BLOB nell'archiviazione (oggetto) BLOB. Verrà successivamente illustrato come scaricare il BLOB nel computer locale e come elencare tutti i BLOB in un contenitore.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 18afd9faf21e5de4831d54d0870b64e79d29cb0c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2020
ms.locfileid: "85833300"
---
# <a name="quickstart-manage-blobs-with-javascript-v12-sdk-in-nodejs"></a>Guida introduttiva: Gestire i BLOB con JavaScript v12 SDK in Node.js

In questo argomento di avvio rapido viene illustrato come gestire i BLOB con Node.js. I BLOB sono oggetti che possono contenere grandi quantità di dati di testo o binari, tra cui immagini, documenti, flussi multimediali e dati di archiviazione. Verranno caricati, scaricati ed elencati i BLOB e verranno creati ed eliminati i contenitori.

[Documentazione di riferimento delle API](/javascript/api/@azure/storage-blob) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob) | [Pacchetto (Gestione pacchetti del nodo)](https://www.npmjs.com/package/@azure/storage-blob) | [Esempi](https://docs.microsoft.com/azure/storage/common/storage-samples-javascript?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Un account dell'Archiviazione di Azure. [Creare un account di archiviazione](../common/storage-account-create.md).
- [Node.js](https://nodejs.org/en/download/).

> [!NOTE]
> Per iniziare a usare la versione precedente dell'SDK, vedere [Avvio rapido: Gestire i BLOB con JavaScript v10 SDK in Node.js](storage-quickstart-blobs-nodejs-legacy.md).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

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
        console.log('Azure Blob storage v12 - JavaScript quickstart sample');
        // Quick start code goes here
    }

    main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));
    ```

1. Salvare il nuovo file come *blob-quickstart-v12.js* nella directory *blob-quickstart-v12*.

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Modello a oggetti

Archiviazione BLOB è ottimizzata per archiviare enormi quantità di dati non strutturati. I dati non strutturati sono dati che non seguono una definizione o un modello di dati specifico, ad esempio dati di testo o binari. L’archiviazione BLOB offre tre tipi di risorse:

* L'account di archiviazione
* Un contenitore nell'account di archiviazione
* Un oggetto BLOB in un contenitore

Il diagramma seguente mostra la relazione tra queste risorse.

![Diagramma dell'architettura dell'archivio BLOB](./media/storage-blobs-introduction/blob1.png)

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
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the
// environment variable is created after the application is launched in a
// console or with Visual Studio, the shell or application needs to be closed
// and reloaded to take the environment variable into account.
const AZURE_STORAGE_CONNECTION_STRING = process.env.AZURE_STORAGE_CONNECTION_STRING;
```

### <a name="create-a-container"></a>Creare un contenitore

Decidere un nome per il nuovo contenitore. Il codice seguente aggiunge un valore UUID al nome del contenitore per garantirne l'univocità.

> [!IMPORTANT]
> I nomi dei contenitori devono essere in minuscolo. Per altre informazioni sulla denominazione di contenitori e BLOB, vedere [Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Assegnazione di nome e riferimento a contenitori, BLOB e metadati).

Creare un'istanza della classe [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient) chiamando il metodo [fromConnectionString](/javascript/api/@azure/storage-blob/blobserviceclient#fromconnectionstring-string--storagepipelineoptions-). Chiamare quindi il metodo [getContainerClient](/javascript/api/@azure/storage-blob/blobserviceclient#getcontainerclient-string-) per ottenere un riferimento a un contenitore. Infine, chiamare [create](/javascript/api/@azure/storage-blob/containerclient#create-containercreateoptions-) per creare effettivamente il contenitore nell'account di archiviazione.

Aggiungere questo codice alla fine della funzione `main`:

```javascript
// Create the BlobServiceClient object which will be used to create a container client
const blobServiceClient = BlobServiceClient.fromConnectionString(AZURE_STORAGE_CONNECTION_STRING);

// Create a unique name for the container
const containerName = 'quickstart' + uuidv1();

console.log('\nCreating container...');
console.log('\t', containerName);

// Get a reference to a container
const containerClient = blobServiceClient.getContainerClient(containerName);

// Create the container
const createContainerResponse = await containerClient.create();
console.log("Container was created successfully. requestId: ", createContainerResponse.requestId);
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

console.log('\nUploading to Azure storage as blob:\n\t', blobName);

// Upload data to the blob
const data = 'Hello, World!';
const uploadBlobResponse = await blockBlobClient.upload(data, data.length);
console.log("Blob was uploaded successfully. requestId: ", uploadBlobResponse.requestId);
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
const deleteContainerResponse = await containerClient.delete();
console.log("Container was deleted successfully. requestId: ", deleteContainerResponse.requestId);
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

Scorrere il codice nel debugger e controllare il [portale di Azure](https://portal.azure.com) durante il processo. Verificare che il contenitore venga creato. È possibile aprire il BLOB all'interno del contenitore e visualizzarne il contenuto.

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come caricare, scaricare ed elencare i BLOB con JavaScript.

Per esercitazioni, esempi, guide di avvio rapido e altra documentazione, vedere:

> [!div class="nextstepaction"]
> [Documentazione di Azure per JavaScript](/azure/developer/javascript/)

* Per altre informazioni, vedere la [libreria client di Archiviazione BLOB di Azure per JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob).
* Per altre app di esempio su Archiviazione BLOB di Azure, continuare con gli [esempi della libreria client di Archiviazione BLOB di Azure v12 per JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples).
