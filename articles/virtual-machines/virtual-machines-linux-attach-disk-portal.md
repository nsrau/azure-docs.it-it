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
ms.date: 03/07/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: cc14f7747c4a24acea434f62b7615d10819bd619
ms.openlocfilehash: 31d7f4620420839ade1ca58391fad78e94d4e929
ms.lasthandoff: 02/23/2017


---
# <a name="how-to-attach-a-data-disk-to-a-linux-vm-in-the-azure-portal"></a>Come collegare un disco dati a una macchina virtuale Linux nel portale di Azure
In questo articolo viene illustrato come collegare dischi nuovi o esistenti a una macchina virtuale Linux tramite il portale di Azure. È possibile anche [collegare un disco dati a una macchina virtuale Windows nel portale di Azure](virtual-machines-windows-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). È possibile scegliere di usare Azure Managed Disks o dischi non gestiti. La funzionalità Managed Disks viene gestita dalla piattaforma Azure e non richiede alcuna pianificazione o alcuna posizione per l'archiviazione. I dischi non gestiti richiedono un account di archiviazione e presentano alcune [quote e limiti applicati](../azure-subscription-service-limits.md#storage-limits). Per altre informazioni su Azure Managed Disks, vedere [Azure Managed Disks overview](../storage/storage-managed-disks-overview.md) (Panoramica di Azure Managed Disks).

Prima di collegare i dischi alla macchina virtuale, leggere i seguenti suggerimenti:

* La dimensione della macchina virtuale controlla il numero di dischi dati che è possibile collegare. Per informazioni dettagliate, vedere [Dimensioni delle macchine virtuali](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Per usare l'archiviazione Premium, è necessaria una macchina virtuale della serie DS o GS. Con queste macchine virtuali, è possibile usare dischi Premium e Standard. L’archiviazione Premium è disponibile solo in determinate aree geografiche. Per ulteriori informazioni, vedere [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](../storage/storage-premium-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* I dischi collegati a macchine virtuali sono effettivamente file con estensione VHD archiviati in Azure. Per informazioni dettagliate, vedere [Informazioni sui dischi e sui dischi rigidi virtuali per le macchine virtuali](../storage/storage-about-disks-and-vhds-linux.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="find-the-virtual-machine"></a>Trovare la macchina virtuale
1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Scegliere **Macchine virtuali**dal menu Hub.
3. Selezionare la macchina virtuale dall'elenco.
4. Nel pannello Macchine virtuali di **Essentials** fare clic su **Dischi**.
   
    ![Aprire le impostazioni del disco](./media/virtual-machines-linux-attach-disk-portal/find-disk-settings.png)

Continuare seguendo le istruzioni per il collegamento di un [disco gestito](#use-azure-managed-disks) o di un [disco non gestito](#use-unmanaged-disks).

## <a name="use-azure-managed-disks"></a>Usare Azure Managed Disks

### <a name="attach-a-new-disk"></a>Collegare un nuovo disco

1. Nel pannello **Dischi** fare clic su **+ Add data disk** (+ Aggiungi disco dati).
2. Fare clic sul menu a discesa **Nome** e selezionare **Crea disco**:

    ![Creare un disco gestito Azure](./media/virtual-machines-linux-attach-disk-portal/create-new-md.png)

3. Immettere un nome per il disco gestito. Esaminare le impostazioni predefinite, aggiornare se necessario e quindi fare clic su **Crea**.
   
   ![Esaminare le impostazioni del disco](./media/virtual-machines-linux-attach-disk-portal/create-new-md-settings.png)

4. Fare clic su **Salva** per creare il disco gestito e aggiornare la configurazione della macchina virtuale:

   ![Salvare il nuovo disco gestito Azure](./media/virtual-machines-linux-attach-disk-portal/confirm-create-new-md.png)

5. Dopo che Azure crea il disco e lo collega alla macchina virtuale, il nuovo disco viene elencato nella sezione Impostazioni disco della macchina virtuale in **Dischi dati**. Dal momento che i dischi gestiti sono una risorsa di livello superiore, il disco viene visualizzato nella directory principale del gruppo di risorse:

   ![Disco gestito Azure nel gruppo di risorse](./media/virtual-machines-linux-attach-disk-portal/view-md-resource-group.png)

### <a name="attach-an-existing-disk"></a>Collegare un disco esistente
1. Nel pannello **Dischi** fare clic su **+ Add data disk** (+ Aggiungi disco dati).
2. Fare clic sul menu a discesa **Nome** per visualizzare un elenco di dischi gestiti esistenti a cui è possibile accedere con la sottoscrizione di Azure. Selezionare il disco gestito da collegare:

   ![Collegare il disco gestito di Azure](./media/virtual-machines-linux-attach-disk-portal/select-existing-md.png)

3. Fare clic su **Salva** per collegare il disco gestito esistente e aggiornare la configurazione della macchina virtuale:
   
   ![Salvare gli aggiornamenti del disco gestito Azure](./media/virtual-machines-linux-attach-disk-portal/confirm-attach-existing-md.png)

4. Dopo che Azure collega il disco alla macchina virtuale, esso viene elencato nella sezione Impostazioni disco della macchina virtuale in **Dischi dei dati**.

## <a name="use-unmanaged-disks"></a>Usare i dischi non gestiti

### <a name="attach-a-new-disk"></a>Collegare un nuovo disco

1. Nel pannello **Dischi** fare clic su **+ Add data disk** (+ Aggiungi disco dati).
2. Esaminare le impostazioni predefinite, aggiornare se necessario e quindi fare clic su **OK**.
   
   ![Esaminare le impostazioni del disco](./media/virtual-machines-linux-attach-disk-portal/attach-new.png)
3. Dopo che Azure crea il disco e lo collega alla macchina virtuale, il nuovo disco viene elencato nella sezione Impostazioni disco della macchina virtuale in **Dischi dati**.

### <a name="attach-an-existing-disk"></a>Collegare un disco esistente
1. Nel pannello **Dischi** fare clic su **+ Add data disk** (+ Aggiungi disco dati).
2. In **Collega un disco esistente** fare clic su **File VHD**.
   
   ![Collegare un disco esistente](./media/virtual-machines-linux-attach-disk-portal/attach-existing.png)
3. In **Account di archiviazione**, selezionare l'account e un contenitore che contiene il file con estensione vhd.
   
   ![Individuare il percorso di un VHD](./media/virtual-machines-linux-attach-disk-portal/find-storage-container.png)
4. Selezionare il file con estensione vhd.
5. In **Collega un disco esistente** il file appena selezionato è elencato in **File VHD**. Fare clic su **OK**.
6. Dopo che Azure collega il disco alla macchina virtuale, esso viene elencato nella sezione Impostazioni disco della macchina virtuale in **Dischi dei dati**.


## <a name="next-steps"></a>Passaggi successivi
Dopo aver aggiunto il disco, è necessario prepararlo per l'uso. Per ulteriori informazioni, vedere [Procedura: inizializzare un nuovo disco dati in Linux](virtual-machines-linux-classic-attach-disk.md#initialize-a-new-data-disk-in-linux).

