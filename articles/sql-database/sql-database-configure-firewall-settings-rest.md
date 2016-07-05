<properties
	pageTitle="Configurare regole firewall a livello di server del database SQL di Azure tramite l'API REST | Microsoft Azure"
	description="Come configurare il firewall per gli indirizzi IP che accedono a database SQL di Azure."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article" 
	ms.date="06/15/2016"
	ms.author="sstein"/>


#  Configurare regole firewall a livello di server del database SQL di Azure tramite l'API REST


> [AZURE.SELECTOR]
- [Panoramica](sql-database-firewall-configure.md)
- [Portale di Azure](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [API REST](sql-database-configure-firewall-settings-rest.md)


Il database SQL di Microsoft Azure utilizza le regole del firewall per consentire le connessioni a server e database. È possibile definire le impostazioni del firewall a livello di server e a livello di database per un database master o utente nel server del database SQL di Azure per consentire l'accesso al database in modo selettivo.

> [AZURE.IMPORTANT] Per consentire alle applicazioni da Azure di stabilire la connessione al server di database, è necessario abilitare le connessioni da Azure. Per ulteriori informazioni sulle regole del firewall e l'abilitazione delle connessioni da Azure, vedere [Firewall di database SQL di Azure](sql-database-firewall-configure.md). È necessario aprire alcune porte TCP aggiuntive se si apportano le connessioni all'interno del cloud di Azure. Per ulteriori informazioni, vedere la sezione **V12 del database SQL: all'esterno rispetto all'interno** di [porte 1433 per 4.5 ADO.NET e SQL Database V12](sql-database-develop-direct-route-ports-adonet-v12.md)


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


## Gestione delle regole del firewall tramite l'API REST di gestione del servizio

* [Creare la regola del firewall](https://msdn.microsoft.com/library/azure/dn505712.aspx)
* [Eliminare la regola del firewall](https://msdn.microsoft.com/library/azure/dn505706.aspx)
* [Recuperare la regola del firewall](https://msdn.microsoft.com/library/azure/dn505698.aspx)
* [Elencare le regole del firewall](https://msdn.microsoft.com/library/azure/dn505715.aspx)
 
## Passaggi successivi

L'articolo [Configurare le regole firewall a livello di server e di database per il database SQL di Azure tramite T-SQL](sql-database-configure-firewall-settings-tsql.md) illustra come usare Transact-SQL per creare regole firewall a livello di server e di database.

Per informazioni su come creare regole firewall a livello di server con altri metodi, vedere:

- [Configurare regole firewall a livello di server del database SQL di Azure tramite il portale di Azure](sql-database-configure-firewall-settings.md)
- [Configurare le regole firewall a livello di server per il database SQL di Azure con PowerShell](sql-database-configure-firewall-settings-powershell.md)

Per un'esercitazione sulla creazione di un database, vedere [Creare un database SQL in pochi minuti usando il portale di Azure](sql-database-get-started.md). Per informazioni sulla connessione a un database SQL di Azure da applicazioni open source o di terze parti, vedere [Esempi di codice di avvio rapido del client per il database SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx). Per informazioni su come passare ai database, vedere [Gestire l'accesso al database e la sicurezza degli account di accesso](https://msdn.microsoft.com/library/azure/ee336235.aspx).


## Risorse aggiuntive

- [Protezione del database](sql-database-security.md)
- [Centro sicurezza per il motore di Database di SQL Server e il Database SQL di Azure](https://msdn.microsoft.com/library/bb510589)

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->

 

<!---HONumber=AcomDC_0622_2016-->