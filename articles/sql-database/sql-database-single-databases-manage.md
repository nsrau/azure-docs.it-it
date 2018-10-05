---
title: Creare e gestire server e database SQL singoli di Azure | Microsoft Docs
description: Informazioni sulla creazione e sulla gestione dei server logici e dei database singoli.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 09/20/2018
ms.openlocfilehash: c2855a9cdbb2abc01b3d3b55b12b979a2dbbf8bb
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2018
ms.locfileid: "47165358"
---
# <a name="create-and-manage-logical-servers-and-single-databases-in-azure-sql-database"></a>Creare e gestire server logici e database singoli in database SQL di Azure 

È possibile creare e gestire server logici e database singoli del database SQL di Azure usando il portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure, l'API REST e Transact-SQL.

## <a name="azure-portal-manage-logical-servers-and-databases"></a>Portale di Azure: gestire i database e i server logici

È possibile creare il gruppo di risorse del database SQL di Azure in anticipo oppure durante la creazione del server stesso. Esistono diversi metodi per accedere al modulo per la creazione di un nuovo server SQL Server, creando un nuovo server SQL Server o nell'ambito della procedura per creare un nuovo database. 

### <a name="create-a-blank-sql-server-logical-server"></a>Creare un server SQL Server vuoto (server logico)

