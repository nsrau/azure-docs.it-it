---
title: Creare un database singolo di database SQL di Azure | Microsoft Docs
description: Gestire un database SQL di Azure singolo.
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
ms.workload: data-management
ms.date: 08/25/2017
ms.author: carlrab
ms.translationtype: HT
ms.sourcegitcommit: cb9130243bdc94ce58d6dfec3b96eb963cdaafb0
ms.openlocfilehash: 3af597d7af13daaf80cd750b230675c8475033e1
ms.contentlocale: it-it
ms.lasthandoff: 09/26/2017

---
# <a name="manage-resources-for-a-single-database-in-azure-sql-database"></a>Gestire le risorse per un database singolo nel database SQL di Microsoft Azure

Con un database singolo si determina la quantità di risorse di cui il database necessita per gestire il carico di lavoro al livello di servizio, il livello di prestazioni e le dimensioni di archiviazione. 

## <a name="manage-single-database-resources-using-the-azure-portal"></a>Gestire risorse di database singoli con il portale di Azure

Per impostare o modificare il livello di servizio, il livello di prestazioni o lo spazio di archiviazione per un database SQL di Azure nuovo o esistente con il portale di Azure, aprire la finestra **Configura prestazioni** relativa al database facendo clic su **Piano tariffario (piano DTU)**, come illustrato nello screenshot seguente. 

- Impostare o modificare il livello di servizio selezionando quello per il proprio carico di lavoro. 
- Impostare o modificare il livello di prestazioni (**DTU**) all'interno di un livello di servizio usando il dispositivo di scorrimento **DTU**.
- Impostare o modificare lo spazio di archiviazione per il livello di prestazioni usando il dispositivo di scorrimento **Archiviazione**. 

![Configurare il livello di servizio e di prestazioni](./media/sql-database-single-database-resources/change-service-tier.png)

> [!IMPORTANT]
> Se si seleziona un livello di servizio P11 o P15, vedere [Limitazioni correnti dei database P11 e P15 con dimensioni massime di 4 TB](#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).
>

## <a name="manage-single-database-resources-using-powershell"></a>Gestire risorse di database singoli con PowerShell

Per impostare o modificare i livelli di servizio, i livelli di prestazioni e lo spazio di archiviazione per database SQL di Azure con PowerShell, usare questi cmdlet di PowerShell. Se è necessario installare o aggiornare PowerShell, vedere [Installare il modulo Azure PowerShell](/powershell/azure/install-azurerm-ps). 

| Cmdlet | Descrizione |
| --- | --- |
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|Crea un database |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|Recupera uno o più database|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|Imposta le proprietà per un database oppure sposta un database esistente in un pool elastico. Ad esempio, usare la proprietà **MaxSizeBytes** per impostare la dimensione massima per un database.|


> [!TIP]
> Per uno script di esempio di PowerShell che monitora le metriche delle prestazioni di un database, lo ridimensiona passando a un livello di prestazioni superiore e crea una regola di avviso per una delle metriche delle prestazioni, vedere [Monitorare e ridimensionare un database SQL singolo usando PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md).

## <a name="manage-single-database-resources-using-the-azure-cli"></a>Gestire risorse di database singoli con l'interfaccia della riga di comando di Azure

Per impostare o modificare i livelli di servizio, i livelli di prestazioni e lo spazio di archiviazione per database SQL di Azure con l'interfaccia della riga di comando di Azure, usare questi [comandi per database SQL](/cli/azure/sql/db). Usare [Cloud Shell](/azure/cloud-shell/overview) per eseguire l'interfaccia della riga di comando nel browser o [installarla](/cli/azure/install-azure-cli) in macOS, Linux o Windows. Per creare e gestire pool elastici SQL, vedere l'articolo relativo ai [pool elastici](sql-database-elastic-pool.md).

| Cmdlet | Descrizione |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#create) |Crea un database|
|[az sql db list](/cli/azure/sql/db#list)|Elenca tutti i database e i data warehouse in un server o tutti i database in un pool elastico|
|[az sql db list-editions](/cli/azure/sql/db#list-editions)|Elenca gli obiettivi di servizio e i limiti di archiviazione disponibili|
|[az sql db list-usages](/cli/azure/sql/db#list-usages)|Restituisce gli utilizzi del database|
|[az sql db show](/cli/azure/sql/db#show)|Recupera un database o un data warehouse|
|[az sql db update](/cli/azure/sql/db#update)|Aggiorna un database|

> [!TIP]
> Per uno script di esempio dell'interfaccia della riga di comando di Azure che ridimensiona un database SQL di Azure singolo passando a un livello di prestazioni diverso dopo l'esecuzione di query sulle dimensioni del database, vedere l'articolo su come [usare l'interfaccia della riga di comando per monitorare e ridimensionare un database SQL singolo](scripts/sql-database-monitor-and-scale-database-cli.md).
>

## <a name="manage-single-database-resources-using-transact-sql"></a>Gestire risorse di database singoli con Transact-SQL

Per impostare o modificare i livelli di servizio, i livelli di prestazioni e lo spazio di archiviazione per database SQL di Azure con Transact-SQL, usare i comandi T-SQL seguenti. È possibile eseguire questi comandi usando il portale di Azure, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs) o qualsiasi altro programma che può connettersi a un server di database SQL di Azure e passare comandi Transact-SQL. 

| Comando | Descrizione |
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

| Comando | Descrizione |
| --- | --- |
|[Databases - Create Or Update](/rest/api/sql/databases/createorupdate)|Crea un nuovo database o ne aggiorna uno esistente.|
|[Databases - Get](/rest/api/sql/databases/get)|Ottiene un database.|
|[Database: ottieni da pool elastico](/rest/api/sql/databases/getbyelasticpool)|Ottiene un database all'interno di un pool elastico.|
|[Database: ottieni da pool elastico consigliato](/rest/api/sql/databases/getbyrecommendedelasticpool)|Ottiene un database all'interno di un pool elastico consigliato.|
|[Database: elenca da pool elastico](/rest/api/sql/databases/listbyelasticpool)|Restituisce un elenco di database in un pool elastico.|
|[Database: elenca da pool elastico consigliato](/rest/api/sql/databases/listbyrecommendedelasticpool)|Restituisce un elenco di database in un pool elastico consigliato.|
|[Databases - List By Server](/rest/api/sql/databases/listbyserver)|Restituisce un elenco di database in un server.|
|[Database - Aggiornamento] ((/rest/api/sql/databases/update)|Aggiorna un database esistente.|



## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sui livelli di servizio, i livelli di prestazioni e le dimensioni di archiviazione, vedere [Livelli di servizio](sql-database-service-tiers.md).
- Altre informazioni sui pool elastici, vedere [Pool elastici](sql-database-elastic-pool.md).
- Per informazioni, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md).

