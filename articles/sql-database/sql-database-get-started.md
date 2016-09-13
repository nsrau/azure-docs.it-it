<properties
	pageTitle="Esercitazione sul database SQL: creare un database SQL | Microsoft Azure"
	description="L'articolo fornisce informazioni su come configurare un server logico del database SQL, una regola firewall del server, un database SQL e dati di esempio. Fornisce anche informazioni su come connettersi con strumenti client, configurare utenti e impostare una regola firewall del database."
	keywords="esercitazione sul database sql, creare un database sql"
	services="sql-database"
	documentationCenter=""
	authors="CarlRabeler"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="07/05/2016"
	ms.author="carlrab"/>


# Esercitazione sul database SQL: Creare un database SQL in pochi minuti usando il portale di Azure

**Database singolo**

> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)

In questa esercitazione si apprenderà come usare il portale di Azure per:

- Creare un server logico del database SQL di Azure in cui ospitare i database SQL.
- Creare un database SQL senza dati, con dati di esempio o con dati da un backup del database SQL.
- Creare una regola del firewall a livello di server per un singolo indirizzo IP o per un intervallo di indirizzi IP.

Per eseguire le stesse attività è possibile usare [C#](sql-database-get-started-csharp.md) o [PowerShell](sql-database-get-started-powershell.md).

[AZURE.INCLUDE [Login](../../includes/azure-getting-started-portal-login.md)]

<a name="create-logical-server-bk"></a>

[AZURE.INCLUDE [Creare un server logico del database SQL](../../includes/sql-database-create-new-server-portal.md)]

[AZURE.INCLUDE [Creare un database SQL](../../includes/sql-database-create-new-database-portal.md)]

[AZURE.INCLUDE [Creare un database SQL](../../includes/sql-database-create-new-server-firewall-portal.md)]

## Passaggi successivi
Al termine dell'esercitazione sul database SQL e dopo aver creato un database con alcuni dati di esempio, è possibile iniziare l'esplorazione con gli strumenti preferiti.

- Se si ha familiarità con Transact-SQL e SQL Server Management Studio (SSMS), vedere [Connettersi al database SQL con SQL Server Management Studio ed eseguire una query T-SQL di esempio](sql-database-connect-query-ssms.md).

- Se si ha familiarità con Excel, vedere l'esercitazione [Connettere Excel a un database SQL di Azure e creare un report](sql-database-connect-excel.md).

- Se si è pronti per iniziare a scrivere codice, scegliere il linguaggio di programmazione in [Raccolte di connessioni per database SQL e SQL Server](sql-database-libraries.md).

- Per spostare i database SQL Server locali in Azure, vedere [Migrazione di un database SQL Server al database SQL nel cloud](sql-database-cloud-migrate.md).

- Per caricare alcuni dati in una nuova tabella da un file CSV usando lo strumento da riga di comando BCP, vedere [Caricare dati da CSV in Azure SQL Data Warehouse (file flat)](sql-database-load-from-csv-with-bcp.md).


## Risorse aggiuntive

[Informazioni sul database SQL](sql-database-technical-overview.md)

<!---HONumber=AcomDC_0907_2016-->