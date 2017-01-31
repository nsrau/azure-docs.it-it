---
title: Spostare i dati da e verso l&quot;archiviazione BLOB di Azure usando AzCopy | Documentazione Microsoft
description: Spostamento dei dati da e verso l&quot;archiviazione BLOB di Azure utilizzando AzCopy.
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: c309ceb2-0e83-4a07-b16d-c997dcd62d5c
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: bradsev
translationtype: Human Translation
ms.sourcegitcommit: 9b32c0d9c3bc19a187873eebd6ab21036ee06db8
ms.openlocfilehash: 1acc98f9688ced2e1cf177a881b28ce98c0bff9e


---
# <a name="move-data-to-and-from-azure-blob-storage-using-azcopy"></a>Spostamento dei dati da e verso l'archivio BLOB di Azure tramite AzCopy
AzCopy è un'utilità della riga di comando progettata per le operazioni di caricamento, download e copia dei dati in e da servizi di archiviazione BLOB, file e tabelle di Microsoft Azure.

Per istruzioni sull'installazione di AzCopy e informazioni aggiuntive sull'utilizzo della piattaforma Azure, vedere [Introduzione all’utilità della riga di comando di AzCopy](../storage/storage-use-azcopy.md).

[!INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Se si utilizza una macchina virtuale che è stata impostata con gli script forniti da [Macchine virtuali della scienza dei dati in Azure](machine-learning-data-science-virtual-machines.md), allora AzCopy è già installata nella macchina virtuale.
> 
> [!NOTE]
> Per un'introduzione completa all'archiviazione BLOB di Azure, vedere [Introduzione all'archivio BLOB di Azure](../storage/storage-dotnet-how-to-use-blobs.md) e [Servizio BLOB di Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

## <a name="prerequisites"></a>Prerequisiti
In questo documento si presuppone di avere una sottoscrizione di Azure, un account di archiviazione e delle chiavi di archiviazione corrispondenti per quell'account. Prima di caricare/scaricare i dati, è necessario conoscere il nome e la chiave del proprio account di archiviazione di Azure.

* Per configurare una sottoscrizione di Azure, vedere [Versione di prova gratuita di un mese](https://azure.microsoft.com/pricing/free-trial/).
* Per istruzioni sulla creazione di un account di archiviazione e per ottenere informazioni sull’account e la chiave, vedere [Informazioni sugli account di archiviazione di Azure](../storage/storage-create-storage-account.md).

## <a name="run-azcopy-commands"></a>Eseguire i comandi di AzCopy
Per eseguire i comandi di AzCopy, aprire una finestra di comando e passare alla directory di installazione di AzCopy contenente il file eseguibile AzCopy.exe. 

La sintassi di base per i comandi di AzCopy è la seguente:

    AzCopy /Source:<source> /Dest:<destination> [Options]

> [!NOTE]
> È possibile aggiungere il percorso di installazione di AzCopy al percorso di sistema ed eseguire i comandi da qualsiasi directory. Per impostazione predefinita, AzCopy viene installato in *%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy* o in *%ProgramFiles%\Microsoft SDKs\Azure\AzCopy*.
> 
> 

## <a name="upload-files-to-an-azure-blob"></a>Caricare file in un BLOB di Azure
Per caricare un file, usare il comando seguente:

    # Upload from local file system
    AzCopy /Source:<your_local_directory> /Dest: https://<your_account_name>.blob.core.windows.net/<your_container_name> /DestKey:<your_account_key> /S


## <a name="download-files-from-an-azure-blob"></a>Scaricare file da un BLOB di Azure
Per scaricare un file da un BLOB di Azure, utilizzare il comando seguente:

    # Downloading blobs to local file system
    AzCopy /Source:https://<your_account_name>.blob.core.windows.net/<your_container_name>/<your_sub_directory_at_blob>  /Dest:<your_local_directory> /SourceKey:<your_account_key> /Pattern:<file_pattern> /S


## <a name="transfer-blobs-between-azure-containers"></a>Trasferire i BLOB tra contenitori di Azure
Per trasferire i BLOB tra i contenitori di Azure, utilizzare il comando seguente:

    # Transferring blobs between Azure containers
    AzCopy /Source:https://<your_account_name1>.blob.core.windows.net/<your_container_name1>/<your_sub_directory_at_blob1> /Dest:https://<your_account_name2>.blob.core.windows.net/<your_container_name2>/<your_sub_directory_at_blob2> /SourceKey:<your_account_key1> /DestKey:<your_account_key2> /Pattern:<file_pattern> /S

    <your_account_name>: your storage account name
    <your_account_key>: your storage account key
    <your_container_name>: your container name
    <your_sub_directory_at_blob>: the sub directory in the container
    <your_local_directory>: directory of local file system where files to be uploaded from or the directory of local file system files to be downloaded to
    <file_pattern>: pattern of file names to be transferred. The standard wildcards are supported


## <a name="tips-for-using-azcopy"></a>Suggerimenti per l'utilizzo di AzCopy
> [!TIP]
> 1. Quando si **caricano** file, */S* caricherà i file in modo ricorsivo. Senza questo parametro, i file nelle sottodirectory non vengono caricati.  
> 2. Durante il **download** di file, */S* cerca il contenitore in modo ricorsivo fino a quando non vengono scaricati tutti i file nella directory specificata e nelle relative sottodirectory o tutti i file corrispondenti al criterio specificato nella directory data e nelle relative sottodirectory.  
> 3. Non è possibile specificare un **file BLOB specifico** da scaricare utilizzando il parametro */Source* . Per scaricare un file specifico, specificare il nome del file BLOB per il download utilizzando il parametro */Pattern* . **/S** può essere utilizzato per fare in modo che AzCopy cerchi il nome del file in modo ricorsivo. Senza il parametro pattern, AzCopy scarica tutti i file nella directory.
> 
> 




<!--HONumber=Dec16_HO1-->


