<properties
	pageTitle="Procedura: configurare le impostazioni del firewall | Microsoft Azure"
	description="Come configurare il firewall per gli indirizzi IP che accedono a database SQL di Azure."
	services="sql-database"
	documentationCenter=""
	authors="BYHAM"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article" 
	ms.date="09/04/2015"
	ms.author="rickbyh"/>


# Procedura: configurare le impostazioni del firewall su Database SQL


Il database SQL di Microsoft Azure utilizza le regole del firewall per consentire le connessioni a server e database. È possibile definire le impostazioni del firewall a livello di server e a livello di database per un database master o utente nel server del database SQL di Azure per consentire l'accesso al database in modo selettivo.

> [AZURE.IMPORTANT]Per consentire alle applicazioni da Azure di stabilire la connessione al server di database, è necessario abilitare le connessioni da Azure. Per ulteriori informazioni sulle regole del firewall e l'abilitazione delle connessioni da Azure, vedere [Firewall di database SQL di Azure](sql-database-firewall-configure.md). È necessario aprire alcune porte TCP aggiuntive se si apportano le connessioni all'interno del cloud di Azure. Per ulteriori informazioni, vedere la sezione **V12 del database SQL: all'esterno rispetto all'interno** di [porte 1433 per 4.5 ADO.NET e SQL Database V12](sql-database-develop-direct-route-ports-adonet-v12.md)


## Regole del firewall a livello di server

Le regole del firewall a livello di server possono essere create e gestite tramite il portale di gestione di Microsoft Azure, Transact-SQL, Azure PowerShell o API REST.

### Gestione delle regole del firewall a livello di server tramite il nuovo portale di Azure


