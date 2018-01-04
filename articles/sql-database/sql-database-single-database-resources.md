---
title: Creare un database singolo di database SQL di Azure | Microsoft Docs
description: "Gestire il livello di servizio, un livello di prestazioni e una quantità di risorse di archiviazione per un singolo database SQL di Azure."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 12/14/2017
ms.author: carlrab
ms.openlocfilehash: 0f88b09c342c1849a5c61fdb5dc048d7cbadc83b
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2017
---
# <a name="manage-resources-for-a-single-database-in-azure-sql-database"></a>Gestire le risorse per un database singolo nel database SQL di Microsoft Azure

Con un database singolo si determina la quantità di risorse di cui il database necessita per gestire il carico di lavoro al livello di servizio, il livello di prestazioni e le dimensioni di archiviazione. 

## <a name="manage-single-database-resources-using-the-azure-portal"></a>Gestire risorse di database singoli con il portale di Azure

Per impostare o modificare il livello di servizio, il livello di prestazioni o lo spazio di archiviazione per un database SQL di Azure nuovo o esistente con il portale di Azure, aprire la finestra **Configura prestazioni** relativa al database facendo clic su **Piano tariffario (piano DTU)**, come illustrato nello screenshot seguente. 

- Impostare o modificare il livello di servizio selezionando quello per il proprio carico di lavoro. 
- Impostare o modificare il livello di prestazioni (**DTU**) all'interno di un livello di servizio usando il dispositivo di scorrimento **DTU**.
- Impostare o modificare lo spazio di archiviazione per il livello di prestazioni usando il dispositivo di scorrimento **Archiviazione**. 

![Configurare il livello di servizio e di prestazioni](./media/sql-database-single-database-resources/change-service-tier.png)

Fare clic su **Panoramica** per il monitoraggio e di annullare un'operazione in corso.

![Operazione di annullamento.](./media/sql-database-single-database-resources/cancel-operation.png)

> [!IMPORTANT]
> Se si seleziona un livello di servizio P11 o P15, vedere [Limitazioni correnti dei database P11 e P15 con dimensioni massime di 4 TB](sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).
>

## <a name="manage-single-database-resources-using-powershell"></a>Gestire risorse di database singoli con PowerShell

Per impostare o modificare i livelli di servizio, i livelli di prestazioni e lo spazio di archiviazione per database SQL di Azure con PowerShell, usare questi cmdlet di PowerShell. Se è necessario installare o aggiornare PowerShell, vedere [Installare il modulo Azure PowerShell](/powershell/azure/install-azurerm-ps). 

| Cmdlet | DESCRIZIONE |
| --- | --- |
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|Crea un database |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|Recupera uno o più database|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|Imposta le proprietà per un database oppure sposta un database esistente in un pool elastico. Ad esempio, usare la proprietà **MaxSizeBytes** per impostare la dimensione massima per un database.|
|[Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity)|Ottiene lo stato delle operazioni del database. |
|[Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity)|Annulla l'operazione di aggiornamento asincrono nel database.|


> [!TIP]
> Per uno script di esempio di PowerShell che monitora le metriche delle prestazioni di un database, lo ridimensiona passando a un livello di prestazioni superiore e crea una regola di avviso per una delle metriche delle prestazioni, vedere [Monitorare e ridimensionare un database SQL singolo usando PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md).

## <a name="manage-single-database-resources-using-the-azure-cli"></a>Gestire risorse di database singoli con l'interfaccia della riga di comando di Azure

Per impostare o modificare i livelli di servizio, i livelli di prestazioni e lo spazio di archiviazione per database SQL di Azure con l'interfaccia della riga di comando di Azure, usare questi [comandi per database SQL](/cli/azure/sql/db). Usare [Cloud Shell](/azure/cloud-shell/overview) per eseguire l'interfaccia della riga di comando nel browser o [installarla](/cli/azure/install-azure-cli) in macOS, Linux o Windows. Per creare e gestire pool elastici SQL, vedere l'articolo relativo ai [pool elastici](sql-database-elastic-pool.md).

