---
title: Gestire i pool elastici
description: Creare e gestire pool elastici del database SQL di Azure usando il portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure, Transact-SQL (T-SQL) e l'API REST.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.custom: seoapril2019 sqldbrb=1
ms.openlocfilehash: 0fc4f9c2142011edf78033d36d13ecb9115a8850
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84031032"
---
# <a name="manage-elastic-pools-in-azure-sql-database"></a>Gestire i pool elastici nel database SQL di Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Con un pool elastico si determina la quantità di risorse di cui il pool elastico necessita per gestire il carico di lavoro dei propri database e la quantità di risorse per ogni database in pool.

## <a name="azure-portal"></a>Portale di Azure

Tutte le impostazioni del pool sono reperibili in una stessa area dell'interfaccia, ovvero il pannello **Configura pool**. Per accedere a questa pagina, trovare un pool elastico nella portale di Azure e fare clic su **Configura pool** nella parte superiore del pannello oppure dal menu delle risorse a sinistra.

Da qui è possibile eseguire una combinazione qualsiasi di modifiche tra quelle elencate di seguito e salvarle in un unico batch:

1. Modificare il livello di servizio del pool
2. Aumentare o ridurre le prestazioni (DTU o vCore) e le risorse di archiviazione
3. Aggiungere database nel pool o rimuoverli
4. Impostare un limite di prestazioni minimo (garantito) e massimo per i database nei pool
5. Rivedere il riepilogo dei costi per visualizzare le variazioni nella fattura in seguito alle nuove selezioni

