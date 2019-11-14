---
title: Problemi noti con Azure Data Lake Storage Gen2 | Microsoft Docs
description: Informazioni sulle limitazioni e sui problemi noti relativi ad Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 78693dceaac119279b1c1d06a6c3a18cc4fdb485
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033949"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Problemi noti con Azure Data Lake Storage Gen2

Questo articolo elenca le funzionalità e gli strumenti non ancora supportati o solo parzialmente supportati con gli account di archiviazione che hanno uno spazio dei nomi gerarchico (Azure Data Lake Storage Gen2).

<a id="blob-apis-disabled" />

## <a name="issues-and-limitations-with-using-blob-apis"></a>Problemi e limitazioni dell'uso delle API BLOB

Le API BLOB e le API Data Lake Storage Gen2 possono operare sugli stessi dati.

Questa sezione descrive i problemi e le limitazioni dell'uso delle API BLOB e delle API Data Lake Storage Gen2 per operare sugli stessi dati.

* Non è possibile usare sia API BLOB sia API Data Lake Storage per scrivere nella stessa istanza di un file. Se si scrive in un file usando Data Lake Storage Gen2 API, i blocchi del file non saranno visibili alle chiamate all'API blob [Get Block List](https://docs.microsoft.com/rest/api/storageservices/get-block-list) . È possibile sovrascrivere un file usando API Data Lake Storage Gen2 o API blob. Questa operazione non influirà sulle proprietà del file.

* Quando si usa l'operazione [List Blobs](https://docs.microsoft.com/rest/api/storageservices/list-blobs) senza specificare un delimitatore, i risultati includeranno sia le directory che i BLOB. Se si sceglie di utilizzare un delimitatore, utilizzare solo una barra (`/`). Questo è l'unico delimitatore supportato.

* Se si usa l'API [Delete Blob](https://docs.microsoft.com/rest/api/storageservices/delete-blob) per eliminare una directory, tale directory verrà eliminata solo se è vuota. Ciò significa che non è possibile usare le directory di eliminazione dell'API BLOB in modo ricorsivo.

Queste API REST BLOB non sono supportate:

* [Put Blob (pagina)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Put Page](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [Ottenere gli intervalli di pagine](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [BLOB di copia incrementale](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [Inserisci pagina dall'URL](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [Put Blob (Append)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Append Block](https://docs.microsoft.com/rest/api/storageservices/append-block)
* [Accoda blocco da URL](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

I dischi delle macchine virtuali non gestiti non sono supportati negli account che dispongono di uno spazio dei nomi gerarchico. Se si vuole abilitare uno spazio dei nomi gerarchico in un account di archiviazione, collocare i dischi delle macchine virtuali non gestiti in un account di archiviazione in cui non è abilitata la funzionalità di spazio dei nomi gerarchico.

## <a name="support-for-other-blob-storage-features"></a>Supporto per altre funzionalità di archiviazione BLOB

La tabella seguente elenca tutte le altre funzionalità e gli strumenti non ancora supportati o solo parzialmente supportati con gli account di archiviazione che hanno uno spazio dei nomi gerarchico (Azure Data Lake Storage Gen2).

| Funzionalità/strumento    | Altre informazioni    |
|--------|-----------|
| **API Data Lake Storage Gen2** | Supporto parziale <br><br>Nella versione corrente è possibile usare Data Lake Storage Gen2 le API **Rest** per interagire con le directory e impostare gli elenchi di controllo di accesso (ACL), ma non sono presenti altri SDK, ad esempio .NET, Java o Python, per eseguire tali attività. Per eseguire altre attività, ad esempio il caricamento e il download di file, è possibile usare gli SDK per BLOB.  |
| **AzCopy** | Supporto specifico della versione <br><br>Usare solo la versione più recente di AzCopy ([AzCopy V10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). Le versioni precedenti di AzCopy, ad esempio AzCopy v 8.1, non sono supportate.|
| **Criteri di gestione del ciclo di vita dell'archiviazione BLOB di Azure** | Sono supportati tutti i livelli di accesso. Il livello di accesso all'archivio è attualmente in anteprima. L'eliminazione degli snapshot BLOB non è ancora supportata. |
| **Rete per la distribuzione di contenuti (CDN) di Azure** | Non ancora supportato|
| **Ricerca di Azure** |Supportato (anteprima)|
| **Azure Storage Explorer** | Supporto specifico della versione <br><br>Utilizzare solo le versioni `1.6.0` tramite `1.10.0`. <br> La versione `1.10.0` è disponibile come [download gratuito](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-relnotes). La versione `1.11.0` non è ancora supportata.|
| **ACL del contenitore BLOB** |Non ancora supportato|
| **Blobfuse** |Non ancora supportato|
| **Domini personalizzati** |Non ancora supportato|
| **Storage Explorer nella portale di Azure** | Supporto limitato. Gli ACL non sono ancora supportati. |
| **Registrazione diagnostica** |I log di diagnostica sono supportati (anteprima).<br><br>L'abilitazione dei log nell'portale di Azure non è attualmente supportata. Di seguito è riportato un esempio di come abilitare i log usando PowerShell. <br><br>`$storageAccount = Get-AzStorageAccount -ResourceGroupName <resourceGroup> -Name <storageAccountName>`<br><br>`Set-AzStorageServiceLoggingProperty -Context $storageAccount.Context -ServiceType Blob -LoggingOperations read,write,delete -RetentionDays <days>`. <br><br>Assicurarsi di specificare `Blob` come valore del parametro `-ServiceType`, come illustrato in questo esempio. <br><br>Attualmente non è possibile usare Azure Storage Explorer per la visualizzazione dei log di diagnostica. Per visualizzare i log, usare AzCopy o gli SDK.
| **Archiviazione non modificabile** |Non ancora supportato <br><br>L'archiviazione non modificabile offre la possibilità di archiviare i dati in un [worm (scrivere una sola volta, leggere molti)](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage) stato.|
| **Livelli a livello di oggetto** |Sono supportati i livelli ad accesso sporadico e archivio. Il livello archivio è in anteprima. Tutti gli altri livelli di accesso non sono ancora supportati.|
| **Supporto di PowerShell e CLI** | Funzionalità limitate <br><br>Sono supportate le operazioni BLOB. L'utilizzo delle directory e l'impostazione degli elenchi di controllo di accesso (ACL) non sono ancora supportati. |
| **Siti web statici** |Non ancora supportato <br><br>In particolare, la possibilità di gestire i file nei [siti web statici](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website).|
| **Applicazioni di terze parti** | Supporto limitato <br><br>Le applicazioni di terze parti che usano le API REST per lavorare continueranno a funzionare se vengono usate con Data Lake Storage Gen2. <br>Le applicazioni che chiamano API blob probabilmente funzioneranno.|
|**eliminazione temporanea** |Non ancora supportato|
| **Funzionalità di controllo delle versioni** |Non ancora supportato <br><br>Sono incluse l' [eliminazione](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)temporanea e altre funzionalità di controllo delle versioni, ad esempio gli [snapshot](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob).|


