<properties linkid="develop-python-blob-service" urlDisplayName="Blob Service" pageTitle="How to use blob storage (Python) | Microsoft Azure" metaKeywords="Azure blob service Python, Azure blobs Python" description="Learn how to use the Azure Blob service to upload, list, download, and delete blobs." metaCanonical="" disqusComments="1" umbracoNaviHide="0" services="storage" documentationCenter="Python" title="How to use the Blob service from Python" authors="" videoId="" scriptId="" />

Utilizzo del servizio di archiviazione BLOB da Python
=====================================================

In questa guida verranno illustrati diversi scenari comuni di utilizzo del servizio di archiviazione BLOB di Azure. Gli esempi sono scritti utilizzando l'API Python. Gli scenari presentati includono **caricamento**, **visualizzazione dell'elenco**, **download** ed **eliminazione** di BLOB. Per ulteriori informazioni sui BLOB, vedere la sezione [Passaggi successivi](#next-steps).

Sommario
--------

[Informazioni sull'archiviazione BLOB](#what-is)
 [Concetti](#concepts)
 [Creazione di un account di archiviazione di Azure](#create-account)
 [Procedura: Creare un contenitore](#create-container)
 [Procedura: Caricare un BLOB in un archivio](#upload-blob)
 [Procedura: Visualizzare un elenco dei BLOB in un contenitore](#list-blob)
 [Procedura: Scaricare BLOB](#download-blobs)
 [Procedura: Eliminare un BLOB](#delete-blobs)
 [Procedura: Caricare e scaricare BLOB di grandi dimensioni](#large-blobs)
 [Passaggi successivi](#next-steps)

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

Creazione di un account di archiviazione di Azure
-------------------------------------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

Procedura: Creare un contenitore
--------------------------------

**Nota:** se è necessario installare Python o le librerie client, vedere la [guida all'installazione di Python](../python-how-to-install/).

L'oggetto **BlobService** consente di lavorare con contenitori e BLOB. Il codice seguente consente di creare un oggetto **BlobService**. Aggiungere il codice seguente vicino all'inizio del file Python da cui si desidera accedere all'archiviazione di Azure a livello di codice:

    from azure.storage import *

Il codice seguente consente di creare un oggetto **BlobService** utilizzando il nome dell'account di archiviazione e la chiave dell'account. Sostituire 'myaccount' e 'mykey' con l'account e la chiave reali.

    blob_service = BlobService(account_name='myaccount', account_key='mykey')

Tutti i BLOB di archiviazione risiedono in un contenitore. È possibile utilizzare un oggetto **BlobService** per creare il contenitore, se non esiste:

    blob_service.create_container('mycontainer')

Per impostazione predefinita, il nuovo contenitore è privato ed è necessario specificare la chiave di accesso alle risorse di archiviazione, come già fatto in precedenza, per scaricare BLOB da questo contenitore. Se si desidera che i file all'interno del contenitore siano disponibili a tutti, è possibile creare il contenitore e passare il livello di accesso pubblico utilizzando il codice seguente:

    blob_service.create_container('mycontainer', x_ms_blob_public_access='container') 

In alternativa è possibile modificare un contenitore dopo averlo creato utilizzando il codice seguente:

    blob_service.set_container_acl('mycontainer', x_ms_blob_public_access='container')

Dopo questa modifica, i BLOB in un contenitore pubblico saranno visibili a tutti gli utenti di Internet, tuttavia solo l'utente che li ha creati sarà in grado di modificarli o eliminarli.

Procedura: Caricare un BLOB in un contenitore
---------------------------------------------

Per caricare un file in un BLOB, utilizzare il metodo **put\_blob** per crearne uno, utilizzando un flusso di file come contenuto del BLOB. Creare innanzitutto un file denominato **task1.txt** (è possibile utilizzare qualsiasi contenuto) e archiviarlo nella stessa directory in cui si trova il file Python.

    myblob = open(r'task1.txt', 'r').read()
    blob_service.put_blob('mycontainer', 'myblob', myblob, x_ms_blob_type='BlockBlob')

Procedura: Elencare i BLOB in un contenitore
--------------------------------------------

Per elencare i BLOB in un contenitore, utilizzare il metodo **list\_blobs** con un ciclo **for** per visualizzare il nome di ogni BLOB nel contenitore. Il codice seguente consente di inviare alla console il valore di **name** e di **url** di ogni BLOB in un contenitore.

    blobs = blob_service.list_blobs('mycontainer')
    for blob in blobs:
        print(blob.name)
        print(blob.url)

Procedura: Scaricare BLOB
-------------------------

Per scaricare BLOB, utilizzare il metodo **get\_blob** per trasferire il contenuto del BLOB a un oggetto Stream che è quindi possibile rendere persistente in un file locale.

    blob = blob_service.get_blob('mycontainer', 'myblob')
    with open(r'out-task1.txt', 'w') as f:
        f.write(blob)

Procedura: Eliminare un BLOB
----------------------------

Per eliminare un BLOB, infine, chiamare **delete\_blob**.

    blob_service.delete_blob('mycontainer', 'myblob') 

Procedura: Caricare e scaricare BLOB di grandi dimensioni
---------------------------------------------------------

Le dimensioni massime per un BOLB in blocchi sono pari a 200 GB. Per BLOB di dimensioni inferiori a 64 MB, è possibile effettuare il caricamento o il download è sufficiente utilizzare una chiamata a **put\_blob** o a **get\_blob**, come illustrato in precedenza. Per BLOB di dimensioni superiori a 64 MB è necessario effettuare il caricamento o il download in blocchi di 4 MB o di dimensioni inferiori.

Nel codice seguente vengono riportati esempi di funzioni per caricare o scaricare BLOB in blocchi di ogni dimensione.

    import base64

    chunk_size = 4 * 1024 * 1024

    def upload(blob_service, container_name, blob_name, file_path):
        blob_service.create_container(container_name, None, None, False)
        blob_service.put_blob(container_name, blob_name, '', 'BlockBlob')

        block_ids = []
        index = 0
        with open(file_path, 'rb') as f:
            while True:
                data = f.read(chunk_size)
                if data:
                    length = len(data)
                    block_id = base64.b64encode(str(index))
                    blob_service.put_block(container_name, blob_name, data, block_id)
                    block_ids.append(block_id)
                    index += 1
                else:
                    break

        blob_service.put_block_list(container_name, blob_name, block_ids)

    def download(blob_service, container_name, blob_name, file_path):
        props = blob_service.get_blob_properties(container_name, blob_name)
        blob_size = int(props['content-length'])

        index = 0
        with open(file_path, 'wb') as f:
            while index < blob_size:
                chunk_range = 'bytes={}-{}'.format(index, index + chunk_size - 1)
                data = blob_service.get_blob(container_name, blob_name, x_ms_range=chunk_range)
                length = len(data)
                index += length
                if length > 0:
                    f.write(data)
                    if length < chunk_size:
                        break
                else:
                    break

Se sono necessari BLOB di dimensioni superiori a 200 GB, è possibile utilizzare un BLOB di pagine anziché un BLOB in blocchi. La dimensione massima di un BLOB di pagine è 1 TB, con pagine allineate a limiti di pagina di 512 byte. Utilizzare **put\_blob** per creare un BLOB di pagine, **put\_page** per scrivere nel BLOB e **get\_blob** per leggere da esso.

Passaggi successivi
-------------------

A questo punto, dopo aver appreso le nozioni di base dell'archiviazione BLOB, visitare i collegamenti seguenti per ulteriori informazioni sulle attività di archiviazione più complesse.

-   Riferimento in MSDN: [Archiviazione e accesso ai dati in Azure](http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx)
-   [Blog del team di Archiviazione di Azure](http://blogs.msdn.com/b/windowsazurestorage/)

