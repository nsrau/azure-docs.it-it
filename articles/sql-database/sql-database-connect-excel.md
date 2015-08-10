<properties
	pageTitle="Connettersi a un database SQL di Azure con Excel"
	description="Foglio di calcolo Excel al Database SQL Azure per l'esplorazione dei dati e creazione di report."
	services="sql-database"
	documentationCenter=""
	authors="joseidz"
	manager="joseidz"
	editor="joseidz"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/09/2015"
	ms.author="joseidz"/>


# Connettersi a un database SQL di Azure con Excel
Connessione di Excel a un Database di SQL Azure e creare un report sui dati nel database.

## Prerequisiti
- Database SQL di Azure implementato e in esecuzione. Per creare un nuovo database SQL, vedere l'argomento [introduttivo al database SQL di Microsoft Azure](sql-database-get-started.md).
- [Microsoft Excel 2013](https://products.office.com/it-it/)(o Microsoft Excel 2010)

## Connettersi al Database SQL e creare il Report
1.	Aprire Excel.
2.	Nella barra dei menu nella parte superiore della pagina fare clic su**Dati**.
3.	Fare clic su**Da altre fonti**quindi**da SQL Server**. Si apre la**Guida Connessione dati**.

	![Connessione dati guidata][1]
4.	Nella casella del **nome Server**digitare il nome del server di Database SQL di Azure. Esempio:

	 	adventureserver.database.windows.net
5.	Nella sezione **credenziali di accesso**selezionare**Utilizzare il nome utente e Password seguenti**e quindi digitare le credenziali appropriate per il server di Database SQL. Quindi fare clic su **Next**.

	Nota: Entrambi gli add-ins[PowerPivot](https://www.microsoft.com/download/details.aspx?id=102)e[Power Query](https://www.microsoft.com/download/details.aspx?id=39379)aggiuntivi per Excel hanno esperienze simili

6. Nel**Database e tabella**di dialogo selezionati, selezionare il**AdventureWorks**del database dal menu a discesa e selezionare**vGetAllCategories**dall'elenco di tabelle e viste, fare clic su**Avanti**.

	![Selezionare database e tabella][5]
7. Nel**Salva File di connessione dati e di fine**finestra di dialogo, fare clic su**Fine**.
8. Nel**Importazione dei dati**finestra di dialogo, seleziona**grafico pivot**e fare clic su**OK**.

	![Selezionare l'importazione dei dati][2]
9. Nella finestra di **PivotChart Fields**, selezionare la configurazione seguente per creare un report per il conteggio dei prodotti per categoria.

	![Configurazione][3]
10.	Esito positivo è simile al seguente:

	![esito positivo][4]

## Passaggi successivi

Se si è uno sviluppatore di servizio Software (SaaS), per ulteriori informazioni, vedere[pool di database elastici](sql-database-elastic-pool.md). È possibile gestire con facilità grandi raccolte di database utilizzando[processi di database elastici](sql-database-elastic-jobs-overview.md).

<!--Image references-->
[1]: ./media/sql-database-connect-excel/connect-to-database-server.png
[2]: ./media/sql-database-connect-excel/import-data.png
[3]: ./media/sql-database-connect-excel/power-pivot.png
[4]: ./media/sql-database-connect-excel/power-pivot-results.png
[5]: ./media/sql-database-connect-excel/select-database-and-table.png

<!---HONumber=July15_HO5-->