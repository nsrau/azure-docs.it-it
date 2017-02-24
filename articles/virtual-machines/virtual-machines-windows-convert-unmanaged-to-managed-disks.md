---
title: Conversione dei dischi di una macchina virtuale da non gestiti a gestiti - Azure | Microsoft Docs
description: Conversione dei dischi di una macchina virtuale da non gestiti a gestiti tramite PowerShell nel modello di distribuzione di Resource Manager
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: be2752f46ebedf35a28277ad853207ae8038e345
ms.openlocfilehash: 16d18b375ba91338aafa0eaef106fdb9107bb872


---
# <a name="convert-a-vm-from-unmanaged-disks-to-managed-disks"></a>Conversione dei dischi di una macchina virtuale da non gestiti a gestiti

Se le macchine virtuali di Azure esistenti di cui si dispone usano i dischi non gestiti negli account di archiviazione e si desidera sfruttare i vantaggi dei [dischi gestiti](../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), è possibile convertire le macchine virtuali. Il processo consente al disco del sistema operativo e a eventuali dischi di dati collegati di passare dall'uso di dischi non gestiti in un account di archiviazione all'uso di dischi gestiti. Per usare Powershell al fine di convertire le macchine virtuali all'uso dei dischi gestiti, le macchine virtuali vengono prima arrestate e deallocate. Dopo la conversione, è necessario riavviare la macchina virtuale che ora userà i dischi gestiti.

