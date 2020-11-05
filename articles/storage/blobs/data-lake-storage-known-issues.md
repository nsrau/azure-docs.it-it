---
title: Problemi noti con Azure Data Lake Storage Gen2 | Microsoft Docs
description: Informazioni sulle limitazioni e i problemi noti relativi ad Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/28/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 87532876551cacbd4da03b95b10c41600989a947
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93359715"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Problemi noti con Azure Data Lake Storage Gen2

Questo articolo descrive le limitazioni e i problemi noti relativi ad Azure Data Lake Storage Gen2.

## <a name="supported-blob-storage-features"></a>Funzionalità di archiviazione BLOB supportate

Un numero crescente di funzionalità di archiviazione BLOB ora funziona con gli account che hanno uno spazio dei nomi gerarchico. Per un elenco completo, vedere [Funzionalità di archiviazione BLOB disponibili in Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md).

## <a name="supported-azure-service-integrations"></a>Integrazioni di servizi di Azure supportate

Azure Data Lake Storage Gen2 supporta diversi servizi di Azure che è possibile usare per inserire dati, eseguire analisi e creare rappresentazioni visive. Per un elenco dei servizi di Azure supportati, vedere [Servizi di Azure che supportano Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

Vedere [Servizi di Azure che supportano Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

## <a name="supported-open-source-platforms"></a>Piattaforme open source supportate

Diverse piattaforme open source supportano Data Lake Storage Gen2. Per un elenco completo, vedere [Piattaforme open source che supportano Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

Vedere [Piattaforme open source che supportano Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

## <a name="blob-storage-apis"></a>Api di archiviazione BLOB

Le API BLOB e le API di Data Lake Storage Gen2 possono operare sugli stessi dati.

Questa sezione descrive i problemi e le limitazioni delle API BLOB e delle API di Data Lake Storage Gen2 quando vengono usate per operare sugli stessi dati.

* Non è possibile usare l'API BLOB e le API Data Lake Storage per scrivere nella stessa istanza di un file. Se si scrive in un file usando le API di Data Lake Storage Gen2, i blocchi di tale file non saranno visibili alle chiamate all'API BLOB [Get Block List](https://docs.microsoft.com/rest/api/storageservices/get-block-list). L'unica eccezione è quando si usa la sovrascrittura. È possibile sovrascrivere un file o un BLOB usando una delle due API.

* Quando si usa l'operazione [Elenca BLOB](https://docs.microsoft.com/rest/api/storageservices/list-blobs) senza specificare un delimitatore, i risultati includeranno sia le directory che i BLOB. Se si sceglie di specificare un delimitatore, usare solo una barra (`/`). Si tratta dell'unico delimitatore supportato.

* Se si usa l'API [Delete Blob](https://docs.microsoft.com/rest/api/storageservices/delete-blob) per eliminare una directory, tale directory verrà eliminata solo se vuota. Ciò significa che non è possibile usare le directory di eliminazione dell'API BLOB in modo ricorsivo.

Le API REST per i BLOB seguenti non sono supportate:

* [Put Blob (Page)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Put Page](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [Get Page Ranges](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [Incremental Copy Blob](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [Put Page from URL](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [Append Block from URL](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

I dischi non gestiti delle macchine virtuali non sono supportati negli account che hanno uno spazio dei nomi gerarchico. Se si intende abilitare uno spazio dei nomi gerarchico in un account di archiviazione, inserire i dischi non gestiti delle macchine virtuali in un account di archiviazione in cui non è abilitata la funzionalità dello spazio dei nomi gerarchico.

<a id="api-scope-data-lake-client-library"></a>

## <a name="support-for-setting-access-control-lists-acls-recursively"></a>Supporto per l'impostazione in modo ricorsivo degli elenchi di controllo di accesso (ACL)

La possibilità di applicare le modifiche ACL in modo ricorsivo dalla directory padre agli elementi figlio è disponibile a livello generale. Nella versione corrente di questa funzionalità è possibile applicare le modifiche ACL usando PowerShell, l'interfaccia della riga di comando di Azure e .NET, Java e Python SDK. Il supporto non è ancora disponibile per il portale di Azure o Azure Storage Explorer.

<a id="known-issues-tools"></a>

## <a name="azcopy"></a>AzCopy

Usare solo la versione più recente di AzCopy ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)).  Le versioni precedenti di AzCopy, ad esempio AzCopy v 8.1, non sono supportate.

<a id="storage-explorer"></a>

## <a name="azure-storage-explorer"></a>Esplora archivi Azure

Usare solo versioni `1.6.0` o versioni successive.

<a id="explorer-in-portal"></a>

## <a name="storage-explorer-in-the-azure-portal"></a>Storage Explorer nel portale di Azure

Gli elenchi di controllo di accesso non sono ancora supportati.

<a id="third-party-apps"></a>

## <a name="third-party-applications"></a>Applicazioni di terze parti

Le applicazioni di terze parti che usano le API REST continueranno a funzionare se usate con Data Lake Storage Gen2. È probabile che funzionino le applicazioni che chiamano le API BLOB.

## <a name="access-control-lists-acl-and-anonymous-read-access"></a>Elenchi di controllo di accesso (ACL) e accesso in lettura anonimo

Se è stato concesso l'[accesso in lettura anonimo](storage-manage-access-to-resources.md) a un contenitore, gli elenchi di controllo di accesso non avranno alcun effetto su quel contenitore o sui file che contiene.

## <a name="diagnostic-logs"></a>Log di diagnostica

L'impostazione per i giorni di conservazione non è ancora supportata, ma è possibile eliminare i log manualmente usando qualsiasi strumento supportato, ad esempio Azure Storage Explorer, REST o un SDK.

## <a name="lifecycle-management-policies-with-premium-tier-for-azure-data-lake-storage"></a>Criteri di gestione del ciclo di vita con livello Premium per Azure Data Lake Storage

Non è possibile spostare i dati archiviati nel livello Premium tra i livelli ad accesso frequente, ad accesso sporadico e archivio. Tuttavia, è possibile copiare i dati dal livello Premium al livello di accesso frequente in un altro account.

## <a name="dremio-support-with-premium-performance-blockblobstorage-storage-accounts"></a>Supporto di Dremio con account di archiviazione BlockBlobStorage a prestazioni Premium

Dremio non si connette ancora a un account BlockBlobStorage con la funzionalità di spazio dei nomi gerarchico abilitata. 

## <a name="windows-azure-storage-blob-wasb-driver-unsupported-with-data-lake-storage-gen2"></a>Driver del BLOB del servizio di archiviazione di Windows Azure (WASB) (non supportato con Data Lake Storage Gen2)

Il driver WASB, progettato per funzionare solo con l'API BLOB, incontra attualmente problemi in alcuni scenari comuni, in particolare quando è il client di un account di archiviazione abilitato per lo spazio dei nomi gerarchico. L'accesso a più protocolli su Data Lake Storage non risolve tali problemi. 

Per il momento e nel prossimo futuro, non saranno supportati i clienti che usano il driver WASB come client in un account di archiviazione abilitato per lo spazio dei nomi gerarchico. È invece consigliabile usare il driver [Azure Blob File System (ABFS)](data-lake-storage-abfs-driver.md) nell'ambiente Hadoop. Se si sta tentando di eseguire la migrazione da un ambiente Hadoop locale con una versione precedente a Hadoop branch-3, aprire un ticket di supporto di Azure per ricevere indicazioni sul percorso corretto per l'utente e l'organizzazione.
