---
title: Problemi noti con Azure Data Lake Storage Gen2 | Microsoft Docs
description: Informazioni sulle limitazioni e sui problemi noti relativi ad Azure Data Lake Storage Gen2
services: storage
author: normesta
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: normesta
ms.openlocfilehash: d42d05046f4ef133aa3dfb52bbe4e2b72255ccce
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2018
ms.locfileid: "53322893"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Problemi noti con Azure Data Lake Storage Gen2

Questo articolo contiene i problemi noti e le limitazioni temporanee con Azure Data Lake Storage Gen2.

## <a name="api-interoperability"></a>Interoperabilità API

Le API di archiviazione BLOB e le API di Azure Data Lake Gen2 non sono interoperative tra loro.

Se è necessario usare lo stesso strumento per lavorare con tutto il contenuto caricato nell'account, allora non abilitare spazi dei nomi gerarchici in account di archiviazione BLOB finché queste API non diventano interoperative tra loro. Usare un account di archiviazione senza lo spazio dei nomi gerarchico implica che non si dispone dell'accesso alle funzionalità specifiche di Data Lake Storage Gen2, ad esempio l'elenco di controllo di accesso alla directory e a filesystem.

## <a name="blob-storage-apis"></a>Api di archiviazione BLOB

Le API di archiviazione BLOB non sono ancora disponibili per gli account Azure Data Lake Storage Gen2.

Queste API sono disabilitate per evitare problemi di accesso accidentale ai dati che possono verificarsi perché le API di archiviazione BLOB non sono ancora interoperabili con le API di Azure Data Lake Gen2.

Se sono state usate queste API per caricare i dati prima che venissero disabilitate e occorre accedere ai dati per motivi di produzione, contattare il supporto tecnico Microsoft con le informazioni seguenti:

* ID della sottoscrizione (il GUID, non il nome)

* Il nome dell'account di archiviazione

* Se si è coinvolti in modo attivo nella produzione e in tal caso, per quali account di archiviazione?

* Anche se non si è coinvolti in modo attivo nella produzione, indicare se è necessario che i dati vengano copiati in un altro account di archiviazione per qualche motivo e in tal caso, perché?

In queste circostanze è possibile ripristinare l'accesso all'API BLOB per un periodo di tempo limitato in modo che sia possibile copiare i dati in un account di archiviazione che non dispone dell'abilitazione degli spazi dei nomi gerarchici.

I dischi non gestiti di macchine virtuali (VM) dipendono da queste API BLOB disabilitate, quindi se si desidera abilitare spazi dei nomi gerarchici in un account di archiviazione, considerare l'inserimento di dischi non gestiti delle macchine virtuali in un account di archiviazione che non dispone dell'abilitazione degli spazi dei nomi gerarchici.

## <a name="azure-storage-explorer"></a>Esplora archivi Azure

Per visualizzare o gestire gli account Data Lake Storage Gen2 con Azure Storage Explorer, è necessario disporre almeno della versione `1.6.0` dello strumento disponibile come [download gratuito](https://azure.microsoft.com/features/storage-explorer/).

Si noti che la versione di Azure Storage Explorer incorporata nel portale di Azure attualmente non supporta la visualizzazione o la gestione degli account Data Lake Storage Gen2 con spazi dei nomi gerarchici abilitati.

## <a name="blob-viewing-tool"></a>Strumento di visualizzazione BLOB

Lo strumento di visualizzazione BLOB nel portale di Azure offre solo supporto limitato per Azure Data Lake Storage Gen2.

## <a name="third-party-applications"></a>Applicazioni di terze parti

Le applicazioni di terze parti potrebbero non supportare Azure Data Lake Storage Gen2.

Il supporto è a discrezione di ciascun provider di terze parti dell'applicazione. Attualmente, le API di archiviazione BLOB e le API di Azure Data Lake Storage Gen2 possono essere utilizzate per gestire lo stesso contenuto. Poiché Microsoft si impegna per consentire tale interoperabilità, è possibile che molti strumenti di terze parti supportino automaticamente Azure Data Lake Storage Gen2.

## <a name="azcopy-support"></a>Supporto AzCopy

La versione 8 di AzCopy non supporta Data Lake Storage Gen2.

In alternativa, usare la versione di anteprima più recente di AzCopy ( [AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json) ) poiché supporta gli endpoint di Azure Data Lake Storage Gen2.

## <a name="oauth-authentication"></a>Autenticazione OAuth

Servizi come HDInsight e Azure Data Factory non si integrano ancora con l'autenticazione basata su token di connessione OAuth di Azure Active Directory (Azure AD).

## <a name="azure-event-grid"></a>Griglia di eventi di Azure

La [griglia di eventi di Azure](https://azure.microsoft.com/services/event-grid/) non riceve gli eventi da un account Azure Data Lake Gen2 perché tali account non sono ancora in grado di generarli.  

## <a name="soft-delete-and-snapshots"></a>Snapshot ed eliminazione temporanea

Snapshot ed eliminazione temporanea non sono disponibili per gli account Azure Data Lake Storage Gen2.

Tutte le funzionalità di controllo delle versioni compresi [snapshot](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob) ed [eliminazione temporanea](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) non sono ancora disponibili per gli account di archiviazione con spazi dei nomi gerarchici abilitati.

## <a name="object-level-storage-tiers"></a>Livelli di archiviazione a livello di oggetto

I livelli di archiviazione a livello di oggetto (accesso frequente, accesso sporadico e archivio) non sono ancora disponibili per gli account Azure Data Lake Storage Gen2, ma sono disponibili per gli account di archiviazione che non hanno spazi gerarchici abilitati.

## <a name="azure-blob-storage-lifecycle-management-preview-policies"></a>Criteri di gestione del ciclo di vita di archiviazione BLOB di Azure (anteprima)

I criteri di gestione del ciclo di vita di archiviazione BLOB di Azure (anteprima) non sono ancora disponibili per gli account Azure Data Lake Storage Gen2.

Questi criteri sono disponibili per gli account di archiviazione che non hanno spazi gerarchici abilitati.

## <a name="diagnostic-logs"></a>Log di diagnostica

I log di diagnostica non sono disponibili per gli account Azure Data Lake Storage Gen2.

Per richiedere i log di diagnostica, contattare il supporto di Azure. Fornire il nome dell'account e il periodo di tempo per cui si richiedono i log.
