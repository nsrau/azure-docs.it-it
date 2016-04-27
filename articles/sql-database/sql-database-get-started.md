<properties
	pageTitle="Esercitazione sul database SQL: creare un database SQL | Microsoft Azure"
	description="Informazioni su come configurare un server logico di database SQL, una regola firewall del server, un database SQL, usare dati di esempio, connettersi con strumenti client, configurare gli utenti e una regola firewall del database."
	keywords="esercitazione sul database sql,creare un database sql"
	services="sql-database"
	documentationCenter=""
	authors="carlrabeler"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="04/14/2016"
	ms.author="carlrab"/>

# Esercitazione sul database SQL: Creare un database SQL in pochi minuti usando il portale di Azure

**Database singolo**

> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)

In questa esercitazione si apprenderà come usare il portale di Azure per:

- Creare un server logico di database SQL per ospitare i database SQL
- Creare un database SQL senza dati, con dati di esempio o con dati da un backup del database SQL.
- Creare una regola del firewall a livello di server per un singolo indirizzo IP o per un intervallo di indirizzi IP.

Usare questi collegamenti per eseguire le stesse attività usando [C#](sql-database-get-started-csharp.md) o [PowerShell](sql-database-get-started-powershell.md).

[AZURE.INCLUDE [Login](../../includes/azure-getting-started-portal-login.md)]

[AZURE.INCLUDE [Creare un server logico del database SQL](../../includes/sql-database-create-new-server-portal.md)]

[AZURE.INCLUDE [Creare un database SQL](../../includes/sql-database-create-new-database-portal.md)]

[AZURE.INCLUDE [Creare un database SQL](../../includes/sql-database-create-new-server-firewall-portal.md)]

## Passaggi successivi
Al termine dell'esercitazione sul database SQL e dopo aver creato un database con alcuni dati di esempio, è possibile iniziare l'esplorazione con i propri strumenti preferiti.

- Se si ha familiarità con Transact-SQL e SQL Server Management Studio, vedere l'articolo che illustra come [connettersi a un database SQL ed eseguire query con SQL Server Management Studio](sql-database-connect-query-ssms.md).

- Se si ha familiarità con Excel, vedere l'articolo che illustra come [connettersi al database SQL con Excel](sql-database-connect-excel.md).

- Se si ha familiarità con la scrittura di codice, vedere [Connettersi al database SQL con C# ed eseguire query](sql-database-connect-query.md) e [Uso del database SQL da .NET (C#)](sql-database-develop-dotnet-simple.md). Vedere anche [Esempi di codice di avvio rapido per il database SQL](sql-database-develop-quick-start-client-code-samples.md) per Node.js, Python, Ruby, Java e PHP, gli esempi e le procedure per C++, oltre a quelli per C#.

- Per spostare i database SQL Server locali in Azure, vedere l'articolo relativo alla [migrazione di un database al database SQL di Azure](sql-database-cloud-migrate.md).


## Risorse aggiuntive

[Panoramica del database SQL](sql-database-technical-overview.md)

<!---HONumber=AcomDC_0420_2016-->