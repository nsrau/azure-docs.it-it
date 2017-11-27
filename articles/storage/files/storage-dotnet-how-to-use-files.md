---
title: Eseguire lo sviluppo per File di Azure con .NET | Microsoft Docs
description: Informazioni su come sviluppare applicazioni e servizi .NET che usano File di Azure per archiviare i dati dei file.
services: storage
documentationcenter: .net
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 6a889ee1-1e60-46ec-a592-ae854f9fb8b6
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 11/22/2017
ms.author: renash
ms.openlocfilehash: 66a68a1ca048b50b8e2ba4ac1bb86d367b8a5bb9
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2017
---
# <a name="develop-for-azure-files-with-net-and-windowsazurestorage"></a>Eseguire lo sviluppo per File di Azure con .NET e WindowsAzure.Storage

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Questa esercitazione illustra le nozioni di base per l'uso di .NET e dell'API `WindowsAzure.Storage` per sviluppare applicazioni che usano [File di Azure](storage-files-introduction.md) per archiviare i dati dei file. Questa esercitazione crea una semplice applicazione console per eseguire azioni di base con .NET e File di Azure:

* Ottenere il contenuto di un file
* Impostare la quota (dimensione massima) per la condivisione file.
* Creare una firma di accesso condiviso (chiave di firma di accesso condiviso) per un file che usa un criterio di accesso condiviso definito nella condivisione.
* Copiare un file in un altro file nello stesso account di archiviazione.
* Copiare un file in un BLOB nello stesso account di archiviazione.
* Usare la metrica di archiviazione di Azure per la risoluzione dei problemi

Per altre informazioni su File di Azure, vedere [Introduzione a File di Azure](storage-files-introduction.md).

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

## <a name="understanding-the-net-apis"></a>Informazioni sulle API .NET

File di Azure offre due ampi approcci alle applicazioni client: Server Message Block (SMB) e REST. In .NET questi approcci vengono astratti dalle API `System.IO` e `WindowsAzure.Storage`.

