<properties
	pageTitle="Collegare un disco a una macchina virtuale | Microsoft Azure"
	description="Collegare un disco dati da una macchina virtuale Windows creata con il modello di distribuzione classico e inizializzarla."
	services="virtual-machines-windows, storage"
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
	ms.date="05/17/2016"
	ms.author="cynthn"/>

# Collegare un disco dati da una macchina virtuale di Windows creata con il modello di distribuzione classico.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](virtual-machines-windows-attach-disk-portal.md).

Se è necessario un disco dati aggiuntivo, è possibile collegare un disco vuoto o un disco esistente con dei dati a una macchina virtuale. In entrambi i casi, i dischi sono file con estensione vhd che risiedono in un account di archiviazione di Azure. Nel caso di un nuovo disco, dopo aver collegato il disco, è anche necessario inizializzarlo in modo che sia pronto per essere usato da una macchina virtuale Windows.

Per altre informazioni dettagliate sui dischi, vedere [Informazioni sui dischi e sui dischi rigidi virtuali per le macchine virtuali](virtual-machines-windows-about-disks-vhds.md).

## Procedura dettagliata video

Di seguito è riportata una [procedura video dettagliata](https://azure.microsoft.com/documentation/videos/attaching-a-data-disk-to-a-windows-vm/) dei passaggi di questa esercitazione.


[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

## Inizializzare il disco

1. Connettersi alla macchina virtuale. Per istruzioni, vedere [Come accedere a una macchina virtuale che esegue Windows Server][logon].

2. Dopo aver eseguito l'accesso alla macchina virtuale, aprire **Server Manager**. Nel riquadro sinistro fare clic su **Servizi file e archiviazione**.

	![Avviare Server Manager](./media/virtual-machines-windows-classic-attach-disk/fileandstorageservices.png)

3. Espandere il menu e selezionare **Dischi**.

4. La sezione **dischi** contiene un elenco dei dischi. Nella maggior parte dei casi, avrà disco 0, disco 1 e disco 2. Il disco 0 è il disco del sistema operativo, il disco 1 è il disco temporaneo e il disco 2 è il disco dati che è stato appena connesso alla VM. Il nuovo disco dati elencherà la partizione come **sconosciuta**. Fare clic con il pulsante destro del mouse sul disco e scegliere **Inizializza**.

5.	Si riceverà una notifica che tutti i dati verranno cancellati quando viene inizializzato il disco. Fare clic su **Sì** per accettare il messaggio di avviso e inizializzare il disco. Una volta completata l’operazione, la partizione verrà elencata come **GPT**. Fare di nuovo clic con il pulsante destro del mouse sul disco e scegliere **Nuovo volume**.

6.	Completare la procedura guidata usando i valori predefiniti. Al termine della procedura guidata, nella sezione **Volumi** verrà visualizzato il nuovo volume. Il disco sarà ora online e pronto per l'archiviazione di dati.

	![Inizializzazione del volume completata](./media/virtual-machines-windows-classic-attach-disk/newvolumecreated.png)

> [AZURE.NOTE] Le dimensioni della VM determinano il numero di dischi che è possibile collegare. Per informazioni dettagliate, vedere [Dimensioni delle macchine virtuali](virtual-machines-linux-sizes.md).

## Risorse aggiuntive

[Come scollegare un disco dati da una macchina virtuale di Windows](virtual-machines-windows-classic-detach-disk.md)

[Informazioni sui dischi e sui dischi rigidi virtuali per le macchine virtuali](virtual-machines-linux-about-disks-vhds.md)

[logon]: virtual-machines-windows-classic-connect-logon.md

<!---HONumber=AcomDC_0525_2016-->