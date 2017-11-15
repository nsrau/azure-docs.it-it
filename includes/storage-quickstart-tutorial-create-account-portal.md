## <a name="create-a-storage-account-using-the-azure-portal"></a>Creare un account di archiviazione usando il portale di Azure

Innanzitutto, creare un nuovo account di archiviazione generico da usare per questa guida rapida. 

1. Aprire e accedere al [portale di Azure](https://portal.azure.com) con il proprio account di Azure. 
2. Nel menu Hub selezionare **Nuovo** > **Archiviazione** > **Account di archiviazione: BLOB, File, Tabelle, Code**. 
3. Immettere un nome univoco per l'account di archiviazione. Per la denominazione dell'account di archiviazione tenere presente queste regole:
    - Il nome deve avere una lunghezza compresa tra 3 e 24 caratteri.
    - Il nome può contenere solo lettere minuscole e numeri.
4. Verificare che siano impostati i valori predefiniti seguenti: 
    - **Modello di distribuzione** deve essere impostato su **Gestione risorse**.
    - **Tipologia account** deve essere impostato su **Utilizzo generico**.
    - **Prestazioni** deve essere impostato su **Standard**.
    - **Replica** deve essere impostato su **Archiviazione con ridondanza locale**.
5. Selezionare la propria sottoscrizione. 
6. Per **Gruppo di risorse** crearne una nuova e assegnarle un nome univoco. 
7. Selezionare il **Percorso** da usare per l'account di archiviazione.
8. Selezionare **Aggiungi al dashboard** e fare clic su **Crea** per creare l'account di archiviazione. 

Dopo aver creato l'account di archiviazione, questa viene aggiunta al dashboard. Fare clic su di essa per aprirla. In **Impostazioni** fare clic su **Chiavi di accesso**. Selezionare la chiave primaria e copiare la **Stringa di connessione** associata negli Appunti, quindi incollarla in un editor di testo per un usarla in seguito.
