---
title: Gestire i backup della condivisione file di Azure con PowerShellManage Azure file share backups with PowerShell
description: Informazioni su come usare PowerShell per gestire e monitorare le condivisioni file di Azure di cui è stato eseguito il backup dal servizio Backup di Azure.Learn how to use PowerShell to manage and monitor Azure file shares backed up by the Azure Backup service.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: a9dc421db740963fc5cd11e868eb383694376ce1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083173"
---
# <a name="manage-azure-file-share-backups-with-powershell"></a>Gestire i backup della condivisione file di Azure con PowerShellManage Azure file share backups with PowerShell

Questo articolo descrive come usare Azure PowerShell per gestire e monitorare le condivisioni file di Azure di cui viene eseguito il backup dal servizio Backup di Azure.This article describes how to use Azure PowerShell to manage and monitor the Azure file shares that are backed up by the Azure Backup service.

> [!WARNING]
> Assicurarsi che la versione PS sia aggiornata alla versione minima per 'Az.RecoveryServices 2.6.0' per i backup AFS. Per ulteriori dettagli, fare riferimento [alla sezione](backup-azure-afs-automation.md#important-notice---backup-item-identification-for-afs-backups) che descrive il requisito di questa modifica.

## <a name="modify-the-protection-policy"></a>Modificare i criteri di protezione

Per modificare i criteri usati per il backup della condivisione file di Azure, usare [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). Specificare l'elemento di backup pertinente e il nuovo criterio di backup.

L'esempio seguente modifica il criterio di protezione **testAzureFS** da **dailyafs** a **monthlyafs**.

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="track-backup-and-restore-jobs"></a>Tenere traccia dei processi di backup e ripristino

Le operazioni di backup e ripristino su richiesta restituiscono un processo insieme a un ID, come illustrato quando si [esegue un backup su richiesta.](backup-azure-afs-automation.md#trigger-an-on-demand-backup) Utilizzare il cmdlet [Get-AzRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob?view=azps-1.4.0) per tenere traccia dello stato di avanzamento e dei dettagli del processo.

```powershell
$job = Get-AzRecoveryServicesBackupJob -JobId 00000000-6c46-496e-980a-3740ccb2ad75 -VaultId $vaultID

 $job | fl


IsCancellable        : False
IsRetriable          : False
ErrorDetails         : {Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureFileShareJobErrorInfo}
ActivityId           : 00000000-5b71-4d73-9465-8a4a91f13a36
JobId                : 00000000-6c46-496e-980a-3740ccb2ad75
Operation            : Restore
Status               : Failed
WorkloadName         : testAFS
StartTime            : 12/10/2018 9:56:38 AM
EndTime              : 12/10/2018 11:03:03 AM
Duration             : 01:06:24.4660027
BackupManagementType : AzureStorage

$job.ErrorDetails

 ErrorCode ErrorMessage                                          Recommendations
 --------- ------------                                          ---------------
1073871825 Microsoft Azure Backup encountered an internal error. Wait for a few minutes and then try the operation again. If the issue persists, please contact Microsoft support.
```

## <a name="stop-protection-on-a-file-share"></a>Interrompere la protezione in una condivisione file

Per interrompere la protezione di condivisioni file di Azure è possibile procedere in due modi:

* Arrestare tutti i processi di backup futuri ed *eliminare* tutti i punti di ripristino
* Arrestare tutti i processi di backup futuri ma *lasciare* i punti di ripristino

Potrebbe esserci un costo associato all'abbandono dei punti di ripristino nell'archiviazione, poiché gli snapshot sottostanti creati da Backup di Azure verranno mantenuti. Tuttavia, il vantaggio di lasciare i punti di ripristino è che è possibile ripristinare la condivisione file in un secondo momento, se lo si desidera. Per informazioni sul costo dell'uscita dai punti di ripristino, vedere i dettagli sui [prezzi.](https://azure.microsoft.com/pricing/details/storage/files/) Se si sceglie di eliminare tutti i punti di ripristino, non è possibile ripristinare la condivisione file.

## <a name="stop-protection-and-retain-recovery-points"></a>Arrestare la protezione e mantenere i punti di ripristino

Per arrestare la protezione durante la conservazione dei dati, utilizzare il cmdlet [Disable-AzRecoveryServicesBackupProtection.](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection?view=azps-3.3.0)

Nell'esempio seguente viene interrotta la protezione per la condivisione file *afsfileshare* ma vengono mantenuti tutti i punti di ripristino:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "afstesting" -Name "afstest" | select -ExpandProperty ID
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureStorage -WorkloadType AzureFiles -Name "afsfileshare" -VaultId $vaultID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $vaultID
```

```output
WorkloadName     Operation         Status         StartTime                 EndTime                   JobID
------------     ---------         ------         ---------                 -------                   -----
afsfileshare     DisableBackup     Completed      1/26/2020 2:43:59 PM      1/26/2020 2:44:21 PM      98d9f8a1-54f2-4d85-8433-c32eafbd793f
```

L'attributo ID processo nell'output corrisponde all'ID processo del processo creato dal servizio di backup per l'operazione di "stop protection". Per tenere traccia dello stato del processo, utilizzare il cmdlet [Get-AzRecoveryServicesBackupJob.](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob?view=azps-3.3.0)

## <a name="stop-protection-without-retaining-recovery-points"></a>Arrestare la protezione senza mantenere i punti di ripristino

Per arrestare la protezione senza mantenere i punti di ripristino, utilizzare il cmdlet [Disable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection?view=azps-3.3.0) e aggiungere il parametro **-RemoveRecoveryPoints.**

Nell'esempio seguente viene interrotta la protezione per la condivisione file *afsfileshare* senza mantenere i punti di ripristino:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "afstesting" -Name "afstest" | select -ExpandProperty ID
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureStorage -WorkloadType AzureFiles -Name "afsfileshare" -VaultId $vaultID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $vaultID -RemoveRecoveryPoints
```

```output
WorkloadName     Operation            Status         StartTime                 EndTime                   JobID
------------     ---------            ------         ---------                 -------                   -----
afsfileshare     DeleteBackupData     Completed      1/26/2020 2:50:57 PM      1/26/2020 2:51:39 PM      b1a61c0b-548a-4687-9d15-9db1cc5bcc85
```

## <a name="next-steps"></a>Passaggi successivi

[Informazioni sulla](manage-afs-backup.md) gestione dei backup di condivisione file di Azure nel portale di Azure.Learn about managing Azure file share backups in the Azure portal.
