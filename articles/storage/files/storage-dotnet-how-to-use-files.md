---
title: Eseguire lo sviluppo per File di Azure con .NET | Microsoft Docs
description: Informazioni su come sviluppare applicazioni e servizi .NET che usano File di Azure per archiviare i dati dei file.
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 10/7/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 4d8be13a75e276d5be6ec71141a13f95601869f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78301438"
---
# <a name="develop-for-azure-files-with-net"></a>Eseguire lo sviluppo per File di Azure con .NET

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Questa esercitazione illustra le nozioni di base per l'uso di .NET per sviluppare applicazioni che usano [File di Azure](storage-files-introduction.md) per archiviare i dati dei file. Questa esercitazione crea una semplice applicazione console per eseguire azioni di base con file .NET e Azure:This tutorial creates a simple console application to do basic actions with .NET and Azure Files:

* Ottenere il contenuto di un file.
* Impostare la dimensione massima o la quota per la *condivisione* file.
* Creare una firma di accesso condiviso per un file che usa criteri di accesso archiviati definiti nella condivisione.
* Copiare un file in un altro file nello stesso account di archiviazione.
* Copiare un file in un BLOB nello stesso account di archiviazione.
* Usare le metriche di Archiviazione di Azure per la risoluzione dei problemi.

Per altre informazioni sui file di Azure, vedere [Che cos'è File di Azure?](storage-files-introduction.md)

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

## <a name="understanding-the-net-apis"></a>Informazioni sulle API .NET

File di Azure offre due ampi approcci alle applicazioni client: Server Message Block (SMB) e REST. All'interno di `System.IO` `WindowsAzure.Storage` .NET, le API e astraggono questi approcci.

API | Utilizzo | Note
----|-------------|------
[System.IO](https://docs.microsoft.com/dotnet/api/system.io) | L'applicazione: <ul><li>È necessario leggere/scrivere file utilizzando SMB</li><li>È in esecuzione su un dispositivo che ha accesso tramite la porta 445 all'account File di Azure</li><li>Non deve gestire le impostazioni amministrative della condivisione file</li></ul> | I/O di file implementato con File di Azure su SMB è in genere lo stesso di I/O con qualsiasi condivisione file di rete o dispositivo di archiviazione locale. Per un'introduzione a una serie di funzionalità in .NET, tra cui l'I/O di file, vedere l'esercitazione [Applicazione console.](https://docs.microsoft.com/dotnet/csharp/tutorials/console-teleprompter)
[Microsoft.Azure.Storage.File](/dotnet/api/overview/azure/storage?view=azure-dotnet#version-11x) | L'applicazione: <ul><li>Impossibile accedere ai file di Azure utilizzando SMB sulla porta 445 a causa di vincoli del firewall o ISP</li><li>Richiede funzionalità amministrative, ad esempio la possibilità di impostare la quota di una condivisione file o di creare una firma di accesso condiviso</li></ul> | In questo articolo `Microsoft.Azure.Storage.File` viene illustrato l'utilizzo di per l'I/O di file tramite REST anziché SMB e la gestione della condivisione file.

## <a name="create-the-console-application-and-obtain-the-assembly"></a>Creare l'applicazione console e ottenere l'assembly

In Visual Studio creare una nuova applicazione console di Windows. La procedura seguente illustra come creare un'applicazione console in Visual Studio 2019. La procedura è simile per le altre versioni di Visual Studio.

1. Avviare Visual Studio e selezionare **Crea un nuovo progetto**.
1. In **Create a new project (Create a new project),** scegliere Console App **(.NET Framework)** per C, quindi selezionare **Next**.
1. In **Configura il nuovo progetto**immettere un nome per l'app e selezionare **Crea**.

È possibile aggiungere tutti gli esempi `Main()` di codice in questa `Program.cs` esercitazione al metodo del file dell'applicazione console.

È possibile usare la libreria client di Archiviazione di Azure in qualsiasi tipo di applicazione .NET. Questi tipi includono un servizio cloud di Azure o un'app Web e applicazioni desktop e per dispositivi mobili. Per semplicità, in questa guida si usa un'applicazione console.

## <a name="use-nuget-to-install-the-required-packages"></a>Usare NuGet per installare i pacchetti necessari

Fare riferimento a questi pacchetti nel progetto per completare questa esercitazione:Refer to these packages in your project to complete this tutorial:

* [Libreria comune di Archiviazione di Microsoft Azure per .NETMicrosoft Azure Storage common library for .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)
  
  Questo pacchetto fornisce l'accesso a livello di codice alle risorse comuni nell'account di archiviazione.
* [Libreria BLOB di archiviazione di Microsoft Azure per .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/)

  Questo pacchetto fornisce l'accesso a livello di codice alle risorse BLOB nell'account di archiviazione.
* [Libreria file di archiviazione di Microsoft Azure per .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.File/)

  Questo pacchetto fornisce l'accesso a livello di codice alle risorse file nell'account di archiviazione.
* [Libreria di Microsoft Azure Configuration Manager per .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/)

  Questo pacchetto fornisce una classe per l'analisi di una stringa di connessione in un file di configurazione, ovunque venga eseguita l'applicazione.

Per ottenere entrambi i pacchetti, è possibile usare NuGet. A tale scopo, seguire questa procedura:

1. In **Esplora soluzioni**fare clic con il pulsante destro del mouse sul progetto e **scegliere Gestisci pacchetti NuGet**.
1. In **Gestione pacchetti NuGet**selezionare **Sfoglia**. Quindi cercare e scegliere **Microsoft.Azure.Storage.Blob**, quindi selezionare **Installa**.

   Questo passaggio installa il pacchetto e le relative dipendenze.
1. Cercare e installare i pacchetti seguenti:

   * **Microsoft.Azure.Storage.Common**
   * **Microsoft.Azure.Storage.File**
   * **Microsoft.Azure.ConfigurationManager**

## <a name="save-your-storage-account-credentials-to-the-appconfig-file"></a>Salvare le credenziali dell'account di archiviazione nel file App.configSave your storage account credentials to the App.config file

Successivamente, salvare le credenziali nel `App.config` file del progetto. In **Esplora soluzioni** `App.config` fare doppio clic e modificare il file in modo che sia simile all'esempio seguente. Sostituire `myaccount` con il nome `mykey` dell'account di archiviazione e con la chiave dell'account di archiviazione.

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

In **Esplora soluzioni**aprire il `Program.cs` file e aggiungere le direttive using seguenti all'inizio del file.

```csharp
using Microsoft.Azure; // Namespace for Azure Configuration Manager
using Microsoft.Azure.Storage; // Namespace for Storage Client Library
using Microsoft.Azure.Storage.Blob; // Namespace for Azure Blobs
using Microsoft.Azure.Storage.File; // Namespace for Azure Files
```

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="access-the-file-share-programmatically"></a>Accedere alla condivisione file a livello di programmazione

Successivamente, aggiungere il contenuto `Main()` seguente al metodo, dopo il codice illustrato in precedenza, per recuperare la stringa di connessione. Questo codice ottiene un riferimento al file creato in precedenza e ne restituisce il contenuto.

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

A partire dalla versione 5.x della libreria client di Archiviazione di Azure, è possibile impostare la quota (dimensione massima) per una condivisione file. È anche possibile controllare la quantità di dati archiviata attualmente nella condivisione.

L'impostazione della quota per una condivisione limita la dimensione totale dei file archiviati nella condivisione. Se la dimensione totale dei file nella condivisione supera la quota impostata nella condivisione, i client non possono aumentare la dimensione dei file esistenti. I client non possono creare nuovi file, a meno che tali file non siano vuoti.

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
    Microsoft.Azure.Storage.File.Protocol.ShareStats stats = share.GetStats();
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

A partire dalla versione 5.x della Libreria del client di archiviazione di Azure, è possibile generare una firma di accesso condiviso (SAS) per una condivisione file o per un singolo file. È inoltre possibile creare criteri di accesso archiviati in una condivisione file per gestire le firme di accesso condiviso. È consigliabile creare un criterio di accesso archiviato perché consente di revocare la creazione di accesso client se viene compromessa.

Nell'esempio seguente viene creato un criterio di accesso archiviato in una condivisione. L'esempio usa tale criterio per fornire i vincoli per una chiamata in stato di condiviso in un file nella condivisione.

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

    // Create a new stored access policy and define its constraints.
    SharedAccessFilePolicy sharedPolicy = new SharedAccessFilePolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessFilePermissions.Read | SharedAccessFilePermissions.Write
        };

    // Get existing permissions for the share.
    FileSharePermissions permissions = share.GetPermissions();

    // Add the stored access policy to the share's policies. Note that each policy must have a unique name.
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
    fileSas.UploadText("This write operation is authorized via SAS.");
    Console.WriteLine(fileSas.DownloadText());
}
```

Per ulteriori informazioni sulla creazione e l'utilizzo delle firme di accesso condiviso, vedere Funzionamento di una firma di [accesso condiviso](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#how-a-shared-access-signature-works).

## <a name="copy-files"></a>Copiare i file

A partire dalla versione 5.x della libreria del client di archiviazione di Azure, è possibile copiare un file in un altro file, un file in un BLOB o un BLOB in un file. Nelle sezioni successive viene illustrato come eseguire queste operazioni di copia a livello di codice.

È anche possibile usare AzCopy per copiare un file in un altro o per copiare un BLOB in un file o viceversa. Vedere [Introduzione ad AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

> [!NOTE]
> Se si copia un BLOB in un file o un file in un BLOB, è necessario usare una firma di accesso condiviso per autorizzare l'accesso all'oggetto di origine, anche se la copia viene eseguita nello stesso account di archiviazione.
>

### <a name="copy-a-file-to-another-file"></a>Copiare un file in un altro file

Nell'esempio seguente viene copiato un file in un altro file nella stessa condivisione. Poiché questa operazione di copia viene copiata tra i file nello stesso account di archiviazione, è possibile usare l'autenticazione con chiave condivisa per eseguire la copia.

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

### <a name="copy-a-file-to-a-blob"></a>Copiare un file in un BLOB

Nell'esempio seguente viene creato un file che viene copiato in un BLOB nello stesso account di archiviazione. L'esempio crea una firma di accesso condiviso per il file di origine, che il servizio usa per autorizzare l'accesso al file di origine durante l'operazione di copia.

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
// to authorize access to the source object, even if you are copying within the same
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

È possibile copiare un BLOB in un file nello stesso modo. Se l'oggetto di origine è un BLOB, creare una firma di accesso condiviso per autorizzare l'accesso al BLOB durante l'operazione di copia.

## <a name="share-snapshots"></a>Snapshot di condivisione

A partire dalla versione 8.5 della libreria client di archiviazione di Azure, è possibile creare uno snapshot di condivisione, nonché elencare, esplorare ed eliminare snapshot di condivisione. Gli snapshot di condivisione sono di sola lettura, di conseguenza non sono consentite operazioni di scrittura su di essi.

### <a name="create-share-snapshots"></a>Creare snapshot di condivisione

L'esempio seguente crea uno snapshot di condivisione file.

```csharp
storageAccount = CloudStorageAccount.Parse(ConnectionString); 
fClient = storageAccount.CreateCloudFileClient(); 
string baseShareName = "myazurefileshare"; 
CloudFileShare myShare = fClient.GetShareReference(baseShareName); 
var snapshotShare = myShare.Snapshot();

```

### <a name="list-share-snapshots"></a>Elencare gli snapshot di condivisione

L'esempio seguente elenca gli snapshot di condivisione in una condivisione.

```csharp
var shares = fClient.ListShares(baseShareName, ShareListingDetails.All);
```

### <a name="browse-files-and-directories-within-share-snapshots"></a>Esplorare i file e le directory negli snapshot di condivisione

L'esempio seguente esplora file e directory all'interno degli snapshot di condivisione.

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); 
var rootDirectory = mySnapshot.GetRootDirectoryReference(); 
var items = rootDirectory.ListFilesAndDirectories();
```

### <a name="list-shares-and-share-snapshots-and-restore-file-shares-or-files-from-share-snapshots"></a>Elencare condivisioni e snapshot di condivisione e ripristinare condivisioni file o file da snapshot di condivisione

La creazione di uno snapshot di una condivisione file consente di ripristinare in futuro singoli file o l'intera condivisione.

È possibile ripristinare un file da uno snapshot di condivisione file eseguendo una query sugli snapshot di condivisione di una condivisione file. È quindi possibile recuperare un file appartenente a uno snapshot di condivisione specifico. Utilizzare tale versione per leggere e confrontare direttamente o per ripristinare.

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

### <a name="delete-share-snapshots"></a>Eliminare snapshot di condivisione

L'esempio seguente elimina uno snapshot di condivisione file.

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); mySnapshot.Delete(null, null, null);
```

## <a name="troubleshoot-azure-files-by-using-metrics"></a>Risolvere i problemi relativi ai file di Azure usando le metricheTroubleshoot Azure Files by using metrics<a name="troubleshooting-azure-files-using-metrics"></a>

Analisi archiviazione di Azure supporta ora metriche per File di Azure. Grazie ai dati di metrica, è possibile monitorare le richieste e diagnosticare i problemi.

È possibile abilitare le metriche per File di Azure dal portale di [Azure.You can](https://portal.azure.com)enable metrics for Azure Files from the Azure portal . È anche possibile abilitare le metriche a livello di programmazione chiamando l'operazione Imposta proprietà servizio file con l'API REST o uno dei relativi analoghi nella libreria client di archiviazione.

L'esempio di codice seguente mostra come usare la libreria client di archiviazione per .NET per abilitare le metriche per File di Azure.

In primo luogo, aggiungere le seguenti `using` direttive al `Program.cs` file, insieme a quelli aggiunti sopra:

```csharp
using Microsoft.Azure.Storage.File.Protocol;
using Microsoft.Azure.Storage.Shared.Protocol;
```

Anche se BLOB di Azure, tabelle di `ServiceProperties` Azure e `Microsoft.Azure.Storage.Shared.Protocol` code di Azure usano il `FileServiceProperties` tipo `Microsoft.Azure.Storage.File.Protocol` condiviso nello spazio dei nomi, File di Azure usa il proprio tipo, ovvero il tipo nello spazio dei nomi. È tuttavia necessario fare riferimento a entrambi gli spazi dei nomi dal codice per la compilazione del codice seguente.

```csharp
// Parse your storage connection string from your application's configuration file.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
// Create the File service client.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Set metrics properties for File service.
// Note that the File service currently uses its own service properties type,
// available in the Microsoft.Azure.Storage.File.Protocol namespace.
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

