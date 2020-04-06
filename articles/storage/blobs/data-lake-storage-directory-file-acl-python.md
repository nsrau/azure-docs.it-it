---
title: Azure Data Lake Storage Gen2 Python SDK per file & Acl
description: Utilizzare Python gestire le directory e gli elenchi di controllo di accesso (ACL) di file e directory negli account di archiviazione con spazio dei nomi gerarchico (HNS) abilitato.
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: a00713df2cdda626a76cc648826f7e56df214232
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668716"
---
# <a name="use-python-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Usare Python per gestire directory, file e ACL in Azure Data Lake Storage Gen2

Questo articolo illustra come usare Python per creare e gestire directory, file e autorizzazioni negli account di archiviazione con spazio dei nomi gerarchico abilitato. 

[Riferimento](https://pypi.org/project/azure-storage-file-datalake/) | [dell'API](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-storage-file-datalake/12.0.0/azure.storage.filedatalake.html) | degli esempi del pacchetto (Python Package Index)[da](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples) | [Gen1 a Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | [Give Feedback](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>Prerequisiti

> [!div class="checklist"]
> * Una sottoscrizione di Azure. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Un account di archiviazione con spazio dei nomi gerarchico abilitato. Seguire [queste](data-lake-storage-quickstart-create-account.md) istruzioni per crearne una.

## <a name="set-up-your-project"></a>Configurare il progetto

Installare la libreria client di Archiviazione di Azure Data Lake per Python usando [pip](https://pypi.org/project/pip/).

```
pip install azure-storage-file-datalake --pre
```

Aggiungere queste istruzioni import all'inizio del file di codice.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

## <a name="connect-to-the-account"></a>Connettersi all'account

Per usare i frammenti in questo articolo, è necessario creare un **DataLakeServiceClient** istanza che rappresenta l'account di archiviazione. 

### <a name="connect-by-using-an-account-key"></a>Connettersi utilizzando una chiave dell'account

Questo è il modo più semplice per connettersi a un account. 

In questo esempio viene creata **un'istanza DataLakeServiceClient** utilizzando una chiave account.

```python
try:  
    global service_client
        
    service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
        "https", storage_account_name), credential=storage_account_key)
    
except Exception as e:
    print(e)
```
 
- Sostituire il valore segnaposto `storage_account_name` con il nome del proprio account di archiviazione.

- Sostituire `storage_account_key` il valore segnaposto con la chiave di accesso dell'account di archiviazione.

### <a name="connect-by-using-azure-active-directory-ad"></a>Connettersi tramite Azure Active Directory (AD)Connect by using Azure Active Directory (AD)

È possibile usare la libreria client di [identità](https://pypi.org/project/azure-identity/) di Azure per Python per autenticare l'applicazione con Azure AD.

In questo esempio viene creata **un'istanza DataLakeServiceClient** utilizzando un ID client, un segreto client e un ID tenant.  Per ottenere questi valori, vedere [Acquisire un token da Azure AD per autorizzare le richieste da un'applicazione client.](../common/storage-auth-aad-app.md)

```python
def initialize_storage_account_ad(storage_account_name, client_id, client_secret, tenant_id):
    
    try:  
        global service_client

        credential = ClientSecretCredential(tenant_id, client_id, client_secret)

        service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
            "https", storage_account_name), credential=credential)
    
    except Exception as e:
        print(e)
```

> [!NOTE]
> Per altri esempi, vedere la [libreria client di identità di Azure per](https://pypi.org/project/azure-identity/) la documentazione di Python.For more examples, see the Azure identity client library for Python documentation.

## <a name="create-a-file-system"></a>Creare un file system

Un file system funge da contenitore per i file. È possibile crearne uno chiamando il **metodo FileSystemDataLakeServiceClient.create_file_system.**

In questo esempio viene `my-file-system`creato un file system denominato .

```python
def create_file_system():
    try:
        global file_system_client

        file_system_client = service_client.create_file_system(file_system="my-file-system")
    
    except Exception as e:
        print(e) 
```


## <a name="create-a-directory"></a>Creare una directory

Creare un riferimento alla directory chiamando il **FileSystemClient.create_directory** metodo.

In questo esempio `my-directory` viene aggiunta una directory denominata a un file system. 

```python
def create_directory():
    try:
        file_system_client.create_directory("my-directory")
    
    except Exception as e:
     print(e) 
```

## <a name="rename-or-move-a-directory"></a>Rinominare o spostare una directory

Rinominare o spostare una directory chiamando il **DataLakeDirectoryClient.rename_directory** metodo. Passare il percorso della directory desiderata un parametro. 

In questo esempio viene rinominata `my-subdirectory-renamed`una sottodirectory con il nome .

```python
def rename_directory():
    try:
       
       file_system_client = service_client.get_file_system_client(file_system="my-file-system")
       directory_client = file_system_client.get_directory_client("my-directory")
       
       new_dir_name = "my-directory-renamed"
       directory_client.rename_directory(rename_destination=directory_client.file_system_name + '/' + new_dir_name)

    except Exception as e:
     print(e) 
```

## <a name="delete-a-directory"></a>Eliminare una directory

Eliminare una directory chiamando il metodo **DataLakeDirectoryClient.delete_directory** .

In questo esempio viene `my-directory`eliminata una directory denominata .  

```python
def delete_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")
        directory_client = file_system_client.get_directory_client("my-directory")

        directory_client.delete_directory()
    except Exception as e:
     print(e) 
```

## <a name="manage-directory-permissions"></a>Gestire le autorizzazioni della directory

Ottenere l'elenco di controllo di accesso (ACL) di una directory chiamando il metodo **DataLakeDirectoryClient.get_access_control** e impostarlo chiamando il metodo **DataLakeDirectoryClient.set_access_control** .

> [!NOTE]
> Se l'applicazione autorizza l'accesso tramite Azure Active Directory (Azure AD), assicurarsi che all'entità di sicurezza usata dall'applicazione per autorizzare l'accesso sia stato assegnato il ruolo Proprietario dati BLOB di [archiviazione.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) Per altre informazioni sull'applicazione delle autorizzazioni ACL e sugli effetti della modifica, vedere Controllo di [accesso in Archiviazione di Azure Data Lake Gen2.](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)

In questo esempio viene ottenuto `my-directory`e impostato l'ACL di una directory denominata . La `rwxr-xrw-` stringa concede all'utente proprietario le autorizzazioni di lettura, scrittura ed esecuzione, concede al gruppo proprietario solo le autorizzazioni di lettura ed esecuzione e concede a tutti gli altri l'autorizzazione di lettura e scrittura.

```python
def manage_directory_permissions():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])
        
        new_dir_permissions = "rwxr-xrw-"
        
        directory_client.set_access_control(permissions=new_dir_permissions)
        
        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])
    
    except Exception as e:
     print(e) 
```

## <a name="upload-a-file-to-a-directory"></a>Caricare un file in una directory 

Creare innanzitutto un riferimento al file nella directory di destinazione creando un'istanza della classe **DataLakeFileClient.** Caricare un file chiamando il **DataLakeFileClient.append_data** metodo. Assicurarsi di completare il caricamento chiamando il **DataLakeFileClient.flush_data** metodo.

In questo esempio viene caricato un `my-directory`file di testo in una directory denominata .   

```python
def upload_file_to_directory():
    try:

        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.create_file("uploaded-file.txt")
        local_file = open("C:\\file-to-upload.txt",'r')

        file_contents = local_file.read()

        file_client.append_data(data=file_contents, offset=0, length=len(file_contents))

        file_client.flush_data(len(file_contents))

    except Exception as e:
      print(e) 
```

> [!TIP]
> Se le dimensioni del file sono grandi, il codice dovrà effettuare più chiamate al metodo **DataLakeFileClient.append_data.** È consigliabile utilizzare il metodo **DataLakeFileClient.upload_data.Consider using the DataLakeFileClient.upload_data** method instead. In questo modo, è possibile caricare l'intero file in una singola chiamata. 

## <a name="upload-a-large-file-to-a-directory"></a>Caricare un file di grandi dimensioni in una directory

Utilizzare il metodo **DataLakeFileClient.upload_data** per caricare file di grandi dimensioni senza dover effettuare più chiamate al metodo **DataLakeFileClient.append_data** .

```python
def upload_file_to_directory_bulk():
    try:

        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.get_file_client("uploaded-file.txt")

        local_file = open("C:\\file-to-upload.txt",'r')

        file_contents = local_file.read()

        file_client.upload_data(file_contents, overwrite=True)

    except Exception as e:
      print(e) 
```

## <a name="manage-file-permissions"></a>Gestire le autorizzazioni per i file

Ottenere l'elenco di controllo di accesso (ACL) di un file chiamando il metodo **DataLakeFileClient.get_access_control** e impostarlo chiamando il metodo **DataLakeFileClient.set_access_control** .

> [!NOTE]
> Se l'applicazione autorizza l'accesso tramite Azure Active Directory (Azure AD), assicurarsi che all'entità di sicurezza usata dall'applicazione per autorizzare l'accesso sia stato assegnato il ruolo Proprietario dati BLOB di [archiviazione.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) Per altre informazioni sull'applicazione delle autorizzazioni ACL e sugli effetti della modifica, vedere Controllo di [accesso in Archiviazione di Azure Data Lake Gen2.](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)

In questo esempio viene ottenuto `my-file.txt`e impostato l'ACL di un file denominato . La `rwxr-xrw-` stringa concede all'utente proprietario le autorizzazioni di lettura, scrittura ed esecuzione, concede al gruppo proprietario solo le autorizzazioni di lettura ed esecuzione e concede a tutti gli altri l'autorizzazione di lettura e scrittura.

```python
def manage_file_permissions():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.get_file_client("uploaded-file.txt")

        acl_props = file_client.get_access_control()
        
        print(acl_props['permissions'])
        
        new_file_permissions = "rwxr-xrw-"
        
        file_client.set_access_control(permissions=new_file_permissions)
        
        acl_props = file_client.get_access_control()
        
        print(acl_props['permissions'])

    except Exception as e:
     print(e) 
```

## <a name="download-from-a-directory"></a>Scarica da una directory 

Aprire un file locale per la scrittura. Creare quindi un'istanza **DataLakeFileClient** che rappresenta il file che si desidera scaricare. Chiamare **DataLakeFileClient.read_file** per leggere i byte dal file e quindi scrivere tali byte nel file locale. 

```python
def download_file_from_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        local_file = open("C:\\file-to-download.txt",'wb')

        file_client = directory_client.get_file_client("uploaded-file.txt")

        download = file_client.download_file()

        downloaded_bytes = download.readall()

        local_file.write(downloaded_bytes)

        local_file.close()

    except Exception as e:
     print(e)
```
## <a name="list-directory-contents"></a>Elencare il contenuto della directory

Elencare il contenuto della directory chiamando il **metodo FileSystemClient.get_paths** e quindi enumerando i risultati.

In questo esempio viene stampato il percorso di ogni sottodirectory e file che si trova in una directory denominata `my-directory`.

```python
def list_directory_contents():
    try:
        
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        paths = file_system_client.get_paths(path="my-directory")

        for path in paths:
            print(path.name + '\n')

    except Exception as e:
     print(e) 
```

## <a name="see-also"></a>Vedere anche

* [Documentazione di riferimento delle API](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-storage-file-datalake/12.0.0b5/index.html)
* [Pacchetto (indice pacchetto Python)](https://pypi.org/project/azure-storage-file-datalake/)
* [Esempi](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
* [Mapping da Gen1 a Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [Problemi noti](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Invia commenti e suggerimenti](https://github.com/Azure/azure-sdk-for-python/issues)
