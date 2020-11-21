---
title: Sviluppare per File di Azure con Java | Microsoft Docs
description: Informazioni su come sviluppare applicazioni e servizi Java che usano File di Azure per archiviare i dati dei file.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2020
ms.custom: devx-track-java
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 25baa278961b93b04e60f2e997b98753cb6cf3ab
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024110"
---
# <a name="develop-for-azure-files-with-java"></a>Sviluppare per File di Azure con Java

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Informazioni sulle nozioni di base sullo sviluppo di applicazioni Java che usano File di Azure per archiviare i dati. Creare un'applicazione console e acquisire informazioni sulle azioni di base usando le API File di Azure:

- Creare ed eliminare condivisioni file di Azure
- Creare ed eliminare directory
- Enumerare file e directory in una condivisione file di Azure
- Caricare, scaricare ed eliminare un file

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="create-a-java-application"></a>Creare un'applicazione Java

Per compilare gli esempi, saranno necessari Java Development Kit (JDK) e [Azure Storage SDK per Java](https://github.com/azure/azure-sdk-for-java). È inoltre necessario aver creato un account di archiviazione di Azure.

## <a name="set-up-your-application-to-use-azure-files"></a>Configurare l'applicazione per usare File di Azure

Per usare le API File di Azure, aggiungere il codice seguente all'inizio del file Java da cui si intende accedere File di Azure.

# <a name="java-v12"></a>[Java V12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_ImportStatements":::

# <a name="java-v11"></a>[V11 Java](#tab/java11)

```java
// Include the following imports to use Azure Files APIs v11
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

---

## <a name="set-up-an-azure-storage-connection-string"></a>Impostare una stringa di connessione di archiviazione di Azure

Per usare File di Azure, è necessario connettersi all'account di archiviazione di Azure. Configurare una stringa di connessione e usarla per connettersi all'account di archiviazione. Definire una variabile statica che contenga la stringa di connessione.

# <a name="java-v12"></a>[Java V12](#tab/java)

Sostituire *\<storage_account_name\>* e *\<storage_account_key\>* con i valori effettivi dell'account di archiviazione.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_ConnectionString":::

# <a name="java-v11"></a>[V11 Java](#tab/java11)

Sostituire *your_storage_account_name* e *your_storage_account_key* con i valori effettivi dell'account di archiviazione.

```java
// Configure the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account_name;" +
    "AccountKey=your_storage_account_key";
```

---

## <a name="access-azure-files-storage"></a>Accesso File di Azure archiviazione

# <a name="java-v12"></a>[Java V12](#tab/java)

Per accedere File di Azure, creare un oggetto [ShareClient](/java/api/com.azure.storage.file.share.shareclient) . Usare la classe [ShareClientBuilder](/java/api/com.azure.storage.file.share.shareclientbuilder) per compilare un nuovo oggetto **ShareClient** .

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createClient":::

# <a name="java-v11"></a>[V11 Java](#tab/java11)

Per accedere all'account di archiviazione, usare l'oggetto **CloudStorageAccount** , passando la stringa di connessione al relativo metodo **Parse** .

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

**CloudStorageAccount.parse** genera un'eccezione InvalidKeyException, sarà quindi necessario inserirlo in un blocco Try-Catch.

---

## <a name="create-a-file-share"></a>Creare una condivisione file

Tutti i file e le directory in File di Azure vengono archiviati in un contenitore denominato condivisione.

# <a name="java-v12"></a>[Java V12](#tab/java)

Il metodo [ShareClient. Create](/java/api/com.azure.storage.file.share.shareclient.create) genera un'eccezione se la condivisione esiste già. Inserire la chiamata per **creare** in un `try/catch` blocco e gestire l'eccezione.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createFileShare":::

# <a name="java-v11"></a>[V11 Java](#tab/java11)

Per ottenere l'accesso a una condivisione e al relativo contenuto, creare un client File di Azure.

```java
// Create the Azure Files client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

In questo modo è possibile ottenere un riferimento a una condivisione.

```java
// Get a reference to the file share
CloudFileShare share = fileClient.getShareReference("sampleshare");
```

Per creare effettivamente la condivisione, usare il metodo **createIfNotExists** dell'oggetto **CloudFileShare** .

```java
if (share.createIfNotExists()) {
    System.out.println("New share created");
}
```

A questo punto, **share** include un riferimento a una condivisione denominata **Sample Share**.

---

## <a name="delete-a-file-share"></a>Eliminare una condivisione file

Il codice di esempio seguente elimina una condivisione file.

# <a name="java-v12"></a>[Java V12](#tab/java)

Eliminare una condivisione chiamando il metodo [ShareClient. Delete](/java/api/com.azure.storage.file.share.shareclient.delete) .

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteFileShare":::

# <a name="java-v11"></a>[V11 Java](#tab/java11)

Eliminare una condivisione chiamando il metodo **deleteIfExists** su un oggetto **CloudFileShare** .

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the file client.
   CloudFileClient fileClient = storageAccount.createCloudFileClient();

   // Get a reference to the file share
   CloudFileShare share = fileClient.getShareReference("sampleshare");

   if (share.deleteIfExists()) {
       System.out.println("sampleshare deleted");
   }
} catch (Exception e) {
    e.printStackTrace();
}
```

---

## <a name="create-a-directory"></a>Creare una directory

Organizzare l'archiviazione inserendo i file all'interno di sottodirectory anziché includerli tutti nella directory radice.

# <a name="java-v12"></a>[Java V12](#tab/java)

Il codice seguente crea una directory chiamando [ShareDirectoryClient. Create](/java/api/com.azure.storage.file.share.sharedirectoryclient.create). Il metodo di esempio restituisce un `Boolean` valore che indica se la directory è stata creata correttamente.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createDirectory":::

# <a name="java-v11"></a>[V11 Java](#tab/java11)

Il codice seguente crea una sottodirectory denominata **SampleDir** nella directory radice.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the sampledir directory
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

if (sampleDir.createIfNotExists()) {
    System.out.println("sampledir created");
} else {
    System.out.println("sampledir already exists");
}
```

