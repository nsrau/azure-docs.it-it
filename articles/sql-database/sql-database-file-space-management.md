---
title: Gestione dello spazio file del database SQL di Azure| Microsoft Docs
description: Questa pagina descrive come gestire lo spazio file con il database SQL di Azure e fornisce esempi di codice per determinare se è necessario compattare un database e come eseguire un'operazione di compattazione del database.
services: sql-database
author: oslake
manager: craigg
ms.service: sql-database
ms.custom: how-to
ms.topic: conceptual
ms.date: 08/08/2018
ms.author: moslake
ms.openlocfilehash: 5dce07996191af3df3a4bdf16b211c29d59a994f
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2018
ms.locfileid: "40003859"
---
# <a name="manage-file-space-in-azure-sql-database"></a>Gestire lo spazio file nel database SQL di Azure
Questo articolo descrive i diversi tipi di spazio di archiviazione nel database SQL di Azure e le operazioni che è possibile eseguire quando lo spazio file allocato per i database e i pool elastici deve essere gestito esplicitamente.

## <a name="overview"></a>Panoramica

Nel database SQL di Azure, la maggior parte delle metriche per lo spazio di archiviazione visualizzate nel portale di Azure e le API seguenti misura il numero di pagine di dati usate per i database e i pool elastici:
- API per le metriche basate su Azure Resource Manager tra cui [get-metrics](https://docs.microsoft.com/powershell/module/azurerm.insights/get-azurermmetric) di PowerShell
- T-SQL: [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)
- T-SQL: [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)
- T-SQL: [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)

Sono disponibili modelli di carico di lavoro in cui l'allocazione dei file di dati sottostanti per i database può superare la quantità di pagine di dati usate.  Questo problema si può verificare quando lo spazio usato aumenta e i dati vengono successivamente eliminati.  Ciò è dovuto al fatto che lo spazio file allocato non viene recuperato automaticamente quando i dati vengono eliminati.  In questi scenari lo spazio allocato per un database o un pool potrebbe superare i limiti supportati e potrebbe impedire la crescita dei dati o le modifiche ai livelli di prestazioni e richiedere quindi la compattazione dei file di dati per l'attenuazione.

Il servizio database SQL non compatta automaticamente i file di dati per recuperare spazio allocato non usato a causa del potenziale impatto sulle prestazioni dei database.  I clienti possono tuttavia compattare i file di dati in modalità self-service quando preferiscono, seguendo la procedura illustrata in [Recuperare lo spazio allocato non usato](#reclaim-unused-allocated-space). 

> [!NOTE]
> A differenza dei file di dati, il servizio di database SQL compatta automaticamente i file di log, in quanto tale operazione non influisce sulle prestazioni del database. 

## <a name="understanding-types-of-storage-space-for-a-database"></a>Informazioni sui tipi di spazio di archiviazione per un database

La comprensione delle quantità di spazio di archiviazione seguenti è importante per la gestione dello spazio file di un database.

|Quantità di database|Definizione|Commenti|
|---|---|---|
|**Spazio dati usato**|Quantità di spazio usato per archiviare i dati del database in pagine da 8 KB.|In genere, lo spazio usato aumenta quando vengono inseriti i dati e diminuisce quando vengono eliminati. In alcuni casi, lo spazio usato non cambia in caso di inserimenti o eliminazioni, a seconda della quantità e del modello di dati coinvolti nell'operazione e dell'eventuale frammentazione. Ad esempio, se si elimina una riga da ogni pagina di dati, non si riduce necessariamente lo spazio usato.|
|**Spazio dati allocato**|Quantità di spazio file formattato messo a disposizione per l'archiviazione dei dati del database.|La quantità di spazio allocato aumenta automaticamente, ma non diminuisce mai dopo le eliminazioni. Questo comportamento assicura che gli inserimenti futuri avvengano più velocemente, perché non è necessario riformattare lo spazio.|
|**Spazio dati allocato ma non usato**|Differenza tra la quantità di spazio dati allocato e lo spazio dati usato.|Questa quantità rappresenta la quantità massima di spazio libero che può essere recuperata compattando i file di dati del database.|
|**Dimensioni massime dei dati**|Quantità massima di spazio che può essere usata per l'archiviazione dei dati del database.|La quantità di spazio dati allocato non può superare le dimensioni massime dei dati.|
||||

Il diagramma seguente illustra la relazione tra i diversi tipi di spazio di archiviazione per un database.

![Tipi di spazio di archiviazione e relazioni](./media/sql-database-file-space-management/storage-types.png) 

## <a name="query-a-database-for-storage-space-information"></a>Eseguire una query su un database per ottenere informazioni sullo spazio di archiviazione

Le query seguenti possono essere usate per determinare le quantità di spazio di archiviazione per un database.  

### <a name="database-data-space-used"></a>Spazio dati del database usato
Modificare la query seguente per restituire la quantità di spazio dati del database usato.  L'unità di misura dei risultati di query è costituita da MB.

```sql
-- Connect to master
-- Database data space used in MB
SELECT TOP 1 storage_in_megabytes AS DatabaseDataSpaceUsedInMB
FROM sys.resource_stats
WHERE database_name = 'db1'
ORDER BY end_time DESC
```

### <a name="database-data-space-allocated-and-unused-allocated-space"></a>Spazio di dati del database allocato e spazio allocato non usato
Usare la query seguente per restituire la quantità di spazio dati di database allocato e la quantità di spazio non usato allocato.  L'unità di misura dei risultati di query è costituita da MB.

```sql
-- Connect to database
-- Database data space allocated in MB and database data space allocated unused in MB
SELECT SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, 
SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB 
FROM sys.database_files
GROUP BY type_desc
HAVING type_desc = 'ROWS'
```
 
### <a name="database-data-max-size"></a>Dimensioni massime dei dati del database
Modificare la query seguente per restituire le dimensioni massime dei dati del database.  L'unità di misura dei risultati di query è costituita da byte.

```sql
-- Connect to database
-- Database data max size in bytes
SELECT DATABASEPROPERTYEX('db1', 'MaxSizeInBytes') AS DatabaseDataMaxSizeInBytes
```

## <a name="understanding-types-of-storage-space-for-an-elastic-pool"></a>Informazioni sui tipi di spazio di archiviazione per un pool elastico

La comprensione delle quantità di spazio di archiviazione seguenti è importante per la gestione dello spazio file di un pool elastico.

|Quantità di pool elastico|Definizione|Commenti|
|---|---|---|
|**Spazio dati usato**|Somma dello spazio dati usato da tutti i database nel pool elastico.||
|**Spazio dati allocato**|Somma dello spazio dati allocato da tutti i database nel pool elastico.||
|**Spazio dati allocato ma non usato**|Differenza tra la quantità di spazio dati allocato e lo spazio dati usato da tutti i database nel pool elastico.|Questa quantità rappresenta la quantità massima di spazio allocato per il pool elastico che può essere recuperata compattando i file di dati del database.|
|**Dimensioni massime dei dati**|Quantità massima di spazio dati che può essere usata dal pool elastico per tutti i rispettivi database.|Lo spazio allocato per il pool elastico non deve superare le dimensioni massime del pool elastico.  Se si verifica tale situazione, lo spazio allocato e non usato può essere recuperato compattando i file di dati del database.|
||||

## <a name="query-an-elastic-pool-for-storage-space-information"></a>Eseguire una query su un pool elastico per ottenere informazioni sullo spazio di archiviazione

Le query seguenti possono essere usate per determinare le quantità di spazio di archiviazione per un pool elastico.  

### <a name="elastic-pool-data-space-used"></a>Spazio dati del pool elastico usato
Modificare la query seguente per restituire la quantità di spazio dati del pool elastico usato.  L'unità di misura dei risultati di query è costituita da MB.

```sql
-- Connect to master
-- Elastic pool data space used in MB  
SELECT TOP 1 avg_storage_percent * elastic_pool_storage_limit_mb AS ElasticPoolDataSpaceUsedInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

### <a name="elastic-pool-data-space-allocated-and-unused-allocated-space"></a>Spazio di dati del pool elastico allocato e spazio allocato non usato

Modificare lo script di PowerShell seguente per restituire una tabella che elenca lo spazio allocato e lo spazio allocato non usato per ogni database in un pool elastico. La tabella dispone i database in ordine decrescente in base alla quantità di spazio allocato non usato.  L'unità di misura dei risultati di query è costituita da MB.  

I risultati delle query per determinare lo spazio allocato per ogni database nel pool possono essere sommati per determinare lo spazio totale allocato per il pool elastico. Lo spazio allocato del pool elastico non deve superare le dimensioni massime del pool elastico.  

```powershell
# Resource group name
$resourceGroupName = "rg1" 
# Server name
$serverName = "ls2" 
# Elastic pool name
$poolName = "ep1"
# User name for server
$userName = "name"
# Password for server
$password = "password"

# Get list of databases in elastic pool
$databasesInPool = Get-AzureRmSqlElasticPoolDatabase `
    -ResourceGroupName $resourceGroupName `
    -ServerName $serverName `
    -ElasticPoolName $poolName
$databaseStorageMetrics = @()

# For each database in the elastic pool,
# get its space allocated in MB and space allocated unused in MB.
# Requires SQL Server PowerShell module – see here to install.  
foreach ($database in $databasesInPool)
{
    $sqlCommand = "SELECT DB_NAME() as DatabaseName, `
    SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, `
    SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB `
    FROM sys.database_files `
    GROUP BY type_desc `
    HAVING type_desc = 'ROWS'"
    $serverFqdn = "tcp:" + $serverName + ".database.windows.net,1433"
    $databaseStorageMetrics = $databaseStorageMetrics + 
        (Invoke-Sqlcmd -ServerInstance $serverFqdn `
        -Database $database.DatabaseName `
        -Username $userName `
        -Password $password `
        -Query $sqlCommand)
}
# Display databases in descending order of space allocated unused
Write-Output "`n" "ElasticPoolName: $poolName"
Write-Output $databaseStorageMetrics | Sort `
    -Property DatabaseDataSpaceAllocatedUnusedInMB `
    -Descending | Format-Table
```

Lo screenshot seguente mostra un esempio di output dello script:

![Esempio di spazio allocato del pool elastico e spazio allocato non usato](./media/sql-database-file-space-management/elastic-pool-allocated-unused.png)

### <a name="elastic-pool-data-max-size"></a>Dimensioni massime dei dati del pool elastico

Modificare la query T-SQL seguente per restituire le dimensioni massime dei dati del pool elastico.  L'unità di misura dei risultati di query è costituita da MB.

```sql
-- Connect to master
-- Elastic pools max size in MB
SELECT TOP 1 elastic_pool_storage_limit_mb AS ElasticPoolMaxSizeInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

## <a name="reclaim-unused-allocated-space"></a>Recuperare lo spazio allocato non usato

Dopo l'identificazione dei database per il recupero di spazio allocato non usato, modificare il comando seguente per compattare i file di dati per ogni database.

```sql
-- Shrink database data space allocated.
DBCC SHRINKDATABASE (N'db1')
```

Per altre informazioni su questo comando, vedere [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql). 

> [!IMPORTANT] 
> Prendere in considerazione la ricompilazione degli indici di database dopo la compattazione dei file di dati del database, in quanto gli indici possono diventare frammentati e perdere l'efficacia di ottimizzazione delle prestazioni. Se ciò si verifica, è necessario ricompilare gli indici. Per altre informazioni sulla frammentazione e sulla ricompilazione degli indici, vedere [Riorganizzare e ricompilare gli indici](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulle dimensioni massime dei database, vedere:
  - [Limiti del modello di acquisto basato su vCore per il database SQL di Azure per un database singolo](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases)
  - [Limiti di risorse per i database singoli usando il modello di acquisto basato su DTU](https://docs.microsoft.com/azure/sql-database/sql-database-dtu-resource-limits-single-databases)
  - [Limiti del modello di acquisto in base ai vCore per il database SQL di Azure per i pool elastici](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools)
  - [Limiti di risorse per i pool elastici usando il modello di acquisto basato su DTU](https://docs.microsoft.com/azure/sql-database/sql-database-dtu-resource-limits-elastic-pools)
- Per altre informazioni sul comando `SHRINKDATABASE`, vedere [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql). 
- Per altre informazioni sulla frammentazione e sulla ricompilazione degli indici, vedere [Riorganizzare e ricompilare gli indici](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).
