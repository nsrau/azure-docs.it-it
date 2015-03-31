
Per altre informazioni sui dischi, vedere [Informazioni sui dischi delle macchine virtuali in Azure](http://go.microsoft.com/fwlink/p/?LinkId=403697).

## <a id="attachempty"></a>Procedura: Collegare un disco vuoto
Il collegamento di un disco vuoto costituisce il modo più semplice per aggiungere un disco dati perché Azure crea automaticamente il file con estensione vhd e lo archivia nell'account di archiviazione.

1. Fare clic su **Macchine virtuali** e quindi selezionare la macchina virtuale appropriata.

2. Sulla barra dei comandi fare clic su **Connetti** e quindi su **Connetti disco vuoto**.


	![Attach an empty disk](./media/howto-attach-disk-window-linux/AttachEmptyDisk.png)

3.	Verrà visualizzata la finestra di dialogo **Connetti disco vuoto**.


	![Attach a new empty disk](./media/howto-attach-disk-window-linux/AttachEmptyDetail.png)

 
	Seguire questa procedura:

	- In **Nome file** accettare il nome predefinito o digitarne un altro per il file con estensione vhd, che viene usato per il disco. Il disco dati usa un nome generato automaticamente, anche se si digita un altro nome per il file con estensione vhd.

	- Digitare in **Dimensioni (GB)** le dimensioni del disco dati. 

	- Fare clic sul segno di spunta per terminare.

4.	Una volta creato e collegato, il disco dati verrà elencato nel dashboard della macchina virtuale.

	![Empty data disk successfully attached](./media/howto-attach-disk-window-linux/AttachEmptySuccess.png)

## <a id="attachexisting"></a>Procedura: Collegare un disco esistente

Per collegare un disco esistente, è necessario che in un account di archiviazione sia disponibile un file con estensione vhd. Usare il cmdlet [Add-AzureVhd](http://go.microsoft.com/FWLink/p/?LinkID=391684) per caricare il file con estensione vhd nell'account di archiviazione. Dopo aver creato e caricato il file con estensione vhd, è possibile collegarlo a una macchina virtuale. 

1. Fare clic su **Macchine virtuali** e quindi selezionare la macchina virtuale appropriata.

2. Sulla barra dei comandi fare clic su **Allega**, quindi selezionare **Connetti disco**.


	![Attach data disk](./media/howto-attach-disk-window-linux/AttachExistingDisk.png)

	Verrà visualizzata la finestra di dialogo **Connetti disco**.



	![Enter data disk details](./media/howto-attach-disk-window-linux/AttachExistingDetail.png)

3. Selezionare il disco dati da collegare alla macchina virtuale.

4. Fare clic sul segno di spunta per collegare il disco dati alla macchina virtuale.
 
5.	Una volta collegato, il disco dati verrà elencato nel dashboard della macchina virtuale.


	![Data disk successfully attached](./media/howto-attach-disk-window-linux/AttachExistingSuccess.png)

> [AZURE.NOTE] 
> Dopo l'aggiunta di un disco dati, sarà necessario accedere alla macchina virtuale e inizializzare il disco in modo che la macchina virtuale possa usarlo per l'archiviazione.




<!--HONumber=47-->
