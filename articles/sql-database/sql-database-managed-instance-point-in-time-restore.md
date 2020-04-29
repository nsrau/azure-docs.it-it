---
title: 'Istanza gestita: ripristino temporizzato (ripristino temporizzato)'
description: Ripristinare un database SQL in un'istanza gestita a un momento precedente.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79268808"
---
# <a name="restore-a-sql-database-in-a-managed-instance-to-a-previous-point-in-time"></a>Ripristinare un database SQL in un'istanza gestita a un momento precedente

Usare il ripristino temporizzato (ripristino temporizzato) per creare un database come copia di un altro database da un po' di tempo nel passato. Questo articolo descrive come eseguire un ripristino temporizzato di un database in un'istanza gestita di database SQL di Azure.

Il ripristino temporizzato è utile in scenari di ripristino, ad esempio eventi imprevisti causati da errori, dati caricati in modo errato o eliminazione di dati cruciali. È anche possibile usarlo semplicemente per i test o il controllo. I file di backup vengono conservati da 7 a 35 giorni, a seconda delle impostazioni del database.

Il ripristino temporizzato consente di ripristinare un database:

- da un database esistente.
- da un database eliminato.
- alla stessa istanza gestita o a un'altra istanza gestita. 

## <a name="limitations"></a>Limitazioni

Il ripristino temporizzato a un'istanza gestita presenta le limitazioni seguenti:

- Quando si esegue il ripristino da un'istanza gestita a un'altra, entrambe le istanze devono trovarsi nella stessa sottoscrizione e nella stessa area. Il ripristino tra più aree e tra sottoscrizioni non è attualmente supportato.
- Il ripristino temporizzato di un'intera istanza gestita non è possibile. Questo articolo illustra solo le operazioni possibili: ripristino temporizzato di un database ospitato in un'istanza gestita.

> [!WARNING]
> Tenere presente le dimensioni di archiviazione dell'istanza gestita. A seconda delle dimensioni dei dati da ripristinare, è possibile che l'archiviazione dell'istanza venga esaurita. Se non è disponibile spazio sufficiente per i dati ripristinati, utilizzare un approccio diverso.

La tabella seguente illustra gli scenari di ripristino temporizzato per le istanze gestite:

|           |Ripristinare il database esistente nella stessa istanza gestita| Ripristinare il database esistente in un'altra istanza gestita|Ripristinare il database eliminato nella stessa istanza gestita|Ripristinare il database eliminato in un'altra istanza gestita|
|:----------|:----------|:----------|:----------|:----------|
|**Azure portal**| Sì|No |Sì|No|
|**Interfaccia della riga di comando di Azure**|Sì |Sì |No|No|
|**PowerShell**| Sì|Sì |Sì|Sì|

## <a name="restore-an-existing-database"></a>Ripristinare un database esistente

Ripristinare un database esistente nella stessa istanza usando il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure. Per ripristinare un database a un'altra istanza, usare PowerShell o l'interfaccia della riga di comando di Azure in modo da poter specificare le proprietà per l'istanza gestita e il gruppo di risorse di destinazione. Se non si specificano questi parametri, per impostazione predefinita il database verrà ripristinato nell'istanza esistente. Il portale di Azure attualmente non supporta il ripristino in un'altra istanza.

# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Accedere al [portale di Azure](https://portal.azure.com). 
2. Passare all'istanza gestita e selezionare il database che si desidera ripristinare.
3. Selezionare **Ripristina** nella pagina database:

    ![Ripristinare un database utilizzando il portale di Azure](media/sql-database-managed-instance-point-in-time-restore/restore-database-to-mi.png)

