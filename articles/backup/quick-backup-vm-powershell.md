---
title: 'Avvio rapido: Eseguire il backup di una macchina virtuale con PowerShell'
description: Questa Guida di avvio rapido illustra come eseguire il backup delle macchine virtuali di Azure con il modulo Azure PowerShell.
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 04/16/2019
ms.custom: mvc
ms.openlocfilehash: 8021ca553a1434c891bee911e85d351c61938594
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74171956"
---
# <a name="back-up-a-virtual-machine-in-azure-with-powershell"></a>Eseguire il backup di una macchina virtuale in Azure con PowerShell

Il modulo [Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0) viene usato per creare e gestire le risorse di Azure dalla riga di comando o negli script.

[Backup di Azure](backup-overview.md) esegue il backup di computer e app locali, oltre che delle VM di Azure. Questo articolo illustra come eseguire il backup di una VM di Azure con il modulo AZ. In alternativa, è possibile eseguire il backup di una VM con l'[interfaccia della riga di comando di Azure](quick-backup-vm-cli.md) oppure nel [portale di Azure](quick-backup-vm-portal.md).

Questa guida introduttiva abilita il backup in una VM di Azure esistente. Se necessario, è possibile [creare una VM con Azure PowerShell](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json).

Per questo argomento di avvio rapido è necessario il modulo Azure PowerShell versione 1.0.0 o successiva. Eseguire `Get-Module -ListAvailable Az` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere come [installare il modulo Azure PowerShell](/powershell/azure/install-az-ps).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-and-register"></a>Accesso e registrazione

1. Accedere alla sottoscrizione di Azure con il comando `Connect-AzAccount` e seguire le istruzioni visualizzate.

    ```powershell
    Connect-AzAccount
    ```

2. Quando si usa Backup di Azure per la prima volta, è necessario registrare il provider di Servizi di ripristino di Azure nella sottoscrizione con [Register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider), come illustrato di seguito:

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

Un [insieme di credenziali di Servizi di ripristino](backup-azure-recovery-services-vault-overview.md) è un contenitore logico in cui vengono archiviati i dati di backup per le risorse protette, ad esempio le VM di Azure. Quando viene eseguito, il processo di backup crea un punto di ripristino all'interno dell'insieme di credenziali di Servizi di ripristino. È quindi possibile usare uno di questi punti di ripristino per ripristinare i dati a un dato momento.

Quando viene creato l'insieme di credenziali:

- Per il gruppo di risorse e la località, specificare il gruppo di risorse e la località della VM di cui eseguire il backup.
- Se la VM è stata creata usando lo [script di esempio](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json), il gruppo di risorse è **myResourceGroup**, la VM è ***myVM** e le risorse si trovano nell'area **WestEurope**.
- Backup di Azure gestisce automaticamente l'archiviazione per i dati sottoposti a backup. Per impostazione predefinita, l'insieme di credenziali usa l'[archiviazione con ridondanza geografica](../storage/common/storage-redundancy-grs.md). Con l'archiviazione con ridondanza geografica i dati di backup vengono replicati in un'area di Azure secondaria a centinaia di chilometri di distanza dall'area primaria.

Creare ora un insieme di credenziali:

1. usare il cmdlet [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) per creare l'insieme di credenziali:

    ```powershell
    New-AzRecoveryServicesVault `
        -ResourceGroupName "myResourceGroup" `
        -Name "myRecoveryServicesVault" `
    -Location "WestEurope"
    ```

2. Impostare il contesto dell'insieme di credenziali con [Set-AzRecoveryServicesVaultContext](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext), come indicato di seguito:

    ```powershell
    Get-AzRecoveryServicesVault `
        -Name "myRecoveryServicesVault" | Set-AzRecoveryServicesVaultContext
    ```

