---
title: Problemi noti con Azure Data Lake Storage Gen2 | Microsoft Docs
description: Informazioni sulle limitazioni e sui problemi noti relativi ad Azure Data Lake Storage Gen2
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: normesta
ms.openlocfilehash: 61d168a5f501923812db5945fa6df439ae7e70f9
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65145096"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Problemi noti con Azure Data Lake Storage Gen2

Questo articolo elenca le funzionalità e strumenti che non ancora supportati o supportati solo in parte con gli account di archiviazione con uno spazio dei nomi gerarchico (Azure Data Lake Storage Gen2).

<a id="blob-apis-disabled" />

## <a name="blob-storage-apis"></a>Api di archiviazione BLOB

Le API di archiviazione BLOB sono disabilitati per evitare problemi di accesso accidentale dei dati che possono verificarsi perché l'API di archiviazione Blob non sono ancora l'interoperabilità con API di Azure Data Lake Gen2.

### <a name="what-to-do-with-existing-tools-applications-and-services"></a>Cosa fare con i servizi, applicazioni e gli strumenti esistenti

Se uno qualsiasi di questi usano le API di Blob e si vuole usarli per rivolgersi a tutto il contenuto caricato nell'account, quindi non abilitare uno spazio dei nomi gerarchico in account di archiviazione Blob finché non diventano l'interoperabilità con API di Azure Data Lake Gen2 API Blob.

Usando un account di archiviazione senza uno spazio dei nomi gerarchico, significa che quindi non hai accesso alle funzionalità specifiche di Data Lake Storage Gen2, ad esempio file e directory elenchi di controllo di accesso di sistema.

### <a name="what-to-do-with-unmanaged-virtual-machine-vm-disks"></a>Cosa fare con i dischi non gestiti di macchine virtuali (VM)

Queste variano a seconda le API di archiviazione Blob disabilitato, pertanto, se si desidera abilitare uno spazio dei nomi gerarchico in un account di archiviazione, prendere in considerazione inserirli in un account di archiviazione che non è abilitata la funzionalità di spazio dei nomi gerarchico.

### <a name="what-to-do-if-you-used-blob-apis-to-load-data-before-blob-apis-were-disabled"></a>Cosa fare se si usa l'API Blob per caricare i dati prima che le API di Blob sono state disabilitate

Se sono state usate queste API per caricare i dati prima che venissero disabilitate e occorre accedere ai dati per motivi di produzione, contattare il supporto tecnico Microsoft con le informazioni seguenti:

> [!div class="checklist"]
> * ID della sottoscrizione (GUID, non il nome).
> * Nomi degli account di archiviazione.
> * Se si verifica in modo attivo nell'ambiente di produzione e in tal caso, per gli account di archiviazione.
> * Anche se non si è coinvolti in modo attivo nella produzione, indicare se è necessario che i dati vengano copiati in un altro account di archiviazione per qualche motivo e in tal caso, perché?

In queste circostanze è possibile ripristinare l'accesso all'API BLOB per un periodo di tempo limitato, in modo che sia possibile copiare i dati in un account di archiviazione in cui la funzionalità degli spazi dei nomi gerarchici non è abilitata.

## <a name="all-other-features-and-tools"></a>Tutte le altre funzionalità e strumenti

La tabella seguente elenca tutte le altre funzionalità e strumenti che non ancora supportati o supportati solo in parte con gli account di archiviazione con uno spazio dei nomi gerarchico (Azure Data Lake Storage Gen2).

| Funzionalità o lo strumento    | Altre informazioni    |
|--------|-----------|
| **API per gli account di archiviazione Gen2 di archivio Data Lake** | Supporto parziale <br><br>È possibile usare Data Lake Storage Gen2 **REST** API, ma le API in altri SDK di Blob, ad esempio .NET, Java, Python SDK non sono ancora disponibili.|
| **AzCopy** | Supporto specifico della versione <br><br>Usare solo la versione più recente di AzCopy ([v10 AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). Le versioni precedenti di AzCopy, ad esempio AzCopy v8.1, non sono supportati.|
| **Criteri di gestione del ciclo di vita di archiviazione di Azure Blob** | Non è ancora supportata |
| **Rete CDN di Azure (CDN)** | Non è ancora supportata|
| **Ricerca di Azure** |Non è ancora supportata|
| **Azure Storage Explorer** | Supporto specifico della versione <br><br>Usare la versione sola `1.6.0` o versione successiva. <br>Versione `1.6.0` è disponibile come un [download gratuito](https://azure.microsoft.com/features/storage-explorer/).|
| **Elenchi ACL del contenitore BLOB** |Non è ancora supportata|
| **Blobfuse** |Non è ancora supportata|
| **Domini personalizzati** |Non è ancora supportata|
| **Log di diagnostica** |Non è ancora supportata|
| **Esplorazione del file System** | Supporto limitato |
| **Archiviazione non modificabile** |Non è ancora supportata <br><br>L'archiviazione non modificabile ti offre la possibilità di archiviare i dati in un [WORM (Write Once, Read Many)](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage) dello stato.|
| **Livelli a livello di oggetto** |Non è ancora supportata <br><br>Ad esempio:  Premium, i livelli di accesso frequente ad accesso sporadico e archivio.|
| **Supporto della riga di comando e PowerShell** | Funzionalità limitate <br><br>È possibile creare un account con Powershell o l'interfaccia della riga di comando. Non è possibile eseguire operazioni o impostare elenchi di controllo di accesso nel file System, directory e file.|
| **Siti Web statici** |Non è ancora supportata <br><br>In particolare, la possibilità di rendere disponibili i file per [siti Web statici](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website).|
| **Applicazioni di terze parti** | Supporto limitato <br><br>Le applicazioni di terze parti che consentono di utilizzare le API REST continueranno a funzionare se vengono utilizzati con Data Lake Storage Gen2. <br>Se si dispone di un'applicazione che usa le API di Blob, tale applicazione avrà probabilmente i problemi se si usa quell'applicazione con Data Lake Storage Gen2. Per altre informazioni, vedere la [archivio API sono disabilitate per gli account di archiviazione Gen2 di Data Lake Storage Blob](#blob-apis-disabled) sezione di questo articolo.|
| **Funzionalità di controllo delle versioni** |Non è ancora supportata <br><br>Sono inclusi [istantanee](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob) e [eliminazione temporanea](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete).|
|