API | Quando usare le autorizzazioni | Note
----|-------------|------
[System.IO](https://docs.microsoft.com/dotnet/api/system.io) | L'applicazione: <ul><li>Deve leggere/scrivere file tramite SMB</li><li>È in esecuzione su un dispositivo che ha accesso tramite la porta 445 all'account File di Azure</li><li>Non deve gestire le impostazioni amministrative della condivisione file</li></ul> | La codifica dell'I/O dei file con File di Azure tramite SMB è in genere uguale alla codifica dell'I/O con le condivisioni file di rete o i dispositivi di archiviazione locale. Per un'introduzione ad alcune funzionalità di .NET, incluso l'I/O dei file, vedere [questa esercitazione](https://docs.microsoft.com/dotnet/csharp/tutorials/console-teleprompter).
[WindowsAzure.Storage](https://docs.microsoft.com/dotnet/api/overview/azure/storage?view=azure-dotnet#client-library) | L'applicazione: <ul><li>Non può accedere a File di Azure tramite SMB sulla porta 445 a causa del firewall o dei vincoli dell'ISP</li><li>Richiede funzionalità amministrative, ad esempio la possibilità di impostare la quota di una condivisione file o di creare una firma di accesso condiviso</li></ul> | Questo articolo illustra l'utilizzo di `WindowsAzure.Storage` per l'I/O dei file tramite REST (invece di SMB) e la gestione della condivisione file.

> [!TIP]
> A seconda dei requisiti dell'applicazione, i BLOB di Azure possono essere una scelta più appropriata come risorsa di archiviazione. Per altre informazioni sulla scelta di File di Azure o dei BLOB di Azure, vedere [Decidere quando usare BLOB di Azure, File di Azure o Dischi di Azure](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks).

## <a name="create-the-console-application-and-obtain-the-assembly"></a>Creare l'applicazione console e ottenere l'assembly
In Visual Studio creare una nuova applicazione console di Windows. La procedura seguente illustra come creare un'applicazione console in Visual Studio 2017, ma i passaggi sono simili anche per le altre versioni di Visual Studio.

1. Selezionare **File** > **Nuovo** > **Progetto**
2. Selezionare **Installati** > **Modelli** > **Visual C#** > **Desktop classico di Windows**
3. Selezionare **App console (.NET Framework)**
4. Immettere un nome per l'applicazione nel campo **Nome**
5. Selezionare **OK**.

Tutti gli esempi di codice in questa esercitazione possono essere aggiunti al metodo `Main()` del file `Program.cs` dell'applicazione console.

È possibile usare la libreria client di archiviazione di Azure in qualsiasi tipo di applicazione .NET, ad esempio un servizio cloud o un'app Web di Azure e applicazioni desktop e per dispositivi mobili. Per semplicità, in questa guida si usa un'applicazione console.

## <a name="use-nuget-to-install-the-required-packages"></a>Usare NuGet per installare i pacchetti necessari
Per completare questa esercitazione, è necessario fare riferimento a due pacchetti nel progetto:

* [Libreria client di archiviazione di Microsoft Azure per .NET](https://www.nuget.org/packages/WindowsAzure.Storage/): questo pacchetto fornisce l'accesso a livello di codice alle risorse dati nell'account di archiviazione.
* [Libreria Gestione configurazione di Microsoft Azure per .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/): questo pacchetto fornisce una classe per l'analisi di una stringa di connessione in un file di configurazione, indipendentemente dalla posizione in cui viene eseguita l'applicazione.

Per ottenere entrambi i pacchetti, è possibile usare NuGet. A tale scopo, seguire questa procedura:

1. Fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Gestisci pacchetti NuGet**.
2. Cercare online "WindowsAzure.Storage" e fare clic su **Installa** per installare il pacchetto della libreria client di archiviazione e le relative dipendenze.
3. Cercare online "WindowsAzure.ConfigurationManager" e fare clic su **Installa** per installare Gestione configurazione di Azure.

## <a name="save-your-storage-account-credentials-to-the-appconfig-file"></a>Salvare le credenziali dell'account di archiviazione nel file app.config
A questo punto salvare le credenziali nel file app.config del progetto. Modificare il file app.config in modo che assomigli all'esempio seguente, sostituendo `myaccount` con il nome dell'account di archiviazione e `mykey` con la chiave dell'account di archiviazione.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=StorageAccountKeyEndingIn==" />
    </appSettings>
</configuration>
```

> [!NOTE]
> L'ultima versione dell'emulatore di archiviazione di Azure non supporta File di Azure. Per poter usare File di Azure, la stringa di connessione deve specificare come destinazione un account di archiviazione di Azure nel cloud.

## <a name="add-using-directives"></a>Aggiungere le direttive using
Aprire il file `Program.cs` da Esplora soluzioni e aggiungere le direttive using seguenti all'inizio del file.

```csharp
using Microsoft.Azure; // Namespace for Azure Configuration Manager
using Microsoft.WindowsAzure.Storage; // Namespace for Storage Client Library
using Microsoft.WindowsAzure.Storage.Blob; // Namespace for Azure Blobs
using Microsoft.WindowsAzure.Storage.File; // Namespace for Azure Files
```

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="access-the-file-share-programmatically"></a>Accedere alla condivisione file a livello di programmazione
A questo punto aggiungere il codice seguente al metodo `Main()`, dopo il codice indicato sopra, per recuperare la stringa di connessione. Questo codice ottiene un riferimento al file creato in precedenza e genera i contenuti nella finestra della console.

```csharp
// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile file = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the file exists.
        if (file.Exists())
        {
            // Write the contents of the file to the console window.
            Console.WriteLine(file.DownloadTextAsync().Result);
        }
    }
}
```

Eseguire l'applicazione console per visualizzare l'output.

## <a name="set-the-maximum-size-for-a-file-share"></a>Impostare la dimensione massima per una condivisione file
A partire dalla versione 5.x della libreria client di archiviazione di Azure, è possibile impostare la quota (o dimensione massima) per una condivisione file, in gigabyte. È anche possibile controllare la quantità di dati archiviata attualmente nella condivisione.

Impostando la quota per una condivisione, è possibile limitare la dimensione totale dei file archiviati nella condivisione. Se la dimensione totale dei file nella condivisione supera la quota impostata per la condivisione, i client non saranno in grado di aumentare le dimensioni dei file esistenti o creare nuovi file, a meno che tali file non siano vuoti.

L'esempio seguente illustra come controllare l'uso corrente per una condivisione e come impostare la quota per la condivisione.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Check current usage stats for the share.
    // Note that the ShareStats object is part of the protocol layer for the File service.
    Microsoft.WindowsAzure.Storage.File.Protocol.ShareStats stats = share.GetStats();
    Console.WriteLine("Current share usage: {0} GB", stats.Usage.ToString());

    // Specify the maximum size of the share, in GB.
    // This line sets the quota to be 10 GB greater than the current usage of the share.
    share.Properties.Quota = 10 + stats.Usage;
    share.SetProperties();

    // Now check the quota for the share. Call FetchAttributes() to populate the share's properties.
    share.FetchAttributes();
    Console.WriteLine("Current share quota: {0} GB", share.Properties.Quota);
}
```

### <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>Generare la firma di accesso condiviso per un file o una condivisione file
A partire dalla versione 5.x della Libreria del client di archiviazione di Azure, è possibile generare una firma di accesso condiviso (SAS) per una condivisione file o per un singolo file. È inoltre possibile creare un criterio di accesso condiviso in una condivisione file per gestire le firme di accesso condiviso. È consigliabile creare un criterio di accesso condiviso, in quanto fornisce un modo per revocare la firma SAS se necessario.

Nell'esempio seguente viene creato un criterio di accesso condiviso in una condivisione e quindi viene usato tale criterio per fornire i vincoli per una firma di accesso condiviso su un file della condivisione.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    string policyName = "sampleSharePolicy" + DateTime.UtcNow.Ticks;

    // Create a new shared access policy and define its constraints.
    SharedAccessFilePolicy sharedPolicy = new SharedAccessFilePolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessFilePermissions.Read | SharedAccessFilePermissions.Write
        };

    // Get existing permissions for the share.
    FileSharePermissions permissions = share.GetPermissions();

    // Add the shared access policy to the share's policies. Note that each policy must have a unique name.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    share.SetPermissions(permissions);

    // Generate a SAS for a file in the share and associate this access policy with it.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");
    CloudFile file = sampleDir.GetFileReference("Log1.txt");
    string sasToken = file.GetSharedAccessSignature(null, policyName);
    Uri fileSasUri = new Uri(file.StorageUri.PrimaryUri.ToString() + sasToken);

    // Create a new CloudFile object from the SAS, and write some text to the file.
    CloudFile fileSas = new CloudFile(fileSasUri);
    fileSas.UploadText("This write operation is authenticated via SAS.");
    Console.WriteLine(fileSas.DownloadText());
}
```

Per altre informazioni sulla creazione e sull'uso di firme di accesso condiviso, vedere [Uso delle firme di accesso condiviso](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) e [Creare e usare una firma di accesso condiviso con i BLOB di Azure](../blobs/storage-dotnet-shared-access-signature-part-2.md).

## <a name="copy-files"></a>Copiare i file
A partire dalla versione 5.x della libreria del client di archiviazione di Azure, è possibile copiare un file in un altro file, un file in un BLOB o un BLOB in un file. Le sezioni seguenti illustrano come eseguire queste operazioni di copia a livello di programmazione.

È inoltre possibile utilizzare AzCopy per copiare un file in un altro o per copiare un blob in un file o viceversa. Vedere [Trasferire dati con l'utilità della riga di comando AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

> [!NOTE]
> Se si copia un BLOB in un file o un file in un BLOB, è necessario utilizzare una firma di accesso condiviso (SAS) per autenticare l'oggetto di origine, anche se si copia nello stesso account di archiviazione.
> 
> 

**Copiare un file in un altro file** Nell'esempio seguente viene copiato un file in un altro file nella stessa condivisione. Poiché questa operazione esegue la copia tra file nello stesso account di archiviazione, è possibile utilizzare l'autenticazione chiave condivisa per eseguire la copia.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile sourceFile = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the source file exists.
        if (sourceFile.Exists())
        {
            // Get a reference to the destination file.
            CloudFile destFile = sampleDir.GetFileReference("Log1Copy.txt");

            // Start the copy operation.
            destFile.StartCopy(sourceFile);

            // Write the contents of the destination file to the console window.
            Console.WriteLine(destFile.DownloadText());
        }
    }
}
```

