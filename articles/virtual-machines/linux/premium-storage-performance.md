---
title: 'Archiviazione Di Azure Premium: progettazione per le prestazioni in macchine virtuali Linux Documenti Microsoft'
description: Progettare applicazioni ad alte prestazioni usando i dischi gestiti SSD premium di Azure.Design high-performance applications using Azure premium SSD managed disks. Archiviazione Premium offre prestazioni elevate e supporto per dischi a bassa latenza per carichi di lavoro con I/O intensivo in esecuzione su Macchine virtuali di Azure.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 9940ee4cfce9721ac65f2b3cf1469e180adfa098
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267144"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Archiviazione Premium di Azure: progettata per prestazioni elevate
[!INCLUDE [virtual-machines-common-premium-storage-introduction](../../../includes/virtual-machines-common-premium-storage-introduction.md)]

> [!NOTE]
> In alcuni casi, quello che sembra essere un problema di prestazioni del disco è in realtà un collo di bottiglia a livello di rete. In queste situazioni, è consigliabile ottimizzare le [prestazioni di rete](../../virtual-network/virtual-network-optimize-network-bandwidth.md).
>
> Se stai cercando di confrontare il tuo disco, consulta il nostro articolo sul [benchmarking di un disco](disks-benchmarks.md).
>
> Se la macchina virtuale supporta la rete accelerata, è necessario assicurarsi che sia abilitata. Se non è abilitata, è possibile abilitarla nelle macchine virtuali già distribuite sia in [Windows](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) che in [Linux](../../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

Prima di iniziare, se non si ha familiarità con Archiviazione Premium, leggere [selezionare Selezionare un tipo di disco di Azure per le macchine virtuali IaaS](disks-types.md) e gli obiettivi di scalabilità per gli account di [archiviazione BLOB di pagine premium.](../../storage/blobs/scalability-targets-premium-page-blobs.md)


[!INCLUDE [virtual-machines-common-premium-storage-performance.md](../../../includes/virtual-machines-common-premium-storage-performance.md)]

Se stai cercando di confrontare il tuo disco, consulta il nostro articolo sul [benchmarking di un disco](disks-benchmarks.md).

Ulteriori informazioni sui tipi di disco disponibili: [Selezionare un tipo di disco](disks-types.md)  

Per gli utenti di SQL Server sono disponibili articoli sulle procedure consigliate per le prestazioni per SQL Server:

* [Procedure consigliate per le prestazioni per SQL Server in Macchine virtuali di AzurePerformance Best Practices for SQL Server in Azure Virtual Machines](../windows/sql/virtual-machines-windows-sql-performance.md)
* [L'Archiviazione Premium di Azure offre le prestazioni più elevate per SQL Server in VM di Azure](https://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx)