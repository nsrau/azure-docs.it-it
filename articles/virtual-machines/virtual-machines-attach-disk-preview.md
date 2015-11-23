<properties
	pageTitle="Collegare un disco dati | Microsoft Azure"
	description="Come collegare un disco dati nuovo o esistente a una macchina virtuale nel portale di anteprima di Azure tramite il modello di distribuzione di gestione delle risorse."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/14/2015"
	ms.author="cynthn"/>

# Come collegare un disco dati nel portale di anteprima di Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](storage-windows-attach-disk.md).

In questo articolo viene illustrato come collegare dischi sia nuovi che esistenti a una macchina virtuale tramite il portale di anteprima di Azure. Prima di procedere, rivedere i suggerimenti seguenti:

- La dimensione della macchina virtuale controlla il numero di dischi dati che è possibile collegare. Per informazioni dettagliate, vedere [Dimensioni delle macchine virtuali](virtual-machines-size-specs.md).
- Per utilizzare l'archiviazione Premium, è necessario utilizzare una macchina virtuale della serie DS o serie GS. È possibile utilizzare dischi dagli account di archiviazione sia Premium che Standard con queste macchine virtuali. L’archiviazione Premium è disponibile solo in determinate aree geografiche. Per ulteriori informazioni, vedere [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](../storage/storage-premium-storage-preview-portal.md).
- Dischi collegati a macchine virtuali sono effettivamente file con estensione .vhd in un account di archiviazione di Azure. Per informazioni dettagliate, vedere [Informazioni sui dischi e sui dischi rigidi virtuali per le macchine virtuali](virtual-machines-disks-vhds.md).
- Per un nuovo disco, non è necessario crearlo prima perché Azure lo crea quando lo si collega.
- Per un disco esistente, il file .vhd deve essere disponibile in un account di archiviazione di Azure. È possibile utilizzarne uno esistente, se non è collegato a un'altra macchina virtuale o caricare il proprio file.vhd nell'account di archiviazione.

## Trovare la macchina virtuale

1. Accedere al portale di anteprima di Azure.

2. Nel menu Hub fare clic su **Sfoglia**.

3. Nel pannello di ricerca scorrere verso il basso e fare clic su **Macchine virtuali**.

	![Cercare macchine virtuali](./media/virtual-machines-attach-disk-preview/search-blade-preview-portal.png)

4.	Selezionare la macchina virtuale dall'elenco.

5. A destra, in **Essentials**, fare clic su **Tutte le impostazioni**, quindi fare clic su **Dischi**.

	![Aprire le impostazioni del disco](./media/virtual-machines-attach-disk-preview/find-disk-settings.png)

Continuare seguendo le istruzioni riportate per collegare un nuovo disco o un disco esistente.

## Opzione 1: Collega un nuovo disco

1.	Nel pannello **Dischi** fare clic su **Collega nuovo**.

2.	Esaminare le impostazioni predefinite, aggiornare se necessario e quindi fare clic su **OK**.

 	![Esaminare le impostazioni del disco](./media/virtual-machines-attach-disk-preview/attach-new.png)

3.	Dopo che Azure crea il disco e lo collega alla macchina virtuale, il nuovo disco viene elencato nella sezione Impostazioni disco della macchina virtuale in **Dischi dati**.

## Opzione 2: Collegare un disco esistente

1.	Nel pannello **Dischi** fare clic su **Collega esistente**.

2.	In **Collega un disco esistente** fare clic su **File VHD**.

	![Collegare un disco esistente](./media/virtual-machines-attach-disk-preview/attach-existing.png)

3.	In **Account di archiviazione** selezionare l'account e un contenitore che contiene il file con estensione vhd.

	![Individuare il percorso di un VHD](./media/virtual-machines-attach-disk-preview/find-storage-container.png)

4.	Selezionare il file con estensione vhd.

5.	In **Collega un disco esistente** il file appena selezionato è elencato in **File VHD**. Fare clic su **OK**.

6.	Dopo che Azure collega il disco alla macchina virtuale, esso viene elencato nella sezione Impostazioni disco della macchina virtuale in **Dischi dati**.

## Passaggi successivi

Dopo aver aggiunto il disco, è necessario prepararlo per l'utilizzo nel sistema operativo della macchina virtuale:

- Per Linux, vedere "Procedura: inizializzare un nuovo disco dati in Linux" in questo [articolo](virtual-machines-linux-how-to-attach-disk.md).
- Per Windows, vedere "Procedura: inizializzare un nuovo disco dati in Windows Server" in questo [articolo](storage-windows-attach-disk.md).

## Risorse aggiuntive

[Informazioni sugli account di archiviazione di Azure]

<!--Link references-->

[Informazioni sugli account di archiviazione di Azure]: ../storage-whatis-account/

<!---HONumber=Nov15_HO3-->