---

## <a name="delete-a-directory"></a>Eliminare una directory

L'eliminazione di una directory è un'attività semplice. Non è possibile eliminare una directory che contiene ancora file o sottodirectory.

# <a name="java-v12"></a>[Java V12](#tab/java)

Il metodo [ShareDirectoryClient. Delete](/java/api/com.azure.storage.file.share.sharedirectoryclient.delete) genera un'eccezione se la directory non esiste o non è vuota. Inserire la chiamata a **Delete** in un `try/catch` blocco e gestire l'eccezione.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteDirectory":::

# <a name="java-v11"></a>[V11 Java](#tab/java11)

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory you want to delete
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

// Delete the directory
if ( containerDir.deleteIfExists() ) {
    System.out.println("Directory deleted");
}
```

---

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Enumerare file e directory in una condivisione file di Azure

# <a name="java-v12"></a>[Java V12](#tab/java)

Ottenere un elenco di file e directory chiamando [ShareDirectoryClient. listFilesAndDirectories](/java/api/com.azure.storage.file.share.sharedirectoryclient.listfilesanddirectories). Il metodo restituisce un elenco di oggetti [ShareFileItem](/java/api/com.azure.storage.file.share.models.sharefileitem) su cui è possibile eseguire l'iterazione. Il codice seguente elenca i file e le directory nella directory specificata dal parametro *dirname* .

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_enumerateFilesAndDirs":::

# <a name="java-v11"></a>[V11 Java](#tab/java11)

Ottenere un elenco di file e directory chiamando **listFilesAndDirectories** in un riferimento **CloudFileDirectory** . Il metodo restituisce un elenco di oggetti **ListFileItem** su cui è possibile eseguire l'iterazione. Il codice seguente elenca i file e le directory nella directory radice.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

---

## <a name="upload-a-file"></a>Caricare un file

Informazioni su come caricare un file dalla risorsa di archiviazione locale.

# <a name="java-v12"></a>[Java V12](#tab/java)

Il codice seguente carica un file locale nell'archiviazione file di Azure chiamando il metodo [ShareFileClient. uploadFromFile](/java/api/com.azure.storage.file.share.sharefileclient.uploadfromfile) . Il metodo di esempio seguente restituisce un `Boolean` valore che indica se il file specificato è stato caricato correttamente.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_uploadFile":::

# <a name="java-v11"></a>[V11 Java](#tab/java11)

Ottenere un riferimento alla directory in cui verrà caricato il file chiamando il metodo **getRootDirectoryReference** sull'oggetto share.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();
```

