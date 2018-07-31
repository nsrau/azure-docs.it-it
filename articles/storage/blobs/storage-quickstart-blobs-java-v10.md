---
title: "Guida introduttiva di Azure: Creare un BLOB nell'archivio oggetti con Java Storage SDK V10 | Microsoft Docs"
description: In questa guida introduttiva si crea un contenitore nell'archivio oggetti (BLOB di Azure), si carica un file, si elencano gli oggetti e si esegue il download con Java Storage SDK.
services: storage
author: roygara
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 07/02/2018
ms.author: rogarana
ms.openlocfilehash: 749badbc0bf5286c06da02f092933abbefda7ceb
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2018
ms.locfileid: "39185415"
---
# <a name="quickstart-upload-download-and-list-blobs-by-using-the-java-storage-sdk-v10-preview"></a>Guida introduttiva: Caricare, scaricare ed elencare BLOB con Java Storage SDK V10 (anteprima)

In questa guida introduttiva viene illustrato come usare il nuovo Java Storage SDK per caricare, scaricare ed elencare BLOB in blocchi in un contenitore di Archiviazione BLOB di Azure. Il nuovo Java SDK usa il modello di programmazione reattiva con RxJava, che offre operazioni asincrone. Altre informazioni sulle [estensioni reattive di RxJava per la macchina virtuale Java](https://github.com/ReactiveX/RxJava). 

## <a name="prerequisites"></a>Prerequisiti

Installare e configurare queste applicazioni:

* [Maven](http://maven.apache.org/download.cgi) per usare la riga di comando o qualsiasi altro ambiente di sviluppo integrato per Java preferito
* [JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html)

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Scaricare l'applicazione di esempio

L'[applicazione di esempio](https://github.com/Azure-Samples/storage-blobs-java-v10-quickstart) utilizzata in questa guida rapida è un'applicazione console di base. 

Usare [git](https://git-scm.com/) per scaricare una copia dell'applicazione nell'ambiente di sviluppo.

```bash
git clone https://github.com/Azure-Samples/storage-blobs-java-v10-quickstart.git
```

Questo comando consente di duplicare il repository nella cartella locale git.

Al termine dell'importazione del progetto, aprire **Quickstart.java** in **src/main/java/quickstart**.

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Configurare la stringa di connessione di archiviazione
Questa soluzione richiede l'archiviazione sicura del nome e della chiave dell'account di archiviazione. Archiviarli in variabili di ambiente locali per il computer che esegue l'esempio. Seguire l'esempio per Linux o per Windows, in base al sistema operativo, per creare le variabili di ambiente.

### <a name="linux-example"></a>Esempio per Linux

```
export AZURE_STORAGE_ACCOUNT="<youraccountname>"
export AZURE_STORAGE_ACCESS_KEY="<youraccountkey>"
```

### <a name="windows-example"></a>Esempio per Windows

```
setx AZURE_STORAGE_ACCOUNT "<youraccountname>"
setx AZURE_STORAGE_ACCESS_KEY "<youraccountkey>"
```

## <a name="run-the-sample"></a>Eseguire l'esempio

Questo esempio crea un file di test nella directory predefinita **AppData\Local\Temp**, per gli utenti di Windows. Quindi viene richiesto di eseguire i passaggi seguenti:

1. Immettere i comandi per caricare il file di test in Archiviazione BLOB di Azure.
2. Elencare i BLOB nel contenitore.
3. Scaricare il file caricato con un nuovo nome in modo che sia possibile confrontare il file nuovo e quello precedente. 

Se si vuole eseguire l'esempio usando Maven dalla riga di comando, aprire una shell e passare a **storage-blobs-java-v10-quickstart** all'interno della directory clonata. Immettere quindi `mvn compile exec:java`.

Questo esempio mostra l'output se si esegue l'applicazione in Windows.

```
Created quickstart container
Enter a command
(P)utBlob | (L)istBlobs | (G)etBlob | (D)eleteBlobs | (E)xitSample
# Enter a command :
P
Uploading the sample file into the container: https://<storageaccount>.blob.core.windows.net/quickstart
# Enter a command :
L
Listing blobs in the container: https://<storageaccount>.blob.core.windows.net/quickstart
Blob name: SampleBlob.txt
# Enter a command :
G
Get the blob: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt
The blob was downloaded to C:\Users\<useraccount>\AppData\Local\Temp\downloadedFile13097087873115855761.txt
# Enter a command :
D
Delete the blob: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt

# Enter a command :
>> Blob deleted: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt
E
Cleaning up the sample and exiting!
```

Avendo il controllo dell'esempio, immettere i comandi per eseguire il codice. Per gli input viene fatta distinzione tra maiuscole e minuscole.

È anche possibile usare uno strumento come [Azure Storage Explorer](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) per visualizzare i file nell'archivio BLOB. Azure Storage Explorer è uno strumento multipiattaforma gratuito che consente di accedere alle informazioni dell'account di archiviazione. 

Verificare i file. Selezionare quindi **E** ed **Enter** per terminare la demo ed eliminare i file di test. Ora che si conosce il risultato dell'esempio, aprire il file **Quickstart.java** per esaminare il codice. 

## <a name="understand-the-sample-code"></a>Informazioni sul codice di esempio

Nelle sezioni seguenti viene descritto in dettaglio il codice di esempio, per consentire di comprenderne il funzionamento.

### <a name="get-references-to-the-storage-objects"></a>Ottenere i riferimenti agli oggetti di archiviazione

Prima di tutto, creare i riferimenti agli oggetti usati per accedere all'archivio BLOB e gestirlo. Questi oggetti sono basati l'uno sull'altro. Ognuno viene usato da quello successivo nell'elenco.

1. Creare un'istanza dell'oggetto **StorageURL** che punti all'account di archiviazione.

    * L'oggetto [StorageURL](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._storage_u_r_l?view=azure-java-preview) è una rappresentazione dell'account di archiviazione. Usarlo per generare una nuova pipeline. 
    * Una pipeline è un set di criteri usati per modificare richieste e risposte con meccanismi di autorizzazione, registrazione e ripetizione dei tentativi. Per altre informazioni, vedere [Pipeline HTTP](https://github.com/Azure/azure-storage-java/wiki/Azure-Storage-Java-V10-Overview#url-types--http-pipeline).  
    * Tramite la pipeline, creare un'istanza dell'oggetto [ServiceURL](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._service_u_r_l?view=azure-java-preview).
    * Tramite l'oggetto **ServiceURL** creare un'istanza di [ContainerURL](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._container_u_r_l?view=azure-java-preview).
    * **ContainerURL** è necessario per eseguire operazioni su contenitori BLOB.

2. Creare un'istanza dell'oggetto **ContainerURL** che rappresenta il contenitore a cui si accede. I contenitori vengono usati per organizzare i BLOB, così come si usano le cartelle nel computer per organizzare i file.

    * **ContainerURL** offre un punto di accesso al servizio contenitore. 
    * È possibile creare un'istanza dell'oggetto [BlobURL](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._blob_u_r_l?view=azure-java-preview) tramite [ContainerURL](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._container_u_r_l?view=azure-java-preview).
    * **BlobURL** è necessario creare i BLOB.

3. Creare un'istanza dell'oggetto **BlobURL** che punti al BLOB specifico a cui si è interessati. 

> [!IMPORTANT]
> I nomi dei contenitori devono essere in minuscolo. Per altre informazioni sui nomi di contenitori e BLOB, vedere [Naming and Referencing Containers, Blobs, and Metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Denominazione e riferimento a contenitori, BLOB e metadati).

### <a name="create-a-container"></a>Creare un contenitore 

In questa sezione viene creata un'istanza di **ContainerURL**, insieme a un nuovo contenitore. Il contenitore dell'esempio è denominato **quickstart**. 

Questo esempio usa [ContainerURL.create](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._container_u_r_l.create?view=azure-java-preview), quindi è possibile creare un nuovo contenitore ogni volta che si esegue l'esempio. In alternativa, è possibile creare il contenitore in anticipo in modo che non sia necessario crearlo nel codice.

```java
// Create a ServiceURL to call the Blob service. We will also use this to construct the ContainerURL
SharedKeyCredentials creds = new SharedKeyCredentials(accountName, accountKey);
// We are using a default pipeline here, you can learn more about it at https://github.com/Azure/azure-storage-java/wiki/Azure-Storage-Java-V10-Overview
final ServiceURL serviceURL = new ServiceURL(new URL("http://" + accountName + ".blob.core.windows.net"), StorageURL.createPipeline(creds, new PipelineOptions()));

// Let's create a container using a blocking call to Azure Storage
// If container exists, we'll catch and continue
containerURL = serviceURL.createContainerURL("quickstart");

try {
    ContainersCreateResponse response = containerURL.create(null, null).blockingGet();
    System.out.println("Container Create Response was " + response.statusCode());
} catch (RestException e){
    if (e instanceof RestException && ((RestException)e).response().statusCode() != 409) {
        throw e;
    } else {
        System.out.println("quickstart container already exists, resuming...");
    }
}
```

### <a name="upload-blobs-to-the-container"></a>Caricare i BLOB nel contenitore

L'archiviazione BLOB supporta BLOB in blocchi, BLOB di aggiunta e BLOB di pagine. I BLOB in blocchi sono i BLOB usati più frequentemente. Vengono usati in questa guida introduttiva. 

1. Per caricare un file in un BLOB, ottenere un riferimento al BLOB nel contenitore di destinazione. 
2. Dopo avere ottenuto il riferimento al BLOB, è possibile caricare un file usando una delle API seguenti:

    * API di basso livello. Sono esempi [BlockBlobURL.upload](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._block_blob_u_r_l.upload?view=azure-java-preview), detto anche PutBlob, e [BlockBlobURL.stageBlock](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._block_blob_u_r_l.stageblock?view=azure-java-preview#com_microsoft_azure_storage_blob__block_blob_u_r_l_stageBlock_String_Flowable_ByteBuffer__long_LeaseAccessConditions_), chiamato anche PutBLock, nell'istanza di **BlockBlobURL**. 

    * API di alto livello fornite nella [classe TransferManager](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._transfer_manager?view=azure-java-preview). Un esempio è il metodo [TransferManager.uploadFileToBlockBlob](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._transfer_manager.uploadfiletoblockblob?view=azure-java-preview). 

    Questa operazione crea il BLOB se non esiste già. Il BLOB viene sovrascritto se esiste già.

Il codice di esempio crea un file locale da usare per il caricamento e il download, archiviando il file da caricare come **sourceFile** e l'URL del BLOB in **blob**. Nell'esempio seguente il file viene caricato in un contenitore denominato **quickstart**.

```java
static void uploadFile(BlockBlobURL blob, File sourceFile) throws IOException {
    
    FileChannel fileChannel = FileChannel.open(sourceFile.toPath());
            
    // Uploading a file to the blobURL using the high-level methods available in TransferManager class
    // Alternatively call the Upload/StageBlock low-level methods from BlockBlobURL type
    TransferManager.uploadFileToBlockBlob(fileChannel, blob, 8*1024*1024, null)
        .subscribe(response-> {
            System.out.println("Completed upload request.");
            System.out.println(response.response().statusCode());
        });
}
```

I BLOB in blocchi possono essere qualsiasi tipo di file di testo o binario. I BLOB di pagine vengono usati principalmente per i file VHD usati per tornare alle macchine virtuali IaaS. I BLOB di aggiunta vengono usati per aggiungere dati alla fine e spesso vengono usati per la registrazione. La maggior parte degli oggetti presenti nell'archiviazione BLOB è costituita da BLOB in blocchi.

### <a name="list-the-blobs-in-a-container"></a>Elencare i BLOB in un contenitore

È possibile ottenere un elenco di oggetti in un contenitore usando [ContainerURL.listBlobsFlatSegment](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._container_u_r_l.listblobsflatsegment?view=azure-java-preview). Questo metodo restituisce fino a 5.000 oggetti in una sola volta con un marcatore di continuazione (o marcatore next) se nel contenitore sono presenti altri elementi da elencare. Creare una funzione helper che chiama se stessa ripetutamente quando è presente un marcatore next nella risposta **listBlobsFlatSegment** precedente.

```java
static void listBlobs(ContainerURL containerURL) {
    // Each ContainerURL.listBlobsFlatSegment call return up to maxResults (maxResults=10 passed into ListBlobOptions below).
    // To list all Blobs, we are creating a helper static method called listAllBlobs, 
    // and calling it after the initial listBlobsFlatSegment call
    ListBlobsOptions options = new ListBlobsOptions(null, null, 10);

    containerURL.listBlobsFlatSegment(null, options)
        .flatMap(containersListBlobFlatSegmentResponse -> 
            listAllBlobs(containerURL, containersListBlobFlatSegmentResponse))    
                .subscribe(response-> {
                    System.out.println("Completed list blobs request.");
                    System.out.println(response.statusCode());
                });
}

private static Single <ContainersListBlobFlatSegmentResponse> listAllBlobs(ContainerURL url, ContainersListBlobFlatSegmentResponse response) {                
    // Process the blobs returned in this result segment (if the segment is empty, blobs() will be null.
    if (response.body().blobs() != null) {
        for (Blob b : response.body().blobs().blob()) {
            String output = "Blob name: " + b.name();
            if (b.snapshot() != null) {
                output += ", Snapshot: " + b.snapshot();
            }
            System.out.println(output);
        }
    }
    else {
        System.out.println("There are no more blobs to list off.");
    }
    
    // If there is not another segment, return this response as the final response.
    if (response.body().nextMarker() == null) {
        return Single.just(response);
    } else {
        /*
        IMPORTANT: ListBlobsFlatSegment returns the start of the next segment; you MUST use this to get the next
        segment (after processing the current result segment
        */
            
        String nextMarker = response.body().nextMarker();

        /*
        The presence of the marker indicates that there are more blobs to list, so we make another call to
        listBlobsFlatSegment and pass the result through this helper function.
        */
            
    return url.listBlobsFlatSegment(nextMarker, new ListBlobsOptions(null, null,1))
        .flatMap(containersListBlobFlatSegmentResponse ->
            listAllBlobs(url, containersListBlobFlatSegmentResponse));
    }
}
```

### <a name="download-blobs"></a>Scaricare BLOB

Scaricare i BLOB nel disco locale usando [BlobURL.download](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._blob_u_r_l.download?view=azure-java-preview).

Il codice seguente consente di scaricare il BLOB caricato nella sezione precedente. Viene aggiunto il suffisso **_DOWNLOADED** al nome del BLOB in modo da poter visualizzare entrambi i file nel disco locale. 

```java
static void getBlob(BlockBlobURL blobURL, File sourceFile) {
    try {
        // Get the blob using the low-level download method in BlockBlobURL type
        // com.microsoft.rest.v2.util.FlowableUtil is a static class that contains helpers to work with Flowable
        blobURL.download(new BlobRange(0, Long.MAX_VALUE), null, false)
            .flatMapCompletable(response -> {
                AsynchronousFileChannel channel = AsynchronousFileChannel.open(Paths
                    .get(sourceFile.getPath()), StandardOpenOption.CREATE,  StandardOpenOption.WRITE);
                        return FlowableUtil.writeFile(response.body(), channel);
            }).doOnComplete(()-> System.out.println("The blob was downloaded to " + sourceFile.getAbsolutePath()))
            // To call it synchronously add .blockingAwait()
            .subscribe();
    } catch (Exception ex){
        System.out.println(ex.toString());
    }
}
```

### <a name="clean-up-resources"></a>Pulire le risorse

Se i BLOB caricati in questa guida introduttiva non sono più necessari, è possibile eliminare l'intero contenitore usando [ContainerURL.delete](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._container_u_r_l.delete?view=azure-java-preview). Questo metodo elimina anche i file nel contenitore.

```java
containerURL.delete(null).blockingGet();
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato descritto il trasferimento di file tra un disco locale e Archiviazione BLOB di Azure con Java. 

> [!div class="nextstepaction"]
> [Codice sorgente di Storage SDK V10 per Java](https://github.com/Azure/azure-storage-java/tree/New-Storage-SDK-V10-Preview)
> [Informazioni di riferimento sull'API](https://docs.microsoft.com/en-us/java/api/storage/client?view=azure-java-preview)
> [Altre informazioni su RxJava](https://github.com/ReactiveX/RxJava)
