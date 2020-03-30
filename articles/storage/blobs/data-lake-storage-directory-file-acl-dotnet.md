---
title: Azure Data Lake Storage Gen2 SDK .NET per file & ACL
description: Usare la libreria client di Archiviazione di Azure per gestire directory ed elenchi di controllo di accesso (ACL) di file e directory negli account di archiviazione con spazio dei nomi gerarchico abilitato.
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: b83d0d2d765b60585832f1a3e7c610f05eac075c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061573"
---
# <a name="use-net-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Usare .NET per gestire directory, file e ACL in Azure Data Lake Storage Gen2

Questo articolo illustra come usare .NET per creare e gestire directory, file e autorizzazioni negli account di archiviazione con spazio dei nomi gerarchico abilitato. 

[Riferimento](https://www.nuget.org/packages/Azure.Storage.Files.DataLake) | [all'API](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake) | [degli esempi](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake) | di pacchetti (NuGet)[da Gen1 a Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md) | [Give Feedback](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>Prerequisiti

> [!div class="checklist"]
> * Una sottoscrizione di Azure. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Un account di archiviazione con spazio dei nomi gerarchico abilitato. Seguire [queste](data-lake-storage-quickstart-create-account.md) istruzioni per crearne una.

## <a name="set-up-your-project"></a>Configurare il progetto

Per iniziare, installare il pacchetto Azure.Storage.Files.DataLake NuGet.To get started, install the [Azure.Storage.Files.DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) NuGet package.

Per ulteriori informazioni su come installare i pacchetti NuGet, vedere [Installare e gestire pacchetti in Visual Studio utilizzando NuGet Package Manager](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio).

Quindi, aggiungere queste istruzioni using all'inizio del file di codice.

```csharp
using Azure.Storage.Files.DataLake;
using Azure.Storage.Files.DataLake.Models;
using Azure.Storage;
using System.IO;
using Azure;
```

## <a name="connect-to-the-account"></a>Connettersi all'account

Per usare i frammenti in questo articolo, è necessario creare un [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) istanza che rappresenta l'account di archiviazione. 

### <a name="connect-by-using-an-account-key"></a>Connettersi utilizzando una chiave dell'account

Questo è il modo più semplice per connettersi a un account. 

In questo esempio viene creata [un'istanza DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) utilizzando una chiave account.

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient,
    string accountName, string accountKey)
{
    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient
        (new Uri(dfsUri), sharedKeyCredential);
}
```

### <a name="connect-by-using-azure-active-directory-ad"></a>Connettersi tramite Azure Active Directory (AD)Connect by using Azure Active Directory (AD)

È possibile usare la libreria client di identità di [Azure per .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) per autenticare l'applicazione con Azure AD.

In questo esempio viene creata [un'istanza DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) utilizzando un ID client, un segreto client e un ID tenant.  Per ottenere questi valori, vedere [Acquisire un token da Azure AD per autorizzare le richieste da un'applicazione client.](../common/storage-auth-aad-app.md)

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient, 
    String accountName, String clientID, string clientSecret, string tenantID)
{

    TokenCredential credential = new ClientSecretCredential(
        tenantID, clientID, clientSecret, new TokenCredentialOptions());

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient(new Uri(dfsUri), credential);
}

```

> [!NOTE]
> Per altri esempi, vedere la [libreria client di identità di Azure per](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) la documentazione di .NET.For more examples, see the Azure identity client library for .NET documentation.For more examples, see the Azure identity client library for .NET documentation

## <a name="create-a-file-system"></a>Creare un file system

Un file system funge da contenitore per i file. È possibile crearne uno chiamando il metodo [DataLakeServiceClient.CreateFileSystem](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient.createfilesystemasync) .

In questo esempio viene `my-file-system`creato un file system denominato . 

```cs
public async Task<DataLakeFileSystemClient> CreateFileSystem
    (DataLakeServiceClient serviceClient)
{
        return await serviceClient.CreateFileSystemAsync("my-file-system");
}
```

## <a name="create-a-directory"></a>Creare una directory

Creare un riferimento alla directory chiamando il metodo [DataLakeFileSystemClient.CreateDirectoryAsync.Create](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.createdirectoryasync) a directory reference by calling the DataLakeFileSystemClient.CreateDirectoryAsync method.

In questo esempio `my-directory` viene aggiunta una directory denominata a `my-subdirectory`un file system, quindi viene aggiunta una sottodirectory denominata . 

```cs
public async Task<DataLakeDirectoryClient> CreateDirectory
    (DataLakeServiceClient serviceClient, string fileSystemName)
{
    DataLakeFileSystemClient fileSystemClient =
        serviceClient.GetFileSystemClient(fileSystemName);

    DataLakeDirectoryClient directoryClient =
        await fileSystemClient.CreateDirectoryAsync("my-directory");

    return await directoryClient.CreateSubDirectoryAsync("my-subdirectory");
}
```

## <a name="rename-or-move-a-directory"></a>Rinominare o spostare una directory

Rinominare o spostare una directory chiamando il metodo [DataLakeDirectoryClient.RenameAsync.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.renameasync) Passare il percorso della directory desiderata un parametro. 

In questo esempio viene rinominata `my-subdirectory-renamed`una sottodirectory con il nome .

```cs
public async Task<DataLakeDirectoryClient> 
    RenameDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory/my-subdirectory");

    return await directoryClient.RenameAsync("my-directory/my-subdirectory-renamed");
}
```

In questo esempio `my-subdirectory-renamed` viene spostata una directory `my-directory-2`denominata in una sottodirectory di una directory denominata . 

```cs
public async Task<DataLakeDirectoryClient> MoveDirectory
    (DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
            fileSystemClient.GetDirectoryClient("my-directory/my-subdirectory-renamed");

    return await directoryClient.RenameAsync("my-directory-2/my-subdirectory-renamed");                
}
```

## <a name="delete-a-directory"></a>Eliminare una directory

Eliminare una directory chiamando il metodo [DataLakeDirectoryClient.Delete](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.delete) .

In questo esempio viene `my-directory`eliminata una directory denominata .  

```cs
public void DeleteDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    directoryClient.Delete();
}
```

## <a name="manage-a-directory-acl"></a>Gestire un ACL di directory

Ottenere l'elenco di controllo di accesso (ACL) di una directory chiamando il metodo [DataLakeDirectoryClient.GetAccessControlAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) e impostare l'ACL chiamando il metodo [DataLakeDirectoryClient.SetAccessControlList](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) .

> [!NOTE]
> Se l'applicazione autorizza l'accesso tramite Azure Active Directory (Azure AD), assicurarsi che all'entità di sicurezza usata dall'applicazione per autorizzare l'accesso sia stato assegnato il ruolo Proprietario dati BLOB di [archiviazione.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) Per altre informazioni sull'applicazione delle autorizzazioni ACL e sugli effetti della modifica, vedere Controllo di [accesso in Archiviazione di Azure Data Lake Gen2.](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control) 

In questo esempio viene ottenuto `my-directory`e impostato l'ACL di una directory denominata . La `user::rwx,group::r-x,other::rw-` stringa concede all'utente proprietario le autorizzazioni di lettura, scrittura ed esecuzione, concede al gruppo proprietario solo le autorizzazioni di lettura ed esecuzione e concede a tutti gli altri l'autorizzazione di lettura e scrittura.

```cs
public async Task ManageDirectoryACLs(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    PathAccessControl directoryAccessControl =
        await directoryClient.GetAccessControlAsync();

    Console.WriteLine(directoryAccessControl.AccessControlList);

    IList<PathAccessControlItem> accessControlList
        = PathAccessControlExtensions.ParseAccessControlList
        ("user::rwx,group::r-x,other::rw-");

    directoryClient.SetAccessControlList(accessControlList);

}

```

## <a name="upload-a-file-to-a-directory"></a>Caricare un file in una directory

Creare innanzitutto un riferimento al file nella directory di destinazione creando un'istanza della classe [DataLakeFileClient.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient) Caricare un file chiamando il metodo [DataLakeFileClient.AppendAsync.Upload](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) a file by calling the DataLakeFileClient.AppendAsync method. Assicurarsi di completare il caricamento chiamando il [metodo DataLakeFileClient.FlushAsync.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.flushasync)

In questo esempio viene caricato un `my-directory`file di testo in una directory denominata .    

```cs
public async Task UploadFile(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = await directoryClient.CreateFileAsync("uploaded-file.txt");

    FileStream fileStream = 
        File.OpenRead("C:\\file-to-upload.txt");

    long fileSize = fileStream.Length;

    await fileClient.AppendAsync(fileStream, offset: 0);

    await fileClient.FlushAsync(position: fileSize);

}
```

> [!TIP]
> Se le dimensioni del file sono grandi, il codice dovrà effettuare più chiamate a [DataLakeFileClient.AppendAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync). Valutare invece l'utilizzo del metodo [DataLakeFileClient.UploadAsync.Consider using the DataLakeFileClient.UploadAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync?view=azure-dotnet#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) method instead. In questo modo, è possibile caricare l'intero file in una singola chiamata. 
>
> Vedere la sezione successiva per un esempio.

## <a name="upload-a-large-file-to-a-directory"></a>Caricare un file di grandi dimensioni in una directory

Utilizzare il metodo [DataLakeFileClient.UploadAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync?view=azure-dotnet#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) per caricare file di grandi dimensioni senza dover effettuare più chiamate al metodo [DataLakeFileClient.AppendAsync.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync)

```cs
public async Task UploadFileBulk(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = directoryClient.GetFileClient("uploaded-file.txt");

    FileStream fileStream =
        File.OpenRead("C:\\file-to-upload.txt");

    await fileClient.UploadAsync(fileStream);

}

```

## <a name="manage-a-file-acl"></a>Gestire un ACL di file

Ottenere l'elenco di controllo di accesso (ACL) di un file chiamando il metodo [DataLakeFileClient.GetAccessControlAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.getaccesscontrolasync) e impostare l'ACL chiamando il metodo [DataLakeFileClient.SetAccessControlList](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.setaccesscontrollist) .

> [!NOTE]
> Se l'applicazione autorizza l'accesso tramite Azure Active Directory (Azure AD), assicurarsi che all'entità di sicurezza usata dall'applicazione per autorizzare l'accesso sia stato assegnato il ruolo Proprietario dati BLOB di [archiviazione.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) Per altre informazioni sull'applicazione delle autorizzazioni ACL e sugli effetti della modifica, vedere Controllo di [accesso in Archiviazione di Azure Data Lake Gen2.](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control) 

In questo esempio viene ottenuto `my-file.txt`e impostato l'ACL di un file denominato . La `user::rwx,group::r-x,other::rw-` stringa concede all'utente proprietario le autorizzazioni di lettura, scrittura ed esecuzione, concede al gruppo proprietario solo le autorizzazioni di lettura ed esecuzione e concede a tutti gli altri l'autorizzazione di lettura e scrittura.

```cs
public async Task ManageFileACLs(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.GetFileClient("hello.txt");

    PathAccessControl FileAccessControl =
        await fileClient.GetAccessControlAsync();

    Console.WriteLine(FileAccessControl.AccessControlList);

    IList<PathAccessControlItem> accessControlList
        = PathAccessControlExtensions.ParseAccessControlList
        ("user::rwx,group::r-x,other::rw-");

    fileClient.SetAccessControlList(accessControlList);
}
```

## <a name="download-from-a-directory"></a>Scarica da una directory 

Creare innanzitutto un'istanza [DataLakeFileClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient) che rappresenta il file che si desidera scaricare. Utilizzare il metodo [DataLakeFileClient.ReadAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.readasync) e analizzare il valore restituito per ottenere un oggetto [Stream.](https://docs.microsoft.com/dotnet/api/system.io.stream) Utilizzare qualsiasi API di elaborazione file .NET per salvare i byte dal flusso in un file. 

In questo esempio vengono utilizzati un [BinaryReader](https://docs.microsoft.com/dotnet/api/system.io.binaryreader) e un [FileStream](https://docs.microsoft.com/dotnet/api/system.io.filestream) per salvare i byte in un file. 

```cs
public async Task DownloadFile(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.GetFileClient("my-image.png");

    Response<FileDownloadInfo> downloadResponse = await fileClient.ReadAsync();

    BinaryReader reader = new BinaryReader(downloadResponse.Value.Content);

    FileStream fileStream = 
        File.OpenWrite("C:\\my-image-downloaded.png");

    int bufferSize = 4096;

    byte[] buffer = new byte[bufferSize];

    int count;

    while ((count = reader.Read(buffer, 0, buffer.Length)) != 0)
    {
        fileStream.Write(buffer, 0, count);
    }

    await fileStream.FlushAsync();

    fileStream.Close();
}
```

## <a name="list-directory-contents"></a>Elencare il contenuto della directory

Elencare il contenuto della directory chiamando il metodo [FileSystemClient.GetPathsAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getpathsasync) e quindi enumerando i risultati.

In questo esempio vengono stampati i nomi di `my-directory`ogni file che si trova in una directory denominata .

```cs
public async Task ListFilesInDirectory(DataLakeFileSystemClient fileSystemClient)
{
    IAsyncEnumerator<PathItem> enumerator = 
        fileSystemClient.GetPathsAsync("my-directory").GetAsyncEnumerator();

    await enumerator.MoveNextAsync();

    PathItem item = enumerator.Current;

    while (item != null)
    {
        Console.WriteLine(item.Name);

        if (!await enumerator.MoveNextAsync())
        {
            break;
        }
                
        item = enumerator.Current;
    }

}
```

## <a name="see-also"></a>Vedere anche

* [Documentazione di riferimento delle API](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake)
* [Pacchetto (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)
* [Esempi](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
* [Mapping da Gen1 a Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
* [Problemi noti](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Invia commenti e suggerimenti](https://github.com/Azure/azure-sdk-for-net/issues)

