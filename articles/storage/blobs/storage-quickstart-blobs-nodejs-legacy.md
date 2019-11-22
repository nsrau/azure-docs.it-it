---
title: 'Guida introduttiva: libreria client di archiviazione BLOB di Azure V2 per JavaScript'
description: Si creano un account di archiviazione e un contenitore nell'archivio oggetti (BLOB). Usare la libreria client di archiviazione di Azure per node. js V2 per caricare un BLOB, scaricare un BLOB ed elencare i BLOB in un contenitore.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 02/04/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.custom: seo-javascript-september2019
ms.openlocfilehash: 7300d4eccec5b1e4b3b5b7dc292cf6150a42e7b4
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74269693"
---
# <a name="quickstart-azure-blob-storage-client-library-v2-for-javascript"></a>Guida introduttiva: libreria client di archiviazione BLOB di Azure V2 per JavaScript

Questa guida pratica illustra come usare la libreria client per Node.js v2 per caricare, scaricare ed elencare BLOB con Archiviazione BLOB di Azure.

> [!TIP]
> La versione più recente della libreria client di Archiviazione di Azure per Node.js è v10. È consigliabile usare la versione della libreria client più recente quando possibile. Per iniziare a usare V10, vedere [Guida introduttiva: caricare, scaricare, elencare ed eliminare BLOB usando la libreria client di archiviazione di Azure per JavaScript V10 (anteprima)](storage-quickstart-blobs-nodejs-v10.md).

## <a name="prerequisites"></a>prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

Creare un account di Archiviazione di Azure nel [portale di Azure](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM). Per informazioni sulla creazione dell'account, vedere [Creare un account di archiviazione](../common/storage-quickstart-create-account.md).

## <a name="download-the-sample-application"></a>Scaricare l'applicazione di esempio

L'[applicazione di esempio](https://github.com/Azure-Samples/storage-blobs-node-quickstart.git) è una semplice applicazione console Node.js. Per iniziare, clonare il repository nel computer in uso con il comando seguente:

```bash
git clone https://github.com/Azure-Samples/storage-blobs-node-quickstart.git
```

Per aprire l'applicazione, cercare la cartella *storage-blobs-node-quickstart* e aprirla nel proprio editor di codice preferito.

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Configurare la stringa di connessione di archiviazione

Prima di eseguire l'applicazione, è necessario specificare la stringa di connessione per l'account di archiviazione. Il repository di esempio include un file denominato *. env.example*. È possibile rinominare il file rimuovendo l'estensione *.example*, per ottenere un file denominato *.env*. All'interno del file *.env* aggiungere il valore della stringa di connessione dopo la chiave *AZURE_STORAGE_CONNECTION_STRING*.

## <a name="install-required-packages"></a>Installare i pacchetti necessari

Nella directory dell'applicazione eseguire il comando *npm install* per installare i pacchetti richiesti per l'applicazione.

```bash
npm install
```

## <a name="run-the-sample"></a>Eseguire l'esempio
Ora che le dipendenze sono installate, è possibile eseguire l'esempio tramite il comando seguente:

```bash
npm start
```

L'output di questo script sarà simile al seguente:

```bash
Containers:
 - container-one
 - container-two
Container "demo" is created
Blob "quickstart.txt" is uploaded
Local file "./readme.md" is uploaded
Blobs in "demo" container:
 - quickstart.txt
 - readme.md
Blob downloaded blob content: "hello Blob SDK"
Blob "quickstart.txt" is deleted
Container "demo" is deleted
Done
```

Se per questo esempio si usa un nuovo account di archiviazione, potrebbero non essere visibili i nomi dei contenitori elencati sotto l'etichetta "*Contenitori*".

## <a name="understanding-the-code"></a>Informazioni sul codice
La prima espressione viene usata per caricare i valori in variabili di ambiente.

```javascript
if (process.env.NODE_ENV !== 'production') {
    require('dotenv').load();
}
```

