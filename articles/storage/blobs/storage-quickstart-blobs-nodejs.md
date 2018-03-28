---
title: 'Guida introduttiva di Azure: Caricare, scaricare ed elencare BLOB in Archiviazione di Azure con Node.js | Microsoft Docs'
description: In questa guida introduttiva viene creato un account di archiviazione e un contenitore. Si usa quindi la libreria client di archiviazione per Node.js per caricare un BLOB in Archiviazione di Azure, scaricare un BLOB ed elencare i BLOB presenti in un contenitore.
services: storage
author: craigshoemaker
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 03/15/2018
ms.author: cshoe
ms.openlocfilehash: 28f9936c297b6f641810e0c7783f4d84be108286
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-nodejs"></a>Guida introduttiva: Caricare, scaricare ed elencare BLOB con Node.js

Questa guida introduttiva mostra come usare Node.js per caricare, scaricare ed elencare BLOB in blocchi in un contenitore usando Archiviazione BLOB di Azure.

Per completare questa guida introduttiva è necessaria una [sottoscrizione di Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Scaricare l'applicazione di esempio

L'[applicazione di esempio](https://github.com/Azure-Samples/storage-blobs-node-quickstart.git) usata in questa guida introduttiva è una semplice applicazione console Node.js. Per iniziare, clonare il repository nel computer in uso con il comando seguente:

```bash
git clone https://github.com/Azure-Samples/storage-blobs-node-quickstart.git
```

Per aprire l'applicazione, cercare la cartella *storage-blobs-node-quickstart* e aprirla nel proprio editor di codice preferito.

## <a name="configure-your-storage-connection-string"></a>Configurare la stringa di connessione di archiviazione

Prima di eseguire l'applicazione, è necessario specificare la stringa di connessione per l'account di archiviazione. Il repository di esempio include un file denominato *. env.example*. È possibile rinominare il file rimuovendo l'estensione *.example*, per ottenere un file denominato *.env*. All'interno del file *.env* aggiungere il valore della stringa di connessione dopo la chiave *AZURE_STORAGE_CONNECTION_STRING*.

## <a name="install-required-packages"></a>Installare i pacchetti necessari

Nella directory dell'applicazione eseguire il comando *npm install* per installare i pacchetti richiesti per l'applicazione.

```bash
npm install
```

## <a name="run-the-sample"></a>Eseguire l'esempio
Ora che le dipendenze sono installate, è possibile eseguire l'esempio passando comandi allo script. Per creare un contenitore BLOB, ad esempio, si esegue il comando seguente:

```bash
node index.js --command createContainer
```

I comandi disponibili sono:

| Comando | DESCRIZIONE |
|---------|---------|
|*createContainer* | Crea un contenitore denominato *test-container* (ha esito positivo anche se il contenitore esiste già) |
|*upload*          | Carica il file *example.txt* nel contenitore *test-container* |
|*download*        | Scarica il contenuto del BLOB *example* in *example.downloaded.txt* |
|*delete*          | Elimina il BLOB *example* |
|*list*            | Mostra il contenuto del contenitore *test-container* nella console |


## <a name="understanding-the-sample-code"></a>Informazioni sul codice di esempio
Questo codice di esempio usa alcuni moduli per interfacciarsi con il file system e la riga di comando. 

```javascript
if (process.env.NODE_ENV !== 'production') {
    require('dotenv').load();
}
const path = require('path');
const args = require('yargs').argv;
const storage = require('azure-storage');
```

Di seguito è descritto lo scopo di questi moduli: 

- *dotenv* carica le variabili di ambiente definite in un file denominato *.env* nel contesto di esecuzione corrente
- *path* è necessario per determinare il percorso assoluto del file da caricare nell'archivio BLOB
- *yargs* espone un'interfaccia semplice per accedere agli argomenti della riga di comando
- *azure-storage* è il modulo [Azure Storage SDK](/nodejs/api/azure-storage) per Node.js

Viene quindi inizializzata una serie di variabili:

```javascript
const blobService = storage.createBlobService();
const containerName = 'test-container';
const sourceFilePath = path.resolve('./example.txt');
const blobName = path.basename(sourceFilePath, path.extname(sourceFilePath));
```

Le variabili sono impostate sui valori seguenti:

- *blobService* è impostata su una nuova istanza del servizio BLOB di Azure
- *containerName* è impostata sul nome del contenitore
- *sourceFilePath* è impostata sul percorso assoluto del file da caricare
- *blobName* viene creata rimuovendo l'estensione dal nome del file

Nell'implementazione seguente viene eseguito il wrapping di ognuna delle funzioni *blobService* in una *Promessa*, che consente l'accesso alla funzione *async* e all'operatore *await* di JavaScript per ottimizzare il funzionamento delle [API di Archiviazione di Azure](/nodejs/api/azure-storage/blobservice), basato su callback. Quando viene restituita una risposta con esito positivo per ogni funzione, la promessa viene risolta con i dati rilevanti insieme a un messaggio specifico per l'azione.

### <a name="create-a-blob-container"></a>Creare un contenitore BLOB

La funzione *createContainer* chiama [createContainerIfNotExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createContainerIfNotExists) e imposta il livello di accesso appropriato per il BLOB.

```javascript
const createContainer = () => {
    return new Promise((resolve, reject) => {
        blobService.createContainerIfNotExists(containerName, { publicAccessLevel: 'blob' }, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Container '${containerName}' created` });
            }
        });
    });
};
```

Il secondo parametro (*options*) per **createContainerIfNotExists** accetta un valore per [publicAccessLevel](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createContainerIfNotExists). Il valore *blob* per *publicAccessLevel* specifica che vengono esposti pubblicamente specifici dati del BLOB. L'impostazione opposta è l'accesso di livello *container*, che consente di elencare il contenuto del contenitore.

L'uso di **createContainerIfNotExists** consente all'applicazione di eseguire il comando *createContainer* più volte senza restituire errori quando il contenitore esiste già. In un ambiente di produzione, la funzione **createContainerIfNotExists** viene generalmente chiamata una volta sola, perché viene usato lo stesso contenitore in tutta l'applicazione. In questi casi è possibile creare il contenitore in anticipo tramite il portale o tramite l'interfaccia della riga di comando di Azure.

### <a name="upload-a-blob-to-the-container"></a>Caricare un BLOB nel contenitore

La funzione *upload* usa la funzione [createBlockBlobFromLocalFile](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromLocalFile) per caricare e scrivere o sovrascrivere un file dal file system all'archivio BLOB. 

```javascript
const upload = () => {
    return new Promise((resolve, reject) => {
        blobService.createBlockBlobFromLocalFile(containerName, blobName, sourceFilePath, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Upload of '${blobName}' complete` });
            }
        });
    });
};
```
Nel contesto dell'applicazione di esempio, il file denominato *example.txt* viene caricato in un BLOB denominato *example* all'interno di un contenitore denominato *test-container*. Altri approcci disponibili per caricare contenuti nei BLOB includono l'uso di [testo](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromText) e [flussi](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromStream).

Per verificare che il file sia stato caricato nell'archivio BLOB, è possibile usare [Azure Storage Explorer](https://azure.microsoft.com/en-us/features/storage-explorer/) per visualizzare i dati nel proprio account.

### <a name="list-the-blobs-in-a-container"></a>Elencare i BLOB in un contenitore

La funzione *list* chiama il metodo [listBlobsSegmented](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromText) per restituire un elenco di metadati dei BLOB in un contenitore. 

```javascript
const list = () => {
    return new Promise((resolve, reject) => {
        blobService.listBlobsSegmented(containerName, null, (err, data) => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Items in container '${containerName}':`, data: data });
            }
        });
    });
};
```

La chiamata di *listBlobsSegmented* restituisce i metadati dei BLOB sotto forma di matrice di istanze [BlobResult](/nodejs/api/azure-storage/blobresult). I risultati sono restituiti in batch con incrementi di 5.000 (segmenti). Se sono presenti più di 5.000 BLOB in un contenitore, i risultati includeranno un valore per **continuationToken**. Per elencare i segmenti successivi del contenitore BLOB, è possibile passare il token di continuazione in **listBlobSegmented** come secondo argomento.

### <a name="download-a-blob-from-the-container"></a>Scaricare un BLOB dal contenitore

La funzione *download* usa [getBlobToLocalFile](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_getBlobToLocalFile) per scaricare il contenuto del BLOB nel percorso file assoluto specificato.

```javascript
const download = () => {
    const dowloadFilePath = sourceFilePath.replace('.txt', '.downloaded.txt');
    return new Promise((resolve, reject) => {
        blobService.getBlobToLocalFile(containerName, blobName, dowloadFilePath, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Download of '${blobName}' complete` });
            }
        });
    });
};
```
L'implementazione illustrata di seguito modifica il percorso del file di origine aggiungendo *.downloaded.txt* al nome del file. In contesti reali è possibile modificare il percorso oltre al nome del file quando si seleziona una destinazione di download.

### <a name="delete-blobs-in-the-container"></a>Eliminare BLOB nel contenitore

La funzione *deleteBlock* (con il comando della console *delete* come alias) chiama la funzione [deleteBlobIfExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_deleteBlobIfExists). Come suggerisce il nome, questa funzione non restituisce un errore se il BLOB è già stato eliminato.

```javascript
const deleteBlock = () => {
    return new Promise((resolve, reject) => {
        blobService.deleteBlobIfExists(containerName, blobName, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Block blob '${blobName}' deleted` });
            }
        });
    });
};
```

### <a name="upload-and-list"></a>Caricare ed elencare

Uno dei vantaggi dell'uso delle promesse è la possibilità di concatenare più comandi. La funzione **uploadAndList** dimostra come è facile elencare il contenuto di un BLOB subito dopo aver caricato un file.

```javascript
const uploadAndList = () => {
    return _module.upload().then(_module.list);
};
```

### <a name="calling-code"></a>Codice della chiamata

Per esporre le funzioni implementate alla riga di comando, ognuna delle funzioni è mappata a un valore letterale di oggetto.

```javascript
const _module = {
    "createContainer": createContainer,
    "upload": upload,
    "download": download,
    "delete": deleteBlock,
    "list": list,
    "uploadAndList": uploadAndList
};
```

Ora che *_module* è implementato, ognuno dei comandi è disponibile dalla riga di comando.

```javascript
const commandExists = () => exists = !!_module[args.command];
```

Se un comando specificato non esiste, viene eseguito il rendering delle proprietà di *_module* sulla console come testo della guida per l'utente. 

La funzione *executeCommand* è una funzione *async* che chiama il comando specificato usando l'operatore *await* e registra i messaggi come dati nella console.

```javascript
const executeCommand = async () => {
    const response = await _module[args.command]();

    console.log(response.message);

    if (response.data) {
        response.data.entries.forEach(entry => {
            console.log('Name:', entry.name, ' Type:', entry.blobType)
        });
    }
};
```

Il codice in esecuzione, infine, chiama prima *commandExists* per verificare che allo script venga passato un comando noto. Se è selezionato un comando esistente, il comando viene eseguito e gli eventuali errori vengono registrati nella console.

```javascript
try {
    const cmd = args.command;

    console.log(`Executing '${cmd}'...`);

    if (commandExists()) {
        executeCommand();
    } else {
        console.log(`The '${cmd}' command does not exist. Try one of these:`);
        Object.keys(_module).forEach(key => console.log(` - ${key}`));
    }
} catch (e) {
    console.log(e);
}
```

## <a name="clean-up-resources"></a>Pulire le risorse
Se non si prevede l'uso dei dati o degli account creati in questo articolo, è consigliabile eliminarli per evitare addebiti indesiderati. Per eliminare il BLOB e i contenitori, è possibile usare i metodi [deleteBlobIfExists](/nodejs/api/azure-storage/blobservice?view=azure-node-latest#deleteBlobIfExists_container__blob__options__callback_) e [deleteContainerIfExists](/nodejs/api/azure-storage/blobservice?view=azure-node-latest#deleteContainerIfExists_container__options__callback_). È anche possibile eliminare l'account di archiviazione [tramite il portale](../common/storage-create-storage-account.md).

## <a name="resources-for-developing-nodejs-applications-with-blobs"></a>Risorse per lo sviluppo di applicazioni Node.js con BLOB

Per lo sviluppo Node.js con archiviazione BLOB, vedere le risorse aggiuntive seguenti:

### <a name="binaries-and-source-code"></a>File binari e codice sorgente

- Visualizzare e installare il [codice sorgente della libreria client Node.js](https://github.com/Azure/azure-storage-node) per Archiviazione di Azure su GitHub.

### <a name="client-library-reference-and-samples"></a>Informazioni di riferimento ed esempi relativi alla libreria client

- Per altre informazioni sulla libreria client Node.js, vedere le [informazioni di riferimento sulle API Node.js](https://docs.microsoft.com/en-us/javascript/api/overview/azure/storage).
- Esplorare gli [esempi per Archiviazione BLOB](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=nodejs&term=blob) scritti con la libreria client Node.js.

## <a name="next-steps"></a>Passaggi successivi

Questa guida introduttiva mostra come caricare un file tra un disco locale e Archiviazione BLOB di Azure con Node.js. Per altre informazioni sull'uso dell'archiviazione BLOB, continuare la procedura relativa all'archiviazione BLOB.

> [!div class="nextstepaction"]
> [Procedura relativa alle operazioni di archiviazione BLOB](storage-nodejs-how-to-use-blob-storage.md)

Per informazioni di riferimento su Node.js per Archiviazione di Azure, vedere [azure-storage package](https://docs.microsoft.com/javascript/api/azure-storage/?view=azure-node-latest).