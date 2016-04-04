

In questo articolo viene illustrato come collegare dischi sia nuovi che esistenti a una macchina virtuale tramite il portale di Azure. Prima di procedere, rivedere i suggerimenti seguenti:

- La dimensione della macchina virtuale controlla il numero di dischi dati che è possibile collegare. Per informazioni dettagliate, vedere [Dimensioni delle macchine virtuali](virtual-machines-linux-sizes.md).
- Per utilizzare l'archiviazione Premium, è necessario utilizzare una macchina virtuale della serie DS o serie GS. È possibile utilizzare dischi dagli account di archiviazione sia Premium che Standard con queste macchine virtuali. L’archiviazione Premium è disponibile solo in determinate aree geografiche. Per ulteriori informazioni, vedere [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](../storage/storage-premium-storage-preview-portal.md).
- Dischi collegati a macchine virtuali sono effettivamente file con estensione .vhd in un account di archiviazione di Azure. Per informazioni dettagliate, vedere [Informazioni sui dischi e sui dischi rigidi virtuali per le macchine virtuali](virtual-machines-linux-about-disks-vhds.md).
- Per un nuovo disco, non è necessario crearlo prima perché Azure lo crea quando lo si collega.
- Per un disco esistente, il file .vhd deve essere disponibile in un account di archiviazione di Azure. È possibile usare un .vhd già esistente se non è collegato a un'altra macchina virtuale o caricare il proprio file .vhd nell'account di archiviazione.

## Trovare la macchina virtuale

1. Accedere al portale di Azure.

2. Nel menu Hub fare clic su **Macchine virtuali**.

3.	Selezionare la macchina virtuale dall'elenco.

4. A destra, in **Essentials**, fare clic su **Tutte le impostazioni**, quindi fare clic su **Dischi**.

	![Aprire le impostazioni del disco](./media/virtual-machines-common-attach-disk-portal/find-disk-settings.png)

Continuare seguendo le istruzioni riportate per collegare un nuovo disco o un disco esistente.

## Opzione 1: Collega un nuovo disco

1.	Nel pannello **Dischi** fare clic su **Collega nuovo**.

2.	Esaminare le impostazioni predefinite, aggiornare se necessario e quindi fare clic su **OK**.

 	![Esaminare le impostazioni del disco](./media/virtual-machines-common-attach-disk-portal/attach-new.png)

3.	Dopo che Azure crea il disco e lo collega alla macchina virtuale, il nuovo disco viene elencato nella sezione Impostazioni disco della macchina virtuale in **Dischi dati**.

## Opzione 2: Collegare un disco esistente

1.	Nel pannello **Dischi**, fare clic su **Collega esistente**.

2.	In **Collega un disco esistente** fare clic su **File VHD**.

	![Collegare un disco esistente](./media/virtual-machines-common-attach-disk-portal/attach-existing.png)

3.	In **Account di archiviazione**, selezionare l'account e un contenitore che contiene il file con estensione vhd.

	![Individuare il percorso di un VHD](./media/virtual-machines-common-attach-disk-portal/find-storage-container.png)

4.	Selezionare il file con estensione vhd.

5.	In **Collega un disco esistente** il file appena selezionato è elencato in **File VHD**. Fare clic su **OK**.

6.	Dopo che Azure collega il disco alla macchina virtuale, esso viene elencato nella sezione Impostazioni disco della macchina virtuale in **Dischi dei dati**.

<!---HONumber=AcomDC_0323_2016-->