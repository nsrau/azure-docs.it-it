---
title: Obiettivi di scalabilità per gli account di archiviazione BLOB di pagine Premium
titleSuffix: Azure Storage
description: Un account di archiviazione BLOB di pagine con prestazioni Premium è ottimizzato per le operazioni di lettura/scrittura. Questo tipo di account di archiviazione esegue il backup di un disco non gestito per una macchina virtuale di Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 1542204a2ac779a711407ab1a36c6f8ab9669510
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483531"
---
# <a name="scalability-and-performance-targets-for-premium-page-blob-storage-accounts"></a>Obiettivi di scalabilità e prestazioni per gli account di archiviazione BLOB di pagine Premium

[!INCLUDE [storage-scalability-intro-include](../../../includes/storage-scalability-intro-include.md)]

## <a name="scale-targets-for-premium-page-blob-accounts"></a>Ridimensionare gli obiettivi per gli account BLOB di pagine Premium

Un account di archiviazione BLOB di pagine con prestazioni Premium è ottimizzato per le operazioni di lettura/scrittura. Questo tipo di account di archiviazione esegue il backup di un disco non gestito per una macchina virtuale di Azure.

> [!NOTE]
> Se possibile, Microsoft consiglia di usare Managed disks con macchine virtuali di Azure (VM). Per altre informazioni su Managed disks, vedere [archiviazione su disco di Azure panoramica per le macchine virtuali Windows](../../virtual-machines/windows/managed-disks-overview.md).

Gli account di archiviazione BLOB di pagine Premium hanno i seguenti obiettivi di scalabilità:

| Capacità account totale                            | Larghezza di banda totale per un account di archiviazione con ridondanza locale                     |
| ------------------------------------------------- | --------------------------------------------------------------------------- |
| Capacità disco: 35 TB <br>Capacità snapshot: 10 TB | Fino a 50 gigabit al secondo per dati in ingresso<sup>1</sup> e in uscita<sup>2</sup> |

<sup>1</sup> Tutti i dati (richieste) inviati a un account di archiviazione

<sup>2</sup> Tutti i dati (risposte) ricevuti da un account di archiviazione

Un account BLOB di pagine Premium è un account per utilizzo generico configurato per le prestazioni Premium. Sono consigliati gli account di archiviazione per utilizzo generico V2.

Se si usano account di archiviazione BLOB di pagine Premium per i dischi non gestiti e l'applicazione supera gli obiettivi di scalabilità di un singolo account di archiviazione, Microsoft consiglia di eseguire la migrazione a Managed Disks. Per altre informazioni su Managed disks, vedere [archiviazione su disco di Azure panoramica per le macchine virtuali Windows](../../virtual-machines/windows/managed-disks-overview.md) o [archiviazione su disco di Azure panoramica per le macchine virtuali Linux](../../virtual-machines/linux/managed-disks-overview.md).

Se non è possibile eseguire la migrazione a Managed disks, compilare l'applicazione per usare più account di archiviazione e partizionare i dati tra gli account di archiviazione. Ad esempio, se si desidera collegare dischi da 51 TB tra più VM, distribuirli in due account di archiviazione. Il limite per un account di Archiviazione Premium singolo è 35 TB. Assicurarsi che un singolo account di archiviazione con prestazioni Premium non disponga mai di più di 35 TB di dischi sottoposti a provisioning.

## <a name="see-also"></a>Vedi anche

- [Obiettivi di scalabilità e prestazioni per gli account di archiviazione standard](../common/scalability-targets-standard-account.md)
- [Obiettivi di scalabilità per gli account di archiviazione BLOB in blocchi Premium](../blobs/scalability-targets-premium-block-blobs.md)