3. Modificare la configurazione dell'archiviazione con ridondanza dell'archiviazione (LRS/GRS) dell'insieme di credenziali con [Set-AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/Set-AzRecoveryServicesBackupProperty), come indicato di seguito:

    ```powershell
    Get-AzRecoveryServicesVault `
        -Name "myRecoveryServicesVault" | Set-AzRecoveryServicesBackupProperty -BackupStorageRedundancy LocallyRedundant/GeoRedundant
    ```

    > [!NOTE]
    > La ridondanza dell'archiviazione può essere modificata solo se non ci sono elementi di backup protetti nell'insieme di credenziali.

## <a name="enable-backup-for-an-azure-vm"></a>Abilitare il backup per una VM di Azure

Abilitare il backup per una VM di Azure e specificare i relativi criteri.

- I criteri definiscono quando eseguire i backup e per quanto tempo conservare i punti di ripristino creati dal backup.
- I criteri di protezione predefiniti eseguono un processo di backup una volta al giorno per la VM e conservano i punti di ripristino creati per 30 giorni. È possibile usare questi criteri predefiniti per proteggere rapidamente la VM.

Abilitare il backup come segue:

1. Per prima cosa, impostare i criteri predefiniti con [Get-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy):

    ```powershell
    $policy = Get-AzRecoveryServicesBackupProtectionPolicy     -Name "DefaultPolicy"
    ```

2. Abilitare il backup della VM con [Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection). Specificare i criteri, il gruppo di risorse e il nome della VM.

    ```powershell
    Enable-AzRecoveryServicesBackupProtection `
        -ResourceGroupName "myResourceGroup" `
        -Name "myVM" `
        -Policy $policy
    ```

## <a name="start-a-backup-job"></a>Avviare un processo di backup

I backup vengono eseguiti in base alla pianificazione specificata nei relativi criteri. È inoltre possibile eseguire un backup su richiesta:

- Il primo processo di backup iniziale crea un punto di ripristino completo.
- Dopo il backup iniziale, ogni processo crea punti di ripristino incrementali.
- I punti di ripristino incrementali sono veloci ed efficienti in termini di archiviazione, perché trasferiscono solo le modifiche eseguite dopo l'ultimo backup.

Per eseguire un backup su richiesta, usare [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem).

- Specificare un contenitore nell'insieme di credenziali che memorizzi i dati di backup con [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer).
- Ogni VM di cui deve essere eseguito il backup viene considerata come un elemento. Per avviare un processo di backup, ottenere le informazioni relative alla VM con [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem).

Eseguire un processo di backup su richiesta come indicato di seguito:

1. Specificare il contenitore, ottenere le informazioni della VM ed eseguire il backup.

    ```powershell
    $backupcontainer = Get-AzRecoveryServicesBackupContainer `
        -ContainerType "AzureVM" `
        -FriendlyName "myVM"

    $item = Get-AzRecoveryServicesBackupItem `
        -Container $backupcontainer `
        -WorkloadType "AzureVM"

    Backup-AzRecoveryServicesBackupItem -Item $item
    ```

2. Dato che questo primo processo di backup crea un punto di ripristino completo, l'operazione può richiedere fino a 20 minuti. Monitorare il processo come descritto nella procedura seguente.

## <a name="monitor-the-backup-job"></a>Monitorare il processo di backup

1. Eseguire [Get-AzRecoveryservicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) per monitorare lo stato del processo.

    ```powershell
    Get-AzRecoveryservicesBackupJob
    ```

    L'output è simile all'esempio seguente, che mostra che il processo è **InProgress**:

    ```output
    WorkloadName   Operation         Status       StartTime              EndTime                JobID
    ------------   ---------         ------       ---------              -------                -----
    myvm           Backup            InProgress   9/18/2017 9:38:02 PM                          9f9e8f14
    myvm           ConfigureBackup   Completed    9/18/2017 9:33:18 PM   9/18/2017 9:33:51 PM   fe79c739
    ```

2. Quando lo stato del processo diventa **Completed**, la VM è protetta ed è stato archiviato un punto di recupero completo.

## <a name="clean-up-the-deployment"></a>Pulire la distribuzione

Se non è più necessario eseguire il backup della VM, è possibile eseguire la pulizia.

- Se si vuole provare a ripristinare la macchina virtuale, ignorare la pulizia.
- Se è stata usata una VM esistente, si può ignorare il cmdlet [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) finale e mantenere così il gruppo di risorse e la VM.

Disabilitare la protezione, rimuovere i punti di ripristino e l'insieme di credenziali. Quindi, eliminare il gruppo di risorse e le risorse VM associate, come segue:

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato creato un insieme di credenziali di Servizi di ripristino, è stata abilitata la protezione per una VM ed è stato creato il punto di ripristino iniziale.

- [Informazioni su come](tutorial-backup-vm-at-scale.md) eseguire il backup delle VM nel portale di Azure.
- [Informazioni su come](tutorial-restore-disk.md) ripristinare rapidamente una VM
