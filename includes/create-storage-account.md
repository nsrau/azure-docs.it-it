Per effettuare operazioni di archiviazione, è necessario un account di archiviazione di Azure. Per creare un account di archiviazione, attenersi alla procedura riportata di seguito (è possibile creare un account di archiviazione anche [utilizzando l'API REST][1]).

1.  Accedere al [portale di gestione di Azure][2].

2.  Nella parte inferiore del pannello di navigazione fare clic su
    **NEW**.
    
    ![+nuovo](./media/create-storage-account/plus-new.png)

3.  Fare clic su **DATA SERVICES**, quindi su **STORAGE** e infine su **QUICK CREATE**.
    
    ![Finestra di dialogo Creazione
    rapida](./media/create-storage-account/quick-storage-2.png)

4.  Nel campo URL, digitare un nome di sottodominio da utilizzare nell'URI per l'account di archiviazione. La voce può contenere da 3 a 24 lettere minuscole e numeri. Questo valore diventa il nome host all'interno dell'URI utilizzato per fare riferimento a risorse BLOB, di accodamento o tabelle per la sottoscrizione.

5.  Scegliere una regione o un gruppo di affinità in cui situare l'archiviazione. Se si utilizzerà l'archiviazione dalla propria applicazione Azure, selezionare la stessa area in cui verrà distribuita l'applicazione.

6.  Facoltativamente, è possibile abilitare la replica geografica.

7.  Fare clic su **CREATE STORAGE ACCOUNT**.



[1]: http://msdn.microsoft.com/en-us/library/windowsazure/hh264518.aspx
[2]: http://manage.windowsazure.com