**Copiare un file in un BLOB** Nell'esempio seguente viene creato un file che viene copiato in un BLOB nello stesso account di archiviazione. Nell'esempio viene creata una firma di accesso condiviso per il file di origine, che il servizio utilizza per autenticare l'accesso al file di origine durante l'operazione di copia.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Create a new file share, if it does not already exist.
CloudFileShare share = fileClient.GetShareReference("sample-share");
share.CreateIfNotExists();

// Create a new file in the root directory.
CloudFile sourceFile = share.GetRootDirectoryReference().GetFileReference("sample-file.txt");
sourceFile.UploadText("A sample file in the root directory.");

// Get a reference to the blob to which the file will be copied.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
container.CreateIfNotExists();
CloudBlockBlob destBlob = container.GetBlockBlobReference("sample-blob.txt");

// Create a SAS for the file that's valid for 24 hours.
// Note that when you are copying a file to a blob, or a blob to a file, you must use a SAS
// to authenticate access to the source object, even if you are copying within the same
// storage account.
string fileSas = sourceFile.GetSharedAccessSignature(new SharedAccessFilePolicy()
{
    // Only read permissions are required for the source file.
    Permissions = SharedAccessFilePermissions.Read,
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24)
});

// Construct the URI to the source file, including the SAS token.
Uri fileSasUri = new Uri(sourceFile.StorageUri.PrimaryUri.ToString() + fileSas);