Il modulo *dotenv* carica le variabili di ambiente quando esegue l'app in locale per il debug. Le variabili di ambiente vengono definite in un file denominato *.env* e caricate nel contesto di esecuzione corrente. In contesti di produzione, la configurazione del server fornisce questi valori; questo è il motivo per cui questo codice viene eseguito solo quando lo script non è in esecuzione in un contesto di "produzione".

```javascript
const path = require('path');
const storage = require('azure-storage');
```

Di seguito è descritto lo scopo di questi moduli: 

Il file denominato *env* nel contesto di esecuzione corrente
- *path* è necessario per determinare il percorso assoluto del file da caricare nell'archivio BLOB
- *azure-storage* è il modulo della [libreria client di Archiviazione di Azure](https://docs.microsoft.com/javascript/api/azure-storage) per Node.js

Successivamente, la variabile **servizio BLOB** viene inizializzata come una nuova istanza del servizio BLOB di Azure.

```javascript
const blobService = storage.createBlobService();
```

Nell'implementazione seguente viene eseguito il wrapping di ognuna delle funzioni *blobService* in una *Promessa*, che consente l'accesso alla funzione *async* e all'operatore *await* di JavaScript per ottimizzare il funzionamento delle [API di Archiviazione di Azure](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest), basato su callback. Quando viene restituita una risposta con esito positivo per ogni funzione, la promessa viene risolta con i dati rilevanti insieme a un messaggio specifico per l'azione.

### <a name="list-containers"></a>Elencare i contenitori

La funzione *listContainers* chiama [listContainersSegmented](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) che restituisce raccolte dei contenitori nei gruppi.

```javascript
const listContainers = async () => {
    return new Promise((resolve, reject) => {
        blobService.listContainersSegmented(null, (err, data) => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `${data.entries.length} containers`, containers: data.entries });
            }
        });
    });
};
```

Le dimensioni dei gruppi sono configurabili tramite [ListContainersOptions](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice.listcontaineroptions?view=azure-node-latest). La chiamata di *listContainersSegmented* restituisce i metadati dei BLOB sotto forma di matrice di istanze [ContainerResult](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice.containerresult?view=azure-node-latest). I risultati sono restituiti in batch con incrementi di 5.000 (segmenti). Se sono presenti più di 5.000 BLOB in un contenitore, i risultati includeranno un valore per *continuationToken*. Per elencare i segmenti successivi del contenitore BLOB, è possibile passare il token di continuazione in *listContainersSegment* come secondo argomento.

### <a name="create-a-container"></a>Creare un contenitore

La funzione *createContainer* chiama [createContainerIfNotExists](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) e imposta il livello di accesso appropriato per il BLOB.

```javascript
const createContainer = async (containerName) => {
    return new Promise((resolve, reject) => {
        blobService.createContainerIfNotExists(containerName, { publicAccessLevel: 'blob' }, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Container '${containerName}' created` });
            }
        });
    });
};
```

Il secondo parametro (*options*) per **createContainerIfNotExists** accetta un valore per [publicAccessLevel](https://docs.microsoft.com/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest). Il valore *blob* per *publicAccessLevel* specifica che vengono esposti pubblicamente specifici dati del BLOB. L'impostazione opposta è l'accesso di livello *container*, che consente di elencare il contenuto del contenitore.

L'uso di **createContainerIfNotExists** consente all'applicazione di eseguire il comando *createContainer* più volte senza restituire errori quando il contenitore esiste già. In un ambiente di produzione, la funzione **createContainerIfNotExists** viene generalmente chiamata una volta sola, perché viene usato lo stesso contenitore in tutta l'applicazione. In questi casi è possibile creare il contenitore in anticipo tramite il portale o tramite l'interfaccia della riga di comando di Azure.

### <a name="upload-text"></a>Caricare un file di testo

La funzione *uploadString* chiama [createBlockBlobFromText](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) per scrivere (o sovrascrivere) una stringa arbitraria nel contenitore BLOB.

```javascript
const uploadString = async (containerName, blobName, text) => {
    return new Promise((resolve, reject) => {
        blobService.createBlockBlobFromText(containerName, blobName, text, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Text "${text}" is written to blob storage` });
            }
        });
    });
};
```
### <a name="upload-a-local-file"></a>Caricare un file locale

La funzione *uploadLocalFile* usa la funzione [createBlockBlobFromLocalFile](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#createblockblobfromlocalfile-string--string--string--errororresult-blobresult--) per caricare e scrivere (o sovrascrivere) un file dal file system all'archivio BLOB. 

```javascript
const uploadLocalFile = async (containerName, filePath) => {
    return new Promise((resolve, reject) => {
        const fullPath = path.resolve(filePath);
        const blobName = path.basename(filePath);
        blobService.createBlockBlobFromLocalFile(containerName, blobName, fullPath, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Local file "${filePath}" is uploaded` });
            }
        });
    });
};
```
Altri approcci disponibili per caricare contenuti nei BLOB includono l'uso di [testo](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest-string--string--string---buffer--errororresult-blobresult--) e [flussi](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#createblockblobfromstream-string--string--stream-readable--number--errororresult-blobresult--). Per verificare che il file sia stato caricato nell'archivio BLOB, è possibile usare [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) per visualizzare i dati nel proprio account.

### <a name="list-the-blobs"></a>Elencare i BLOB

La funzione *listBlobs* chiama il metodo [listBlobsSegmented](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#listblobssegmented-string--continuationtoken--errororresult-listblobsresult--) per restituire un elenco di metadati dei BLOB in un contenitore. 

```javascript
const listBlobs = async (containerName) => {
    return new Promise((resolve, reject) => {
        blobService.listBlobsSegmented(containerName, null, (err, data) => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `${data.entries.length} blobs in '${containerName}'`, blobs: data.entries });
            }
        });
    });
};
```

La chiamata di *listBlobsSegmented* restituisce i metadati dei BLOB sotto forma di matrice di istanze [BlobResult](https://docs.microsoft.com/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice.blobresult?view=azure-node-latest). I risultati sono restituiti in batch con incrementi di 5.000 (segmenti). Se sono presenti più di 5.000 BLOB in un contenitore, i risultati includeranno un valore per **continuationToken**. Per elencare i segmenti successivi del contenitore BLOB, è possibile passare il token di continuazione in **listBlobSegmented** come secondo argomento.

### <a name="download-a-blob"></a>Scaricare un BLOB

La funzione *downloadBlob* usa [getBlobToText](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) per scaricare i contenuti del BLOB nel percorso file assoluto specificato.

```javascript
const downloadBlob = async (containerName, blobName) => {
    const dowloadFilePath = path.resolve('./' + blobName.replace('.txt', '.downloaded.txt'));
    return new Promise((resolve, reject) => {
        blobService.getBlobToText(containerName, blobName, (err, data) => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Blob downloaded "${data}"`, text: data });
            }
        });
    });
};
```
L'implementazione illustrata in questo articolo modifica l'origine e restituisce il contenuto del BLOB sotto forma di stringa. È anche possibile scaricare il BLOB come [flusso](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) nonché direttamente in un [file locale](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest).

### <a name="delete-a-blob"></a>Eliminare un BLOB

La funzione *deleteBlob* chiama la funzione [deleteBlobIfExists](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#deleteblobifexists-string--string--errororresult-boolean--). Come suggerisce il nome, questa funzione non restituisce un errore se il BLOB è già stato eliminato.

```javascript
const deleteBlob = async (containerName, blobName) => {
    return new Promise((resolve, reject) => {
        blobService.deleteBlobIfExists(containerName, blobName, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Block blob '${blobName}' deleted` });
            }
        });
    });
};
```

### <a name="delete-a-container"></a>Eliminare un contenitore

I contenitori vengono eliminati chiamando il metodo *deleteContainer* per disattivare il servizio BLOB e presentare il nome del contenitore.

```javascript
const deleteContainer = async (containerName) => {
    return new Promise((resolve, reject) => {
        blobService.deleteContainer(containerName, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Container '${containerName}' deleted` });
            }
        });
    });
};
```

### <a name="calling-code"></a>Codice della chiamata

Per il supporto della sintassi JavaScript *async/await*, viene eseguito il wrapping per tutto il codice chiamante in una funzione denominata *execute*. La funzione "execute" viene quindi chiamata e gestita come promessa.

```javascript
async function execute() {
    // commands 
}

execute().then(() => console.log("Done")).catch((e) => console.log(e));
```
Tutto il codice seguente viene eseguito all'interno della funzione execute in cui si trova il `// commands` commento.

In primo luogo, le variabili rilevanti vengono dichiarate per assegnare nomi, contenuto di esempio e in modo da puntare al file locale da caricare nell'archivio BLOB.

```javascript
const containerName = "demo";
const blobName = "quickstart.txt";
const content = "hello Node SDK";
const localFilePath = "./readme.md";
let response;
```

Per elencare i contenitori nell'account di archiviazione, viene chiamata la funzione listContainers e l'elenco restituito di contenitori viene registrato nella finestra di output.

```javascript
console.log("Containers:");
response = await listContainers();
response.containers.forEach((container) => console.log(` -  ${container.name}`));
```

Una volta disponibile l'elenco di contenitori, è possibile usare il metodo Matrice *findIndex* per verificare se il contenitore che si desidera creare già esiste. Se il contenitore non esiste viene creato.

```javascript
const containerDoesNotExist = response.containers.findIndex((container) => container.name === containerName) === -1;

if (containerDoesNotExist) {
    await createContainer(containerName);
    console.log(`Container "${containerName}" is created`);
}
```
Successivamente, una stringa e un file locale vengono caricati nell'archivio BLOB.

```javascript
await uploadString(containerName, blobName, content);
console.log(`Blob "${blobName}" is uploaded`);

response = await uploadLocalFile(containerName, localFilePath);
console.log(response.message);
```
Il processo per elencare i BLOB è quello usato per elencare i contenitori. La chiamata a *listBlobs* restituisce una matrice di oggetti BLOB nel contenitore che vengono registrati nella finestra di output.

```javascript
console.log(`Blobs in "${containerName}" container:`);
response = await listBlobs(containerName);
response.blobs.forEach((blob) => console.log(` - ${blob.name}`));
```

Per scaricare un blob, la risposta viene acquisita e usata per accedere al valore del BLOB. Dalla risposta readableStreamBody viene convertito in una stringa e disconnesso dalla finestra di output.

```javascript
response = await downloadBlob(containerName, blobName);
console.log(`Downloaded blob content: "${response.text}"`);
```

Infine, il BLOB e il contenitore vengono eliminati dall'account di archiviazione.

```javascript
await deleteBlob(containerName, blobName);
console.log(`Blob "${blobName}" is deleted`);

await deleteContainer(containerName);
console.log(`Container "${containerName}" is deleted`);
```

## <a name="clean-up-resources"></a>Pulire le risorse
Tutti i dati scritti nell'account di archiviazione vengono automaticamente eliminati alla fine del codice di esempio. 

## <a name="resources-for-developing-nodejs-applications-with-blobs"></a>Risorse per lo sviluppo di applicazioni Node.js con BLOB

Per lo sviluppo Node.js con archiviazione BLOB, vedere le risorse aggiuntive seguenti:

### <a name="binaries-and-source-code"></a>File binari e codice sorgente

- Visualizzare e installare il [codice sorgente della libreria client Node.js](https://github.com/Azure/azure-storage-node) per Archiviazione di Azure su GitHub.

### <a name="client-library-reference-and-samples"></a>Informazioni di riferimento ed esempi relativi alla libreria client

- Per altre informazioni sulla libreria client Node.js, vedere le [informazioni di riferimento sulle API Node.js](https://docs.microsoft.com/javascript/api/overview/azure/storage).
- Esplorare gli [esempi per Archiviazione BLOB](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=nodejs&term=blob) scritti con la libreria client Node.js.

## <a name="next-steps"></a>Passaggi successivi

Questo articolo illustra come caricare un file da un disco locale e Archiviazione BLOB di Azure e viceversa con Node.js. Per altre informazioni sull'uso dell'archiviazione BLOB, continuare sul repository di GitHub.

> [!div class="nextstepaction"]
> [Microsoft Azure Storage SDK per Node.js e JavaScript per Browsers](https://github.com/Azure/azure-storage-node)
