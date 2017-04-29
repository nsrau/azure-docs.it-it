<!--author=alkohli last changed: 9/17/15-->

#### <a name="to-add-a-storage-account-in-storsimple-8000-series-update-10"></a>Per aggiungere un account di archiviazione nell’aggiornamento 1.0 della serie 8000 di StorSimple
1. Nella pagina di destinazione del servizio StorSimple Manager, selezionare il servizio e fare doppio clic su di esso. Verrà visualizzata la pagina **Avvio rapido** . Selezionare la pagina **Configura** .
2. Fare clic su **Aggiungi/modifica account di archiviazione**.
3. Nella finestra di dialogo **Aggiungi/Modifica account di archiviazione** fare clic su **Aggiungi nuovo**.
4. Nel campo **Provider** selezionare il provider del servizio cloud appropriato. I provider supportati sono Azure, Amazon S3, Amazon S3 con RRS, HP e OpenStack. Specificare le credenziali e il percorso associati all'account di archiviazione dei provider del servizio cloud. I campi visualizzati per le credenziali saranno diversi a seconda del provider del servizio cloud specificato. 
   
   * Se è stato selezionato Azure come provider di servizi cloud, specificare il **nome** e la **chiave di accesso** primaria dell'account di archiviazione di Microsoft Azure. Per un account di Azure, il percorso verrà inserito automaticamente.
     
        ![Account un account di archiviazione di Azure](./media/storsimple-configure-new-storage-account-u1/AddAzureStorageaccount-include.png)
   * Se è stato selezionato Amazon S3 o Amazon S3 con RRS, specificare un **nome account di archiviazione** descrittivo, una **chiave di accesso** e una **chiave privata**. Per Amazon S3 e Amazon S3 con RRS, sono supportati i seguenti percorsi:
     
     * Stati Uniti Standard
     * Stati Uniti occidentali (Oregon)
     * Stati Uniti occidentali (California settentrionale)
     * Europa (Irlanda)
     * Asia Pacifico (Singapore)
     * Asia Pacifico (Sydney)
     * Asia Pacifico (Tokyo)
     * America meridionale (Sao Paulo)
       
       ![Account un account di archiviazione di Amazon](./media/storsimple-configure-new-storage-account-u1/AddAmazonStorageaccount-include.png)
   * Se è stato selezionato HP come provider di servizi cloud, specificare un **nome account di archiviazione** descrittivo, un **ID tenant**, un **nome utente** e una **password**. Per HP, sono supportati i percorsi seguenti:
     
     * Stati Uniti orientali
     * Stati Uniti occidentali
       
       ![Aggiungere un account di archiviazione di HP](./media/storsimple-configure-new-storage-account-u1/AddHPStorageaccount-include.png)
   * Se è stato selezionato **Openstack** come provider di servizi cloud, specificare un **nome host**, una **chiave di accesso** e una **chiave privata**.
     
     > [!NOTE]
     > Per tutti i provider di servizi cloud, ad eccezione di Azure, è consentito l'uso di un nome descrittivo. È possibile usare nomi descrittivi e creare più account di archiviazione con lo stesso set di credenziali.
     > 
     > 
     
        ![Aggiungere un account di archiviazione di Openstack](./media/storsimple-configure-new-storage-account-u1/AddOpenstackStorageaccount-include.png)
5. Selezionare **Abilita modalità SSL** per creare un canale sicuro per la comunicazione di rete tra il dispositivo e il cloud. Deselezionare la casella di controllo **Abilita modalità SSL** solo se si opera all'interno di un cloud privato.
   
   > [!NOTE]
   > Se si usa HP come provider, SSL viene sempre abilitato.
   > 
   > 
6. Fare clic sull’icona del segno di spunta  ![icona del segno di spunta](./media/storsimple-configure-new-storage-account/HCS_CheckIcon-include.png). Quando la creazione dell’account di archiviazione sarà completata si riceverà una notifica.
7. L'account di archiviazione appena creato verrà visualizzato nella pagina **Configura** in **Account di archiviazione**. Fare clic su **Salva** per salvare il nuovo account di archiviazione. Fare clic su **OK** quando viene richiesto di confermare.

