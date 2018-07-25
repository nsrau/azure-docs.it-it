---
title: Eseguire il backup di Azure Stack | Microsoft Docs
description: Eseguire un backup su richiesta in Azure Stack con backup posto.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 9565DDFB-2CDB-40CD-8964-697DA2FFF70A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/08/2017
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: c9e7ffae1b988d0940d10acdb1b387a25e0466ec
ms.sourcegitcommit: d76d9e9d7749849f098b17712f5e327a76f8b95c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242887"
---
# <a name="back-up-azure-stack"></a>Eseguire il backup di Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Eseguire un backup su richiesta in Azure Stack con backup posto. Per istruzioni sulla configurazione dell'ambiente di PowerShell, vedere [installazione di PowerShell per Azure Stack ](azure-stack-powershell-install.md). Per accedere ad Azure Stack, vedere [configurare l'ambiente di operatore e accedere ad Azure Stack](azure-stack-powershell-configure-admin.md).

## <a name="start-azure-stack-backup"></a>Avviare il backup di Azure Stack

Utilizzare Start-AzSBackup per avviare un nuovo backup con variabile di - AsJob per tenere traccia dello stato. 

```powershell
    $backupjob = Start-AzsBackup -Force -AsJob
    "Start time: " + $backupjob.PSBeginTime;While($backupjob.State -eq "Running"){("Job is currently: " + $backupjob.State+" ;Duration: " + (New-TimeSpan -Start ($backupjob.PSBeginTime) -End (Get-Date)).Minutes);Start-Sleep -Seconds 30};$backupjob.Output
```

## <a name="confirm-backup-completed-via-powershell"></a>Conferma backup completato tramite PowerShell

```powershell
    if($backupjob.State -eq "Completed"){Get-AzsBackup | where {$_.BackupId -eq $backupjob.Output.BackupId}}
```

- Il risultato dovrebbe essere simile al seguente:

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

## <a name="confirm-backup-completed-in-the-administration-portal"></a>Conferma backup completato nel portale di amministrazione

1. Aprire il portale di amministrazione di Azure Stack al [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external).
2. Selezionare **altri servizi** > **backup Infrastructure**. Scegli **Configuration** nel **backup dell'infrastruttura** pannello.
3. Trovare il **Name** e **data di completamento** del backup nella **backup disponibili** elenco.
4. Verificare i **lo stato** viene **Succeeded**.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sul flusso di lavoro per il ripristino da un evento di perdita di dati. Visualizzare [risarcimento grave perdita dei dati](azure-stack-backup-recover-data.md).