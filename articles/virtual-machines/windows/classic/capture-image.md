---
title: Acquisire un&quot;immagine di una macchina virtuale Windows di Azure | Microsoft Docs
description: Acquisire un&quot;immagine di una macchina virtuale Windows di Azure creata con il modello di distribuzione classico.
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: a5986eac-4cf3-40bd-9b79-7c811806b880
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 06a97b48fed8f80679632f5c0320d4dd73b8e1e2
ms.lasthandoff: 03/27/2017


---
# <a name="capture-an-image-of-an-azure-windows-virtual-machine-created-with-the-classic-deployment-model"></a>Acquisire un'immagine di una macchina virtuale Windows di Azure creata con il modello di distribuzione classico.
> [!IMPORTANT]
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../../../resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti. Per informazioni sul modello Resource Manager, vedere [Create a copy of a specialized Windows VM running in Azure](../../virtual-machines-windows-vhd-copy.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Creare una copia di macchina virtuale Windows specializzata in esecuzione in Azure).

Questo articolo illustra come acquisire una macchina virtuale Linux che esegue Windows in modo da usarla come immagine per creare altre macchine virtuali. Tale immagine include il disco del sistema operativo ed eventuali dischi dati collegati alla macchina virtuale. Poiché le configurazioni di rete non sono incluse, sarà necessario impostare le configurazioni di rete quando vengono create le altre macchine virtuali che usano l'immagine.

Azure archivia l'immagine in **Immagini VM (classico)**, un servizio di **Calcolo** elencato quando si visualizzano tutti i servizi di Azure. che è anche la posizione in cui vengono archiviate le immagini caricate. Per altre informazioni sulle immagini, vedere [Informazioni sulle immagini per le macchine virtuali Linux](about-images.md?toc=%2fazure%2fvirtual-machines%2fWindows%2fclassic%2ftoc.json).

## <a name="before-you-begin"></a>Prima di iniziare
Questa procedura presuppone che sia stata creata una macchina virtuale di Azure e che sia stato configurato il sistema operativo, inclusi gli eventuali dischi dati connessi. Vedere gli articoli seguenti, se non è stato già fatto, per informazioni sulla creazione e la preparazione della macchina virtuale:

* [Creare una macchina virtuale da un'immagine](createportal.md)
* [Come collegare un disco dati a una macchina virtuale](attach-disk.md)
* Assicurarsi che i ruoli server siano supportati con Sysprep. Per ulteriori informazioni, vedere [Supporto Sysprep per i ruoli server](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

> [!WARNING]
> Questo processo elimina la macchina virtuale originale dopo che viene acquisita.
>
>

Prima dell'acquisizione dell'immagine di una macchina virtuale di Azure, si consiglia di eseguire il backup della macchina virtuale di destinazione. È possibile eseguire il backup delle macchine virtuali di Azure con Backup di Azure. Per informazioni dettagliate, vedere [Backup delle macchine virtuali di Azure](../../../backup/backup-azure-vms.md). Altre soluzioni sono disponibili da partner certificati. Per scoprire ciò che è attualmente disponibile, eseguire la ricerca in Azure Marketplace.

## <a name="capture-the-virtual-machine"></a>Acquisizione della macchina virtuale
1. Nel [portale di Azure](http://portal.azure.com) **connettersi** alla macchina virtuale. Per istruzioni, vedere [Come accedere a una macchina virtuale che esegue Windows Server][How to sign in to a virtual machine running Windows Server].
2. Aprire una finestra del Prompt dei comandi come amministratore.
3. Impostare la directory su `%windir%\system32\sysprep`, quindi eseguire sysprep.exe.
4. Verrà visualizzata la finestra di dialogo **Utilità preparazione sistema** . Eseguire le operazioni seguenti:

   * In **Azione pulizia sistema** selezionare **Passare alla Configurazione guidata** e verificare che l'opzione **Generalizza** sia selezionata. Per altre informazioni sull'uso di Sysprep, vedere [How to Use Sysprep: An Introduction][How to Use Sysprep: An Introduction] (Introduzione all'uso di Sysprep).
   * In **Opzioni di arresto del sistema** selezionare **Arresta il sistema**.
   * Fare clic su **OK**.

   ![Eseguire Sysprep.](./media/capture-image/SysprepGeneral.png)
5. Sysprep arresta la macchina virtuale il cui stato nel portale di Azure classico diventa **Arrestato**.
6. Nel portale di Azure, fare clic su **Immagini VM (classico)** e selezionare la macchina virtuale che si desidera acquisire. Il gruppo **Immagini VM (classico)** è elencato in **Calcolo** quando si visualizza **Altri servizi**.

7. Nella barra dei comandi fare clic su **Capture**.

   ![Acquisire la macchina virtuale](./media/capture-image/CaptureVM.png)

   Viene visualizzata la finestra di dialogo **Capture the Virtual Machine** .

8. In **Nome immagine** digitare un nome per la nuova immagine. In **Etichetta immagine** digitare un'etichetta per la nuova immagine.

9. Fare clic su **I've run Sysprep on the virtual machine** (Sysprep è stato eseguito sulla macchina virtuale). Questa casella di controllo si riferisce alle azioni con Sysprep nei passaggi 3-5. Prima di aggiungere un'immagine di Windows Server al set di immagini personalizzate, è _necessario_ eseguire Sysprep per generalizzare un'immagine.

10. Una volta completata l'acquisizione, la nuova immagine viene resa disponibile nel **Marketplace**, in **Calcolo**, nel contenitore **Immagini VM (classico)**.

    ![Acquisizione dell'immagine eseguita correttamente](./media/capture-image/VMCapturedImageAvailable.png)

## <a name="next-steps"></a>Passaggi successivi
L'immagine è pronta per essere utilizzata per creare macchine virtuali. A tale scopo, si creerà una macchina virtuale selezionando la voce di menu **Altri servizi** nella parte inferiore del menu dei servizi, quindi **Immagini VM (classico)** nel gruppo **Calcolo**. Per istruzioni, vedere [Creare una macchina virtuale da un'immagine](createportal.md).

[How to sign in to a virtual machine running Windows Server]:connect-logon.md
[How to Use Sysprep: An Introduction]: http://technet.microsoft.com/library/bb457073.aspx
[Run Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Enter Sysprep.exe options]: ./media/capture-image/SysprepGeneral.png
[The virtual machine is stopped]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[Capture an image of the virtual machine]: ./media/capture-image/CaptureVM.png
[Enter the image name]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[Image capture successful]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[Use the captured image]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png

