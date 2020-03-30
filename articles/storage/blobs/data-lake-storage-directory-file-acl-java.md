---
title: Azure Data Lake Storage Gen2 Java SDK per file & Acl
description: Usare le librerie di Archiviazione di Azure per Java per gestire le directory e gli elenchi di controllo di accesso (ACL) di file e directory negli account di archiviazione con spazio dei nomi gerarchico abilitato.
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 45870dd7d3035b6b49340fd6e8016794088e775a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061568"
---
# <a name="use-java-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Usare Java per gestire directory, file e ACL in Azure Data Lake Storage Gen2Use Java to manage directories, files, and ACLs in Azure Data Lake Storage Gen2

In questo articolo viene illustrato come utilizzare Java per creare e gestire directory, file e autorizzazioni negli account di archiviazione con spazio dei nomi gerarchico abilitato. 

[Riferimento](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) | [all'API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.0.1/index.html) | [degli esempi](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake) | di pacchetto (Maven)[da Gen1 a Gen2](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | [Give Feedback](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Prerequisiti

> [!div class="checklist"]
> * Una sottoscrizione di Azure. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Un account di archiviazione con spazio dei nomi gerarchico abilitato. Seguire [queste](data-lake-storage-quickstart-create-account.md) istruzioni per crearne una.

## <a name="set-up-your-project"></a>Configurare il progetto

Per iniziare, apri [questa pagina](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) e trova la versione più recente della libreria Java. Aprire quindi il file *pom.xml* nell'editor di testo. Aggiungere un elemento di dipendenza che fa riferimento a tale versione.

Se si prevede di autenticare l'applicazione client usando Azure Active Directory (AD), aggiungere una dipendenza alla libreria client segreta di Azure.If you plan to authenticate your client application by using Azure Active Directory (AD), then add a dependency to the Azure Secret Client Library. Vedere [Aggiunta del pacchetto Secret Client Library al progetto.](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project)

Successivamente, aggiungere queste istruzioni imports al file di codice.

```java
import com.azure.core.credential.TokenCredential;
import com.azure.storage.common.StorageSharedKeyCredential;
import com.azure.storage.file.datalake.DataLakeDirectoryClient;
import com.azure.storage.file.datalake.DataLakeFileClient;
import com.azure.storage.file.datalake.DataLakeFileSystemClient;
import com.azure.storage.file.datalake.DataLakeServiceClient;
import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
import com.azure.storage.file.datalake.models.ListPathsOptions;
import com.azure.storage.file.datalake.models.PathAccessControl;
import com.azure.storage.file.datalake.models.PathAccessControlEntry;
import com.azure.storage.file.datalake.models.PathItem;
import com.azure.storage.file.datalake.models.PathPermissions;
import com.azure.storage.file.datalake.models.RolePermissions;
```

## <a name="connect-to-the-account"></a>Connettersi all'account 

Per usare i frammenti in questo articolo, è necessario creare un **DataLakeServiceClient** istanza che rappresenta l'account di archiviazione. 

### <a name="connect-by-using-an-account-key"></a>Connettersi utilizzando una chiave dell'account

Questo è il modo più semplice per connettersi a un account. 

In questo esempio viene creata **un'istanza DataLakeServiceClient** utilizzando una chiave account.

```java

static public DataLakeServiceClient GetDataLakeServiceClient
(String accountName, String accountKey){

    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    DataLakeServiceClientBuilder builder = new DataLakeServiceClientBuilder();

    builder.credential(sharedKeyCredential);
    builder.endpoint("https://" + accountName + ".dfs.core.windows.net");

    return builder.buildClient();
}      
```

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Connettersi tramite Azure Active Directory (Azure AD)Connect by using Azure Active Directory (Azure AD)

È possibile usare la libreria client di [identità di Azure per Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) per autenticare l'applicazione con Azure AD.

In questo esempio viene creata **un'istanza DataLakeServiceClient** utilizzando un ID client, un segreto client e un ID tenant.  Per ottenere questi valori, vedere [Acquisire un token da Azure AD per autorizzare le richieste da un'applicazione client.](../common/storage-auth-aad-app.md)

```java
static public DataLakeServiceClient GetDataLakeServiceClient
    (String accountName, String clientId, String ClientSecret, String tenantID){

    String endpoint = "https://" + accountName + ".dfs.core.windows.net";
        
    ClientSecretCredential clientSecretCredential = new ClientSecretCredentialBuilder()
    .clientId(clientId)
    .clientSecret(ClientSecret)
    .tenantId(tenantID)
    .build();
           
    DataLakeServiceClientBuilder builder = new DataLakeServiceClientBuilder();
    return builder.credential(clientSecretCredential).endpoint(endpoint).buildClient();
 } 
```

> [!NOTE]
> Per altri esempi, vedere la [libreria client di identità di Azure per](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) la documentazione Java.For more examples, see the Azure identity client library for Java documentation.