Ora che si dispone di un riferimento alla directory radice della condivisione, è possibile caricarvi un file mediante il codice seguente.

```java
// Define the path to a local file.
final String filePath = "C:\\temp\\Readme.txt";

CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
cloudFile.uploadFromFile(filePath);
```

---

## <a name="download-a-file"></a>Scaricare un file

Una delle operazioni più frequenti è il download dei file dalla risorsa di archiviazione File di Azure.

# <a name="java-v12"></a>[Java V12](#tab/java)

Nell'esempio seguente viene scaricato il file specificato nella directory locale specificata nel parametro *DESTDIR* . Il metodo di esempio rende univoco il nome del file scaricato anteponendo la data e l'ora.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_downloadFile":::

# <a name="java-v11"></a>[V11 Java](#tab/java11)

Nell'esempio seguente viene scaricato SampleFile.txt e ne viene visualizzato il contenuto.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the directory that contains the file
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

//Get a reference to the file you want to download
CloudFile file = sampleDir.getFileReference("SampleFile.txt");

//Write the contents of the file to the console.
System.out.println(file.downloadText());
```

---

## <a name="delete-a-file"></a>Eliminare un file

Un'altra operazione comunemente eseguita in File di Azure è l'eliminazione dei file.

# <a name="java-v12"></a>[Java V12](#tab/java)

Il codice seguente consente di eliminare il file specificato. Innanzitutto, nell'esempio viene creato un [ShareDirectoryClient](/java/api/com.azure.storage.file.share.sharedirectoryclient) in base al parametro *dirname* . Il codice ottiene quindi un [ShareFileClient](/java/api/com.azure.storage.file.share.sharefileclient) dal client di directory in base al parametro *filename* . Infine, il metodo di esempio chiama [ShareFileClient. Delete](/java/api/com.azure.storage.file.share.sharefileclient.delete) per eliminare il file.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteFile":::

# <a name="java-v11"></a>[V11 Java](#tab/java11)

Il codice seguente elimina un file denominato SampleFile.txt memorizzato all'interno di una directory denominata **sampledir**.

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory where the file to be deleted is in
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

String filename = "SampleFile.txt"
CloudFile file;

file = containerDir.getFileReference(filename)
if ( file.deleteIfExists() ) {
    System.out.println(filename + " was deleted");
}
```

---

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su altre API di archiviazione di Azure, seguire i collegamenti seguenti.

- [Azure for Java developers](/azure/developer/java) (Azure per sviluppatori Java)
- [Azure SDK per Java](https://github.com/azure/azure-sdk-for-java)
- [Azure SDK per Android](https://github.com/azure/azure-sdk-for-android)
- [Guida di riferimento alla libreria client di condivisione file di Azure per Java SDK](/java/api/overview/azure/storage-file-share-readme)
- [API REST dei servizi di archiviazione di Azure](/rest/api/storageservices/)
- [Blog del team di Archiviazione di Azure](https://azure.microsoft.com/blog/topics/storage-backup-and-recovery/)
- [Trasferire dati con l'utilità della riga di comando AzCopy](../common/storage-use-azcopy-v10.md)
- [Risoluzione dei problemi di File di Azure - Windows](storage-troubleshoot-windows-file-connection-problems.md)
