---
title: Eseguire la migrazione di macchine virtuali di Azure a Managed Disks | Microsoft Docs
description: Eseguire la migrazione di macchine virtuali di Azure create usando dischi non gestiti negli account di archiviazione per l'uso in Managed Disks.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 7487d53195b45664b094ccc3a8418bd0c700e052
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68693488"
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Eseguire la migrazione di macchine virtuali di Azure a Managed Disks in Azure

Il servizio Azure Managed Disks semplifica la gestione dell'archiviazione, eliminando la necessità di gestire separatamente gli account.  È anche possibile eseguire la migrazione delle macchine virtuali di Azure esistenti a Managed Disks per trarre vantaggio dalla maggiore affidabilità delle macchine virtuali in un set di disponibilità. Garantisce che i dischi di macchine virtuali diverse in un set di disponibilità siano sufficientemente isolati tra loro per evitare un singolo punto di errore. Colloca automaticamente i dischi di macchine virtuali differenti in un set di disponibilità in diverse unità di scala di archiviazione (indicatori) per limitare l'impatto degli errori di una singola unità causati da anomalie hardware e software.
In base alle esigenze specifiche, è possibile scegliere tra quattro tipi di opzioni di archiviazione. Per informazioni sui tipi di dischi disponibili, vedere l'articolo [Selezionare un tipo di disco](disks-types.md)

## <a name="migration-scenarios"></a>Scenari di migrazione

È possibile eseguire la migrazione a Managed Disks negli scenari seguenti:

|Scenario  |Articolo  |
|---------|---------|
|Conversione di macchine virtuali autonome e incluse in un set di disponibilità in dischi gestiti     |[Convertire le macchine virtuali per usare i dischi gestiti](convert-unmanaged-to-managed-disks.md)         |
|Convertire una singola macchina virtuale dal modello di distribuzione classica a Gestione risorse nei dischi gestiti     |[Creare una macchina virtuale da un disco rigido virtuale classico](create-vm-specialized-portal.md)         |
|Convertire tutte le macchine virtuali in un vNet dal modello di distribuzione classica a quello Gestione risorse sui dischi gestiti     |[Eseguire la migrazione di risorse IaaS dal modello di distribuzione classico a Resource Manager](migration-classic-resource-manager-ps.md) e quindi [convertire una macchina virtuale da dischi non gestiti in dischi gestiti](convert-unmanaged-to-managed-disks.md)         |
|Aggiornare le macchine virtuali con dischi non gestiti standard alle macchine virtuali con dischi Premium gestiti     | Convertire prima [di tutto una macchina virtuale Windows da dischi non gestiti a Managed disks](convert-unmanaged-to-managed-disks.md). [Aggiornare quindi il tipo di archiviazione di un disco gestito](convert-disk-storage.md).         |

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Managed Disks](managed-disks-overview.md)
- Esaminare i [prezzi per Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/).