// Copy the file to the blob.
destBlob.StartCopy(fileSasUri);

// Write the contents of the file to the console window.
Console.WriteLine("Source file contents: {0}", sourceFile.DownloadText());
Console.WriteLine("Destination blob contents: {0}", destBlob.DownloadText());
```

È possibile copiare un BLOB in un file nello stesso modo. Se l'oggetto di origine è un BLOB, creare una firma di accesso condiviso per consentire l'accesso al BLOB durante l'operazione di copia.

## <a name="share-snapshots-preview"></a>Snapshot di condivisione (anteprima)
A partire dalla versione 8.5 della libreria client di archiviazione di Azure, è possibile creare uno snapshot di condivisione (anteprima), nonché elencare, esplorare ed eliminare snapshot di condivisione. Gli snapshot di condivisione sono di sola lettura, di conseguenza non sono consentite operazioni di scrittura su di essi.

**Creare snapshot di condivisione**

L'esempio seguente crea uno snapshot di condivisione file.

```csharp
storageAccount = CloudStorageAccount.Parse(ConnectionString); 
fClient = storageAccount.CreateCloudFileClient(); 
string baseShareName = "myazurefileshare"; 
CloudFileShare myShare = fClient.GetShareReference(baseShareName); 
var snapshotShare = myShare.Snapshot();

```
**Elencare gli snapshot di condivisione**

L'esempio seguente elenca gli snapshot di condivisione in una condivisione.

```csharp
var shares = fClient.ListShares(baseShareName, ShareListingDetails.All);
```

**Esplorare i file e le directory negli snapshot di condivisione**

L'esempio seguente esplora file e directory all'interno degli snapshot di condivisione.

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); 
var rootDirectory = mySnapshot.GetRootDirectoryReference(); 
var items = rootDirectory.ListFilesAndDirectories();
```

**Elencare condivisioni e snapshot di condivisione e ripristinare condivisioni file o file da snapshot di condivisione** 

La creazione di uno snapshot di una condivisione file consente di ripristinare in futuro singoli file o l'intera condivisione. 

È possibile ripristinare un file da uno snapshot di condivisione file eseguendo una query sugli snapshot di condivisione di una condivisione file. Si può quindi recuperare un file appartenente a un determinato snapshot di condivisione e usare tale versione per eseguire direttamente la lettura e il confronto oppure per il ripristino.

```csharp
CloudFileShare liveShare = fClient.GetShareReference(baseShareName);
var rootDirOfliveShare = liveShare.GetRootDirectoryReference();

       var dirInliveShare = rootDirOfliveShare.GetDirectoryReference(dirName);
var fileInliveShare = dirInliveShare.GetFileReference(fileName);

           
CloudFileShare snapshot = fClient.GetShareReference(baseShareName, snapshotTime);
var rootDirOfSnapshot = snapshot.GetRootDirectoryReference();

       var dirInSnapshot = rootDirOfSnapshot.GetDirectoryReference(dirName);
var fileInSnapshot = dir1InSnapshot.GetFileReference(fileName);

string sasContainerToken = string.Empty;
       SharedAccessFilePolicy sasConstraints = new SharedAccessFilePolicy();
       sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
       sasConstraints.Permissions = SharedAccessFilePermissions.Read;
       //Generate the shared access signature on the container, setting the constraints directly on the signature.
sasContainerToken = fileInSnapshot.GetSharedAccessSignature(sasConstraints);

string sourceUri = (fileInSnapshot.Uri.ToString() + sasContainerToken + "&" + fileInSnapshot.SnapshotTime.ToString()); ;
fileInliveShare.StartCopyAsync(new Uri(sourceUri));

```


