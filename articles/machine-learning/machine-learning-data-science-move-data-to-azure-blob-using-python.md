<properties
	pageTitle="Spostamento dei dati da e verso l'archiviazione BLOB di Azure utilizzando Python | Microsoft Azure"
	description="Spostamento dei dati da e verso l'archiviazione BLOB di Azure utilizzando Python."
	services="machine-learning,storage"
	documentationCenter=""
	authors="bradsev"
	manager="jhubbard"
	editor="cgronlun" /> 

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/14/2016"
	ms.author="bradsev" /> 

# Spostamento dei dati da e verso l'archiviazione BLOB di Azure utilizzando Python.

Questo argomento descrive come elencare, caricare e scaricare BLOB usando l'API Python. Con l'API Python fornita in Azure SDK, è possibile:

- Creare un contenitore
- Caricare un BLOB in un contenitore
- Scaricare BLOB
- Elencare i BLOB in un contenitore
- Eliminare un BLOB

Per ulteriori informazioni sull'utilizzo dell’API di Python, vedere [Come utilizzare il servizio di archiviazione BLOB da Python](../storage/storage-python-how-to-use-blob-storage.md).

Collegamenti a indicazioni sulle tecnologie usate per spostare i dati in e/o da un archivio BLOB di Azure sono disponibili qui:

[AZURE.INCLUDE [selettore dello strumento di archiviazione BLOB](../../includes/machine-learning-blob-storage-tool-selector.md)]


> [AZURE.NOTE] Se si utilizza una macchina virtuale che è stata impostata con gli script forniti da [Macchine virtuali della scienza dei dati in Azure](machine-learning-data-science-virtual-machines.md), allora AzCopy è già installata nella macchina virtuale.

> [AZURE.NOTE] Per un'introduzione completa dell'archiviazione BLOB di Azure, vedere [Informazioni di base sui BLOB di Azure](../storage/storage-dotnet-how-to-use-blobs.md) e [Servizio BLOB di Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).


## Prerequisiti

In questo documento si presuppone di avere una sottoscrizione di Azure, un account di archiviazione e chiavi di archiviazione corrispondenti per quell'account. Prima di caricare/scaricare i dati, è necessario conoscere il nome e la chiave del proprio account di archiviazione di Azure.

- Per configurare una sottoscrizione di Azure, vedere [Versione di prova gratuita di un mese](https://azure.microsoft.com/pricing/free-trial/).

- Per istruzioni sulla creazione di un account di archiviazione e per ottenere informazioni sull’account e la chiave, vedere [Informazioni sugli account di archiviazione di Azure](../storage/storage-create-storage-account.md).


## Caricamento di dati BLOB

Aggiungere lo snippet seguente vicino all'inizio di ogni codice Python in cui si desidera accedere all'archiviazione di Azure a livello di programmazione:

	from azure.storage.blob import BlobService

L'oggetto **BlobService** consente di lavorare con contenitori e BLOB. Il codice seguente crea un oggetto BlobService usando il nome e la chiave dell'account di archiviazione. Sostituire nome e chiave dell'account con la chiave e l'account effettivi.

	blob_service = BlobService(account_name="<your_account_name>", account_key="<your_account_key>")

Per caricare dati in un BLOB, utilizzare i metodi seguenti:

1. put\_block\_blob\_from\_path (consente di caricare il contenuto di un file da un percorso specifico)
2. put\_block\_blob\_from\_file (consente di caricare il contenuto da un flusso/file già aperto)
3. put\_block\_blob\_from\_bytes (consente di caricare una matrice di byte)
4. put\_block\_blob\_from\_text (consente di caricare il valore di testo specificato mediante la codifica specificata)

Il seguente codice di esempio consente di caricare un file locale in un contenitore:

	blob_service.put_block_blob_from_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

Il seguente codice di esempio consente di caricare tutti i file (a esclusione delle directory) in una directory locale in un'archiviazione BLOB:

	from azure.storage.blob import BlobService
	from os import listdir
	from os.path import isfile, join

	# Set parameters here
	ACCOUNT_NAME = "<your_account_name>"
	ACCOUNT_KEY = "<your_account_key>"
	CONTAINER_NAME = "<your_container_name>"
	LOCAL_DIRECT = "<your_local_directory>"		

	blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)
	# find all files in the LOCAL_DIRECT (excluding directory)
	local_file_list = [f for f in listdir(LOCAL_DIRECT) if isfile(join(LOCAL_DIRECT, f))]

	file_num = len(local_file_list)
	for i in range(file_num):
	    local_file = join(LOCAL_DIRECT, local_file_list[i])
	    blob_name = local_file_list[i]
	    try:
	        blob_service.put_block_blob_from_path(CONTAINER_NAME, blob_name, local_file)
	    except:
	        print "something wrong happened when uploading the data %s"%blob_name


## Download dei dati dal BLOB

Per scaricare dati da un BLOB, utilizzare i metodi seguenti:
1. get\_blob\_to\_path
2. get\_blob\_to\_file
3. get\_blob\_to\_bytes
4. get\_blob\_to\_text

Questi sono metodi che eseguono il blocco dei dati necessario quando le dimensioni superano i 64 MB.

Il seguente codice di esempio consente di scaricare i contenuti di un BLOB in un contenitore in un file locale:

	blob_service.get_blob_to_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

Il seguente codice di esempio consente di scaricare tutti i BLOB da un contenitore. Utilizza list\_blobs per ottenere un elenco di BLOB disponibili nel contenitore e li scarica in una directory locale.

	from azure.storage.blob import BlobService
	from os.path import join

	# Set parameters here
	ACCOUNT_NAME = "<your_account_name>"
	ACCOUNT_KEY = "<your_account_key>"
	CONTAINER_NAME = "<your_container_name>"
	LOCAL_DIRECT = "<your_local_directory>"		

	blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

	# List all blobs and download them one by one
	blobs = blob_service.list_blobs(CONTAINER_NAME)
	for blob in blobs:
	    local_file = join(LOCAL_DIRECT, blob.name)
	    try:
	        blob_service.get_blob_to_path(CONTAINER_NAME, blob.name, local_file)
	    except:
	        print "something wrong happened when downloading the data %s"%blob.name

<!---HONumber=AcomDC_0921_2016-->