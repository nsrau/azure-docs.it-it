---
title: Come usare l'archiviazione BLOB di Azure (archiviazione degli oggetti) da Python | Microsoft Docs
description: Archiviare i dati non strutturati nel cloud con l'archivio BLOB (archivio di oggetti) di Azure.
services: storage
documentationcenter: python
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 0348e360-b24d-41fa-bb12-b8f18990d8bc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 2/24/2017
ms.author: tamram
ms.openlocfilehash: b94a0f95454f9243ef09ce37a62466bca4003a91
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/29/2017
---
# <a name="how-to-use-azure-blob-storage-from-python"></a>Come usare l'archiviazione BLOB di Azure da Python
[!INCLUDE [storage-selector-blob-include](../../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Panoramica
L'archiviazione BLOB di Azure è un servizio che archivia dati non strutturati nel cloud come oggetti/BLOB. Archivio BLOB può archiviare qualsiasi tipo di dati di testo o binari, ad esempio un documento, un file multimediale o un programma di installazione di un'applicazione. L'archivio BLOB è anche denominato archivio di oggetti.

In questo articolo verranno illustrati diversi scenari comuni di uso del servizio di archiviazione BLOB. Gli esempi sono scritti in Python e usano [Microsoft Azure Storage SDK per Python]. Gli scenari presentati includono caricamento, visualizzazione in elenchi, download ed eliminazione di BLOB.

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Scaricare e installare Azure Storage SDK per Python

Azure Storage SDK per Python richiede Python 2.7, 3.3, 3.4, 3.5 o 3.6 ed è disponibile in quattro diversi pacchetti: `azure-storage-blob`, `azure-storage-file`, `azure-storage-table` e `azure-storage-queue`. In questa esercitazione verrà usato il pacchetto `azure-storage-blob`.
 
### <a name="install-via-pypi"></a>Eseguire l'installazione tramite PyPi

Per eseguire l'installazione tramite Python Package Index (PyPI), digitare:

```bash
pip install azure-storage-blob
```


> [!NOTE]
> Se si esegue l'aggiornamento da Azure Storage SDK per Python 0.36 o versione precedente, sarà innanzitutto necessario eseguire la disinstallazione tramite `pip uninstall azure-storage` dal momento che Azure Storage SDK per Python non viene più rilasciato in un singolo pacchetto.
> 
> 

Per metodi di installazione alternativi, vedere la pagina di [Azure Storage SDK per Python su Github](https://github.com/Azure/azure-storage-python/).

## <a name="create-a-container"></a>Creare un contenitore
In base al tipo di BLOB che si vuole usare, creare un oggetto **BlockBlobService**, **AppendBlobService** o **PageBlobService**. Il codice seguente usa un oggetto **BlockBlobService** . Aggiungere il codice seguente vicino all'inizio del file Python da cui si desidera accedere all'archiviazione di BLOB in blocchi di Azure a livello di codice.

```python
from azure.storage.blob import BlockBlobService
```

Il codice seguente crea un oggetto **BlockBlobService** usando il nome e la chiave dell'account di archiviazione.  Sostituire 'myaccount' e 'mykey' con l'account e la chiave da usare.

```python
block_blob_service = BlockBlobService(account_name='myaccount', account_key='mykey')
```

[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

Nell'esempio di codice seguente è possibile usare un oggetto **BlockBlobService** per creare il contenitore, se non esiste.

```python
block_blob_service.create_container('mycontainer')
```

Per impostazione predefinita, il nuovo contenitore è privato ed è necessario specificare la chiave di accesso alle risorse di archiviazione, come già fatto in precedenza, per scaricare BLOB da questo contenitore. Se si vuole che i BLOB all'interno del contenitore siano disponibili a tutti, è possibile creare il contenitore e passare il livello di accesso pubblico usando il codice seguente.

```python
from azure.storage.blob import PublicAccess
block_blob_service.create_container('mycontainer', public_access=PublicAccess.Container)
```

In alternativa è possibile modificare un contenitore dopo averlo creato usando il codice seguente:

```python
block_blob_service.set_container_acl('mycontainer', public_access=PublicAccess.Container)
```

Dopo questa modifica, i BLOB in un contenitore pubblico saranno visibili a tutti gli utenti di Internet, tuttavia solo l'utente che li ha creati sarà in grado di modificarli o eliminarli.

## <a name="upload-a-blob-into-a-container"></a>Caricare un BLOB in un contenitore
Per creare un BLOB in blocchi e caricare i dati, usare i metodi **create\_blob\_from\_path**, **create\_blob\_from\_stream**, **create\_blob\_from\_bytes** o **create\_blob\_from\_text**. Questi sono metodi di carattere generale che eseguono il blocco dei dati necessario quando le dimensioni superano i 64 MB.

**create\_blob\_from\_path** carica i contenuti di un file dal percorso specificato, mentre **create\_blob\_from\_stream** carica i contenuti da un file/flusso già aperto. **create\_blob\_from\_bytes** carica un array di byte, mentre **create\_blob\_from\_text** carica il valore di testo specificato usando la codifica specificata (l'impostazione predefinita è UTF-8).

Nell'esempio seguente viene caricato il contenuto del file **sunset.png** nel BLOB **myblockblob**.

```python
from azure.storage.blob import ContentSettings
block_blob_service.create_blob_from_path(
    'mycontainer',
    'myblockblob',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png')
            )
```

## <a name="list-the-blobs-in-a-container"></a>Elencare i BLOB in un contenitore
Per elencare i BLOB in un contenitore, usare il metodo **list\_blobs**. Questo metodo restituisce un generatore. Il codice seguente consente di inviare alla console il valore **name** di ogni BLOB in un contenitore.

```python
generator = block_blob_service.list_blobs('mycontainer')
for blob in generator:
    print(blob.name)
```

## <a name="download-blobs"></a>Scaricare BLOB
Per scaricare i dati da un BLOB, usare **get\_blob\_to\_path**, **get\_blob\_to\_stream**, **get\_blob\_to\_bytes** o **get\_blob\_to\_text**. Questi sono metodi di carattere generale che eseguono il blocco dei dati necessario quando le dimensioni superano i 64 MB.

Nell'esempio seguente viene illustrato l'uso di **get\_blob\_to\_path** per scaricare il contenuto del BLOB **myblockblob** e archiviarlo nel file **out-sunset.png**.

```python
block_blob_service.get_blob_to_path('mycontainer', 'myblockblob', 'out-sunset.png')
```

## <a name="delete-a-blob"></a>Eliminare un BLOB
Per eliminare un BLOB, infine, chiamare **delete_blob**.

```python
block_blob_service.delete_blob('mycontainer', 'myblockblob')
```

## <a name="writing-to-an-append-blob"></a>Scrittura in un blob di Accodamento
Un blob di accodamento è ottimizzato per le operazioni di accodamento, ad esempio la registrazione. Come un blob in blocchi, un blob di accodamento è costituito da blocchi, ma quando si aggiunge un nuovo blocco in un blob di accodamento, viene aggiunto sempre alla fine del blob. È possibile aggiornare o eliminare un blocco esistente in un blob di Accodamento. L'ID di blocco per un blob di accodamento non vengono esposte come in un blob in blocchi.

Ogni blocco di un blob di accodamento può avere dimensioni diverse, con un massimo di 4 MB e un blob di accodamento può includere un massimo di 50.000 blocchi. La dimensione massima di un blob di accodamento è pertanto leggermente superiore a 195 GB (4 MB X 50.000 blocchi).

Nell'esempio seguente si crea un nuovo blob di accodamento e vi si aggiungono alcuni dati, per simulare un'operazione di registrazione semplice.

```python
from azure.storage.blob import AppendBlobService
append_blob_service = AppendBlobService(account_name='myaccount', account_key='mykey')

# The same containers can hold all types of blobs
append_blob_service.create_container('mycontainer')

# Append blobs must be created before they are appended to
append_blob_service.create_blob('mycontainer', 'myappendblob')
append_blob_service.append_blob_from_text('mycontainer', 'myappendblob', u'Hello, world!')

append_blob = append_blob_service.get_blob_to_text('mycontainer', 'myappendblob')
```

## <a name="next-steps"></a>Passaggi successivi
A questo punto, dopo avere appreso le nozioni di base dell'archivio BLOB, visitare i collegamenti seguenti per altre informazioni.

* [Centro per sviluppatori di Python](https://azure.microsoft.com/develop/python/)
* [API REST dei servizi di archiviazione di Azure](http://msdn.microsoft.com/library/azure/dd179355)
* [Blog del team di Archiviazione di Azure]
* [Microsoft Azure Storage SDK per Python]

[Blog del team di Archiviazione di Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure Storage SDK per Python]: https://github.com/Azure/azure-storage-python
