---
title: "Rendere persistente l'output di processi e attività in Archiviazione di Azure con l'API del servizio Azure Batch | Microsoft Docs"
description: "Informazioni su come usare l'API del servizio Batch per rendere persistente l'output di attività e processi Batch in Archiviazione di Azure."
services: batch
author: tamram
manager: timlt
editor: 
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 06/16/2017
ms.author: tamram
ms.openlocfilehash: 2530b7c20347b9fb58aee4dfe693847cf3911741
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2017
---
# <a name="persist-task-data-to-azure-storage-with-the-batch-service-api"></a>Rendere persistenti i dati delle attività in Archiviazione di Azure con l'API del servizio Batch

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

A partire dalla versione 2017-05-01, l'API del servizio Batch supporta l'archiviazione permanente dei dati di output in Archiviazione di Azure per le attività e le attività di gestione processo eseguite sui pool con la configurazione della macchina virtuale. Quando si aggiunge un'attività, è possibile specificare un contenitore in Archiviazione di Azure come destinazione per l'output dell'attività. Il servizio Batch scrive quindi i dati di output in tale contenitore al completamento dell'attività.

Un vantaggio dell'uso dell'API del servizio Batch per rendere persistente l'output dell'attività è il fatto di non avere la necessità di l'applicazione eseguita dall'attività. Con poche semplici modifiche dell'applicazione client, è invece possibile rendere persistente l'output dell'attività dall'interno del codice che crea l'attività.   

## <a name="when-do-i-use-the-batch-service-api-to-persist-task-output"></a>Quando è appropriato usare l'API del servizio Batch per rendere persistente l'output delle attività?

Il servizio Azure Batch offre diversi modi per rendere persistente l'output delle attività. Usare l'API del servizio Batch è un approccio pratico adatto in particolar modo per gli scenari seguenti:

- Si vuole scrivere codice per rendere persistente l'output dell'attività dall'interno dell'applicazione client, senza modificare l'applicazione eseguita dall'attività.
- Si vuole rendere persistente l'output delle attività del servizio Batch e delle attività del gestore di processi create con la configurazione della macchina virtuale.
- Si vuole rendere persistente l'output in un contenitore di Archiviazione di Azure con un nome arbitrario.
- Si vuole rendere persistente l'output in un contenitore di Archiviazione di Azure denominato in base agli [standard di Batch File Conventions](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions). 

