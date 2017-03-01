---
title: 'PowerShell: Gestire la conservazione dei backup del database a lungo termine (Azure) | Documentazione Microsoft'
description: Riferimento rapido per configurare, gestire e ripristinare dalla conservazione a lungo termine di backup automatici di un database SQL di Azure in un insieme di credenziali di Servizi di ripristino di Azure tramite PowerShell
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 12/22/2016
ms.author: carlrab; sstein
translationtype: Human Translation
ms.sourcegitcommit: f234e50d194c1b8b8deed8282a6ab1250b53d075
ms.openlocfilehash: c0f073578ecf067d115a143edf4e797c4e8b5200
ms.lasthandoff: 02/16/2017


---
# <a name="configure-long-term-retention-of-database-backups-in-an-azure-recovery-services-vault-using-powershell"></a>Configurare la conservazione a lungo termine dei backup del database in un insieme di credenziali di Servizi di ripristino di Azure tramite PowerShell
Questo argomento illustra come configurare, gestire e ripristinare dalla conservazione a lungo termine di backup automatici in un insieme di credenziali di Servizi di ripristino di Azure tramite PowerShell. È anche possibile eseguire questa attività tramite il [portale di Azure](sql-database-manage-long-term-backup-retention-portal.md).

Per altre informazioni, vedere l'articolo sulla [conservazione dei backup a lungo termine](sql-database-long-term-retention.md).

> [!TIP]
> Per un'esercitazione dettagliata, vedere [Introduzione al backup e ripristino per la protezione dei dati e il ripristino con PowerShell](sql-database-get-started-backup-recovery-powershell.md).
>

## <a name="configure-long-term-backup-retention-using-powershell"></a>Configurare la conservazione del backup a lungo termine in PowerShell

La configurazione a lungo termine richiede l'ultima versione di [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) per eseguire questa procedura:

1. Creare un insieme di credenziali dei servizi di ripristino di Azure nella stessa area, sottoscrizione e gruppo di risorse in cui si trova il server del database SQL. ([New-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices/v2.3.0/new-azurermrecoveryservicesvault), [Set-AzureRmRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices/v2.3.0/set-azurermrecoveryservicesbackupproperties))
2. Registrare il server di Azure SQL nell'insieme di credenziali ([Set-AzureRmSqlServerBackupLongTermRetentionVault](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/set-azurermsqlserverbackuplongtermretentionvault))
3. Creare criteri di conservazione ([New-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/new-azurermrecoveryservicesbackupprotectionpolicy))
4. Applicare i criteri di protezione ai database che richiedono la conservazione dei backup a lungo termine ([Set AzureRmSqlDatabaseBackupLongTermRetentionPolicy](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/set-azurermsqldatabasebackuplongtermretentionpolicy))

Per altre informazioni, vedere [Archiviazione di backup del database SQL di Azure per un massimo di 10 anni](sql-database-long-term-retention.md).

Per un'esercitazione dettagliata, vedere [Introduzione al backup e ripristino per la protezione dei dati e il ripristino con PowerShell](sql-database-get-started-backup-recovery-powershell.md).

```
# Configure long-term backup retention

# User variables
################
$resourceGroupName = "{resource-group-name}"
$serverName = "{server-name}"
$databaseToBackup = "{database-name}"
$recoveryServiceVaultName = "{vault-name}"

$serverLocation = (Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $resourceGroupName).Location


# Create an Azure recovery services vault
#########################################
$vault = New-AzureRmRecoveryServicesVault -Name $recoveryServiceVaultName -ResourceGroupName $resourceGroupName -Location $serverLocation 
Set-AzureRmRecoveryServicesBackupProperties -BackupStorageRedundancy LocallyRedundant -Vault $vault


# Register your Azure SQL server to the vault
#############################################
Set-AzureRmSqlServerBackupLongTermRetentionVault -ResourceGroupName $resourceGroupName -ServerName $serverName -ResourceId $vault.Id

# Create a retention policy 
###########################
$retentionPolicy = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType AzureSQLDatabase

# Set the retention values to 1 year (set to anytime between 1 week and 10 years)
#################################################################################
$retentionPolicy.RetentionDurationType = "Years"
$retentionPolicy.RetentionCount = 1

$retentionPolicyName = "myOneYearRetentionPolicy"

# Set the vault context to the vault you are creating the policy for
####################################################################
Set-AzureRmRecoveryServicesVaultContext -Vault $vault

# Create the new policy
#######################
$policy = New-AzureRmRecoveryServicesBackupProtectionPolicy -name $retentionPolicyName -WorkloadType AzureSQLDatabase -retentionPolicy $retentionPolicy


# Apply the retention policy to the database to backup 
######################################################
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseToBackup -State "enabled" -ResourceId $policy.Id
```

## <a name="restore-from-long-term-backup-retention-using-powershell"></a>Eseguire il ripristino dalla conservazione dei backup a lungo termine con PowerShell

Per ripristinare un database da un backup con conservazione a lungo termine è necessaria l'ultima versione di [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) e vengono usati i cmdlet seguenti:

- [Get-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices/v2.3.0/get-azurermrecoveryservicesvault)
- [Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupcontainer)
- [Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupitem)
- [Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackuprecoverypoint)
- [Restore-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/restore-azurermsqldatabase)