**Eliminare snapshot di condivisione**

L'esempio seguente elimina uno snapshot di condivisione file.

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); mySnapshot.Delete(null, null, null);
```

## <a name="troubleshooting-azure-files-using-metrics"></a>Risoluzione dei problemi di File di Azure con le metriche
Analisi archiviazione di Azure supporta ora metriche per File di Azure. Grazie ai dati di metrica, è possibile monitorare le richieste e diagnosticare i problemi.

È possibile abilitare le metriche per File di Azure dal [portale di Azure](https://portal.azure.com). È anche possibile abilitare le metriche a livello di codice chiamando l'operazione Set File Service Properties tramite l'API REST o una delle soluzioni analoghe disponibili nella libreria client di archiviazione.

L'esempio di codice seguente mostra come usare la libreria client di archiviazione per .NET per abilitare le metriche per File di Azure.

Aggiungere prima le direttive `using` seguenti al file `Program.cs`, oltre a quelle aggiunte sopra:

```csharp
using Microsoft.WindowsAzure.Storage.File.Protocol;
using Microsoft.WindowsAzure.Storage.Shared.Protocol;
```

Si noti che, anche se i BLOB, le tabelle e le code di Azure usano il tipo `ServiceProperties` condiviso nello spazio dei nomi `Microsoft.WindowsAzure.Storage.Shared.Protocol`, File di Azure usa un proprio tipo, ossia il tipo `FileServiceProperties` nello spazio dei nomi `Microsoft.WindowsAzure.Storage.File.Protocol`. È tuttavia necessario fare riferimento a entrambi gli spazi dei nomi dal proprio codice, per poter compilare il codice seguente.

```csharp
// Parse your storage connection string from your application's configuration file.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
// Create the File service client.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Set metrics properties for File service.
// Note that the File service currently uses its own service properties type,
// available in the Microsoft.WindowsAzure.Storage.File.Protocol namespace.
fileClient.SetServiceProperties(new FileServiceProperties()
{
    // Set hour metrics
    HourMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 14,
        Version = "1.0"
    },
    // Set minute metrics
    MinuteMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 7,
        Version = "1.0"
    }
});

// Read the metrics properties we just set.
FileServiceProperties serviceProperties = fileClient.GetServiceProperties();
Console.WriteLine("Hour metrics:");
Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
Console.WriteLine(serviceProperties.HourMetrics.Version);
Console.WriteLine();
Console.WriteLine("Minute metrics:");
Console.WriteLine(serviceProperties.MinuteMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.MinuteMetrics.RetentionDays);
Console.WriteLine(serviceProperties.MinuteMetrics.Version);
```

È anche possibile fare riferimento all'articolo sulla [risoluzione dei problemi di archiviazione file di Azure](storage-troubleshoot-windows-file-connection-problems.md) per indicazioni sulla risoluzione dei problemi end-to-end.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su File di Azure, vedere i collegamenti seguenti.

### <a name="conceptual-articles-and-videos"></a>Articoli concettuali e video
* [File di Azure: un file system SMB nel cloud senza conflitti per Windows e Linux](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [Come usare File di Azure con Linux](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-file-storage"></a>Supporto degli strumenti per Archiviazione file
* [Come usare AzCopy con Archiviazione di Microsoft Azure](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Utilizzo dell'interfaccia della riga di comando di Azure con archiviazione di Azure](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#create-and-manage-file-shares)
* [Risoluzione dei problemi di File di Azure](https://docs.microsoft.com/azure/storage/storage-troubleshoot-file-connection-problems)

### <a name="reference"></a>riferimento
* [Informazioni di riferimento sulla libreria client di archiviazione per .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [Riferimento API REST del servizio File](http://msdn.microsoft.com/library/azure/dn167006.aspx)

### <a name="blog-posts"></a>Post di BLOG
* [Azure Files is now generally available](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/) (File di Azure è ora disponibile a livello generale)
* [Inside Azure Files](https://azure.microsoft.com/blog/inside-azure-file-storage/) (Analisi di File di Azure)
* [Introduzione al servizio File di Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Mantenimento delle connessioni ai file di Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)