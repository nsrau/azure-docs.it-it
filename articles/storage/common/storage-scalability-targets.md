---
title: Obiettivi di scalabilità e prestazioni per Archiviazione di Azure
description: Informazioni sugli obiettivi di scalabilità e prestazioni, incluse la capacità, la frequenza delle richieste e la larghezza di banda in entrata e in uscita per gli account di archiviazione standard di Azure.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 11/08/2018
ms.author: rogarana
ms.component: common
ms.openlocfilehash: 93e09f3ab6780eb9ce7fa29b4554b53d796b6837
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2018
ms.locfileid: "51564954"
---
# <a name="azure-storage-scalability-and-performance-targets-for-standard-storage-accounts"></a>Obiettivi di scalabilità e prestazioni di Archiviazione di Azure per gli account di archiviazione standard

Questo articolo illustra nel dettaglio gli obiettivi di scalabilità e prestazioni per gli account di archiviazione standard di Azure. Gli obiettivi di scalabilità e prestazioni elencati di seguito sono di fascia alta, ma possono essere conseguiti. In tutti i casi, la velocità e la larghezza di banda richieste e ottenute dall’account di archiviazione dipendono dalla dimensione degli oggetti archiviati, dai modelli di accesso utilizzati e dal tipo di carico di lavoro eseguito dall’applicazione. 

Assicurarsi di eseguire il test del servizio per determinare se le prestazioni soddisfano i requisiti. Se possibile, evitare picchi improvvisi nella frequenza di traffico e assicurarsi che questo sia ben distribuito tra le partizioni.

Quando l'applicazione raggiunge il limite in termini di carico di lavoro che può essere gestito da una partizione, Archiviazione di Azure inizierà a restituire il codice di errore 503 (Server occupato) o 500 (Timeout operazione). Se si verificano errori 503, provare a modificare l'applicazione in modo da usare un criterio di backoff esponenziale per i nuovi tentativi. Il backoff esponenziale consente di ridurre il carico sulla partizione e di uniformare i picchi di traffico verso tale partizione.

## <a name="standard-storage-account-scale-limits"></a>Limiti di scalabilità dell'account di archiviazione standard
[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

## <a name="storage-resource-provider-scale-limits"></a>Limiti di scalabilità del provider delle risorse di archiviazione 

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="azure-blob-storage-scale-targets"></a>Obiettivi di scalabilità dell'archiviazione BLOB di Azure
[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

## <a name="azure-files-scale-targets"></a>Obiettivi di scalabilità di File di Azure
Per altre informazioni sugli obiettivi di scalabilità e prestazioni per File di Azure e Sincronizzazione file di Azure, vedere [Obiettivi di scalabilità e prestazioni di File di Azure](../files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="azure-file-sync-scale-targets"></a>Obiettivi di scalabilità di Sincronizzazione file di Azure
Sincronizzazione file di Azure è stato progettato per supportare un utilizzo senza limiti, cosa però non sempre possibile. La tabella seguente indica i limiti dei test Microsoft e anche le destinazioni con limiti rigidi:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="azure-queue-storage-scale-targets"></a>Obiettivi di scalabilità di Archiviazione code di Azure
[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

## <a name="azure-table-storage-scale-targets"></a>Obiettivi di scalabilità di Archiviazione tabelle di Azure
[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="see-also"></a>Vedere anche
* [Dettagli prezzi di archiviazione](https://azure.microsoft.com/pricing/details/storage/)
* [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../../azure-subscription-service-limits.md)
* [Replica di Archiviazione di Azure](../storage-redundancy.md)
* [Elenco di controllo di prestazioni e scalabilità per Archiviazione di Microsoft Azure](../storage-performance-checklist.md)

