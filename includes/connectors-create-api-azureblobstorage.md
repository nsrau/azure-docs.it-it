### <a name="prerequisites"></a>prerequisiti
* Un account Azure, che è possibile [creare gratuitamente](https://azure.microsoft.com/free)
* Un [account di archiviazione BLOB di Azure](../articles/storage/common/storage-create-storage-account.md), inclusi il nome dell'account di archiviazione e la relativa chiave di accesso. Queste informazioni sono riportate nelle proprietà dell'account di archiviazione nel portale di Azure. Altre informazioni sull'[archiviazione di Azure](../articles/storage/common/storage-introduction.md).

Prima di utilizzare l'account di archiviazione Blob di Azure in app per la logica, connettersi all'account di archiviazione Blob di Azure. È possibile eseguire questa operazione in modo facile dalla propria app per la logica nel portale di Azure.  

Connettersi all'account di archiviazione Blob di Azure attenendosi alla procedura seguente:  

1. Creare un'app per la logica. Nella finestra di progettazione App per la logica, aggiungere un trigger e un'azione. Selezionare **Mostra API gestite da Microsoft** nell'elenco a discesa, quindi immettere "blob" nella casella di ricerca. Selezionare una delle azioni:  
   
    ![Passaggio per la creazione della connessione ad archiviazione BLOB di Azure](./media/connectors-create-api-azureblobstorage/azureblobstorage-1.png)  
2. Se in precedenza non sono state create connessioni all'archiviazione di Azure, vengono richiesti i dettagli della connessione:   
   
    ![Passaggio per la creazione della connessione ad archiviazione BLOB di Azure](./media/connectors-create-api-azureblobstorage/connection-details.png)  
3. Immettere i dettagli dell'account di archiviazione. Le proprietà con un asterisco sono obbligatorie.
   
   | Proprietà | Dettagli |
   | --- | --- |
   | Nome connessione * |Immettere un nome per la connessione. |
   | Nome dell'account di archiviazione di Azure * |Immettere il nome dell'account di archiviazione, Il nome dell'account di archiviazione viene visualizzato nelle proprietà di archiviazione nel portale di Azure. |
   | Chiave di accesso dell'account di archiviazione di Azure * |Immettere la chiave dell'account di archiviazione. La chiave di accesso viene visualizzata nelle proprietà di archiviazione nel portale di Azure. |
   
    Queste credenziali vengono usate per autorizzare l'app per la logica a connettersi e ad accedere ai dati. 
4. Selezionare **Create**.
5. La connessione è stata creata. A questo punto, procedere con gli altri passaggi nell'app per la logica: 
   
    ![Passaggio per la creazione della connessione ad archiviazione BLOB di Azure](./media/connectors-create-api-azureblobstorage/azureblobstorage-3.png)  

