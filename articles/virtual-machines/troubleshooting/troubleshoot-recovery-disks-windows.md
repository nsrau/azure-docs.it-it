---
title: Usare una macchina virtuale per la risoluzione dei problemi Windows con Azure PowerShell | Documentazione Microsoft
description: Informazioni su come risolvere i problemi delle VM Windows in Azure connettendo il disco del sistema operativo a una VM di ripristino usando Azure PowerShell
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: gwallace
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/09/2018
ms.author: genli
ms.openlocfilehash: 265a4f58fc383519246d8d69d6cf7407b92e4728
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70089777"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-azure-powershell"></a>Risolvere i problemi relativi a una macchina virtuale Windows collegando il disco del sistema operativo a una macchina virtuale di ripristino usando Azure PowerShell
Se nella macchina virtuale (VM) Windows in Azure viene rilevato un errore di avvio o del disco, potrebbe essere necessario eseguire alcuni passaggi per la risoluzione dei problemi sul disco stesso. Un esempio comune è un aggiornamento di un'applicazione non riuscito che impedisce il corretto avvio della VM. Questo articolo illustra come usare Azure PowerShell per connettere il disco a un'altra VM Windows per risolvere eventuali errori e quindi riparare la VM originale. 

> [!Important]
> Gli script in questo articolo si applicano solo alle VM che usano [Managed Disks](../windows/managed-disks-overview.md). 

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="recovery-process-overview"></a>Panoramica del processo di ripristino
È ora possibile usare Azure PowerShell per modificare il disco del sistema operativo per una VM. Non è più necessario eliminare e ricreare la VM.

I passaggi per la risoluzione dei problemi sono i seguenti:

1. Arrestare la VM interessata.
2. Creare uno snapshot dal disco del sistema operativo della VM.
3. Creare un disco dallo snapshot del disco del sistema operativo.
4. Collegare il disco come disco dati a una VM di ripristino.
5. Connettersi alla VM di ripristino. Modificare i file o eseguire eventuali strumenti per risolvere i problemi nel disco del sistema operativo copiato.
6. Smontare e scollegare il disco dalla VM di ripristino.
7. Modificare il disco del sistema operativo per la VM interessata.

È possibile usare script di ripristino di macchina virtuale per automatizzare i passaggi 1, 2, 3, 4, 6 e 7. Per altra documentazione e istruzioni, vedere [VM Recovery Scripts for Resource Manager VM](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager) (Script di ripristino di macchina virtuale per una macchina virtuale di Resource Manager).

Verificare di aver prima installato e registrato [la versione più recente di Azure PowerShell](/powershell/azure/overview) nella sottoscrizione:

```powershell
Connect-AzAccount
```

Negli esempi seguenti sostituire i nomi dei parametri con i valori desiderati. 

## <a name="determine-boot-issues"></a>Individuare i problemi di avvio
È possibile visualizzare uno screenshot della VM in Azure per risolvere i problemi di avvio. Questo screenshot consente di identificare il motivo per cui non è possibile avviare una VM. L'esempio seguente ottiene lo screenshot dalla VM Windows denominata `myVM` nel gruppo di risorse `myResourceGroup`:

```powershell
Get-AzVMBootDiagnosticsData -ResourceGroupName myResourceGroup `
    -Name myVM -Windows -LocalPath C:\Users\ops\
```

Esaminare lo screenshot per determinare perché la macchina virtuale non riesce ad avviarsi correttamente. Esaminare i messaggi di errore o i codici di errore specifici visualizzati.

## <a name="stop-the-vm"></a>Arrestare la VM

L'esempio seguente arresta la VM `myVM` dal gruppo di risorse `myResourceGroup`:

```powershell
Stop-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

Attendere fino al completamento dell'eliminazione prima di proseguire con il passaggio successivo.


## <a name="create-a-snapshot-from-the-os-disk-of-the-vm"></a>Creare uno snapshot dal disco del sistema operativo della VM

L'esempio seguente crea uno snapshot denominato `mySnapshot` dal disco del sistema operativo della VM denominata "myVM". 

```powershell
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$vmName = 'myVM'
$snapshotName = 'mySnapshot'  

#Get the VM
$vm = get-azvm `
-ResourceGroupName $resourceGroupName `
-Name $vmName

#Create the snapshot configuration for the OS disk
$snapshot =  New-AzSnapshotConfig `
-SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
-Location $location `
-CreateOption copy

#Take the snapshot
New-AzSnapshot `
   -Snapshot $snapshot `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName 
```

Uno snapshot è una copia completa di sola lettura di un disco rigido virtuale. Non può essere collegato a una VM. Nel passaggio successivo si creerà un disco da questo snapshot.

## <a name="create-a-disk-from-the-snapshot"></a>Creare un disco dallo snapshot

Questo script crea un disco gestito denominato `newOSDisk` dallo snapshot `mysnapshot`.  

```powershell
#Set the context to the subscription Id where Managed Disk will be created
#You can skip this step if the subscription is already selected

$subscriptionId = 'yourSubscriptionId'

Select-AzSubscription -SubscriptionId $SubscriptionId

#Provide the name of your resource group
$resourceGroupName ='myResourceGroup'

#Provide the name of the snapshot that will be used to create Managed Disks
$snapshotName = 'mySnapshot' 

#Provide the name of the Managed Disk
$diskName = 'newOSDisk'

#Provide the size of the disks in GB. It should be greater than the VHD file size.
$diskSize = '128'

#Provide the storage type for Managed Disk. Premium_LRS or Standard_LRS.
$storageType = 'Standard_LRS'

#Provide the Azure region (e.g. westus) where Managed Disks will be located.
#This location should be same as the snapshot location
#Get all the Azure location using command below:
#Get-AzLocation
$location = 'eastus'

$snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
 
$diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
 
New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
```
A questo punto si ha una copia del disco del sistema operativo originale. È possibile montare questo disco in un'altra VM Windows per la risoluzione di problemi.

