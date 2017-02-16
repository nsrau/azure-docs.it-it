---
title: Eliminare i backup con conservazione a lungo termine e l&quot;insieme di credenziali di Servizi di ripristino di Azure | Documentazione Microsoft
description: Riferimento rapido su come eliminare i backup con conservazione a lungo termine e l&quot;insieme di credenziali di Servizi di ripristino di Azure
services: sql-database
documentationcenter: 
author: stevestein
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
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 10b40214ad4c7d7bb7999a5abce1c22100b617d8
ms.openlocfilehash: 5e5a868ece1fc661b0bf1a83de5a9a30e9852fb1


---
# <a name="delete-long-term-retention-backups"></a>Eliminare i backup con conservazione a lungo termine

Questo argomento procedurale illustra come eliminare i backup con conservazione a lungo termine e l'insieme di credenziali di Servizi di ripristino di Azure.

>[!Warning]
>Accertarsi di voler eliminare i backup con conservazione a lungo termine prima di eseguire questo codice. Per evitare eventuali addebiti indesiderati, questo frammento di codice pulisce tutti i backup e gli insiemi di credenziali creati durante l'apprendimento dell'uso della [conservazione dei backup a lungo termine](sql-database-long-term-retention.md) o i backup e gli insiemi di credenziali che non sono più necessari.

## <a name="delete-long-term-retention-backups-using-powershell"></a>Eliminare i backup con conservazione a lungo termine usando PowerShell

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

- Per informazioni sui backup automatici generati dal servizio, vedere l'articolo relativo ai [backup automatici](sql-database-automated-backups.md)
- Per altre informazioni sulla conservazione dei backup a lungo termine, vedere [conservazione dei backup a lungo termine](sql-database-long-term-retention.md)
- Per altre informazioni sul ripristino da backup, vedere [ripristino dal backup](sql-database-recovery-using-backups.md)


<!--HONumber=Jan17_HO2-->


