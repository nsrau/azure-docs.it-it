<properties
	pageTitle="Spostamento dei dati da e verso l'archiviazione BLOB di Azure utilizzando AzCopy | Microsoft Azure"
	description="Spostamento dei dati da e verso l'archiviazione BLOB di Azure utilizzando AzCopy."
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

# Spostamento dei dati da e verso l'archiviazione BLOB di Azure utilizzando AzCopy.

AzCopy è un'utilità della riga di comando progettata per le operazioni di caricamento, download e copia dei dati in e da servizi di archiviazione BLOB, file e tabelle di Microsoft Azure.

Per istruzioni sull'installazione di AzCopy e informazioni aggiuntive sull'utilizzo della piattaforma Azure, vedere [Introduzione all’utilità della riga di comando di AzCopy](../storage/storage-use-azcopy.md).

Collegamenti a indicazioni sulle tecnologie usate per spostare i dati in e/o da un archivio BLOB di Azure sono disponibili qui:

[AZURE.INCLUDE [selettore dello strumento di archiviazione BLOB](../../includes/machine-learning-blob-storage-tool-selector.md)]


> [AZURE.NOTE] Se si utilizza una macchina virtuale che è stata impostata con gli script forniti da [Macchine virtuali della scienza dei dati in Azure](machine-learning-data-science-virtual-machines.md), allora AzCopy è già installata nella macchina virtuale.

> [AZURE.NOTE] Per un'introduzione completa dell'archiviazione BLOB di Azure, vedere [Informazioni di base sui BLOB di Azure](../storage/storage-dotnet-how-to-use-blobs.md) e [Servizio BLOB di Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).


## Prerequisiti

In questo documento si presuppone di avere una sottoscrizione di Azure, un account di archiviazione e delle chiavi di archiviazione corrispondenti per quell'account. Prima di caricare/scaricare i dati, è necessario conoscere il nome e la chiave del proprio account di archiviazione di Azure.

- Per configurare una sottoscrizione di Azure, vedere [Versione di prova gratuita di un mese](https://azure.microsoft.com/pricing/free-trial/).

- Per istruzioni sulla creazione di un account di archiviazione e per ottenere informazioni sull’account e la chiave, vedere [Informazioni sugli account di archiviazione di Azure](../storage/storage-create-storage-account.md).


## Eseguire i comandi di AzCopy

Per eseguire i comandi di AzCopy, aprire una finestra di comando e passare alla directory di installazione di AzCopy contenente il file eseguibile AzCopy.exe.

La sintassi di base per i comandi di AzCopy è la seguente:

	AzCopy /Source:<source> /Dest:<destination> [Options]

>[AZURE.NOTE] È possibile aggiungere il percorso di installazione di AzCopy al percorso di sistema ed eseguire i comandi da qualsiasi directory. Per impostazione predefinita, AzCopy viene installato in *% ProgramFiles (x86) %\\Microsoft SDK\\Azure\\AzCopy* o in *%ProgramFiles%\\Microsoft SDK\\Azure\\AzCopy*.

## Caricare file in un BLOB di Azure

Per caricare un file, usare il comando seguente:

	# Upload from local file system
	AzCopy /Source:<your_local_directory> /Dest: https://<your_account_name>.blob.core.windows.net/<your_container_name> /DestKey:<your_account_key> /S


## Scaricare file da un BLOB di Azure

Per scaricare un file da un BLOB di Azure, utilizzare il comando seguente:

	# Downloading blobs to local file system
	AzCopy /Source:https://<your_account_name>.blob.core.windows.net/<your_container_name>/<your_sub_directory_at_blob>  /Dest:<your_local_directory> /SourceKey:<your_account_key> /Pattern:<file_pattern> /S


## Trasferire i BLOB tra contenitori di Azure

Per trasferire i BLOB tra i contenitori di Azure, utilizzare il comando seguente:

	# Transferring blobs between Azure containers
	AzCopy /Source:https://<your_account_name1>.blob.core.windows.net/<your_container_name1>/<your_sub_directory_at_blob1> /Dest:https://<your_account_name2>.blob.core.windows.net/<your_container_name2>/<your_sub_directory_at_blob2> /SourceKey:<your_account_key1> /DestKey:<your_account_key2> /Pattern:<file_pattern> /S

	<your_account_name>: your storage account name
	<your_account_key>: your storage account key
	<your_container_name>: your container name
	<your_sub_directory_at_blob>: the sub directory in the container
	<your_local_directory>: directory of local file system where files to be uploaded from or the directory of local file system files to be downloaded to
	<file_pattern>: pattern of file names to be transferred. The standard wildcards are supported


## Suggerimenti per l'utilizzo di AzCopy

> [AZURE.TIP]   
> 1. Quando si **caricano** file, */S* caricherà i file in modo ricorsivo. Senza questo parametro, i file nelle sottodirectory non vengono caricati.
> 2. Durante il **download** di file, */S* cerca il contenitore in modo ricorsivo fino a quando non vengono scaricati tutti i file nella directory specificata e nelle relative sottodirectory o tutti i file corrispondenti al criterio specificato nella directory data e nelle relative sottodirectory.
> 3.  Non è possibile specificare un **file BLOB specifico** da scaricare utilizzando il parametro */Source*. Per scaricare un file specifico, specificare il nome del file BLOB per il download utilizzando il parametro */Pattern*. Il parametro **/S** può essere utilizzato per fare in modo che AzCopy cerchi il nome del file in modo ricorsivo. Senza il parametro pattern, AzCopy scarica tutti i file nella directory.

<!---HONumber=AcomDC_0921_2016-->