![Pannello di configurazione del pool elastico](./media/elastic-pool-manage/configure-pool.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo Azure Resource Manager di PowerShell è ancora supportato da Database SQL di Azure, ma tutte le attività di sviluppo future sono incentrate sul modulo Az.Sql. Per informazioni su questi cmdlet, vedere [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nei moduli Az e AzureRm sono sostanzialmente identici.

Per creare e gestire i pool elastici e i database in pool del database SQL con Azure PowerShell, usare i cmdlet di PowerShell seguenti. Se è necessario installare o aggiornare PowerShell, vedere [Installare il modulo Azure PowerShell](/powershell/azure/install-az-ps). Per creare e gestire i server per un pool elastico, vedere [creare e gestire server](logical-servers.md). Per creare e gestire regole del firewall, vedere [Creare e gestire le regole del firewall con PowerShell](firewall-configure.md#use-powershell-to-manage-server-level-ip-firewall-rules).

> [!TIP]
> Per gli script di esempio di PowerShell, vedere [Creare pool elastici e spostare i database tra i pool e al loro esterno usando PowerShell](scripts/move-database-between-elastic-pools-powershell.md) e [Usare PowerShell per il monitoraggio e il ridimensionamento di un pool elastico SQL nel database SQL di Azure](scripts/monitor-and-scale-pool-powershell.md).
>

| Cmdlet | Descrizione |
| --- | --- |
|[New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool)|Consente di creare un pool elastico.|
|[Get-AzSqlElasticPool](/powershell/module/az.sql/get-azsqlelasticpool)|Consente di ottenere i pool elastici e i relativi valori della proprietà.|
|[Set-AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool)|Consente di modificare le proprietà di un pool di database elastico. Ad esempio, usare le proprietà **StorageMB** per modificare l'archiviazione massima di un pool elastico.|
|[Remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool)|Consente di eliminare un pool elastico.|
|[Get-AzSqlElasticPoolActivity](/powershell/module/az.sql/get-azsqlelasticpoolactivity)|Consente di ottenere lo stato delle operazioni in un pool elastico|
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Consente di creare un nuovo database in un pool esistente o in un database singolo. |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Ottiene uno o più database.|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Consente di impostare le proprietà per un database oppure sposta un database esistente all'interno o all'esterno di in un pool elastico.|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Rimuove un database.|

> [!TIP]
> La creazione di molti database in un pool elastico può richiedere tempo quando viene eseguita tramite il portale o i cmdlet di PowerShell che creano un database singolo alla volta. Per automatizzare la creazione in un pool elastico, vedere [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per creare e gestire pool elastici del database SQL con l'[interfaccia della riga di comando di Azure](/cli/azure), usare i comandi seguenti del [database SQL per l'interfaccia della riga di comando di Azure](/cli/azure/sql/db). Usare [Cloud Shell](/azure/cloud-shell/overview) per eseguire l'interfaccia della riga di comando nel browser o [installarla](/cli/azure/install-azure-cli) in macOS, Linux o Windows.

> [!TIP]
> Per gli script di esempio dell'interfaccia della riga di comando di Azure vedere usare l'interfaccia della riga [di comando per spostare un database nel database SQL in un pool elastico SQL](scripts/move-database-between-elastic-pools-cli.md) e [usare l'interfaccia della riga di comando di Azure per ridimensionare un pool](scripts/scale-pool-cli.md)
>

| Cmdlet | Descrizione |
| --- | --- |
|[az sql elastic-pool create](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-create)|Consente di creare un pool elastico.|
|[az sql elastic-pool list](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list)|Restituisce un elenco di pool elastici in un server.|
|[az sql elastic-pool list-dbs](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-dbs)|Restituisce un elenco di database in un pool elastico.|
|[az sql elastic-pool list-editions](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-editions)|Include anche le impostazioni di DTU del pool disponibile, i limiti di archiviazione e per le impostazioni per ogni database. Al fine di ridurre il livello di dettaglio, i limiti di spazio di archiviazione aggiuntivo e le impostazioni per ogni database sono nascoste per impostazione predefinita.|
|[az sql elastic-pool update](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)|Consente di aggiornare un pool elastico.|
|[az sql elastic-pool delete](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-delete)|Consente di eliminare un pool elastico.|

## <a name="transact-sql-t-sql"></a>Transact-SQL (T-SQL)

Per creare e spostare i database all'interno dei pool elastici esistenti o per restituire informazioni su un pool elastico del database SQL con Transact-SQL, usare i comandi T-SQL seguenti. È possibile eseguire questi comandi utilizzando la portale di Azure, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs)o qualsiasi altro programma in grado di connettersi a un server e passare i comandi Transact-SQL. Per creare e gestire regole del firewall con T-SQL, vedere [Gestire le regole del firewall con Transact-SQL](firewall-configure.md#use-transact-sql-to-manage-ip-firewall-rules).

> [!IMPORTANT]
> Non è possibile creare, aggiornare o eliminare un pool elastico del database SQL di Azure con Transact-SQL. È possibile aggiungere o rimuovere i database da un pool elastico ed è possibile usare le DMV per restituire informazioni sui pool elastici esistenti.
>

| Comando | Description |
| --- | --- |
|[CREATE DATABASE (database SQL di Azure)](/sql/t-sql/statements/create-database-azure-sql-database)|Consente di creare un nuovo database in un pool esistente o in un database singolo. È necessario essere connessi al database master per creare un nuovo database.|
| [ALTER DATABASE (database SQL di Azure)](/sql/t-sql/statements/alter-database-azure-sql-database) |Consente di spostare un database all'interno, all'esterno o tra pool elastici.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Questo comando elimina un database.|
|[sys.elastic_pool_resource_stats (Database SQL di Azure)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|Restituisce le statistiche di utilizzo delle risorse per tutti i pool elastici in un server. Per ogni pool elastico è presente una riga per ogni finestra di report di 15 secondi (quattro righe al minuto). Sono inclusi CPU, IO, Log, uso dell'archiviazione e uso di richieste/sessioni simultanee da parte di tutti i database nel pool.|
|[sys.database_service_objectives (database SQL di Azure)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Restituisce l'edizione (livello di servizio), l'obiettivo di servizio (piano tariffario) e il nome del pool elastico, se presente, per un database nel database SQL o Azure SQL Data Warehouse. Se si è connessi al database master in un server, in vengono restituite informazioni su tutti i database. Per Azure SQL Data Warehouse, è necessario essere connessi al database master.|

## <a name="rest-api"></a>API REST

Per creare e gestire i pool elastici e i database in pool del database SQL, usare queste richieste API REST.

| Comando | Description |
| --- | --- |
|[Pool elastici-crea o aggiorna](https://docs.microsoft.com/rest/api/sql/elasticpools/createorupdate)|Crea un nuovo pool elastico o aggiorna un pool elastico esistente.|
|[Pool elastici-eliminazione](https://docs.microsoft.com/rest/api/sql/elasticpools/delete)|Consente di eliminare un pool elastico.|
|[Pool elastici-Ottieni](https://docs.microsoft.com/rest/api/sql/elasticpools/get)|Ottiene un pool elastico.|
|[Pool elastici-elenca per server](https://docs.microsoft.com/rest/api/sql/elasticpools/listbyserver)|Restituisce un elenco di pool elastici in un server.|
|[Pool elastici-aggiornamento](https://docs.microsoft.com/rest/api/sql/elasticpools/listbyserver)|Consente di aggiornare un pool elastico esistente.|
|[Attività del pool elastico](https://docs.microsoft.com/rest/api/sql/elasticpoolactivities)|Restituisce le attività del pool elastico.|
|[Attività del database del pool elastico](https://docs.microsoft.com/rest/api/sql/elasticpooldatabaseactivities)|Restituisce l'attività sul database all'interno di un pool elastico.|
|[Databases - Create or update](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)|Crea un nuovo database o ne aggiorna uno esistente.|
|[Databases - Get](https://docs.microsoft.com/rest/api/sql/databases/get)|Ottiene un database.|
|[Database-elenca per pool elastico](https://docs.microsoft.com/rest/api/sql/databases/listbyelasticpool)|Restituisce un elenco di database in un pool elastico.|
|[Database-elenco per server](https://docs.microsoft.com/rest/api/sql/databases/listbyserver)|Restituisce un elenco di database in un server.|
|[Databases - Update](https://docs.microsoft.com/rest/api/sql/databases/update)|Aggiorna un database esistente.|

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sui modelli di progettazione per applicazioni SaaS con pool elastici, vedere l'articolo relativo ai [modelli di progettazione per applicazioni SaaS multi-tenant con database SQL di Azure](saas-tenancy-app-design-patterns.md).
* Per un'esercitazione sul SaaS con i pool elastici, vedere [Introduzione all'applicazione SaaS Wingtip](saas-dbpertenant-wingtip-app-overview.md).
