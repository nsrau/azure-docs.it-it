<properties
	pageTitle="Procedura: configurare le impostazioni del firewall | Microsoft Azure"
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
	ms.date="04/12/2016"
	ms.author="rickbyh;carlrab"/>


# Procedura: configurare le impostazioni del firewall su Database SQL mediante il portale di Azure


> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [API REST](sql-database-configure-firewall-settings-rest.md)

Il server di Azure SQL utilizza le regole del firewall per consentire le connessioni a server e database. È possibile definire le impostazioni del firewall a livello di server e a livello di database per un database master o utente nel server logico del server di Azure SQL per consentire l'accesso al database in modo selettivo.

> [AZURE.IMPORTANT] Per consentire alle applicazioni da Azure di stabilire la connessione al server di Azure SQL, è necessario abilitare le connessioni da Azure. Per comprendere il funzionamento delle regole firewall, vedere [Come configurare un firewall del database SQL di Azure](sql-database-firewall-configure.md). È necessario aprire alcune porte TCP aggiuntive se si apportano le connessioni all'interno del cloud di Azure. Per ulteriori informazioni, vedere la sezione **V12 del database SQL: all'esterno rispetto all'interno** di [porte 1433 per 4.5 ADO.NET e SQL Database V12](sql-database-develop-direct-route-ports-adonet-v12.md)

[AZURE.INCLUDE [Creare un database SQL](../../includes/sql-database-create-new-server-firewall-portal.md)]

## Gestione delle regole del firewall a livello di server esistenti tramite il portale di Azure

Ripetere i passaggi per gestire le regole del firewall a livello di server.

- Per aggiungere il computer corrente, fare clic su Aggiungi IP client.
- Per aggiungere ulteriori indirizzi IP, digitare nome della regola, indirizzo IP iniziale e indirizzo IP finale.
- Per modificare una regola esistente, fare clic su uno dei campi nella regola e modificare.
- Per eliminare una regola esistente, passare il mouse sulla regola fino a quando non viene visualizzata la X alla fine della riga. Fare clic su X per rimuovere la regola.

Fare clic su **Salva** per salvare le modifiche.

## Passaggi successivi

Una regola firewall del server influisce su tutti i database SQL sul server di Azure SQL. Per configurare invece una regola del firewall a livello di database che interessi solo un database singolo, vedere [sp\_set\_ database\_firewall\_rule (database SQL di Azure)](https://msdn.microsoft.com/library/dn270010.aspx").

Per un'esercitazione sulla creazione di un database, vedere [Creare il primo database SQL di Azure](sql-database-get-started.md). Per informazioni sulla connessione a un database SQL di Azure da applicazioni open-source o di terze parti, vedere [Linee guida per la connessione al database SQL di Azure a livello di programmazione](https://msdn.microsoft.com/library/azure/ee336282.aspx). Per comprendere come spostarsi ai database vedere [Gestione di database e account di accesso in database SQL di Azure](https://msdn.microsoft.com/library/azure/ee336235.aspx).

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->

 

<!---HONumber=AcomDC_0413_2016-->