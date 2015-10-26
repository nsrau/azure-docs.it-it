<properties
	pageTitle="Come usare l'archiviazione BLOB da Python | Microsoft Azure"
	description="Informazioni su come usare il servizio BLOB di Azure da Python per caricare, elencare, scaricare, elencare ed eliminare BLOB."
	services="storage"
	documentationCenter="python"
	authors="emgerner-msft"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="08/25/2015"
	ms.author="emgerner"/>

# Come usare l'archiviazione BLOB di Azure da Python

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Panoramica

In questo articolo verranno illustrati diversi scenari comuni di uso del servizio di archiviazione BLOB. Gli esempi sono scritti in Python e utilizzano il [pacchetto di archiviazione Python di Azure][]. Gli scenari presentati includono caricamento, visualizzazione in elenchi, download ed eliminazione di BLOB.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Creare un contenitore

> [AZURE.NOTE]Se è necessario installare Python o il [pacchetto Python di Azure][], vedere la [guida all'installazione di Python](../python-how-to-install.md).

L'oggetto **BlobService** consente di lavorare con contenitori e BLOB. Il codice seguente consente di creare un oggetto **BlobService**. Aggiungere il codice seguente vicino all'inizio del file Python da cui si desidera accedere all'archiviazione di Azure a livello di codice:

	from azure.storage.blob import BlobService

Il codice seguente consente di creare un oggetto **BlobService** utilizzando il nome dell'account di archiviazione e la chiave dell'account. Sostituire 'myaccount' e 'mykey' con l'account e la chiave reali.

	blob_service = BlobService(account_name='myaccount', account_key='mykey')

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

Nell’esempio di codice seguente, è possibile utilizzare un oggetto **BlobService** per creare il contenitore, se non esiste:

	blob_service.create_container('mycontainer')

Per impostazione predefinita, il nuovo contenitore è privato ed è necessario specificare la chiave di accesso alle risorse di archiviazione, come già fatto in precedenza, per scaricare BLOB da questo contenitore. Se si desidera che i file all'interno del contenitore siano disponibili a tutti, è possibile creare il contenitore e passare il livello di accesso pubblico usando il codice seguente:

	blob_service.create_container('mycontainer', x_ms_blob_public_access='container')

In alternativa è possibile modificare un contenitore dopo averlo creato usando il codice seguente:

	blob_service.set_container_acl('mycontainer', x_ms_blob_public_access='container')

Dopo questa modifica, i BLOB in un contenitore pubblico saranno visibili a tutti gli utenti di Internet, tuttavia solo l'utente che li ha creati sarà in grado di modificarli o eliminarli.

## Caricare un BLOB in un contenitore

Per caricare i dati in un BLOB, usare i metodi **put\_block\_blob\_from\_path**, **put\_block\_blob\_from\_file**, **put\_block\_blob\_from\_bytes** o **put\_block\_blob\_from\_text**. Questi sono metodi di carattere generale che eseguono il blocco dei dati necessario quando le dimensioni superano i 64 MB.

**put\_block\_blob\_from\_path** carica i contenuti di un file dal percorso specificato, **put\_block\_blob\_from\_file** carica i contenuti da un file/flusso già aperto. **put\_block\_blob\_from\_bytes** carica una matrice di byte e **put\_block\_blob\_from\_text** carica il valore di testo specificato usando la codifica specificata (l'impostazione predefinita è UTF-8).

Nell'esempio seguente viene caricato il contenuto del file **sunset.png** nel BLOB **myblob**.

	blob_service.put_block_blob_from_path(
        'mycontainer',
        'myblob',
        'sunset.png',
        x_ms_blob_content_type='image/png'
    )

## Elencare i BLOB in un contenitore

Per elencare i BLOB in un contenitore, usare il metodo **list\_blobs**. Ogni chiamata a **list\_blobs** restituirà un segmento di risultati. Per ottenere tutti i risultati, controllare il **next\_marker** dei risultati e chiamare **list\_blobs** nuovamente in base alle esigenze. Il codice seguente consente di inviare alla console il valore **name** di ogni BLOB in un contenitore.

	blobs = []
	marker = None
	while True:
		batch = blob_service.list_blobs('mycontainer', marker=marker)
		blobs.extend(batch)
		if not batch.next_marker:
			break
		marker = batch.next_marker
	for blob in blobs:
		print(blob.name)

## Scaricare BLOB

Ogni segmento di risultati può contenere un numero variabile di BLOB con un massimo di 5000. Se**next\_marker**esiste per un particolare segmento, possono esistere più BLOB nel contenitore.

Per scaricare i dati da un BLOB, usare **get\_blob\_to\_path**, **get\_blob\_to\_file**, **get\_blob\_to\_bytes** o **get\_blob\_to\_text**. Questi sono metodi di carattere generale che eseguono il blocco dei dati necessario quando le dimensioni superano i 64 MB.

Nell'esempio seguente viene illustrato l'uso di **get\_blob\_to\_path** per scaricare il contenuto del BLOB **myblob** e archiviarlo nel file **out-sunset.png**:

	blob_service.get_blob_to_path('mycontainer', 'myblob', 'out-sunset.png')

## Eliminare un BLOB

Per eliminare un BLOB, infine, chiamare **delete\_blob**.

	blob_service.delete_blob('mycontainer', 'myblob')

## Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base dell'archiviazione BLOB, visitare i collegamenti seguenti per altre informazioni sulle attività di archiviazione più complesse.

-   Vedere la documentazione MSDN: [Archiviazione e accesso ai dati in Azure][]
-   [Blog del team di Archiviazione di Azure][]

Per ulteriori informazioni, vedere anche il [Centro per sviluppatori di Python](/develop/python/).

[Archiviazione e accesso ai dati in Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
[Blog del team di Archiviazione di Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[pacchetto Python di Azure]: https://pypi.python.org/pypi/azure
[pacchetto di archiviazione Python di Azure]: https://pypi.python.org/pypi/azure-storage

<!---HONumber=Oct15_HO3-->