Per creare un server di database SQL di Azure (senza un database) tramite il [portale di Azure](https://portal.azure.com), passare al modulo per un server SQL Server vuoto (server logico).  

### <a name="create-a-blank-or-sample-sql-database"></a>Creare un database SQL vuoto o di esempio

Per creare un database SQL di Azure tramite il [portale di Azure](https://portal.azure.com), passare a un modulo per database SQL vuoto e specificare le informazioni richieste. È possibile creare il gruppo di risorse e il server logico del database SQL di Azure anticipatamente o durante la creazione del database stesso. È possibile creare un database vuoto o creare un database di esempio basato su Adventure Works LT. 

  ![Creare il database 1](./media/sql-database-get-started-portal/create-database-1.png)

> [!IMPORTANT]
> Per informazioni sulla selezione del piano tariffario per il database, vedere [modello di acquisto basato su DTU](sql-database-service-tiers-dtu.md) e [modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md).

Per creare un'istanza di Istanza gestita, vedere [Create a Managed Instance](sql-database-managed-instance-get-started.md) (Creare un'istanza di Istanza gestita).

### <a name="manage-an-existing-sql-server"></a>Gestire un server SQL Server esistente

Per gestire un server esistente, passare al server usando uno dei vari metodi disponibili, ad esempio dalla pagina specifica del database SQL, dalla pagina **SQL Server** o dalla pagina **Tutte le risorse**. 

Per gestire un database esistente, passare alla pagina **Database SQL** e fare clic sul database che si desidera gestire. Lo screenshot seguente mostra come iniziare a impostare un firewall di livello server per un database dalla pagina **Informazioni generali** per un database. 

   ![Regola del firewall del server](./media/sql-database-get-started-portal/server-firewall-rule.png) 

> [!IMPORTANT]
> Per configurare le proprietà delle prestazioni di un database, vedere [modello di acquisto basato su DTU](sql-database-service-tiers-dtu.md) e [modello di acquisto basato su vCore](sql-database-service-tiers-vcore.md).
>

> [!TIP]
> Per una guida introduttiva per il portale di Azure, vedere [Creare un database SQL di Azure nel portale di Azure](sql-database-get-started-portal.md).

## <a name="powershell-manage-logical-servers-and-databases"></a>PowerShell: gestire i database e i server logici

Per creare e gestire server, database e firewall SQL di Azure con Azure PowerShell, usare i cmdlet di PowerShell seguenti. Se è necessario installare o aggiornare PowerShell, vedere [Installare il modulo Azure PowerShell](/powershell/azure/install-azurerm-ps). 

> [!TIP]
> Per una guida introduttiva per PowerShell, vedere [Creare un singolo database SQL di Azure usando PowerShell](sql-database-get-started-portal.md). Per gli script di esempio di PowerShell, vedere [Usare PowerShell per creare un singolo database SQL di Azure e configurare una regola del firewall](scripts/sql-database-create-and-configure-database-powershell.md) e [Monitorare e ridimensionare un database SQL singolo usando PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md).
>

| Cmdlet | DESCRIZIONE |
| --- | --- |
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|Crea un database |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|Recupera uno o più database|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|Imposta le proprietà per un database oppure sposta un database esistente in un pool elastico|
|[Remove-AzureRmSqlDatabase](/powershell/module/azurerm.sql/remove-azurermsqldatabase)|Rimuove un database|
|[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup)|Crea un gruppo di risorse|
|[New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver)|Crea un server|
|[Get-AzureRmSqlServer](/powershell/module/azurerm.sql/get-azurermsqlserver)|Restituisce informazioni sui server|
|[Set-AzureRmSqlServer](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqlserver)|Modifica le proprietà di un server|
|[Remove-AzureRmSqlServer](/powershell/module/azurerm.sql/remove-azurermsqlserver)|Rimuove un server|
|[New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule)|Crea una regola del firewall a livello di server |
|[Get-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/get-azurermsqlserverfirewallrule)|Ottiene le regole del firewall per un server|
|[Set-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/set-azurermsqlserverfirewallrule)|Modifica una regola del firewall in un server|
|[Remove-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/remove-azurermsqlserverfirewallrule)|Elimina una regola del firewall da un server|
| New-AzureRmSqlServerVirtualNetworkRule | Crea una [*regola della rete virtuale*](sql-database-vnet-service-endpoint-rule-overview.md), in base a una subnet che è un endpoint servizio di Rete virtuale. |

## <a name="azure-cli-manage-logical-servers-and-databases"></a>Interfaccia della riga di comando di Azure: gestire i database e i server logici

Per creare e gestire server, database e firewall SQL di Azure con l'[interfaccia della riga di comando di Azure](/cli/azure), usare i comandi seguenti dell'[interfaccia della riga di comando di Azure per il database SQL](/cli/azure/sql/db). Usare [Cloud Shell](/azure/cloud-shell/overview) per eseguire l'interfaccia della riga di comando nel browser o [installarla](/cli/azure/install-azure-cli) in macOS, Linux o Windows. Per creare e gestire i pool elastici, vedere [Pool elastici](sql-database-elastic-pool.md).

> [!TIP]
> Per una guida introduttiva per l'interfaccia della riga di comando di Azure, vedere [Creare un singolo database SQL di Azure usando l'interfaccia della riga di comando di Azure](sql-database-cli-samples.md). Per gli script di esempio dell'interfaccia della riga di comando di Azure, vedere [Usare l'interfaccia della riga di comando per creare un singolo database SQL di Azure e configurare una regola del firewall](scripts/sql-database-create-and-configure-database-cli.md) e [Usare l'interfaccia della riga di comando per monitorare e ridimensionare un singolo database SQL](scripts/sql-database-monitor-and-scale-database-cli.md).
>

| Cmdlet | DESCRIZIONE |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az-sql-db-create) |Crea un database|
|[az sql db list](/cli/azure/sql/db#az-sql-db-list)|Elenca tutti i database e i data warehouse in un server o tutti i database in un pool elastico|
|[az sql db list-editions](/cli/azure/sql/db#az-sql-db-list-editions)|Elenca gli obiettivi di servizio e i limiti di archiviazione disponibili|
|[az sql db list-usages](/cli/azure/sql/db#az-sql-db-list-usages)|Restituisce gli utilizzi del database|
|[az sql db show](/cli/azure/sql/db#az-sql-db-show)|Recupera un database o un data warehouse|
|[az sql db update](/cli/azure/sql/db#az-sql-db-update)|Aggiorna un database|
|[az sql db delete](/cli/azure/sql/db#az-sql-db-delete)|Rimuove un database|
|[az group create](/cli/azure/group#az-group-create)|Crea un gruppo di risorse|
|[az sql server create](/cli/azure/sql/server#az-sql-server-create)|Crea un server|
|[az sql server list](/cli/azure/sql/server#az-sql-server-list)|Elenca i server|
|[az sql server list-usages](/cli/azure/sql/server#az-sql-server-list-usages)|Restituisce gli utilizzi del server|
|[az sql server show](/cli/azure/sql/server#az-sql-server-show)|Ottiene un server|
|[az sql server update](/cli/azure/sql/server#az-sql-server-update)|Aggiorna un server|
|[az sql server delete](/cli/azure/sql/server#az-sql-server-delete)|Consente di eliminare un server|
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Crea una regola del firewall del server|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Elenca le regole del firewall in un server|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Mostra i dettagli di una regola del firewall|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Aggiorna una regola del firewall|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Elimina una regola del firewall|

## <a name="transact-sql-manage-logical-servers-and-databases"></a>Transact-SQL: gestire i database e i server logici

Per creare e gestire server, database e firewall SQL di Azure con Transact-SQL, usare i comandi T-SQL seguenti. È possibile eseguire questi comandi usando il portale di Azure, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs) o qualsiasi altro programma che può connettersi a un server di database SQL di Azure e passare comandi Transact-SQL. Per la gestione dei pool elastici, vedere [Pool elastici](sql-database-elastic-pool.md).


> [!TIP]
> Per una guida introduttiva per SQL Server Management Studio in Microsoft Windows, vedere [Database SQL di Azure: usare SQL Server Management Studio per connettersi ed eseguire query sui dati](sql-database-connect-query-ssms.md). Per una guida introduttiva per Visual Studio Code in macOS, Linux o Windows, vedere [Database SQL di Azure: Usare Visual Studio Code per connettersi ai dati ed eseguire query](sql-database-connect-query-vscode.md).

> [!IMPORTANT]
> Non è possibile creare o eliminare un server con Transact-SQL.
>

| Comando | DESCRIZIONE |
| --- | --- |
|[CREATE DATABASE (database SQL di Azure)](/sql/t-sql/statements/create-database-azure-sql-database)|Crea un nuovo database. Per creare un nuovo database è necessario essere connessi al database master.|
| [ALTER DATABASE (database SQL di Azure)](/sql/t-sql/statements/alter-database-azure-sql-database) |Modifica un database SQL di Azure. |
|[ALTER DATABASE (Azure SQL Data Warehouse)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse)|Modifica un Azure SQL Data Warehouse.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Questo comando elimina un database.|
|[sys.database_service_objectives (database SQL di Azure)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Restituisce l'edizione (livello di servizio), l'obiettivo di servizio (piano tariffario) e il nome del pool elastico, se presente, di un database SQL di Azure o un'istanza di Azure SQL Data Warehouse. Se si è connessi al database master in un server di database SQL di Azure, restituisce informazioni su tutti i database. Per Azure SQL Data Warehouse, è necessario essere connessi al database master.|
|[sys.dm_db_resource_stats (database SQL di Azure)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Restituisce il consumo di CPU, I/O e memoria per un database SQL di Azure. È presente una riga ogni 15 secondi, anche se non c'è attività di database.|
|[sys.resource_stats (database SQL di Azure)](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Restituisce i dati di archiviazione e di uso della CPU per un database SQL di Azure. I dati vengono raccolti e aggregati per intervalli di cinque minuti.|
|[sys.database_connection_stats (database SQL di Azure)](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Contiene le statistiche per gli eventi di connettività di database del database SQL fornendo una panoramica delle connessioni di database riuscite e non riuscite. |
|[sys.event_log (database SQL di Azure)](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Restituisce le connessioni del database SQL di Azure che hanno esito positivo, quelle che hanno esito negativo e i deadlock. È possibile usare queste informazioni per rilevare o risolvere i problemi dell'attività del database SQL.|
|[sp_set_firewall_rule (database SQL di Azure)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Crea o aggiorna le impostazioni del firewall a livello di server per il server di database SQL. Questa stored procedure è disponibile solo nel database master per l'account di accesso dell'entità di livello server. Una regola del firewall a livello di server può essere creata tramite Transact-SQL solo dopo la creazione della prima regola del firewall a livello di server da parte di un utente con autorizzazioni a livello di Azure|
|[sys.firewall_rules (database SQL di Azure)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Restituisce informazioni sulle impostazioni del firewall a livello di server associate al database SQL di Microsoft Azure.|
|[sp_delete_firewall_rule (database SQL di Azure)](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Rimuove le impostazioni del firewall a livello di server dal server di database SQL. Questa stored procedure è disponibile solo nel database master per l'account di accesso dell'entità di livello server.|
|[sp_set_database_firewall_rule (database SQL di Azure)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Crea o aggiorna le regole del firewall a livello di database per il database SQL di Azure o per SQL Data Warehouse. Le regole firewall del database possono essere configurate per il database master e per i database utente nel database SQL. Le regole del firewall del database sono utili quando si usano utenti di database indipendenti. |
|[sys.database_firewall_rules (database SQL di Azure)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Restituisce informazioni sulle impostazioni del firewall a livello di database associate al database SQL di Microsoft Azure. |
|[sp_delete_database_firewall_rule (database SQL di Azure)](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Rimuove l'impostazione del firewall a livello di database dal database SQL di Azure o da SQL Data Warehouse. |



## <a name="rest-api-manage-logical-servers-and-databases"></a>API REST: gestire i database e i server logici

Per creare e gestire server, database e firewall SQL di Azure, usare queste richieste dell'API REST.

| Comando | DESCRIZIONE |
| --- | --- |
|[Servers - Create Or Update](/rest/api/sql/servers/createorupdate)|Crea o aggiorna un nuovo server.|
|[Servers - Delete](/rest/api/sql/servers/delete)|Elimina un server SQL.|
|[Servers - Get](/rest/api/sql/servers/get)|Ottiene un server.|
|[Servers - List](/rest/api/sql/servers/list)|Restituisce un elenco di server.|
|[Servers - List By Resource Group](/rest/api/sql/servers/listbyresourcegroup)|Restituisce un elenco di server in un gruppo di risorse.|
|[Servers - Update](/rest/api/sql/servers/update)|Aggiorna un server esistente.|
|[Database: crea o aggiorna](/rest/api/sql/databases/createorupdate)|Crea un nuovo database o ne aggiorna uno esistente.|
|[Databases - Get](/rest/api/sql/databases/get)|Ottiene un database.|
|[Database: elenca da pool elastico](/rest/api/sql/databases/listbyelasticpool)|Restituisce un elenco di database in un pool elastico.|
|[Databases - List By Server](/rest/api/sql/databases/listbyserver)|Restituisce un elenco di database in un server.|
|[Databases - Update](/rest/api/sql/databases/update)|Aggiorna un database esistente.|
|[Firewall Rules - Create Or Update](/rest/api/sql/firewallrules/createorupdate)|Crea o aggiorna una regola del firewall.|
|[Firewall Rules - Delete](/rest/api/sql/firewallrules/delete)|Elimina una regola del firewall.|
|[Firewall Rules - Get](/rest/api/sql/firewallrules/get)|Ottiene una regola del firewall.|
|[Firewall Rules - List By Server](/rest/api/sql/firewallrules/listbyserver)|Restituisce un elenco di regole firewall.|

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulla migrazione di un database SQL Server in Azure, vedere [Migrazione al database SQL di Azure](sql-database-cloud-migrate.md).
- Per informazioni sulle funzionalità supportate, vedere [Azure SQL Database features](sql-database-features.md) (Funzioni del database SQL di Azure).
