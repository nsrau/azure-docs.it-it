


## <a name="attach-an-empty-disk"></a>Collegare un disco vuoto
Il collegamento di un disco vuoto costituisce un modo semplice per aggiungere un disco dati perché Azure crea automaticamente il file con estensione vhd e lo archivia nell'account di archiviazione.

1. Fare clic su **Macchine virtuali**, quindi selezionare la VM appropriata.
2. Sulla barra dei comandi fare clic su **Collega** e quindi su **Collega disco vuoto**.

    ![Collegare un disco vuoto](./media/howto-attach-disk-windows-linux/AttachEmptyDisk.png)

3. Verrà visualizzata la finestra di dialogo **Connetti disco vuoto** .

    ![Collegare un nuovo disco vuoto](./media/howto-attach-disk-windows-linux/AttachEmptyDetail.png)

    Eseguire la procedura seguente:
    - In **Nome file**accettare il nome predefinito o digitarne un altro per il file con estensione vhd. Il disco dati usa un nome generato automaticamente, anche se si digita un altro nome per il file con estensione vhd.
    - Digitare in **Dimensioni (GB)** le dimensioni del disco dati.
    - Fare clic sul segno di spunta per terminare.

4. Una volta creato e collegato, il disco dati verrà elencato nel dashboard della VM.
   
   ![Disco dati vuoto collegato correttamente](./media/howto-attach-disk-windows-linux/AttachEmptySuccess.png)

> [!NOTE]
> Dopo aver aggiunto un disco dati, è necessario accedere alla VM e inizializzare il disco in modo che possa essere usato. 

## <a name="how-to-attach-an-existing-disk"></a>Procedura: Collegare un disco esistente
Per collegare un disco esistente, è necessario che in un account di archiviazione sia disponibile un file con estensione vhd. Usare il cmdlet [Add-AzureVhd](https://msdn.microsoft.com/library/azure/dn495173.aspx) per caricare il file con estensione vhd nell'account di archiviazione. Dopo aver creato e caricato il file con estensione vhd, è possibile collegarlo a una VM.

1. Fare clic su **Macchine virtuali**e quindi selezionare la macchina virtuale appropriata.
2. Sulla barra dei comandi fare clic su **Collega** e quindi selezionare **Collega disco**.

    ![Collegare il disco dati](./media/howto-attach-disk-windows-linux/AttachExistingDisk.png)

3. Selezionare il disco dati e quindi fare clic sul segno di spunta per collegarlo.
   
    ![Immettere i dettagli del disco](./media/howto-attach-disk-windows-linux/AttachExistingDetail.png)
4. Una volta collegato, il disco dati verrà elencato nel dashboard della VM.

    ![Disco dati correttamente collegato](./media/howto-attach-disk-windows-linux/AttachExistingSuccess.png)


<!--HONumber=Nov16_HO3-->


