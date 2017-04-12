---
title: Creare un&quot;immagine di Azure RemoteApp basata su una macchina virtuale di Azure | Microsoft Docs
description: Informazioni su come creare un&quot;immagine per Azure RemoteApp iniziando con una macchina virtuale di Azure.
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: d41583ef-6cd8-4115-8dcb-b2cd5b3d301a
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: c7ecd590503fcd2bc3ba06919a2f1a5c0fd0943e
ms.lasthandoff: 03/31/2017


---
# <a name="create-a-azure-remoteapp-image-based-on-an-azure-virtual-machine"></a>Creare un'immagine di Azure RemoteApp basata su una macchina virtuale di Azure
> [!IMPORTANT]
> Azure RemoteApp verrà sospeso a partire dal 31 agosto 2017. Per i dettagli, vedere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) .
> 
> 

È possibile creare immagini di Azure RemoteApp (che contengono le app condivise nella raccolta) da una macchina virtuale di Azure. Inoltre, è possibile scegliere di utilizzare un’immagine della macchina virtuale che è stata aggiunta alla raccolta immagini della VM di Azure che soddisfa tutti i requisiti per le immagini di Azure RemoteApp: se si desidera, l'immagine della macchina virtuale può essere usata come punto di partenza per la propria macchina virtuale. È sufficiente cercare l'immagine "Host sessione Desktop remoto di Windows Server" nella libreria.

Sono necessari due passaggi per creare la propria immagine in base a una VM di Azure: la creazione dell'immagine e il caricamento dalla raccolta di macchine virtuali di Azure ad Azure RemoteApp.

## <a name="create-a-custom-image-based-on-an-azure-vm"></a>Creare un'immagine personalizzata basata su una macchina virtuale di Azure
Usare questi passaggi per creare un'immagine basata su una macchina virtuale di Azure.

1. Creare una macchina virtuale di Azure. È possibile usare l'immagine "Windows Server Remote Desktop Session Host" o "Windows Server Remote Desktop Session Host with Microsoft Office 365 ProPlus" dalla raccolta immagini per le macchine virtuali di Azure. Questa immagine soddisfa tutti i requisiti dell'immagine modello di Azure RemoteApp.
   
    Per i dettagli, vedere [Creare una macchina virtuale che esegue Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
2. Connettersi alla macchina virtuale e installare e configurare le app da condividere mediante RemoteApp. Assicurarsi di eseguire tutte le configurazioni aggiuntive di Windows richieste dalle app.
   
    Per informazioni dettagliate, vedere [How to Log on to a Virtual Machine Running Windows Server](../virtual-machines/windows/classic/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) (Come accedere a una macchina virtuale che esegue Windows Server).
3. Se si usa una delle immagini Host sessione Desktop remoto di Windows Server, viene fornito uno script di valutazione che assicura che la macchina virtuale soddisfi i prerequisiti di RemoteApp. Per eseguire lo script, fare doppio clic su **ValidateRemoteAppImage** nel desktop. Assicurarsi di risolvere tutti gli errori rilevati dallo script prima di procedere al passaggio successivo.
4. Generalizzare l'immagine con SYSPREP e acquisirla. Per istruzioni, vedere [How to Capture a Windows Virtual Machine to Use as a Template](../virtual-machines/windows/classic/capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) (Come acquisire una macchina virtuale Windows da usare come modello).

## <a name="import-the-image-into-the-azure-remoteapp-image-library"></a>Importare l'immagine nella raccolta immagini di Azure RemoteApp
Usare questi passaggi per importare la nuova immagine Azure RemoteApp:

1. Nella scheda **Immagini modello** :
   
   * Se non sono disponibili immagini, fare clic su **Caricare o importare un'immagine modello**.
   * Se è presente almeno un'immagine, fare clic su **+** per aggiungere una nuova immagine.
2. Selezionare **Importare un'immagine dalla raccolta di macchine virtuali** e fare clic su **Avanti**.
3. Nella pagina successiva, selezionare l'immagine personalizzata dall'elenco e confermare che sono stati seguiti i passaggi elencati durante la creazione dell'immagine. Fare clic su **Next**.
4. Immettere un nome per la nuova immagine di RemoteApp e scegliere il percorso, quindi fare clic sul segno di spunta per avviare il processo di importazione.

> [!NOTE]
> È possibile importare le immagini da qualsiasi percorso di Azure supportato dalle macchine virtuali di Azure in qualsiasi percorso di Azure supportato da Azure RemoteApp. A seconda dei percorsi, l'importazione può richiedere fino a 25 minuti.
> 
> 

Ora è possibile creare la nuova raccolta, [cloud](remoteapp-create-cloud-deployment.md) o [ibrida](remoteapp-create-hybrid-deployment.md), in base alle proprie esigenze.


