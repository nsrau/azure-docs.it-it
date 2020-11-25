---
title: Modificare le prestazioni di Azure Managed disks usando il portale di Azure
description: Informazioni su come modificare i livelli di prestazioni per i dischi gestiti nuovi ed esistenti usando il portale di Azure.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 11/19/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: a868d5d236cf4c5a8d29f15490909c2f1a53546f
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2020
ms.locfileid: "96016584"
---
# <a name="change-your-performance-tier-using-the-azure-portal"></a>Modificare il livello di prestazioni usando il portale di Azure

[!INCLUDE [virtual-machines-disks-performance-tiers-intro](../../includes/virtual-machines-disks-performance-tiers-intro.md)]

## <a name="restrictions"></a>Restrizioni

[!INCLUDE [virtual-machines-disks-performance-tiers-restrictions](../../includes/virtual-machines-disks-performance-tiers-restrictions.md)]

## <a name="getting-started"></a>Introduzione

### <a name="new-disks"></a>Nuovi dischi

La procedura seguente illustra come modificare il livello di prestazioni del disco quando si crea il disco per la prima volta:

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Passare alla macchina virtuale per la quale si vuole creare un nuovo disco.
1. Quando si seleziona il nuovo disco, scegliere innanzitutto le dimensioni del disco necessario.
1. Dopo aver selezionato una dimensione, selezionare un livello di prestazioni diverso per modificarne le prestazioni.
1. Selezionare **OK** per creare il disco.

:::image type="content" source="media/disks-performance-tiers-portal/new-disk-change-performance-tier.png" alt-text="Screenshot del pannello di creazione del disco, viene evidenziato un disco e viene evidenziato l'elenco a discesa del livello di prestazioni." lightbox="media/disks-performance-tiers-portal/performance-tier-settings.png":::


## <a name="existing-disks"></a>Dischi esistenti

La procedura seguente illustra come modificare il livello di prestazioni di un disco esistente:

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Passare alla macchina virtuale contenente il disco che si vuole modificare.
1. Deallocare la macchina virtuale o scollegare il disco.
1. Selezionare il disco
1. Selezionare **dimensioni e prestazioni**.
1. Nell'elenco a discesa **livello di prestazioni** selezionare un livello diverso rispetto alla baseline corrente del disco.
1. Selezionare **Ridimensiona**.

:::image type="content" source="media/disks-performance-tiers-portal/change-tier-existing-disk.png" alt-text="Screenshot del pannello dimensioni + prestazioni. il livello di prestazioni è evidenziato." lightbox="media/disks-performance-tiers-portal/performance-tier-settings.png":::

## <a name="next-steps"></a>Passaggi successivi

Se è necessario ridimensionare un disco per sfruttare i livelli di prestazioni più elevati, vedere questi articoli:

- [Espandere dischi rigidi virtuali in una macchina virtuale Linux con l'interfaccia della riga di comando di Azure](linux/expand-disks.md)
- [Espandere un disco gestito collegato a una macchina virtuale Windows](windows/expand-os-disk.md)
