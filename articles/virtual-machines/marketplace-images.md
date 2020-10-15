---
title: Specificare le informazioni sul piano di acquisto del Marketplace utilizzando Azure PowerShell
description: Informazioni su come specificare i dettagli del piano di acquisto di Azure Marketplace durante la creazione di immagini in una raccolta di immagini condivise.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 07/07/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 3de79e5cb3db2d0c52d13826900ec7160271edf9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86225160"
---
# <a name="supply-azure-marketplace-purchase-plan-information-when-creating-images"></a>Fornire informazioni sul piano di acquisto di Azure Marketplace durante la creazione di immagini

Se si crea un'immagine in una raccolta condivisa, usando un'origine creata originariamente da un'immagine di Azure Marketplace, potrebbe essere necessario tenere traccia delle informazioni sul piano di acquisto. Questo articolo illustra come trovare informazioni sul piano di acquisto per una macchina virtuale, quindi usare tali informazioni quando si crea una definizione di immagine. Viene inoltre illustrata l'utilizzo delle informazioni della definizione dell'immagine per semplificare la fornitura delle informazioni sul piano di acquisto quando si crea una macchina virtuale per un'immagine.

Per altre informazioni su come trovare e usare le immagini del Marketplace, vedere [trovare e usare immagini di Azure Marketplace](./windows/cli-ps-findimage.md).


## <a name="get-the-source-vm-information"></a>Ottenere le informazioni sulla VM di origine
Se la macchina virtuale originale è ancora presente, è possibile ottenere le informazioni relative al piano, al server di pubblicazione e allo SKU usando Get-AzVM. Questo esempio Mostra come ottenere una macchina virtuale denominata *myVM* nel gruppo di risorse *myResourceGroup* e quindi visualizzare le informazioni sul piano di acquisto.

```azurepowershell-interactive
$vm = Get-azvm `
   -ResourceGroupName myResourceGroup `
   -Name myVM
$vm.Plan.Publisher
$vm.Plan.Name
$vm.Plan.Product
```

## <a name="create-the-image-definition"></a>Creare la definizione dell'immagine

Ottenere la raccolta immagini che si vuole usare per archiviare l'immagine. Per prima cosa è possibile elencare tutte le raccolte.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

Quindi creare le variabili per la raccolta che si vuole usare. In questo esempio viene creata una variabile denominata `$gallery` per la *raccolta* nel gruppo di risorse *myGalleryRG* .

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myGalleryRG
```

Creare la definizione dell'immagine usando i  `-PurchasePlanPublisher` `-PurchasePlanProduct` parametri, e `-PurchasePlanName` .

```azurepowershell-interactive
 $imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Linux `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU' `
   -PurchasePlanPublisher $vm.Plan.Publisher `
   -PurchasePlanProduct $vm.Plan.Product `
   -PurchasePlanName  $vm.Plan.Name
```

Creare quindi la versione dell'immagine usando [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion). È possibile creare una versione di immagine da una [macchina virtuale](image-version-vm-powershell.md#create-an-image-version), da un' [immagine gestita](image-version-managed-image-powershell.md#create-an-image-version), da [VHD\snapshot](image-version-snapshot-powershell.md#create-an-image-version)o da [un'altra versione di immagine](image-version-another-gallery-powershell.md#create-the-image-version). 


## <a name="create-the-vm"></a>Creare la VM

Quando si passa alla creazione di una macchina virtuale dall'immagine, è possibile usare le informazioni della definizione dell'immagine per passare le informazioni del server di pubblicazione tramite [set-AzVMPlan](/powershell/module/az.compute/set-azvmplan).


```azurepowershell-interactive
# Create some variables for the new VM.
$resourceGroup = "mySIGPubVM"
$location = "West Central US"
$vmName = "mySIGPubVM"

# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create the network resources.
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet `
   -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name MYvNET `
   -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -Name "mypublicdns$(Get-Random)" `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig `
   -Name myNetworkSecurityGroupRuleRDP  `
   -Protocol Tcp `
   -Direction Inbound `
   -Priority 1000 `
   -SourceAddressPrefix * `
   -SourcePortRange * `
   -DestinationAddressPrefix * `
   -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name myNetworkSecurityGroup `
   -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface `
   -Name $vmName `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using Set-AzVMSourceImage -Id $imageDefinition.Id to use the latest available image version. Set-AZVMPlan is used to pass the plan information in for the VM.

$vmConfig = New-AzVMConfig `
   -VMName $vmName `
   -VMSize Standard_D1_v2   | `
   Set-AzVMSourceImage -Id $imageDefinition.Id | `
   Set-AzVMPlan `
     -Publisher $imageDefinition.PurchasePlan.Publisher `
     -Product $imageDefinition.PurchasePlan.Product `
     -Name $imageDefinition.PurchasePlan.Name | `
   Add-AzVMNetworkInterface -Id $nic.Id

# Create the virtual machine
New-AzVM `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -VM $vmConfig
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come trovare e usare le immagini del Marketplace, vedere [trovare e usare immagini di Azure Marketplace](./windows/cli-ps-findimage.md).