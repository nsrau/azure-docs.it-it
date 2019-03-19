---
title: Problemi noti con Azure Data Lake Storage Gen2 | Microsoft Docs
description: Informazioni sulle limitazioni e sui problemi noti relativi ad Azure Data Lake Storage Gen2
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: normesta
ms.openlocfilehash: 89cfdbdaa034bae5ca736ccb9164255b833ed75d
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57194709"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Problemi noti con Azure Data Lake Storage Gen2

Questo articolo contiene i problemi noti e limitazioni temporanee con Data Lake Storage Gen2.

## <a name="sdk-support-for-data-lake-storage-gen2-accounts"></a>Supporto SDK per gli account Data Lake Storage Gen2

Non sono disponibili SDK che funzioneranno con gli account Data Lake Storage Gen2.

## <a name="blob-storage-apis"></a>Api di archiviazione BLOB

API di archiviazione di BLOB non sono ancora disponibili per gli account Data Lake Storage Gen2.

Queste API sono disabilitate per evitare problemi di accesso accidentale ai dati che possono verificarsi perché le API di archiviazione BLOB non sono ancora interoperabili con le API di Azure Data Lake Gen2.

Se sono state usate queste API per caricare i dati prima che venissero disabilitate e occorre accedere ai dati per motivi di produzione, contattare il supporto tecnico Microsoft con le informazioni seguenti:

* ID della sottoscrizione (il GUID, non il nome)

* Il nome dell'account di archiviazione

* Se si è coinvolti in modo attivo nella produzione e in tal caso, per quali account di archiviazione?

* Anche se non si è coinvolti in modo attivo nella produzione, indicare se è necessario che i dati vengano copiati in un altro account di archiviazione per qualche motivo e in tal caso, perché?

In queste circostanze è possibile ripristinare l'accesso all'API BLOB per un periodo di tempo limitato, in modo che sia possibile copiare i dati in un account di archiviazione in cui la funzionalità degli spazi dei nomi gerarchici non è abilitata.

I dischi non gestiti di macchine virtuali dipendono da queste API BLOB disabilitate e, pertanto, se si intende abilitare spazi dei nomi gerarchici in un account di archiviazione, considerare l'inserimento di dischi non gestiti delle macchine virtuali in un account di archiviazione in cui la funzionalità degli spazi dei nomi gerarchici non è abilitata.

## <a name="api-interoperability"></a>Interoperabilità API

Le API di archiviazione BLOB e le API di Azure Data Lake Gen2 non sono interoperative tra loro.

Se si dispone di strumenti, applicazioni, servizi o script che usano le API BLOB e si intende usarli per interagire con tutto il contenuto caricato nell'account, non abilitare uno spazio dei nomi gerarchico in account di archiviazione BLOB finché le API BLOB non diventano interoperative con le API Azure Data Lake Gen2. Usando un account di archiviazione senza uno spazio dei nomi gerarchico non si dispone dell'accesso alle funzionalità specifiche di Data Lake Storage Gen2, ad esempio l'elenco di controllo di accesso alla directory e al file system.

## <a name="azure-storage-explorer"></a>Esplora archivi Azure

Per visualizzare o gestire gli account Data Lake Storage Gen2 con Azure Storage Explorer, è necessario disporre almeno della versione `1.6.0` dello strumento disponibile come [download gratuito](https://azure.microsoft.com/features/storage-explorer/).

La versione di Storage Explorer incorporata nel portale di Azure non supporta attualmente la visualizzazione o la gestione di account Data Lake Storage Gen2 con la funzionalità degli spazi dei nomi gerarchici abilitata.

## <a name="blob-viewing-tool"></a>Strumento di visualizzazione BLOB

BLOB strumento per la visualizzazione nel portale di Azure offre solo supporto limitato per Data Lake Storage Gen2.

## <a name="third-party-applications"></a>Applicazioni di terze parti

Le applicazioni di terze parti potrebbero non supportare Data Lake Storage Gen2.

Il supporto è a discrezione di ciascun provider di terze parti dell'applicazione. Blob attualmente, le API di archiviazione e archiviazione Gen2 API di Data Lake non può essere usate per gestire il contenuto stesso. Poiché Microsoft si impegna per garantire che l'interoperabilità, è possibile che molti strumenti di terze parti supporta automaticamente Gen2 di archivio Data Lake.

## <a name="azcopy-support"></a>Supporto AzCopy

Versione 8 AzCopy non supporta il Data Lake Storage Gen2.

In alternativa, usare la versione di anteprima più recente di AzCopy ( [v10 AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json) ) perché supporta gli endpoint Data Lake Storage Gen2.

## <a name="azure-event-grid"></a>Griglia di eventi di Azure

La [griglia di eventi di Azure](https://azure.microsoft.com/services/event-grid/) non riceve gli eventi da un account Azure Data Lake Gen2 perché tali account non sono ancora in grado di generarli.  

## <a name="soft-delete-and-snapshots"></a>Snapshot ed eliminazione temporanea

Gli snapshot e l'eliminazione temporanea non sono disponibili per gli account Data Lake Storage Gen2.

Per gli account di archiviazione con la funzionalità degli spazi dei nomi gerarchici abilitata non sono ancora disponibili tutte le funzionalità di controllo delle versioni, compresi [snapshot](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob) ed [eliminazione temporanea](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete).

## <a name="object-level-storage-tiers"></a>Livelli di archiviazione a livello di oggetto

I livelli di archiviazione a livello di oggetto (accesso frequente, accesso sporadico e archivio) non sono ancora disponibili per gli account Azure Data Lake Storage Gen2, ma sono disponibili per gli account di archiviazione senza la funzionalità degli spazi dei nomi gerarchici abilitata.

## <a name="azure-blob-storage-lifecycle-management-policies"></a>Criteri di gestione del ciclo di vita di Archiviazione BLOB di Azure

Criteri di gestione del ciclo di vita di archiviazione Blob Azure non sono ancora disponibili per gli account Data Lake Storage Gen2.

ma sono disponibili per gli account di archiviazione senza la funzionalità degli spazi dei nomi gerarchici abilitata.

## <a name="diagnostic-logs"></a>Log di diagnostica

I log di diagnostica non sono disponibili per gli account Data Lake Storage Gen2.
