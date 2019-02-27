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
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: hectorl
ms.lastreviewed: 09/05/2018
ms.openlocfilehash: 685183f61c1574d8c533efad8a7e0c46b1e2d23c
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56870313"
---
# <a name="back-up-azure-stack"></a>Eseguire il backup di Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Eseguire un backup su richiesta in Azure Stack. Per istruzioni sulla configurazione dell'ambiente di PowerShell, vedere [installazione di PowerShell per Azure Stack](azure-stack-powershell-install.md). Per accedere ad Azure Stack, vedere [usando il portale di amministratore in Azure Stack](azure-stack-manage-portals.md).

## <a name="start-azure-stack-backup"></a>Avviare il backup di Azure Stack

### <a name="start-a-new-backup-without-job-progress-tracking"></a>Avviare un nuovo backup senza avanzamento del processo di rilevamento
Utilizzare Start-AzSBackup per avviare un nuovo backup immediatamente con nessun avanzamento del processo di rilevamento.

```powershell
   Start-AzsBackup -Force
```

### <a name="start-azure-stack-backup-with-job-progress-tracking"></a>Avviare il backup di Azure Stack con lo stato del processo di rilevamento
Utilizzare Start-AzSBackup per avviare un nuovo backup con il **- AsJob** parametro e salvarla come una variabile per monitorarne lo stato del processo di backup.

> [!NOTE]
> Processo di backup verrà visualizzato come correttamente completato nel portale di circa 10-15 minuti prima che il processo verrà terminata.
>
> Pertanto, lo stato effettivo è meglio osservato tramite il codice seguente.

> [!IMPORTANT]
> Il ritardo di millisecondo 1 iniziale è stata introdotta in quanto il codice è troppo veloce eseguire correttamente la registrazione del processo e viene restituita senza alcun **PSBeginTime** e a sua volta senza alcun **stato** del processo.

```powershell
    $BackupJob = Start-AzsBackup -Force -AsJob
    While (!$BackupJob.PSBeginTime) {
        Start-Sleep -Milliseconds 1
    }
    Write-Host "Start time: $($BackupJob.PSBeginTime)"
    While ($BackupJob.State -eq "Running") {
        Write-Host "Job is currently: $($BackupJob.State) - Duration: $((New-TimeSpan -Start ($BackupJob.PSBeginTime) -End (Get-Date)).ToString().Split(".")[0])"
        Start-Sleep -Seconds 30
    }

    If ($BackupJob.State -eq "Completed") {
        Get-AzsBackup | Where-Object {$_.BackupId -eq $BackupJob.Output.BackupId}
        $Duration = $BackupJob.Output.TimeTakenToCreate
        $Pattern = '^P?T?((?<Years>\d+)Y)?((?<Months>\d+)M)?((?<Weeks>\d+)W)?((?<Days>\d+)D)?(T((?<Hours>\d+)H)?((?<Minutes>\d+)M)?((?<Seconds>\d*(\.)?\d*)S)?)$'
        If ($Duration -match $Pattern) {
            If (!$Matches.ContainsKey("Hours")) {
                $Hours = ""
            } 
            Else {
                $Hours = ($Matches.Hours).ToString + 'h '
            }
            $Minutes = ($Matches.Minutes)
            $Seconds = [math]::round(($Matches.Seconds))
            $Runtime = '{0}{1:00}m {2:00}s' -f $Hours, $Minutes, $Seconds
        }
        Write-Host "BackupJob: $($BackupJob.Output.BackupId) - Completed with Status: $($BackupJob.Output.Status) - It took: $($Runtime) to run" -ForegroundColor Green
    }
    ElseIf ($BackupJob.State -ne "Completed") {
        $BackupJob
        $BackupJob.Output
    }
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
2. Selezionare **tutti i servizi**e quindi sotto il **amministrazione** categoria, selezionare > **backup dell'infrastruttura**. Scegli **Configuration** nel **backup dell'infrastruttura** pannello.
3. Trovare il **Name** e **data di completamento** del backup nella **backup disponibili** elenco.
4. Verificare i **lo stato** viene **Succeeded**.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul flusso di lavoro per [il ripristino da un evento di perdita di dati](azure-stack-backup-recover-data.md).
