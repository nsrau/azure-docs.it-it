---
title: Come usare l&quot;archiviazione file di Azure da Python | Microsoft Docs
description: Informazioni su come usare l&quot;archiviazione file di Azure da Python per caricare, elencare, scaricare ed eliminare file.
services: storage
documentationcenter: python
author: robinsh
manager: carmonm
editor: tysonn
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 09/20/2016
ms.author: robinsh
translationtype: Human Translation
ms.sourcegitcommit: fefebeae665ccd14f15b0197241b30d33830fd09
ms.openlocfilehash: 9973da827ea5a9311904d7d6d4c22d59b5e2d0ce


---
# <a name="how-to-use-azure-file-storage-from-python"></a>Come usare l'archiviazione file di Azure da Python
[!INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../includes/storage-try-azure-tools-files.md)]

## <a name="overview"></a>Panoramica
In questo articolo verranno illustrati diversi scenari comuni di uso dell'archiviazione file. Gli esempi sono scritti in Python e usano [Microsoft Azure Storage SDK per Python]. Gli scenari presentati includono caricamento, visualizzazione dell'elenco, download ed eliminazione di file.

[!INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-share"></a>Creare una condivisione
L'oggetto **FileService** consente di usare condivisioni, directory e file. Il codice seguente consente di creare un oggetto **FileService** . Aggiungere il codice seguente vicino all'inizio del file Python da cui si desidera accedere all'archiviazione di Azure a livello di codice:

```python
from azure.storage.file import FileService
```

Il codice seguente consente di creare un oggetto **FileService** usando il nome e la chiave dell'account di archiviazione.  Sostituire 'myaccount' e 'mykey' con l'account e la chiave da usare.

```python
file_service = **FileService** (account_name='myaccount', account_key='mykey')
```

Nell'esempio di codice seguente, è possibile usare un oggetto **FileService** per creare la condivisione, se non esiste.

```python
file_service.create_share('myshare')
```

## <a name="upload-a-file-into-a-share"></a>Caricare un file in una condivisione
Una condivisione di archiviazione file di Azure contiene almeno una directory radice in cui possono risiedere i file. In questa sezione verrà illustrato come caricare un file dall'archiviazione locale nella directory radice di una condivisione.

Per creare un file e caricare i dati, usare i metodi **create\_file\_from\_path**, **create\_file\_from\_stream**, **create\_file\_from\_bytes** o **create\_file\_from\_text**. Questi sono metodi di carattere generale che eseguono il blocco dei dati necessario quando le dimensioni superano i 64 MB.

**create\_file\_from\_path** carica i contenuti di un file dal percorso specificato, mentre **create\_file\_from\_stream** carica i contenuti da un file/flusso già aperto. **create\_file\_from\_bytes** carica un array di byte, mentre **create\_file\_from\_text** carica il valore di testo specificato usando la codifica specificata (l'impostazione predefinita è UTF-8).

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

## <a name="how-to-create-a-directory"></a>Procedura: creare una directory
È inoltre possibile organizzare l'archiviazione inserendo i file all'interno di sottodirectory anziché inserirli tutti nella directory radice. Il servizio di archiviazione file di Azure permette di creare tante directory quante ne sono consentite dall'account. Il codice riportato di seguito creerà una sottodirectory denominata **sampledir** nella directory radice.

```python
file_service.create_directory('myshare', 'sampledir')
```

## <a name="how-to-list-files-and-directories-in-a-share"></a>Procedura: elencare i file e le directory in una condivisione
Per elencare i file e le directory di una condivisione, usare il metodo **list\_directories\_and\_files**. Questo metodo restituisce un generatore. Il codice seguente consente di inviare alla console il valore **name** di ogni file e directory in una condivisione.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

## <a name="download-files"></a>Download dei file
Per scaricare i dati da un file, usare **get\_file\_to\_path**, **get\_file\_to\_stream**, **get\_file\_to\_bytes** o **get\_file\_to\_text**. Questi sono metodi di carattere generale che eseguono il blocco dei dati necessario quando le dimensioni superano i 64 MB.

Nell'esempio seguente viene illustrato l'uso di **get\_file\_to\_path** per scaricare il contenuto del file **myfile** e archiviarlo nel file **out-sunset.png**.

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

## <a name="delete-a-file"></a>Eliminare un file
Per eliminare un file, infine, chiamare **delete_file**.

```python
file_service.delete_file('myshare', None, 'myfile')
```

## <a name="next-steps"></a>Passaggi successivi
A questo punto, dopo avere appreso le nozioni di base dell'archiviazione file, visitare i collegamenti seguenti per altre informazioni.

* [Centro per sviluppatori di Python](/develop/python/)
* [API REST dei servizi di archiviazione di Azure](http://msdn.microsoft.com/library/azure/dd179355)
* [Blog del team di Archiviazione di Azure]
* [Microsoft Azure Storage SDK per Python]

[Blog del team di Archiviazione di Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure Storage SDK per Python]: https://github.com/Azure/azure-storage-python



<!--HONumber=Nov16_HO3-->


