---
title: Ripristinare i file di Azure con PowerShellRestore Azure Files with PowerShell
description: In this article, learn how to restore Azure Files using the Azure Backup service and PowerShell.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 99aeaa6173bb5336e6e1719a9fc0df0c668374e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086819"
---
# <a name="restore-azure-files-with-powershell"></a>Ripristinare i file di Azure con PowerShellRestore Azure Files with PowerShell

Questo articolo illustra come ripristinare un'intera condivisione file, o file specifici, da un punto di ripristino creato dal servizio Backup di Azure tramite Azure Powershell.This article explains how to restore an entire file share, or specific files, from a restore point created by the [Azure Backup](backup-overview.md) service using Azure Powershell.

È possibile ripristinare un'intera condivisione file o file specifici nella condivisione. È possibile eseguire il ripristino nella posizione originale o in una posizione alternativa.

> [!WARNING]
> Assicurarsi che la versione PS sia aggiornata alla versione minima per 'Az.RecoveryServices 2.6.0' per i backup AFS. Per ulteriori dettagli, fare riferimento [alla sezione](backup-azure-afs-automation.md#important-notice---backup-item-identification-for-afs-backups) che descrive il requisito di questa modifica.

## <a name="fetch-recovery-points"></a>Recuperare i punti di recupero

Utilizzare [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint?view=azps-1.4.0) per elencare tutti i punti di ripristino per l'elemento di cui è stato eseguito il backup.

Nello script seguente:

* La variabile **$rp** è una matrice di punti di ripristino per l'elemento di backup selezionato degli ultimi sette giorni.
* La matrice viene ordinata in ordine inverso di tempo con il punto di recupero più recente in posizione **0** nell'indice.
* Per scegliere il punto di ripristino, usare l'indicizzazione standard della matrice di PowerShell.
* Nell'esempio **$rp [0]** seleziona il punto di recupero più recente.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $afsBkpItem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()

$rp[0] | fl
```

L'output è simile al seguente.

```powershell
FileShareSnapshotUri : https://testStorageAcct.file.core.windows.net/testAzureFS?sharesnapshot=2018-11-20T00:31:04.00000
                       00Z
RecoveryPointType    : FileSystemConsistent
RecoveryPointTime    : 11/20/2018 12:31:05 AM
RecoveryPointId      : 86593702401459
ItemName             : testAzureFS
Id                   : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Micros                      oft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;teststorageRG;testStorageAcct/protectedItems/AzureFileShare;testAzureFS/recoveryPoints/86593702401462
WorkloadType         : AzureFiles
ContainerName        : storage;teststorageRG;testStorageAcct
ContainerType        : AzureStorage
BackupManagementType : AzureStorage
```

Dopo aver selezionato il punto di ripristino pertinente, ripristinare la condivisione file o il file nel percorso originale o in un percorso alternativo.

## <a name="restore-an-azure-file-share-to-an-alternate-location"></a>Ripristinare una condivisione file di Azure in un percorso alternativoRestore an Azure file share to an alternate location

Utilizzare [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) per ripristinare il punto di ripristino selezionato. Specificare questi parametri per identificare il percorso alternativo:

* **TargetStorageAccountName:** account di archiviazione in cui viene ripristinato il contenuto di cui è stato eseguito il backup. L'account di archiviazione di destinazione deve trovarsi nella stessa posizione dell'insieme di credenziali.
* **TargetFileShareName:** condivisioni file all'interno dell'account di archiviazione di destinazione in cui viene ripristinato il contenuto di cui è stato eseguito il backup.
* **TargetFolder**: La cartella nella condivisione file in cui vengono ripristinati i dati. Se il contenuto sottoposto a backup deve essere ripristinato in una cartella radice, fornire i valori della cartella di destinazione come stringa vuota.
* **ResolveConflict**: Istruzioni in caso di conflitto con i dati ripristinati. Accetta **Overwrite** o **skip**.

Eseguire il cmdlet con i parametri come segue:

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
```

Il comando restituisce un processo con un ID di cui tenere traccia, come mostrato nell'esempio seguente.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
```

## <a name="restore-an-azure-file-to-an-alternate-location"></a>Ripristinare un file di Azure in un percorso alternativoRestore an Azure file to an alternate location

Utilizzare [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) per ripristinare il punto di ripristino selezionato. Specificare questi parametri per identificare il percorso alternativo e per identificare in modo univoco il file che si desidera ripristinare.

* **TargetStorageAccountName:** account di archiviazione in cui viene ripristinato il contenuto di cui è stato eseguito il backup. L'account di archiviazione di destinazione deve trovarsi nella stessa posizione dell'insieme di credenziali.
* **TargetFileShareName:** condivisioni file all'interno dell'account di archiviazione di destinazione in cui viene ripristinato il contenuto di cui è stato eseguito il backup.
* **TargetFolder**: La cartella nella condivisione file in cui vengono ripristinati i dati. Se il contenuto sottoposto a backup deve essere ripristinato in una cartella radice, fornire i valori della cartella di destinazione come stringa vuota.
* **SourceFilePath**: il percorso assoluto del file, da ripristinare all'interno della condivisione file, come stringa. Questo percorso è uguale a quello usato nel cmdlet **Get-AzStorageFile** di PowerShell.
* **SourceFileType**: Indica se è selezionata una directory o un file. Accetta **directory** o **file**.
* **ResolveConflict**: Istruzioni in caso di conflitto con i dati ripristinati. Accetta **Overwrite** o **skip**.

I parametri aggiuntivi (SourceFilePath e SourceFileType) sono correlati solo al singolo file che si desidera ripristinare.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

Questo comando restituisce un processo con un ID di cui tenere traccia, come illustrato nella sezione precedente.

## <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>Ripristinare le condivisioni file e i file di Azure nel percorso originaleRestore Azure file shares and files to the original location

Quando si esegue il ripristino in una posizione originale, non è necessario specificare i parametri relativi alla destinazione e alla destinazione. Deve essere fornito solo **ResolveConflict**.

#### <a name="overwrite-an-azure-file-share"></a>Sovrascrivere una condivisione file di Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>Sovrascrivere un file di Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="next-steps"></a>Passaggi successivi

[Informazioni sul](restore-afs.md) ripristino di File di Azure nel portale di Azure.Learn about restoring Azure Files in the Azure portal.