## <a name="create-a-file-system"></a>Creare un file system

Un file system funge da contenitore per i file. È possibile crearne uno chiamando il **metodo DataLakeServiceClient.createFileSystem** .

In questo esempio viene `my-file-system`creato un file system denominato . 

```java
static public DataLakeFileSystemClient CreateFileSystem
(DataLakeServiceClient serviceClient){

    return serviceClient.createFileSystem("my-file-system");
}
```

## <a name="create-a-directory"></a>Creare una directory

Creare un riferimento alla directory chiamando il metodo **DataLakeFileSystemClient.createDirectory** .

In questo esempio `my-directory` viene aggiunta una directory denominata a `my-subdirectory`un file system, quindi viene aggiunta una sottodirectory denominata . 

```java
static public DataLakeDirectoryClient CreateDirectory
(DataLakeServiceClient serviceClient, String fileSystemName){
    
    DataLakeFileSystemClient fileSystemClient =
    serviceClient.getFileSystemClient(fileSystemName);

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.createDirectory("my-directory");

    return directoryClient.createSubDirectory("my-subdirectory");
}
```

## <a name="rename-or-move-a-directory"></a>Rinominare o spostare una directory

Rinominare o spostare una directory chiamando il metodo **DataLakeDirectoryClient.rename** . Passare il percorso della directory desiderata un parametro. 

In questo esempio viene rinominata `my-subdirectory-renamed`una sottodirectory con il nome .

```java
static public DataLakeDirectoryClient
    RenameDirectory(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory/my-subdirectory");

    return directoryClient.rename(
        fileSystemClient.getFileSystemName(),"my-subdirectory-renamed");
}
```

In questo esempio `my-subdirectory-renamed` viene spostata una directory `my-directory-2`denominata in una sottodirectory di una directory denominata . 

```java
static public DataLakeDirectoryClient MoveDirectory
(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory/my-subdirectory-renamed");

    return directoryClient.rename(
        fileSystemClient.getFileSystemName(),"my-directory-2/my-subdirectory-renamed");                
}
```

## <a name="delete-a-directory"></a>Eliminare una directory

Eliminare una directory chiamando il metodo **DataLakeDirectoryClient.deleteWithResponse** .

In questo esempio viene `my-directory`eliminata una directory denominata .   

```java
static public void DeleteDirectory(DataLakeFileSystemClient fileSystemClient){
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    directoryClient.deleteWithResponse(true, null, null, null);
}
```

## <a name="manage-a-directory-acl"></a>Gestire un ACL di directory

In questo esempio viene ottenuto e `my-directory`quindi impostato l'ACL di una directory denominata . In questo esempio vengono fornite all'utente proprietario le autorizzazioni di lettura, scrittura ed esecuzione, al gruppo proprietario solo le autorizzazioni di lettura ed esecuzione e a tutti gli altri accesso in lettura.

> [!NOTE]
> Se l'applicazione autorizza l'accesso tramite Azure Active Directory (Azure AD), assicurarsi che all'entità di sicurezza usata dall'applicazione per autorizzare l'accesso sia stato assegnato il ruolo Proprietario dati BLOB di [archiviazione.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) Per altre informazioni sull'applicazione delle autorizzazioni ACL e sugli effetti della modifica, vedere Controllo di [accesso in Archiviazione di Azure Data Lake Gen2.](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)

```java
static public void ManageDirectoryACLs(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    PathAccessControl directoryAccessControl =
        directoryClient.getAccessControl();

    List<PathAccessControlEntry> pathPermissions = directoryAccessControl.getAccessControlList();
       
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));
             
    RolePermissions groupPermission = new RolePermissions();
    groupPermission.setExecutePermission(true).setReadPermission(true);
  
    RolePermissions ownerPermission = new RolePermissions();
    ownerPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);
  
    RolePermissions otherPermission = new RolePermissions();
    otherPermission.setReadPermission(true);
  
    PathPermissions permissions = new PathPermissions();
  
    permissions.setGroup(groupPermission);
    permissions.setOwner(ownerPermission);
    permissions.setOther(otherPermission);

    directoryClient.setPermissions(permissions, null, null);

    pathPermissions = directoryClient.getAccessControl().getAccessControlList();
     
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));

}

```

## <a name="upload-a-file-to-a-directory"></a>Caricare un file in una directory

Creare innanzitutto un riferimento al file nella directory di destinazione creando un'istanza della classe **DataLakeFileClient.** Caricare un file chiamando il metodo **DataLakeFileClient.append** . Assicurarsi di completare il caricamento chiamando il **metodo DataLakeFileClient.FlushAsync.**

In questo esempio viene caricato un `my-directory`file di testo in una directory denominata .'

