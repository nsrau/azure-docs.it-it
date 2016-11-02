### Prerequisiti
- Un account Azure, che è possibile [creare gratuitamente](https://azure.microsoft.com/free)
- Un [database SQL di Azure](../articles/sql-database/sql-database-get-started.md) con relative informazioni di connessione, tra cui il nome del server, il nome del database, il nome utente e la password. Queste informazioni sono incluse nella stringa di connessione del database SQL:
  
	Server=tcp:*yoursqlservername*.database.windows.net,1433;Initial Catalog=*yourqldbname*;Persist Security Info=False;User ID={your\_username};Password={your\_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;

	Ulteriori informazioni sui [database SQL di Azure](https://azure.microsoft.com/services/sql-database).

> [AZURE.NOTE] Quando si crea un database SQL di Azure, è inoltre possibile creare i database di esempio inclusi in SQL.



Prima di utilizzare il database SQL di Azure in un'app per la logica, connettersi al proprio database SQL. È possibile eseguire questa operazione in modo facile dalla propria app per la logica nel portale di Azure.

Connettersi al database SQL di Azure attenendosi alla procedura seguente:

1. Creare un'app per la logica. Nella finestra di progettazione App per la logica, aggiungere un trigger e un'azione. Selezionare **Mostra API gestite da Microsoft** nell'elenco a discesa, quindi immettere "sql" nella casella di ricerca. Selezionare una delle azioni:

	![Passaggio di creazione della connessione a SQL Azure](./media/connectors-create-api-sqlazure/sql-actions.png)

2. Se in precedenza non sono state create connessioni al database SQL, vengono richiesti i dettagli della connessione:

	![Passaggio di creazione della connessione a SQL Azure](./media/connectors-create-api-sqlazure/connection-details.png)

3. Immettere i dettagli del database SQL. Le proprietà con l'asterisco sono obbligatorie.

	| Proprietà | Dettagli |
|---|---|
| Connessione tramite gateway | Lasciare deselezionata. Si usa quando ci si connette a un SQL Server locale. |
| Nome connessione* | Immettere un nome per la connessione. | 
| Nome di SQL Server* | Immettere il nome del server, simile a *nomeserver.database.windows.net*. Il nome del server è visualizzato nelle proprietà del database SQL nel portale di Azure ed è mostrato anche nella stringa di connessione. | 
| Nome del database SQL* | Immettere il nome che è stato assegnato al database SQL. Questo nome è riportato nelle proprietà del database SQL nella stringa di connessione: Initial Catalog =*yoursqldbname*. | 
| Nome utente* | Immettere il nome utente creato quando è stato creato il database SQL. Questo nome è riportato nelle proprietà del database SQL nel portale di Azure. | 
| Password* | Immettere la password creata quando è stato creato il database SQL. | 

	Queste credenziali vengono usate per autorizzare l'app per la logica a connettersi e per l'accesso ai dati SQL. Al termine i dettagli della connessione saranno simili ai seguenti:

	![Passaggio di creazione della connessione a SQL Azure](./media/connectors-create-api-sqlazure/sample-connection.png)

4. Selezionare **Create**.

5. La connessione è stata creata. A questo punto, procedere con gli altri passaggi nell'app per la logica:

	![Passaggio di creazione della connessione a SQL Azure](./media/connectors-create-api-sqlazure/table.png)

<!---HONumber=AcomDC_0727_2016-->