4. Nella pagina **Ripristina** selezionare il punto per la data e l'ora in cui si desidera ripristinare il database.
5. Selezionare **conferma** per ripristinare il database. Questa azione avvia il processo di ripristino, che crea un nuovo database e lo popola con i dati del database originale nel punto nel tempo specificato. Per ulteriori informazioni sul processo di ripristino, vedere [tempo di ripristino](sql-database-recovery-using-backups.md#recovery-time).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Se Azure PowerShell non è ancora installato, vedere [Install the Azure PowerShell Module](https://docs.microsoft.com/powershell/azure/install-az-ps).

Per ripristinare il database tramite PowerShell, specificare i valori per i parametri nel comando seguente. Eseguire quindi il comando:

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

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Se l'interfaccia della riga di comando di Azure non è ancora installata, vedere [Install the Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

Per ripristinare il database usando l'interfaccia della riga di comando di Azure, specificare i valori per i parametri nel comando seguente. Eseguire quindi il comando:

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

Per una spiegazione dettagliata dei parametri disponibili, vedere la documentazione dell'interfaccia della riga di comando [per il ripristino di un database in un'istanza gestita](https://docs.microsoft.com/cli/azure/sql/midb?view=azure-cli-latest#az-sql-midb-restore).

---

## <a name="restore-a-deleted-database"></a>Ripristino di un database eliminato

Il ripristino di un database eliminato può essere eseguito tramite PowerShell o portale di Azure. Per ripristinare un database eliminato nella stessa istanza, usare il portale di Azure o PowerShell. Per ripristinare un database eliminato a un'altra istanza, usare PowerShell. 

### <a name="portal"></a>Portale 


Per ripristinare un database gestito utilizzando il portale di Azure, aprire la pagina Panoramica istanza gestita e selezionare **database eliminati**. Scegliere un database eliminato che si desidera ripristinare e digitare il nome del nuovo database che verrà creato con i dati ripristinati dal backup.

  ![Schermata del ripristino del database dell'istanza SQL di Azure](./media/sql-database-recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="powershell"></a>PowerShell

Per ripristinare un database nella stessa istanza, aggiornare i valori dei parametri e quindi eseguire il comando PowerShell seguente: 

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

## <a name="overwrite-an-existing-database"></a>Sovrascrivi un database esistente

Per sovrascrivere un database esistente, è necessario:

1. Eliminare il database esistente che si desidera sovrascrivere.
2. Rinominare il database con ripristino temporizzato con il nome del database eliminato.

### <a name="drop-the-original-database"></a>Elimina il database originale

È possibile eliminare il database usando il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure.

È anche possibile eliminare il database connettendosi direttamente all'istanza gestita, avviando SQL Server Management Studio (SSMS) ed eseguendo quindi il comando Transact-SQL (T-SQL) seguente:

```sql
DROP DATABASE WorldWideImporters;
```

Per connettersi al database nell'istanza gestita, utilizzare uno dei metodi seguenti:

- [SSMS/Azure Data Studio tramite una macchina virtuale di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Da punto a sito](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Endpoint pubblico](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

# <a name="portal"></a>[Portale](#tab/azure-portal)

Nella portale di Azure selezionare il database dall'istanza gestita e quindi selezionare **Elimina**.

   ![Eliminare un database usando il portale di Azure](media/sql-database-managed-instance-point-in-time-restore/delete-database-from-mi.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Usare il comando di PowerShell seguente per eliminare un database esistente da un'istanza gestita:

```powershell
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source database>"

Remove-AzSqlInstanceDatabase -Name $databaseName -InstanceName $managedInstanceName -ResourceGroupName $resourceGroupName
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Usare il comando dell'interfaccia della riga di comando di Azure seguente per eliminare un database esistente da un'istanza gestita:

```azurecli-interactive
az sql midb delete -g mygroupname --mi myinstancename -n mymanageddbname
```

---

### <a name="alter-the-new-database-name-to-match-the-original-database-name"></a>Modificare il nome del nuovo database in modo che corrisponda al nome del database originale

Connettersi direttamente all'istanza gestita e avviare SQL Server Management Studio. Eseguire quindi la query Transact-SQL (T-SQL) seguente. La query modificherà il nome del database ripristinato a quello del database eliminato che si desidera sovrascrivere.

```sql
ALTER DATABASE WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```

Per connettersi al database nell'istanza gestita, utilizzare uno dei metodi seguenti:

- [Macchina virtuale di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Da punto a sito](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Endpoint pubblico](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

## <a name="next-steps"></a>Passaggi successivi

Informazioni sui [backup automatici](sql-database-automated-backups.md).
