


## <a name="attach-an-empty-disk"></a>Collegare un disco vuoto
Il collegamento di un disco vuoto costituisce un modo semplice per aggiungere un disco dati perché Azure crea automaticamente il file con estensione vhd e lo archivia nell'account di archiviazione.

1. Fare clic su **Macchine virtuali (versione classica)**, quindi selezionare la VM appropriata.

2. Nel menu Impostazioni fare clic su **Dischi**.

   ![Collegare un nuovo disco vuoto](./media/howto-attach-disk-windows-linux/menudisksattachnew.png)

3. Sulla barra dei comandi fare clic su **Collega nuovo**.  
    Verrà visualizzata la finestra di dialogo **Collega un nuovo disco**.

    ![Collegare un nuovo disco](./media/howto-attach-disk-windows-linux/newdiskdetail.png)

    Specificare le informazioni seguenti:
    - In **Nome file**accettare il nome predefinito o digitarne un altro per il file con estensione vhd. Il disco dati usa un nome generato automaticamente, anche se si digita un altro nome per il file con estensione vhd.
    - Selezionare il **tipo** di disco dati. Tutte le macchine virtuali supportano dischi Standard. Molte macchine virtuali supportano anche dischi Premium.
    - Selezionare le **dimensioni (GB)** del disco dati.
    - Per **Memorizzazione nella cache dell'host** scegliere Nessuna o Sola lettura.
    - Fare clic su OK per terminare.

4. Dopo che è stato creato e collegato, il disco dati verrà elencato nella sezione dei dischi della VM.

   ![Disco dati nuovo e vuoto collegato correttamente](./media/howto-attach-disk-windows-linux/newdiskemptysuccessful.png)

> [!NOTE]
> Dopo aver aggiunto un disco dati, è necessario accedere alla VM e inizializzare il disco in modo che possa essere usato.

## <a name="how-to-attach-an-existing-disk"></a>Procedura: Collegare un disco esistente
Per collegare un disco esistente, è necessario che in un account di archiviazione sia disponibile un file con estensione vhd. Usare il cmdlet [Add-AzureVhd](https://msdn.microsoft.com/library/azure/dn495173.aspx) per caricare il file con estensione vhd nell'account di archiviazione. Dopo aver creato e caricato il file con estensione vhd, è possibile collegarlo a una VM.

1. Fare clic su **Macchine virtuali (versione classica)** e quindi selezionare la macchina virtuale appropriata.

2. Nel menu Impostazioni fare clic su **Dischi**.

3. Sulla barra dei comandi fare clic su **Collega esistente**.

    ![Collegare il disco dati](./media/howto-attach-disk-windows-linux/menudisksattachexisting.png)

4. Fare clic su **Percorso**. Verranno visualizzati gli account di archiviazione disponibili. Selezionare quindi un account di archiviazione appropriato tra quelli elencati.

    ![Selezionare un account di archiviazione per il disco](./media/howto-attach-disk-windows-linux/existdiskstorageaccounts.png)

5. Un **account di archiviazione** ha uno o più contenitori con unità disco (dischi rigidi virtuali). Selezionare il contenitore appropriato nell'elenco.

    ![Finestra di selezione del contenitore delle macchine virtuali](./media/howto-attach-disk-windows-linux/existdiskcontainers.png)

6. Il pannello **VHD** elenca le unità disco presenti nel contenitore. Fare clic su uno dei dischi, quindi su Seleziona.

    ![Finestra di selezione dell'immagine del disco per le macchine virtuali](./media/howto-attach-disk-windows-linux/existdiskvhds.png)

7. Verrà di nuovo visualizzato il pannello **Collega un disco esistente** con il percorso dell'account di archiviazione, il contenitore e il disco rigido virtuale selezionato (vhd) da aggiungere alla macchina virtuale.

  Impostare **Memorizzazione nella cache dell'host** su Nessuno o Sola lettura, quindi fare clic su OK.

    ![Disco dati correttamente collegato](./media/howto-attach-disk-windows-linux/exisitingdisksuccessful.png)
