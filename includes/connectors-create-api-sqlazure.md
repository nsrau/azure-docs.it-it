### Prerequisiti

- Un account [SQL Azure](https://www.microsoft.com/sql)


Prima di poter usare l'account SQL Azure in un'app per la logica, è necessario autorizzare l'app per la logica a connettersi all'account SQL Azure. Fortunatamente, è possibile eseguire questa operazione in modo facile dalla propria app per la logica nel Portale di Azure.

Ecco i passaggi per autorizzare l'app per la logica a connettersi all'account SQL Azure:
1. Per creare una connessione a SQL Azure, nella finestra di progettazione dell'app per la logica, selezionare **Show Microsoft managed APIs** (Mostra API gestite da Microsoft) nell'elenco a discesa, quindi immettere *SQL Azure* nella casella di ricerca. Selezionare il trigger o l'azione che si desidera usare: ![Passaggio di creazione della connessione a SQL Azure](./media/connectors-create-api-sql/sql-1.png)
2. Se non sono mai state create connessioni a SQL Azure prima d'ora, verrà chiesto di fornire le credenziali di SQL Azure. Queste credenziali verranno usate per autorizzare l'app per la logica a connettersi e ad accedere ai dati dell'account SQL Azure. Il formato della stringa di connessione SQL è il seguente: **Server=tcp:[serverName].database.windows.net;Database=myDataBase; User ID=[LoginForDb]@[serverName];Password=myPassword;Trusted\_Connection=False; Encrypt=True;** ![Passaggio di creazione della connessione a SQL Azure](./media/connectors-create-api-sql/sql-2.png)
3. Si noti che la connessione è stata creata ed è ora possibile procedere con gli altri passaggi nell'app per la logica: ![Passaggio di creazione della connessione a SQL Azure](./media/connectors-create-api-sql/sql-3.png)

<!---HONumber=AcomDC_0629_2016-->