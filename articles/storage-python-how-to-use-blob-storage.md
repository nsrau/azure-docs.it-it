<properties urlDisplayName="Blob Service" pageTitle="Come usare il servizio di archiviazione BLOB (Python) | Microsoft Azure" metaKeywords="Azure blob service Python, Azure blobs Python" description="Informazioni su come usare il servizio BLOB di Azure per caricare, elencare, scaricare, elencare ed eliminare BLOB." metaCanonical="" disqusComments="1" umbracoNaviHide="0" services="storage" documentationCenter="Python" title="Come usare il servizio BLOB da Python" authors="huvalo" videoId="" scriptId="" manager="wpickett" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="09/19/2014" ms.author="huvalo" />

# Uso del servizio di archiviazione BLOB da Python

In questa guida verranno illustrati diversi scenari comuni di utilizzo del servizio di
archiviazione BLOB di Azure. Gli esempi sono scritti usando l'API
Python. Gli scenari presentati includono **caricamento**, **visualizzazione dell'elenco**,
**download** ed
**eliminazione** di BLOB. Per altre informazioni sui BLOB,
vedere la sezione [Passaggi successivi][Passaggi successivi].

## Sommario

[Informazioni sull'archiviazione BLOB][Informazioni sull'archiviazione BLOB]
 [Concetti][Concetti]
 [Creare un account di archiviazione di Azure][Creare un account di archiviazione di Azure]
 [Procedura: Creare un contenitore][Procedura: Creare un contenitore]
 [Procedura: Caricare un BLOB in un contenitore][Procedura: Caricare un BLOB in un contenitore]
 [Procedura: Visualizzare un elenco di BLOB in un contenitore][Procedura: Visualizzare un elenco di BLOB in un contenitore]
 [Procedura: Scaricare BLOB][Procedura: Scaricare BLOB]
 [Procedura: Eliminare un BLOB][Procedura: Eliminare un BLOB]
 [Procedura: Caricare e scaricare BLOB di grandi dimensioni][Procedura: Caricare e scaricare BLOB di grandi dimensioni]
 [Passaggi successivi][Passaggi successivi]

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

## <a name="create-account"> </a>Creare un account di archiviazione di Azure

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="create-container"> </a>Procedura: Creare un contenitore

**Nota:** se è necessario installare Python o le librerie client, vedere la [guida all'installazione di Python][guida all'installazione di Python].

L'oggetto **BlobService** consente di lavorare con contenitori e BLOB. Il codice
seguente consente di creare un oggetto **BlobService**. Aggiungere il codice seguente vicino
all'inizio del file Python da cui si vuole accedere all'archiviazione di Azure a livello di codice:

    from azure.storage import BlobService

Il codice seguente consente di creare un oggetto **BlobService** usando il nome dell'account di archiviazione e la chiave dell'account. Sostituire 'myaccount' e 'mykey' con l'account e la chiave reali.

    blob_service = BlobService(account_name='myaccount', account_key='mykey')

Tutti i BLOB di archiviazione risiedono in un contenitore. È possibile usare un oggetto **BlobService** per creare il contenitore, se non esiste:

    blob_service.create_container('mycontainer')

Per impostazione predefinita, il nuovo contenitore è privato ed è necessario specificare la chiave di accesso alle risorse di archiviazione, come già fatto in precedenza, per scaricare BLOB da questo contenitore. Se si desidera che i file all'interno del contenitore siano disponibili a tutti, è possibile creare il contenitore e passare il livello di accesso pubblico usando il codice seguente:

    blob_service.create_container('mycontainer', x_ms_blob_public_access='container') 

In alternativa è possibile modificare un contenitore dopo averlo creato usando il codice seguente:

    blob_service.set_container_acl('mycontainer', x_ms_blob_public_access='container')

Dopo questa modifica, i BLOB in un contenitore
pubblico saranno visibili a tutti gli utenti di Internet, tuttavia solo l'utente che li ha creati sarà in grado di modificarli o eliminarli.

