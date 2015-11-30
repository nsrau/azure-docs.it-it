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
	ms.date="11/13/2015"
	ms.author="rickbyh"/>


# Procedura: configurare le impostazioni del firewall su Database SQL mediante il portale di Azure


> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [REST API](sql-database-configure-firewall-settings-rest.md)


Il database SQL di Microsoft Azure utilizza le regole del firewall per consentire le connessioni a server e database. È possibile definire le impostazioni del firewall a livello di server e a livello di database per un database master o utente nel server del database SQL di Azure per consentire l'accesso al database in modo selettivo.

> [AZURE.IMPORTANT]Per consentire alle applicazioni da Azure di stabilire la connessione al server di database, è necessario abilitare le connessioni da Azure. Per ulteriori informazioni sulle regole del firewall e l'abilitazione delle connessioni da Azure, vedere [Firewall di database SQL di Azure](sql-database-firewall-configure.md). È necessario aprire alcune porte TCP aggiuntive se si apportano le connessioni all'interno del cloud di Azure. Per ulteriori informazioni, vedere la sezione **V12 del database SQL: all'esterno rispetto all'interno** di [porte 1433 per 4.5 ADO.NET e SQL Database V12](sql-database-develop-direct-route-ports-adonet-v12.md)


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


## Passaggi successivi

Per un'esercitazione sulla creazione di un database, vedere [Creare il primo database SQL di Azure](sql-database-get-started.md). Per informazioni sulla connessione a un database SQL di Azure da applicazioni open-source o di terze parti, vedere [Linee guida per la connessione al database SQL di Azure a livello di programmazione](https://msdn.microsoft.com/library/azure/ee336282.aspx). Per comprendere come spostarsi ai database vedere [Gestione di database e account di accesso in database SQL di Azure](https://msdn.microsoft.com/library/azure/ee336235.aspx).

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->

<!---HONumber=Nov15_HO4-->