---
title: Collegare un disco dati a una macchina virtuale Linux | Microsoft Docs
description: Come collegare un disco dati nuovo o esistente a una macchina virtuale Linux nel portale di Azure tramite il modello di distribuzione di Resource Manager.
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 5e1c6212-976c-4962-a297-177942f90907
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 743f6abb871bdc4f65f302f0c1efdc4603c52626


---
# <a name="how-to-attach-a-data-disk-to-a-linux-vm-in-the-azure-portal"></a>Come collegare un disco dati a una macchina virtuale Linux nel portale di Azure
In questo articolo viene illustrato come collegare dischi nuovi o esistenti a una macchina virtuale Linux tramite il portale di Azure. È possibile anche [collegare un disco dati a una macchina virtuale Windows nel portale di Azure](virtual-machines-windows-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Prima di procedere, rivedere i suggerimenti seguenti:

* La dimensione della macchina virtuale controlla il numero di dischi dati che è possibile collegare. Per informazioni dettagliate, vedere [Dimensioni delle macchine virtuali](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Per utilizzare l'archiviazione Premium, è necessario utilizzare una macchina virtuale della serie DS o serie GS. È possibile utilizzare dischi dagli account di archiviazione sia Premium che Standard con queste macchine virtuali. L’archiviazione Premium è disponibile solo in determinate aree geografiche. Per ulteriori informazioni, vedere [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](../storage/storage-premium-storage.md).
* Dischi collegati a macchine virtuali sono effettivamente file con estensione .vhd in un account di archiviazione di Azure. Per informazioni dettagliate, vedere [Informazioni sui dischi e sui dischi rigidi virtuali per le macchine virtuali](virtual-machines-linux-about-disks-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Per un nuovo disco, non è necessario crearlo prima perché Azure lo crea quando lo si collega.
* Per un disco esistente, il file .vhd deve essere disponibile in un account di archiviazione di Azure. È possibile usare un .vhd già esistente se non è collegato a un'altra macchina virtuale o caricare il proprio file .vhd nell'account di archiviazione.

[!INCLUDE [virtual-machines-common-attach-disk-portal](../../includes/virtual-machines-common-attach-disk-portal.md)]

## <a name="next-steps"></a>Passaggi successivi
Dopo aver aggiunto il disco, è necessario prepararlo per l'uso. Nel sistema operativo della macchina virtuale vedere la sezione relativa alla Procedura: Inizializzare un nuovo disco dati in Linux, in questo [articolo](virtual-machines-linux-classic-attach-disk.md#initialize-a-new-data-disk-in-linux).



<!--HONumber=Nov16_HO3-->


