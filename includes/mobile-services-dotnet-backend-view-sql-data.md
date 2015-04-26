
L'ultimo passaggio facoltativo di questa esercitazione consiste nel controllare il database SQL associato al servizio mobile ed esaminare i dati memorizzati. 

1. Nel portale di gestione di Azure fare clic su Gestisci per il database associato al servizio mobile.
 
	![sign-in to manage SQL Database](./media/mobile-services-dotnet-backend-view-sql-data/manage-sql-azure-database.png)

2. Nel portale di gestione eseguire una query per visualizzare le modifiche apportate dall'app di Windows Store. La query da utilizzare sarà simile alla seguente, ma anziché <code>todolist</code> conterrà il nome del database.</p>

        SELECT * FROM [todolist].[todoitems]

    ![query SQL Database for stored items](./media/mobile-services-dotnet-backend-view-sql-data/sql-azure-query.png)

	Si noti che la tabella include le colonne Id, __createdAt, __updatedAt e __version. Tali colonne supportano la sincronizzazione dei dati offline e sono implementate nella classe di base [EntityData](http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.entitydata.aspx). Per altre informazioni, vedere [Introduzione alla sincronizzazione dei dati non in linea].


<!--HONumber=42-->