## <a name="upload-blob"> </a>Procedura: Caricare un BLOB in un contenitore

Per caricare i dati in un BLOB, usare i metodi **put\_block\_blob\_from\_path**, **put\_block\_blob\_from\_file**, **put\_block\_blob\_from\_bytes** o **put\_block\_blob\_from\_text**. Questi sono metodi di carattere generale che eseguono il blocco dei dati necessario quando le dimensioni superano i 64 MB.

**put\_block\_blob\_from\_path** carica i contenuti di un file dal percorso specificato, **put\_block\_blob\_from\_file** carica i contenuti da un file/flusso già aperto. **put\_block\_blob\_from\_bytes** carica una matrice di byte e **put\_block\_blob\_from\_text** carica il valore di testo specificato usando la codifica specificata (l'impostazione predefinita è UTF-8).

Nell'esempio seguente viene caricato il contenuto del file **task1.txt** nel BLOB **myblob**.

    blob_service.put_block_blob_from_path('mycontainer', 'myblob', 'task1.txt')

## <a name="list-blob"> </a>Procedura: Elencare i BLOB in un contenitore

Per elencare i BLOB in un contenitore, usare il metodo **list\_blobs** con un ciclo
**for** per visualizzare il nome di ogni BLOB nel contenitore. Il
codice seguente consente di inviare alla console il valore di **name** e **url** di ogni BLOB in un
contenitore.

    blobs = blob_service.list_blobs('mycontainer')
    for blob in blobs:
        print(blob.name)
        print(blob.url)

## <a name="download-blobs"> </a>Procedura: Scaricare BLOB

Per scaricare i dati da un BLOB, usare **get\_blob\_to\_path**, **get\_blob\_to\_file**, **get\_blob\_to\_bytes** o **get\_blob\_to\_text**. Questi sono metodi di carattere generale che eseguono il blocco dei dati necessario quando le dimensioni superano i 64 MB.

Nell'esempio seguente viene illustrato l'utilizzo di **get\_blob\_to\_path** per scaricare il contenuto del BLOB **myblob** e archiviarlo nel file **out-task1.txt**.

    blob_service.get_blob_to_path('mycontainer', 'myblob', 'out-task1.txt')

## <a name="delete-blobs"> </a>Procedura: Eliminare un BLOB

Per eliminare un BLOB, infine, chiamare **delete\_blob**.

    blob_service.delete_blob('mycontainer', 'myblob') 

## <a name="next-steps"> </a>Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base dell'archiviazione BLOB, visitare i collegamenti
seguenti per altre informazioni sulle attività di archiviazione più complesse.

-   Riferimento in MSDN: [Archiviazione e accesso ai dati in Azure][Archiviazione e accesso ai dati in Azure]
-   [Blog del team di Archiviazione di Azure][Blog del team di Archiviazione di Azure]

  [Passaggi successivi]: #next-steps
  [Informazioni sull'archiviazione BLOB]: #what-is
  [Concetti]: #concepts
  [Creare un account di archiviazione di Azure]: #create-account
  [Procedura: Creare un contenitore]: #create-container
  [Procedura: Caricare un BLOB in un contenitore]: #upload-blob
  [Procedura: Visualizzare un elenco di BLOB in un contenitore]: #list-blob
  [Procedura: Scaricare BLOB]: #download-blobs
  [Procedura: Eliminare un BLOB]: #delete-blobs
  [Procedura: Caricare e scaricare BLOB di grandi dimensioni]: #large-blobs
  [guida all'installazione di Python]: ../python-how-to-install/
  [Archiviazione e accesso ai dati in Azure]: http://msdn.microsoft.com/it-it/library/windowsazure/gg433040.aspx
  [Blog del team di Archiviazione di Azure]: http://blogs.msdn.com/b/windowsazurestorage/
