<properties 
	pageTitle="Spostamento dei dati da e verso  Archiviazione BLOB di Azure" 
	description="Spostamento dei dati da e verso  Archiviazione BLOB di Azure" 
	services="machine-learning" 
	documentationCenter="" 
	authors="sunliangms,sachouks" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2015" 
	ms.author="sunliangms,sachouks" />

# Spostamento dei dati da e verso l'archiviazione BLOB di Azure

L'articolo [Scenari di esempio dell'analisi scientifica dei dati cloud](http://azure.microsoft.com/documentation/articles/machine-learning-data-science-plan-sample-scenarios) consente di determinare le risorse necessarie per un'ampia gamma di flussi di lavoro di analisi scientifica dei dati. Per spostare i dati da e verso l'archiviazione BLOB di Azure in base al proprio scenario, usare uno dei metodi seguenti:

- [Utilizzo di Esplora archivi Azure](#explorer)
- [Utilizzo dell'utilità della riga di comando AzCopy](#AzCopy)
- [Utilizzo di Azure SDK in Python](#PythonSDK)



> [AZURE.NOTE] Per una completa introduzione dell'archiviazione BLOB di Azure, fare riferimento a [Informazioni di base sui BLOB di Azure](storage-dotnet-how-to-use-blobs.md) e  [Servizio BLOB di Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx). 

Prima di caricare/scaricare i dati, è necessario conoscere il nome e la chiave del proprio account di archiviazione di Azure. Per istruzioni su come ottenere queste informazioni, vedere la sezione "Procedura: visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione" di [Gestione degli account di archiviazione](storage-create-storage-account.md). In questo documento si presuppone di avere un account di archiviazione di Azure e delle chiavi di archiviazione corrispondenti.

<a id="explorer"></a>
## Utilizzo di Esplora archivi Azure 

Esplora archivi Azure è uno strumento basato su Windows gratuito per analizzare e modificare i dati in un account di archiviazione di Azure. Può essere scaricato da [Esplora archivi Azure](http://azurestorageexplorer.codeplex.com/). I passaggi seguenti documentano come caricare e scaricare i dati utilizzando Esplora archivi Azure. 

1.  Avviare Esplora archivi Azure 
2.  Se l'account al quale si desidera accedere non è stato aggiunto a Esplora archivi Azure, fare clic sul pulsante "Aggiungi account" per aggiungere l'account. Se è già stato aggiunto, selezionare l'account nell'elenco a discesa "--Selezionare un account di archiviazione--".  
![Create workspace][1]
<br>
3. Immettere il nome e la chiave dell'account di archiviazione, quindi fare clic su Aggiungi account di archiviazione. È possibile aggiungere più account di archiviazione, ognuno dei quali verrà visualizzato in una scheda. I contenitori in questo account di archiviazione vengono visualizzati nel riquadro sinistro. Selezionare un contenitore per visualizzare i BLOB nel contenitore nel riquadro di destra.  
![Create workspace][2]
<br>
![Create workspace][3]
<br>
4. Caricare i dati facendo clic sul pulsante "Carica". Selezionare uno o più file da caricare dal file system e scegliere "Apri" per iniziare a caricare i file.
5. Scaricare i dati selezionando il BLOB di archiviazione nel contenitore corrispondente e facendo clic sul pulsante "Scarica".

<a id="AzCopy"></a>
## Usare AzCopy

AzCopy è un'utilità della riga di comando per caricare e scaricare i dati. 

**Avviso** Se si utilizza un computer diverso da una macchina virtuale che è stata impostata in precedenza nel processo di analisi scientifica dei dati cloud, installare AzCopy seguendo le istruzioni di installazione seguenti: [Scaricare e installare AzCopy](storage-use-azcopy.md#install).

####Esempi di caricamento/download dei file in/da BLOB:

	# Uploading from local file system
	AzCopy /Source:<your_local_directory> /Dest: https://<your_account_name>.blob.core.windows.net/<your_container_name> /DestKey:<your_account_key> /S 

	# Downloading blobs to local file system
	AzCopy /Source:https://<your_account_name>.blob.core.windows.net/<your_container_name>/<your_sub_directory_at_blob>  /Dest:<your_local_directory> /SourceKey:<your_account_key> /Pattern:<file_pattern> /S

	# Transferring blobs between Azure containers
	AzCopy /Source:https://<your_account_name1>.blob.core.windows.net/<your_container_name1>/<your_sub_directory_at_blob1> /Dest:https://<your_account_name2>.blob.core.windows.net/<your_container_name2>/<your_sub_directory_at_blob2> /SourceKey:<your_account_key1> /DestKey:<your_account_key2> /Pattern:<file_pattern> /S
	
	<your_account_name>: your storage account name
	<your_account_key>: your storage account key
	<your_container_name>: your container name
	<your_sub_directory_at_blob>: the sub directory in the container 
	<your_local_directory>: directory of local file system where files to be uploaded from or the directory of local file system files to be downloaded to
	<file_pattern>: pattern of file names to be transferred. The standard wildcards are supported

> [AZURE.TIP]   
> 1. Quando si caricano file, /S caricherà i file in modo ricorsivo. Senza questo parametro, tutti i file nella sottodirectory non verranno caricati.  
> 2. Durante il download di file, /S cercherà il contenitore in modo ricorsivo fino a quando non vengono scaricati tutti i file nella directory specificata e nelle relative sottodirectory o tutti i file corrispondenti al criterio specificato nella directory data e nelle relative sottodirectory.  
> 3.  Non è possibile specificare un file BLOB specifico da scaricare utilizzando il parametro /Source. Per scaricare un file specifico, specificare il nome del file BLOB per il download utilizzando il parametro /Pattern. Il parametro /S può essere utilizzato per fare in modo che AzCopy cerchi il nome del file in modo ricorsivo. Senza il parametro pattern, AzCopy scaricherà tutti i file nella directory. 

Per informazioni dettagliate sull'uso di AzCopy, vedere [Introduzione all'utilità della riga di comando AzCopy](storage-use-azcopy.md#install).


<a id="PythonSDK"></a>
## Utilizzo di Python

Con l'API Python fornita in Azure SDK, è possibile

- Creare un contenitore
- Caricare un BLOB in un contenitore
- Scaricare BLOB
- Elencare i BLOB in un contenitore
- Eliminare un BLOB

Questa sezione viene illustrato come elencare, caricare e scaricare BLOB. Per ulteriori informazioni sull'utilizzo dell'API Python, consultare [Come utilizzare il servizio di archiviazione BLOB di Python](storage-python-how-to-use-blob-storage.md). 

> [AZURE.NOTE] Se si utilizza un computer diverso da una macchina virtuale che è stata impostata in precedenza nel processo di analisi scientifica dei dati cloud, è necessario installare [Azure SDK per Python](python-how-to-install.md) prima di utilizzare il codice di esempio seguente.

###Caricamento di dati BLOB
Aggiungere lo snippet seguente vicino all'inizio di ogni codice Python in cui si desidera accedere all'archiviazione di Azure a livello di programmazione:

	from azure.storage import BlobService

L'oggetto **BlobService** permette di usare contenitori e BLOB. Il codice seguente crea un oggetto BlobService usando il nome e la chiave dell'account di archiviazione. Sostituire nome e chiave dell'account con la chiave e l'account effettivi.
	
	blob_service = BlobService(account_name="<your_account_name>", account_key="<your_account_key>")

Per caricare dati in un BLOB, utilizzare i metodi seguenti:
 
1. put_block_blob_from_path (consente di caricare il contenuto di un file da un percorso specifico)
2. put_block_blob_from_file (consente di caricare il contenuto da un flusso/file già aperto)
3. put_block_blob_from_bytes (consente di caricare una matrice di byte)
4. put_block_blob_from_text (consente di caricare il valore di testo specificato mediante la codifica specificata)
 
Il seguente codice di esempio consente di caricare un file locale in un contenitore:
	
	blob_service.put_block_blob_from_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

Il seguente codice di esempio consente di caricare tutti i file (a esclusione delle directory) in una directory locale in un'archiviazione BLOB:

	from azure.storage import BlobService
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

###Download dei dati dal BLOB

Per scaricare dati da un BLOB, utilizzare i metodi seguenti:
1. get_blob_to_path
2. get_blob_to_file
3. get_blob_to_bytes
4. get_blob_to_text 

Questi sono metodi che eseguono il blocco dei dati necessario quando le dimensioni superano i 64 MB. 

Il seguente codice di esempio consente di scaricare i contenuti di un BLOB in un contenitore in un file locale: 

	blob_service.get_blob_to_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

Il seguente codice di esempio consente di scaricare tutti i BLOB da un contenitore. Utilizza list_blobs per ottenere un elenco di BLOB disponibili nel contenitore e li scarica in una directory locale. 

	from azure.storage import BlobService
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

<!-- Images -->

[1]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img1.png
[2]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img2.png
[3]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img3.png

<!--HONumber=49-->