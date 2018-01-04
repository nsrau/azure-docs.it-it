---
title: "Caricare grandi quantità di dati casuali in parallelo in archiviazione di Azure | Documenti Microsoft"
description: "Imparare a usare Azure SDK per caricare grandi quantità di dati casuali in parallelo a un account di archiviazione di Azure"
services: storage
documentationcenter: 
author: georgewallace
manager: jeconnoc
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 12/12/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: 98f3f69c6025d61caac20e13b573651854952432
ms.sourcegitcommit: 4256ebfe683b08fedd1a63937328931a5d35b157
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/23/2017
---
# <a name="upload-large-amounts-of-random-data-in-parallel-to-azure-storage"></a>Caricare grandi quantità di dati casuali in parallelo nell'archiviazione di Azure

Questa è la seconda di una serie di esercitazioni. Questa esercitazione viene illustrato come che si distribuisce un'applicazione che carica una grande quantità di dati casuali a un account di archiviazione di Azure.

Nella seconda parte della serie si apprenderà come:

> [!div class="checklist"]
> * Configurare la stringa di connessione
> * Compilare l'applicazione.
> * Eseguire l'applicazione
> * Convalidare il numero di connessioni

Archiviazione blob di Azure fornisce un servizio scalabile per l'archiviazione dei dati. Per garantire l'applicazione sia come garantendo così migliori prestazioni possibili, la comprensione del modo in cui è consigliabile works di archiviazione blob. È importante conoscere i limiti per i BLOB di Azure, per ulteriori informazioni su questi limiti, visitare: [obiettivi di scalabilità di archiviazione blob](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-blob-storage-scale-targets).

