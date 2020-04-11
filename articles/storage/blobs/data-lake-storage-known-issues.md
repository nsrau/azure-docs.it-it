---
title: Problemi noti con Azure Data Lake Storage Gen2 | Microsoft Docs
description: Informazioni sulle limitazioni e sui problemi noti di Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 204b5dd4661b34aae8b76d65505a65e20f293f0f
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115334"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Problemi noti con Azure Data Lake Storage Gen2

Questo articolo descrive le limitazioni e i problemi noti di Azure Data Lake Storage Gen2.

## <a name="supported-blob-storage-features"></a>Funzionalità di archiviazione BLOB supportate

Un numero crescente di funzionalità di archiviazione BLOB ora funziona con gli account con uno spazio dei nomi gerarchico. Per un elenco completo, vedere Funzionalità di archiviazione BLOB disponibili in Archiviazione dati di Azure2.For a complete list, [see Blob Storage features available in Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md).

## <a name="supported-azure-service-integrations"></a>Integrazioni di servizi di Azure supportateSupported Azure service integrations

Azure Data Lake Storage Gen2 supporta diversi servizi di Azure che è possibile usare per l'inserimento di dati, l'esecuzione di analisi e la creazione di rappresentazioni visive. Per un elenco dei servizi di Azure supportati, vedere Servizi di [Azure che supportano Azure Data Lake Storage Gen2.](data-lake-storage-supported-azure-services.md)

Vedere [Servizi di Azure che supportano Azure Data Lake Storage Gen2.](data-lake-storage-supported-azure-services.md)

## <a name="supported-open-source-platforms"></a>Piattaforme open source supportate

Diverse piattaforme open source supportano Data Lake Storage Gen2. Per un elenco completo, vedere [Piattaforme Open Source che supportano Azure Data Lake Storage Gen2.](data-lake-storage-supported-open-source-platforms.md)

Vedere [Piattaforme open source che supportano Azure Data Lake Storage Gen2.](data-lake-storage-supported-open-source-platforms.md)

## <a name="blob-storage-apis"></a>Api di archiviazione BLOB

Le API BLOB e le API Gen2 di Data Lake Storage possono operare sugli stessi dati.

Questa sezione descrive i problemi e le limitazioni con l'uso di API BLOB e API Gen2 di Data Lake Storage per operare sugli stessi dati.

* Non è possibile usare entrambe le API BLOB e le API di Archiviazione data lake per scrivere nella stessa istanza di un file. Se si scrive in un file usando le API Gen2 di Data Lake Storage, i blocchi del file non saranno visibili alle chiamate all'API BLOB [Ottieni elenco blocchi.](https://docs.microsoft.com/rest/api/storageservices/get-block-list) È possibile sovrascrivere un file usando le API Gen2 di Data Lake Storage o le API BLOB. Ciò non influirà sulle proprietà del file.

* Quando si usa l'operazione [Elenca BLOB](https://docs.microsoft.com/rest/api/storageservices/list-blobs) senza specificare un delimitatore, i risultati includeranno sia directory che BLOB. Se si sceglie di utilizzare un delimitatore,`/`utilizzare solo una barra ( ). Questo è l'unico delimitatore supportato.

* Se si usa l'API [Elimina BLOB](https://docs.microsoft.com/rest/api/storageservices/delete-blob) per eliminare una directory, tale directory verrà eliminata solo se è vuota. Ciò significa che non è possibile usare le directory di eliminazione dell'API BLOB in modo ricorsivo.

Queste API REST BLOB non sono supportate:These Blob REST APIs aren't supported:

