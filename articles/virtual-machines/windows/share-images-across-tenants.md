---
title: Condividere immagini della raccolta tra tenant in AzureShare gallery images across tenants in Azure
description: Informazioni su come condividere immagini di macchine virtuali tra tenant di Azure usando raccolte di immagini condivise.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 07/15/2019
ms.author: cynthn
ms.openlocfilehash: 9b7e7066f186017b7cc4408cd4f7edcc7e5f0dcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74065512"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Condividere immagini di macchine virtuali della raccolta tra tenant di AzureShare gallery VM images across Azure tenants

Le raccolte di immagini condivise consentono di condividere immagini utilizzando il controllo degli accessi in base al ruolo. È possibile usare il controllo degli accessi in base al ruolo per condividere immagini all'interno del tenant e anche a utenti esterni al tenant. Per ulteriori informazioni su questa semplice opzione di condivisione, vedere [la raccolta Condividi la raccolta](/azure/virtual-machines/windows/shared-images-portal#share-the-gallery).

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]


> [!IMPORTANT]
> Non è possibile usare il portale per distribuire una macchina virtuale da un'immagine in un altro tenant azure.You cannot use the portal to deploy a VM from an image in another azure tenant. Per creare una macchina virtuale da un'immagine condivisa tra tenant, è necessario usare l'interfaccia della riga di comando di Azure o [Powershell.To](../linux/share-images-across-tenants.md) create a VM from an image shared between tenants, you must use the Azure CLI or Powershell.

## <a name="create-a-vm-using-powershell"></a>Creare una macchina virtuale con PowerShell

Accedere a entrambi i tenant utilizzando l'ID applicazione, il segreto e l'ID tenant. 

```azurepowershell-interactive
$applicationId = '<App ID>'
$secret = <Secret> | ConvertTo-SecureString -AsPlainText -Force
$tenant1 = "<Tenant 1 ID>"
$tenant2 = "<Tenant 2 ID>"
$cred = New-Object -TypeName PSCredential -ArgumentList $applicationId, $secret
Clear-AzContext
Connect-AzAccount -ServicePrincipal -Credential $cred  -Tenant "<Tenant 1 ID>"
Connect-AzAccount -ServicePrincipal -Credential $cred -Tenant "<Tenant 2 ID>"
```

Creare la macchina virtuale nel gruppo di risorse che dispone dell'autorizzazione per la registrazione dell'app. Sostituire le informazioni in questo esempio con le proprie.



```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$location = "South Central US"
$vmName = "myVMfromImage"

# Set a variable for the image version in Tenant 1 using the full image ID of the shared image version
$image = "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Networking pieces
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using the $image variable to specify the shared image
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -Id $image | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

## <a name="next-steps"></a>Passaggi successivi

È anche possibile creare risorse della raccolta di immagini condivise usando il portale di Azure.You can also create shared image gallery resources using the [Azure portal](shared-images-portal.md).