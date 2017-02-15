---
title: Collegare un disco dati a una macchina virtuale Windows | Microsoft Docs
description: Come collegare un disco dati nuovo o esistente a una macchina virtuale Windows nel portale di Azure tramite il modello di distribuzione di Resource Manager.
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3790fc59-7264-41df-b7a3-8d1226799885
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/02/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: e7ec8715087abaf5c96a6327d4bcde0ad3fd547a


---
# <a name="how-to-attach-a-data-disk-to-a-windows-vm-in-the-azure-portal"></a>Collegare un disco dati a una macchina virtuale Windows nel portale di Azure
Questo articolo illustra come collegare dischi nuovi o esistenti a una macchina virtuale Windows tramite il portale di Azure. È possibile anche [collegare un disco dati a una macchina virtuale Linux nel portale di Azure](virtual-machines-linux-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Prima di procedere, rivedere i suggerimenti seguenti:

* La dimensione della macchina virtuale controlla il numero di dischi dati che è possibile collegare. Per informazioni dettagliate, vedere [Dimensioni delle macchine virtuali](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Per un nuovo disco, non è necessario crearlo prima perché Azure lo crea quando lo si collega.
* Per un disco esistente, il file .vhd deve essere disponibile in un account di archiviazione di Azure. È possibile usare un .vhd già esistente se non è collegato a un'altra macchina virtuale o caricare il proprio file .vhd nell'account di archiviazione.

È anche possibile [collegare un disco dati usando Powershell](virtual-machines-windows-ps-manage.md#add-a-data-disk-to-a-virtual-machine).

[!INCLUDE [virtual-machines-common-attach-disk-portal](../../includes/virtual-machines-common-attach-disk-portal.md)]

## <a name="a-idinitializeinwsahow-to-initialize-a-new-data-disk-in-windows-server"></a><a id="initializeinWS"></a>Procedura: Inizializzare un nuovo disco dati in Windows Server
1. Connettersi alla macchina virtuale. Per istruzioni, vedere [Come connettersi e accedere a una macchina virtuale di Azure che esegue Windows Server](virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
2. Dopo aver eseguito l'accesso alla macchina virtuale, aprire **Server Manager**. Nel riquadro sinistro fare clic su **Servizi file e archiviazione**.
   
    ![Avviare Server Manager](./media/virtual-machines-windows-classic-attach-disk/fileandstorageservices.png)
3. Espandere il menu e selezionare **Dischi**.
4. La sezione **dischi** contiene un elenco dei dischi. Nella maggior parte dei casi, avrà disco 0, disco 1 e disco 2. Il disco 0 è il disco del sistema operativo, il disco 1 è il disco temporaneo e il disco 2 è il disco dati che è stato appena connesso alla VM. Il nuovo disco dati elencherà la partizione come **sconosciuta**. Fare clic con il pulsante destro del mouse sul disco e scegliere **Inizializza**.
5. Si riceverà una notifica che tutti i dati verranno cancellati quando viene inizializzato il disco. Fare clic su **Sì** per accettare il messaggio di avviso e inizializzare il disco. Una volta completata l'operazione, la partizione verrà elencata come **GPT**. Fare di nuovo clic con il pulsante destro del mouse sul disco e scegliere **Nuovo volume**.
6. Completare la procedura guidata usando i valori predefiniti. Al termine della procedura guidata, nella sezione **Volumi** verrà visualizzato il nuovo volume. Il disco sarà ora online e pronto per l'archiviazione di dati.

    ![Inizializzazione del volume completata](./media/virtual-machines-windows-classic-attach-disk/newvolumecreated.png)

> [!NOTE]
> Le dimensioni della VM determinano il numero di dischi che è possibile collegare. Per informazioni dettagliate, vedere [Dimensioni delle macchine virtuali](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

## <a name="next-steps"></a>Passaggi successivi
Se l'applicazione deve usare l'unità D: per archiviare i dati, è possibile [modificare la lettera di unità del disco temporaneo di Windows](virtual-machines-windows-classic-change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).




<!--HONumber=Nov16_HO3-->


