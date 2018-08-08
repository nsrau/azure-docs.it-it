---
title: Gestione dello spazio file del database SQL di Azure| Microsoft Docs
description: Questa pagina descrive come gestire lo spazio file con il database SQL di Azure e fornisce esempi di codice per determinare se è necessario compattare un database e come eseguire un'operazione di compattazione del database.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: how-to
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: carlrab
ms.openlocfilehash: 1ecc0ce08ef42f5f5935bca29e8269be2ea142f0
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39416004"
---
# <a name="manage-file-space-in-azure-sql-database"></a>Gestire lo spazio file nel database SQL di Azure

Questo articolo descrive i diversi tipi di spazio di archiviazione nel database SQL di Azure e le operazioni che è possibile eseguire quando lo spazio file allocato per i database e i pool elastici deve essere gestito dal cliente.

## <a name="overview"></a>Panoramica

Nel database SQL di Azure, le metriche per le dimensioni dello spazio di archiviazione visualizzate nel portale di Azure e le API seguenti misurano il numero di pagine di dati usate per i database e i pool elastici:
- API per le metriche basate su Azure Resource Manager tra cui [get-metrics](https://docs.microsoft.com/powershell/module/azurerm.insights/get-azurermmetric) di PowerShell
- T-SQL: [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)
- T-SQL: [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)
- T-SQL: [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)

Ci sono modelli di carico di lavoro in cui l'allocazione dello spazio nei file di dati sottostanti per i database diventa superiore al numero di pagine di dati usate nei file di dati. Questo scenario può verificarsi quando lo spazio usato aumenta e quindi i dati vengono successivamente eliminati. Quando i dati vengono eliminati, lo spazio file allocato non viene recuperato automaticamente. In questi scenari, lo spazio allocato per un database o un pool può superare i limiti massimi supportati definiti (o supportati) per il database e, di conseguenza, impedire l'aumento dei dati o le modifiche ai livelli di prestazioni, anche se lo spazio del database effettivamente usato è inferiore al limite di spazio massimo. Per ovviare a questo problema, può essere necessario compattare il database per ridurre lo spazio allocato ma non usato al suo interno.

Il servizio di database SQL non compatta automaticamente i file di database per recuperare lo spazio allocato non usato, a causa del potenziale impatto sulle prestazioni del database. È tuttavia possibile compattare il file in un database quando si vuole, seguendo la procedura descritta in [Recuperare lo spazio allocato non usato](#reclaim-unused-allocated-space). 

> [!NOTE]
> A differenza dei file di dati, il servizio di database SQL compatta automaticamente i file di log, in quanto tale operazione non influisce sulle prestazioni del database.

## <a name="understanding-the-types-of-storage-space-for-a-database"></a>Informazioni sui tipi di spazio di archiviazione per un database

Per gestire lo spazio file, è necessario comprendere i termini seguenti correlati all'archiviazione di database sia per un database singolo che per un pool elastico.

|Termine relativo allo spazio di archiviazione|Definizione|Commenti|
|---|---|---|
|**Spazio dati usato**|Quantità di spazio usato per archiviare i dati del database in pagine da 8 KB.|In genere, questo spazio usato aumenta quando vengono inseriti i dati e diminuisce quando vengono eliminati. In alcuni casi, lo spazio usato non cambia in caso di inserimenti o eliminazioni, a seconda della quantità e del modello di dati coinvolti nell'operazione e dell'eventuale frammentazione. Ad esempio, se si elimina una riga da ogni pagina di dati, non si riduce necessariamente lo spazio usato.|
|**Spazio allocato**|Quantità di spazio file formattato messo a disposizione per l'archiviazione dei dati del database|Lo spazio allocato aumenta automaticamente, ma non diminuisce mai dopo le eliminazioni. Questo comportamento assicura che gli inserimenti futuri avvengano più velocemente, perché non è necessario riformattare lo spazio.|
|**Spazio allocato ma non usato**|Quantità di spazio file di dati non usato allocato per il database.|Questa quantità è la differenza tra la quantità di spazio allocato e lo spazio usato e rappresenta la quantità massima di spazio che è possibile recuperare compattando i file di database.|
|**Dimensioni massime**|Quantità massima di spazio dati che il database può usare.|Lo spazio dati allocato non può superare le dimensioni massime dei dati.|
||||

Il diagramma seguente illustra la relazione tra i tipi di spazio di archiviazione.

![Tipi di spazio di archiviazione e relazioni](./media/sql-database-file-space-management/storage-types.png)

## <a name="query-a-database-for-storage-space-information"></a>Eseguire una query su un database per ottenere informazioni sullo spazio di archiviazione

Per determinare se per un singolo database è presente spazio dati allocato ma non usato che si potrebbe voler recuperare, usare le query seguenti:

### <a name="database-data-space-used"></a>Spazio dati del database usato
Modificare la query seguente per restituire la quantità di spazio dati del database usato, in MB.

```sql
-- Connect to master
-- Database data space used in MB
SELECT TOP 1 storage_in_megabytes AS DatabaseDataSpaceUsedInMB
FROM sys.resource_stats
WHERE database_name = 'db1'
ORDER BY end_time DESC
```

### <a name="database-data-allocated-and-allocated-space-unused"></a>Dati del database allocati e spazio allocato non usato
Modificare la query seguente per restituire la quantità di dati del database allocati e lo spazio allocato non usato.

```sql
-- Connect to database
-- Database data space allocated in MB and database data space allocated unused in MB
SELECT SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, 
SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB 
FROM sys.database_files
GROUP BY type_desc
HAVING type_desc = 'ROWS'
```
 
### <a name="database-max-size"></a>Dimensioni massime del database
Modificare la query seguente per restituire le dimensioni massime del database, in byte.

```sql
-- Connect to database
-- Database data max size in bytes
SELECT DATABASEPROPERTYEX('db1', 'MaxSizeInBytes') AS DatabaseDataMaxSizeInBytes
```

## <a name="query-an-elastic-pool-for-storage-space-information"></a>Eseguire una query su un pool elastico per ottenere informazioni sullo spazio di archiviazione

Per determinare se in un pool elastico e per ogni database del pool è presente spazio dati allocato ma non usato che si potrebbe voler recuperare, usare le query seguenti:

### <a name="elastic-pool-data-space-used"></a>Spazio dati del pool elastico usato
Modificare la query seguente per restituire la quantità di spazio dati del pool elastico usato, in MB.

```sql
-- Connect to master
-- Elastic pool data space used in MB  
SELECT TOP 1 avg_storage_percent * elastic_pool_storage_limit_mb AS ElasticPoolDataSpaceUsedInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

### <a name="elastic-pool-data-allocated-and-allocated-space-unused"></a>Dati del pool elastico allocati e spazio allocato non usato

Modificare lo script di PowerShell seguente per restituire una tabella che elenca lo spazio totale allocato e lo spazio allocato non usato per ogni database in un pool elastico. Nella tabella i database sono disposti in ordine decrescente di quantità di spazio allocato non usato.  

I risultati della query per determinare lo spazio allocato per ogni database nel pool possono essere sommati per determinare lo spazio allocato del pool elastico. Lo spazio allocato del pool elastico non deve superare le dimensioni massime del pool elastico.  

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

### <a name="elastic-pool-max-size"></a>Dimensioni massime del pool elastico

Usare la query T-SQL seguente per restituire le dimensioni massime del database elastico, in MB.

```sql
-- Connect to master
-- Elastic pools max size in MB
SELECT TOP 1 elastic_pool_storage_limit_mb AS ElasticPoolMaxSizeInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

## <a name="reclaim-unused-allocated-space"></a>Recuperare lo spazio allocato non usato

Dopo aver determinato la presenza di spazio allocato non usato che si vuole recuperare, usare il comando seguente per compattare lo spazio allocato del database. 

> [!IMPORTANT]
> Per i database in un pool elastico, quelli con la maggior quantità di spazio allocato non usato devono essere compattati per primi per recuperare spazio file più rapidamente.  

Per compattare tutti i file di dati nel database specificato, usare il comando seguente:

```sql
-- Shrink database data space allocated.
DBCC SHRINKDATABASE (N'<database_name>')
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