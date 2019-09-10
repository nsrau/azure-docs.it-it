---
title: Istanza gestita di database SQL-ripristino temporizzato | Microsoft Docs
description: Come ripristinare un database in un'istanza gestita di SQL a un momento precedente.
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
ms.openlocfilehash: 67f13d16dcf6bbe4fa13fe3a6e78d3e4d61e1999
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70862135"
---
# <a name="restore-a-sql-managed-instance-database-to-a-previous-point-in-time"></a>Ripristinare un database di istanza gestita di SQL a un momento precedente

Il ripristino temporizzato (ripristino temporizzato) consente di creare un database come copia di un altro database in un determinato momento nel passato. In questo articolo viene descritto come eseguire un ripristino temporizzato di un database in un'istanza gestita.

Il ripristino temporizzato può essere usato in scenari di ripristino, ad esempio eventi imprevisti causati da errori, dati caricati in modo errato, eliminazione di dati cruciali e altri problemi, nonché semplicemente a scopo di test o controllo. A seconda delle impostazioni del database, i file di backup vengono conservati per un periodo compreso tra 7 e 35 giorni.

Il ripristino temporizzato può essere usato per:

- Ripristinare un database da un database esistente.
- Ripristinare un database da un database eliminato.

Inoltre, con un'istanza gestita, il ripristino temporizzato può essere usato per: 

- Ripristinare un database nella stessa istanza gestita.
- Ripristinare un database in un'altra istanza gestita.


> [!NOTE]
> Il ripristino temporizzato di un'intera istanza gestita non è possibile. Ciò che è possibile e illustrato in questo articolo è il ripristino temporizzato di un database ospitato in un'istanza gestita.


## <a name="limitations"></a>Limitazioni

Quando si esegue il ripristino in un'altra istanza gestita, entrambe le istanze devono trovarsi nella stessa sottoscrizione e nella stessa area. I ripristini tra aree e tra sottoscrizioni non sono attualmente supportati.

> [!WARNING]
> Prestare attenzione alle dimensioni di archiviazione dell'istanza gestita, a seconda delle dimensioni del ripristino dei dati, è possibile che l'archiviazione dell'istanza venga esaurita. Se non è disponibile spazio sufficiente per i dati ripristinati, utilizzare un approccio alternativo.

La tabella seguente illustra gli scenari di ripristino temporizzato per l'istanza gestita:

|           |Ripristina database esistente| Ripristina database esistente|Ripristinare il database eliminato| Ripristinare il database eliminato|
|:----------|:----------|:----------|:----------|:----------|
|Destination| Stesso MI|Un altro MI |Stesso MI|Un altro MI |
|Portale di Azure| Yes|No |No|No|
|Interfaccia della riga di comando di Azure|Sì |Sì |No|No|
|PowerShell| Sì|Sì |Sì|Sì|


## <a name="restore-existing-database"></a>Ripristina database esistente

Ripristinare un database esistente nella stessa istanza usando il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure. Ripristinare un database a un'altra istanza usando PowerShell o l'interfaccia della riga di comando di Azure specificando le proprietà istanza gestita di destinazione e gruppo di risorse. Se questi parametri non vengono specificati, per impostazione predefinita il database verrà ripristinato nell'istanza esistente. Il ripristino in un'altra istanza di non è attualmente supportato tramite il portale di Azure. 

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)