* [Put Blob (pagina)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Put Page](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [Get Page Ranges](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [BLOB di copia incrementale](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [Inserisci pagina dall'URL](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [Put Blob (Aggiungi)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Append Block](https://docs.microsoft.com/rest/api/storageservices/append-block)
* [Aggiungi blocco da URL](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

I dischi di macchine virtuali non gestiti non sono supportati negli account con uno spazio dei nomi gerarchico. Se si vuole abilitare uno spazio dei nomi gerarchico in un account di archiviazione, inserire i dischi delle macchine virtuali non gestiti in un account di archiviazione per il quale non è abilitata la funzionalità dello spazio dei nomi gerarchico.

<a id="api-scope-data-lake-client-library" />

## <a name="file-system-support-in-sdks"></a>Supporto del file system negli SDK

Le operazioni Get e set ACL non sono attualmente ricorsive.

## <a name="file-system-support-in-powershell-and-azure-cli"></a>File system support in PowerShell and Azure CLI

- Il supporto di [PowerShell](data-lake-storage-directory-file-acl-powershell.md) e [dell'interfaccia della riga di](data-lake-storage-directory-file-acl-cli.md) comando di Azure è in anteprima pubblica.
- Le operazioni Get e set ACL non sono attualmente ricorsive.

## <a name="lifecycle-management-policies"></a>Criteri di gestione del ciclo di vita

* L'eliminazione degli snapshot BLOB non è ancora supportata.  

## <a name="archive-tier"></a>Livello di archiviazione

È attualmente presente un bug che influisce sul livello di accesso all'archivio.


## <a name="blobfuse"></a>Blobfuse

Blobfuse non è supportato.

<a id="known-issues-tools" />

## <a name="azcopy"></a>AzCopy

Utilizzare solo la versione più recente di AzCopy ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)).Le versioni precedenti di AzCopy, ad esempio AzCopy v8.1, non sono supportate.

<a id="storage-explorer" />

## <a name="azure-storage-explorer"></a>Esplora archivi Azure

Utilizzare solo `1.6.0` versioni o versioni successive.

<a id="explorer-in-portal" />

## <a name="storage-explorer-in-the-azure-portal"></a>Storage Explorer nel portale di AzureStorage Explorer in the Azure portal

Gli ACL non sono ancora supportati.

<a id="third-party-apps" />

## <a name="thirdpartyapplications"></a>Applicazioni di terze parti

Le applicazioni di terze parti che usano le API REST per funzionare continueranno a funzionare se vengono usate con le applicazioni Data Lake Storage Gen2 che chiamano le API BLOB probabilmente funzioneranno.

## <a name="access-control-lists-acl-and-anonymous-read-access"></a>Elenchi di controllo di accesso (ACL) e accesso in lettura anonimo

Se [l'accesso in lettura anonimo](storage-manage-access-to-resources.md) è stato concesso a un contenitore, gli ACL non hanno alcun effetto su tale contenitore o sui file in tale contenitore.

## <a name="windows-azure-storage-blob-wasb-driver-unsupported-with-data-lake-storage-gen2"></a>Driver BLOB di Archiviazione di Windows Azure (WASB) (non supportato con Data Lake Storage Gen2)Windows Azure Storage Blob (WASB) driver (un supported with Data Lake Storage Gen2)

Attualmente, il driver WASB, che è stato progettato per funzionare solo con l'API BLOB, rileva problemi in alcuni scenari comuni. In particolare, quando si tratta di un client a un account di archiviazione gerarchico abilitato allo spazio dei nomi. L'accesso multiprotocollo su Data Lake Storage non ridurrà questi problemi. 

Per il momento (e molto probabilmente nel prossimo futuro), non supporteremo i clienti che utilizzano il driver WASB come client per un account di archiviazione gerarchico abilitato allo spazio dei nomi. È invece consigliabile scegliere di usare il driver [Azure Blob File System (ABFS)](data-lake-storage-abfs-driver.md) nell'ambiente Hadoop. Se si sta tentando di eseguire la migrazione da un ambiente Hadoop locale con una versione precedente a Hadoop branch-3, aprire un ticket di supporto di Azure in modo che sia possibile contattare l'utente sulla strada giusta per l'utente e l'organizzazione.