| Cmdlet | DESCRIZIONE |
| --- | --- |
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create)|Crea una regola del firewall del server|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_list)|Elenca le regole del firewall in un server|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_show)|Mostra i dettagli di una regola del firewall|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_update)|Aggiorna una regola del firewall|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_delete)|Elimina una regola del firewall|
|[elenco di operazioni db sql AZ](/cli/azure/sql/db/op?#az_sql_db_op_list)|Ottiene un elenco di operazioni eseguite nel database.|
|[annullamento di operazioni db sql AZ](/cli/azure/sql/db/op#az_sql_db_op_cancel)|Annulla l'operazione asincrona per il database.|

> [!TIP]
> Per uno script di esempio dell'interfaccia della riga di comando di Azure che ridimensiona un database SQL di Azure singolo passando a un livello di prestazioni diverso dopo l'esecuzione di query sulle dimensioni del database, vedere l'articolo su come [usare l'interfaccia della riga di comando per monitorare e ridimensionare un database SQL singolo](scripts/sql-database-monitor-and-scale-database-cli.md).
>

## <a name="manage-single-database-resources-using-transact-sql"></a>Gestire risorse di database singoli con Transact-SQL

Per impostare o modificare i livelli di servizio, i livelli di prestazioni e lo spazio di archiviazione per database SQL di Azure con Transact-SQL, usare i comandi T-SQL seguenti. È possibile eseguire questi comandi usando il portale di Azure, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs) o qualsiasi altro programma che può connettersi a un server di database SQL di Azure e passare comandi Transact-SQL. 

| Comando | DESCRIZIONE |
| --- | --- |
|[CREATE DATABASE (database SQL di Azure)](/sql/t-sql/statements/create-database-azure-sql-database)|Crea un nuovo database. Per creare un nuovo database è necessario essere connessi al database master.|
| [ALTER DATABASE (database SQL di Azure)](/sql/t-sql/statements/alter-database-azure-sql-database) |Modifica un database SQL di Azure. |
|[sys.database_service_objectives (database SQL di Azure)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Restituisce l'edizione (livello di servizio), l'obiettivo di servizio (piano tariffario) e il nome del pool elastico, se presente, di un database SQL di Azure o un'istanza di Azure SQL Data Warehouse. Se si è connessi al database master in un server di database SQL di Azure, restituisce informazioni su tutti i database. Per Azure SQL Data Warehouse, è necessario essere connessi al database master.|
|[sys.database_usage (database SQL di Azure)](/sql/relational-databases/system-catalog-views/sys-database-usage-azure-sql-database)|Elenca il numero, il tipo e la durata dei database in un server di database SQL di Azure.|

L'esempio seguente illustra la modifica delle dimensioni massime per un database con il comando ALTER DATABASE:

 ```sql
ALTER DATABASE <myDatabaseName> 
   MODIFY (MAXSIZE = 4096 GB);
```

## <a name="manage-single-database-resources-using-the-rest-api"></a>Gestire risorse di database singoli con l'API REST

Per impostare o modificare i livelli di servizio, i livelli di prestazioni e lo spazio di archiviazione per database dei database SQL di Azure usare le richieste API REST.

| Comando | DESCRIZIONE |
| --- | --- |
|[Database: crea o aggiorna](/rest/api/sql/databases/createorupdate)|Crea un nuovo database o ne aggiorna uno esistente.|
|[Databases - Get](/rest/api/sql/databases/get)|Ottiene un database.|
|[Database: ottieni da pool elastico](/rest/api/sql/databases/getbyelasticpool)|Ottiene un database all'interno di un pool elastico.|
|[Database: ottieni da pool elastico consigliato](/rest/api/sql/databases/getbyrecommendedelasticpool)|Ottiene un database all'interno di un pool elastico consigliato.|
|[Database: elenca da pool elastico](/rest/api/sql/databases/listbyelasticpool)|Restituisce un elenco di database in un pool elastico.|
|[Database: elenca da pool elastico consigliato](/rest/api/sql/databases/listbyrecommendedelasticpool)|Restituisce un elenco di database in un pool elastico consigliato.|
|[Databases - List By Server](/rest/api/sql/databases/listbyserver)|Restituisce un elenco di database in un server.|
|[Databases - Update](/rest/api/sql/databases/update)|Aggiorna un database esistente.|
|[Operazioni - elenco](/rest/api/sql/Operations/List)|Elenca tutte le operazioni API Rest di SQL disponibili.|



## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sui livelli di servizio, i livelli di prestazioni e le dimensioni di archiviazione, vedere [Livelli di servizio](sql-database-service-tiers.md).
- Altre informazioni sui pool elastici, vedere [Pool elastici](sql-database-elastic-pool.md).
- Per informazioni, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md).
