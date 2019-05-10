---
title: Esempio di script di Azure PowerShell - Eseguire il backup di una macchina virtuale di Azure | Microsoft Docs
description: Esempio di script di Azure PowerShell - Eseguire il backup di una macchina virtuale di Azure
services: backup
documentationcenter: ''
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: sample
ms.date: 03/05/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 62d0c7a66e37d0796655bd20f780fa7e0847474c
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65228690"
---
# <a name="back-up-an-encrypted-azure-virtual-machine-with-powershell"></a>Eseguire il backup di una macchina virtuale crittografata di Azure con PowerShell

Questo script crea un insieme di credenziali di Servizi di ripristino con archiviazione con ridondanza geografica (GRS) per una macchina virtuale di Azure crittografata. I criteri di protezione predefiniti verranno applicati all'insieme di credenziali. I criteri generano un backup giornaliero per la macchina virtuale e conservano ciascun backup per 30 giorni. I criteri attivano il punto di ripristino iniziale per la macchina virtuale e lo conservano per 365 giorni.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/backup/backup-encrypted-vm/backup-encrypted-vm.ps1 "Back up encrypted virtual machine")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Eseguire questo comando per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare la distribuzione. Ogni elemento della tabella include collegamenti alla documentazione specifica del comando.


| Comando | Note | 
|---|---| 
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. | 
| [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) | Consente di creare un insieme di credenziali di Servizi di ripristino per archiviare i backup. | 
| [Set-AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) | Consente di impostare le proprietà di archiviazione dei backup nell'insieme di credenziali di Servizi di ripristino. | 
| [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy)| Consente di creare dei criteri di protezione usando i criteri di pianificazione e i criteri di conservazione nell'insieme di credenziali di Servizi di ripristino. | 
| [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) | Imposta le autorizzazioni in Key Vault per concedere l'accesso dell'entità servizio alle chiavi di crittografia. | 
| [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) | Consente di eseguire il backup di un elemento con dei criteri specificati di protezione del backup. | 
| [Set-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy)| Consente di modificare i criteri esistenti di protezione del backup. | 
| [Backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) | Consente di avviare un backup per un elemento protetto di Backup di Azure che non è associato alla pianificazione del backup. |
| [Wait-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) | Consente di attendere il completamento del processo di Backup di Azure. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Rimuove un gruppo di risorse e tutte le risorse contenute al suo interno. | 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).

