---
title: Scale single database resources
description: Questo articolo illustra come ridimensionare le risorse di calcolo e di archiviazione disponibili per un database singolo nel database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 04/26/2019
ms.openlocfilehash: 8d4917bb8956185e0cb557368fbb0c64343c0ac6
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422551"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Ridimensionare le risorse di database singoli nel database SQL di Azure

This article describes how to scale the compute and storage resources available for an Azure SQL Database in the provisioned compute tier. Alternatively, the [serverless compute tier](sql-database-serverless.md) provides compute auto-scaling and bills per second for compute used.

## <a name="change-compute-size-vcores-or-dtus"></a>Change compute size (vCores or DTUs)

After initially picking the number of vCores or DTUs, you can scale a single database up or down dynamically based on actual experience using the [Azure portal](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShell](/powershell/module/az.sql/set-azsqldatabase), the [Azure CLI](/cli/azure/sql/db#az-sql-db-update), or the [REST API](https://docs.microsoft.com/rest/api/sql/databases/update).

Il video seguente mostra come modificare in modo dinamico il livello di servizio e le dimensioni di calcolo per aumentare le DTU disponibili per un singolo database.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]

> [!IMPORTANT]
> In alcune circostanze, può essere necessario compattare un database per recuperare spazio inutilizzato. Per altre informazioni, vedere [Gestire lo spazio file nel database SQL di Azure](sql-database-file-space-management.md).

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>Impact of changing service tier or rescaling compute size

Changing the service tier or compute size of mainly involves the service performing the following steps:

1. Create new compute instance for the database  

    A new compute instance is created with the requested service tier and compute size. For some combinations of service tier and compute size changes, a replica of the database must be created in the new compute instance which involves copying data and can strongly influence the overall latency. Regardless, the database remains online during this step, and connections continue to be directed to the database in the original compute instance.

2. Switch routing of connections to new compute instance

    Existing connections to the database in the original compute instance are dropped. Any new connections are established to the database in the new compute instance. For some combinations of service tier and compute size changes, database files are detached and reattached during the switch.  Regardless, the switch can result in a brief service interruption when the database is unavailable generally for less than 30 seconds and often for only a few seconds. If there are long running transactions running when connections are dropped, the duration of this step may take longer in order to recover aborted transactions. [Accelerated Database Recovery](sql-database-accelerated-database-recovery.md) can reduce the impact from aborting long running transactions.

> [!IMPORTANT]
> No data is lost during any step in the workflow. Make sure that you have implemented some [retry logic](sql-database-connectivity-issues.md) in the applications and components that are using Azure SQL Database while the service tier is changed.

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>Latency of changing service tier or rescaling compute size

The estimated latency to change the service tier or rescale the compute size of a single database or elastic pool is parameterized as follows:

|Livello di servizio|Basic single database,</br>Standard (S0-S1)|Basic elastic pool,</br>Standard (S2-S12), </br>Hyperscale, </br>General Purpose single database or elastic pool|Premium or Business Critical single database or elastic pool|
|:---|:---|:---|:---|
|**Basic single database,</br> Standard (S0-S1)**|&bull; &nbsp;Constant time latency independent of space used</br>&bull; &nbsp;Typically, less than 5 minutes|&bull; &nbsp;Latency proportional to database space used due to data copying</br>&bull; &nbsp;Typically, less than 1 minute per GB of space used|&bull; &nbsp;Latency proportional to database space used due to data copying</br>&bull; &nbsp;Typically, less than 1 minute per GB of space used|
|**Basic elastic pool, </br>Standard (S2-S12), </br>Hyperscale, </br>General Purpose single database or elastic pool**|&bull; &nbsp;Latency proportional to database space used due to data copying</br>&bull; &nbsp;Typically, less than 1 minute per GB of space used|&bull; &nbsp;Constant time latency independent of space used</br>&bull; &nbsp;Typically, less than 5 minutes|&bull; &nbsp;Latency proportional to database space used due to data copying</br>&bull; &nbsp;Typically, less than 1 minute per GB of space used|
|**Premium or Business Critical single database or elastic pool**|&bull; &nbsp;Latency proportional to database space used due to data copying</br>&bull; &nbsp;Typically, less than 1 minute per GB of space used|&bull; &nbsp;Latency proportional to database space used due to data copying</br>&bull; &nbsp;Typically, less than 1 minute per GB of space used|&bull; &nbsp;Latency proportional to database space used due to data copying</br>&bull; &nbsp;Typically, less than 1 minute per GB of space used|

