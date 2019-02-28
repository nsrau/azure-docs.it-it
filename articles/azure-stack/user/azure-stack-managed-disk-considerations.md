---
title: Differenze e considerazioni per Managed Disks in Azure Stack | Microsoft Docs
description: Informazioni sulle differenze e considerazioni quando si lavora con Managed Disks in Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2019
ms.author: sethm
ms.reviewer: jiahan
ms.lastreviewed: 02/26/2019
ms.openlocfilehash: c1a0e77f98d269185bc065c86a367c3ed6519fb5
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961976"
---
# <a name="azure-stack-managed-disks-differences-and-considerations"></a>Azure Stack Managed Disks: differenze e considerazioni

Questo articolo riepiloga le differenze tra note [Managed Disks di Azure Stack](azure-stack-manage-vm-disks.md) e [Managed Disks per Azure](../../virtual-machines/windows/managed-disks-overview.md). Per altre informazioni sulle differenze generali tra Azure e Azure Stack, vedere la [considerazioni chiave](azure-stack-considerations.md) articolo.

Managed Disks semplifica la gestione dei dischi per le macchine virtuali IaaS gestendo il [gli account di archiviazione](../azure-stack-manage-storage-accounts.md) associati ai dischi della macchina virtuale.

> [!Note]  
> Managed Disks in Azure Stack è disponibile da 1808 update. Si è abilitato per impostazione predefinita durante la creazione di macchine virtuali usando il portale di Azure Stack da 1811 update.
  
## <a name="cheat-sheet-managed-disk-differences"></a>Foglio informativo: Differenze di disco gestito

| Funzionalità | Azure (globale) | Azure Stack |
| --- | --- | --- |
|Crittografia per dati inattivi |Crittografia del Servizio archiviazione di Azure (SSE), crittografia dischi di Azure (ADE)     |Crittografia AES a 128 bit BitLocker      |
|Image          | Supporta l'immagine personalizzata gestita |Supportato|
|Opzioni di backup |Supporto servizio Backup di Azure |Non è ancora supportata |
|Opzioni di ripristino di emergenza |Supporto per Azure Site Recovery |Non è ancora supportata|
|Tipi di disco     |Unità SSD Premium SSD Standard (anteprima) e unità disco rigido Standard |Premium SSD, HDD Standard |
|Dischi Premium  |Supporto completo |Può essere eseguito il provisioning, ma nessun limite delle prestazioni o garanzia  |
|IOPs di dischi Premium  |Dipende dalle dimensioni del disco  |2300 IOPs per disco |
|Velocità effettiva di dischi Premium |Dipende dalle dimensioni del disco |145 MB al secondo per disco |
|Dimensioni disco  |Azure Premium Disk: P4 (32 GiB) a P80 (32 TiB)<br>Disco SSD Standard di Azure: E10 (128 GiB) a E80 (32 TiB)<br>Disco di Azure Standard HDD: S4 (32 GiB) a S80 (32 TiB) |M4: 32 GiB<br>M6: 64 GiB<br>M10: 128 GiB<br>M15: 256 GiB<br>M20: 512 GiB<br>M30: 1024 GiB |
|Copia di snapshot di dischi|Lo snapshot di Azure collegati a una macchina virtuale in esecuzione supportata dischi gestiti|Non è ancora supportata |
|Analisi delle prestazioni di dischi |Aggregare le metriche e metriche per disco supportate |Non è ancora supportata |
|Migrazione      |Fornisce lo strumento per eseguire la migrazione da non gestiti Azure Resource Manager le macchine virtuali esistenti senza dover ricreare la macchina virtuale  |Non è ancora supportata |

> [!NOTE]  
> Dischi IOPs e velocità effettiva in Azure Stack è un numero limite di utilizzo anziché un numero con provisioning, che potrebbe interessate da carichi di lavoro in esecuzione in Azure Stack e hardware.

## <a name="metrics"></a>Metriche

Esistono anche differenze con le metriche di archiviazione:

- Con Azure Stack, i dati delle transazioni metriche di archiviazione non viene fatta distinzione della larghezza di banda di rete interna o esterna.
- Azure Stack transazione i dati in metriche di archiviazione non includono l'accesso alle macchine virtuali nei dischi montati.

## <a name="api-versions"></a>Versioni dell'API

Azure Stack Managed Disks supporta le versioni dell'API seguente:

- 2017-03-30
- 2017-12-01

## <a name="convert-to-managed-disks"></a>Conversione a managed disks

È possibile usare lo script seguente per convertire una macchina virtuale attualmente sottoposte a provisioning da non gestiti a managed disks. Sostituire i segnaposto con i propri valori:

