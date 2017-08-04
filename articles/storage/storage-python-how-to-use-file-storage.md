---
title: Eseguire lo sviluppo per Archiviazione file di Azure con Python | Microsoft Docs
description: Informazioni su come sviluppare applicazioni e servizi Python che usano Archiviazione file di Azure per archiviare i dati dei file.
services: storage
documentationcenter: python
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 12/08/2016
ms.author: robinsh
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: a1a37266908277b54e7b42d85b9b4873af77e622
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---

# <a name="develop-for-azure-file-storage-with-python"></a>Eseguire lo sviluppo per Archiviazione file di Azure con Python
[!INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../includes/storage-try-azure-tools-files.md)]

## <a name="about-this-tutorial"></a>Informazioni sull'esercitazione
Questa esercitazione illustra le nozioni base per l'uso di Python per sviluppare applicazioni o servizi che usano Archiviazione file di Azure per archiviare i dati dei file. In questa esercitazione verrà creata una semplice applicazione console e verrà mostrato come eseguire le azioni di base con Python e Archiviazione file di Azure:

* Creare condivisioni file di Azure
* Creare directory
* Enumerare file e directory in una condivisione file di Azure
* Caricare, scaricare ed eliminare un file

> [!Note]  
> Poiché Archiviazione file di Azure è accessibile tramite SMB, è possibile scrivere semplici applicazioni che accedono alla condivisione file di Azure usando le classi e le funzioni standard I/O di Python. Questo articolo descrive come scrivere applicazioni che usano Azure Storage Python SDK, che usa l'[API REST di archiviazione file Azure](https://docs.microsoft.com/en-us/rest/api/storageservices/fileservices/file-service-rest-api) per comunicare con Archiviazione file di Azure.

### <a name="set-up-your-application-to-use-azure-file-storage"></a>Configurare l'applicazione per usare Archiviazione file di Azure
Aggiungere il codice seguente vicino all'inizio del file di origine Python da cui si desidera accedere all'archiviazione di Azure a livello di codice.

```python
from azure.storage.file import FileService
```

### <a name="set-up-a-connection-to-azure-file-storage"></a>Configurare una connessione all'archiviazione file di Azure 
L'oggetto `FileService` consente di usare condivisioni, directory e file. Il codice seguente crea un oggetto `FileService` usando il nome e la chiave dell'account di archiviazione. Sostituire `<myaccount>` e `<mykey>` con il nome e la chiave dell'account.

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

### <a name="create-an-azure-file-share"></a>Creare una condivisione file di Azure
Nell'esempio di codice seguente, è possibile usare un oggetto `FileService` per creare la condivisione, se non esiste.

```python
file_service.create_share('myshare')
```

### <a name="create-a-directory"></a>Creare una directory
È inoltre possibile organizzare l'archiviazione inserendo i file all'interno di sottodirectory anziché inserirli tutti nella directory radice. L'archiviazione file di Azure permette di creare tutte le directory consentite dall'account. Il codice riportato di seguito creerà una sottodirectory denominata **sampledir** nella directory radice.

```python
file_service.create_directory('myshare', 'sampledir')
```

### <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Enumerare file e directory in una condivisione file di Azure
Per elencare i file e le directory di una condivisione, usare il metodo **list\_directories\_and\_files**. Questo metodo restituisce un generatore. Il codice seguente consente di inviare alla console il valore **name** di ogni file e directory in una condivisione.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

### <a name="upload-a-file"></a>Caricare un file 
Una condivisione file di Azure contiene almeno una directory radice in cui possono risiedere i file. In questa sezione verrà illustrato come caricare un file dall'archiviazione locale nella directory radice di una condivisione.

Per creare un file e caricare dati, usare i metodi `create_file_from_path`, `create_file_from_stream`, `create_file_from_bytes` o `create_file_from_text`. Questi sono metodi di carattere generale che eseguono il blocco dei dati necessario quando le dimensioni superano i 64 MB.

`create_file_from_path` carica i contenuti di un file dal percorso specificato, mentre `create_file_from_stream` carica i contenuti da un file/flusso già aperto. `create_file_from_bytes` carica un array di byte, mentre `create_file_from_text` carica il valore di testo specificato usando la codifica specificata (l'impostazione predefinita è UTF-8).

Nell'esempio seguente viene caricato il contenuto del file **sunset.png** nel file **myfile**.

```python
from azure.storage.file import ContentSettings
file_service.create_file_from_path(
    'myshare',
    None, # We want to create this blob in the root directory, so we specify None for the directory_name
    'myfile',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png'))
```

### <a name="download-a-file"></a>Scaricare un file
Per scaricare i dati da un file, usare `get_file_to_path`, `get_file_to_stream`, `get_file_to_bytes` o `get_file_to_text`. Questi sono metodi di carattere generale che eseguono il blocco dei dati necessario quando le dimensioni superano i 64 MB.

Nell'esempio seguente viene illustrato l'uso di `get_file_to_path` per scaricare il contenuto del file **myfile** e archiviarlo nel file **out-sunset.png**.

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

### <a name="delete-a-file"></a>Eliminare un file
Per eliminare un file, infine, chiamare `delete_file`.

```python
file_service.delete_file('myshare', None, 'myfile')
```

## <a name="next-steps"></a>Passaggi successivi
Dopo aver appreso come interagire con l'archiviazione di File di Azure con Python, seguire i collegamenti seguenti per ulteriori informazioni.

* [Centro per sviluppatori di Python](/develop/python/)
* [API REST dei servizi di archiviazione di Azure](http://msdn.microsoft.com/library/azure/dd179355)
* [Microsoft Azure Storage SDK per Python](https://github.com/Azure/azure-storage-python)