Se si verificano problemi, è possibile fare riferimento a [Risoluzione dei problemi](storage-troubleshoot-windows-file-connection-problems.md)relativi ai file di Azure in Windows .

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui file di Azure, vedere le risorse seguenti:For more information about Azure Files, see the following resources:

### <a name="conceptual-articles-and-videos"></a>Articoli concettuali e video

* [File di Azure: un file system SMB nel cloud senza conflitti per Windows e Linux](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [Usare File di Azure con Linux](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-file-storage"></a>Supporto degli strumenti per Archiviazione file

* [Introduzione ad AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Risolvere i problemi di File di Azure in Windows](https://docs.microsoft.com/azure/storage/storage-troubleshoot-file-connection-problems)

### <a name="reference"></a>Riferimento

* [API di archiviazione di Azure per .NET](/dotnet/api/overview/azure/storage)
* [File Service REST API](/rest/api/storageservices/File-Service-REST-API) (API REST del servizio file)

### <a name="blog-posts"></a>Post di BLOG

* [Archiviazione file di Azure, ora disponibile in generaleAzure File Storage, now generally available](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Archiviazione file di AzureInside Azure File Storage](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Introduzione al servizio File di Microsoft Azure](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Mantenimento delle connessioni ai file di Microsoft Azure](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)
