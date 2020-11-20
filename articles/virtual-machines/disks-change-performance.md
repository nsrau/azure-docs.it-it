---
title: Livelli di prestazioni per Azure Managed Disks
description: Informazioni sui livelli di prestazioni per Managed Disks.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 11/19/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 28980756ac9e41c9477d687ea9df608b512759e3
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94986784"
---
# <a name="performance-tiers-for-managed-disks"></a>Livelli di prestazioni per Managed Disks

Le prestazioni del disco gestito di Azure vengono impostate quando si crea il disco, sotto forma di livello di prestazioni. Il livello di prestazioni determina i IOPS e la velocità effettiva del disco gestito. Quando si impostano le dimensioni del disco di cui è stato effettuato il provisioning, viene selezionato automaticamente un livello di prestazioni. Il livello di prestazioni può essere modificato in fase di distribuzione o in seguito, senza modificare le dimensioni del disco.

Modificando il livello di prestazioni è possibile preparare e soddisfare una richiesta più elevata senza utilizzare la funzionalità di espansione del disco. Per modificare il livello di prestazioni, può essere più conveniente modificare il livello di prestazioni, anziché utilizzare la funzione di espansione, a seconda del tempo necessario per le prestazioni aggiuntive. Questa soluzione è ideale per gli eventi che richiedono temporaneamente un livello di prestazioni costantemente superiore, ad esempio l'acquisto di festività, il test delle prestazioni o l'esecuzione di un ambiente di training. Per gestire questi eventi, è possibile usare un livello di prestazioni superiore per tutto il tempo necessario. È quindi possibile tornare al livello originale quando non sono più necessarie le prestazioni aggiuntive.

## <a name="how-it-works"></a>Funzionamento

Quando si distribuisce o si effettua il provisioning di un disco per la prima volta, il livello di prestazioni di base per tale disco viene impostato in base alle dimensioni del disco con provisioning È possibile usare un livello di prestazioni superiore rispetto alla baseline originale per soddisfare una domanda più elevata. Quando il livello di prestazioni non è più necessario, è possibile tornare al livello di prestazioni iniziale della linea di base.

La fatturazione viene modificata in fase di modifica del livello di prestazioni. Se, ad esempio, si esegue il provisioning di un disco P10 (128 GiB), il livello di prestazioni di base viene impostato come P10 (500 IOPS e 100 MBps). Ti verrà addebitata la tariffa P10. È possibile aggiornare il livello in modo che corrisponda alle prestazioni di P50 (7.500 IOPS e 250 MBps) senza aumentare le dimensioni del disco. Durante l'aggiornamento, ti verrà addebitata la tariffa di P50. Quando non sono più necessarie le prestazioni più elevate, è possibile tornare al livello P10. Il disco verrà nuovamente fatturato in base alla tariffa P10.

| Dimensioni del disco | Livello di prestazioni di base | Può essere aggiornato a |
|----------------|-----|-------------------------------------|
| 4 GiB | P1 | P2, P3, P4, P6, P10, P15, P20, P30, P40, P50 |
| 8 GiB | P2 | P3, P4, P6, P10, P15, P20, P30, P40, P50 |
| 16 GiB | P3 | P4, P6, P10, P15, P20, P30, P40, P50 | 
| 32 GiB | P4 | P6, P10, P15, P20, P30, P40, P50 |
| 64 GiB | P6 | P10, P15, P20, P30, P40, P50 |
| 128 GiB | P10 | P15, P20, P30, P40, P50 |
| 256 GiB | P15 | P20, P30, P40, P50 |
| 512 GiB | P20 | P30, P40, P50 |
| 1 TiB | P30 | P40, P50 |
| 2 TiB | P40 | P50 |
| 4 TiB | P50 | nessuno |
| 8 TiB | P60 |  P70, P80 |
| 16 TiB | P70 | P80 |
| 32 TiB | P80 | nessuno |

Per informazioni sulla fatturazione, vedere [prezzi dei dischi gestiti](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="restrictions"></a>Restrizioni

[!INCLUDE [virtual-machines-disks-performance-tiers-restrictions](../../includes/virtual-machines-disks-performance-tiers-restrictions.md)]

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come modificare il livello di prestazioni, vedere il [portale](disks-performance-tiers-portal.md) o gli articoli di [PowerShell/CLI](disks-performance-tiers.md) .

