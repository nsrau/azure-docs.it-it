---
title: Ripristinare File di Azure con PowerShell
description: Questo articolo illustra come ripristinare File di Azure usando il servizio backup di Azure e PowerShell.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: c1c116033dbf44e6e1f332195a18c7dfdbcd6c71
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76776339"
---
# <a name="restore-azure-files-with-powershell"></a>Ripristinare File di Azure con PowerShell

Questo articolo illustra come ripristinare un'intera condivisione file o file specifici da un punto di ripristino creato dal servizio backup di [Azure](backup-overview.md) tramite Azure PowerShell.

È possibile ripristinare un'intera condivisione file o file specifici nella condivisione. È possibile eseguire il ripristino nel percorso originale o in un percorso alternativo.

## <a name="fetch-recovery-points"></a>Recuperare i punti di recupero

Usare [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint?view=azps-1.4.0) per elencare tutti i punti di ripristino per l'elemento di cui è stato eseguito il backup.

Nello script seguente:

* La variabile **$RP** è una matrice di punti di ripristino per l'elemento di backup selezionato negli ultimi sette giorni.
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

## <a name="restore-an-azure-file-share-to-an-alternate-location"></a>Ripristinare una condivisione file di Azure in un percorso alternativo

Usare [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) per ripristinare il punto di ripristino selezionato. Specificare questi parametri per identificare il percorso alternativo:

* **TargetStorageAccountName**: account di archiviazione in cui viene ripristinato il contenuto di cui è stato eseguito il backup. L'account di archiviazione di destinazione deve trovarsi nella stessa posizione dell'insieme di credenziali.
* **TargetFileShareName**: le condivisioni file nell'account di archiviazione di destinazione in cui viene ripristinato il contenuto di cui è stato eseguito il backup.
* **TargetFolder**: cartella nella condivisione file in cui vengono ripristinati i dati. Se il contenuto sottoposto a backup deve essere ripristinato in una cartella radice, fornire i valori della cartella di destinazione come stringa vuota.
* **ResolveConflict**: istruzione se si verifica un conflitto con i dati ripristinati. Accetta **Overwrite** o **skip**.

Eseguire il cmdlet con i parametri come indicato di seguito:

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
```

Il comando restituisce un processo con un ID di cui tenere traccia, come mostrato nell'esempio seguente.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
```

## <a name="restore-an-azure-file-to-an-alternate-location"></a>Ripristinare un file di Azure in un percorso alternativo

Usare [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) per ripristinare il punto di ripristino selezionato. Specificare questi parametri per identificare il percorso alternativo e per identificare in modo univoco il file che si desidera ripristinare.

* **TargetStorageAccountName**: account di archiviazione in cui viene ripristinato il contenuto di cui è stato eseguito il backup. L'account di archiviazione di destinazione deve trovarsi nella stessa posizione dell'insieme di credenziali.
* **TargetFileShareName**: le condivisioni file nell'account di archiviazione di destinazione in cui viene ripristinato il contenuto di cui è stato eseguito il backup.
* **TargetFolder**: cartella nella condivisione file in cui vengono ripristinati i dati. Se il contenuto sottoposto a backup deve essere ripristinato in una cartella radice, fornire i valori della cartella di destinazione come stringa vuota.
* **Percorsofileorigine**: percorso assoluto del file, da ripristinare all'interno della condivisione file, sotto forma di stringa. Questo percorso è uguale a quello usato nel cmdlet **Get-AzStorageFile** di PowerShell.
* **SourceFileType**: indica se è selezionata una directory o un file. Accetta **Directory** o **File**.
* **ResolveConflict**: istruzione se si verifica un conflitto con i dati ripristinati. Accetta **Overwrite** o **skip**.

I parametri aggiuntivi (percorsofileorigine e SourceFileType) sono correlati solo al singolo file che si desidera ripristinare.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

Questo comando restituisce un processo con un ID da rilevare, come illustrato nella sezione precedente.

## <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>Ripristinare le condivisioni file di Azure e i file nel percorso originale

Quando si esegue il ripristino in un percorso originale, non è necessario specificare i parametri relativi alla destinazione e alla destinazione. Deve essere fornito solo **ResolveConflict**.

#### <a name="overwrite-an-azure-file-share"></a>Sovrascrivere una condivisione file di Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>Sovrascrivere un file di Azure

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="next-steps"></a>Passaggi successivi

[Informazioni sul](restore-afs.md) ripristino di File di Azure nel portale di Azure.