1. Accedere al [portale di Azure](https://portal.azure.com). 
1. Passare all'istanza gestita e selezionare il database che si desidera ripristinare. 
1. Selezionare **Ripristina** nella pagina database. 

    ![Ripristina database esistente](media/sql-database-managed-instance-point-in-time-restore/restore-database-to-mi.png)

1. Nella pagina **Ripristina** selezionare il punto per la data e l'ora nella cronologia in cui si desidera ripristinare il database.
1. Selezionare **conferma** per ripristinare il database. Viene avviato il processo di ripristino, che consente di creare un nuovo database e viene popolato con i dati del database originale nel momento desiderato. Per ulteriori informazioni sul processo di ripristino, vedere [tempo di ripristino](sql-database-recovery-using-backups.md#recovery-time). 

1. Trova istanza gestita
1. Selezionare il database che si desidera ripristinare
1. In schermata database fare clic su Ripristina azione
1. Nella schermata ripristino selezionare la data e l'ora del punto nella cronologia in cui si sta ripristinando il database
1. Dopo la conferma, il processo di ripristino verrà avviato e, a seconda delle dimensioni del database, il nuovo database verrà creato e popolato con i dati del database originale al momento desiderato. Per la durata del processo di ripristino, vedere l'articolo relativo al ripristino con backup.


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Se Azure PowerShell non è ancora installato, vedere [Install the Azure PowerShell Module](https://docs.microsoft.com/powershell/azure/install-az-ps).

Per ripristinare il database usando PowerShell, aggiornare i parametri con i valori ed eseguire il comando seguente:

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

Per ripristinare il database in un'altra istanza gestita, impostare il nome del gruppo di risorse di destinazione e il nome dell'istanza gestita di destinazione.  

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


# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Se l'interfaccia della riga di comando di Azure non è ancora installata, vedere [Install the Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

Per ripristinare il database usando l'interfaccia della riga di comando di Azure, aggiornare i parametri con i valori ed eseguire il comando seguente:


```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```


Per ripristinare il database in un'altra istanza gestita, impostare il nome del gruppo di risorse di destinazione e il nome dell'istanza gestita di destinazione.  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

Per una spiegazione dettagliata dei parametri disponibili, vedere interfaccia della riga di comando di [istanza gestita](https://docs.microsoft.com/cli/azure/sql/midb?view=azure-cli-latest#az-sql-midb-restore). 

---

## <a name="restore-a-deleted-database"></a>Ripristino di un database eliminato 
 
Il ripristino di un database eliminato può essere eseguito solo con PowerShell. Il database può essere ripristinato nella stessa istanza o in un'altra istanza. 

Per ripristinare un database eliminato tramite PowerShell, aggiornare i parametri con i valori ed eseguire il comando seguente:

```powershell-interactive
$subscriptionId = "<Subscription ID>"
Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$deletedDatabaseName = "<Source database name>"

$deleted_db = Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName $resourceGroupName `
            -InstanceName $managedInstanceName -DatabaseName $deletedDatabaseName 

$pointInTime = "2018-06-27T08:51:39.3882806Z"
$properties = New-Object System.Object
$properties | Add-Member -type NoteProperty -name CreateMode -Value "PointInTimeRestore"
$properties | Add-Member -type NoteProperty -name RestorePointInTime -Value $pointInTime
$properties | Add-Member -type NoteProperty -name RestorableDroppedDatabaseId -Value $deleted_db.Id
```

Per ripristinare il database eliminato a un'altra istanza, modificare il nome del gruppo di risorse e il nome dell'istanza gestita.

Il parametro location deve corrispondere al percorso del gruppo di risorse e dell'istanza gestita.

```powershell-interactive
$resourceGroupName = "<Second resource group name>"
$managedInstanceName = "<Second managed instance name>"

$location = "West Europe"

$restoredDBName = "WorldWideImportersPITR"
$resource_id = "subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$managedInstanceName/databases/$restoredDBName"

New-AzResource -Location $location -Properties $properties `
        -ResourceId $resource_id -ApiVersion "2017-03-01-preview" -Force
```

## <a name="overwrite-existing-database"></a>Sovrascrivi database esistente 
 
Per sovrascrivere un database esistente, è necessario anche:

1. ELIMINARE il database esistente che si desidera sovrascrivere.
1. Rinominare il database ripristinato temporizzato con il nome del database eliminato. 


### <a name="drop-original-database"></a>Elimina database originale 
 
L'eliminazione del database può essere eseguita con il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure. 

È anche possibile eliminare il database connettendosi direttamente all'istanza gestita, avviando SQL Server Management Studio (SSMS) ed eseguendo il comando Transact-SQL (T-SQL) riportato di seguito.

```sql
DROP DATABASE WorldWideImporters;
```

Per connettersi al database dell'istanza gestita, utilizzare uno dei metodi seguenti: 

- [Macchina virtuale SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Da punto a sito](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Endpoint pubblico](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)

Nella portale di Azure selezionare il database dall'istanza gestita e selezionare **Elimina**.

   ![Ripristina database esistente](media/sql-database-managed-instance-point-in-time-restore/delete-database-from-mi.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Usare il comando di PowerShell seguente per eliminare un database esistente da un'istanza gestita: 

```powershell
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source database>"

Remove-AzSqlInstanceDatabase -Name $databaseName -InstanceName $managedInstanceName -ResourceGroupName $resourceGroupName
```

# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Usare il comando dell'interfaccia della riga di comando di Azure seguente per eliminare un database esistente da un'istanza gestita: 

```azurecli-interactive
az sql midb delete -g mygroupname --mi myinstancename -n mymanageddbname
```

---


### <a name="alter-new-database-name-to-original"></a>MODIFICARE il nome del nuovo database in originale

Connettersi direttamente all'istanza gestita, avviare SQL Server Management Studio, quindi eseguire la query Transact-SQL (T-SQL) seguente per modificare il nome del database ripristinato con quello del database eliminato che si intende sovrascrivere. 


```sql
ALTER WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```


Per connettersi al database dell'istanza gestita, utilizzare uno dei metodi seguenti: 

- [Macchina virtuale SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Da punto a sito](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Endpoint pubblico](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulla [conservazione a lungo termine](sql-database-long-term-retention.md) e sui [backup automatici](sql-database-automated-backups.md). 
