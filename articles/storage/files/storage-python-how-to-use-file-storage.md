---
title: Sviluppare per File di Azure con Python | Microsoft Docs
description: Informazioni su come sviluppare applicazioni e servizi Python che usano File di Azure per archiviare i dati dei file.
services: storage
documentationcenter: python
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 09/19/2017
ms.author: tamram
ms.openlocfilehash: cee6ece907950724f6ad4a86c489a5f07dfcaaec
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
# <a name="develop-for-azure-files-with-python"></a>Sviluppare per File di Azure con Python
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

Questa esercitazione illustra le nozioni base per usare Python per sviluppare applicazioni o servizi che usano File di Azure per archiviare i dati dei file. In questa esercitazione verrà creata una semplice applicazione console e verrà illustrato come eseguire le azioni di base con Python e File di Azure:

* Creare condivisioni file di Azure
* Creare directory
* Enumerare file e directory in una condivisione file di Azure
* Caricare, scaricare ed eliminare un file

> [!Note]  
> Poiché è possibile accedere a File di Azure tramite SMB, è possibile scrivere semplici applicazioni che accedono alla condivisione di File di Azure usando le classi e le funzioni standard I/O di Python. L'articolo illustra come scrivere applicazioni che usano l'SDK Python di Archiviazione di Azure, che usa l'[API REST di File di Azure](https://docs.microsoft.com/rest/api/storageservices/fileservices/file-service-rest-api) per comunicare con File di Azure.

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Scaricare e installare Azure Storage SDK per Python

Azure Storage SDK per Python richiede Python 2.7, 3.3, 3.4, 3.5 o 3.6 ed è disponibile in quattro diversi pacchetti: `azure-storage-blob`, `azure-storage-file`, `azure-storage-table` e `azure-storage-queue`. In questa esercitazione verrà usato il pacchetto `azure-storage-file`.
 
## <a name="install-via-pypi"></a>Eseguire l'installazione tramite PyPi

Per eseguire l'installazione tramite Python Package Index (PyPI), digitare:

```bash
pip install azure-storage-file
```


> [!NOTE]
> Se si esegue l'aggiornamento da Azure Storage SDK per Python 0.36 o versione precedente, sarà innanzitutto necessario eseguire la disinstallazione tramite `pip uninstall azure-storage` dal momento che Azure Storage SDK per Python non viene più rilasciato in un singolo pacchetto.
> 
> 

Per metodi di installazione alternativi, vedere la pagina di [Azure Storage SDK per Python su Github](https://github.com/Azure/azure-storage-python/).

## <a name="set-up-your-application-to-use-azure-files"></a>Configurare l'applicazione per usare File di Azure
Aggiungere il codice seguente vicino all'inizio del file di origine Python da cui si desidera accedere all'archiviazione di Azure a livello di codice.

```python
from azure.storage.file import FileService
```

## <a name="set-up-a-connection-to-azure-files"></a>Configurare una connessione a File di Azure 
L'oggetto `FileService` consente di usare condivisioni, directory e file. Il codice seguente crea un oggetto `FileService` usando il nome e la chiave dell'account di archiviazione. Sostituire `<myaccount>` e `<mykey>` con il nome e la chiave dell'account.

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

## <a name="create-an-azure-file-share"></a>Creare una condivisione file di Azure
Nell'esempio di codice seguente, è possibile usare un oggetto `FileService` per creare la condivisione, se non esiste.

```python
file_service.create_share('myshare')
```

## <a name="create-a-directory"></a>Creare una directory
È inoltre possibile organizzare l'archiviazione inserendo i file all'interno di sottodirectory anziché inserirli tutti nella directory radice. In File di Azure è possibile creare tutte le directory consentite dall'account. Il codice riportato di seguito creerà una sottodirectory denominata **sampledir** nella directory radice.

```python
file_service.create_directory('myshare', 'sampledir')
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Enumerare file e directory in una condivisione file di Azure
Per elencare i file e le directory di una condivisione, usare il metodo **list\_directories\_and\_files**. Questo metodo restituisce un generatore. Il codice seguente consente di inviare alla console il valore **name** di ogni file e directory in una condivisione.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

## <a name="upload-a-file"></a>Caricare un file 
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

## <a name="download-a-file"></a>Scaricare un file
Per scaricare i dati da un file, usare `get_file_to_path`, `get_file_to_stream`, `get_file_to_bytes` o `get_file_to_text`. Questi sono metodi di carattere generale che eseguono il blocco dei dati necessario quando le dimensioni superano i 64 MB.

Nell'esempio seguente viene illustrato l'uso di `get_file_to_path` per scaricare il contenuto del file **myfile** e archiviarlo nel file **out-sunset.png**.

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

## <a name="delete-a-file"></a>Eliminare un file
Per eliminare un file, infine, chiamare `delete_file`.

```python
file_service.delete_file('myshare', None, 'myfile')
```

## <a name="create-share-snapshot-preview"></a>Creare uno snapshot di condivisione (anteprima)
È possibile creare una copia temporizzata dell'intera condivisione file.

```python
snapshot = file_service.snapshot_share(share_name)
snapshot_id = snapshot.snapshot
```

**Creare uno snapshot di condivisione con metadati**

```python
metadata = {"foo": "bar"}
snapshot = file_service.snapshot_share(share_name, metadata=metadata)
```

## <a name="list-shares-and-snapshots"></a>Elencare condivisioni e snapshot 
È possibile elencare tutti gli snapshot per una particolare condivisione.

```python
shares = list(file_service.list_shares(include_snapshots=True))
```

## <a name="browse-share-snapshot"></a>Esplorare uno snapshot di condivisione
È possibile esplorare il contenuto di ogni snapshot di condivisione per recuperare file e directory da un determinato momento.

```python
directories_and_files = list(file_service.list_directories_and_files(share_name, snapshot=snapshot_id))
```

## <a name="get-file-from-share-snapshot"></a>Ottenere file da uno snapshot di condivisione
È possibile scaricare un file da uno snapshot di condivisione per lo scenario di ripristino.

```python
with open(FILE_PATH, 'wb') as stream:
    file = file_service.get_file_to_stream(share_name, directory_name, file_name, stream, snapshot=snapshot_id)
```

## <a name="delete-a-single-share-snapshot"></a>Eliminare un singolo snapshot di condivisione  
È possibile eliminare un singolo snapshot di condivisione.

```python
file_service.delete_share(share_name, snapshot=snapshot_id)
```

## <a name="delete-share-when-share-snapshots-exist"></a>Eliminare una condivisione in presenza di snapshot di condivisione
Una condivisione contenente snapshot non può essere eliminata se prima non vengono eliminati tutti gli snapshot.

```python
file_service.delete_share(share_name, delete_snapshots=DeleteSnapshot.Include)
```

## <a name="next-steps"></a>Passaggi successivi
Dopo aver appreso come interagire con File di Azure con Python, selezionare i collegamenti seguenti per altre informazioni.

* [Centro per sviluppatori Python](/develop/python/)
* [API REST dei servizi di archiviazione di Azure](http://msdn.microsoft.com/library/azure/dd179355)
* [Microsoft Azure Storage SDK per Python](https://github.com/Azure/azure-storage-python)