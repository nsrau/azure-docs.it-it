---
title: Esempio di script di Azure PowerShell - Eseguire il backup di una macchina virtuale di Azure | Microsoft Docs
description: Esempio di script di Azure PowerShell - Eseguire il backup di una macchina virtuale di Azure
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
tags: 
ms.assetid: 
ms.service: backup
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/07/2017
ms.author: markgal
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 321a034340fb31efc1b539ba1a5ab5a34661cb63
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---

# <a name="back-up-an-encrypted-azure-virtual-machine-with-powershell"></a>Eseguire il backup di una macchina virtuale crittografata di Azure con PowerShell

Questo script crea un insieme di credenziali di Servizi di ripristino con archiviazione con ridondanza geografica (GRS) per una macchina virtuale di Azure. I criteri di protezione predefiniti verranno applicati all'insieme di credenziali. I criteri generano un backup giornaliero per la macchina virtuale e conservano ciascun backup per 30 giorni. I criteri attivano il punto di ripristino iniziale per la macchina virtuale e lo conservano per 365 giorni. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/backup/backup-encrypted-vm/backup-encrypted-vm.ps1 "Back up encrypted virtual machine")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Eseguire questo comando per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare la distribuzione. Ogni elemento della tabella include collegamenti alla documentazione specifica del comando.


| Comando | Note | 
|---|---| 
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. | 
| [New-AzureRmRecoveryServicesVault](/powershell/module/azurerm.recoveryservices/New-AzureRmRecoveryServicesVault) | Consente di creare un insieme di credenziali di Servizi di ripristino per archiviare i backup. | 
| [Set-AzureRmRecoveryServicesBackupProperties](/powershell/module/azurerm.recoveryservices/Set-AzureRmRecoveryServicesBackupProperties) | Consente di impostare le proprietà di archiviazione dei backup nell'insieme di credenziali di Servizi di ripristino. | 
| [New-AzureRmRecoveryServicesBackupProtectionPolicy](/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)| Consente di creare dei criteri di protezione usando i criteri di pianificazione e i criteri di conservazione nell'insieme di credenziali di Servizi di ripristino. | 
| [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) | Imposta le autorizzazioni in Key Vault per concedere l'accesso dell'entità servizio alle chiavi di crittografia. | 
| [Enable-AzureRmRecoveryServicesBackupProtection](/powershell/module/azurerm.recoveryservices.backup/enable-azurermrecoveryservicesbackupprotection) | Consente di eseguire il backup di un elemento con dei criteri specificati di protezione del backup. | 
| [Set-AzureRmRecoveryServicesBackupProtectionPolicy](/powershell/module/azurerm.recoveryservices.backup/set-azurermrecoveryservicesbackupprotectionpolicy)| Consente di modificare i criteri esistenti di protezione del backup. | 
| [Backup-AzureRmRecoveryServicesBackupItem](/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem) | Consente di avviare un backup per un elemento protetto di Backup di Azure che non è associato alla pianificazione del backup. |
| [Wait-AzureRmRecoveryServicesBackupJob](/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob) | Consente di attendere il completamento del processo di Backup di Azure. | 
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Rimuove un gruppo di risorse e tutte le risorse contenute al suo interno. | 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).