> [!TIP]
> To monitor in-progress operations, see: [Manage operations using the SQL REST API](https://docs.microsoft.com/rest/api/sql/operations/list), [Manage operations using CLI](/cli/azure/sql/db/op), [Monitor operations using T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) and these two PowerShell commands: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) and [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

### <a name="cancelling-service-tier-changes-or-compute-rescaling-operations"></a>Cancelling service tier changes or compute rescaling operations

A service tier change or compute rescaling operation can be canceled.

#### <a name="azure-portal"></a>Portale di Azure

In the database overview blade, navigate to **Notifications** and click on the tile indicating there is an ongoing operation:

![Ongoing operation](media/sql-database-single-database-scale/ongoing-operations.png)

Next, click on the button labeled **Cancel this operation**.

![Cancel ongoing operation](media/sql-database-single-database-scale/cancel-ongoing-operation.png)

#### <a name="powershell"></a>PowerShell

From a PowerShell command prompt, set the `$resourceGroupName`, `$serverName`, and `$databaseName`, and then run the following command:

```powershell
$operationName = (az sql db op list --resource-group $resourceGroupName --server $serverName --database $databaseName --query "[?state=='InProgress'].name" --out tsv)
if (-not [string]::IsNullOrEmpty($operationName)) {
    (az sql db op cancel --resource-group $resourceGroupName --server $serverName --database $databaseName --name $operationName)
        "Operation " + $operationName + " has been canceled"
}
else {
    "No service tier change or compute rescaling operation found"
}
```

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>Additional considerations when changing service tier or rescaling compute size

- Se si esegue l'aggiornamento a un livello di servizio o dimensioni di calcolo superiori, le dimensioni massime del database non aumentano a meno che non si specifichino esplicitamente dimensioni più elevate (massime).
- Per effettuare il downgrade di un database, la relativa quantità di spazio usato deve essere inferiore alle dimensioni massime consentite per il livello di servizio e le dimensioni di calcolo di destinazione.
- Quando si effettua il downgrade dal livello **Premium** al livello **Standard**, viene applicato un costo per le risorse di archiviazione extra se (1) le dimensioni massime del database sono supportate nelle dimensioni di calcolo di destinazione e (2) le dimensioni massime superano lo spazio di archiviazione incluso delle dimensioni di calcolo di destinazione. For example, if a P1 database with a max size of 500 GB is downsized to S3, then an extra storage cost applies since S3 supports a max size of 1 TB and its included storage amount is only 250 GB. Lo spazio di archiviazione extra è quindi 500 GB - 250 GB = 250 GB. Per i prezzi delle risorse di archiviazione extra, vedere [Prezzi di Database SQL](https://azure.microsoft.com/pricing/details/sql-database/). Se la quantità effettiva di spazio usato è inferiore allo spazio di archiviazione incluso, questo costo aggiuntivo può essere evitato riducendo le dimensioni massime del database fino allo spazio incluso.
- Quando si aggiorna un database con la [replica geografica](sql-database-geo-replication-portal.md) abilitata, aggiornare i database secondari al livello di servizio e alle dimensioni di calcolo desiderati prima di aggiornare il database primario (indicazione generale per ottenere prestazioni ottimali). Durante l'aggiornamento a un'edizione diversa è necessario aggiornare per primo il database secondario.
- Quando si effettua il downgrade di un database con la [replica geografica](sql-database-geo-replication-portal.md) abilitata, eseguire il downgrade dei database primari al livello di servizio e alle dimensioni di calcolo desiderati prima del downgrade del database secondario (indicazione generale per ottenere prestazioni ottimali). Al momento del downgrade a un'edizione diversa, è necessario eseguire questa operazione iniziando dal database primario.
- Le offerte per il ripristino del servizio sono diverse per i vari livelli di servizio. In caso di downgrade al livello **Basic**, il periodo di conservazione dei backup sarà inferiore. Vedere l'articolo relativo ai [backup del database SQL di Azure](sql-database-automated-backups.md).
- Le nuove proprietà del database non vengono applicate finché non sono state completate le modifiche.

### <a name="billing-during-compute-rescaling"></a>Billing during compute rescaling

Viene fatturata ogni ora per cui un database esiste usando il livello di servizio più elevato e le dimensioni di calcolo applicati in quell'ora, indipendentemente dall'uso o dal fatto che il database sia stato attivo per meno di un'ora. Ad esempio, se si crea un database singolo che viene eliminato cinque minuti dopo, in fattura viene riportato l'addebito relativo a un'ora di database.

## <a name="change-storage-size"></a>modifica delle dimensioni di archiviazione

### <a name="vcore-based-purchasing-model"></a>Modello di acquisto basato su vCore

- Il provisioning dell'archiviazione può essere effettuato fino al limite massimo delle dimensioni tramite incrementi di 1 GB. Lo spazio di archiviazione dei dati minimo configurabile è 5 GB
- Il provisioning dell'archiviazione per un database singolo può essere effettato aumentandone o diminuendone le dimensioni massime tramite il [portale di Azure](https://portal.azure.com), [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShell](/powershell/module/az.sql/set-azsqldatabase), l'[interfaccia della riga di comando di Azure](/cli/azure/sql/db#az-sql-db-update) o l'[API REST](https://docs.microsoft.com/rest/api/sql/databases/update).
- Il database SQL alloca automaticamente il 30% di archiviazione aggiuntiva per i file di log e 32 GB per ogni vCore per TempDB, ma senza superare 384 GB. TempDB è disponibile in un'unità SSD collegata in tutti i livelli di servizio.
- Il prezzo dell'archiviazione per un database singolo corrisponde alla somma delle dimensioni di archiviazione dei dati e degli spazi di archiviazione dei log moltiplicata per il prezzo unitario dell'archiviazione del livello di servizio. Il costo di TempDB è compreso nel prezzo dei vCore. Per informazioni dettagliate sul prezzo delle risorse di archiviazione extra, vedere [Prezzi di Database SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> In alcune circostanze, può essere necessario compattare un database per recuperare spazio inutilizzato. Per altre informazioni, vedere [Gestire lo spazio file nel database SQL di Azure](sql-database-file-space-management.md).

### <a name="dtu-based-purchasing-model"></a>modello di acquisto basato su DTU

- Il prezzo DTU per un singolo database include una determinata quantità di risorse di archiviazione senza costi aggiuntivi. Le risorse di archiviazione extra rispetto alla quantità inclusa possono essere sottoposte a provisioning per un costo aggiuntivo fino alla quantità massima in incrementi di 250 GB fino a 1 TB e quindi in incrementi di 256 GB oltre 1 TB. Per gli spazi di archiviazione inclusi e i limiti di dimensioni massime, vedere [Database singolo: dimensioni di archiviazione e dimensioni di calcolo](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- Il provisioning delle risorse di archiviazione extra per un database singolo può essere effettuato aumentandone le dimensioni massime tramite il portale di Azure, [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShell](/powershell/module/az.sql/set-azsqldatabase), l'[interfaccia della riga di comando di Azure](/cli/azure/sql/db#az-sql-db-update) o l'[API REST](https://docs.microsoft.com/rest/api/sql/databases/update).
- Il prezzo delle risorse di archiviazione extra per un singolo database corrisponde allo spazio di archiviazione extra moltiplicato per il prezzo unitario del livello di servizio. Per informazioni dettagliate sul prezzo delle risorse di archiviazione extra, vedere [Prezzi di Database SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> In alcune circostanze, può essere necessario compattare un database per recuperare spazio inutilizzato. Per altre informazioni, vedere [Gestire lo spazio file nel database SQL di Azure](sql-database-file-space-management.md).

## <a name="p11-and-p15-constraints-when-max-size-greater-than-1-tb"></a>P11 and P15 constraints when max size greater than 1 TB

More than 1 TB of storage in the Premium tier is currently available in all regions except: China East, China North, Germany Central, Germany Northeast, West Central US, US DoD regions, and US Government Central. In queste aree la quantità massima di risorse di archiviazione nel livello Premium è limitata a 1 TB. Ai database P11 e P15 con dimensioni massime maggiori di 1 TB vengono applicate le considerazioni e le limitazioni seguenti:

- If the max size for a P11 or P15 database was ever set to a value greater than 1 TB, then can it only be restored or copied to a P11 or P15 database.  Subsequently, the database can be rescaled to a different compute size provided the amount of space allocated at the time of the rescaling operation does not exceed max size limits of the new compute size.
- Per gli scenari di replica geografica attiva:
  - Impostazione di una relazione di replica geografica: se il database primario è P11 o P15, devono esserlo anche i database secondari. Le dimensioni di calcolo inferiori non vengono accettate come database secondari in quanto non supportano l'opzione superiore a 1 TB.
  - Aggiornamento del database primario in una relazione di replica geografica: portando a oltre 1 TB le dimensioni massime di un database primario, viene attivata la stessa modifica nel database secondario. Entrambi gli aggiornamenti devono avere esito positivo per applicare la modifica al database primario. Vengono applicate limitazioni per l'opzione da oltre 1 TB. Se il database secondario si trova in un'area che non supporta l'opzione da oltre 1 TB, il database primario non viene aggiornato.
- Il servizio di importazione/esportazione per caricare i database P11 o P15 con oltre 1 TB non è supportato. Usare SqlPackage.exe per [importare](sql-database-import.md) ed [esportare](sql-database-export.md) i dati.

## <a name="next-steps"></a>Passaggi successivi

Per i limiti generali delle risorse, vedere [Limiti delle risorse basate su vCore del database SQL - database singoli](sql-database-vcore-resource-limits-single-databases.md), [Limiti delle risorse basate su DTU del database SQL - pool elastici](sql-database-dtu-resource-limits-single-databases.md).
