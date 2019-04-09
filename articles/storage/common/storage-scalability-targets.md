---
title: Scalabilità e prestazioni obiettivi di archiviazione Azure - account di archiviazione
description: Informazioni sugli obiettivi di scalabilità e prestazioni, tra cui la capacità e la frequenza delle richieste in ingresso e in uscita della larghezza di banda, per gli account di archiviazione di Azure.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: 96322c730300e360ed03f4b623db2a7f18825f55
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59267702"
---
# <a name="azure-storage-scalability-and-performance-targets-for-storage-accounts"></a>Obiettivi archiviazione di Azure la scalabilità e prestazioni per gli account di archiviazione

Questo articolo illustra nel dettaglio gli obiettivi di scalabilità e prestazioni per gli account di archiviazione di Azure. Gli obiettivi di scalabilità e prestazioni elencati di seguito sono di fascia alta, ma possono essere conseguiti. In tutti i casi, la velocità e la larghezza di banda richieste e ottenute dall’account di archiviazione dipendono dalla dimensione degli oggetti archiviati, dai modelli di accesso utilizzati e dal tipo di carico di lavoro eseguito dall’applicazione.

Assicurarsi di eseguire il test del servizio per determinare se le prestazioni soddisfano i requisiti. Se possibile, evitare picchi improvvisi nella frequenza di traffico e assicurarsi che questo sia ben distribuito tra le partizioni.

Quando l'applicazione raggiunge il limite in termini di carico di lavoro che può essere gestito da una partizione, Archiviazione di Azure inizierà a restituire il codice di errore 503 (Server occupato) o 500 (Timeout operazione). Se si verificano errori 503, provare a modificare l'applicazione in modo da usare un criterio di backoff esponenziale per i nuovi tentativi. Il backoff esponenziale consente di ridurre il carico sulla partizione e di uniformare i picchi di traffico verso tale partizione.

## <a name="storage-account-scale-limits"></a>Limiti di scalabilità dell'account di archiviazione

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

## <a name="premium-performance-storage-account-scale-limits"></a>Limiti di scalabilità di account di archiviazione di prestazioni di Premium

[!INCLUDE [azure-premium-limits](../../../includes/azure-storage-limits-premium.md)]

## <a name="storage-resource-provider-scale-limits"></a>Limiti di scalabilità del provider delle risorse di archiviazione

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="azure-blob-storage-scale-targets"></a>Obiettivi di scalabilità dell'archiviazione BLOB di Azure

[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

## <a name="azure-files-scale-targets"></a>Obiettivi di scalabilità di File di Azure

Per altre informazioni sugli obiettivi di scalabilità e prestazioni per File di Azure e Sincronizzazione file di Azure, vedere [Obiettivi di scalabilità e prestazioni di File di Azure](../files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="premium-files-scale-targets"></a>File Premium obiettivi di scalabilità

Esistono tre categorie di limitazioni da considerare per i file premium: gli account di archiviazione, condivisioni e file.

Ad esempio:  Una singola condivisione può raggiungere 100.000 IOPS e un singolo file possono aumentare fino a 5.000 IOPS. Quindi, ad esempio, se si dispone di tre file in una condivisione, il numero di IOPs max è possibile ottenere da tale condivisione è 15.000.

#### <a name="premium-file-share-limits"></a>Limiti di condivisione file Premium

> [!IMPORTANT]
> Limiti dell'account di archiviazione si applicano a tutte le condivisioni. Scalabilità per il numero massimo di account di archiviazione solo è realizzabile se è presente solo una condivisione per ogni account di archiviazione.

|Area  |Destinazione  |
|---------|---------|
|Dimensioni minime                        |100 GiB      |
|Dimensioni massime                        |100 TiB      |
|Aumentare o ridurre la dimensione minima    |1 GiB      |
|Operazioni di I/O al secondo di base    |Numero di 1 IOPS per GiB fino a 100.000|
|IOPS bursting    |3 x IOPS per GiB fino a 100.000|
|Larghezza di banda minima                     |100        |
|Larghezza di banda |0,1 MB/s per GiB fino a 5 GiB/s     |
|Numero massimo di snapshot        |200       |

#### <a name="premium-file-limits"></a>Limiti dei file Premium

|Area  |Destinazione  |
|---------|---------|
|Dimensione                  |1 TiB         |
|IOPS Max per ogni file     |5.000         |
|Handle simultanei    |2.000         |

### <a name="azure-file-sync-scale-targets"></a>Obiettivi di scalabilità di Sincronizzazione file di Azure

Sincronizzazione file di Azure è stato progettato per supportare un utilizzo senza limiti, cosa però non sempre possibile. La tabella seguente indica i limiti dei test Microsoft e anche le destinazioni con limiti rigidi:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="azure-queue-storage-scale-targets"></a>Obiettivi di scalabilità di Archiviazione code di Azure

[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

## <a name="azure-table-storage-scale-targets"></a>Obiettivi di scalabilità di Archiviazione tabelle di Azure

[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="see-also"></a>Vedere anche

- [Dettagli prezzi di archiviazione](https://azure.microsoft.com/pricing/details/storage/)
- [Sottoscrizione di Azure e limiti, quote e vincoli](../../azure-subscription-service-limits.md)
- [Replica di archiviazione di Azure](../storage-redundancy.md)
- [Elenco di controllo di prestazioni e scalabilità per Archiviazione di Microsoft Azure](../storage-performance-checklist.md)