[AZURE.INCLUDE [sql-database-include-ip-address-22-v12portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]


## Gestione delle regole del firewall a livello di server tramite il Portale di gestione 

1. Nel Portale di gestione fare clic su **Database SQL**. Di seguito sono elencati tutti i database e i server corrispondenti.
2. Fare clic su **Servers** nella parte superiore della pagina.
3. Fare clic sulla freccia accanto al server per il quale si desidera gestire le regole del firewall.
4. Fare clic su **Configure** nella parte superiore della pagina.

	*  Per aggiungere il computer corrente, fare clic su Aggiungi agli indirizzi IP consentiti.
	*  Per aggiungere ulteriori indirizzi IP, digitare nome della regola, indirizzo IP iniziale e indirizzo IP finale.
	*  Per modificare una regola esistente, fare clic su uno dei campi nella regola e modificare.
	*  Per eliminare una regola esistente, passare il mouse sulla regola fino a quando non viene visualizzata la X alla fine della riga. Fare clic su X per rimuovere la regola.
5. Per salvare le modifiche, fare clic su **Salva** nella parte inferiore della pagina.

## Gestire le regole del firewall a livello di server tramite Transact-SQL

1. Avviare una finestra di query tramite il Portale di gestione o tramite SQL Server Management Studio.
2. Verificare di essere connessi al database master.
3. Le regole del firewall a livello di server possono essere selezionate, create, aggiornate o eliminate all'interno della finestra di query.
4. Per creare o aggiornare le regole del firewall a livello di server, eseguire la stored procedure della regola sp\_set\_firewall. Nell'esempio seguente viene abilitato un intervallo di indirizzi IP nel server di Contoso.<br/>Iniziare verificando quali regole esistono già.

		SELECT * FROM sys.database_firewall_rules ORDER BY name;

	Successivamente, aggiungere una regola del firewall.

		EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
			@start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'

	Per eliminare una regola del firewall a livello di server, eseguire la stored procedure sp\_delete\_firewall\_rule. Nell'esempio seguente viene eliminata la regola denominata ContosoFirewallRule.
 
		EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
 
## Gestire le regole firewall a livello di Server tramite Azure PowerShell
1. Avviare Azure PowerShell.
2. Le regole del firewall a livello di server possono essere create, aggiornate ed eliminate tramite Azure PowerShell. 

	Per creare una nuova regola del firewall a livello di server, eseguire il cmdlet New-AzureSqlDatabaseServerFirewallRule. Nell'esempio seguente viene abilitato un intervallo di indirizzi IP nel server di Contoso.
 
		New-AzureSqlDatabaseServerFirewallRule –StartIPAddress 192.168.1.1 –EndIPAddress 192.168.1.10 –RuleName ContosoFirewallRule –ServerName Contoso
 
	Per modificare una regola firewall a livello di server esistente, eseguire il cmdlet Set-AzureSqlDatabaseServerFirewallRule. Nell'esempio seguente viene modificato l'intervallo di indirizzi IP validi per la regola denominata ContosoFirewallRule.
 
		Set-AzureSqlDatabaseServerFirewallRule –StartIPAddress 192.168.1.4 –EndIPAddress 192.168.1.10 –RuleName ContosoFirewallRule –ServerName Contoso

	Per eliminare una regola del firewall a livello di server esistente, eseguire il cmdlet Remove-AzureSqlDatabaseServerFirewallRule. Nell'esempio seguente viene eliminata la regola denominata ContosoFirewallRule.

		Remove-AzureSqlDatabaseServerFirewallRule –RuleName ContosoFirewallRule –ServerName Contoso
 
## Gestione delle regole del firewall a livello di server tramite l'API REST
1. La gestione delle regole del firewall tramite l'API REST deve essere autenticata. Per informazioni, vedere Autenticazione di richieste di gestione del servizio.
2. È possibile creare, aggiornare o eliminare regole a livello di server tramite l'API REST

	Per creare o aggiornare una regola del firewall a livello di server, eseguire il metodo POST con le seguenti operazioni:
 
		https://management.core.windows.net:8443/{subscriptionId}/services/sqlservers/servers/Contoso/firewallrules
	
	Request Body

		<ServiceResource xmlns="http://schemas.microsoft.com/windowsazure">
		  <Name>ContosoFirewallRule</Name>
		  <StartIPAddress>192.168.1.4</StartIPAddress>
		  <EndIPAddress>192.168.1.10</EndIPAddress>
		</ServiceResource>
 

	Per rimuovere una regola del firewall a livello di server esistente, eseguire il metodo DELETE con le seguenti operazioni:
	 
		https://management.core.windows.net:8443/{subscriptionId}/services/sqlservers/servers/Contoso/firewallrules/ContosoFirewallRule
 
## Regole del firewall a livello di database

1. Dopo la creazione di un firewall a livello di server per l'indirizzo IP, avviare una finestra di query tramite il Portale di gestione o tramite SQL Server Management Studio.
2. Connettersi al database per cui si desidera creare una regola del firewall a livello di database.

	Per creare una nuova regola del firewall a livello di database o aggiornarne una esistente, eseguire la stored procedure sp\_set\_database\_firewall\_rule. Nell'esempio seguente viene creata una nuova regola del firewall denominata ContosoFirewallRule.
 
		EXEC sp_set_database_firewall_rule @name = N'ContosoFirewallRule', @start_ip_address = '192.168.1.11', @end_ip_address = '192.168.1.11'
 
	Per eliminare una regola del firewall a livello di database esistente, eseguire la stored procedure sp\_delete\_database\_firewall\_rule. Nell'esempio seguente viene eliminata la regola denominata ContosoFirewallRule.
 
		EXEC sp_delete_database_firewall_rule @name = N'ContosoFirewallRule'


## Gestione delle regole del firewall tramite l'API REST di gestione del servizio

* [Creare la regola del firewall](https://msdn.microsoft.com/library/azure/dn505712.aspx)
* [Eliminare la regola del firewall](https://msdn.microsoft.com/library/azure/dn505706.aspx)
* [Recuperare la regola del firewall](https://msdn.microsoft.com/library/azure/dn505698.aspx)
* [Elencare le regole del firewall](https://msdn.microsoft.com/library/azure/dn505715.aspx)

## Gestione delle regole del firewall tramite PowerShell

* [New-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546724.aspx)
* [Remove-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546727.aspx)
* [Set-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546739.aspx)
* [Get-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546731.aspx)
 
## Passaggi successivi

Per un'esercitazione sulla creazione di un database, vedere [Creare il primo database SQL di Azure](sql-database-get-started.md). Per informazioni sulla connessione a un database SQL di Azure da applicazioni open-source o di terze parti, vedere [Linee guida per la connessione al database SQL di Azure a livello di programmazione](https://msdn.microsoft.com/library/azure/ee336282.aspx). Per comprendere come spostarsi ai database vedere [Gestione di database e account di accesso in database SQL di Azure](https://msdn.microsoft.com/library/azure/ee336235.aspx).

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->

<!---HONumber=Oct15_HO3-->