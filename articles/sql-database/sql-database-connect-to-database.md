<properties
	pageTitle="Come connettersi a un database SQL di Azure tramite SQL Server Management Studio" 
	description="Informazioni su come connettersi a un database SQL di Azure tramite SQL Server Management Studio."
	services="sql-database"
	documentationCenter=""
	authors="sidneyh" solutions=""
	manager="jhubbard" editor="" />

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="06/25/2015"
	ms.author="sidneyh" />

# Come connettersi a un database SQL di Azure con SQL Server Management Studio

Questo argomento illustra la procedura per la connessione a un database SQL di Microsoft Azure tramite SQL Server Management Studio (SSMS).

## Prerequisiti
* Database SQL di Azure implementato e in esecuzione. Per creare un nuovo database SQL, vedere l'argomento [introduttivo al database SQL di Microsoft Azure](sql-database-get-started.md).
* Immettere il nome e la password dell'amministratore per il database SQL.
* SQL Server Management Studio 2014. Per ottenere lo strumento, vedere l'articolo di blog relativo al [download di SQL Express](http://www.hanselman.com/blog/DownloadSQLServerExpress.aspx).
* Configurazione delle impostazioni del firewall per il database. Vedere [Procedura: configurare le impostazioni del firewall (Database SQL di Azure)](sql-database-configure-firewall-settings.md)

## Per connettersi a un'istanza del database SQL
1. Accedere al [portale di gestione di Azure](https://portal.azure.com).
2. Fare clic sul pulsante **Sfoglia** e quindi su **Database SQL** (b).

	![Fare clic su Sfoglia e Database SQL][1]
3. Con **Database SQL** selezionato (a), fare clic sul nome di un database nel server a cui si desidera connettersi (b).

	![Fare clic sul nome di un database][2]
4. Con il nome selezionato (a), fare clic su Proprietà (b). Copiare il nome del server (c) e il nome dell'amministratore (d). Il nome dell'amministratore e la password vengono creati al momento della creazione del database SQL. È necessario conoscere la password per proseguire con il passaggio successivo.

	![Fare clic su SQL Server, Impostazioni e Proprietà][3]
5. Aprire SQL Server Management Studio 2014.
6. Nella finestra di dialogo Connetti al server incollare il nome del server nella casella **Nome server** (a). Impostare Autenticazione su **Autenticazione di SQL Server** (b). Incollare il nome dell'amministratore del server nella casella **Accesso** (c) e quindi digitare la password (d). Fare clic su **Opzioni** (e).

	![Finestra di dialogo di accesso di SQL Server Management Studio][4]
7. Nella scheda Proprietà connessione impostare la casella **Connetti al database** su **master** (o su qualsiasi altro database a cui si desidera connettersi). Fare clic su **Connetti**.

	![Impostare su master e fare clic su Connetti][5]

## Risoluzione dei problemi di connessione

In caso di problemi, vedere [Risoluzione dei problemi di connessione nel database SQL di Azure](https://support.microsoft.com/kb/2980233/). Per un elenco di possibili problemi e risposte, vedere la procedura per la [risoluzione dei problemi di connettività del database SQL di Microsoft Azure](https://support2.microsoft.com/common/survey.aspx?scid=sw;en;3844&showpage=1).


## Passaggi successivi
Per creare o gestire i database è possibile usare istruzioni Transact-SQL. Vedere [CREATE DATABASE (Database di SQL Azure)](https://msdn.microsoft.com/library/dn268335.aspx) e [Gestione di database SQL di Azure tramite SQL Server Management Studio](sql-database-manage-azure-ssms.md). È anche possibile registrare eventi nell'archiviazione di Azure. Vedere [Introduzione al controllo del database SQL](sql-database-auditing-get-started.md).

<!--Image references-->

[1]: ./media/sql-database-connect-to-database/browse-vms.png
[2]: ./media/sql-database-connect-to-database/sql-databases.png
[3]: ./media/sql-database-connect-to-database/blades.png
[4]: ./media/sql-database-connect-to-database/ssms-connect-to-server.png
[5]: ./media/sql-database-connect-to-database/ssms-master.png
 

<!---HONumber=July15_HO2-->