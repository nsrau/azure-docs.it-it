<properties
	pageTitle="Procedura: Configurare un firewall del database SQL | Microsoft Azure"
	description="Come configurare il firewall per gli indirizzi IP che accedono a database SQL di Azure."
	services="sql-database"
	documentationCenter=""
	authors="BYHAM"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article" 
	ms.date="04/26/2016"
	ms.author="rickbyh"/>


# Procedura: Configurare un firewall del database SQL di Azure con TSQL


> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [API REST](sql-database-configure-firewall-settings-rest.md)


Il database SQL di Microsoft Azure utilizza le regole del firewall per consentire le connessioni a server e database. È possibile definire le impostazioni del firewall a livello di server e a livello di database per un database master o utente nel server del database SQL di Azure per consentire l'accesso al database in modo selettivo.

> [AZURE.IMPORTANT] Per consentire alle applicazioni da Azure di stabilire la connessione al server di database, è necessario abilitare le connessioni da Azure. Per ulteriori informazioni sulle regole del firewall e l'abilitazione delle connessioni da Azure, vedere [Firewall di database SQL di Azure](sql-database-firewall-configure.md). È necessario aprire alcune porte TCP aggiuntive se si apportano le connessioni all'interno del cloud di Azure. Per ulteriori informazioni, vedere la sezione **V12 del database SQL: all'esterno rispetto all'interno** di [porte 1433 per 4.5 ADO.NET e SQL Database V12](sql-database-develop-direct-route-ports-adonet-v12.md)


## Gestire le regole del firewall a livello di server tramite Transact-SQL

Solo l'account di accesso dell'entità di livello server e l'amministratore di Azure Active Directory possono creare una regola firewall a livello di server con Transact-SQL.

1. Avviare una finestra di query e connettersi al database master virtuale con SQL Server Management Studio.
2. Le regole del firewall a livello di server possono essere selezionate, create, aggiornate o eliminate all'interno della finestra di query.
3. Per creare o aggiornare le regole del firewall a livello di server, eseguire la stored procedure della regola sp\_set\_firewall. Nell'esempio seguente viene abilitato un intervallo di indirizzi IP nel server di Contoso.<br/>Iniziare verificando quali regole esistono già.

		SELECT * FROM sys.firewall_rules ORDER BY name;

	Successivamente, aggiungere una regola del firewall.

		EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
			@start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'

	Per eliminare una regola del firewall a livello di server, eseguire la stored procedure sp\_delete\_firewall\_rule. Nell'esempio seguente viene eliminata la regola denominata ContosoFirewallRule.
 
		EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
 
 Per altre informazioni su queste stored procedure, vedere [sp\_set\_firewall\_rule](https://msdn.microsoft.com/library/dn270017.aspx) e [sp\_delete\_firewall\_rule](https://msdn.microsoft.com/library/dn270024.aspx).

## Regole del firewall a livello di database

Solo un utente del database con l'autorizzazione **CONTROL** sul database, ad esempio il proprietario del database, può creare una regola firewall a livello di database.

1. Dopo la creazione di un firewall a livello di server per l'indirizzo IP, avviare una finestra di query tramite il portale classico o tramite SQL Server Management Studio.
2. Connettersi al database per cui si desidera creare una regola del firewall a livello di database.

	Per creare una nuova regola del firewall a livello di database o aggiornarne una esistente, eseguire la stored procedure sp\_set\_database\_firewall\_rule. Nell'esempio seguente viene creata una nuova regola del firewall denominata ContosoFirewallRule.
 
		EXEC sp_set_database_firewall_rule @name = N'ContosoFirewallRule', 
		    @start_ip_address = '192.168.1.11', @end_ip_address = '192.168.1.11'
 
	Per eliminare una regola del firewall a livello di database esistente, eseguire la stored procedure sp\_delete\_database\_firewall\_rule. Nell'esempio seguente viene eliminata la regola denominata ContosoFirewallRule.
 
		EXEC sp_delete_database_firewall_rule @name = N'ContosoFirewallRule'

Per altre informazioni su queste stored procedure, vedere [sp\_set\_database\_firewall\_rule](https://msdn.microsoft.com/library/dn270010.aspx) e [sp\_delete\_database\_firewall\_rule](https://msdn.microsoft.com/library/dn270030.aspx).

## Passaggi successivi

Per un'esercitazione sulla creazione di un database, vedere [Creare un database SQL in pochi minuti usando il portale di Azure](sql-database-get-started.md). Per informazioni sulla connessione a un database SQL di Azure da applicazioni open-source o di terze parti, vedere [Esempi di codice di avvio rapido del client per il database SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx). Per informazioni su come passare ai database, vedere [Gestire l'accesso al database e la sicurezza degli account di accesso](https://msdn.microsoft.com/library/azure/ee336235.aspx).

<!---HONumber=AcomDC_0511_2016-->