[Partizione di denominazione](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#subheading47) è un altro fattore importante quando si progetta un'applicazione elevata verrà eseguita tramite i BLOB. Archiviazione di Azure usa uno schema di partizionamento basato su intervallo per il bilanciamento del carico e scalabilità. Questa configurazione implica che i file con le convenzioni di denominazione simili o prefissi indirizzato alla stessa partizione. Questa logica include il nome del contenitore in cui i file vengono caricati. In questa esercitazione, utilizzare i file che dispongono di GUID per i nomi come contenuto anche come casuale. Questi vengono quindi caricati in cinque contenitori diversi con nomi casuali.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario avere completato l'esercitazione di archiviazione precedente: [creare una macchina virtuale e l'account di archiviazione per un'applicazione scalabile][previous-tutorial].

## <a name="remote-into-your-virtual-machine"></a>Remoto nella macchina virtuale

Utilizzare il comando seguente nel computer locale per creare una sessione desktop remoto con la macchina virtuale. Sostituire l'indirizzo IP con publicIPAddress della macchina virtuale. Quando richiesto, immettere le credenziali utilizzate durante la creazione della macchina virtuale.

```
mstsc /v:<publicIpAddress>
```

## <a name="configure-the-connection-string"></a>Configurare la stringa di connessione

Nel portale di Azure, passare all'account di archiviazione. Selezionare **le chiavi di accesso** in **impostazioni** nell'account di archiviazione. Copia il **stringa di connessione** dalla chiave primaria o secondaria. Accedere alla macchina virtuale creata nell'esercitazione precedente. Aprire un **prompt dei comandi** come amministratore ed eseguire il `setx` comando con il `/m` switch, questo comando consente di risparmiare una variabile di ambiente impostazione macchina. La variabile di ambiente non è disponibile fino a quando non si ricarica il **prompt dei comandi**. Sostituire  **\<storageConnectionString\>**  nell'esempio seguente:

```
setx storageconnectionstring "<storageConnectionString>" /m
```

Al termine, aprire un'altra **prompt dei comandi**, passare a `D:\git\storage-dotnet-perf-scale-app` e tipo `dotnet build` per compilare l'applicazione.

## <a name="run-the-application"></a>Eseguire l'applicazione

Accedere a `D:\git\storage-dotnet-perf-scale-app`.

Digitare `dotnet run` per eseguire l'applicazione. Alla prima esecuzione `dotnet` popola la cache locale del pacchetto, per migliorare la velocità di ripristino e abilitare l'accesso offline. Questo comando richiede a un minuto per completare e si verifica solo una volta.

```
dotnet run
```

L'applicazione crea cinque contenitori denominati in modo casuale e inizia a caricare i file nella directory di gestione temporanea per l'account di archiviazione. L'applicazione imposta il minimo di thread a 100 e [DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit(v=vs.110).aspx) a 100 per garantire che un numero elevato di connessioni simultanee è consentito quando esegue l'applicazione.

Oltre a impostare le impostazioni di limite di connessione e di threading di [BlobRequestOptions](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions?view=azure-dotnet) per il [UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet) metodo sono configurati per utilizzare il parallelismo e disattivare la convalida dell'hash MD5. I file vengono caricati in blocchi di 100 mb, questa configurazione offre prestazioni migliori, ma può essere costosa se si utilizza un valore di esecuzione rete come se si è verificato un errore l'intero blocco di 100 mb viene ripetuta.

|Proprietà|Valore|DESCRIZIONE|
|---|---|---|
|[ParallelOperationThreadCount](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.paralleloperationthreadcount?view=azure-dotnet)| 8| L'impostazione suddivide il blob in blocchi durante il caricamento. Per ottenere prestazioni più elevata, questo valore deve essere 8 volte il numero di core. |
|[DisableContentMD5Validation](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.disablecontentmd5validation?view=azure-dotnet)| true| Questa proprietà Disabilita verifica l'hash MD5 del contenuto caricato. Disabilitare la convalida MD5 produce un trasferimento più veloce. Ma non conferma la validità o l'integrità dei file trasferiti.   |
|[StorBlobContentMD5](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.storeblobcontentmd5?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_StoreBlobContentMD5)| false| Questa proprietà determina se un hash MD5 viene calcolato e archiviato con il file.   |
| [RetryPolicy](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.retrypolicy?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_RetryPolicy)| backoff 2 secondi con 10 massimo di tentativi |Determina i criteri di ripetizione delle richieste. Errori di connessione vengono ritentati, in questo esempio un [ExponentialRetry](/dotnet/api/microsoft.windowsazure.storage.retrypolicies.exponentialretry?view=azure-dotnet) criterio è configurato con un backoff 2 secondi e un numero massimo di tentativi pari a 10. Questa impostazione è importante quando l'applicazione sta per raggiungere raggiunge il [obiettivi di scalabilità di archiviazione blob](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-blob-storage-scale-targets).  |

Il `UploadFilesAsync` attività è illustrato nell'esempio seguente:

```csharp
private static async Task UploadFilesAsync()
{
    // Create random 5 characters containers to upload files to.
    CloudBlobContainer[] containers = await GetRandomContainersAsync();
    var currentdir = System.IO.Directory.GetCurrentDirectory();

    // path to the directory to upload
    string uploadPath = currentdir + "\\upload";
    Stopwatch time = Stopwatch.StartNew();
    try
    {
        Console.WriteLine("Iterating in directory: {0}", uploadPath);
        int count = 0;
        int max_outstanding = 100;
        int completed_count = 0;

        // Define the BlobRequestionOptions on the upload.
        // This includes defining an exponential retry policy to ensure that failed connections are retried with a backoff policy. As multiple large files are being uploaded
        // large block sizes this can cause an issue if an exponential retry policy is not defined.  Additionally parallel operations are enabled with a thread count of 8
        // This could be should be multiple of the number of cores that the machine has. Lastly MD5 hash validation is disabled for this example, this improves the upload speed.
        BlobRequestOptions options = new BlobRequestOptions
        {
            ParallelOperationThreadCount = 8,
            DisableContentMD5Validation = true,
            StoreBlobContentMD5 = false
        };
        // Create a new instance of the SemaphoreSlim class to define the number of threads to use in the application.
        SemaphoreSlim sem = new SemaphoreSlim(max_outstanding, max_outstanding);

        List<Task> tasks = new List<Task>();
        Console.WriteLine("Found {0} file(s)", Directory.GetFiles(uploadPath).Count());

        // Iterate through the files
        foreach (string path in Directory.GetFiles(uploadPath))
        {
            // Create random file names and set the block size that is used for the upload.
            var container = containers[count % 5];
            string fileName = Path.GetFileName(path);
            Console.WriteLine("Uploading {0} to container {1}.", path, container.Name);
            CloudBlockBlob blockBlob = container.GetBlockBlobReference(fileName);

            // Set block size to 100MB.
            blockBlob.StreamWriteSizeInBytes = 100 * 1024 * 1024;
            await sem.WaitAsync();

            // Create tasks for each file that is uploaded. This is added to a collection that executes them all asyncronously.  
            tasks.Add(blockBlob.UploadFromFileAsync(path, null, options, null).ContinueWith((t) =>
            {
                sem.Release();
                Interlocked.Increment(ref completed_count);
            }));
            count++;
        }

        // Creates an asynchronous task that completes when all the uploads complete.
        await Task.WhenAll(tasks);

        time.Stop();

        Console.WriteLine("Upload has been completed in {0} seconds. Press any key to continue", time.Elapsed.TotalSeconds.ToString());

        Console.ReadLine();
    }
    catch (DirectoryNotFoundException ex)
    {
        Console.WriteLine("Error parsing files in the directory: {0}", ex.Message);
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }
}
```

L'esempio seguente è un output troncato applicazione in esecuzione in un sistema di Windows.

```
Created container https://mystorageaccount.blob.core.windows.net/9efa7ecb-2b24-49ff-8e5b-1d25e5481076
Created container https://mystorageaccount.blob.core.windows.net/bbe5f0c8-be9e-4fc3-bcbd-2092433dbf6b
Created container https://mystorageaccount.blob.core.windows.net/9ac2f71c-6b44-40e7-b7be-8519d3ba4e8f
Created container https://mystorageaccount.blob.core.windows.net/47646f1a-c498-40cd-9dae-840f46072180
Created container https://mystorageaccount.blob.core.windows.net/38b2cdab-45fa-4cf9-94e7-d533837365aa
Iterating in directory: D:\git\storage-dotnet-perf-scale-app\upload
Found 50 file(s)
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\1d596d16-f6de-4c4c-8058-50ebd8141e4d.txt to container 9efa7ecb-2b24-49ff-8e5b-1d25e5481076.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\242ff392-78be-41fb-b9d4-aee8152a6279.txt to container bbe5f0c8-be9e-4fc3-bcbd-2092433dbf6b.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\38d4d7e2-acb4-4efc-ba39-f9611d0d55ef.txt to container 9ac2f71c-6b44-40e7-b7be-8519d3ba4e8f.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\45930d63-b0d0-425f-a766-cda27ff00d32.txt to container 47646f1a-c498-40cd-9dae-840f46072180.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\5129b385-5781-43be-8bac-e2fbb7d2bd82.txt to container 38b2cdab-45fa-4cf9-94e7-d533837365aa.
...
Upload has been completed in 142.0429536 seconds. Press any key to continue
```

### <a name="validate-the-connections"></a>Convalida delle connessioni

Mentre vengono caricati i file, è possibile verificare il numero di connessioni simultanee all'account di archiviazione. Aprire un **prompt dei comandi** e tipo `netstat -a | find /c "blob:https"`. Questo comando Mostra il numero di connessioni attualmente aperti con `netstat`. L'esempio seguente mostra un output simile a ciò che viene visualizzato quando si esegue manualmente l'esercitazione. Come si può notare nell'esempio, 800 connessioni aperte durante il caricamento di file casuali all'account di archiviazione. Questo valore cambia in tutta l'esecuzione del caricamento. Caricando in blocchi di blocco parallelo, la quantità di tempo necessaria per il trasferimento del contenuto è notevolmente ridotte.

```
C:\>netstat -a | find /c "blob:https"
800

C:\>
```

## <a name="next-steps"></a>Passaggi successivi

Nella seconda parte della serie, illustrando il caricamento di grandi quantità di dati casuali in un account di archiviazione in parallelo, ad esempio come:

> [!div class="checklist"]
> * Configurare la stringa di connessione
> * Compilare l'applicazione.
> * Eseguire l'applicazione
> * Convalidare il numero di connessioni

Spostare la terza parte della serie per il download di grandi quantità di dati da un account di archiviazione.

> [!div class="nextstepaction"]
> [Caricare grandi quantità di file di grandi dimensioni in parallelo a un account di archiviazione](storage-blob-scalable-app-download-files.md)

[previous-tutorial]: storage-blob-scalable-app-create-vm.md
