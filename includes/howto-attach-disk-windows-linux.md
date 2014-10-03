Per altre informazioni sui dischi in macchine virtuali di Azure, vedere [Informazioni sui dischi delle macchine virtuali in Azure][].

## <span id="attachempty"></span></a>Procedura: Collegare un disco vuoto

Il collegamento di un disco vuoto costituisce il modo più semplice per aggiungere un disco dati perché Azure crea automaticamente il file con estensione vhd e lo archivia nell'account di archiviazione.

1.  Fare clic su **Virtual Machines** e quindi selezionare la macchina virtuale appropriata.

2.  Sulla barra dei comandi fare clic su **Connetti**, quindi selezionare **Connetti disco vuoto**.

    ![Collegare un disco vuoto][]

    Verrà visualizzata la finestra di dialogo **Connetti disco vuoto**.

    ![Collegare un nuovo disco vuoto][]

3.  In **Nome file** accettare il nome generato automaticamente o digitare un nome descrittivo. Il disco dati creato dal file con estensione vhd userà sempre il nome generato automaticamente.

4.  In **Size** immettere la dimensione del disco dati.

5.  Fare clic sul segno di spunta per collegare il disco dati vuoto.

    A questo punto il disco dati verrà visualizzato nel dashboard della macchina virtuale.

    ![Disco dati vuoto collegato correttamente][]

## <span id="attachexisting"></span></a>Procedura: Collegare un disco esistente

Per collegare un disco esistente, è necessario che in un account di archiviazione sia disponibile un file con estensione vhd. Usare il cmdlet [Add-AzureVhd][] per caricare il file con estensione vhd nell'account di archiviazione. Dopo aver creato e caricato il file con estensione vhd, è possibile collegarlo a una macchina virtuale.

1.  Fare clic su **Virtual Machines** e quindi selezionare la macchina virtuale appropriata.

2.  Sulla barra dei comandi fare clic su **Attach**, quindi selezionare **Attach Disk**.

    ![Collegare il disco dati][]

    Verrà visualizzata la finestra di dialogo **Attach Disk**.

    ![Immettere i dettagli del disco][]

3.  Selezionare il disco dati da collegare alla macchina virtuale.
4.  Fare clic sul segno di spunta per collegare il disco dati alla macchina virtuale.

    A questo punto il disco dati verrà visualizzato nel dashboard della macchina virtuale.

    ![Disco dati correttamente collegato][]

> [WACOM.NOTE]
> Dopo l'aggiunta di un disco dati, sarà necessario accedere alla macchina virtuale e inizializzare il disco in modo che la macchina virtuale possa usarlo per l'archiviazione.

  [Informazioni sui dischi delle macchine virtuali in Azure]: http://go.microsoft.com/fwlink/p/?LinkId=403697
  [Collegare un disco vuoto]: ./media/howto-attach-disk-window-linux/AttachDiskWindows.png
  [Collegare un nuovo disco vuoto]: ./media/howto-attach-disk-window-linux/AttachNewDiskWindows.png
  [Disco dati vuoto collegato correttamente]: ./media/howto-attach-disk-window-linux/AttachEmptySuccess.png
  [Add-AzureVhd]: http://go.microsoft.com/FWLink/p/?LinkID=391684
  [Collegare il disco dati]: ./media/howto-attach-disk-window-linux/AttachExistingDiskWindows.png
  [Immettere i dettagli del disco]: ./media/howto-attach-disk-window-linux/AttachExistingDisk.png
  [Disco dati correttamente collegato]: ./media/howto-attach-disk-window-linux/AttachSuccess.png
