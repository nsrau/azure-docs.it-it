---
title: Informazione sull'archiviazione su disco non gestita (BLOB di pagine) e gestita per le VM Linux di Microsoft Azure | Microsoft Docs
description: Nozioni di base sull'archiviazione su disco non gestita (BLOB di pagine) e gestita per le VM Linux in Azure.
services: virtual-machines
author: iainfoulds
manager: jeconnoc
ms.service: virtual-machines
ms.workload: storage
ms.tgt_pltfrm: linux
ms.topic: article
ms.date: 11/15/2017
ms.author: iainfou
ms.openlocfilehash: 107e332a0f8c9d5a84a74de685ca458fb29caa8b
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2018
---
# <a name="about-disks-storage-for-azure-linux-vms"></a>Informazioni sull'archiviazione su disco per le VM Linux di Azure
Analogamente a qualsiasi altro computer, le macchine virtuali in Azure utilizzano i dischi come posizioni per archiviare un sistema operativo, le applicazioni e i dati. Tutte le macchine virtuali di Azure dispongono di almeno due dischi: un disco del sistema operativo Linux e un disco temporaneo. Il disco del sistema operativo viene creato da un'immagine e sia il disco del sistema operativo sia l'immagine sono effettivamente dischi rigidi virtuali archiviati in un account di archiviazione di Azure. Anche le macchine virtuali possono disporre di uno o più dischi dati archiviati in dischi rigidi virtuali. 

Questo articolo illustra i diversi usi dei dischi e descrive i diversi tipi di dischi che è possibile creare e usare. Questo articolo è disponibile anche per le [macchine virtuali Windows](../windows/about-disks-and-vhds.md).

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="disks-used-by-vms"></a>Dischi usati dalle VM

Esaminiamo come i dischi vengono usati dalle VM.

## <a name="operating-system-disk"></a>Disco del sistema operativo
Tutte le macchine virtuali dispongono di un disco del sistema operativo collegato. Per impostazione predefinita, è registrato come unità SATA con etichetta /dev/sda. Questo disco ha una capacità massima di 2048 gigabyte (GB). 

## <a name="temporary-disk"></a>Disco temporaneo
Ogni VM contiene un disco temporaneo. Il disco temporaneo offre archiviazione a breve termine per applicazioni e processi ed è destinato solo all'archiviazione di dati come file di paging o di scambio. I dati presenti nel disco temporaneo potrebbero andare persi durante un [evento di manutenzione](../windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime) o la [ridistribuzione di una VM](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Durante un riavvio standard della VM, i dati nell'unità temporanea vengono mantenuti.

Nelle macchine virtuali Linux il disco è in genere **/dev/sdb** e viene formattato e montato in **/mnt** dall'agente Linux di Azure. Le dimensioni del disco temporaneo variano in base alle dimensioni della macchina virtuale. Per altre informazioni, vedere [Dimensioni per le macchine virtuali Linux](../windows/sizes.md).

Per altre informazioni sull'uso del disco temporaneo in Azure, vedere l'articolo relativo alle [unità temporanee nelle macchine virtuali di Microsoft Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="data-disk"></a>Disco dati
Un disco dati è un disco rigido virtuale collegato a una macchina virtuale per archiviare i dati delle applicazioni o altri dati che è necessario conservare. I dischi dati vengono registrati come unità SCSI ed etichettati con una lettera di propria scelta. Ogni disco dati ha una capacità massima di 4095 GB. Le dimensioni della macchina virtuale determinano il numero di dischi dati è possibile collegare e il tipo di archiviazione che è possibile utilizzare per ospitare i dischi.

> [!NOTE]
> Per ulteriori dettagli sulle capacità delle macchine virtuali, vedere [Dimensioni per le macchine virtuali Linux](./sizes.md).
> 

Quando viene creata una macchina virtuale da un'immagine, Azure crea un disco del sistema operativo. Se si utilizza un'immagine che include dischi dati, anche Azure crea dischi dati quando viene creata la macchina virtuale. In caso contrario, aggiungere dischi dati dopo aver creato la macchina virtuale.

È possibile aggiungere dischi dati a una macchina virtuale in qualsiasi momento, **collegando** il disco alla macchina virtuale. È possibile usare un disco rigido virtuale caricato o copiato nell'account di archiviazione o uno creato da Azure. Il collegamento di un disco dati associa il file del disco rigido virtuale alla macchina virtuale, inserendo un "lease" nel disco rigido virtuale in modo che non possa essere eliminato dalla memoria se ancora collegato.

[!INCLUDE [storage-about-vhds-and-disks-windows-and-linux](../../../includes/storage-about-vhds-and-disks-windows-and-linux.md)]

## <a name="troubleshooting"></a>risoluzione dei problemi
[!INCLUDE [virtual-machines-linux-lunzero](../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Passaggi successivi
* [Collegare un disco](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) per aggiungere altro spazio di archiviazione per la VM.
* [Creare uno snapshot](snapshot-copy-managed-disk.md).
* [Eseguire la conversione in dischi gestiti](convert-unmanaged-to-managed-disks.md).

