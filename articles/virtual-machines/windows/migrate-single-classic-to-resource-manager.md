---
title: Eseguire la migrazione di una macchina virtuale classica a una con disco gestito ARM | Microsoft Docs
description: Eseguire la migrazione di una singola macchina virtuale di Azure dal modello di distribuzione classica a Managed Disks nel modello di distribuzione di Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/15/2017
ms.author: cynthn
ms.openlocfilehash: a662a61d737dbb620d07fa6d114649e70c082796
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2019
ms.locfileid: "54329770"
---
# <a name="migrate-a-classic-vm-to-use-a-managed-disk"></a>Eseguire la migrazione di una macchina virtuale classica per usare un disco gestito 


Questa sezione illustra come eseguire la migrazione di una macchina virtuale di Azure esistente dal modello di distribuzione classica a [Managed Disks](managed-disks-overview.md) nel modello di distribuzione di Resource Manager.


## <a name="plan-for-the-migration-to-managed-disks"></a>Pianificare la migrazione a Managed Disks

Questa sezione consente di prendere le decisioni migliori sui tipi di macchina virtuale e disco.


### <a name="location"></a>Località

Selezionare una località in cui Managed Disks è disponibile. Se si esegue la migrazione a Managed Disks tramite Archiviazione Premium, assicurarsi che anche Archiviazione Premium sia disponibile in tale area. Per informazioni aggiornate sulle località disponibili, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/regions/#services).

### <a name="vm-sizes"></a>Dimensioni delle macchine virtuali

Se si sta eseguendo la migrazione a Managed Disks usando Archiviazione Premium, sarà necessario aggiornare le dimensioni della macchina virtuale alle dimensioni di Archiviazione Premium disponibili nell'area in cui si trova la VM. Esaminare le dimensioni delle macchine virtuali in grado di supportare Archiviazione Premium. Le specifiche delle dimensioni delle VM di Azure sono elencate in [Dimensioni delle macchine virtuali](sizes.md).
Esaminare le caratteristiche delle prestazioni delle Macchine virtuali che usano Archiviazione Premium e scegliere le dimensioni delle VM maggiormente indicate per i propri carichi di lavoro. Assicurarsi che nella macchina virtuale sia disponibile larghezza di banda sufficiente per gestire il traffico dei dischi.

### <a name="disk-sizes"></a>Dimensione disco

**Premium**

È possibile usare sette tipi di Archiviazione Premium con la macchina virtuale, ognuno con limiti IOP e di velocità effettiva specifici. Tenere in considerazione questi limiti nella scelta del tipo di disco Premium per la macchina virtuale in base alle esigenze dell'applicazione in termini di capacità, prestazioni, scalabilità e carichi di picco.

| Tipo di disco Premium  | P4    | P6    | P10   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| Dimensioni disco           | 128 GB| 512 GB| 128 GB| 512 GB            | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| IOPS per disco       | 120   | 240   | 500   | 2300              | 5000              | 7500              | 7500              | 
| Velocità effettiva per disco | 25 MB al secondo  | 50 MB al secondo  | 100 MB al secondo | 150 MB al secondo | 200 MB al secondo | 250 MB al secondo | 250 MB al secondo | 

**Standard**

Esistono sette tipi di dischi Standard che possono essere usati con la macchina virtuale. Si differenziano per capacità ma presentano gli stessi limiti IOP e di velocità effettiva. Scegliere il tipo di disco gestito della versione Standard in base alle esigenze in termini di capacità dell'applicazione.

| Tipo di disco Standard  | S4               | S6               | S10              | S20              | S30              | S40              | S50              | 
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------| 
| Dimensioni disco           | 30 GB            | 64 GB            | 128 GB           | 512 GB           | 1024 GB (1 TB)   | 2048 GB (2 TB)    | 4095 GB (4 TB)   | 
| IOPS per disco       | 500              | 500              | 500              | 500              | 500              | 500             | 500              | 
| Velocità effettiva per disco | 60 MB al secondo | 60 MB al secondo | 60 MB al secondo | 60 MB al secondo | 60 MB al secondo | 60 MB al secondo | 60 MB al secondo | 


### <a name="disk-caching-policy"></a>Criteri di memorizzazione nella cache su disco 

**Managed Disks Premium**

Per impostazione predefinita, il criterio di memorizzazione nella cache su disco è impostato su *Sola lettura* per tutti i dischi di dati Premium e su *Lettura/scrittura* per il disco del sistema operativo Premium collegato alla macchina virtuale. Queste impostazioni di configurazione sono consigliate per ottenere prestazioni ottimali per le operazioni di I/O dell'applicazione. Per i dischi di dati con un utilizzo elevato della scrittura o di sola scrittura (ad esempio i file di log di SQL Server), disabilitare la memorizzazione nella cache su disco in modo da migliorare le prestazioni delle applicazioni.

### <a name="pricing"></a>Prezzi

Esaminare i [prezzi per Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/). Il prezzo dei dischi Premium gestiti è uguale a quello dei dischi Premium non gestiti. Tuttavia, il prezzo dei dischi Standard gestiti è diverso da quello dei dischi Standard non gestiti.


## <a name="checklist"></a>Elenco di controllo

1.  Se si esegue la migrazione a Managed Disks Premium, assicurarsi che sia disponibile nell'area di migrazione.

2.  Decidere la nuova serie di VM da usare. Se si esegue la migrazione a Managed Disks Premium, deve essere compatibile con Archiviazione Premium.

