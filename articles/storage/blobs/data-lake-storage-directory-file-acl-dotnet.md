---
title: Usare Azure .NET per i file & ACL in Azure Data Lake Storage Gen2 (anteprima)
description: Usare la libreria client di archiviazione di Azure per gestire directory e elenchi di controllo di accesso (ACL) di file e directory negli account di archiviazione in cui è abilitato lo spazio dei nomi gerarchico (HNS).
author: normesta
ms.service: storage
ms.date: 11/24/2019
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: fb69e0b797243a3403e8899d3f4ef23a9be9451d
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74534267"
---
# <a name="use-net-for-files--acls-in-azure-data-lake-storage-gen2-preview"></a>Usare .NET per i file & ACL in Azure Data Lake Storage Gen2 (anteprima)

Questo articolo illustra come usare .NET per creare e gestire directory, file e autorizzazioni negli account di archiviazione in cui è abilitato lo spazio dei nomi gerarchico (HNS). 

> [!IMPORTANT]
> Il pacchetto NuGet [Azure. storage. files. Datalake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/12.0.0-preview.6) disponibile in questo articolo è attualmente disponibile in anteprima pubblica.

[Pacchetto (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/12.0.0-preview.6) | [esempi](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake) | [riferimento API](https://azuresdkdocs.blob.core.windows.net/$web/dotnet/Azure.Storage.Files.DataLake/12.0.0-preview.6/api/index.html) | [Gen1 al mapping di Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md) | [inviare commenti e suggerimenti](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>Prerequisiti

> [!div class="checklist"]
> * Una sottoscrizione di Azure. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Un account di archiviazione con spazio dei nomi gerarchico (HNS) abilitato. Seguire [queste](data-lake-storage-quickstart-create-account.md) istruzioni per crearne uno.

## <a name="set-up-your-project"></a>Configurare il progetto

Per iniziare, installare il pacchetto NuGet [Azure. storage. files. datalake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/12.0.0-preview.6) .

Per altre informazioni su come installare i pacchetti NuGet, vedere [installare e gestire i pacchetti in Visual Studio usando Gestione pacchetti NuGet](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio).

Aggiungere quindi queste istruzioni using all'inizio del file di codice.

```csharp
using Azure.Storage.Files.DataLake;
using Azure.Storage.Files.DataLake.Models;
using System.IO;
using Azure;
```

## <a name="connect-to-the-account"></a>Connettersi all'account

Per usare i frammenti di codice in questo articolo, è necessario creare un'istanza di **DataLakeServiceClient** che rappresenta l'account di archiviazione. Il modo più semplice per ottenerne uno consiste nell'usare una chiave dell'account. 

Questo esempio crea un'istanza di **DataLakeServiceClient** usando una chiave dell'account.

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

## <a name="create-a-file-system"></a>Creare un file system

Un file system funge da contenitore per i file. È possibile crearne una chiamando il metodo **FileSystemClient. CreateFileSystemAsync** .

In questo esempio viene creato un file system denominato `my-file-system`. 

```cs
public async Task<DataLakeFileSystemClient> CreateFileSystem
    (DataLakeServiceClient serviceClient)
{
        return await serviceClient.CreateFileSystemAsync("my-file-system");
}
```

## <a name="create-a-directory"></a>Creare una directory

Creare un riferimento alla directory chiamando il metodo **FileSystemClient. CreateDirectoryAsync** .

In questo esempio viene aggiunta una directory denominata `my-directory` a un file system, quindi viene aggiunta una sottodirectory denominata `my-subdirectory`. 

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

Rinominare o spostare una directory chiamando il metodo **DirectoryClient. renameAsync** . Passare il percorso della directory desiderata a un parametro. 

Questo esempio rinomina una sottodirectory con il nome `my-subdirectory-renamed`.

```cs
public async Task<DataLakeDirectoryClient> 
    RenameDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory/my-subdirectory");

    return await directoryClient.RenameAsync("my-directory/my-subdirectory-renamed");
}
```

In questo esempio una directory denominata `my-subdirectory-renamed` viene spostata in una sottodirectory di una directory denominata `my-directory-2`. 

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

Eliminare una directory chiamando il metodo **DirectoryClient. Delete** .

In questo esempio viene eliminata una directory denominata `my-directory`.  

```cs
public void DeleteDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    directoryClient.Delete();
}
```

## <a name="manage-a-directory-acl"></a>Gestire un ACL di directory

Ottenere l'elenco di controllo di accesso (ACL) di una directory chiamando il metodo **directoryClient. GetAccessControlAsync** e impostare l'ACL chiamando il metodo **directoryClient. SetAccessControl** .

Questo esempio Mostra come ottenere e impostare l'ACL di una directory denominata `my-directory`. La stringa `user::rwx,group::r-x,other::rw-` fornisce le autorizzazioni di lettura, scrittura ed esecuzione dell'utente proprietario, fornisce al gruppo proprietario solo le autorizzazioni di lettura ed esecuzione e assegna a tutte le altre autorizzazioni di lettura e scrittura.

```cs
public async Task ManageDirectoryACLs(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    PathAccessControl directoryAccessControl =
        await directoryClient.GetAccessControlAsync();

    Console.WriteLine(directoryAccessControl.Acl);

    directoryClient.SetAccessControl("user::rwx,group::r-x,other::rw-");

}

```

## <a name="upload-a-file-to-a-directory"></a>Caricare un file in una directory

Per prima cosa, creare un riferimento a un file nella directory di destinazione creando un'istanza della classe **DataLakeFileClient** . Caricare un file chiamando il metodo **DataLakeFileClient. AppendAsync** . Assicurarsi di completare il caricamento chiamando il metodo **DataLakeFileClient. FlushAsync** .

Questo esempio consente di caricare un file di testo in una directory denominata `my-directory`.    

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

## <a name="manage-a-file-acl"></a>Gestire un ACL di file

Ottenere l'elenco di controllo di accesso (ACL) di un file chiamando il metodo **DataLakeFileClient. GetAccessControlAsync** e impostare l'ACL chiamando il metodo **fileclient. SetAccessControl** .

Questo esempio Mostra come ottenere e impostare l'ACL di un file denominato `my-file.txt`. La stringa `user::rwx,group::r-x,other::rw-` fornisce le autorizzazioni di lettura, scrittura ed esecuzione dell'utente proprietario, fornisce al gruppo proprietario solo le autorizzazioni di lettura ed esecuzione e assegna a tutte le altre autorizzazioni di lettura e scrittura.

```cs
public async Task ManageFileACLs(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.GetFileClient("hello.txt");

    PathAccessControl FileAccessControl =
        await fileClient.GetAccessControlAsync();

    Console.WriteLine(FileAccessControl.Acl);

    fileClient.SetAccessControl("user::rwx,group::r-x,other::rw-");
}
```

## <a name="download-from-a-directory"></a>Scarica da una directory 

Per prima cosa, creare un'istanza di **DataLakeFileClient** che rappresenti il file che si desidera scaricare. Utilizzare il metodo **fileclient. ReadAsync** e analizzare il valore restituito per ottenere un oggetto [flusso](https://docs.microsoft.com/dotnet/api/system.io.stream) . Usare qualsiasi API di elaborazione di file .NET per salvare i byte dal flusso a un file. 

Questo esempio usa un oggetto [BinaryReader](https://docs.microsoft.com/dotnet/api/system.io.binaryreader) e un [FileStream](https://docs.microsoft.com/dotnet/api/system.io.filestream) per salvare i byte in un file. 

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

Elencare il contenuto della directory chiamando il metodo **FileSystemClient. ListPathsAsync** e quindi enumerando i risultati.

In questo esempio vengono stampati i nomi di ogni file che si trova in una directory denominata `my-directory`.

```cs
public async Task ListFilesInDirectory(DataLakeFileSystemClient fileSystemClient)
{
    IAsyncEnumerator<PathItem> enumerator = 
        fileSystemClient.ListPathsAsync("my-directory").GetAsyncEnumerator();

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

## <a name="see-also"></a>Vedi anche

* [Documentazione di riferimento per le API](https://azuresdkdocs.blob.core.windows.net/$web/dotnet/Azure.Storage.Files.DataLake/12.0.0-preview.6/api/index.html)
* [Pacchetto (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/12.0.0-preview.6)
* [Esempi](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
* [Mapping tra Gen1 e Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
* [Problemi noti](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Invia commenti e suggerimenti](https://github.com/Azure/azure-sdk-for-net/issues)