```java
static public void UploadFile(DataLakeFileSystemClient fileSystemClient) 
    throws FileNotFoundException{
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = directoryClient.createFile("uploaded-file.txt");

    File file = new File("C:\\mytestfile.txt");

    InputStream targetStream = new FileInputStream(file);

    long fileSize = file.length();

    fileClient.append(targetStream, 0, fileSize);

    fileClient.flush(fileSize);
}
```

> [!TIP]
> Se le dimensioni del file sono grandi, il codice dovrà effettuare più chiamate al metodo **DataLakeFileClient.append.If** your file size is large, your code will have to make multiple calls to the DataLakeFileClient.append method. È consigliabile usare il metodo **DataLakeFileClient.uploadFromFile.Consider using the DataLakeFileClient.uploadFromFile** method instead. In questo modo, è possibile caricare l'intero file in una singola chiamata. 
>
> Vedere la sezione successiva per un esempio.

## <a name="upload-a-large-file-to-a-directory"></a>Caricare un file di grandi dimensioni in una directory

Utilizzare il metodo **DataLakeFileClient.uploadFromFile** per caricare file di grandi dimensioni senza dover effettuare più chiamate al metodo **DataLakeFileClient.append.**

```java
static public void UploadFileBulk(DataLakeFileSystemClient fileSystemClient) 
    throws FileNotFoundException{
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = directoryClient.getFileClient("uploaded-file.txt");

    fileClient.uploadFromFile("C:\\mytestfile.txt");

    }

```


## <a name="manage-a-file-acl"></a>Gestire un ACL di file

In questo esempio viene ottenuto e `upload-file.txt`quindi impostato l'ACL di un file denominato . In questo esempio vengono fornite all'utente proprietario le autorizzazioni di lettura, scrittura ed esecuzione, al gruppo proprietario solo le autorizzazioni di lettura ed esecuzione e a tutti gli altri accesso in lettura.

> [!NOTE]
> Se l'applicazione autorizza l'accesso tramite Azure Active Directory (Azure AD), assicurarsi che all'entità di sicurezza usata dall'applicazione per autorizzare l'accesso sia stato assegnato il ruolo Proprietario dati BLOB di [archiviazione.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) Per altre informazioni sull'applicazione delle autorizzazioni ACL e sugli effetti della modifica, vedere Controllo di [accesso in Archiviazione di Azure Data Lake Gen2.](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)

```java
static public void ManageFileACLs(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.getFileClient("uploaded-file.txt");

    PathAccessControl fileAccessControl =
        fileClient.getAccessControl();

    List<PathAccessControlEntry> pathPermissions = fileAccessControl.getAccessControlList();
     
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));
           
    RolePermissions groupPermission = new RolePermissions();
    groupPermission.setExecutePermission(true).setReadPermission(true);

    RolePermissions ownerPermission = new RolePermissions();
    ownerPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);

    RolePermissions otherPermission = new RolePermissions();
    otherPermission.setReadPermission(true);

    PathPermissions permissions = new PathPermissions();

    permissions.setGroup(groupPermission);
    permissions.setOwner(ownerPermission);
    permissions.setOther(otherPermission);

    fileClient.setPermissions(permissions, null, null);

    pathPermissions = fileClient.getAccessControl().getAccessControlList();
   
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));

}
```

## <a name="download-from-a-directory"></a>Scarica da una directory

Creare innanzitutto un'istanza **DataLakeFileClient** che rappresenta il file che si desidera scaricare. Utilizzare il metodo **DataLakeFileClient.read** per leggere il file. Utilizzare qualsiasi API di elaborazione file .NET per salvare i byte dal flusso in un file. 

```java
static public void DownloadFile(DataLakeFileSystemClient fileSystemClient)
    throws FileNotFoundException, java.io.IOException{

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.getFileClient("uploaded-file.txt");

    File file = new File("C:\\downloadedFile.txt");

    OutputStream targetStream = new FileOutputStream(file);

    fileClient.read(targetStream);

    targetStream.close();
      
}

```

## <a name="list-directory-contents"></a>Elencare il contenuto della directory

In questo esempio vengono stampati i nomi di `my-directory`ogni file che si trova in una directory denominata .

```java
static public void ListFilesInDirectory(DataLakeFileSystemClient fileSystemClient){
        
    ListPathsOptions options = new ListPathsOptions();
    options.setPath("my-directory");
     
    PagedIterable<PathItem> pagedIterable = 
    fileSystemClient.listPaths(options, null);

    java.util.Iterator<PathItem> iterator = pagedIterable.iterator();
       
    PathItem item = iterator.next();

    while (item != null)
    {
        System.out.println(item.getName());


        if (!iterator.hasNext())
        {
            break;
        }
            
        item = iterator.next();
    }

}
```

## <a name="see-also"></a>Vedere anche

* [Documentazione di riferimento delle API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.0.1/index.html)
* [Pacchetto (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)
* [Esempi](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
* [Mapping da Gen1 a Gen2](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [Problemi noti](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Invia commenti e suggerimenti](https://github.com/Azure/azure-sdk-for-java/issues)