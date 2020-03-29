---
title: Usare JavaScript per i file & gli ACL in Azure Data Lake Storage Gen2Use JavaScript for files and ACLs in Azure Data Lake Storage Gen2
description: Usare la libreria client di Azure Storage Data Lake per JavaScript per gestire directory ed elenchi di controllo di accesso (ACL) di file e directory negli account di archiviazione con spazio dei nomi gerarchico abilitato.
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 04d0d23bdbdaeda6a4823c900badb3133ba9eeae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061547"
---
# <a name="use-javascript-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Usare JavaScript per gestire directory, file e ACL in Azure Data Lake Storage Gen2

Questo articolo illustra come usare JavaScript per creare e gestire directory, file e autorizzazioni negli account di archiviazione con spazio dei nomi gerarchico abilitato. 

[Package (Node Package Manager)](https://www.npmjs.com/package/@azure/storage-file-datalake) | [Gli esempi](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples) | di pacchetti (Gestione pacchetti nodo)[forniscono commenti e suggerimentiPackage](https://github.com/Azure/azure-sdk-for-java/issues) (Node Package Manager) Samples Give Feedback

## <a name="prerequisites"></a>Prerequisiti

> [!div class="checklist"]
> * Una sottoscrizione di Azure. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Un account di archiviazione con spazio dei nomi gerarchico abilitato. Seguire [queste](data-lake-storage-quickstart-create-account.md) istruzioni per crearne una.
> * Se si utilizza questo pacchetto in un'applicazione Node.js, è necessario Node.js 8.0.0 o versione successiva.

## <a name="set-up-your-project"></a>Configurare il progetto

Installare la libreria client Data Lake per JavaScript aprendo una finestra del terminale e digitando il comando seguente.

```javascript
npm install @azure/storage-file-datalake
```

Importare `storage-file-datalake` il pacchetto inserendo questa istruzione all'inizio del file di codice. 

```javascript
const AzureStorageDataLake = require("@azure/storage-file-datalake");
```

## <a name="connect-to-the-account"></a>Connettersi all'account 

Per usare i frammenti in questo articolo, è necessario creare un **DataLakeServiceClient** istanza che rappresenta l'account di archiviazione. 

### <a name="connect-by-using-an-account-key"></a>Connettersi utilizzando una chiave dell'account

Questo è il modo più semplice per connettersi a un account. 

In questo esempio viene creata **un'istanza DataLakeServiceClient** utilizzando una chiave account.

```javascript

function GetDataLakeServiceClient(accountName, accountKey) {

  const sharedKeyCredential = 
     new StorageSharedKeyCredential(accountName, accountKey);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, sharedKeyCredential);

  return datalakeServiceClient;             
}      

```
> [!NOTE]
> Questo metodo di autorizzazione funziona solo per le applicazioni Node.js.This method of authorization works only for Node.js applications. Se si prevede di eseguire il codice in un browser, è possibile autorizzare usando Azure Active Directory (AD). 

### <a name="connect-by-using-azure-active-directory-ad"></a>Connettersi tramite Azure Active Directory (AD)Connect by using Azure Active Directory (AD)

È possibile usare la libreria client di [identità di Azure per JS](https://www.npmjs.com/package/@azure/identity) per autenticare l'applicazione con Azure AD.

In questo esempio viene creata **un'istanza DataLakeServiceClient** utilizzando un ID client, un segreto client e un ID tenant.  Per ottenere questi valori, vedere [Acquisire un token da Azure AD per autorizzare le richieste da un'applicazione client.](../common/storage-auth-aad-app.md)

```javascript
function GetDataLakeServiceClientAD(accountName, clientID, clientSecret, tenantID) {

  const credential = new ClientSecretCredential(tenantID, clientID, clientSecret);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, credential);

  return datalakeServiceClient;             
}
```

> [!NOTE]
> Per altri esempi, vedere la [libreria client di identità di Azure per](https://www.npmjs.com/package/@azure/identity) la documentazione JS.

## <a name="create-a-file-system"></a>Creare un file system

Un file system funge da contenitore per i file. È possibile crearne uno ottenendo un **FileSystemClient** istanza e quindi chiamando il **FileSystemClient.Create** metodo.

In questo esempio viene `my-file-system`creato un file system denominato . 

```javascript
async function CreateFileSystem(datalakeServiceClient) {

  const fileSystemName = "my-file-system";
  
  const fileSystemClient = datalakeServiceClient.getFileSystemClient(fileSystemName);

  const createResponse = await fileSystemClient.create();
        
}
```

## <a name="create-a-directory"></a>Creare una directory

Creare un riferimento alla directory ottenendo **un'istanza DirectoryClient** e quindi chiamando il metodo **DirectoryClient.create.**

In questo esempio `my-directory` viene aggiunta una directory denominata a un file system. 

```javascript
async function CreateDirectory(fileSystemClient) {
   
  const directoryClient = fileSystemClient.getDirectoryClient("my-directory");
  
  await directoryClient.create();

}
```

## <a name="rename-or-move-a-directory"></a>Rinominare o spostare una directory

Rinominare o spostare una directory chiamando il metodo **DirectoryClient.rename** . Passare il percorso della directory desiderata un parametro. 

In questo esempio viene rinominata `my-directory-renamed`una sottodirectory con il nome .

```javascript
async function RenameDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.move("my-directory-renamed");

}
```

In questo esempio `my-directory-renamed` viene spostata una directory `my-directory-2`denominata in una sottodirectory di una directory denominata . 

```javascript
async function MoveDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory-renamed"); 
  await directoryClient.move("my-directory-2/my-directory-renamed");      

}
```

## <a name="delete-a-directory"></a>Eliminare una directory

Eliminare una directory chiamando il metodo **DirectoryClient.delete** .

In questo esempio viene `my-directory`eliminata una directory denominata .   

```javascript
async function DeleteDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.delete();

}
```

## <a name="manage-a-directory-acl"></a>Gestire un ACL di directory

In questo esempio viene ottenuto e `my-directory`quindi impostato l'ACL di una directory denominata . In questo esempio vengono fornite all'utente proprietario le autorizzazioni di lettura, scrittura ed esecuzione, al gruppo proprietario solo le autorizzazioni di lettura ed esecuzione e a tutti gli altri accesso in lettura.

> [!NOTE]
> Se l'applicazione autorizza l'accesso tramite Azure Active Directory (Azure AD), assicurarsi che all'entità di sicurezza usata dall'applicazione per autorizzare l'accesso sia stato assegnato il ruolo Proprietario dati BLOB di [archiviazione.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) Per altre informazioni sull'applicazione delle autorizzazioni ACL e sugli effetti della modifica, vedere Controllo di [accesso in Archiviazione di Azure Data Lake Gen2.](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)

```javascript
async function ManageDirectoryACLs(fileSystemClient) {

    const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
    const permissions = await directoryClient.getAccessControl();

    console.log(permissions.acl);

    const acl = [
    {
      accessControlType: "user",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: true
      }
    },
    {
      accessControlType: "group",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: false,
        execute: true
      }
    },
    {
      accessControlType: "other",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: false
      }

    }

  ];

  await directoryClient.setAccessControl(acl);
}
```

## <a name="upload-a-file-to-a-directory"></a>Caricare un file in una directory

Per prima cosa, leggi un file. In questo esempio viene `fs` utilizzato il modulo Node.js. Quindi, creare un riferimento al file nella directory di destinazione creando un **FileClient** istanza e quindi chiamando il **FileClient.create** metodo. Caricare un file chiamando il **FileClient.append** metodo. Assicurarsi di completare il caricamento chiamando il **FileClient.flush** metodo.

In questo esempio viene caricato un `my-directory`file di testo in una directory denominata .'

```javascript
async function UploadFile(fileSystemClient) {

  const fs = require('fs') 

  var content = "";
  
  fs.readFile('mytestfile.txt', (err, data) => { 
      if (err) throw err; 

      content = data.toString();

  }) 
  
  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");
  await fileClient.create();
  await fileClient.append(content, 0, content.length);
  await fileClient.flush(content.length);

}
```

## <a name="manage-a-file-acl"></a>Gestire un ACL di file

In questo esempio viene ottenuto e `upload-file.txt`quindi impostato l'ACL di un file denominato . In questo esempio vengono fornite all'utente proprietario le autorizzazioni di lettura, scrittura ed esecuzione, al gruppo proprietario solo le autorizzazioni di lettura ed esecuzione e a tutti gli altri accesso in lettura.

> [!NOTE]
> Se l'applicazione autorizza l'accesso tramite Azure Active Directory (Azure AD), assicurarsi che all'entità di sicurezza usata dall'applicazione per autorizzare l'accesso sia stato assegnato il ruolo Proprietario dati BLOB di [archiviazione.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) Per altre informazioni sull'applicazione delle autorizzazioni ACL e sugli effetti della modifica, vedere Controllo di [accesso in Archiviazione di Azure Data Lake Gen2.](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)

```javascript
async function ManageFileACLs(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt"); 
  const permissions = await fileClient.getAccessControl();

  console.log(permissions.acl);

  const acl = [
  {
    accessControlType: "user",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: true
    }
  },
  {
    accessControlType: "group",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: false,
      execute: true
    }
  },
  {
    accessControlType: "other",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: false
    }

  }

];

await fileClient.setAccessControl(acl);        
}
```

## <a name="download-from-a-directory"></a>Scarica da una directory

Creare innanzitutto un'istanza **FileSystemClient** che rappresenta il file che si desidera scaricare. Utilizzare il metodo **FileSystemClient.read** per leggere il file. Quindi, scrivere il file. In questo esempio viene `fs` utilizzato il modulo Node.js per eseguire questa operazione. 

> [!NOTE]
> Questo metodo di download di un file funziona solo per le applicazioni Node.js. Se si prevede di eseguire il codice in un browser, vedere la libreria client di Azure Storage File Lake per il file leggimi [JavaScript](https://www.npmjs.com/package/@azure/storage-file-datalake) per un esempio di come eseguire questa operazione in un browser. 

```javascript
async function DownloadFile(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");

  const downloadResponse = await fileClient.read();

  const downloaded = await streamToString(downloadResponse.readableStreamBody);
 
  async function streamToString(readableStream) {
    return new Promise((resolve, reject) => {
      const chunks = [];
      readableStream.on("data", (data) => {
        chunks.push(data.toString());
      });
      readableStream.on("end", () => {
        resolve(chunks.join(""));
      });
      readableStream.on("error", reject);
    });
  }   
  
  const fs = require('fs');

  fs.writeFile('mytestfiledownloaded.txt', downloaded, (err) => {
    if (err) throw err;
  });
}

```

## <a name="list-directory-contents"></a>Elencare il contenuto della directory

In questo esempio vengono stampati i nomi di ogni `my-directory`directory e file che si trova in una directory denominata .

```javascript
async function ListFilesInDirectory(fileSystemClient) {
  
  let i = 1;

  let iter = await fileSystemClient.listPaths({path: "my-directory", recursive: true});

  for await (const path of iter) {
    
    console.log(`Path ${i++}: ${path.name}, is directory: ${path.isDirectory}`);
  }

}
```

## <a name="see-also"></a>Vedere anche

* [Package (Node Package Manager)](https://www.npmjs.com/package/@azure/storage-file-datalake)
* [Esempi](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)
* [Invia commenti e suggerimenti](https://github.com/Azure/azure-sdk-for-java/issues)