Se lo scenario è diverso da quelli sopra elencati, potrebbe essere necessario prendere in considerazione un approccio diverso. Ad esempio, l'API del servizio Batch attualmente non supporta flussi dell'output in Archiviazione di Azure durante l'esecuzione dell'attività. Per questa esigenza, prendere in considerazione l'uso della libreria Batch File Conventions, disponibile per .NET. Per altri linguaggi, sarà necessario implementare una soluzione personalizzata. Per altre informazioni sulle opzioni per rendere persistente l'output delle attività, vedere [Rendere persistente l'output di processi e attività](batch-task-output.md). 

## <a name="create-a-container-in-azure-storage"></a>Creare un contenitore in Archiviazione di Azure

Per rendere persistente l'output delle attività in Archiviazione di Azure, è necessario creare un contenitore che funge da destinazione per i file di output. Creare il contenitore prima di eseguire l'attività, preferibilmente prima di inviare il processo. Per creare il contenitore, usare la libreria client o l'SDK di Archiviazione di Azure appropriati. Per altre informazioni sulle API di Archiviazione di Azure, vedere la [documentazione su Archiviazione di Azure](https://docs.microsoft.com/azure/storage/).

Se si scrive l'applicazione in C#, ad esempio, usare la [libreria client di Archiviazione di Azure per .NET](https://www.nuget.org/packages/WindowsAzure.Storage/). L'esempio seguente mostra come creare un contenitore:

```csharp
CloudBlobContainer container = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
await conainer.CreateIfNotExists();
```

## <a name="get-a-shared-access-signature-for-the-container"></a>Ottenere una firma di accesso condiviso per il contenitore

Dopo aver creato il contenitore, è possibile ottenere una firma di accesso condiviso (SAS) con accesso in scrittura al contenitore. Una firma di accesso condiviso consente l'accesso delegato al contenitore. La firma di accesso condiviso concede l'accesso con un set specificato di autorizzazioni e per un intervallo di tempo specificato. Il servizio Batch deve disporre di una firma di accesso condiviso con autorizzazioni di scrittura per scrivere l'output delle attività del contenitore. Per altre informazioni sulle firme di accesso condiviso, vedere [Uso delle firme di \(accesso condiviso\) in Archiviazione di Azure](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

Quando si ottiene una firma di accesso condiviso tramite le API di Archiviazione di Azure, l'API restituisce una stringa di token di firma di accesso condiviso. Questa stringa di token include tutti i parametri della firma di accesso condiviso, incluse le autorizzazioni e l'intervallo di validità della firma di accesso condiviso. Per usare la firma di accesso condiviso per accedere a un contenitore in Archiviazione di Azure, è necessario aggiungere la stringa di token di firma di accesso all'URI della risorsa. L'URI della risorsa, insieme al token della firma di accesso condiviso aggiunto, consente l'accesso autenticato ad Archiviazione di Azure.

L'esempio seguente mostra come ottenere una stringa di token di firma di accesso condiviso in sola scrittura per il contenitore, quindi aggiunge la firma di accesso condiviso all'URI del contenitore:

```csharp
string containerSasToken = container.GetSharedAccessSignature(new SharedAccessBlobPolicy()
{
    SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddDays(1),
    Permissions = SharedAccessBlobPermissions.Write
});

string containerSasUrl = container.Uri.AbsoluteUri + containerSasToken; 
```

## <a name="specify-output-files-for-task-output"></a>Specificare i file di output per l'output dell'attività

Per specificare i file di output per un'attività, creare una raccolta di oggetti [OutputFile](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfile) e assegnarla alla proprietà [CloudTask.OutputFiles](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudtask.outputfiles#Microsoft_Azure_Batch_CloudTask_OutputFiles) quando si crea l'attività. 

L'esempio di codice .NET seguente crea un'attività che scrive numeri casuali in un file denominato `output.txt`. Nell'esempio viene creato un file di output per `output.txt` da scrivere nel contenitore. L'esempio crea anche i file di output per gli eventuali file di log corrispondenti al modello di file `std*.txt` (_ad esempio_, `stdout.txt` e `stderr.txt`). L'URL del contenitore richiede la firma di accesso condiviso creata in precedenza per il contenitore. Il servizio Batch usa la firma di accesso condiviso per autenticare l'accesso al contenitore: 

```csharp
new CloudTask(taskId, "cmd /v:ON /c \"echo off && set && (FOR /L %i IN (1,1,100000) DO (ECHO !RANDOM!)) > output.txt\"")
{
    OutputFiles = new List<OutputFile>
    {
        new OutputFile(
            filePattern: @"..\std*.txt",
            destination: new OutputFileDestination(
         new OutputFileBlobContainerDestination(
                    containerUrl: containerSasUrl,
                    path: taskId)),
            uploadOptions: new OutputFileUploadOptions(
            uploadCondition: OutputFileUploadCondition.TaskCompletion)),
        new OutputFile(
            filePattern: @"output.txt",
            destination: 
         new OutputFileDestination(new OutputFileBlobContainerDestination(
                    containerUrl: containerSasUrl,
                    path: taskId + @"\output.txt")),
            uploadOptions: new OutputFileUploadOptions(
            uploadCondition: OutputFileUploadCondition.TaskCompletion)),
}
```

### <a name="specify-a-file-pattern-for-matching"></a>Specificare un modello di file per la corrispondenza

Quando si specifica un file di output, è possibile usare la proprietà [OutputFile.FilePattern](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfile.filepattern#Microsoft_Azure_Batch_OutputFile_FilePattern) per specificare un modello di file per la corrispondenza. Il modello di file potrebbe corrispondere a zero file, un singolo file o un set di file creati dall'attività.

La proprietà **FilePattern** supporta i caratteri jolly standard del file system, ad esempio `*` (per corrispondenze non ricorsive) e `**` (per corrispondenze ricorsive). Ad esempio, l'esempio di codice precedente specifica il modello di file da usare per trovare corrispondenze per `std*.txt` in modo non ricorsivo: 

`filePattern: @"..\std*.txt"`

Per caricare un singolo file, specificare un modello di file senza caratteri jolly. Ad esempio, l'esempio di codice precedente specifica il modello di file da usare per trovare corrispondenze per `output.txt`:

`filePattern: @"output.txt"`

### <a name="specify-an-upload-condition"></a>Specificare una condizione di caricamento

La proprietà [OutputFileUploadOptions.UploadCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileuploadoptions.uploadcondition#Microsoft_Azure_Batch_OutputFileUploadOptions_UploadCondition) consente il caricamento condizionale dei file di output. Uno scenario comune consiste nel caricare un set di file se l'attività ha esito positivo e un set di file diverso, in caso di errore. Ad esempio, può essere necessario caricare i file di log dettagliati solo quando l'attività ha esito negativo e viene terminata con un codice di uscita diverso da zero. Analogamente, può essere utile caricare il file dei risultati solo se l'attività ha esito positivo, perché tali file potrebbero essere mancanti o incompleti se l'attività non riesce.

L'esempio di codice precedente imposta la proprietà **UploadCondition** su **TaskCompletion**. Questa impostazione specifica che il file deve essere caricato dopo aver completato le attività, indipendentemente dal valore del codice di uscita. 

`uploadCondition: OutputFileUploadCondition.TaskCompletion`

Per altre impostazioni, vedere l'enumerazione [OutputFileUploadCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.common.outputfileuploadcondition).

### <a name="disambiguate-files-with-the-same-name"></a>Eliminare le ambiguità causate da file con lo stesso nome

Le attività in un processo possono produrre file con lo stesso nome. Ad esempio, i file `stdout.txt` e `stderr.txt` vengono creati per ogni attività eseguita in un processo. Dato che ogni attività viene eseguita in un contesto specifico, questi file non sono in conflitto nel file system del nodo. Quando si caricano file da più attività in un contenitore condiviso, tuttavia, è necessario evitare ambiguità tra i file con lo stesso nome.

La proprietà [OutputFileBlobContainerDestination.Path](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination.path#Microsoft_Azure_Batch_OutputFileBlobContainerDestination_Path) specifica il BLOB o la directory virtuale di destinazione per i file di output. È possibile usare la proprietà **Path** come nome del BLOB o della directory virtuale, in modo che i file di output con lo stesso nome abbiano un nome univoco in Archiviazione di Azure. L'uso dell'ID attività nel percorso è un buon metodo per garantire nomi univoci e identificare facilmente i file.

Se la proprietà **FilePattern** è impostata su un'espressione con caratteri jolly, tutti i file che corrispondono al modello vengono caricati nella directory virtuale specificata dalla proprietà **Path**. Ad esempio, se il contenitore è `mycontainer`, l'ID attività è `mytask` e il modello di file è `..\std*.txt`, l'URI assoluto per i file di output in Archiviazione di Azure sarà simile a:

```
https://myaccount.blob.core.windows.net/mycontainer/mytask/stderr.txt
https://myaccount.blob.core.windows.net/mycontainer/mytask/stdout.txt
```

Se la proprietà **FilePattern** è impostata su a un singolo nome di file, ovvero non contiene caratteri jolly, il valore della proprietà **Path** specifica il nome completo del BLOB. Se si prevedono conflitti di nome per un singolo file da più attività, includere il nome della directory virtuale come parte del nome del file per evitare ambiguità. Ad esempio, impostare la proprietà **Path** in modo da includere l'ID attività, il carattere di delimitazione (in genere una barra rovesciata) e il nome del file:

`path: taskId + @"/output.txt"`

L'URI assoluto dei file di output per un set di attività sarà simile a:

```
https://myaccount.blob.core.windows.net/mycontainer/task1/output.txt
https://myaccount.blob.core.windows.net/mycontainer/task2/output.txt
```

Per altre informazioni sulle directory virtuali in Archiviazione di Azure, vedere [Elencare i BLOB in un contenitore](../storage/blobs/storage-dotnet-how-to-use-blobs.md#list-the-blobs-in-a-container).


## <a name="diagnose-file-upload-errors"></a>Diagnosticare gli errori di caricamento file

Se si verifica un errore di caricamento dei file di output in Archiviazione di Azure, l'attività passa allo stato **Completato** e viene impostata la proprietà [TaskExecutionInformation.FailureInformation](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskexecutioninformation.failureinformation#Microsoft_Azure_Batch_TaskExecutionInformation_FailureInformation). Esaminare la proprietà **FailureInformation** per determinare l'errore che si è verificato. Quello che segue è un esempio di errore che si verifica al momento del caricamento di file se non è possibile trovare il contenitore: 

```
Category: UserError
Code: FileUploadContainerNotFound
Message: One of the specified Azure container(s) was not found while attempting to upload an output file
```

Per ogni caricamento di file, il servizio Batch scrive due file di log nel nodo di calcolo, `fileuploadout.txt` e `fileuploaderr.txt`. È possibile esaminare questi file di log per ottenere ulteriori informazioni su un errore specifico. Nei casi in cui il caricamento del file non è mai stato tentato, ad esempio perché non può essere eseguita l'attività stessa, questi file di log non esisteranno.

## <a name="diagnose-file-upload-performance"></a>Diagnosticare le prestazioni di caricamento file

Lo stato di caricamento viene registrato nel file `fileuploadout.txt`. È possibile esaminare questo file per ottenere ulteriori informazioni su quanto tempo richiede il caricamento del file. Tenere presente che esistono molti fattori che possono influire sulle prestazioni del caricamento, tra cui la dimensione del nodo, altre attività in esecuzione sul nodo durante il caricamento, il fatto che il contenitore di destinazione si trovi nella stessa area del pool di Batch, il numero di nodi in caricamento nell'account di archiviazione nello stesso momento e così via.

## <a name="use-the-batch-service-api-with-the-batch-file-conventions-standard"></a>Usare l'API del servizio Batch con lo standard Batch File Conventions

Quando si rende persistente l'output con l'API del servizio Batch, è possibile assegnare il nome preferito al contenitore di destinazione e ai BLOB. Si può anche scegliere il nome in base allo [standard Batch File Conventions](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions). Lo standard File Conventions determina i nomi del contenitore e del BLOB di destinazione in Archiviazione di Azure per un file di output specificato in base ai nomi del processo e dell'attività. Se si usa lo standard File Conventions per la denominazione dei file di output, i file di output sono disponibili per la visualizzazione nel [portale di Azure](https://portal.azure.com).

Se si sviluppa in C#, è possibile usare i metodi inclusi nella [libreria Batch File Conventions per .NET](https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files). Questa libreria crea automaticamente i percorsi di BLOB e contenitori con nomi appropriati. Ad esempio, è possibile chiamare l'API per ottenere il nome corretto per il contenitore, in base al nome del processo:

```csharp
string containerName = job.OutputStorageContainerName();
```

È possibile usare il metodo [CloudJobExtensions.GetOutputStorageContainerUrl](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.conventions.files.cloudjobextensions.getoutputstoragecontainerurl) per restituire un URL di firma di accesso condiviso usato per scrivere nel contenitore. È quindi possibile passare questa firma di accesso condiviso al costruttore [OutputFileBlobContainerDestination](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination).

Se si sviluppa in un linguaggio diverso da C#, sarà necessario implementare manualmente lo standard File Conventions.

## <a name="code-sample"></a>Esempio di codice

Il progetto di esempio [PersistOutputs][github_persistoutputs] è uno degli [esempi di codice di Azure Batch][github_samples] disponibili in GitHub. Questa soluzione di Visual Studio descrive come usare la libreria client Batch per .NET per rendere persistente l'output dell'attività in una risorsa di archiviazione permanente. Per eseguire l'esempio, seguire questa procedura:

1. Aprire il progetto in **Visual Studio 2015 o in una versione più recente**.
2. Aggiungere **le credenziali dell'account** di archiviazione e Batch a **AccountSettings.settings** nel progetto Microsoft.Azure.Batch.Samples.Common.
3. **Compilare** , ma non eseguire, la soluzione. Se richiesto, ripristinare tutti i pacchetti NuGet.
4. Usare il portale di Azure per caricare un [pacchetto dell'applicazione](batch-application-packages.md) per **PersistOutputsTask**. Includere `PersistOutputsTask.exe` e relativi assembly dipendenti nel pacchetto ZIP, impostare l'ID applicazione su "PersistOutputsTask" e la versione del pacchetto dell'applicazione su "1.0".
5. **Avviare**, ovvero eseguire, il progetto **PersistOutputs**.
6. Quando viene richiesto di scegliere la tecnologia di persistenza da usare per l'esecuzione dell'esempio, immettere **2** per eseguire l'esempio con l'API del servizio Batch per rendere persistente l'output dell'attività.
7. Se si desidera, eseguire nuovamente l'esempio, immettere **3** per rendere persistente l'output con l'API del servizio Batch e definire anche i nomi per il percorso del BLOB e del contenitore di destinazione in base allo standard File Conventions.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni su come rendere persistente l'output delle attività con la libreria File Conventions per .NET, vedere [Rendere persistenti i dati di attività e processi in Archiviazione di Azure con la libreria Batch File Conventions per .NET](batch-task-output-file-conventions.md).
- Per informazioni su altri approcci per rendere persistente l'output delle attività in Azure Batch, vedere [Rendere persistente l'output di processi e attività](batch-task-output.md).

[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
