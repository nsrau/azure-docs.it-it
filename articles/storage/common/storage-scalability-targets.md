---
title: Obiettivi di ridimensionamento e prestazioni di Archiviazione di Azure | Microsoft Docs
description: "Scopri gli obiettivi di scalabilità e prestazioni di Archiviazione di Azure, incluse la capacità, la velocità di richiesta e la larghezza di banda in entrata e in uscita sia per gli account di archiviazione standard che premium. Comprendere obiettivi di prestazioni per le partizioni all'interno di ciascun servizio Archiviazione di Azure."
services: storage
documentationcenter: na
author: tamram
manager: timlt
editor: tysonn
ms.assetid: be721bd3-159f-40a1-88c1-96418537fe75
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 07/12/2017
ms.author: tamram
ms.openlocfilehash: 805b0eee46846345ee1f33faf0c28393c3e8ebb1
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/02/2017
---
# <a name="azure-storage-scalability-and-performance-targets"></a>Obiettivi di scalabilità e prestazioni per Archiviazione di Azure
## <a name="overview"></a>Panoramica
In questo argomento vengono descritti gli obiettivi di scalabilità e prestazioni per Archiviazione di Microsoft Azure. Per un riepilogo degli altri limiti di Azure, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../../azure-subscription-service-limits.md).

> [!NOTE]
> Tutti gli account di archiviazione vengono eseguiti sulla nuova topologia di rete flat e supportano gli obiettivi di scalabilità e prestazioni descritti di seguito, indipendentemente dal momento in cui sono stati creati. Per ulteriori informazioni sull'architettura di rete flat di Archiviazione di Azure e sulla scalabilità, vedere [Archiviazione di Microsoft Azure: servizio di archiviazione cloud a elevata disponibilità con coerenza assoluta](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).
> 
> [!IMPORTANT]
> Gli obiettivi di scalabilità e prestazioni elencati di seguito sono di fascia alta, ma possono essere conseguiti. In tutti i casi, la velocità e la larghezza di banda richieste e ottenute dall’account di archiviazione dipendono dalla dimensione degli oggetti archiviati, dai modelli di accesso utilizzati e dal tipo di carico di lavoro eseguito dall’applicazione. Assicurarsi di eseguire il test del servizio per determinare se le prestazioni soddisfano i requisiti. Se possibile, evitare picchi improvvisi nella frequenza di traffico e assicurarsi che questo sia ben distribuito tra le partizioni.
> 
> Quando l'applicazione raggiunge il limite in termini di carico di lavoro che può essere gestito da una partizione, Archiviazione di Azure inizierà a restituire il codice di errore 503 (Server occupato) o 500 (Timeout operazione). In questo caso, l'applicazione deve utilizzare un criterio di backoff esponenziale per i nuovi tentativi. Il backoff esponenziale consente di ridurre il carico sulla partizione e di uniformare i picchi di traffico verso tale partizione.
> 
> 

Se le esigenze dell'applicazione superano gli obiettivi di scalabilità di un singolo account di archiviazione, è possibile compilare l'applicazione in modo che sia possibile usare più account di archiviazione e partizionare i dati tra gli account. Per informazioni sui prezzi in base al volume, vedere la pagina relativa ai [prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/) .

## <a name="scalability-targets-for-a-storage-account"></a>Obiettivi di scalabilità per un account di archiviazione
[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="azure-blob-storage-scale-targets"></a>Obiettivi di scalabilità dell'archiviazione BLOB di Azure
[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

## <a name="azure-files-scale-targets"></a>Obiettivi di scalabilità di File di Azure
Per altre informazioni sugli obiettivi di scalabilità e prestazioni per File di Azure e Sincronizzazione file di Azure, vedere [Obiettivi di scalabilità e prestazioni di File di Azure](../files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="azure-file-sync-scale-targets"></a>Obiettivi di scalabilità di Sincronizzazione file di Azure
[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="azure-queue-storage-scale-targets"></a>Obiettivi di scalabilità di Archiviazione code di Azure
[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

## <a name="azure-table-storage-scale-targets"></a>Obiettivi di scalabilità di Archiviazione tabelle di Azure
[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
## <a name="scalability-targets-for-virtual-machine-disks"></a>Obiettivi di scalabilità per i dischi della macchina virtuale
[!INCLUDE [azure-storage-limits-vm-disks](../../../includes/azure-storage-limits-vm-disks.md)]

Per altri dettagli, vedere [Dimensioni per le macchine virtuali Windows](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) o [Dimensioni per le macchine virtuali Linux](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="managed-virtual-machine-disks"></a>Dischi delle macchine virtuali gestiti

[!INCLUDE [azure-storage-limits-vm-disks-managed](../../../includes/azure-storage-limits-vm-disks-managed.md)]

## <a name="unmanaged-virtual-machine-disks"></a>Dischi delle macchine virtuali non gestiti
[!INCLUDE [azure-storage-limits-vm-disks-standard](../../../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../../../includes/azure-storage-limits-vm-disks-premium.md)]

## <a name="see-also"></a>Vedere anche
* [Dettagli prezzi di archiviazione](https://azure.microsoft.com/pricing/details/storage/)
* [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../../azure-subscription-service-limits.md)
* [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](../../virtual-machines/windows/premium-storage.md)
* [Replica di Archiviazione di Azure](../storage-redundancy.md)
* [Elenco di controllo di prestazioni e scalabilità per Archiviazione di Microsoft Azure](../storage-performance-checklist.md)
* [Archiviazione di Microsoft Azure: un servizio di archiviazione cloud a elevata disponibilità con coerenza assoluta](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

