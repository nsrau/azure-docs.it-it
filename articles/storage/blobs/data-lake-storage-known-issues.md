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
ms.openlocfilehash: dfa4d65464192b90d4a6f74255faaf8b664ce118
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81767969"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Problemi noti con Azure Data Lake Storage Gen2

Questo articolo descrive le limitazioni e i problemi noti di Azure Data Lake Storage Gen2.

## <a name="supported-blob-storage-features"></a>Funzionalità di archiviazione BLOB supportate

Un numero crescente di funzionalità di archiviazione BLOB ora funziona con gli account che hanno uno spazio dei nomi gerarchico. Per un elenco completo, vedere [funzionalità di archiviazione BLOB disponibili in Azure Data Lake storage Gen2](data-lake-storage-supported-blob-storage-features.md).

## <a name="supported-azure-service-integrations"></a>Integrazioni di servizi di Azure supportate

Azure Data Lake Storage Gen2 supporta diversi servizi di Azure che è possibile usare per inserire dati, eseguire analisi e creare rappresentazioni visive. Per un elenco dei servizi di Azure supportati, vedere [servizi di Azure che supportano Azure Data Lake storage Gen2](data-lake-storage-supported-azure-services.md).

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
* [Get Page Ranges](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [BLOB di copia incrementale](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [Inserisci pagina dall'URL](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [Put Blob (Append)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Append Block](https://docs.microsoft.com/rest/api/storageservices/append-block)
* [Accoda blocco da URL](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

I dischi delle macchine virtuali non gestiti non sono supportati negli account che dispongono di uno spazio dei nomi gerarchico. Se si vuole abilitare uno spazio dei nomi gerarchico in un account di archiviazione, collocare i dischi delle macchine virtuali non gestiti in un account di archiviazione in cui non è abilitata la funzionalità di spazio dei nomi gerarchico.

<a id="api-scope-data-lake-client-library" />

## <a name="file-system-support-in-sdks-powershell-and-azure-cli"></a>Supporto del file System in SDK, PowerShell e l'interfaccia della riga di comando di Azure

- Le operazioni get e set ACL non sono attualmente ricorsive.
- Il supporto dell'interfaccia della riga di comando di [Azure](data-lake-storage-directory-file-acl-cli.md) è in anteprima pubblica


## <a name="lifecycle-management-policies"></a>Criteri di gestione del ciclo di vita

* L'eliminazione degli snapshot BLOB non è ancora supportata.  

## <a name="archive-tier"></a>Livello archivio

Attualmente esiste un bug che influiscono sul livello di accesso dell'archivio.


## <a name="blobfuse"></a>Blobfuse

Blobfuse non è supportato.

<a id="known-issues-tools" />

## <a name="azcopy"></a>AzCopy

Usare solo la versione più recente di AzCopy ([AzCopy V10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)).Le versioni precedenti di AzCopy, ad esempio AzCopy v 8.1, non sono supportate.

<a id="storage-explorer" />

## <a name="azure-storage-explorer"></a>Esplora archivi Azure

Usare solo versioni `1.6.0` o versioni successive.

<a id="explorer-in-portal" />

## <a name="storage-explorer-in-the-azure-portal"></a>Storage Explorer nella portale di Azure

Gli ACL non sono ancora supportati.

<a id="third-party-apps" />

## <a name="thirdpartyapplications"></a>Applicazioni di terze parti

Le applicazioni di terze parti che usano le API REST per lavorare continueranno a funzionare se vengono usate con Data Lake Storage Gen2 applicazioni che chiamano API blob probabilmente funzioneranno.

## <a name="access-control-lists-acl-and-anonymous-read-access"></a>Elenchi di controllo di accesso (ACL) e accesso in lettura anonimo

Se è stato concesso [l'accesso in lettura anonimo](storage-manage-access-to-resources.md) a un contenitore, gli ACL non avranno alcun effetto sul contenitore o sui file in tale contenitore.

## <a name="windows-azure-storage-blob-wasb-driver-unsupported-with-data-lake-storage-gen2"></a>Driver di Windows BLOB del servizio di archiviazione di Azure (WASB) (non supportato con Data Lake Storage Gen2)

Attualmente, il driver WASB, progettato per funzionare solo con l'API blob, incontra problemi in alcuni scenari comuni. In particolare, quando è un client di un account di archiviazione gerarchico abilitato per lo spazio dei nomi. L'accesso a più protocolli su Data Lake Storage non mitiga tali problemi. 

Per il tempo (e probabilmente il futuro prevedibile), i clienti che usano il driver WASB come client in un account di archiviazione gerarchico abilitato per lo spazio dei nomi non saranno più supportati. È invece consigliabile scegliere di usare il driver del [file System BLOB di Azure (ABFS)](data-lake-storage-abfs-driver.md) nell'ambiente Hadoop. Se si sta tentando di eseguire la migrazione da un ambiente Hadoop locale con una versione precedente a Hadoop Branch-3, aprire un ticket di supporto di Azure per poter entrare in contatto con il percorso giusto per l'utente e l'organizzazione.
