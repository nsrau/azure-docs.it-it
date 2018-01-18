## <a name="create-a-storage-account-by-using-the-azure-portal"></a>Creare un account di archiviazione usando il portale di Azure

Innanzitutto, creare un nuovo account di archiviazione generico da usare per questa guida rapida. 

1. Passare al [portale di Azure](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM) e accedere con l'account di Azure. 
2. Immettere un nome univoco per l'account di archiviazione. Per la denominazione dell'account di archiviazione tenere presente queste regole:
    - Il nome deve avere una lunghezza compresa tra 3 e 24 caratteri.
    - Il nome può contenere solo lettere minuscole e numeri.
3. Verificare che siano impostati i valori predefiniti seguenti: 
    - **Modello di distribuzione** deve essere impostato su **Gestione risorse**.
    - **Tipologia account** deve essere impostato su **Utilizzo generico**.
    - **Prestazioni** deve essere impostato su **Standard**.
    - **Replica** deve essere impostato su **Archiviazione con ridondanza locale**.
4. Selezionare la propria sottoscrizione. 
5. Per **Gruppo di risorse** crearne una nuova e assegnarle un nome univoco. 
6. Selezionare la posizione da usare per l'account di archiviazione.
7. Selezionare **Aggiungi al dashboard** e fare clic su **Crea** per creare l'account di archiviazione. 

Dopo aver creato l'account di archiviazione, questo viene aggiunto al dashboard. Selezionare l'account per aprirlo. In **Impostazioni** selezionare **Chiavi di accesso**. Selezionare la chiave primaria e copiare la stringa di connessione associata negli Appunti. Incollare quindi la stringa in un editor di testo per usarla successivamente.