3.  Decidere l'esatta dimensione della macchina virtuale che si userà tra quelle disponibili nell'area in cui si sta eseguendo la migrazione. Le dimensioni della VM devono essere abbastanza grandi da supportare tutti i dischi dati. Ad esempio, se si dispone di quattro dischi dati, la macchina virtuale deve disporre di due o più core. Considerare anche la potenza di elaborazione, la memoria e la larghezza di banda di rete necessarie.

4.  Tenere a portata di mano i dettagli della VM corrente, inclusi l'elenco di dischi e i BLOB VHD corrispondenti.

Preparare l'applicazione per il tempo di inattività. Per eseguire una migrazione senza problemi, è necessario arrestare ogni elaborazione nel sistema corrente. Solo a quel punto lo stato sarà coerente e sarà possibile eseguire la migrazione alla nuova piattaforma. La durata del tempo di inattività dipende dalla quantità di dati nei dischi di cui eseguire la migrazione.


## <a name="migrate-the-vm"></a>Eseguire la migrazione della macchina virtuale

Preparare l'applicazione per il tempo di inattività. Per eseguire una migrazione senza problemi, è necessario arrestare ogni elaborazione nel sistema corrente. Solo a quel punto lo stato sarà coerente e sarà possibile eseguire la migrazione alla nuova piattaforma. La durata del tempo di inattività dipende dalla quantità di dati nei dischi di cui eseguire la migrazione.

Questa parte richiede il modulo Azure PowerShell 6.0.0 o versioni successive. Eseguire ` Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps). È anche necessario eseguire `Connect-AzureRmAccount` per creare una connessione con Azure.


Creare variabili per i parametri comuni.

```powershell
$resourceGroupName = 'yourResourceGroupName'

$location = 'your location' 

$virtualNetworkName = 'yourExistingVirtualNetworkName'

$virtualMachineName = 'yourVMName'

$virtualMachineSize = 'Standard_DS3'

$adminUserName = "youradminusername"

$adminPassword = "yourpassword" | ConvertTo-SecureString -AsPlainText -Force

$imageName = 'yourImageName'

$osVhdUri = 'https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd'

$dataVhdUri = 'https://storageaccount.blob.core.windows.net/vhdcontainer/datadisk1.vhd'

$dataDiskName = 'dataDisk1'
```

Creare un disco del sistema operativo gestito usando il disco rigido virtuale dalla macchina virtuale classica. Assicurarsi di aver fornito l'URI completo del disco rigido virtuale del sistema operativo per il parametro $osVhdUri. Per **-AccountType**, immettere **Premium_LRS** o **Standard_LRS** in base al tipo di dischi (Premium o Standard) a cui si sta eseguendo la migrazione.

```powershell
$osDisk = New-AzureRmDisk -DiskName $osDiskName '
   -Disk (New-AzureRmDiskConfig '
   -AccountType Premium_LRS '
   -Location $location '
   -CreateOption Import '
   -SourceUri $osVhdUri) '
   -ResourceGroupName $resourceGroupName
```

Collegare il disco del sistema operativo alla nuova macchina virtuale.

```powershell
$VirtualMachine = New-AzureRmVMConfig -VMName $virtualMachineName -VMSize $virtualMachineSize
$VirtualMachine = Set-AzureRmVMOSDisk '
   -VM $VirtualMachine '
   -ManagedDiskId $osDisk.Id '
   -StorageAccountType Premium_LRS '
   -DiskSizeInGB 128 '
   -CreateOption Attach -Windows
```

Creare un disco di dati gestiti dal file del disco rigido virtuale dei dati e aggiungerlo alla nuova macchina virtuale.

```powershell
$dataDisk1 = New-AzureRmDisk '
   -DiskName $dataDiskName '
   -Disk (New-AzureRmDiskConfig '
   -AccountType Premium_LRS '
   -Location $location '
   -CreationOption Import '
   -SourceUri $dataVhdUri ) '
   -ResourceGroupName $resourceGroupName
    
$VirtualMachine = Add-AzureRmVMDataDisk '
   -VM $VirtualMachine '
   -Name $dataDiskName '
   -CreateOption Attach '
   -ManagedDiskId $dataDisk1.Id '
   -Lun 1
```

Creare la nuova macchina virtuale impostando l'IP pubblico, la rete virtuale e la scheda di interfaccia di rete.

```powershell
$publicIp = New-AzureRmPublicIpAddress '
   -Name ($VirtualMachineName.ToLower()+'_ip') '
   -ResourceGroupName $resourceGroupName '
   -Location $location '
   -AllocationMethod Dynamic
    
$vnet = Get-AzureRmVirtualNetwork '
   -Name $virtualNetworkName '
   -ResourceGroupName $resourceGroupName
    
$nic = New-AzureRmNetworkInterface '
   -Name ($VirtualMachineName.ToLower()+'_nic') '
   -ResourceGroupName $resourceGroupName '
   -Location $location '
   -SubnetId $vnet.Subnets[0].Id '
   -PublicIpAddressId $publicIp.Id
    
$VirtualMachine = Add-AzureRmVMNetworkInterface '
   -VM $VirtualMachine '
   -Id $nic.Id
    
New-AzureRmVM -VM $VirtualMachine '
   -ResourceGroupName $resourceGroupName '
   -Location $location
```

> [!NOTE]
>È possibile che per supportare l'applicazione siano necessari passaggi specifici che non sono illustrati in questa guida.
>
>

## <a name="next-steps"></a>Passaggi successivi

- Connettersi alla macchina virtuale. Per istruzioni, vedere [Come connettersi e accedere a una macchina virtuale di Azure che esegue Windows Server](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

