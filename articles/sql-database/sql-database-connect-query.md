<properties
	pageTitle="Connettersi al database SQL con una query in C# | Microsoft Azure"
	description="Scrivere un programma in C# per eseguire query e connettersi al database SQL. Informazioni su indirizzi IP, stringhe di connessione, accesso sicuro e Visual Studio gratuito."
	services="sql-database"
	keywords="query di database in c#, query in c#, connettersi al database, SQL C#"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.date="08/17/2016"
	ms.author="stevestein"/>



# Connettersi a un database SQL con Visual Studio

> [AZURE.SELECTOR]
- [Visual Studio](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

Informazioni su come connettersi a un database SQL di Azure in Visual Studio.

## Prerequisiti


Per connettersi a un database SQL usando Visual Studio sono necessari gli elementi seguenti:


- Un database SQL cui connettersi. Questo articolo usa il database di esempio **AdventureWorks**. Per ottenere il database di esempio AdventureWorks, vedere [Esercitazione sul database SQL: Creare un database SQL in pochi minuti usando il portale di Azure](sql-database-get-started.md).


- Visual Studio 2013 Update 4 o versioni successive. Ora Microsoft fornisce Visual Studio Community *gratuitamente*.
 - [Download di Visual Studio Community](http://www.visualstudio.com/products/visual-studio-community-vs)
 - [Altre opzioni per la versione gratuita di Visual Studio](http://www.visualstudio.com/products/free-developer-offers-vs.aspx)




## Aprire Visual Studio dal portale di Azure


1. Accedere al [Portale di Azure](https://portal.azure.com/).

2. Fare clic su **More Services** (Altri servizi) > **Database SQL**
3. Aprire il pannello del database **AdventureWorks** individuando il database *AdventureWorks* e facendo clic su di esso.

6. Nella parte superiore del pannello del database fare clic su **Strumenti**:

	![Nuova query. Connettersi a un server di database SQL: SQL Server Management Studio](./media/sql-database-connect-query/tools.png)

7. Fare clic su **Apri in Visual Studio**. Se è necessario Visual Studio, fare clic sul collegamento per il download:

	![Nuova query. Connettersi a un server di database SQL: SQL Server Management Studio](./media/sql-database-connect-query/open-in-vs.png)


8. Visual Studio si apre con la finestra **Connetti al server** già impostata per la connessione al server e al database selezionati nel portale. Fare clic su **Opzioni** per verificare che la connessione sia impostata sul database corretto. Digitare la password amministratore server e fare clic su **Connetti**.


	![Nuova query. Connettersi a un server di database SQL: SQL Server Management Studio](./media/sql-database-connect-query/connect.png)


8. Se non è stata configurata una regola del firewall per l'indirizzo IP del computer, verrà visualizzato il messaggio *Impossibile connettersi*. Per creare una regola del firewall, vedere [Configurare una regola firewall a livello di server per il database SQL di Azure tramite il portale di Azure](sql-database-configure-firewall-settings.md).


9. Dopo aver stabilito la connessione si aprirà la finestra **Esplora oggetti di SQL Server** con una connessione al database.

	![Nuova query. Connettersi a un server di database SQL: SQL Server Management Studio](./media/sql-database-connect-query/sql-server-object-explorer.png)


## Eseguire una query di esempio

Ora che è stata stabilita la connessione al database, i passaggi seguenti illustrano come eseguire una query semplice:

2. Fare clic con il pulsante destro del mouse sul database e selezionare **Nuova query**.

	![Nuova query. Connettersi a un server di database SQL: SQL Server Management Studio](./media/sql-database-connect-query/new-query.png)

3. Nella finestra della query appena aperta, copiare e incollare il codice seguente:

		SELECT
		CustomerId
		,Title
		,FirstName
		,LastName
		,CompanyName
		FROM SalesLT.Customer;

4. Fare clic sul pulsante **Esegui** per eseguire la query:

	![Completamento della procedura. Connettersi al server di database SQL: SVisual Studio](./media/sql-database-connect-query/run-query.png)

## Passaggi successivi

- L'apertura dei database SQL in Visual Studio richiede SQL Server Data Tools. Per altri dettagli, vedere [SQL Server Data Tools](https://msdn.microsoft.com/library/hh272686.aspx).
- Per connettersi a un database SQL tramite codice, vedere [Connettersi al database SQL tramite .NET (C#)](sql-database-develop-dotnet-simple.md).

<!----HONumber=AcomDC_0824_2016-->