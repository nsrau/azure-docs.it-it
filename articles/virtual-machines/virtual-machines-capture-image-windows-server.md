<properties
	pageTitle="Acquisire un'immagine di una macchina virtuale Windows di Azure | Microsoft Azure"
	description="Acquisire un'immagine di una macchina virtuale Windows di Azure creata con il modello di distribuzione classico."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/05/2015"
	ms.author="cynthn"/>

#Acquisire un'immagine di una macchina virtuale Windows di Azure creata con il modello di distribuzione classico.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modello Gestione risorse.


Questo articolo illustra come acquisire una macchina virtuale Linux che esegue Windows in modo da usarla come immagine per creare altre macchine virtuali. Tale immagine include il disco del sistema operativo ed eventuali dischi dati collegati alla macchina virtuale. Poiché le configurazioni di rete non sono incluse, sarà necessario configurare tali dischi quando vengono create le altre macchine virtuali che usano l'immagine.

Azure archivia l'immagine in**Immagini personali**, che è anche la posizione in cui vengono archiviate le immagini caricate. Per ulteriori informazioni sulle immagini, vedere[Informazioni sulle immagini per le macchine virtuali](virtual-machines-images.md).

##Prima di iniziare##

Questa procedura presuppone che sia stata creata una macchina virtuale di Azure e che sia stato configurato il sistema operativo, inclusi gli eventuali dischi dati connessi. Se non si sono ancora effettuate queste operazioni, vedere le istruzioni seguenti:

- [Creare una macchina virtuale da un'immagine](virtual-machines-create-custom.md)
- [Come collegare un disco dati a una macchina virtuale](storage-windows-attach-disk.md)

> [AZURE.WARNING]Questo processo elimina la macchina virtuale originale dopo che viene acquisita.

Non rappresenta un modo per eseguire il backup di una macchina virtuale. Uno dei modi per eseguire questa operazione è Azure Backup, disponibile come anteprima in alcune aree. Per informazioni dettagliate, vedere [Backup delle macchine virtuali di Azure](../backup/backup-azure-vms.md). Altre soluzioni sono disponibili da partner certificati. Per scoprire ciò che è attualmente disponibile, eseguire la ricerca in Azure Marketplace.


##Acquisizione della macchina virtuale

1. Nel[portale di Azure classico](http://manage.windowsazure.com), **Connetti**alla macchina virtuale. Per informazioni, vedere [Come accedere a una macchina virtuale che esegue Windows Server][].

2.	Aprire una finestra del Prompt dei comandi come amministratore.

3.	Impostare la directory su `%windir%\system32\sysprep`, quindi eseguire sysprep.exe.

4. 	Verrà visualizzata la finestra di dialogo **Utilità preparazione sistema**. Eseguire le operazioni seguenti:

	- In **Azione pulizia sistema** selezionare **Passare alla Configurazione guidata** e verificare che l'opzione **Generalizza** sia selezionata. Per ulteriori informazioni sull'utilizzo di Sysprep, vedere il relativo [documento introduttivo][].

	- In **Opzioni di arresto del sistema** selezionare **Arresta il sistema**.

	- Fare clic su **OK**.

	![Eseguire Sysprep.](./media/virtual-machines-capture-image-windows-server/SysprepGeneral.png)

7.	Sysprep arresta la macchina virtuale il cui stato nel portale di Azure classico diventa **Arrestato**.

8.	Nel portale di Azure classico, fare clic su**Macchine virtuali**e selezionare la macchina virtuale che si desidera acquisire.

9.	Nella barra dei comandi fare clic su **Capture**.

	![Acquisire la macchina virtuale](./media/virtual-machines-capture-image-windows-server/CaptureVM.png)

	Viene visualizzata la finestra di dialogo **Capture the Virtual Machine**.

10.	In **Image Name** digitare un nome per la nuova immagine.

11.	Prima di aggiungere un'immagine di Windows Server al set di immagini personalizzate, è necessario eseguire Sysprep per generalizzare la macchina virtuale, come indicato nei passaggi precedenti. Fare clic su **Sysprep è stato eseguito nella macchina virtuale** per indicare che è stato eseguito.

12.	Fare clic sul segno di spunta per acquisire l'immagine. La nuova immagine è ora disponibile in **Images**.

 	![Acquisizione dell'immagine eseguita correttamente](./media/virtual-machines-capture-image-windows-server/VMCapturedImageAvailable.png)

##Passaggi successivi

L'immagine è pronta per essere utilizzata per creare macchine virtuali. Per eseguire questa operazione, sarà necessario creare una macchina virtuale usando la voce di menu **Da raccolta** e selezionando l'immagine appena creata. Per istruzioni, vedere [Creare una macchina virtuale da un'immagine](virtual-machines-create-custom.md).



[Come accedere a una macchina virtuale che esegue Windows Server]: virtual-machines-log-on-windows-server.md
[documento introduttivo]: http://technet.microsoft.com/library/bb457073.aspx
[Run Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Enter Sysprep.exe options]: ./media/virtual-machines-capture-image-windows-server/SysprepGeneral.png
[The virtual machine is stopped]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[Capture an image of the virtual machine]: ./media/virtual-machines-capture-image-windows-server/CaptureVM.png
[Enter the image name]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[Image capture successful]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[Use the captured image]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png

<!---HONumber=AcomDC_1203_2015-->