---
title: Problemi noti con Azure Data Lake Storage Gen2 | Microsoft Docs
description: Informazioni sulle limitazioni e sui problemi noti di Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 7d637c2fb3f4a4d5f8deac9cd99c0a44af6568e6
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77919612"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Problemi noti con Azure Data Lake Storage Gen2

Questo articolo descrive le limitazioni e i problemi noti di Azure Data Lake Storage Gen2.

## <a name="supported-blob-storage-features"></a>Funzionalità di archiviazione BLOB supportate

Un numero crescente di funzionalità di archiviazione BLOB ora funziona con gli account che hanno uno spazio dei nomi gerarchico. Per un elenco completo, vedere [funzionalità di archiviazione BLOB disponibili in Azure Data Lake storage Gen2](data-lake-storage-supported-blob-storage-features.md).

## <a name="supported-azure-service-integrations"></a>Integrazioni di servizi di Azure supportate

Data Lake Storage Gen2 supporta diversi servizi di Azure che è possibile usare per inserire dati, eseguire analisi e creare rappresentazioni visive. Per un elenco dei servizi di Azure supportati, vedere [servizi di Azure che supportano Azure Data Lake storage Gen2](data-lake-storage-supported-azure-services.md).

Vedere i [servizi di Azure che supportano Azure Data Lake storage Gen2](data-lake-storage-supported-azure-services.md).

## <a name="supported-open-source-platforms"></a>Piattaforme open source supportate

Diverse piattaforme open source supportano Data Lake Storage Gen2. Per un elenco completo, vedere [piattaforme open source che supportano Azure Data Lake storage Gen2](data-lake-storage-supported-open-source-platforms.md).

Vedere [piattaforme open source che supportano Azure Data Lake storage Gen2](data-lake-storage-supported-open-source-platforms.md).

## <a name="blob-storage-apis"></a>Api di archiviazione BLOB

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

<a id="api-scope-data-lake-client-library" />

## <a name="file-system-support-in-sdks"></a>Supporto del file System negli SDK

- [.NET](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md) e [Python](data-lake-storage-directory-file-acl-python.md)e [JavaScript](data-lake-storage-directory-file-acl-javascript.md) e il supporto sono disponibili in anteprima pubblica. Gli altri SDK non sono attualmente supportati.
- Le operazioni get e set ACL non sono attualmente ricorsive.

## <a name="file-system-support-in-powershell-and-azure-cli"></a>Supporto file System in PowerShell e nell'interfaccia della riga di comando di Azure

- [PowerShell](data-lake-storage-directory-file-acl-powershell.md) e il supporto dell'interfaccia della riga di comando di [Azure](data-lake-storage-directory-file-acl-cli.md) sono in anteprima pubblica.
- Le operazioni get e set ACL non sono attualmente ricorsive.

## <a name="lifecycle-management-policies"></a>Criteri di gestione del ciclo di vita

* L'eliminazione degli snapshot BLOB non è ancora supportata.  

* Attualmente sono presenti bug che influiscono sui criteri di gestione del ciclo di vita e sul livello di accesso dell'archivio. 

## <a name="diagnostic-logs"></a>Log di diagnostica

Non è possibile usare Azure Storage Explorer 1.10. x per la visualizzazione dei log di diagnostica. Per visualizzare i log, usare AzCopy o gli SDK.

## <a name="blobfuse"></a>Blobfuse

Blobfuse non è supportato.



<a id="known-issues-tools" />

## <a name="azcopy"></a>AzCopy

Usare solo la versione più recente di AzCopy ([AzCopy V10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). Le versioni precedenti di AzCopy, ad esempio AzCopy v 8.1, non sono supportate.

<a id="storage-explorer" />

## <a name="azure-storage-explorer"></a>Esplora archivi Azure

Utilizzare solo le versioni `1.6.0` o versione successiva. Attualmente esiste un bug di archiviazione che interessa la versione `1.11.0` che possono causare errori di autenticazione in determinati scenari. È in corso il rollup di una correzione per il bug di archiviazione, ma come soluzione alternativa è consigliabile usare la versione `1.10.x` , disponibile come [download gratuito](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-relnotes). `1.10.x` non è influenzato dal bug di archiviazione.

<a id="explorer-in-portal" />

## <a name="storage-explorer-in-the-azure-portal"></a>Storage Explorer nel portale di Azure

Gli ACL non sono ancora supportati.

<a id="third-party-apps" />

## <a name="thirdpartyapplications"></a>Applicazioni di terze parti

Le applicazioni di terze parti che usano le API REST per lavorare continueranno a funzionare se vengono usate con Data Lake Storage Gen2 applicazioni che chiamano API blob probabilmente funzioneranno.





