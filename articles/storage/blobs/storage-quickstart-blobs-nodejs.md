---
title: Guida introduttiva di Azure - Trasferire oggetti da e verso Archiviazione BLOB di Azure con Node.js | Microsoft Docs
description: Apprendere rapidamente a trasferire oggetti da e verso Archiviazione BLOB di Azure con Node.js
services: storage
documentationcenter: storage
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 10/30/2017
ms.author: gwallace
ms.openlocfilehash: dd4d3abf082767c40760d020c0997b365452e769
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2017
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-nodejs"></a>Trasferire oggetti da e verso Archiviazione BLOB di Azure con Node.js

Questa guida introduttiva mostra come usare Node.js per caricare, scaricare ed elencare BLOB in blocchi in un contenitore di Archiviazione BLOB di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida introduttiva:

* Installare [Node.js](https://nodejs.org/en/)

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Scaricare l'applicazione di esempio

L'[applicazione di esempio](https://github.com/Azure-Samples/storage-blobs-node-quickstart.git) usata in questa guida introduttiva è un'applicazione console di base. 

Usare [git](https://git-scm.com/) per scaricare una copia dell'applicazione nell'ambiente di sviluppo.

```bash
git clone https://github.com/Azure-Samples/storage-blobs-node-quickstart.git
```

Questo comando consente di duplicare il repository nella cartella locale git. Per aprire l'applicazione, cercare la cartella storage-blobs-node-quickstart, aprirla e fare doppio clic su index.js.

## <a name="configure-your-storage-connection-string"></a>Configurare la stringa di connessione di archiviazione

Nell'applicazione è necessario inserire la stringa di connessione per l'account di archiviazione. Aprire il file `index.js` e cercare la variabile `connectionString`. Sostituire il relativo valore con l'intero valore della stringa di connessione con quello salvato dal portale di Azure. La stringa di connessione dell'account di archiviazione avrà un aspetto simile al seguente:

```javascript
// Create a blob client for interacting with the blob service from connection string
// How to create a storage connection string - http://msdn.microsoft.com/library/azure/ee758697.aspx
var connectionString = '<Your connection string here>';
var blobService = storage.createBlobService(connectionString);
```

## <a name="install-required-packages"></a>Installare i pacchetti necessari

Nella directory dell'applicazione eseguire `npm install` per installare tutti i pacchetti necessari elencati nel file `package.json`.

```javascript
npm install
```

## <a name="run-the-sample"></a>Eseguire l'esempio

Questo esempio consente di creare un file di test in Documenti, caricarlo nell'archiviazione BLOB, elencare i BLOB nel contenitore, quindi scaricare il file con un nuovo nome, così da poter confrontare i file nuovi e quelli vecchi.

Per eseguire l'esempio, digitare `node index.js`. L'output seguente proviene da un sistema Windows.  Se si usa Linux, è possibile prevedere un output simile con i percorsi di file appropriati.

```
Azure Storage Node.js Client Library Blobs Quick Start

1. Creating a container with public access: quickstartcontainer-79a3eea0-bec9-11e7-9a36-614cd00ca63d

2. Creating a file in ~/Documents folder to test the upload and download

   Local File: C:\Users\admin\Documents\HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d.txt

3. Uploading BlockBlob: quickstartblob-HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d.txt

   Uploaded Blob URL: https://mystorageaccount.blob.core.windows.net/quickstartcontainer-79a3eea0-bec9-11e7-9a36-614cd00ca63d/quickstartblob-HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d.txt

4. Listing blobs in container

   - quickstartblob-HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d.txt (type: BlockBlob)


5. Downloading blob

   Downloaded File: C:\Users\admin\Documents\HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d_DOWNLOADED.txt

Sample finished running. When you hit <ENTER> key, the temporary files will be deleted and the sample application will exit.
```

Prima di continuare, controllare che nella cartella "Documenti" siano presenti i due file. È possibile aprirli e vedere che sono identici.

È anche possibile usare uno strumento come [Azure Storage Explorer](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) per visualizzare i file nell'archiviazione BLOB. Azure Storage Explorer è uno strumento multipiattaforma gratuito che consente di accedere alle informazioni dell'account di archiviazione.

Dopo aver verificato i file, premere un tasto qualsiasi per terminare la demo ed eliminare i file di test. Ora che si conosce il risultato dell'esempio, aprire il file index.js per esaminare il codice. 

## <a name="understand-the-sample-code"></a>Informazioni sul codice di esempio

Viene quindi descritto in dettaglio il codice di esempio, per consentire di comprenderne il funzionamento.

### <a name="get-references-to-the-storage-objects"></a>Ottenere i riferimenti agli oggetti di archiviazione

La prima cosa da fare è creare il riferimento all'oggetto `BlobService` usato per accedere e gestire l'archiviazione BLOB. Questi oggetti si compilano a vicenda: ognuno di essi viene usato da quello successivo nell'elenco.

* Creare un'istanza dell'oggetto **[BlobService](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService__ctor)** che punti al servizio BLOB nell'account di archiviazione.

* Creare un nuovo contenitore e quindi impostare le autorizzazioni nel contenitore in modo che i BLOB siano pubblici e accessibili solo con un URL. Il contenitore inizia con **quickstartcontainer-**.

Questo esempio usa [createContainerCreateIfNotExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createContainerIfNotExists) perché si desidera creare un nuovo contenitore ogni volta che si esegue l'esempio. In un ambiente di produzione in cui si usa lo stesso contenitore per tutta l'applicazione, è consigliabile chiamare CreateIfNotExists solo una volta. In alternativa, è possibile creare il contenitore in anticipo in modo che non sia necessario crearlo nel codice.

```javascript
// Create a container for organizing blobs within the storage account.
console.log('1. Creating a Container with Public Access:', blockBlobContainerName, '\n');
blobService.createContainerIfNotExists(blockBlobContainerName, { 'publicAccessLevel': 'blob' }, function (error) {
    if (error) return callback(error);
```

### <a name="upload-blobs-to-the-container"></a>Caricare i BLOB nel contenitore

L'archiviazione BLOB supporta BLOB in blocchi, BLOB di aggiunta e BLOB di pagine. I BLOB in blocchi sono i BLOB usati più frequentemente. Rappresentano la soluzione ideale per l'archiviazione di dati di testo e binari ed è per questo motivo che vengono usati in questa guida introduttiva.

Per caricare un file in un BLOB, usare il metodo [createBlockBlobFromLocalFile](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_createBlockBlobFromLocalFile). Questa operazione consente di creare il BLOB se non esiste o di sovrascriverlo se esiste già.

Il codice di esempio crea un file locale da usare per il caricamento e il download, per archiviare il file da caricare come **localPath** e il nome del BLOB in **localFileToUpload**. Nell'esempio seguente il file viene caricato in un contenitore il cui nome inizia con **quickstartcontainer-**.

```javascript
console.log('2. Creating a file in ~/Documents folder to test the upload and download\n');
console.log('   Local File:', LOCAL_FILE_PATH, '\n');
fs.writeFileSync(LOCAL_FILE_PATH, 'Greetings from Microsoft!');

console.log('3. Uploading BlockBlob:', BLOCK_BLOB_NAME, '\n');
blobService.createBlockBlobFromLocalFile(CONTAINER_NAME, BLOCK_BLOB_NAME, LOCAL_FILE_PATH, function (error) {
handleError(error);
console.log('   Uploaded Blob URL:', blobService.getUrl(CONTAINER_NAME, BLOCK_BLOB_NAME), '\n');
```

Esistono diversi metodi di caricamento che è possibile usare con l'archiviazione BLOB. Ad esempio, se si dispone di un flusso di memoria, è possibile usare il metodo [createBlockBlobFromStream](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_createBlockBlobFromStream) anziché [createBlockBlobFromLocalFile](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_createBlockBlobFromLocalFile).

### <a name="list-the-blobs-in-a-container"></a>Elencare i BLOB in un contenitore

L'applicazione recupera quindi un elenco di file nel contenitore usando [listBlobsSegmented](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_listBlobsSegmented). Il codice seguente recupera l'elenco di BLOB, quindi esegue il ciclo per tutti loro mostrando gli URI dei BLOB trovati. Per visualizzare il file è possibile copiare l'URI dalla finestra di comando e incollarlo in un browser.

Se si dispone di un massimo di 5.000 BLOB nel contenitore, tutti i nomi di BLOB vengono recuperati in una chiamata a [listBlobsSegmented](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_listBlobsSegmented). Se si dispone di più di 5.000 BLOB nel contenitore, il servizio recupera l'elenco in set di 5.000 fino a quando tutti i nomi di BLOB vengono recuperati. Pertanto la prima volta che questa API viene chiamata, restituisce i primi 5.000 nomi di BLOB e un token di continuazione. La seconda volta, l'utente presenta il token e il servizio recupera il set successivo di nomi di BLOB e così via, fino a quando il token di continuazione è null, il che vuol dire che sono stati recuperati tutti i nomi di BLOB.

```javascript
console.log('4. Listing blobs in container\n');
blobService.listBlobsSegmented(CONTAINER_NAME, null, function (error, data) {
    handleError(error);

    for (var i = 0; i < data.entries.length; i++) {
    console.log(util.format('   - %s (type: %s)'), data.entries[i].name, data.entries[i].blobType);
    }
    console.log('\n');
```

### <a name="download-blobs"></a>Scaricare BLOB

Scaricare i BLOB nel disco locale usando [getBlobToLocalFile](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_getBlobToLocalFile).

Il codice seguente consente di scaricare il BLOB caricato nella sezione precedente, aggiungendo un suffisso "_DOWNLOADED" al nome del BLOB in modo da visualizzare entrambi i file sul disco locale. 

```javascript
console.log('5. Downloading blob\n');
blobService.getBlobToLocalFile(CONTAINER_NAME, BLOCK_BLOB_NAME, DOWNLOADED_FILE_PATH, function (error) {
handleError(error);
console.log('   Downloaded File:', DOWNLOADED_FILE_PATH, '\n');
```

### <a name="clean-up-resources"></a>Pulire le risorse

Se i BLOB caricati in questa guida introduttiva non sono più necessari, è possibile eliminare l'intero contenitore usando [deleteBlobIfExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_deleteBlobIfExists) e [deleteContainerIfExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_deleteContainerIfExists). Eliminare anche i file creati se non sono più necessari. Queste operazioni vengono eseguite automaticamente quando si preme INVIO per uscire dall'applicazione.

```javascript
console.log('6. Deleting block Blob\n');
    blobService.deleteBlobIfExists(CONTAINER_NAME, BLOCK_BLOB_NAME, function (error) {
        handleError(error);

    console.log('7. Deleting container\n');
    blobService.deleteContainerIfExists(CONTAINER_NAME, function (error) {
        handleError(error);
            
        fs.unlinkSync(LOCAL_FILE_PATH);
        fs.unlinkSync(DOWNLOADED_FILE_PATH);
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato descritto il trasferimento di file tra il disco locale e Archiviazione BLOB di Azure con Node.js. Per altre informazioni sull'uso dell'archiviazione BLOB, continuare la procedura relativa all'archiviazione BLOB.

> [!div class="nextstepaction"]
> [Procedura relativa alle operazioni di archiviazione BLOB](storage-nodejs-how-to-use-blob-storage.md)

Per altre informazioni su Azure Storage Explorer e i BLOB, vedere [Gestire le risorse dell'archivio BLOB di Azure con Storage Explorer](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
