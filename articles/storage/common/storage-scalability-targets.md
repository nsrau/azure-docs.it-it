---
title: Obiettivi di scalabilità e prestazioni per archiviazione di Azure-account di archiviazione
description: Informazioni sugli obiettivi di scalabilità e prestazioni, tra cui la capacità, la frequenza delle richieste e la larghezza di banda in ingresso e in uscita, per gli account di archiviazione di Azure.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: 046c2308d5cef2df7e12b6185fc24b8df4f821dc
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326962"
---
# <a name="azure-storage-scalability-and-performance-targets-for-storage-accounts"></a>Obiettivi di scalabilità e prestazioni per archiviazione di Azure per gli account di archiviazione

Questo articolo illustra nel dettaglio gli obiettivi di scalabilità e prestazioni per gli account di archiviazione di Azure. Gli obiettivi di scalabilità e prestazioni elencati di seguito sono di fascia alta, ma possono essere conseguiti. In tutti i casi, la velocità e la larghezza di banda richieste e ottenute dall’account di archiviazione dipendono dalla dimensione degli oggetti archiviati, dai modelli di accesso utilizzati e dal tipo di carico di lavoro eseguito dall’applicazione.

Assicurarsi di eseguire il test del servizio per determinare se le prestazioni soddisfano i requisiti. Se possibile, evitare picchi improvvisi nella frequenza di traffico e assicurarsi che questo sia ben distribuito tra le partizioni.

Quando l'applicazione raggiunge il limite in termini di carico di lavoro che può essere gestito da una partizione, Archiviazione di Azure inizierà a restituire il codice di errore 503 (Server occupato) o 500 (Timeout operazione). Se si verificano errori 503, provare a modificare l'applicazione in modo da usare un criterio di backoff esponenziale per i nuovi tentativi. Il backoff esponenziale consente di ridurre il carico sulla partizione e di uniformare i picchi di traffico verso tale partizione.

## <a name="storage-account-scale-limits"></a>Limiti di scalabilità dell'account di archiviazione

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

## <a name="premium-performance-storage-account-scale-limits"></a>Limiti di scalabilità dell'account di archiviazione prestazioni Premium

[!INCLUDE [azure-premium-limits](../../../includes/azure-storage-limits-premium.md)]

## <a name="storage-resource-provider-scale-limits"></a>Limiti di scalabilità del provider delle risorse di archiviazione

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="azure-blob-storage-scale-targets"></a>Obiettivi di scalabilità dell'archiviazione BLOB di Azure

[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

## <a name="azure-files-scale-targets"></a>Obiettivi di scalabilità di File di Azure

Per altre informazioni sugli obiettivi di scalabilità e prestazioni per File di Azure e Sincronizzazione file di Azure, vedere [Obiettivi di scalabilità e prestazioni di File di Azure](../files/storage-files-scale-targets.md).

> [!IMPORTANT]
> I limiti dell'account di archiviazione sono validi per tutte le condivisioni. La scalabilità fino al valore massimo per gli account di archiviazione è ottenibile solo se è presente una sola condivisione per ogni account di archiviazione.
>
> Le condivisioni file standard più grandi di 5 TiB sono in anteprima e presentano alcune limitazioni.
> Per un elenco delle limitazioni e per l'onboarding nell'anteprima di queste dimensioni di condivisione file più grandi, vedere la sezione relativa alle [condivisioni file standard](../files/storage-files-planning.md#standard-file-shares) della Guida alla pianificazione del file di Azure.

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="premium-files-scale-targets"></a>Destinazioni di scalabilità dei file Premium

Esistono tre categorie di limitazioni da considerare per i file Premium: gli account di archiviazione, le condivisioni e i file.

Ad esempio:  Una singola condivisione può raggiungere 100.000 IOPS e un singolo file può essere scalato fino a 5.000 IOPS. Se, ad esempio, si dispone di tre file in una condivisione, il numero massimo di IOPs che è possibile ottenere da tale condivisione è 15.000.

#### <a name="premium-file-share-limits"></a>Limiti di condivisione file Premium

[!INCLUDE [storage-files-premium-scale-targets](../../../includes/storage-files-premium-scale-targets.md)]

### <a name="azure-file-sync-scale-targets"></a>Obiettivi di scalabilità di Sincronizzazione file di Azure

Sincronizzazione file di Azure è stato progettato per supportare un utilizzo senza limiti, cosa però non sempre possibile. La tabella seguente indica i limiti dei test Microsoft e anche le destinazioni con limiti rigidi:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="azure-queue-storage-scale-targets"></a>Obiettivi di scalabilità di Archiviazione code di Azure

[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

## <a name="azure-table-storage-scale-targets"></a>Obiettivi di scalabilità di Archiviazione tabelle di Azure

[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="see-also"></a>Vedere anche

- [Dettagli prezzi di archiviazione](https://azure.microsoft.com/pricing/details/storage/)
- [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../../azure-subscription-service-limits.md)
- [Replica di Archiviazione di Azure](../storage-redundancy.md)
- [Elenco di controllo di prestazioni e scalabilità per Archiviazione di Microsoft Azure](../storage-performance-checklist.md)
