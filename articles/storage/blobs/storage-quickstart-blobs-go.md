---
title: 'Guida introduttiva di Azure: Caricare, scaricare ed elencare BLOB in Archiviazione di Azure con Go | Microsoft Docs'
description: In questa guida introduttiva viene creato un account di archiviazione e un contenitore. Si usa quindi la libreria client di archiviazione per Go per caricare un BLOB in Archiviazione di Azure, scaricare un BLOB ed elencare i BLOB presenti in un contenitore.
services: storage
author: seguler
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 02/22/2018
ms.author: seguler
ms.openlocfilehash: a30d012ea663b04028a4b0ebb2880532a596500b
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/24/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-go"></a>Guida introduttiva: Caricare, scaricare ed elencare BLOB con Go

Questa guida introduttiva spiega come usare il linguaggio di programmazione di Go per caricare, scaricare ed elencare BLOB in blocchi in un contenitore di Archiviazione BLOB di Azure. 

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida introduttiva: 
* Installare [Go 1.8 o versione successiva](https://golang.org/dl/)
* Scaricare e installare [Azure Storage Blob SDK per Go](https://github.com/azure/azure-storage-blob-go/) con `go get -u github.com/azure/azure-storage-blob-go/2016-05-31/azblob`. 

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Scaricare l'applicazione di esempio
L'[applicazione di esempio](https://github.com/Azure-Samples/storage-blobs-go-quickstart.git) usata in questa guida introduttiva è un'applicazione Go di base.  

Usare [git](https://git-scm.com/) per scaricare una copia dell'applicazione nell'ambiente di sviluppo. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-go-quickstart 
```

Questo comando consente di duplicare il repository nella cartella locale git. Per aprire l'esempio Go per l'archiviazione BLOB, cercare il file storage-quickstart.go.  

## <a name="configure-your-storage-connection-string"></a>Configurare la stringa di connessione di archiviazione
Questa soluzione richiede che il nome dell'account di archiviazione e la chiave siano archiviati in modo sicuro in variabili di sistema locali nel computer che esegue l'esempio. Seguire uno degli esempi riportati più avanti, in base al sistema operativo, per creare la variabile di ambiente.

# <a name="linuxtablinux"></a>[Linux](#tab/Linux)

```
export AZURE_STORAGE_ACCOUNT="<youraccountname>"
export AZURE_STORAGE_ACCESS_KEY="<youraccountkey>"
```

# <a name="windowstabwindows"></a>[Windows](#tab/Windows)

```
setx AZURE_STORAGE_ACCOUNT "<youraccountname>"
setx AZURE_STORAGE_ACCESS_KEY "<youraccountkey>"
```

---

## <a name="run-the-sample"></a>Eseguire l'esempio
Questo esempio crea un file di test nella cartella corrente, carica il file di test nell'archivio BLOB, elenca i BLOB nel contenitore e scarica il file in un buffer. 

Per eseguire l'esempio, inviare il comando seguente: 

```go run storage-quickstart.go```

Il seguente output è un esempio dell'output ricevuto durante l'esecuzione dell'applicazione:
  
```
Azure Blob storage quick start sample
Creating a container named quickstart-5568059279520899415
Creating a dummy file to test the upload and download
Uploading the file with blob name: 630910657703031215
Blob name: 630910657703031215
Downloaded the blob: hello world
this is a blob
Press the enter key to delete the sample files, example container, and exit the application.
```
Quando si preme il tasto per continuare, il programma di esempio elimina il contenitore di archiviazione e i file. 

> [!TIP]
> È anche possibile usare uno strumento come [Azure Storage Explorer](http://storageexplorer.com) per visualizzare i file nell'archiviazione BLOB. Azure Storage Explorer è uno strumento multipiattaforma gratuito che consente di accedere alle informazioni dell'account di archiviazione. 
>

## <a name="understand-the-sample-code"></a>Informazioni sul codice di esempio

Viene quindi descritto in dettaglio il codice di esempio, per consentire di comprenderne il funzionamento.

### <a name="create-containerurl-and-bloburl-objects"></a>Creare gli oggetti ContainerURL e BlobURL
È prima di tutto necessario creare i riferimenti agli oggetti ContainerURL e BlobURL usati per l'accesso e la gestione dell'archivio BLOB. Questi oggetti offrono API specifiche, ad esempio Create, PutBlob e GetBlob per il rilascio di API REST.

* Usare lo struct **SharedKeyCredential** per archiviare le credenziali. 

* Creare una **Pipeline** usando le credenziali e le opzioni. La pipeline specifica elementi quali i criteri per la ripetizione dei tentativi, la registrazione, la deserializzazione dei payload di risposte HTTP e così via.  

* Creare un'istanza di un nuovo oggetto ContainerURL e un nuovo oggetto BlobURL per eseguire operazioni su contenitori (Create) e BLOB (PutBlob e GetBlob).


Quando ContainerURL è disponibile, è necessario creare un'istanza dell'oggetto **BlobURL** che fa riferimento a un BLOB ed eseguire operazioni quali caricamento, download e copia.

> [!IMPORTANT]
> I nomi dei contenitori devono essere in minuscolo. Per altre informazioni sulla denominazione dei contenitori e dei BLOB vedere [Naming and Referencing Containers, Blobs, and Metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Denominazione e riferimento a contenitori, BLOB e metadati).

In questa sezione viene creato un nuovo contenitore. Il contenitore è denominato **quickstartblobs-[stringa casuale]**. 

```go 
// From the Azure portal, get your storage account name and key and set environment variables.
accountName, accountKey := os.Getenv("AZURE_STORAGE_ACCOUNT"), os.Getenv("AZURE_STORAGE_ACCESS_KEY")
if len(accountName) == 0 || len(accountKey) == 0 {
    log.Fatal("Either the AZURE_STORAGE_ACCOUNT or AZURE_STORAGE_ACCESS_KEY environment variable is not set")
}

// Create a default request pipeline using your storage account name and account key.
credential := azblob.NewSharedKeyCredential(accountName, accountKey)
p := azblob.NewPipeline(credential, azblob.PipelineOptions{})

// Create a random string for the quick start container
containerName := fmt.Sprintf("quickstart-%s", randomString())

// From the Azure portal, get your storage account blob service URL endpoint.
URL, _ := url.Parse(
    fmt.Sprintf("https://%s.blob.core.windows.net/%s", accountName, containerName))

// Create a ContainerURL object that wraps the container URL and a request
// pipeline to make requests.
containerURL := azblob.NewContainerURL(*URL, p)

// Create the container
fmt.Printf("Creating a container named %s\n", containerName)
ctx := context.Background() // This example uses a never-expiring context
_, err := containerURL.Create(ctx, azblob.Metadata{}, azblob.PublicAccessNone)
handleErrors(err)
```
### <a name="upload-blobs-to-the-container"></a>Caricare i BLOB nel contenitore

L'archiviazione BLOB supporta BLOB in blocchi, BLOB di aggiunta e BLOB di pagine. I BLOB in blocchi sono quelli usati più di frequente e vengono usati in questa guida introduttiva.  

Per caricare un file in un BLOB, aprire il file usando **os.Open**. È quindi possibile caricare il file nel percorso specificato usando una delle API REST, ovvero PutBlob, PutBlock/PutBlockList. 

In alternativa, l'SDK offre [API generali](https://github.com/Azure/azure-storage-blob-go/blob/master/2016-05-31/azblob/highlevel.go) basate sulle API REST specifiche. Ad esempio, la funzione ***UploadFileToBlockBlob*** usa operazioni PutBlock per caricare simultaneamente un file in blocchi per ottimizzare la velocità effettiva. Se le dimensioni del file sono inferiori a 256 MB, la funzione usa PutBlob per completare il trasferimento in una singola transazione.

Nell'esempio seguente il file viene caricato in un contenitore denominato **quickstartblobs-[stringacasuale]**.

```go
// Here's how to upload a blob.
blobURL := containerURL.NewBlockBlobURL(fileName)
file, err := os.Open(fileName)
handleErrors(err)

// You can use the low-level PutBlob API to upload files. Low-level APIs are simple wrappers for the Azure Storage REST APIs.
// Note that PutBlob can upload up to 256MB data in one shot. Details: https://docs.microsoft.com/en-us/rest/api/storageservices/put-blob
// Following is commented out intentionally because we will instead use UploadFileToBlockBlob API to upload the blob
// _, err = blobURL.PutBlob(ctx, file, azblob.BlobHTTPHeaders{}, azblob.Metadata{}, azblob.BlobAccessConditions{})
// handleErrors(err)

// The high-level API UploadFileToBlockBlob function uploads blocks in parallel for optimal performance, and can handle large files as well.
// This function calls PutBlock/PutBlockList for files larger 256 MBs, and calls PutBlob for any file smaller
fmt.Printf("Uploading the file with blob name: %s\n", fileName)
_, err = azblob.UploadFileToBlockBlob(ctx, file, blobURL, azblob.UploadToBlockBlobOptions{
    BlockSize:   4 * 1024 * 1024,
    Parallelism: 16})
handleErrors(err)
```

### <a name="list-the-blobs-in-a-container"></a>Elencare i BLOB in un contenitore

Ottenere un elenco di file nel contenitore usando il metodo **ListBlobs** su **ContainerURL**. ListBlobs restituisce singoli segmenti di BLOB, fino a un massimo di 5000, a partire dal valore specificato per **Marker**. Usare un valore Marker vuoto per avviare l'enumerazione dall'inizio. I nomi dei BLOB vengono restituiti in ordine lessicografico. Dopo avere ottenuto un segmento, elaborarlo e quindi chiamare di nuovo ListBlobs passando il valore Marker restituito in precedenza.  

```go
// List the blobs in the container
for marker := (azblob.Marker{}); marker.NotDone(); {
    // Get a result segment starting with the blob indicated by the current Marker.
    listBlob, err := containerURL.ListBlobs(ctx, marker, azblob.ListBlobsOptions{})
    handleErrors(err)

    // ListBlobs returns the start of the next segment; you MUST use this to get
    // the next segment (after processing the current result segment).
    marker = listBlob.NextMarker

    // Process the blobs returned in this result segment (if the segment is empty, the loop body won't execute)
    for _, blobInfo := range listBlob.Blobs.Blob {
        fmt.Print("Blob name: " + blobInfo.Name + "\n")
    }
}
```

### <a name="download-the-blob"></a>Scaricare il BLOB

Scaricare i BLOB usando il metodo **GetBlob** specifico su BlobURL. In alternativa è possibile creare un flusso e leggere intervalli dal flusso usando l'API **NewDownloadStream** generale fornita in [highlevel.go](https://github.com/Azure/azure-storage-blob-go/blob/master/2016-05-31/azblob/highlevel.go). La funzione NewDownloadStream esegue nuovi tentativi in caso di errore di connessione, mentre l'API GetBlob esegue nuovi tentativi solo con codici di stato HTTP come 503 (Server occupato). Il codice seguente scarica il BLOB usando la funzione **NewDownloadStream**. I contenuti del BLOB vengono scritti in un buffer e vengono visualizzati nella console.

```go
// Here's how to download the blob. NOTE: This method automatically retries if the connection fails
// during download (the low-level GetBlob function does NOT retry errors when reading from its stream).
stream := azblob.NewDownloadStream(ctx, blobURL.GetBlob, azblob.DownloadStreamOptions{})
downloadedData := &bytes.Buffer{}
_, err = downloadedData.ReadFrom(stream)
handleErrors(err)
```

### <a name="clean-up-resources"></a>Pulire le risorse
Se i BLOB caricati in questa guida introduttiva non sono più necessari, è possibile eliminare l'intero contenitore usando il metodo **Delete**. 

```go
// Cleaning up the quick start by deleting the container and the file created locally
fmt.Printf("Press the enter key to delete the sample files, example container, and exit the application.\n")
bufio.NewReader(os.Stdin).ReadBytes('\n')
fmt.Printf("Cleaning up.\n")
containerURL.Delete(ctx, azblob.ContainerAccessConditions{})
file.Close()
os.Remove(fileName)
```

## <a name="next-steps"></a>Passaggi successivi
 
In questa guida introduttiva è stato descritto il trasferimento di file tra il disco locale e Archiviazione BLOB di Azure con Go. Per altre informazioni su Azure Storage Blob SDK, vedere il [codice sorgente](https://github.com/Azure/azure-storage-blob-go/) e le [informazioni di riferimento sull'API](https://godoc.org/github.com/Azure/azure-storage-blob-go/2016-05-31/azblob).
