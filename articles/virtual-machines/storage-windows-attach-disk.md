<properties
	pageTitle="Collegare un disco a una macchina virtuale | Azure"
	description="Informazioni su come collegare un disco dati a una macchina virtuale di Azure e inizializzarlo in modo che sia pronto per l'uso."
	services="virtual-machines, storage"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/16/2015"
	ms.author="kathydav"/>

# Come collegare un disco dati a una macchina virtuale Windows

È possibile collegare dischi vuoti e i dischi dati. In entrambi i casi, i dischi sono effettivamente file con estensione vhd che risiedono in un account di archiviazione di Azure. Inoltre, dopo avere collegato il disco, sarà necessario inizializzarlo affinché sia pronto per l'uso.

> [AZURE.NOTE]È consigliabile usare uno o più dischi separati per archiviare i dati di una macchina virtuale. Quando si crea una macchina virtuale di Azure, questa contiene un disco per il sistema operativo mappato all'unità C e un disco temporaneo mappato all'unità D. **Non usare l'unità D per archiviare i dati.** Come si può dedurre dal nome, fornisce solo archiviazione temporanea. Non offre funzionalità di ridondanza o backup perché non risiede nel servizio di archiviazione di Azure.

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

## <a id="initializeinWS"></a>Procedura: Inizializzare un nuovo disco dati in Windows Server

1. Connettersi alla macchina virtuale. Per istruzioni, vedere [Come accedere a una macchina virtuale che esegue Windows Server][logon].

2. Dopo aver eseguito l'accesso alla macchina virtuale, aprire **Server Manager**. Nel riquadro sinistro fare clic su **Servizi file e archiviazione**.

	![Avviare Server Manager](./media/storage-windows-attach-disk/fileandstorageservices.png)

3. Espandere il menu e selezionare **Dischi**.

4. Nella sezione **Dischi** è visualizzato un elenco con tre dischi: disco 0, disco 1 e disco 2. Disco 0 è il disco del sistema operativo, disco 1 è il disco temporaneo (che non deve essere usato per l'archiviazione dei dati) e disco 2 è il disco dati connesso alla macchina virtuale. Il disco dati ha una capacità di 5 GB, come specificato quando il disco è stato collegato. Fare clic con il pulsante destro del mouse sul disco 2 e scegliere **Inizializza**.

5.	Si riceverà una notifica che tutti i dati verranno cancellati quando viene inizializzato il disco. Fare clic su **Sì** per accettare il messaggio di avviso e inizializzare il disco. Quindi, fare di nuovo clic con il pulsante destro del mouse sul disco 2 e scegliere **Nuovo volume**.

6.	Completare la procedura guidata usando i valori predefiniti. Al termine della procedura guidata, nella sezione **Volumi** verrà visualizzato il nuovo volume. Il disco sarà ora online e pronto per l'archiviazione di dati.

	![Inizializzazione del volume completata](./media/storage-windows-attach-disk/newvolumecreated.png)

> [AZURE.NOTE]Le dimensioni della macchina virtuale determinano il numero di dischi che è possibile collegare. Per informazioni dettagliate, vedere [Dimensioni delle macchine virtuali](virtual-machines-size-specs.md).

## Risorse aggiuntive

[Come scollegare un disco dati da una macchina virtuale di Windows](storage-windows-detach-disk.md)

[Informazioni sui dischi e sui dischi rigidi virtuali per le macchine virtuali](virtual-machines-disks-vhds.md)

[logon]: virtual-machines-log-on-windows-server.md

<!---HONumber=July15_HO4-->