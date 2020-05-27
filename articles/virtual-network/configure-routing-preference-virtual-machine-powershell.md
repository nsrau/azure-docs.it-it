---
title: Configurare le preferenze di routing per una macchina virtuale - Azure PowerShell
description: Informazioni su come creare una macchina virtuale con un indirizzo IP pubblico scegliendo il tipo di routing con Azure PowerShell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2020
ms.author: mnayak
ms.openlocfilehash: 1b23340840c2b88ea3f4f9927364bd3e01d8d55a
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596130"
---
# <a name="configure-routing-preference-for-a-vm-using-azure-powershell"></a>Configurare le preferenze di routing per una macchina virtuale con Azure PowerShell

Questo articolo illustra come configurare le preferenze di routing per una macchina virtuale. Quando si sceglie **Internet** come preferenza di routing, il traffico associato a Internet dalla macchina virtuale viene instradato tramite la rete ISP. L'opzione di routing predefinita è la rete globale Microsoft.

Questo articolo spiega come creare una macchina virtuale con un indirizzo IP pubblico impostato per instradare il traffico tramite la rete ISP con Azure PowerShell.

> [!IMPORTANT]
> La preferenza di routing è attualmente Anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-feature-for-your-subscription"></a>Registrare la funzionalità per la propria sottoscrizione
La funzionalità Preferenza di routing è attualmente in anteprima. Registrare la funzionalità per la propria sottoscrizione come indicato di seguito:
```azurepowershell
Register-AzProviderFeature -FeatureName AllowRoutingPreferenceFeature ProviderNamespace Microsoft.Network
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse
1. Se si usa Cloud Shell, andare al passaggio 2. Aprire una sessione di comando e accedere ad Azure con `Connect-AzAccount`.
2. Creare un gruppo di risorse con il comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). L'esempio seguente crea un gruppo di risorse nell'area di Azure Stati Uniti orientali:

    ```azurepowershell
    $rg = New-AzResourceGroup -Name MyResourceGroup -Location EastUS
    ```

## <a name="create-a-public-ip-address"></a>Creare un indirizzo IP pubblico

Per accedere alla propria macchina virtuale da Internet è necessario un indirizzo IP pubblico. Creare un indirizzo IP pubblico con [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). Nell'esempio seguente viene creato un indirizzo IP pubblico IPv4 denominato *MyPublicIP* con tipo di routing *Internet* nel gruppo di risorse *MyResourceGroup* nell'area *Stati Uniti orientali*:

```azurepowershell-interactive
$iptagtype="RoutingPreference"
$tagName = "Internet"
$ipTag = New-AzPublicIpTag -IpTagType $iptagtype -Tag $tagName 
# attach the tag
$publicIp = New-AzPublicIpAddress  `
-Name "MyPublicIP" `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location `
-IpTag $ipTag `
-AllocationMethod Static `
-Sku Standard `
-IpAddressVersion IPv4
```

## <a name="create-network-resources"></a>Creare risorse di rete

Prima di distribuire una macchina virtuale è necessario creare le risorse di rete virtuale di supporto, ovvero il gruppo di sicurezza di rete, la rete virtuale e il NIC virtuale.

### <a name="create-a-network-security-group"></a>Creare un gruppo di sicurezza di rete

Creare un gruppo di sicurezza di rete con [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup). Nell'esempio seguente viene creato un gruppo di sicurezza di rete denominato *myNSG*

```azurepowershell
$nsg = New-AzNetworkSecurityGroup `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "myNSG"
```

### <a name="create-a-virtual-network"></a>Crea rete virtuale

Creare una rete virtuale con [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Nell'esempio seguente viene creata una rete virtuale denominata *myVNET* con una subnet denominata *mySubNet*:

### <a name="create-a-subnet"></a>Creare una subnet

```azurepowershell
$subnet = New-AzVirtualNetworkSubnetConfig `
-Name "mySubnet" `
-AddressPrefix "10.0.0.0/24"
```

```azurepowershell
# Create a virtual network
$vnet = New-AzVirtualNetwork `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "myVNET" `
-AddressPrefix "10.0.0.0/16" `
-Subnet $subnet
```

### <a name="create-a-nic"></a>Creare una scheda di interfaccia di rete

Creare schede di rete virtuali con [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface. Nell'esempio seguente viene creata una scheda di interfaccia di rete virtuale.

```azurepowershell
# Create an IP Config
$ipconfig=New-AzNetworkInterfaceIpConfig `
-Name myIpConfig `
-Subnet $vnet.subnets[0] `
-PrivateIpAddressVersion IPv4 `
-PublicIpAddress  $publicIp

# Create a NIC
$nic = New-AzNetworkInterface `
-Name "mynic" `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-NetworkSecurityGroupId $nsg.Id `
-IpConfiguration $ipconfig 
```

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

Impostare nome utente e password dell'amministratore delle macchine virtuali con il comando [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell
 $cred = get-credential -Message "Routing Preference SAMPLE:  Please enter the Administrator credential to log into the VM."
```

A questo punto è possibile creare la VM con [New-AzVM](/powershell/module/az.compute/new-azvm). Nell'esempio seguente vengono creare due macchine virtuali e i componenti di rete virtuale necessari, se non esistono già.

```azurepowershell
 $vmsize = "Standard_A2"
 $ImagePublisher = "MicrosoftWindowsServer"
 $imageOffer = "WindowsServer"
 $imageSKU = "2019-Datacenter"

 $vmName= "myVM"
 $vmconfig = New-AzVMConfig -VMName $vmName -VMSize $vmsize | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption "FromImage" | Add-AzVMNetworkInterface -Id $nic.Id 
 $VM1 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $vmconfig
```

## <a name="allow-network-traffic-to-the-vm"></a>Consentire il traffico di rete verso la macchina virtuale

Prima di connettersi all'indirizzo IP pubblico da Internet, assicurarsi che le porte necessarie siano aperte in tutti i gruppi di sicurezza di rete che potrebbero essere associati all'interfaccia di rete, alla subnet in cui si trova l'interfaccia di rete o a entrambe. È possibile visualizzare le regole di sicurezza effettive per un'interfaccia di rete e la relativa subnet usando il [portale](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal), l'[interfaccia della riga di comando](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli) o [PowerShell](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell).

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile usare il comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

 ```azurepowershell
 Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su [preferenze di routing negli indirizzi IP pubblici](routing-preference-overview.md).
* Altre informazioni sugli [indirizzi IP pubblici](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) in Azure.
* Altre informazioni sull'[impostazione dell'indirizzo IP pubblico](virtual-network-public-ip-address.md#create-a-public-ip-address).