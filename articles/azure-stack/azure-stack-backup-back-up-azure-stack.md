---
title: Eseguire il backup di Azure Stack | Microsoft Docs
description: Eseguire un backup su richiesta in Azure Stack con backup posto.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 9565DDFB-2CDB-40CD-8964-697DA2FFF70A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.openlocfilehash: 578bb864f56b788db77d1201533e73d3b9616669
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2018
ms.locfileid: "42139728"
---
# <a name="back-up-azure-stack"></a>Eseguire il backup di Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Eseguire un backup su richiesta in Azure Stack con backup posto. Per istruzioni sulla configurazione dell'ambiente di PowerShell, vedere [installazione di PowerShell per Azure Stack ](azure-stack-powershell-install.md). Per accedere ad Azure Stack, vedere [usando il portale di amministratore in Azure Stack](azure-stack-manage-portals.md).

## <a name="start-azure-stack-backup"></a>Avviare il backup di Azure Stack

### <a name="start-a-new-backup-without-job-progress-tracking"></a>Avviare un nuovo backup senza avanzamento del processo di rilevamento
Utilizzare Start-AzSBackup per avviare un nuovo backup immediatamente con nessun avanzamento del processo di rilevamento.

```powershell
   Start-AzsBackup -Force
```

### <a name="start-azure-stack-backup-with-job-progress-tracking"></a>Avviare il backup di Azure Stack con lo stato del processo di rilevamento
Utilizzare Start-AzSBackup per avviare un nuovo backup con la variabile di - AsJob per tenere traccia dello stato dei processi di backup.

```powershell
    $backupjob = Start-AzsBackup -Force -AsJob 
    "Start time: " + $backupjob.PSBeginTime;While($backupjob.State -eq "Running"){("Job is currently: " `
    + $backupjob.State+" ;Duration: " + (New-TimeSpan -Start ($backupjob.PSBeginTime) `
    -End (Get-Date)).Minutes);Start-Sleep -Seconds 30};$backupjob.Output

    if($backupjob.State -eq "Completed"){Get-AzsBackup | where {$_.BackupId -eq $backupjob.Output.BackupId}}
```

## <a name="confirm-backup-has-completed"></a>Confermare il backup è stata completata

### <a name="confirm-backup-has-completed-using-powershell"></a>Confermare il backup è stata completata con PowerShell
Usare i comandi PowerShell seguenti per assicurarsi che il backup è stata completata:

```powershell
   Get-AzsBackup
```

Il risultato dovrebbe essere simile al seguente:

```powershell
    BackupDataVersion : 1.0.1
    BackupId          : <backup ID>
    RoleStatus        : {NRP, SRP, CRP, KeyVaultInternalControlPlane...}
    Status            : Succeeded
    CreatedDateTime   : 7/6/2018 6:46:24 AM
    TimeTakenToCreate : PT20M32.364138S
    DeploymentID      : <deployment ID>
    StampVersion      : 1.1807.0.41
    OemVersion        : 
    Id                : /subscriptions/<subscription ID>/resourceGroups/System.local/providers/Microsoft.Backup.Admin/backupLocations/local/backups/<backup ID>
    Name              : local/<local name>
    Type              : Microsoft.Backup.Admin/backupLocations/backups
    Location          : local
    Tags              : {}
```

### <a name="confirm-backup-has-completed-in-the-administration-portal"></a>Confermare il backup è stata completata nel portale di amministrazione
Usare il portale di amministrazione di Azure Stack per verificare che il backup è stata completata correttamente seguendo questa procedura:

1. Aprire il [portale di amministrazione di Azure Stack](azure-stack-manage-portals.md).
2. Selezionare **altri servizi** > **backup Infrastructure**. Scegli **Configuration** nel **backup dell'infrastruttura** pannello.
3. Trovare il **Name** e **data di completamento** del backup nella **backup disponibili** elenco.
4. Verificare i **lo stato** viene **Succeeded**.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul flusso di lavoro per il ripristino da un evento di perdita di dati. Visualizzare [risarcimento grave perdita dei dati](azure-stack-backup-recover-data.md).
