
L'ultimo passaggio facoltativo di questa esercitazione consiste nel controllare il databse SQL associato al servizio mobile ed esaminare i dati memorizzati.

1. Nel portale di gestione di Azure fare clic su Gestisci per il database associato al servizio mobile.
 
	![accesso per gestione database SQL](./media/mobile-services-dotnet-backend-view-sql-data/manage-sql-azure-database.png)

2. Nel portale di gestione eseguire una query per visualizzare le modifiche apportate dall'app di Windows Store. La query da usare sarà simile alla seguente, ma anziché <code>todolist</code> conterrà il nome del database.</p>

        SELECT * FROM [todolist].[todoitems]

    ![query al database SQL per ricerca elementi memorizzati](./media/mobile-services-dotnet-backend-view-sql-data/sql-azure-query.png)

	Si noti che la tabella include le colonne Id, \_\_createdAt, \_\_updatedAt, and \_\_version. Tali colonne supportano la sincronizzazione dei dati offline e sono implementate nella classe di base [EntityData](http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.entitydata.aspx). Per ulteriori informazioni, vedere [Get started with offline data sync].

<!---HONumber=Oct15_HO3-->