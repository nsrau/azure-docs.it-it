---
title: Trasferire dati con la libreria per lo spostamento dei dati per .NET
titleSuffix: Azure Storage
description: Usare la libreria per lo spostamento dei dati per spostare o copiare dati da o verso BLOB e contenuto del file. Copiare i dati in archiviazione di Azure da file locali o copiare i dati all'interno o tra account di archiviazione. Migrare facilmente i dati in archiviazione di Azure.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 22dae518a45d5c4af20044d5f3eb88e764e92c8b
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895122"
---
# <a name="transfer-data-with-the-data-movement-library"></a>Trasferire dati con la libreria per lo spostamento dei dati

La libreria per lo spostamento dei dati di archiviazione di Azure è una libreria open source multipiattaforma progettata per prestazioni elevate di caricamento, download e copia di BLOB e file. Questa libreria è il principale framework di spostamento dei dati alla base di [AzCopy](../storage-use-azcopy.md). La libreria per lo spostamento dei dati fornisce metodi pratici che non sono disponibili nella libreria client di archiviazione di Azure per .NET. Questi metodi offrono la possibilità di impostare il numero di operazioni parallele, tenere traccia dello stato del trasferimento, riprendere facilmente un trasferimento annullato e molto altro ancora.

La libreria usa inoltre .NET Core, utile quando si creano app .NET per Windows, Linux e macOS. Per altre informazioni su .NET Core, consultare la [documentazione di .NET Core](https://dotnet.github.io/). La libreria è compatibile anche con le app .NET Framework tradizionali per Windows.

In questo documento viene spiegato come creare un'applicazione console di .NET Core da eseguire su Windows, Linux e macOS e vengono dimostrati gli scenari seguenti:

- Caricare file e directory nell'archivio BLOB.
- Definire il numero di operazioni parallele durante il trasferimento dati.
- Monitorare lo stato del trasferimento dati.
- Riprendere un trasferimento dati annullato.
- Copiare file dall'URL all'archivio BLOB.
- Copiare da archivio BLOB ad archivio BLOB.

## <a name="prerequisites"></a>Prerequisiti

- [Visual Studio Code](https://code.visualstudio.com/)
- Un [account di archiviazione di Azure](storage-quickstart-create-account.md)

## <a name="setup"></a>Configurazione

1. Consultare la [guida all'installazione di .NET Core](https://www.microsoft.com/net/core) per installare .NET Core. Quando si seleziona l'ambiente, scegliere l'opzione della riga di comando.
2. Dalla riga di comando creare una directory per il progetto. Passare a questa directory, quindi digitare `dotnet new console -o <sample-project-name>` per creare un progetto console C#.
3. Aprire la directory in Visual Studio Code. Questo passaggio può essere eseguito rapidamente tramite la riga di comando digitando `code .` in Windows.
4. Installare l'[estensione C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) dal marketplace di Visual Studio Code. Riavviare Visual Studio Code.
5. A questo punto compariranno due prompt. Un prompt permette di aggiungere le "risorse necessarie per compilare ed eseguire il debug". Fare clic su "Sì". L'altro prompt consente di ripristinare le dipendenze non risolte. Fare clic su "Ripristina".
6. Modificare `launch.json` in `.vscode` per usare il terminale esterno come una console. Questa impostazione deve essere impostata su `"console": "externalTerminal"`
7. Visual Studio Code consente di eseguire il debug delle applicazioni di .NET Core. Premere `F5` per eseguire l'applicazione e verificare che l'installazione funzioni. Dovrebbe essere visualizzato "Hello World!" sulla console.

## <a name="add-the-data-movement-library-to-your-project"></a>Aggiungere la libreria per lo spostamento dei dati al progetto

1. Aggiungere la versione più recente della libreria di spostamento dei dati alla sezione `dependencies` del file di `<project-name>.csproj`. Al momento della redazione di questo documento, la versione è `"Microsoft.Azure.Storage.DataMovement": "0.6.2"`
2. Per ripristinare il progetto dovrebbe essere visualizzato un prompt dei comandi. Fare clic sul pulsante "ripristina". È inoltre possibile ripristinare il progetto dalla riga di comando digitando il comando `dotnet restore` nella radice della directory del progetto.

Modificare `<project-name>.csproj`:

```xml
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.Azure.Storage.DataMovement" Version="0.6.2" />
        </ItemGroup>
    </Project>
```

## <a name="set-up-the-skeleton-of-your-application"></a>Configurare lo scheletro dell'applicazione

La prima cosa da fare è impostare il codice "scheletro" dell'applicazione. Questo codice richiede un nome e una chiave dell'account di archiviazione, che usa per creare un oggetto `CloudStorageAccount`. Questo oggetto viene usato per interagire con l'account di archiviazione in tutti gli scenari di trasferimento. Il codice consente inoltre di scegliere il tipo di operazione di trasferimento desiderata.

Modificare `Program.cs`:

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;
using System.Diagnostics;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Blob;
using Microsoft.Azure.Storage.DataMovement;

namespace DMLibSample
{
    public class Program
    {
        public static void Main()
        {
            Console.WriteLine("Enter Storage account name:");
            string accountName = Console.ReadLine();

            Console.WriteLine("\nEnter Storage account key:");
            string accountKey = Console.ReadLine();

            string storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=" + accountName + ";AccountKey=" + accountKey;
            CloudStorageAccount account = CloudStorageAccount.Parse(storageConnectionString);

            ExecuteChoice(account);
        }

        public static void ExecuteChoice(CloudStorageAccount account)
        {
            Console.WriteLine("\nWhat type of transfer would you like to execute?\n1. Local file --> Azure Blob\n2. Local directory --> Azure Blob directory\n3. URL (e.g. Amazon S3 file) --> Azure Blob\n4. Azure Blob --> Azure Blob");
            int choice = int.Parse(Console.ReadLine());

            if(choice == 1)
            {
                TransferLocalFileToAzureBlob(account).Wait();
            }
            else if(choice == 2)
            {
                TransferLocalDirectoryToAzureBlobDirectory(account).Wait();
            }
            else if(choice == 3)
            {
                TransferUrlToAzureBlob(account).Wait();
            }
            else if(choice == 4)
            {
                TransferAzureBlobToAzureBlob(account).Wait();
            }
        }

        public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
        {

        }

        public static async Task TransferLocalDirectoryToAzureBlobDirectory(CloudStorageAccount account)
        {

        }

        public static async Task TransferUrlToAzureBlob(CloudStorageAccount account)
        {

        }

        public static async Task TransferAzureBlobToAzureBlob(CloudStorageAccount account)
        {

        }
    }
}
```

## <a name="upload-a-local-file-to-a-blob"></a>Caricare un file locale in un BLOB

Aggiungere i metodi `GetSourcePath` e `GetBlob` a `Program.cs`:

```csharp
public static string GetSourcePath()
{
    Console.WriteLine("\nProvide path for source:");
    string sourcePath = Console.ReadLine();

    return sourcePath;
}

public static CloudBlockBlob GetBlob(CloudStorageAccount account)
{
    CloudBlobClient blobClient = account.CreateCloudBlobClient();

    Console.WriteLine("\nProvide name of Blob container:");
    string containerName = Console.ReadLine();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExistsAsync().Wait();

    Console.WriteLine("\nProvide name of new Blob:");
    string blobName = Console.ReadLine();
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    return blob;
}
```

Modificare il metodo `TransferLocalFileToAzureBlob`:

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    Console.WriteLine("\nTransfer started...");
    await TransferManager.UploadAsync(localFilePath, blob);
    Console.WriteLine("\nTransfer operation complete.");
    ExecuteChoice(account);
}
```

Questo codice richiede il percorso di un file locale, il nome di un contenitore nuovo o esistente e il nome di un nuovo BLOB. Il metodo `TransferManager.UploadAsync` esegue il caricamento usando queste informazioni.

Premere `F5` per eseguire l'applicazione. È possibile verificare l'avvenuto caricamento visualizzando l'account di archiviazione con [Esplora archivi di Microsoft Azure](https://storageexplorer.com/).

## <a name="set-the-number-of-parallel-operations"></a>Impostare il numero di operazioni parallele

Una funzionalità offerta dalla libreria per lo spostamento dei dati è la possibilità di impostare il numero di operazioni parallele per aumentare la velocità effettiva di trasferimento dei dati. Per impostazione predefinita, la libreria per lo spostamento dei dati imposta il numero di operazioni parallele su 8 * il numero di core nel computer.

Tenere presente che un numero elevato di operazioni parallele in un ambiente con larghezza di banda ridotta potrebbe sovraccaricare la connessione di rete e impedire il corretto completamento delle operazioni. È necessario fare qualche prova con questa impostazione per determinare la soluzione migliore in base alla larghezza di banda di rete disponibile.

Aggiungere il codice che consente di impostare il numero di operazioni parallele. Aggiungere anche il codice che imposta il tempo per completare il trasferimento.

Aggiungere un metodo `SetNumberOfParallelOperations` a `Program.cs`:

```csharp
public static void SetNumberOfParallelOperations()
{
    Console.WriteLine("\nHow many parallel operations would you like to use?");
    string parallelOperations = Console.ReadLine();
    TransferManager.Configurations.ParallelOperations = int.Parse(parallelOperations);
}
```

Modificare il metodo `ExecuteChoice` in modo che usi `SetNumberOfParallelOperations`:

```csharp
public static void ExecuteChoice(CloudStorageAccount account)
{
    Console.WriteLine("\nWhat type of transfer would you like to execute?\n1. Local file --> Azure Blob\n2. Local directory --> Azure Blob directory\n3. URL (e.g. Amazon S3 file) --> Azure Blob\n4. Azure Blob --> Azure Blob");
    int choice = int.Parse(Console.ReadLine());

    SetNumberOfParallelOperations();

    if(choice == 1)
    {
        TransferLocalFileToAzureBlob(account).Wait();
    }
    else if(choice == 2)
    {
        TransferLocalDirectoryToAzureBlobDirectory(account).Wait();
    }
    else if(choice == 3)
    {
        TransferUrlToAzureBlob(account).Wait();
    }
    else if(choice == 4)
    {
        TransferAzureBlobToAzureBlob(account).Wait();
    }
}
```

Modificare il metodo `TransferLocalFileToAzureBlob` in modo che usi un timer:

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    Console.WriteLine("\nTransfer started...");
    Stopwatch stopWatch = Stopwatch.StartNew();
    await TransferManager.UploadAsync(localFilePath, blob);
    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

## <a name="track-transfer-progress"></a>Monitorare lo stato del trasferimento dati

È utile sapere quanto tempo è necessario per il trasferimento dei dati. Tuttavia, la possibilità di visualizzare lo stato di avanzamento del trasferimento *durante* l'operazione di trasferimento è ancora migliore. Per realizzare questo scenario, è necessario creare un oggetto `TransferContext`. L'oggetto `TransferContext` è disponibile in due forme: `SingleTransferContext` e `DirectoryTransferContext`. Il primo è per il trasferimento di un singolo file e il secondo è per il trasferimento di una directory di file.

Aggiungere i metodi `GetSingleTransferContext` e `GetDirectoryTransferContext` a `Program.cs`:

```csharp
public static SingleTransferContext GetSingleTransferContext(TransferCheckpoint checkpoint)
{
    SingleTransferContext context = new SingleTransferContext(checkpoint);

    context.ProgressHandler = new Progress<TransferStatus>((progress) =>
    {
        Console.Write("\rBytes transferred: {0}", progress.BytesTransferred );
    });

    return context;
}

public static DirectoryTransferContext GetDirectoryTransferContext(TransferCheckpoint checkpoint)
{
    DirectoryTransferContext context = new DirectoryTransferContext(checkpoint);

    context.ProgressHandler = new Progress<TransferStatus>((progress) =>
    {
        Console.Write("\rBytes transferred: {0}", progress.BytesTransferred );
    });

    return context;
}
```

Modificare il metodo `TransferLocalFileToAzureBlob` in modo che usi `GetSingleTransferContext`:

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    Console.WriteLine("\nTransfer started...\n");
    Stopwatch stopWatch = Stopwatch.StartNew();
    await TransferManager.UploadAsync(localFilePath, blob, null, context);
    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

## <a name="resume-a-canceled-transfer"></a>Riprendere un trasferimento annullato

Un'altra utile funzionalità offerta dalla libreria per lo spostamento dei dati è la possibilità di riprendere un trasferimento annullato. Aggiungere un codice che consente di annullare temporaneamente il trasferimento digitando `c`, quindi riprendere il trasferimento dopo 3 secondi.

Modificare `TransferLocalFileToAzureBlob`:

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.UploadAsync(localFilePath, blob, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.UploadAsync(localFilePath, blob, null, context);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

Fino a questo momento il valore `checkpoint` è sempre rimasto impostato su `null`. A questo punto, se si annulla il trasferimento, viene recuperato l'ultimo checkpoint dell'operazione, che viene poi usato come nuovo checkpoint nel trasferimento.

## <a name="transfer-a-local-directory-to-blob-storage"></a>Trasferire una directory locale nell'archivio BLOB

Sarebbe deludente se la libreria per lo spostamento dei dati potesse trasferire un solo file alla volta. Per fortuna però non è così. La libreria per lo spostamento dei dati consente di trasferire una directory di file e tutte le relative sottodirectory. Aggiungere un codice che consente questa operazione.

Per prima cosa, aggiungere il metodo `GetBlobDirectory` a `Program.cs`:

```csharp
public static CloudBlobDirectory GetBlobDirectory(CloudStorageAccount account)
{
    CloudBlobClient blobClient = account.CreateCloudBlobClient();

    Console.WriteLine("\nProvide name of Blob container. This can be a new or existing Blob container:");
    string containerName = Console.ReadLine();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExistsAsync().Wait();

    CloudBlobDirectory blobDirectory = container.GetDirectoryReference("");

    return blobDirectory;
}
```

Poi modificare `TransferLocalDirectoryToAzureBlobDirectory`:

```csharp
public static async Task TransferLocalDirectoryToAzureBlobDirectory(CloudStorageAccount account)
{
    string localDirectoryPath = GetSourcePath();
    CloudBlobDirectory blobDirectory = GetBlobDirectory(account);
    TransferCheckpoint checkpoint = null;
    DirectoryTransferContext context = GetDirectoryTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    UploadDirectoryOptions options = new UploadDirectoryOptions()
    {
        Recursive = true
    };

    try
    {
        task = TransferManager.UploadDirectoryAsync(localDirectoryPath, blobDirectory, options, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetDirectoryTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.UploadDirectoryAsync(localDirectoryPath, blobDirectory, options, context);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

Esistono alcune differenze tra questo metodo e il metodo di caricamento di un singolo file. Attualmente sono in uso `TransferManager.UploadDirectoryAsync` e il metodo `getDirectoryTransferContext` creato in precedenza. Ora è disponibile anche un valore `options` per l'operazione di caricamento, che consente di indicare la volontà di includere le sottodirectory nel caricamento.

## <a name="copy-a-file-from-url-to-a-blob"></a>Copiare un file da un URL a un BLOB

A questo punto, aggiungere un codice che consente di copiare un file da un URL a un BLOB di Azure.

Modificare `TransferUrlToAzureBlob`:

```csharp
public static async Task TransferUrlToAzureBlob(CloudStorageAccount account)
{
    Uri uri = new Uri(GetSourcePath());
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.CopyAsync(uri, blob, true, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.CopyAsync(uri, blob, true, null, context, cancellationSource.Token);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

Un importante caso in cui viene usata questa funzionalità è quando è necessario spostare i dati da un altro servizio cloud (ad esempio AWS) ad Azure. Se si dispone di un URL che consente di accedere alla risorsa, è possibile spostarla facilmente nei BLOB di Azure tramite il metodo `TransferManager.CopyAsync`. Questo metodo introduce anche un nuovo parametro booleano. L'impostazione di questo parametro su `true` indica che si desidera eseguire una copia asincrona sul lato server. L'impostazione di questo parametro su `false` indica una copia sincrona, ovvero la risorsa viene prima scaricata sul computer locale e poi caricata sul BLOB di Azure. La copia sincrona tuttavia è attualmente disponibile solo per la copia da una risorsa di archiviazione di Azure a un'altra.

## <a name="copy-a-blob"></a>Copiare un BLOB

Un'altra funzionalità fornita in modo univoco dalla libreria per lo spostamento dei dati è la possibilità di copiare da una risorsa di archiviazione di Azure a un'altra.

Modificare `TransferAzureBlobToAzureBlob`:

```csharp
public static async Task TransferAzureBlobToAzureBlob(CloudStorageAccount account)
{
    CloudBlockBlob sourceBlob = GetBlob(account);
    CloudBlockBlob destinationBlob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.CopyAsync(sourceBlob, destinationBlob, true, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.CopyAsync(sourceBlob, destinationBlob, false, null, context, cancellationSource.Token);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

In questo esempio il parametro booleano in `TransferManager.CopyAsync` viene impostato su `false` per indicare che si desidera eseguire una copia sincrona. Ciò significa che la risorsa viene prima scaricata sul computer locale e successivamente caricata sul BLOB di Azure. L'opzione di copia sincrona è un ottimo modo per assicurarsi che l'operazione di copia avvenga a velocità costante. Al contrario, la velocità di una copia asincrona sul lato server dipende dalla larghezza di banda di rete disponibile sul server, che può variare. La copia sincrona, tuttavia, può generare costi aggiuntivi in uscita rispetto alla copia asincrona. Per evitare costi in uscita, si consiglia quindi di usare la copia sincrona in una macchina virtuale di Azure che si trova nella stessa area dell'account di archiviazione di origine.

L'applicazione per lo spostamento dei dati è ora completata. [L'esempio di codice completo è disponibile su GitHub](https://github.com/azure-samples/storage-dotnet-data-movement-library-app).

## <a name="next-steps"></a>Passaggi successivi

[Documentazione di riferimento della libreria di spostamento dei dati di archiviazione di Azure](https://azure.github.io/azure-storage-net-data-movement).

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]
