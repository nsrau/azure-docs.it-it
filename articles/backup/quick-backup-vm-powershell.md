---
title: Guida introduttiva di Azure - Eseguire il backup di una VM con PowerShell | Microsoft Docs
description: Informazioni su come eseguire il backup delle macchine virtuali con Azure PowerShell
services: virtual-machines-windows, azure-backup
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: 
ms.service: virtual-machines-windows, azure-backup
ms.devlang: azurecli
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/18/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 98a86652b13f62ef7acade9eb69e81852b2fc091
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2017
---
# <a name="back-up-a-virtual-machine-in-azure-with-powershell"></a>Eseguire il backup di una macchina virtuale in Azure con PowerShell
Il modulo Azure PowerShell viene usato per creare e gestire le risorse di Azure dalla riga di comando o in script. È possibile proteggere i dati eseguendo backup a intervalli regolari. Backup di Azure crea punti di ripristino che possono essere archiviati in insiemi di credenziali di ripristino con ridondanza geografica. Questo articolo illustra in modo dettagliato come eseguire il backup di una macchina virtuale (VM) con il modulo Azure PowerShell. È anche possibile eseguire questa procedura con [l'interfaccia della riga di comando di Azure](quick-backup-vm-cli.md) o il [portale di Azure](quick-backup-vm-portal.md).

Questa guida introduttiva abilita il backup per una VM di Azure esistente. Se necessario, è possibile [creare una VM con Azure PowerShell](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json).

Questo avvio rapido richiede il modulo Azure PowerShell versione 4.4 o successiva. Eseguire ` Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere come [installare il modulo Azure PowerShell](/powershell/azure/install-azurerm-ps).


## <a name="log-in-to-azure"></a>Accedere ad Azure
Accedere alla sottoscrizione di Azure con il comando `Login-AzureRmAccount` e seguire le istruzioni visualizzate.

```powershell
Login-AzureRmAccount
```

Quando si usa Backup di Azure per la prima volta, è necessario registrare il provider di Servizi di ripristino di Azure nella sottoscrizione con [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider).

```powershell
Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
```


## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino
Un insieme di credenziali dei servizi di ripristino è un contenitore logico in cui vengono archiviati i dati di backup per ogni risorsa protetta, ad esempio per le VM di Azure. Quando viene eseguito, il processo di backup per una risorsa protetta crea un punto di ripristino all'interno dell'insieme di credenziali dei servizi di ripristino. È quindi possibile usare uno di questi punti di ripristino per ripristinare i dati a un dato momento.

Creare un insieme di credenziali dei servizi di ripristino con [New-AzureRmRecoveryServicesVault](/powershell/module/azurerm.recoveryservices/new-azurermrecoveryservicesvault). Specificare lo stesso gruppo di risorse e la stessa località della VM da proteggere. Se si è creata la VM usando lo [script di esempio](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json), il gruppo di risorse è denominato *myResourceGroup*, la VM è denominata *myVM* e le risorse si trovano nella località *WestEurope*.

```powershell
New-AzureRmRecoveryServicesVault `
    -ResourceGroupName "myResourceGroup" `
    -Name "myRecoveryServicesVault" `
    -Location "WestEurope"
```

Per impostazione predefinita, l'insieme di credenziali è impostato per l'archiviazione con ridondanza geografica. Questo livello di ridondanza dell'archiviazione garantisce la replica dei dati di backup in un'area di Azure secondaria distante centinaia di chilometri dall'area primaria, per una maggiore protezione dei dati.

Per usare questo insieme di credenziali nei passaggi rimanenti, impostarne il contesto con [Set-AzureRmRecoveryServicesVaultContext](/powershell/module/AzureRM.RecoveryServices/Set-AzureRmRecoveryServicesVaultContext):

```powershell
Get-AzureRmRecoveryServicesVault `
    -Name "myRecoveryServicesVault" | Set-AzureRmRecoveryServicesVaultContext
```


## <a name="enable-backup-for-an-azure-vm"></a>Abilitare il backup per una VM di Azure
Per definire quando verrà eseguito un processo di backup e per quanto tempo verranno archiviati i punti di ripristino, si creano e si usano criteri. I criteri di protezione predefiniti eseguono un processo di backup ogni giorno e conservano i punti di ripristino per 30 giorni. È possibile usare questi valori dei criteri predefiniti per proteggere rapidamente la VM. Per prima cosa, impostare i criteri predefiniti con [Get-AzureRmRecoveryServicesBackupProtectionPolicy](/powershell/module/AzureRM.RecoveryServices.Backup/Get-AzureRmRecoveryServicesBackupProtectionPolicy):

```powershell
$policy = Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "DefaultPolicy"
```

Per abilitare la protezione dei backup per una VM, usare [Enable-AzureRmRecoveryServicesBackupProtection](/powershell/module/AzureRM.RecoveryServices.Backup/Enable-AzureRmRecoveryServicesBackupProtection). Specificare i criteri da usare e quindi il gruppo di risorse e la VM da proteggere:

```powershell
Enable-AzureRmRecoveryServicesBackupProtection `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Policy $policy
```


## <a name="start-a-backup-job"></a>Avviare un processo di backup
Per avviare subito un backup anziché attendere che il processo venga eseguito dai criteri predefiniti all'ora pianificata, usare [Backup-AzureRmRecoveryServicesBackupItem](/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem). Il primo processo di backup crea un punto di ripristino completo. Tutti i processi di backup successivi a questo backup iniziale creano punti di ripristino incrementali. I punti di ripristino incrementali sono veloci ed efficienti in termini di archiviazione, perché trasferiscono solo le modifiche eseguite dopo l'ultimo backup.

Nel set di comandi seguente si specifica un contenitore nell'insieme di credenziali dei servizi di ripristino che contiene i dati di backup con [Get-AzureRmRecoveryServicesBackupContainer](/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer). Ogni VM di cui deve essere eseguito il backup viene considerata come un elemento. Per avviare un processo di backup, ottenere le informazioni relative all'elemento VM con [Get-AzureRmRecoveryServicesBackupItem](/powershell/module/AzureRM.RecoveryServices.Backup/Get-AzureRmRecoveryServicesBackupItem).

```powershell
$backupcontainer = Get-AzureRmRecoveryServicesBackupContainer `
    -ContainerType "AzureVM" `
    -FriendlyName "myVM"

$item = Get-AzureRmRecoveryServicesBackupItem `
    -Container $backupcontainer `
    -WorkloadType "AzureVM"

Backup-AzureRmRecoveryServicesBackupItem -Item $item
```

Dato che questo primo processo di backup crea un punto di ripristino completo, il processo può richiedere fino a 20 minuti.


## <a name="monitor-the-backup-job"></a>Monitorare il processo di backup
Per monitorare lo stato dei processi di backup, usare [Get-AzureRmRecoveryservicesBackupJob](/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjob):

```powershell
Get-AzureRmRecoveryservicesBackupJob
```

L'output è simile all'esempio seguente, che mostra che il processo di backup è **InProgress**:

```
WorkloadName   Operation         Status       StartTime              EndTime                JobID
------------   ---------         ------       ---------              -------                -----
myvm           Backup            InProgress   9/18/2017 9:38:02 PM                          9f9e8f14
myvm           ConfigureBackup   Completed    9/18/2017 9:33:18 PM   9/18/2017 9:33:51 PM   fe79c739
```

Quando nella colonna *Status* del processo di backup è riportato *Completed*, la VM è protetta con Servizi di ripristino e ha un punto di ripristino completo archiviato.


## <a name="clean-up-deployment"></a>Pulire la distribuzione
Quando non è più necessaria, è possibile disabilitare la protezione per la VM, rimuovere i punti di ripristino e l'insieme di credenziali dei servizi di ripristino e quindi eliminare il gruppo di risorse e le risorse della VM associate. Se è stata usata una VM esistente, si può ignorare il cmdlet [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) finale e mantenere così il gruppo di risorse e la VM.

Se si intende proseguire con l'esercitazione relativa al backup che illustra come ripristinare i dati per la VM, ignorare i passaggi in questa sezione e andare a [Passaggi successivi](#next-steps). 

```powershell
Disable-AzureRmRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzureRmRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzureRmRecoveryServicesVault -Vault $vault
Remove-AzureRmResourceGroup -Name "myResourceGroup"
```


## <a name="next-steps"></a>Passaggi successivi
In questa guida introduttiva si è creato un insieme di credenziali dei servizi di ripristino, si è abilitata la protezione per una VM e si è creato il punto di ripristino iniziale. Per altre informazioni su Backup e Servizi di ripristino di Azure, proseguire con le esercitazioni.

> [!div class="nextstepaction"]
> [Eseguire il backup di più macchine virtuali di Azure](./tutorial-backup-vm-at-scale.md)