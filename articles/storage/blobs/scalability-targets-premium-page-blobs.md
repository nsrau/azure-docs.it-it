---
title: Obiettivi di scalabilità per gli account di archiviazione BLOB di pagine premiumScalability targets for premium page blob storage accounts
titleSuffix: Azure Storage
description: Un account di archiviazione BLOB della pagina delle prestazioni premium è ottimizzato per le operazioni di lettura/scrittura. Questo tipo di account di archiviazione esegue il backup di un disco non gestito per una macchina virtuale di Azure.This type of storage account backs an unmanaged disk for an Azure virtual machine.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: e134c69f5d602cb4369e9410e3e2b9d3478b11a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76756250"
---
# <a name="scalability-and-performance-targets-for-premium-page-blob-storage-accounts"></a>Obiettivi di scalabilità e prestazioni per gli account di archiviazione BLOB di pagine premiumScalability and performance targets for premium page blob storage accounts

[!INCLUDE [storage-scalability-intro-include](../../../includes/storage-scalability-intro-include.md)]

## <a name="scale-targets-for-premium-page-blob-accounts"></a>Obiettivi di scalabilità per gli account BLOB di pagine premiumScale targets for premium page blob accounts

Un account di archiviazione BLOB di pagine premium-performance è ottimizzato per le operazioni di lettura/scrittura. Questo tipo di account di archiviazione esegue il backup di un disco non gestito per una macchina virtuale di Azure.This type of storage account backs an unmanaged disk for an Azure virtual machine.

> [!NOTE]
> Microsoft consiglia di usare dischi gestiti con macchine virtuali di Azure (VM) se possibile. Per altre informazioni sui dischi gestiti, vedere Panoramica di Archiviazione dischi di Azure per macchine virtuali Windows.For more information about managed disks, see [Azure Disk Storage overview for Windows VMs](../../virtual-machines/windows/managed-disks-overview.md).

Gli account di archiviazione BLOB delle pagine Premium hanno gli obiettivi di scalabilità seguenti:Premium page blob storage accounts have the following scalability targets:

| Capacità account totale                            | Larghezza di banda totale per un account di archiviazione con ridondanza locale                     |
| ------------------------------------------------- | --------------------------------------------------------------------------- |
| Capacità del disco: 4 TB (singolo disco)/ 35 TB (totale cumulativo di tutti i dischi) <br>Capacità snapshot: 10 TB | Fino a 50 gigabit al secondo per dati in ingresso<sup>1</sup> e in uscita<sup>2</sup> |

<sup>1</sup> Tutti i dati (richieste) inviati a un account di archiviazione

<sup>2</sup> Tutti i dati (risposte) ricevuti da un account di archiviazione

Un account BLOB di pagine premium è un account generico configurato per le prestazioni premium. Sono consigliati gli account di archiviazione generici v2.

Se si usano account di archiviazione BLOB di pagine premium per i dischi non gestiti e l'applicazione supera gli obiettivi di scalabilità di un singolo account di archiviazione, Microsoft consiglia di eseguire la migrazione a dischi gestiti. Per altre informazioni sui dischi gestiti, vedere Panoramica di Archiviazione dischi di Azure per le macchine virtuali Windows o Panoramica di Archiviazione disco di Azure per le macchine virtuali Linux.For more information about managed disks, see [Azure Disk Storage overview for Windows VMs](../../virtual-machines/windows/managed-disks-overview.md) or Azure Disk Storage overview for Linux [VMs](../../virtual-machines/linux/managed-disks-overview.md).

Se non è possibile eseguire la migrazione a dischi gestiti, compilare l'applicazione per usare più account di archiviazione e partizionare i dati tra tali account di archiviazione. Ad esempio, se si desidera collegare dischi da 51 TB tra più VM, distribuirli in due account di archiviazione. Il limite per un account di Archiviazione Premium singolo è 35 TB. Assicurarsi che un singolo account di archiviazione delle prestazioni Premium non disponga mai di più di 35 TB di dischi di cui è stato eseguito il provisioning.

## <a name="see-also"></a>Vedere anche

- [Obiettivi di scalabilità e prestazioni per gli account di archiviazione standard](../common/scalability-targets-standard-account.md)
- [Obiettivi di scalabilità per gli account di archiviazione BLOB con blocchi PremiumScalability targets for premium block blob storage accounts](../blobs/scalability-targets-premium-block-blobs.md)
- [Limiti e quote della sottoscrizione di Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md)
