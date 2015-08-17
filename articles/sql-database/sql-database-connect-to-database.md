<properties
	urlDisplayName="How to connect to an Azure SQL database using SSMS"
	pageTitle="Come connettersi a un database SQL di Azure tramite SQL Server Management Studio" metaKeywords=""
	description="Informazioni su come connettersi a un database SQL di Azure tramite SQL Server Management Studio."
	metaCanonical=""
	services="sql-database"
	documentationCenter=""
	title="How to connect to an Azure SQL database using SSMS"
	authors="sidneyh" solutions=""
	manager="jhubbard" editor="" />


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="07/15/2015"
	ms.author="sidneyh" />


# Connettersi con SQL Server Management Studio

Questi sono i passaggi per installare SQL Server Management Studio (SSMS) e utilizzare SQL Server Management Studio per connettersi e interrogare il Database SQL.

## Prerequisiti
* Un database di esempio AdventureWorks di database SQL come descritto in[Guida introduttiva al Database SQL di Microsoft Azure](sql-database-get-started.md).

## Installare e avviare SQL Server Management Studio (SSMS)
1. Andare alla pagina di download per[SQL Server 2014 Express](http://www.microsoft.com/download/details.aspx?id=42299)fare clic su**Download**quindi scegliere la versione a 32 bit (x86) o la versione a 64 bit (x64) del download MgmtStudio.

	![MgtmtStudio32BIT o MgmtStudio64BIT][1]
2.	Seguire le istruzioni visualizzate durante l'installazione di SQL Server Management Studio utilizzando le impostazioni predefinite.
3.	Una volta scaricati, eseguire la ricerca di SQL Server 2014 Management Studio sul computer e avviare SQL Server Management Studio.


## Connettersi al database SQL
1. Aprire SQL Server Management Studio.
2. Nella finestra **Connetti al server**, nella casella **Nome server**, digitare il nome del server nel formato *&lt;servername>*.**database.windows.net**
3. Nella casella con menu a discesa **Autenticazione** selezionare **Autenticazione di SQL Server**.
4. Immettere il **nome di accesso** e la **password**specificati quando è stato creato il server di Database SQL.

	![Finestra di dialogo Connetti al server][2]
5. Fare clic sul pulsante **Opzioni**.
6. Nella casella **Connetti al database** immettere **AdventureWorks**e fare clic su **Connetti**.

	![Connetti al database][3]

### Se la connessione ha esito negativo
Assicurarsi che il firewall del server logico che è stato creato consenta le connessioni dal computer locale. Vedere [Procedura: configurare le impostazioni del firewall (Database SQL di Azure)](https://msdn.microsoft.com/library/azure/jj553530.aspx).

## Eseguire query di esempio

1. In **Esplora** passare al database **AdventureWorks**.
2. Fare clic sul database e selezionare fare clic su **Nuova Query**.

	![Nuova query][4]
3. Nella finestra della query appena aperta, copiare e incollare il codice seguente:

		SELECT 
		CustomerId
		,Title
		,FirstName
		,LastName
		,CompanyName
		FROM SalesLT.Customer;

4. Fare clic sul pulsante **Esegui**. Se l'operazione ha esito positivo, si otterrà un risultato analogo al seguente:![Operazione riuscita][5]


## Passaggi successivi
Per creare o gestire i database è possibile usare istruzioni Transact-SQL. Vedere [CREATE DATABASE (Database di SQL Azure)](https://msdn.microsoft.com/library/dn268335.aspx) e [Gestione di database SQL di Azure tramite SQL Server Management Studio](sql-database-manage-azure-ssms.md). È anche possibile registrare eventi nell'archiviazione di Azure. Vedere [Introduzione al controllo del database SQL](sql-database-auditing-get-started.md).

<!--Image references-->

[1]: ./media/sql-database-connect-to-database/1-download.png
[2]: ./media/sql-database-connect-to-database/2-connect.png
[3]: ./media/sql-database-connect-to-database/3-connect-to-database.png
[4]: ./media/sql-database-connect-to-database/4-run-query.png
[5]: ./media/sql-database-connect-to-database/5-success.png
 

<!---HONumber=August15_HO6-->