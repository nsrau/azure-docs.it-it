---
title: 'Archiviazione Premium di Azure: Progettazione per le prestazioni sulle macchine virtuali di Windows | Microsoft Docs'
description: Progettare applicazioni a prestazioni elevate con i Managed Disks SSD Premium di Azure. Archiviazione Premium offre prestazioni elevate e supporto per dischi a bassa latenza per carichi di lavoro con I/O intensivo in esecuzione su Macchine virtuali di Azure.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 06ac680ef48d9602f6e7487eb9ebecee9f3ae080
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84017605"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Archiviazione Premium di Azure: progettata per prestazioni elevate
[!INCLUDE [virtual-machines-common-premium-storage-introduction](../../../includes/virtual-machines-common-premium-storage-introduction.md)]

> [!NOTE]
> In alcuni casi, quello che sembra essere un problema di prestazioni del disco è in realtà un collo di bottiglia a livello di rete. In queste situazioni, è consigliabile ottimizzare le [prestazioni di rete](../../virtual-network/virtual-network-optimize-network-bandwidth.md).
>
> Se si desidera eseguire il benchmarking del disco, vedere l'articolo [Benchmarking di un disco](disks-benchmarks.md).
>
> Se la macchina virtuale supporta la rete accelerata, è necessario assicurarsi che sia abilitata. Se non è abilitata, è possibile abilitarla nelle macchine virtuali già distribuite sia in [Windows](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) che in [Linux](../../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

Prima di iniziare, se non si ha alcuna esperienza con l'Archiviazione Premium, leggere gli articoli relativi alla [selezione di un tipo di disco di Azure per le macchine virtuali IaaS](disks-types.md) e agli [obiettivi di scalabilità per gli account di archiviazione BLOB di pagine Premium](../../storage/blobs/scalability-targets-premium-page-blobs.md).

[!INCLUDE [virtual-machines-common-premium-storage-performance.md](../../../includes/virtual-machines-common-premium-storage-performance.md)]

Se si desidera eseguire il benchmarking del disco, vedere l'articolo [Benchmarking di un disco](disks-benchmarks.md).

Altre informazioni sui tipi di disco disponibili: [Selezionare un tipo di disco](disks-types.md)  

Per gli utenti di SQL Server sono disponibili articoli sulle procedure consigliate per le prestazioni per SQL Server:

* [Procedure consigliate per le prestazioni per SQL Server in Macchine virtuali di Azure](../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md)
* [L'Archiviazione Premium di Azure offre le prestazioni più elevate per SQL Server in VM di Azure](https://cloudblogs.microsoft.com/sqlserver/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm/)
