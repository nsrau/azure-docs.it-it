---
title: 'Archiviazione Premium di Azure: progettazione per le prestazioni in macchine virtuali Windows '
description: Progettare applicazioni a prestazioni elevate con l'Archiviazione Premium di Azure. Archiviazione Premium offre prestazioni elevate e supporto per dischi a bassa latenza per carichi di lavoro con I/O intensivo in esecuzione su Macchine virtuali di Azure.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 584c69f4079eeebf04a0e40021bfc843b40e8b1b
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74032962"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Archiviazione Premium di Azure: progettata per prestazioni elevate
[!INCLUDE [virtual-machines-common-premium-storage-introduction](../../../includes/virtual-machines-common-premium-storage-introduction.md)]

> [!NOTE]
> In alcuni casi, quello che sembra essere un problema di prestazioni del disco è in realtà un collo di bottiglia a livello di rete. In queste situazioni, è consigliabile ottimizzare le [prestazioni di rete](../../virtual-network/virtual-network-optimize-network-bandwidth.md).
>
> Se si sta cercando di eseguire il benchmarking del disco, vedere l'articolo sul [benchmarking di un disco](disks-benchmarks.md).
>
> Se la macchina virtuale supporta la rete accelerata, è necessario assicurarsi che sia abilitata. Se non è abilitata, è possibile abilitarla nelle macchine virtuali già distribuite sia in [Windows](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) che in [Linux](../../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

Prima di iniziare, se non si ha familiarità con archiviazione Premium, leggere prima di tutto gli obiettivi [selezionare un tipo di disco di Azure per macchine virtuali IaaS](disks-types.md) e [obiettivi di scalabilità e prestazioni per archiviazione di Azure per gli account di archiviazione](../../storage/common/storage-scalability-targets.md).

[!INCLUDE [virtual-machines-common-premium-storage-performance.md](../../../includes/virtual-machines-common-premium-storage-performance.md)]

Se si sta cercando di eseguire il benchmarking del disco, vedere l'articolo sul [benchmarking di un disco](disks-benchmarks.md).

Altre informazioni sui tipi di dischi disponibili: [selezionare un tipo di disco](disks-types.md)  

Per gli utenti di SQL Server sono disponibili articoli sulle procedure consigliate per le prestazioni per SQL Server:

* [Procedure consigliate per le prestazioni per SQL Server in Macchine virtuali di Azure](sql/virtual-machines-windows-sql-performance.md)
* [L'Archiviazione Premium di Azure offre le prestazioni più elevate per SQL Server in VM di Azure](https://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx)