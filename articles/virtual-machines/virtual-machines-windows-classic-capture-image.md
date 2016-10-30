<properties
    pageTitle="Acquisire un'immagine di una macchina virtuale Windows di Azure | Microsoft Azure"
    description="Acquisire un'immagine di una macchina virtuale Windows di Azure creata con il modello di distribuzione classico."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="cynthn"/>


#<a name="capture-an-image-of-an-azure-windows-virtual-machine-created-with-the-classic-deployment-model."></a>Acquisire un'immagine di una macchina virtuale Windows di Azure creata con il modello di distribuzione classico.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] Per informazioni sul modello Resource Manager, vedere [Create a copy of a specialized Windows VM running in Azure](virtual-machines-windows-vhd-copy.md) (Creare una copia di macchina virtuale Windows specializzata in esecuzione in Azure).


Questo articolo illustra come acquisire una macchina virtuale Linux che esegue Windows in modo da usarla come immagine per creare altre macchine virtuali. Tale immagine include il disco del sistema operativo ed eventuali dischi dati collegati alla macchina virtuale. Poiché le configurazioni di rete non sono incluse, sarà necessario configurare tali dischi quando vengono create le altre macchine virtuali che usano l'immagine.

Azure archivia l'immagine in **Immagini personali**, che è anche la posizione in cui vengono archiviate le immagini caricate. Per altre informazioni sulle immagini, vedere [Informazioni sulle immagini per le macchine virtuali Linux](virtual-machines-linux-classic-about-images.md).

##<a name="before-you-begin##"></a>Prima di iniziare##

Questa procedura presuppone che sia stata creata una macchina virtuale di Azure e che sia stato configurato il sistema operativo, inclusi gli eventuali dischi dati connessi. Se non si sono ancora effettuate queste operazioni, vedere le istruzioni seguenti:

- [Creare una macchina virtuale da un'immagine](virtual-machines-windows-classic-createportal.md)
- [Come collegare un disco dati a una macchina virtuale](virtual-machines-windows-classic-attach-disk.md)
- Assicurarsi che i ruoli server siano supportati con Sysprep. Per ulteriori informazioni, vedere [Supporto Sysprep per i ruoli server](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

> [AZURE.WARNING] Questo processo elimina la macchina virtuale originale dopo che viene acquisita. 

Prima dell'acquisizione dell'immagine di una macchina virtuale di Azure, si consiglia di eseguire il backup della macchina virtuale di destinazione. È possibile eseguire il backup delle macchine virtuali di Azure con Backup di Azure. Per informazioni dettagliate, vedere [Backup delle macchine virtuali di Azure](../backup/backup-azure-vms.md). Altre soluzioni sono disponibili da partner certificati. Per scoprire ciò che è attualmente disponibile, eseguire la ricerca in Azure Marketplace.


##<a name="capture-the-virtual-machine"></a>Acquisizione della macchina virtuale

1. Nel [portale di Azure classico](http://manage.windowsazure.com), **Connetti** alla macchina virtuale. Per informazioni, vedere [Come accedere a una macchina virtuale che esegue Windows Server] [].

2.  Aprire una finestra del Prompt dei comandi come amministratore.

3.  Impostare la directory su `%windir%\system32\sysprep`, quindi eseguire sysprep.exe.

4.  Verrà visualizzata la finestra di dialogo **Utilità preparazione sistema** . Eseguire le operazioni seguenti:

    - In **Azione pulizia sistema** selezionare **Passare alla Configurazione guidata** e verificare che l'opzione **Generalizza** sia selezionata. Per ulteriori informazioni sull'utilizzo di Sysprep, vedere il relativo [documento introduttivo][].

    - In **Opzioni di arresto del sistema** selezionare **Arresta il sistema**.

    - Fare clic su **OK**.

    ![Eseguire Sysprep.](./media/virtual-machines-windows-classic-capture-image/SysprepGeneral.png)

7.  Sysprep arresta la macchina virtuale il cui stato nel portale di Azure classico diventa **Arrestato**.

8.  Nel portale di Azure classico, fare clic su **Macchine virtuali** e selezionare la macchina virtuale che si desidera acquisire.

9.  Nella barra dei comandi fare clic su **Capture**.

    ![Acquisire la macchina virtuale](./media/virtual-machines-windows-classic-capture-image/CaptureVM.png)

    Viene visualizzata la finestra di dialogo **Capture the Virtual Machine** .

10. In **Image Name**digitare un nome per la nuova immagine.

11. Prima di aggiungere un'immagine di Windows Server al set di immagini personalizzate, è necessario eseguire Sysprep per generalizzare la macchina virtuale, come indicato nei passaggi precedenti. Fare clic su **Sysprep è stato eseguito nella macchina virtuale** per indicare che è stato eseguito.

12. Fare clic sul segno di spunta per acquisire l'immagine. La nuova immagine è ora disponibile in **Images**.

    ![Acquisizione dell'immagine eseguita correttamente](./media/virtual-machines-windows-classic-capture-image/VMCapturedImageAvailable.png)

##<a name="next-steps"></a>Passaggi successivi

L'immagine è pronta per essere utilizzata per creare macchine virtuali. Per eseguire questa operazione, sarà necessario creare una macchina virtuale usando la voce di menu **Da raccolta** e selezionando l'immagine appena creata. Per istruzioni, vedere [Creare una macchina virtuale da un'immagine](virtual-machines-windows-classic-createportal.md).



[Come accedere a una macchina virtuale che esegue Windows Server]: virtual-machines-windows-classic-connect-logon.md
[documento introduttivo]: http://technet.microsoft.com/library/bb457073.aspx
[Eseguire Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Immettere opzioni Sysprep.exe]: ./media/virtual-machines-windows-classic-capture-image/SysprepGeneral.png
[La macchina virtuale viene arrestata]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[Acquisire un'immagine della macchina virtuale]: ./media/virtual-machines-windows-classic-capture-image/CaptureVM.png
[Immettere il nome dell'immagine]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[Acquisizione dell'immagine eseguita correttamente]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[Usare l'immagine acquisita]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png



<!--HONumber=Oct16_HO2-->