```
# Restore a database from long-term backup retention

# User variables
################
$resourceGroupName = "{resource-group-name}"
$serverName = "{server-name}"
$databaseNeedingRestore = "{database-name}"
$recoveryServiceVaultName = "{vault-name}"

$restoredDatabaseName = "{restored-db-name}"
$edition = "{restored-db-edition}"
$performanceLevel = "{restored-db-slo}"


# Set the vault context to the vault we want to restore from
############################################################
$vault = Get-AzureRmRecoveryServicesVault -ResourceGroupName $resourceGroupName -Name $recoveryServiceVaultName
Set-AzureRmRecoveryServicesVaultContext -Vault $vault


# Get the container associated with the selected vault
######################################################
$container = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureSQL -FriendlyName $vault.Name

# Get the long-term retention metadata associated with a specific database
##########################################################################
$item = Get-AzureRmRecoveryServicesBackupItem -Container $container -WorkloadType AzureSQLDatabase -Name $databaseNeedingRestore


# Get all available backups for the previously indicated database
# Optionally, set the -StartDate and -EndDate parameters for specific backups
#############################################################################
$availableBackups = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $item
$availableBackups

if (!$availableBackups)
{
    Write-Host "No backups available"
}
else
{
    # Restore the most recent available backup: $availableBackups[0]
    ################################################################
    $restoredDb = Restore-AzureRmSqlDatabase -FromLongTermRetentionBackup -ResourceId $availableBackups[0].Id -ResourceGroupName $resourceGroupName ` 
     -ServerName $serverName -TargetDatabaseName $restoredDatabaseName -Edition $edition -ServiceObjectiveName $performanceLevel
    $restoredDb
}
```

## <a name="view-long-term-backup-retention-information-using-the-powershell"></a>Visualizzare le informazioni della conservazione dei backup a lungo termine usando PowerShell

La visualizzazione dei backup nella conservazione a lungo termine richiede l'ultima versione di [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) e usa i cmdlet seguenti:

- [Get-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices/v2.3.0/get-azurermrecoveryservicesvault)
- [Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupcontainer)
- [Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupitem)
- [Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackuprecoverypoint)

Per altre informazioni, vedere [Archiviazione di backup del database SQL di Azure per un massimo di 10 anni](sql-database-long-term-retention.md).

```
# View the available backups in long-term backup retention

# User variables
################
$resourceGroupName = "{resource-group-name}"
$serverName = "{server-name}"
$recoveryServiceVaultName = "{vault-name}"

# Set the vault context to the vault we want to query
#####################################################
$vault = Get-AzureRmRecoveryServicesVault -ResourceGroupName $resourceGroupName -Name $recoveryServiceVaultName
Set-AzureRmRecoveryServicesVaultContext -Vault $vault


# Get the container associated with the selected vault
######################################################
$container = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureSQL -FriendlyName $vault.Name

# Get the long-term retention metadata associated with the container
####################################################################
$item = Get-AzureRmRecoveryServicesBackupItem -Container $container -WorkloadType AzureSQLDatabase


# Get all available backups
# Optionally, set the -StartDate and -EndDate parameters
########################################################
$availableBackups = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $item
$availableBackups
```

## <a name="delete-long-term-retention-backups-using-powershell"></a>Eliminare i backup con conservazione a lungo termine usando PowerShell

>[!Warning]
>Accertarsi di voler eliminare i backup con conservazione a lungo termine prima di eseguire questo codice. Per evitare eventuali addebiti indesiderati, questo frammento di codice pulisce tutti i backup e gli insiemi di credenziali creati durante l'apprendimento dell'uso della [conservazione dei backup a lungo termine](sql-database-long-term-retention.md) o i backup e gli insiemi di credenziali che non sono più necessari.

L'eliminazione dei backup con conservazione a lungo termine richiede l'ultima versione di [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) e usa i cmdlet seguenti:

- [Get-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices/v2.3.0/get-azurermrecoveryservicesvault)
- [Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupcontainer)
- [Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupitem)
- [Disable-AzureRmRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/disable-azurermrecoveryservicesbackupprotection)
- [Unregister-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/unregister-azurermrecoveryservicesbackupcontainer)
- [Remove-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices/v2.3.0/remove-azurermrecoveryservicesvault)


Per altre informazioni, vedere [Archiviazione di backup del database SQL di Azure per un massimo di 10 anni](sql-database-long-term-retention.md).

```
# Delete long-term backups
##########################

$resourceGroupName = "{resource-group-name}"
$recoveryServiceVaultName = "{vault-name}"

$vault = Get-AzureRmRecoveryServicesVault -ResourceGroupName $resourceGroupName -Name $recoveryServiceVaultName
Set-AzureRmRecoveryServicesVaultContext -Vault $vault

$containers = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureSQL -FriendlyName $vault.Name

ForEach ($container in $containers)
{
   $items = Get-AzureRmRecoveryServicesBackupItem -container $container -WorkloadType AzureSQLDatabase

   ForEach ($item in $items)
   {
      # Remove the backups from the vault
      ###################################
      Disable-AzureRmRecoveryServicesBackupProtection -item $item -RemoveRecoveryPoints -ea SilentlyContinue
   }
   
   Unregister-AzureRmRecoveryServicesBackupContainer -Container $container
}
# Delete the recovery services vault
####################################
Remove-AzureRmRecoveryServicesVault -Vault $vault
```

## <a name="next-steps"></a>Passaggi successivi

- Per gestire i backup nella conservazione a lungo termine tramite il portale di Azure, vedere [Manage long-term backup retention using the Azure portal](sql-database-manage-long-term-backup-retention-portal.md) (Gestire la conservazione dei backup a lungo termine con il portale di Azure)
- Per informazioni sui backup automatici generati dal servizio, vedere l'articolo relativo ai [backup automatici](sql-database-automated-backups.md)
- Per altre informazioni sulla conservazione dei backup a lungo termine, vedere [conservazione dei backup a lungo termine](sql-database-long-term-retention.md)
- Per informazioni sui backup automatici generati dal servizio, vedere l'articolo relativo ai [backup automatici](sql-database-automated-backups.md)