```powershell
$subscriptionId = 'subid'

# The name of your resource group
$resourceGroupName ='rgmgd'

# The name of the managed disk
$diskName = 'unmgdvm'

# The size of the disks in GB. It should be greater than the VHD file size.
$diskSize = '50'

# The URI of the VHD file that will be used to create the managed disk.
# The VHD file can be deleted as soon as the managed disk is created.
$vhdUri = 'https://rgmgddisks347.blob.local.azurestack.external/vhds/unmgdvm20181109013817.vhd' 

# The storage type for the managed disk; PremiumLRS or StandardLRS.
$accountType = 'StandardLRS'

# The Azure Stack location where the managed disk is located.
# The location should be the same as the location of the storage account in which VHD file is stored.
$location = 'local'
$virtualMachineName = 'mgdvm'
$virtualMachineSize = 'Standard_D1'
$pipname = 'unmgdvm-ip'
$virtualNetworkName = 'rgmgd-vnet'
$nicname = 'unmgdvm295'

# Set the context to the subscription ID in which the managed disk will be created
Select-AzureRmSubscription -SubscriptionId $SubscriptionId

$diskConfig = New-AzureRmDiskConfig -AccountType $accountType  -Location $location -DiskSizeGB $diskSize -SourceUri $vhdUri -CreateOption Import

# Create managed disk
New-AzureRmDisk -DiskName $diskName -Disk $diskConfig -ResourceGroupName $resourceGroupName
$disk = get-azurermdisk -DiskName $diskName -ResourceGroupName $resourceGroupName
$VirtualMachine = New-AzureRmVMConfig -VMName $virtualMachineName -VMSize $virtualMachineSize

# Use the managed disk resource ID to attach it to the virtual machine.
# Change the OS type to Linux if the OS disk has Linux OS.
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -ManagedDiskId $disk.Id -CreateOption Attach -Linux

# Create a public IP for the VM
$publicIp = Get-AzureRmPublicIpAddress -Name $pipname -ResourceGroupName $resourceGroupName 

# Get the virtual network where the virtual machine will be hosted
$vnet = Get-AzureRmVirtualNetwork -Name $virtualNetworkName -ResourceGroupName $resourceGroupName

# Create NIC in the first subnet of the virtual network
$nic = Get-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $resourceGroupName

$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $nic.Id

# Create the virtual machine with managed disk
New-AzureRmVM -VM $VirtualMachine -ResourceGroupName $resourceGroupName -Location $location
```

## <a name="managed-images"></a>Immagini gestite

Azure supporta Stack *gestiti immagini*, che consentono di creare un oggetto immagine gestita in una VM generalizzata (sia non gestite e gestite) che può creare solo gestita su disco le macchine virtuali in futuro. Immagini gestite abilitano i due scenari seguenti:

- Si hanno generalizzato macchine virtuali non gestite e si vuole usare managed disks in futuro.
- Si dispone di una macchina virtuale gestita generalizzata e si vuole creare più, macchine virtuali gestite simile.

### <a name="migrate-unmanaged-vms-to-managed-disks"></a>Eseguire la migrazione di macchine virtuali non gestite a managed disks

Seguire le istruzioni riportate [qui](../../virtual-machines/windows/capture-image-resource.md#create-an-image-from-a-vhd-in-a-storage-account) per creare un'immagine gestita da un disco rigido virtuale generalizzato in un account di archiviazione. Questa immagine può essere usata per creare macchine virtuali gestite in futuro.

### <a name="create-managed-image-from-vm"></a>Creare l'immagine gestita dalla macchina virtuale

Dopo la creazione di un'immagine da un oggetto esistente gestito del disco della macchina virtuale usando lo script [qui](../../virtual-machines/windows/capture-image-resource.md#create-an-image-from-a-managed-disk-using-powershell) , lo script di esempio seguente crea una VM Linux simili da un oggetto immagine esistente:

```powershell
# Variables for common values
$resourceGroup = "myResourceGroup"
$location = "redmond"
$vmName = "myVM"
$imagerg = "managedlinuxrg"
$imagename = "simplelinuxvmm-image-2019122"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzureRmResourceGroup -Name $resourceGroup -Location $location

# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

$image = get-azurermimage -ResourceGroupName $imagerg -ImageName $imagename
# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize Standard_D1 | `
Set-AzureRmVMOperatingSystem -Linux -ComputerName $vmName -Credential $cred | `
Set-AzureRmVMSourceImage -Id $image.Id | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

Per altre informazioni, vedere Azure gestito articoli immagine [creare un'immagine gestita di una macchina virtuale generalizzata in Azure](../../virtual-machines/windows/capture-image-resource.md) e [creare una macchina virtuale da un'immagine gestita](../../virtual-machines/windows/create-vm-generalized-managed.md).

## <a name="configuration"></a>Configurazione

Dopo aver applicato la 1808 aggiornare o versioni successive, è necessario eseguire la configurazione seguente prima di usare Managed Disks:

- Se è stata creata una sottoscrizione prima dell'aggiornamento 1808, seguire questa procedura per aggiornare la sottoscrizione. In caso contrario, la distribuzione di macchine virtuali in questa sottoscrizione potrebbe non riuscire con un messaggio di errore "Errore interno in Gestione disco".
   1. Nel portale Tenant, passare a **sottoscrizioni** e individuare la sottoscrizione. Fare clic su **provider di risorse**, quindi fare clic su **Microsoft. COMPUTE**, quindi fare clic su **registrare nuovamente**.
   2. Nella stessa sottoscrizione, passare a **controllo di accesso (IAM)** e verificare che **Azure Stack-Managed Disks** sia elencato.
- Se si usa un ambiente multi-tenant, porre l'operatore cloud (che può essere un'organizzazione o dal provider di servizi) per riconfigurare tutte le directory guest seguendo i passaggi descritti in [questo articolo](../azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory). In caso contrario, la distribuzione di macchine virtuali in una sottoscrizione associata a tale directory guest potrebbe non riuscire con un messaggio di errore "Errore interno in Gestione disco".

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni su macchine virtuali di Azure Stack](azure-stack-compute-overview.md)
