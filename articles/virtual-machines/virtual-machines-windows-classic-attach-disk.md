---
title: Collegare un disco a una macchina virtuale di Azure classica | Documentazione Microsoft
description: Collegare un disco dati da una macchina virtuale Windows creata con il modello di distribuzione classico e inizializzarla.
services: virtual-machines-windows, storage
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: be4e3e74-05bc-4527-969f-84f10a1d66a7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 783082db6a3d763067a0e661970d6d2b61264803
ms.openlocfilehash: d580732116666d3d304744d90721c05a1fb3462b
ms.lasthandoff: 02/23/2017


---
# <a name="attach-a-data-disk-to-a-windows-virtual-machine-created-with-the-classic-deployment-model"></a>Collegare un disco dati da una macchina virtuale di Windows creata con il modello di distribuzione classico.
<!--
Refernce article:
    If you want to use the new portal, see [How to attach a data disk to a Windows VM in the Azure portal](virtual-machines-windows-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
-->

Questo articolo illustra come collegare dischi nuovi o esistenti creati con il modello di distribuzione classica a una macchina virtuale Windows usando il portale di Azure.

È possibile anche [collegare un disco dati a una macchina virtuale Linux nel portale di Azure](virtual-machines-linux-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Prima di collegare un disco, esaminare i seguenti suggerimenti:

* La dimensione della macchina virtuale controlla il numero di dischi dati che è possibile collegare. Per informazioni dettagliate, vedere [Dimensioni delle macchine virtuali](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* Per usare l'archiviazione Premium, è necessaria una macchina virtuale della serie DS o GS. È possibile utilizzare dischi dagli account di archiviazione sia Premium che Standard con queste macchine virtuali. L’archiviazione Premium è disponibile solo in determinate aree geografiche. Per ulteriori informazioni, vedere [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](../storage/storage-premium-storage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* Per un nuovo disco, non è necessario crearlo prima perché Azure lo crea quando lo si collega.

È anche possibile [collegare un disco dati usando Powershell](virtual-machines-windows-attach-disk-ps.md).

> [!IMPORTANT]
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="find-the-virtual-machine"></a>Trovare la macchina virtuale
1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Selezionare la macchina virtuale dalla risorsa visualizzata nel dashboard.
3. Nella sezione **Impostazioni** presente nel riquadro sinistro fare clic su **Dischi**.

    ![Aprire le impostazioni del disco](./media/virtual-machines-windows-classic-attach-disk/virtualmachinedisks.png)

Continuare seguendo le istruzioni per il collegamento di un [nuovo disco](#option-1-attach-a-new-disk) o un [disco esistente](#option-2-attach-an-existing-disk).

## <a name="option-1-attach-and-initialize-a-new-disk"></a>Opzione 1: Connettere e inizializzare un nuovo disco

1. Nel pannello **Dischi** fare clic su **Collega nuovo**.
2. Esaminare le impostazioni predefinite, aggiornare se necessario e quindi fare clic su **OK**.

   ![Esaminare le impostazioni del disco](./media/virtual-machines-windows-classic-attach-disk/attach-new.png)

3. Dopo che Azure crea il disco e lo collega alla macchina virtuale, il nuovo disco viene elencato nella sezione Impostazioni disco della macchina virtuale in **Dischi dati**.

### <a name="initialize-a-new-data-disk"></a>Inizializzare un nuovo disco dati

1. Connettersi alla macchina virtuale. Per istruzioni, vedere [Come connettersi e accedere a una macchina virtuale di Azure che esegue Windows Server](virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
2. Dopo aver eseguito l'accesso alla macchina virtuale, aprire **Server Manager**. Nel riquadro sinistro fare clic su **Servizi file e archiviazione**.

    ![Avviare Server Manager](./media/virtual-machines-windows-classic-attach-disk/fileandstorageservices.png)

3. Selezionare **Dischi**.
4. La sezione **dischi** contiene un elenco dei dischi. Nella maggior parte dei casi, avrà disco 0, disco 1 e disco 2. Il disco 0 è il disco del sistema operativo, il disco 1 è il disco temporaneo e il disco 2 è il disco dati appena connesso alla macchina virtuale. Il disco dati visualizza la partizione come **Unknown** (Sconosciuta).

 Fare clic con il pulsante destro del mouse sul disco e scegliere **Inizializza**.

5. Si riceverà una notifica che tutti i dati verranno cancellati quando viene inizializzato il disco. Fare clic su **Sì** per accettare il messaggio di avviso e inizializzare il disco. Una volta completata l'operazione, la partizione verrà elencata come **GPT**. Fare di nuovo clic con il pulsante destro del mouse sul disco e scegliere **Nuovo volume**.

6. Completare la procedura guidata usando i valori predefiniti. Al termine della procedura guidata, nella sezione **Volumi** verrà visualizzato il nuovo volume. Il disco sarà ora online e pronto per l'archiviazione di dati.

    ![Inizializzazione del volume completata](./media/virtual-machines-windows-classic-attach-disk/newdiskafterinitialization.png)

## <a name="option-2-attach-an-existing-disk"></a>Opzione 2: Collegare un disco esistente
1. Nel pannello **Dischi** fare clic su **Collega esistente**.
2. In **Collega un disco esistente** fare clic su **Posizione**.

   ![Collegare un disco esistente](./media/virtual-machines-windows-classic-attach-disk/attachexistingdisksettings.png)
3. In **Account di archiviazione**, selezionare l'account e un contenitore che contiene il file con estensione vhd.

   ![Individuare il percorso di un VHD](./media/virtual-machines-windows-classic-attach-disk/existdiskstorageaccountandcontainer.png)

4. Selezionare il file con estensione vhd.
5. In **Collega un disco esistente** il file appena selezionato è elencato in **File VHD**. Fare clic su **OK**.
6. Dopo che Azure collega il disco alla macchina virtuale, esso viene elencato nella sezione Impostazioni disco della macchina virtuale in **Dischi dei dati**.

## <a name="use-trim-with-standard-storage"></a>Usare TRIM con l'archiviazione Standard

Se si usa l'archiviazione Standard (HDD), è consigliabile abilitare TRIM. TRIM ignora i blocchi inutilizzati nel disco facendo in modo che venga addebitato solo lo spazio di archiviazione usato effettivamente. L'uso di TRIM consente di risparmiare sui costi, inclusi i blocchi inutilizzati risultanti dall'eliminazione di file di grandi dimensioni.

Per verificare l'impostazione TRIM, è possibile eseguire questo comando. Aprire un prompt dei comandi nella macchina virtuale Windows e digitare:

```
fsutil behavior query DisableDeleteNotify
```

Se il comando restituisce 0, l'impostazione TRIM è abilitata correttamente. Se restituisce 1, eseguire questo comando per abilitare TRIM:
```
fsutil behavior set DisableDeleteNotify 0
```

## <a name="next-steps"></a>Passaggi successivi
Se l'applicazione deve usare l'unità D: per archiviare i dati, è possibile [modificare la lettera di unità del disco temporaneo di Windows](virtual-machines-windows-classic-change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="additional-resources"></a>Risorse aggiuntive
[Informazioni sui dischi e sui dischi rigidi virtuali per le macchine virtuali](virtual-machines-linux-about-disks-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

