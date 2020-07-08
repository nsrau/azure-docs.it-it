---
title: Gestire i backup di condivisioni file di Azure con PowerShell
description: Informazioni su come usare PowerShell per gestire e monitorare le condivisioni file di Azure di cui è stato eseguito il backup con il servizio backup di Azure.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 6ee5fb92e4a66a9d6db66514f966c3650d3a4f13
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83201952"
---
# <a name="manage-azure-file-share-backups-with-powershell"></a>Gestire i backup di condivisioni file di Azure con PowerShell

Questo articolo descrive come usare Azure PowerShell per gestire e monitorare le condivisioni file di Azure di cui è stato eseguito il backup dal servizio backup di Azure.

> [!WARNING]
> Verificare che la versione PS sia aggiornata alla versione minima per "AZ. RecoveryServices 2.6.0" per i backup AFS. Per ulteriori informazioni, fare riferimento alla [sezione](backup-azure-afs-automation.md#important-notice-backup-item-identification) che descrive il requisito per questa modifica.

## <a name="modify-the-protection-policy"></a>Modificare i criteri di protezione

Per modificare i criteri usati per il backup della condivisione file di Azure, usare [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). Specificare l'elemento di backup pertinente e i nuovi criteri di backup.

L'esempio seguente modifica il criterio di protezione **testAzureFS** da **dailyafs** a **monthlyafs**.

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="track-backup-and-restore-jobs"></a>Tenere traccia dei processi di backup e ripristino

Le operazioni di backup e ripristino su richiesta restituiscono un processo insieme a un ID, come illustrato quando si [esegue un backup su richiesta](backup-azure-afs-automation.md#trigger-an-on-demand-backup). Usare il cmdlet [Get-AzRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob?view=azps-1.4.0) per tenere traccia dello stato e dei dettagli del processo.

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
* Arrestare tutti i processi di backup futuri, *lasciando* i punti di ripristino

È possibile che vi sia un costo associato a lasciare i punti di ripristino nella risorsa di archiviazione, poiché verranno conservati gli snapshot sottostanti creati da backup di Azure. Tuttavia, il vantaggio di lasciare i punti di ripristino è la possibilità di ripristinare la condivisione file in un secondo momento, se necessario. Per informazioni sul costo di lasciare i punti di ripristino, vedere i [dettagli relativi ai prezzi](https://azure.microsoft.com/pricing/details/storage/files/). Se si sceglie di eliminare tutti i punti di ripristino, non è possibile ripristinare la condivisione file.

## <a name="stop-protection-and-retain-recovery-points"></a>Arrestare la protezione e mantenere i punti di ripristino

Per arrestare la protezione durante la conservazione dei dati, usare il cmdlet [Disable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection?view=azps-3.3.0) .

Nell'esempio seguente viene arrestata la protezione per la condivisione file *afsfileshare* , ma vengono conservati tutti i punti di ripristino:

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

L'attributo ID processo nell'output corrisponde all'ID processo del processo creato dal servizio di backup per l'operazione di arresto della protezione dati. Per tenere traccia dello stato del processo, usare il cmdlet [Get-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob?view=azps-3.3.0) .

## <a name="stop-protection-without-retaining-recovery-points"></a>Arrestare la protezione senza mantenere i punti di ripristino

Per arrestare la protezione senza mantenere i punti di ripristino, usare il cmdlet [Disable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection?view=azps-3.3.0) e aggiungere il parametro **-RemoveRecoveryPoints** .

Nell'esempio seguente viene arrestata la protezione per la condivisione file *afsfileshare* senza mantenere i punti di ripristino:

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

[Informazioni sulla](manage-afs-backup.md) gestione dei backup di condivisioni file di Azure nell'portale di Azure.
