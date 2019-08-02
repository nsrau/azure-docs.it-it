---
title: 'Archiviazione Premium di Azure: Progettazione per le prestazioni sulle macchine virtuali di Windows | Microsoft Docs'
description: Progettare applicazioni a prestazioni elevate con l'Archiviazione Premium di Azure. Archiviazione Premium offre prestazioni elevate e supporto per dischi a bassa latenza per carichi di lavoro con I/O intensivo in esecuzione su Macchine virtuali di Azure.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 70da3509ce44fe260f8010ccf6eb1d2192ca6e73
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68695491"
---
[!INCLUDE [virtual-machines-common-premium-storage-introduction](../../../includes/virtual-machines-common-premium-storage-introduction.md)]

> [!NOTE]
> In alcuni casi, quello che sembra essere un problema di prestazioni del disco è in realtà un collo di bottiglia a livello di rete. In queste situazioni, è consigliabile ottimizzare le [prestazioni di rete](../../virtual-network/virtual-network-optimize-network-bandwidth.md).
>
> Se si sta cercando di eseguire il benchmarking del disco, vedere l'articolo sul benchmarking di [un disco](disks-benchmarks.md).
>
> Se la macchina virtuale supporta la rete accelerata, è necessario assicurarsi che sia abilitata. Se non è abilitata, è possibile abilitarla nelle macchine virtuali già distribuite sia in [Windows](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) che in [Linux](../../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

Prima di iniziare, se non si ha familiarità con archiviazione Premium, leggere prima di tutto gli obiettivi [selezionare un tipo di disco di Azure per macchine virtuali IaaS](disks-types.md) e [obiettivi di scalabilità e prestazioni per archiviazione di Azure per gli account di archiviazione](../../storage/common/storage-scalability-targets.md).


[!INCLUDE [virtual-machines-common-premium-storage-performance.md](../../../includes/virtual-machines-common-premium-storage-performance.md)]
