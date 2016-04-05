<properties
	pageTitle="Connettersi a un database SQL - SQL Server Management Studio | Microsoft Azure"
	description="Informazioni su come connettersi a un database SQL in Azure tramite SQL Server Management Studio (SSMS). Eseguire quindi una query di esempio usando Transact-SQL (T-SQL)."
	metaCanonical=""
	keywords="connettersi al database sql,sql server management studio"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor="" />

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="03/25/2016"
	ms.author="sstein" />

# Connettersi al database SQL con SQL Server Management Studio ed eseguire una query T-SQL di esempio

> [AZURE.SELECTOR]
- [C#](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

Questo articolo illustra come connettersi a un database SQL di Azure tramite SQL Server Management Studio (SSMS) ed eseguire una query semplice con istruzioni Transact-SQL (T-SQL).

È necessario innanzitutto un database SQL in Azure. È possibile crearne uno rapidamente con le istruzioni disponibili in [Introduzione al database SQL di Microsoft Azure](sql-database-get-started.md). Gli esempi qui presenti si basano sul database di esempio AdventureWorks creato nell'articolo sopra riportato, ma la stessa procedura, fino all'esecuzione della query, si applica a qualsiasi database SQL.

## Installare e avviare SQL Server Management Studio (SSMS)

Quando si usa il database SQL, è necessario usare la versione più recente di SSMS. Per ottenerla, visitare la pagina di [download di SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx). Con la versione più recente, SSMS notifica automaticamente l'utente quando è disponibile un aggiornamento più recente.

## Avviare SSMS e connettersi al server di database SQL

1. Digitare "Microsoft SQL Server Management Studio" nella casella di ricerca di Windows e quindi fare clic sull'applicazione desktop per avviare SSMS.
2. Nella finestra di dialogo **Connetti al server** digitare nella casella **Nome server** il nome del server che ospita il database SQL nel formato *&lt;nomeserver>*.**database.windows.net**.
3. Nella casella **Autenticazione** selezionare **Autenticazione di SQL Server**.
4. Digitare l'**Account di accesso** e la **Password** specificati durante la creazione del server e quindi fare clic su **Connetti** per connettersi al database SQL.

	![SQL Server Management Studio: Connettersi a un server di database SQL](./media/sql-database-connect-query-ssms/1-connect.png)

5. Se non è già stata stabilita una connessione ad Azure, SQL Server Management Studio richiederà le informazioni di accesso di Azure.

### Se la connessione ha esito negativo

I motivi più comuni per cui si verificano errori di connessione sono gli errori nel nome del server, tenendo presente che <*nomeserver*> è il nome del server logico, non del database, gli errori nel nome utente o nella password, nonché il blocco delle connessioni da parte del server per motivi di sicurezza. Se ci si connette per la prima volta o la connessione non riesce a causa di una configurazione IP modificata, la [versione più recente di SSMS](https://msdn.microsoft.com/library/mt238290.aspx) richiede l'account di accesso di Azure e quindi crea la regola del firewall in Azure. Se si usa una versione precedente, l'indirizzo IP viene segnalato in un messaggio di errore ed è necessario aggiungerlo alla regola del firewall del server in Azure. Assicurarsi che le impostazioni del firewall del server consentano le connessioni dall'indirizzo IP del computer locale e dall'indirizzo IP usato dal client di SSMS. In alcuni casi i due indirizzi IP sono diversi. Per altre informazioni, vedere [Procedura: Configurare le impostazioni del firewall (Database SQL di Azure)](sql-database-configure-firewall-settings.md).

## Eseguire query di esempio

Dopo la connessione al server logico, è possibile connettersi a un database ed eseguire una query di esempio. Se il database non è stato creato usando l'esempio AdventureWorks in [Creare il primo database SQL di Microsoft Azure](sql-database-get-started.md), questa query non funzionerà. Per altre informazioni, passare direttamente ai passaggi successivi.

1. In **Esplora oggetti** passare al database **AdventureWorks**.
2. Fare clic con il pulsante destro del mouse sul database e selezionare **Nuova query**.

	![Nuova query. Connettersi a un server di database SQL: SQL Server Management Studio](./media/sql-database-connect-query-ssms/4-run-query.png)

3. Nella finestra della query appena aperta, copiare e incollare il codice seguente:

		SELECT
		CustomerId
		,Title
		,FirstName
		,LastName
		,CompanyName
		FROM SalesLT.Customer;

4. Fare clic sul pulsante **Esegui**. Nella schermata seguente viene illustrata una query con esito positivo.

	![Operazione riuscita. Connettersi a un server di database SQL: SQL Server Management Studio](./media/sql-database-connect-query-ssms/5-success.png)

## Passaggi successivi

È possibile usare istruzioni T-SQL per creare e gestire i database in Azure in modo analogo a SQL Server. Se si ha familiarità con l'uso di T-SQL con SQL Server, vedere [Informazioni su Transact-SQL del Database SQL di Azure](sql-database-transact-sql-information.md) per un riepilogo delle differenze.

Se non si ha familiarità con T-SQL, vedere [Esercitazione: Scrittura di istruzioni Transact-SQL](https://msdn.microsoft.com/library/ms365303.aspx) e [Guida di riferimento a Transact-SQL (Motore di database)](https://msdn.microsoft.com/library/bb510741.aspx).

<!---HONumber=AcomDC_0330_2016-->