## <a name="attach-the-disk-to-another-windows-vm-for-troubleshooting"></a>Collegare il disco a un'altra VM Windows per la risoluzione di problemi

A questo punto si collega la copia del disco del sistema operativo originale a una VM come disco dati. Questo processo consente di correggere eventuali errori di configurazione o di esaminare anche i file di log delle applicazioni e del registro di sistema nel disco. L'esempio seguente collega il disco `newOSDisk` alla VM `RecoveryVM`.

> [!NOTE]
> Per collegare il disco, la copia del disco del sistema operativo originale e la VM di ripristino devono trovarsi nella stessa posizione.

```powershell
$rgName = "myResourceGroup"
$vmName = "RecoveryVM"
$location = "eastus" 
$dataDiskName = "newOSDisk"
$disk = Get-AzDisk -ResourceGroupName $rgName -DiskName $dataDiskName 

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 

$vm = Add-AzVMDataDisk -CreateOption Attach -Lun 0 -VM $vm -ManagedDiskId $disk.Id

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

## <a name="connect-to-the-recovery-vm-and-fix-issues-on-the-attached-disk"></a>Connettersi alla VM di ripristino e risolvere i problemi del disco collegato

1. Stabilire una connessione RDP alla VM di ripristino usando le credenziali appropriate. L'esempio seguente scarica il file di connessione RDP per la VM denominata `RecoveryVM` nel gruppo di risorse denominato `myResourceGroup` e lo scarica in `C:\Users\ops\Documents`.

    ```powershell
    Get-AzRemoteDesktopFile -ResourceGroupName "myResourceGroup" -Name "RecoveryVM" `
        -LocalPath "C:\Users\ops\Documents\myVMRecovery.rdp"
    ```

2. Il disco dati dovrebbe essere automaticamente rilevato e collegato. Visualizzare l'elenco di volumi collegati per determinare la lettera di unità, come segue:

    ```powershell
    Get-Disk
    ```

    L'output di esempio seguente illustra il disco connesso a un disco **2**. Per visualizzare la lettera di unità, è anche possibile usare `Get-Volume`:

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        newOSDisk                                  Healthy             Online       127 GB MBR
    ```

Dopo aver montato la copia del disco del sistema operativo originale, è possibile eseguire tutti i passaggi necessari per la manutenzione e la risoluzione dei problemi. Dopo avere risolto i problemi, continuare con la procedura seguente.

## <a name="unmount-and-detach-original-os-disk"></a>Smontare e scollegare il disco del sistema operativo originale
Dopo aver risolto gli errori, smontare e scollegare il disco esistente dalla VM di ripristino. Non è possibile usare il disco con altre VM finché non viene rilasciato il lease che collega il disco alla VM di ripristino.

1. Dalla sessione RDP smontare il disco dati nella VM di ripristino. È necessario il numero del disco ottenuto dal cmdlet `Get-Disk` precedente. Usare quindi `Set-Disk` per impostare il disco come offline:

    ```powershell
    Set-Disk -Number 2 -IsOffline $True
    ```

    Verificare che il disco ora sia impostato come offline usando di nuovo `Get-Disk`. L'output di esempio seguente mostra che il disco ora è impostato come offline:

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Offline      127 GB MBR
    ```

2. Uscire dalla sessione RDP. Dalla sessione di Azure PowerShell, rimuovere il disco `newOSDisk` dalla VM "RecoveryVM".

    ```powershell
    $myVM = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "RecoveryVM"
    Remove-AzVMDataDisk -VM $myVM -Name "newOSDisk"
    Update-AzVM -ResourceGroup "myResourceGroup" -VM $myVM
    ```

## <a name="change-the-os-disk-for-the-affected-vm"></a>Modificare il disco del sistema operativo per la VM interessata

Per scambiare i dischi del sistema operativo è possibile usare Azure PowerShell. Non è necessario eliminare e ricreare la macchina virtuale.

Questo esempio arresta la VM `myVM` e assegna il disco `newOSDisk` come nuovo disco del sistema operativo. 

```powershell
# Get the VM 
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name  -sto

# Update the VM with the new OS disk. Possible values of StorageAccountType include: 'Standard_LRS' and 'Premium_LRS'
Update-AzVM -ResourceGroupName myResourceGroup -VM $vm -StorageAccountType <Type of the storage account >

# Start the VM
Start-AzVM -Name $vm.Name -ResourceGroupName myResourceGroup
```

## <a name="verify-and-enable-boot-diagnostics"></a>Verificare e abilitare la diagnostica di avvio

L'esempio seguente abilita l'estensione di diagnostica nella macchina virtuale denominata `myVMDeployed` nel gruppo di risorse `myResourceGroup`:

```powershell
$myVM = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVMDeployed"
Set-AzVMBootDiagnostics -ResourceGroupName myResourceGroup -VM $myVM -enable
Update-AzVM -ResourceGroup "myResourceGroup" -VM $myVM
```

## <a name="next-steps"></a>Passaggi successivi
Se si sono verificati problemi durante la connessione alla VM, vedere [Risolvere i problemi di connessioni RDP a una macchina virtuale di Azure](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Per problemi relativi all'accesso alle applicazioni in esecuzione nella VM, vedere [Risolvere i problemi di connettività a un'applicazione in una macchina virtuale Windows](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Per altre informazioni sull'uso di Resource Manager, vedere [Panoramica di Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).
