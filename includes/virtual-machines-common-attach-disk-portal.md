


## Trovare la macchina virtuale

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Scegliere **Macchine virtuali** dal menu Hub.

3.	Selezionare la macchina virtuale dall'elenco.

4. Nel pannello Macchine virtuali di **Essentials**, fare clic su **Dischi**.

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

<!---HONumber=AcomDC_0928_2016-->