---
title: Collegare un disco dati a una macchina virtuale Linux | Microsoft Docs
description: Usare il portale per collegare il disco dati nuovo o esistente a una macchina virtuale Linux.
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
ms.date: 09/25/2017
ms.author: cynthn
ms.openlocfilehash: 787f729732accd74c212b3be9520af50a2f04261
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-portal-to-attach-a-data-disk-to-a-linux-vm"></a>Usare il portale per collegare un disco dati a una macchina virtuale Linux 
In questo articolo viene illustrato come collegare dischi nuovi o esistenti a una macchina virtuale Linux tramite il portale di Azure. È possibile anche [collegare un disco dati a una macchina virtuale Windows nel portale di Azure](../windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Prima di collegare i dischi alla macchina virtuale, leggere i seguenti suggerimenti:

* La dimensione della macchina virtuale controlla il numero di dischi dati che è possibile collegare. Per informazioni dettagliate, vedere [Dimensioni delle macchine virtuali](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Per usare l'archiviazione Premium, è necessaria una macchina virtuale della serie DS o GS. Con queste macchine virtuali, è possibile usare dischi Premium e Standard. L’archiviazione Premium è disponibile solo in determinate aree geografiche. Per ulteriori informazioni, vedere [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](../../storage/common/storage-premium-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* I dischi collegati a macchine virtuali sono effettivamente file con estensione VHD archiviati in Azure. Per informazioni dettagliate, vedere [Informazioni sui dischi e sui dischi rigidi virtuali per le macchine virtuali](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="find-the-virtual-machine"></a>Trovare la macchina virtuale
1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Nel menu a sinistra fare clic su **Macchine virtuali**.
3. Selezionare la macchina virtuale dall'elenco.
4. Nella pagina Macchine virtuali fare clic su **Dischi** in **Informazioni di base**.
   
    ![Aprire le impostazioni del disco](./media/attach-disk-portal/find-disk-settings.png)


## <a name="attach-a-new-disk"></a>Collegare un nuovo disco

1. Nel riquadro **Dischi** fare clic su **+ Aggiungi disco dati**.
2. Fare clic sul menu a discesa **Nome** e selezionare **Crea disco**:

    ![Creare un disco gestito Azure](./media/attach-disk-portal/create-new-md.png)

3. Immettere un nome per il disco gestito. Esaminare le impostazioni predefinite, aggiornare se necessario e quindi fare clic su **Crea**.
   
   ![Esaminare le impostazioni del disco](./media/attach-disk-portal/create-new-md-settings.png)

4. Fare clic su **Salva** per creare il disco gestito e aggiornare la configurazione della macchina virtuale:

   ![Salvare il nuovo disco gestito Azure](./media/attach-disk-portal/confirm-create-new-md.png)

5. Dopo che Azure crea il disco e lo collega alla macchina virtuale, il nuovo disco viene elencato nella sezione Impostazioni disco della macchina virtuale in **Dischi dati**. Dal momento che i dischi gestiti sono una risorsa di livello superiore, il disco viene visualizzato nella directory principale del gruppo di risorse:

   ![Disco gestito Azure nel gruppo di risorse](./media/attach-disk-portal/view-md-resource-group.png)

## <a name="attach-an-existing-disk"></a>Collegare un disco esistente
1. Nel riquadro **Dischi** fare clic su **+ Aggiungi disco dati**.
2. Fare clic sul menu a discesa **Nome** per visualizzare un elenco di dischi gestiti esistenti a cui è possibile accedere con la sottoscrizione di Azure. Selezionare il disco gestito da collegare:

   ![Collegare il disco gestito di Azure](./media/attach-disk-portal/select-existing-md.png)

3. Fare clic su **Salva** per collegare il disco gestito esistente e aggiornare la configurazione della macchina virtuale:
   
   ![Salvare gli aggiornamenti del disco gestito Azure](./media/attach-disk-portal/confirm-attach-existing-md.png)

4. Dopo che Azure collega il disco alla macchina virtuale, esso viene elencato nella sezione Impostazioni disco della macchina virtuale in **Dischi dei dati**.



## <a name="next-steps"></a>Passaggi successivi
È anche possibile [collegare un disco dati](add-disk.md) usando l'interfaccia della riga di comando di Azure.
