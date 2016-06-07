### Prerequisiti

- Un account di [archiviazione BLOB di Azure](https://azure.microsoft.com/documentation/services/storage/)  


Prima di poter usare l'account di archiviazione BLOB di Azure in un'app per la logica, è necessario autorizzare l'app per la logica a connettersi all'account di archiviazione BLOB di Azure. Fortunatamente, è possibile eseguire questa operazione in modo facile dalla propria app per la logica nel Portale di Azure.

Ecco i passaggi per autorizzare l'app per la logica a connettersi all'account di archiviazione BLOB di Azure:
1. Per creare una connessione ad archiviazione BLOB di Azure, nella finestra di progettazione dell'app per la logica, selezionare **Show Microsoft managed APIs** (Mostra API gestite da Microsoft) nell'elenco a discesa, quindi immettere *archiviazione BLOB di Azure* nella casella di ricerca. Selezionare il trigger o l'azione che si desidera usare: ![Passaggio per la creazione della connessione ad archiviazione BLOB di Azure](./media/connectors-create-api-azureblobstorage/azureblobstorage-1.png)  
2. Se non sono mai state create connessioni ad archiviazione BLOB di Azure prima d'ora, verrà chiesto di fornire le credenziali di archiviazione BLOB di Azure. Queste credenziali verranno usate per autorizzare l'app per la logica a connettersi e ad accedere ai dati dell'account di archiviazione BLOB di Azure: ![Passaggio per la creazione della connessione ad archiviazione BLOB di Azure](./media/connectors-create-api-azureblobstorage/azureblobstorage-2.png)  
3. Si noti che la connessione è stata creata ed è ora possibile procedere con gli altri passaggi nell'app per la logica: ![Passaggio per la creazione della connessione ad archiviazione BLOB di Azure](./media/connectors-create-api-azureblobstorage/azureblobstorage-3.png)  

<!---HONumber=AcomDC_0525_2016-->