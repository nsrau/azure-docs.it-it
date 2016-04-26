## Creare un nuovo utente del database con SSMS

La procedura seguente presuppone l'uso di SSMS, la connessione al database SQL in Esplora oggetti e la connessione al server logico del database SQL come amministratore entità a livello di server o con un account utente con le autorizzazioni necessarie per concedere le autorizzazioni utente. La procedura seguente presuppone anche l'esistenza nel database di un utente a cui si vogliono concedere le autorizzazioni dbo.

1. In Esplora oggetti espandere il nodo Database e selezionare il database con l'utente a cui si vogliono concedere le autorizzazioni dbo.

     ![SQL Server Management Studio: Connettersi a un server di database SQL](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-1.png)

2. Fare clic con il pulsante destro del mouse sul database selezionato e quindi scegliere **Query**.

     ![SQL Server Management Studio: Connettersi a un server di database SQL](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-2.png)

3. Nella finestra della query modificare e usare l'istruzione Transact-SQL seguente per concedere le autorizzazioni dbo a un utente specificato.

    '''ALTER ROLE db\_owner ADD MEMBER user1;

     ![SQL Server Management Studio: Connettersi a un server di database SQL](./media/sql-database-grant-database-user-dbo-permissions/sql-database-grant-database-user-dbo-permissions-1.png)

<!---HONumber=AcomDC_0420_2016-->