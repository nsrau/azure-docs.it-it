---
title: Eseguire il backup di più macchine virtuali di Azure con PowerShell
description: Questa esercitazione descrive in dettaglio l'esecuzione di backup di più macchine virtuali di Azure in un insieme di credenziali di Servizi di ripristino con Azure PowerShell.
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 03/05/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 2a35435f56a4bb09a8a1958fbc175ef7c889c380
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66127671"
---
# <a name="back-up-azure-vms-with-powershell"></a>Eseguire il backup di macchine virtuali di Azure con PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Questa esercitazione descrive come distribuire un insieme di credenziali di Servizi di ripristino di [Backup di Azure](backup-overview.md) per eseguire il backup di più macchine virtuali di Azure con PowerShell.  

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un insieme di credenziali di Servizi di ripristino e impostare il contesto dell'insieme di credenziali.
> * Definire un criterio di backup
> * Applicare un criterio di backup per proteggere più macchine virtuali
> * Attivare un processo di backup su richiesta per le macchine virtuali protette Prima di poter eseguire il backup o proteggere una macchina virtuale, è necessario completare i [prerequisiti](backup-azure-arm-vms-prepare.md) per preparare l'ambiente per la protezione delle macchine virtuali. 

> [!IMPORTANT]
> In questa esercitazione si presuppone che siano già stati creati un gruppo di risorse e una macchina virtuale di Azure.


## <a name="log-in-and-register"></a>Accesso e registrazione


1. Accedere alla sottoscrizione di Azure con il comando `Connect-AzAccount` e seguire le istruzioni visualizzate.

    ```powershell
    Connect-AzAccount
    ```
2. Quando si usa Backup di Azure per la prima volta, è necessario registrare il provider di Servizi di ripristino di Azure nella sottoscrizione con [Register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider). Se la registrazione è stata già eseguita, ignorare questo passaggio.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```


## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

Un [insieme di credenziali di Servizi di ripristino](backup-azure-recovery-services-vault-overview.md) è un contenitore logico in cui vengono archiviati i dati di backup per le risorse protette, ad esempio le VM di Azure. Quando viene eseguito, il processo di backup crea un punto di ripristino all'interno dell'insieme di credenziali di Servizi di ripristino. È quindi possibile usare uno di questi punti di ripristino per ripristinare i dati a un dato momento.


- In questa esercitazione l'insieme di credenziali viene creato nello stesso gruppo di risorse e nella stessa posizione della macchina virtuale di cui si vuole eseguire il backup.
- Backup di Azure gestisce automaticamente l'archiviazione per i dati sottoposti a backup. Per impostazione predefinita, l'insieme di credenziali usa l'[archiviazione con ridondanza geografica](../storage/common/storage-redundancy-grs.md). Con l'archiviazione con ridondanza geografica i dati di backup vengono replicati in un'area di Azure secondaria a centinaia di chilometri di distanza dall'area primaria.

Creare l'insieme di credenziali nel modo seguente:

1. Usare il cmdlet [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) per creare l'insieme di credenziali. Specificare il nome del gruppo di risorse e la posizione della macchina virtuale di cui si vuole eseguire il backup.

    ```powershell
    New-AzRecoveryServicesVault -Name myRSvault -ResourceGroupName "myResourceGroup" -Location "EastUS"
    ```
2. Molti cmdlet di Backup di Azure richiedono l'oggetto dell'insieme di credenziali dei servizi di ripristino come input. Per questo motivo, è utile archiviare l'oggetto dell'insieme di credenziali dei servizi di ripristino di Backup in una variabile.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault –Name myRSVault
    ```
    
3. Impostare il contesto dell'insieme di credenziali con [Set-AzRecoveryServicesVaultContext](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext).

   - Il contesto dell'insieme di credenziali definisce il tipo di dati protetti nell'insieme di credenziali.
   - Una volta impostato il contesto, si applica a tutti i cmdlet successivi.

     ```powershell
     Get-AzRecoveryServicesVault -Name "myRSVault" | Set-AzRecoveryServicesVaultContext
     ```

## <a name="back-up-azure-vms"></a>Eseguire il backup delle VM di Azure

I backup vengono eseguiti in conformità alla pianificazione specificata nei relativi criteri. Quando si crea un insieme di credenziali di Servizi di ripristino, questo è dotato di protezione predefinita e criteri di conservazione.

- I criteri di protezione predefiniti attivano un processo di backup una volta al giorno all'ora specificata.
- I criteri di conservazione predefiniti consentono di mantenere il punto di recupero giornaliero per 30 giorni. 

Per abilitare ed eseguire il backup della macchina virtuale di Azure in questa esercitazione, vengono eseguite le operazioni seguenti:

1. Specificare un contenitore nell'insieme di credenziali che memorizzi i dati di backup con [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Ogni macchina virtuale per il backup è un elemento. Per avviare un processo di backup, ottenere le informazioni relative alla VM con [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Eseguire un backup ad hoc con [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem). 
    - Il primo processo di backup iniziale crea un punto di ripristino completo.
    - Dopo il backup iniziale, ogni processo crea punti di ripristino incrementali.
    - I punti di ripristino incrementali sono veloci ed efficienti in termini di archiviazione, perché trasferiscono solo le modifiche eseguite dopo l'ultimo backup.

Abilitare ed eseguire il backup come segue:

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered -FriendlyName "V2VM"
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType AzureVM
$job = Backup-AzRecoveryServicesBackupItem -Item $item
```

## <a name="troubleshooting"></a>risoluzione dei problemi 

Se si riscontrano problemi durante il backup della macchina virtuale, vedere questo [articolo sulla risoluzione dei problemi](backup-azure-vms-troubleshoot.md).

### <a name="deleting-a-recovery-services-vault"></a>Eliminazione di un insieme di credenziali dei servizi di ripristino

Se è necessario eliminare un insieme di credenziali, eliminare prima di tutto i punti di ripristino nell'insieme di credenziali e quindi annullare la registrazione dell'insieme di credenziali, come segue:


```powershell
$Cont = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered
$PI = Get-AzRecoveryServicesBackupItem -Container $Cont[0] -WorkloadType AzureVm
Disable-AzRecoveryServicesBackupProtection -RemoveRecoveryPoints $PI[0]
Unregister-AzRecoveryServicesBackupContainer -Container $namedContainer
Remove-AzRecoveryServicesVault -Vault $vault1
```

## <a name="next-steps"></a>Passaggi successivi

- [Vedere](backup-azure-vms-automation.md) una procedura più dettagliata per il backup e il ripristino di macchine virtuali di Azure con PowerShell. 
- [Gestire e monitorare macchine virtuali di Azure](backup-azure-manage-vms.md)
- [Ripristinare una macchina virtuale](backup-azure-arm-restore-vms.md)
