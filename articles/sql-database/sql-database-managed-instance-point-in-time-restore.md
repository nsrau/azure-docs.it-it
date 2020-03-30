---
title: Istanza gestita - Ripristino temporizzato (PITR)Managed instance - Point-in-time restore (PITR)
description: Ripristinare un database SQL in un'istanza gestita a un punto precedente nel tempo.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, mathoma
ms.date: 08/25/2019
ms.openlocfilehash: 27f465e6864d0ff639e825c8a816d86648bd8853
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268808"
---
# <a name="restore-a-sql-database-in-a-managed-instance-to-a-previous-point-in-time"></a>Ripristinare un database SQL in un'istanza gestita a un punto precedente nel tempoRestore a SQL database in a managed instance to a previous point in time

Utilizzare il ripristino temporizzato (PITR) per creare un database come copia di un altro database da un momento in passato. Questo articolo descrive come eseguire un ripristino temporizzato di un database in un'istanza gestita del database SQL di Azure.This article describes how to do a point-in-time restore of a database in an Azure SQL Database managed instance.

Il ripristino temporizzato è utile negli scenari di ripristino, ad esempio gli eventi imprevisti causati da errori, i dati caricati in modo non corretto o l'eliminazione di dati cruciali. È inoltre possibile utilizzarlo semplicemente per il test o il controllo. I file di backup vengono conservati per 7-35 giorni, a seconda delle impostazioni del database.

Il ripristino temporizzato può ripristinare un database:Time restore in-time can restore a database:

- da un database esistente.
- da un database eliminato.
- alla stessa istanza gestita o a un'altra istanza gestita. 

## <a name="limitations"></a>Limitazioni

Il ripristino temporizzato in un'istanza gestita presenta le limitazioni seguenti:Point in time restore to a managed instance has the following limitations:

- Quando si esegue il ripristino da un'istanza gestita a un'altra, entrambe le istanze devono trovarsi nella stessa sottoscrizione e nella stessa area. Il ripristino tra aree e intersubscription non è attualmente supportato.
- Non è possibile eseguire il ripristino temporizzato di un'intera istanza gestita. Questo articolo spiega solo cosa è possibile fare: ripristino temporizzato di un database ospitato in un'istanza gestita.

> [!WARNING]
> Tenere presente le dimensioni di archiviazione dell'istanza gestita. A seconda delle dimensioni dei dati da ripristinare, è possibile esaurire l'archiviazione dell'istanza. Se lo spazio disponibile non è sufficiente per i dati ripristinati, usare un approccio diverso.

Nella tabella seguente vengono illustrati scenari di ripristino temporizzato per le istanze gestite:The following table shows point-in-time restore scenarios for managed instances:

|           |Ripristinare il database esistente nella stessa istanza gestitaRestore existing DB to same managed instance| Ripristinare il database esistente in un'altra istanza gestitaRestore existing DB to another managed instance|Ripristinare il database ripristinato nella stessa istanza gestitaRestore dropped dropped d to same managed instance|Ripristinare il database eliminato in un'altra istanza gestitaRestore dropped dropped db to another managed instance|
|:----------|:----------|:----------|:----------|:----------|
|**Portale di Azure**| Sì|No |Sì|No|
|**Interfaccia della riga di comando di AzureAzure**|Sì |Sì |No|No|
|**Powershell**| Sì|Sì |Sì|Sì|

## <a name="restore-an-existing-database"></a>Ripristinare un database esistente

Ripristinare un database esistente nella stessa istanza usando il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure.Restore an existing database to the same instance by using the Azure portal, PowerShell, or the Azure CLI. Per ripristinare un database in un'altra istanza, usare PowerShell o l'interfaccia della riga di comando di Azure in modo da poter specificare le proprietà per l'istanza gestita di destinazione e il gruppo di risorse. Se non si specificano questi parametri, il database verrà ripristinato nell'istanza esistente per impostazione predefinita. Il portale di Azure attualmente non supporta il ripristino in un'altra istanza.

# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Accedere al [portale](https://portal.azure.com)di Azure . 
2. Passare all'istanza gestita e selezionare il database da ripristinare.
3. Selezionare **Ripristina** nella pagina del database:

    ![Ripristinare un database tramite il portale di AzureRestore a database by using the Azure portal](media/sql-database-managed-instance-point-in-time-restore/restore-database-to-mi.png)

4. Nella pagina **Ripristina** selezionare il punto per la data e l'ora in cui si desidera ripristinare il database.
5. Selezionare **Conferma** per ripristinare il database. Questa azione avvia il processo di ripristino, che crea un nuovo database e lo popola con i dati del database originale nel momento specificato. Per ulteriori informazioni sul processo di ripristino, vedere [Tempo di ripristino](sql-database-recovery-using-backups.md#recovery-time).

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Se Azure PowerShell non è già installato, vedere [Installare il modulo di Azure PowerShell.If](https://docs.microsoft.com/powershell/azure/install-az-ps)you don't already have Azure PowerShell installed, see Install the Azure PowerShell module .

Per ripristinare il database tramite PowerShell, specificare i valori per i parametri nel comando seguente. Quindi, eseguire il comando:

```powershell-interactive
$subscriptionId = "<Subscription ID>"
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source-database>"
$pointInTime = "2018-06-27T08:51:39.3882806Z"
$targetDatabase = "<Name of new database to be created>"

Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                              -InstanceName $managedInstanceName `
                              -Name $databaseName `
                              -PointInTime $pointInTime `
                              -TargetInstanceDatabaseName $targetDatabase `
```

Per ripristinare il database in un'altra istanza gestita, specificare anche i nomi del gruppo di risorse di destinazione e dell'istanza gestita di destinazione:  

```powershell-interactive
$targetResourceGroupName = "<Resource group of target managed instance>"
$targetInstanceName = "<Target managed instance name>"

Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                              -InstanceName $managedInstanceName `
                              -Name $databaseName `
                              -PointInTime $pointInTime `
                              -TargetInstanceDatabaseName $targetDatabase `
                              -TargetResourceGroupName $targetResourceGroupName `
                              -TargetInstanceName $targetInstanceName 
```

Per informazioni dettagliate, vedere [Restore-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase).

# <a name="azure-cli"></a>[Interfaccia della riga di comando di AzureAzure](#tab/azure-cli)

Se l'interfaccia della riga di comando di Azure non è già installata, vedere [Installare l'interfaccia della riga di comando](/cli/azure/install-azure-cli?view=azure-cli-latest)di Azure.

Per ripristinare il database usando l'interfaccia della riga di comando di Azure, specificare i valori per i parametri nel comando seguente. Quindi, eseguire il comando:

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```

Per ripristinare il database in un'altra istanza gestita, specificare anche i nomi del gruppo di risorse di destinazione e dell'istanza gestita:  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

Per una spiegazione dettagliata dei parametri disponibili, vedere la [documentazione dell'interfaccia della riga di comando per il ripristino di un database in un'istanza gestita.](https://docs.microsoft.com/cli/azure/sql/midb?view=azure-cli-latest#az-sql-midb-restore)

---

## <a name="restore-a-deleted-database"></a>Ripristino di un database eliminato

Il ripristino di un database eliminato può essere eseguito tramite PowerShell o il portale di Azure.Restoring a deleted database can be done by using PowerShell or Azure portal. To restore a deleted database to the same instance, use either the Azure portal or PowerShell. To restore a deleted database to another instance, use PowerShell. 

### <a name="portal"></a>Portale 


Per ripristinare un database gestito tramite il portale di Azure, aprire la pagina Panoramica dell'istanza gestita e selezionare **Database eliminati.** Scegliere un database eliminato che si desidera ripristinare e digitare il nome del nuovo database che verrà creato con i dati ripristinati dal backup.

  ![Screenshot del ripristino del database dell'istanza SQL di Azure eliminato](./media/sql-database-recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="powershell"></a>PowerShell

Per ripristinare un database nella stessa istanza, aggiornare i valori dei parametri e quindi eseguire il comando di PowerShell seguente:To restore a database to the same instance, update the parameter values and then run the following PowerShell command: 

```powershell-interactive
$subscriptionId = "<Subscription ID>"
Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$deletedDatabaseName = "<Source database name>"
$targetDatabaseName = "<target database name>"

$deletedDatabase = Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName $resourceGroupName `
-InstanceName $managedInstanceName -DatabaseName $deletedDatabaseName

Restore-AzSqlinstanceDatabase -Name $deletedDatabase.Name `
   -InstanceName $deletedDatabase.ManagedInstanceName `
   -ResourceGroupName $deletedDatabase.ResourceGroupName `
   -DeletionDate $deletedDatabase.DeletionDate `
   -PointInTime UTCDateTime `
   -TargetInstanceDatabaseName $targetDatabaseName
```

Per ripristinare il database in un'altra istanza gestita, specificare anche i nomi del gruppo di risorse di destinazione e dell'istanza gestita di destinazione:

```powershell-interactive
$targetResourceGroupName = "<Resource group of target managed instance>"
$targetInstanceName = "<Target managed instance name>"

Restore-AzSqlinstanceDatabase -Name $deletedDatabase.Name `
   -InstanceName $deletedDatabase.ManagedInstanceName `
   -ResourceGroupName $deletedDatabase.ResourceGroupName `
   -DeletionDate $deletedDatabase.DeletionDate `
   -PointInTime UTCDateTime `
   -TargetInstanceDatabaseName $targetDatabaseName `
   -TargetResourceGroupName $targetResourceGroupName `
   -TargetInstanceName $targetInstanceName 
```

## <a name="overwrite-an-existing-database"></a>Sovrascrivere un database esistente

Per sovrascrivere un database esistente, è necessario:

1. Eliminare il database esistente che si desidera sovrascrivere.
2. Rinominare il database ripristinato in tempo con il nome del database eliminato.

### <a name="drop-the-original-database"></a>Eliminare il database originale

È possibile eliminare il database usando il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure.You can drop the database by using the Azure portal, PowerShell, or the Azure CLI.

È inoltre possibile eliminare il database connettendosi direttamente all'istanza gestita, avviando SQL Server Management Studio (SSMS) e quindi eseguendo il comando Transact-SQL (T-SQL) seguente:

```sql
DROP DATABASE WorldWideImporters;
```

Utilizzare uno dei metodi seguenti per connettersi al database nell'istanza gestita:

- [SSMS/Azure Data Studio tramite una macchina virtuale di AzureSSMS/Azure Data Studio via an Azure virtual machine](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Da punto a sito](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Endpoint pubblico](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

# <a name="portal"></a>[Portale](#tab/azure-portal)

Nel portale di Azure selezionare il database dall'istanza gestita e quindi selezionare **Elimina**.

   ![Eliminare un database tramite il portale di AzureDelete a database by using the Azure portal](media/sql-database-managed-instance-point-in-time-restore/delete-database-from-mi.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Usare il comando PowerShell seguente per eliminare un database esistente da un'istanza gestita:Use the following PowerShell command to drop an existing database from a managed instance:

```powershell
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source database>"

Remove-AzSqlInstanceDatabase -Name $databaseName -InstanceName $managedInstanceName -ResourceGroupName $resourceGroupName
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di AzureAzure](#tab/azure-cli)

Usare il comando dell'interfaccia della riga di comando di Azure seguente per eliminare un database esistente da un'istanza gestita:Use the following Azure CLI command to drop an existing database from a managed instance:

```azurecli-interactive
az sql midb delete -g mygroupname --mi myinstancename -n mymanageddbname
```

---

### <a name="alter-the-new-database-name-to-match-the-original-database-name"></a>Modificare il nuovo nome del database in modo che corrisponda al nome del database originale

Connettersi direttamente all'istanza gestita e avviare SQL Server Management Studio. Eseguire quindi la query Transact-SQLTransact-SQL (T-SQL) seguente. La query cambierà il nome del database ripristinato in quello del database eliminato che si intende sovrascrivere.

```sql
ALTER DATABASE WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```

Utilizzare uno dei metodi seguenti per connettersi al database nell'istanza gestita:

- [Macchina virtuale di AzureAzure virtual machine](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Da punto a sito](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Endpoint pubblico](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

## <a name="next-steps"></a>Passaggi successivi

Informazioni sui [backup automatici](sql-database-automated-backups.md).