Prima di iniziare, rivedere [Piano per la migrazione ai dischi gestiti](virtual-machines-windows-on-prem-to-azure.md#plan-for-the-migration-to-managed-disks).
Testare il processo di migrazione eseguendo la migrazione di una macchina virtuale di test prima di eseguire la migrazione nell'ambiente di produzione perché il processo di migrazione non è reversibile.


> [!IMPORTANT] 
> Durante la conversione sarà necessario deallocare la macchina virtuale. Con la deallocazione, la macchina virtuale avrà un nuovo indirizzo IP quando verrà avviata dopo la conversione. Se si dispone di una dipendenza su un indirizzo IP fisso, è necessario usare un indirizzo IP riservato.


## <a name="managed-disks-and-azure-storage-service-encryption-sse"></a>Dischi gestiti e crittografia del servizio di archiviazione di Azure (SSE)

Non è possibile convertire una macchina virtuale non gestita creata nel modello di distribuzione di Gestione risorse ai dischi gestiti se uno dei dischi non gestiti collegati si trova in un account di archiviazione che è, o che lo è stato in precedenza, crittografato con la [crittografia del servizio archiviazione di Azure (SSE)](../storage/storage-service-encryption.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). La procedura seguente illustra come convertire una macchina virtuale non gestita che è, o è stata, in un account di archiviazione crittografato:

**Dischi dati**:
1.    Scollegare il disco dati dalla macchina virtuale.
2.    Copiare il disco rigido virtuale in un account di archiviazione che non è mai stato abilitato per SSE. Per copiare il disco in un altro account di archiviazione, utilizzare [AzCopy](../storage/storage-use-azcopy.md):`https://sourceaccount.blob.core.windows.net/myvhd.vhd  https://destaccount.blob.core.windows.net/myvhd_no_encrypt.vhd /sourcekey:key1 /destkey:key1`
3.    Collegare il disco copiato alla macchina virtuale e convertire la macchina virtuale.

**Disco del sistema operativo**:
1.    Arrestare la deallocazione della macchina virtuale. Se necessario, salvare la configurazione della macchina virtuale.
2.    Copiare il disco rigido virtuale del sistema operativo in un account di archiviazione che non è mai stato abilitato per SSE. Per copiare il disco in un altro account di archiviazione, utilizzare [AzCopy](../storage/storage-use-azcopy.md):`https://sourceaccount.blob.core.windows.net/myvhd.vhd  https://destaccount.blob.core.windows.net/myvhd_no_encrypt.vhd /sourcekey:key1 /destkey:key1`
3.    Creare una macchina virtuale che usi dischi gestiti e allega il file del disco rigido virtuale come disco del sistema operativo durante la creazione.


## <a name="before-you-begin"></a>Prima di iniziare
Se si usa PowerShell, verificare di avere la versione più recente del modulo di PowerShell AzureRM.Compute. Eseguire il comando seguente per installarlo.

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Per altre informazioni, vedere [Controllo delle versioni di Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/#azure-powershell-versioning).



## <a name="convert-vms-in-an-availability-set-to-managed-disks-in-a-managed-availability-set"></a>Convertire le macchine virtuali che si trovano in un set disponibilità in dischi gestiti all'interno di un set di disponibilità gestito

Se le macchine virtuali che si desidera convertire in dischi gestiti si trovano in un set di disponibilità, è innanzitutto necessario convertire il set di disponibilità in un set di disponibilità gestito.

Lo script seguente aggiorna il set di disponibilità per trasformarlo in un set di disponibilità gestito, quindi esegue la deallocazione, converte i dischi e riavvia ogni macchina virtuale nel set di disponibilità.

```powershell
$rgName = 'myResourceGroup'
$avSetName = 'myAvailabilitySet'

$avSet =  Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName

Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Managed

foreach($vmInfo in $avSet.VirtualMachinesReferences)
    {
   $vm =  Get-AzureRmVM -ResourceGroupName $rgName | Where-Object {$_.Id -eq $vmInfo.id}

   Stop-AzureRmVM -ResourceGroupName $rgName -Name  $vm.Name -Force

   ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vm.Name
   
    }
```

## <a name="convert-existing-azure-vms-to-managed-disks-of-the-same-storage-type"></a>Convertire le macchine virtuali di Azure esistenti in dischi gestiti dello stesso tipo di archiviazione

> [!IMPORTANT]
> Dopo aver eseguito la procedura seguente, un solo BLOB in blocchi rimane nel contenitore predefinito/VHDS. Il nome del file è "VMName.xxxxxxx.status". Non eliminare questo oggetto di stato rimanente. Le prossime procedure mirano a risolvere questo problema.

Questa sezione descrive come convertire le macchine virtuali di Azure esistenti da dischi non gestiti negli account di archiviazione a dischi gestiti quando si userà lo stesso tipo di archiviazione. È possibile usare questo processo per passare dai dischi non gestiti Premium (SSD) ai dischi gestiti o dai dischi non gestiti standard (HDD) ai dischi gestiti standard. 

1. Creare variabili e deallocare la macchina virtuale. In questo esempio il nome del gruppo di risorse è **myResourceGroup** e il nome della macchina virtuale è **myVM**.

    ```powershell
    $rgName = "myResourceGroup"
    $vmName = "myVM"
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
   
    Nel portale di Azure lo *Stato* della VM passa da **Interrotto** a **Arrestato (deallocato)**.
    
2. Convertire tutti i dischi associati alla macchina virtuale incluso il disco del sistema operativo ed eventuali dischi dati.

    ```powershell
    ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
    ```


## <a name="migrate-existing-azure-vms-using-standard-unmanaged-disks-to-premium-managed-disks"></a>Eseguire la migrazione delle macchine virtuali di Azure esistenti ai dischi Premium gestiti tramite i dischi non gestiti standard

In questa sezione viene illustrato come convertire le macchine virtuali di Azure esistenti su dischi non gestiti Standard a dischi gestiti Premium. Per usare i dischi gestiti Premium, la macchina virtuale deve avere [dimensioni tali](virtual-machines-windows-sizes.md) da supportare l'archiviazione Premium.


1.  Innanzitutto, impostare i parametri comuni. Assicurarsi che le [dimensioni della macchina virtuale](virtual-machines-windows-sizes.md) selezionate supportino l'archiviazione Premium.

    ```powershell
    $resourceGroupName = 'YourResourceGroupName'
    $vmName = 'YourVMName'
    $size = 'Standard_DS2_v2'
    ```
1.  Creare una macchina virtuale con dischi non gestiti

    ```powershell
    $vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $resourceGroupName
    ```
    
1.  Arrestare (deallocare) la macchina virtuale.

    ```powershell
    Stop-AzureRmVM -ResourceGroupName $resourceGroupName -Name $vmName -Force
    ```

1.  Aggiornare le dimensioni della macchina virtuale alle dimensioni di archiviazione Premium disponibili nell'area in cui si trova macchina virtuale.

    ```powershell
    $vm.HardwareProfile.VmSize = $size
    Update-AzureRmVM -VM $vm -ResourceGroupName $resourceGroupName
    ```

1.  Convertire la macchina virtuale con dischi non gestiti in dischi gestiti. 

    Se viene visualizzato un errore interno del server, riprovare 2 o 3 volte prima di rivolgersi al team di supporto.

    ```powershell
    ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $resourceGroupName -VMName $vmName
    ```
1. Arrestare (deallocare) la macchina virtuale.

    ```powershell
    Stop-AzureRmVM -ResourceGroupName $resourceGroupName -VMName $vmName -Force
    ```
2.  Aggiornare tutti i dischi all'Archiviazione Premium.

    ```powershell
    $vmDisks = Get-AzureRmDisk -ResourceGroupName $resourceGroupName 
    foreach ($disk in $vmDisks) 
        {
        if($disk.OwnerId -eq $vm.Id)
            {
             $diskUpdateConfig = New-AzureRmDiskUpdateConfig –AccountType PremiumLRS
             Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $resourceGroupName `
             -DiskName $disk.Name
            }
        }
    ```
1. Avviare la VM.

    ```powershell
    Start-AzureRmVM -ResourceGroupName $resourceGroupName -VMName $vmName
    ```
    
È inoltre possibile avere una combinazione di dischi che usi l'archiviazione Premium e quella standard.
    

## <a name="next-steps"></a>Passaggi successivi

Eseguire una copia di sola lettura di una macchina virtuale usando [snapshot](virtual-machines-windows-snapshot-copy-managed-disk.md).




<!--HONumber=Feb17_HO2-->


