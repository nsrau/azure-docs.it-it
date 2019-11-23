---
title: Distribuire un'applicazione IPv6 dual stack usando Load Balancer interni standard in Azure-PowerShell
titlesuffix: Azure Virtual Network
description: Questo articolo illustra come distribuire un'applicazione IPv6 dual stack con Load Balancer interni standard in rete virtuale di Azure tramite Azure PowerShell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/14/2019
ms.author: kumud
ms.openlocfilehash: fdf726fd31e8b92a04a1c136eb5cd7110e0c6d5a
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333365"
---
# <a name="deploy-an-ipv6-dual-stack-application-using-standard-internal-load-balancer-in-azure---powershell-preview"></a>Distribuire un'applicazione IPv6 dual stack usando Load Balancer interni standard in Azure-PowerShell (anteprima)

Questo articolo illustra come distribuire un'applicazione dual stack (IPv4 + IPv6) in Azure che include una rete virtuale e una subnet dual stack, un Load Balancer interno standard con due configurazioni front-end Dual (IPv4 + IPv6), VM con schede di rete con un doppio IP configurazione, gruppo di sicurezza di rete e indirizzi IP pubblici.

> [!Important]
> Il supporto IPv6 per la rete virtuale di Azure è attualmente disponibile in anteprima pubblica. Questa anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

La procedura per creare un Load Balancer interno in grado di supportare IPv6 è quasi identica al processo di creazione di un IPv6 con connessione Internet Load Balancer descritto [qui](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md). Le uniche differenze per la creazione di un servizio di bilanciamento del carico interno sono nella configurazione front-end, come illustrato nell'esempio di PowerShell seguente:

```azurepowershell
 $frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
 -Name "dsLbFrontEnd_v6" `
 -PrivateIpAddress "ace:cab:deca:deed::100" `
 -PrivateIpAddressVersion "IPv6" `
 -Subnet $DsSubnet
```

Le modifiche che rendono la configurazione front-end del servizio di bilanciamento del carico interno sono le seguenti:
- Il `PrivateIpAddressVersion` viene specificato come "IPv6"
- L'argomento `-PublicIpAddress` è stato omesso o sostituito con `-PrivateIpAddress`. Si noti che l'indirizzo privato deve essere compreso nell'intervallo di spazio IP della subnet in cui verrà distribuito il servizio di bilanciamento del carico interno. Se un `-PrivateIpAddress` statico viene omesso, verrà selezionato il successivo indirizzo IPv6 libero dalla subnet in cui viene distribuito il servizio di bilanciamento del carico interno.
- La subnet dello stack doppio in cui verrà distribuito il servizio di bilanciamento del carico interno viene specificata con un argomento `-Subnet` o `-SubnetId`.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare PowerShell in locale, per questo articolo è necessario il modulo Azure PowerShell versione 6.9.0 o successiva. Eseguire `Get-Module -ListAvailable Az` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installare il modulo di Azure PowerShell). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="prerequisites"></a>prerequisiti
Prima di distribuire un'applicazione dual stack in Azure, è necessario configurare la sottoscrizione per questa funzionalità di anteprima usando i Azure PowerShell seguenti:

Registra come segue:
```azurepowershell
Register-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Register-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
Sono necessari fino a 30 minuti per completare la registrazione della funzionalità. È possibile controllare lo stato di registrazione eseguendo il comando seguente Azure PowerShell: controllare la registrazione nel modo seguente:
```azurepowershell
Get-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Get-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
Al termine della registrazione eseguire questo comando:

```azurepowershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Prima di poter creare la rete virtuale a doppio stack, è necessario creare un gruppo di risorse con [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Nell'esempio seguente viene creato un gruppo di risorse denominato *dsStd_ILB_RG* nella località *Stati Uniti orientali* :

```azurepowershell
$rg = New-AzResourceGroup `
  -ResourceGroupName "dsStd_ILB_RG"  `
  -Location "east us"
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses"></a>Creare indirizzi IP pubblici IPv4 e IPv6
Per accedere alle macchine virtuali da Internet, sono necessari indirizzi IP pubblici IPv4 e IPv6 per le VM. Creare indirizzi IP pubblici con [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). L'esempio seguente crea un indirizzo IP pubblico IPv4 e IPv6 denominato *RdpPublicIP_1* e *RdpPublicIP_2* nel gruppo di risorse *dsStd_ILB_RG* :

```azurepowershell
$RdpPublicIP_1 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -IpAddressVersion IPv4  `
  -sku Standard
  
$RdpPublicIP_2 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_2" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -IpAddressVersion IPv4  `
  -sku Standard
```
## <a name="create-the-virtual-network-and-the-subnet"></a>Creare la rete virtuale e la subnet

Creare una rete virtuale usando [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) con dual stack una configurazione di subnet con [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Nell'esempio seguente viene creata una rete virtuale denominata *dsVnet* con *dsSubnet*.

```azurepowershell
# Create dual stack subnet config
$DsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name "dsSubnet" `
  -AddressPrefix "10.0.0.0/24","ace:cab:deca:deed::/64"

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsVnet" `
  -AddressPrefix "10.0.0.0/16","ace:cab:deca::/48"  `
  -Subnet $DsSubnet

#Refresh the fully populated subnet for use in load balancer frontend configuration
$DsSubnet = get-AzVirtualNetworkSubnetconfig -name dsSubnet -VirtualNetwork $vnet
```
## <a name="create-standard-load-balancer"></a>Creare un'istanza di Load Balancer Standard

In questa sezione vengono configurati due IP front-end (IPv4 e IPv6) e il pool di indirizzi back-end per il servizio di bilanciamento del carico e quindi viene creato un Load Balancer Standard.

### <a name="create-front-end-ip"></a>Creare l'IP front-end

Creare un IP front-end con [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig). Nell'esempio seguente vengono create le configurazioni IP front-end IPv4 e IPv6 denominate *dsLbFrontEnd_v4* e *dsLbFrontEnd_v6*:

```azurepowershell
$frontendIPv4 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v4" `
  -PrivateIpAddress "10.0.0.100"  `
  -PrivateIpAddressVersion "IPv4"   `
  -Subnet $DsSubnet

$frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PrivateIpAddress "ace:cab:deca:deed::100"  `
  -PrivateIpAddressVersion "IPv6"   `
  -Subnet $DsSubnet

```

### <a name="configure-back-end-address-pool"></a>Configurare il pool di indirizzi back-end

Creare un pool di indirizzi back-end con [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig). Le macchine virtuali si collegano a questo pool back-end nei passaggi rimanenti. Nell'esempio seguente vengono creati pool di indirizzi back-end denominati *dsLbBackEndPool_v4* e *DsLbBackEndPool_v6* per includere VM con configurazioni NIC sia IPv4 che IPv6:

```azurepowershell
$backendPoolv4 = New-AzLoadBalancerBackendAddressPoolConfig -Name "dsLbBackEndPool_v4"

$backendPoolv6 = New-AzLoadBalancerBackendAddressPoolConfig -Name "dsLbBackEndPool_v6"
```

### <a name="create-a-load-balancer-rule"></a>Creare una regola di bilanciamento del carico

Una regola di bilanciamento del carico consente di definire come il traffico verrà distribuito alle VM. Definire la configurazione IP front-end per il traffico in ingresso e il pool IP di back-end affinché riceva il traffico, insieme alla porta di origine e di destinazione necessaria. Per assicurarsi che solo le macchine virtuali integre ricevano il traffico, è possibile definire facoltativamente un probe di integrità. Il servizio di bilanciamento del carico di base usa un probe IPv4 per valutare l'integrità degli endpoint IPv4 e IPv6 nelle VM. Load Balancer standard include il supporto per i probe di integrità IPv6 espliciti.

Creare una regola di bilanciamento del carico con [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig). L'esempio seguente crea regole di bilanciamento del carico denominate *dsLBrule_v4* e *dsLBrule_v6* e bilancia il traffico sulla porta *TCP* *80* alle configurazioni IP front-end IPv4 e IPv6:

```azurepowershell
$lbrule_v4 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v4" `
  -FrontendIpConfiguration $frontendIPv4 `
  -BackendAddressPool $backendPoolv4 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80

$lbrule_v6 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80
```

### <a name="create-load-balancer"></a>Creare un servizio di bilanciamento del carico

Creare una Load Balancer Standard con [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). L'esempio seguente crea un Load Balancer Standard pubblico denominato *myInternalLoadBalancer* usando le configurazioni IP front-end IPv4 e IPv6, i pool back-end e le regole di bilanciamento del carico create nei passaggi precedenti:

```azurepowershell
$lb = New-AzLoadBalancer  `
  -ResourceGroupName $rg.ResourceGroupName  `
  -Location $rg.Location  `
  -Name  "MyInternalLoadBalancer"  `
  -Sku "Standard"  `
  -FrontendIpConfiguration  $frontendIPv4,$frontendIPv6  `
  -BackendAddressPool  $backendPoolv4,$backendPoolv6  `
  -LoadBalancingRule  $lbrule_v4,$lbrule_v6

```

## <a name="create-network-resources"></a>Creare risorse di rete
Prima di distribuire alcune macchine virtuali e testare il servizio di bilanciamento, è necessario creare risorse di rete di supporto: set di disponibilità, gruppo di sicurezza di rete e NIC virtuali. 

### <a name="create-an-availability-set"></a>Creare un set di disponibilità
Per migliorare la disponibilità elevata dell'applicazione, posizionare le macchine virtuali in un set di disponibilità.

Creare un set di disponibilità con [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset). Nell'esempio seguente viene creato un set di disponibilità denominato *dsAVset*:

```azurepowershell
$avset = New-AzAvailabilitySet `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsAVset" `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2 `
  -Sku aligned
```

### <a name="create-network-security-group"></a>Creare un gruppo di sicurezza di rete

Creare un gruppo di sicurezza di rete per le regole che gestiranno le comunicazioni in ingresso e in uscita nella VNet.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Creare una regola del gruppo di sicurezza di rete per la porta 3389

Per consentire le connessioni RDP tramite la porta 3389, creare una regola del gruppo di sicurezza di rete con [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell
$rule1 = New-AzNetworkSecurityRuleConfig `
  -Name 'myNetworkSecurityGroupRuleRDP' `
  -Description 'Allow RDP' `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 3389
```
#### <a name="create-a-network-security-group-rule-for-port-80"></a>Creare una regola del gruppo di sicurezza di rete per la porta 80

Creare una regola del gruppo di sicurezza di rete per consentire le connessioni Internet tramite la porta 80 con [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell
$rule2 = New-AzNetworkSecurityRuleConfig `
  -Name 'myNetworkSecurityGroupRuleHTTP' `
  -Description 'Allow HTTP' `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange 80 `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80
```
#### <a name="create-a-network-security-group"></a>Creare un gruppo di sicurezza di rete

Creare un gruppo di sicurezza di rete con [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

```azurepowershell
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsNSG1"  `
  -SecurityRules $rule1,$rule2
```
### <a name="create-nics"></a>Creare NIC

Creare schede di rete virtuali con [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). Nell'esempio seguente vengono create due schede NIC virtuali con le configurazioni IPv4 e IPv6. Una scheda di interfaccia di rete virtuale per ogni VM creata per l'app nei passaggi successivi.

```azurepowershell

# Create the IPv4 configuration for NIC 1
$Ip4Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp4Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv4 `
  -LoadBalancerBackendAddressPool $backendPoolv4 `
  -PublicIpAddress  $RdpPublicIP_1

# Create the IPv6 configuration
$Ip6Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp6Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv6 `
  -LoadBalancerBackendAddressPool $backendPoolv6

# Create NIC 1
$NIC_1 = New-AzNetworkInterface `
  -Name "dsNIC1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -NetworkSecurityGroupId $nsg.Id `
  -IpConfiguration $Ip4Config,$Ip6Config

# Create the IPv4 configuration for NIC 2
$Ip4Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp4Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv4 `
  -LoadBalancerBackendAddressPool $backendPoolv4 `
  -PublicIpAddress  $RdpPublicIP_2

# Create NIC 2 reusing the IPv6 configuration from NIC 1
$NIC_2 = New-AzNetworkInterface `
  -Name "dsNIC2" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -NetworkSecurityGroupId $nsg.Id `
  -IpConfiguration $Ip4Config,$Ip6Config

```

### <a name="create-virtual-machines"></a>Creare macchine virtuali

Impostare nome utente e password dell'amministratore delle macchine virtuali con il comando [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell
$cred = get-credential -Message "DUAL STACK VNET SAMPLE:  Please enter the Administrator credential to log into the VM's"
```

A questo punto è possibile creare le VM con [New-AzVM](/powershell/module/az.compute/new-azvm). Nell'esempio seguente vengono creare due macchine virtuali e i componenti di rete virtuale necessari, se non esistono già.

```azurepowershell
$vmsize = "Standard_A2"
$ImagePublisher = "MicrosoftWindowsServer"
$imageOffer = "WindowsServer"
$imageSKU = "2019-Datacenter"

$vmName= "dsVM1"
$VMconfig1 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_1.Id  3> $null
$VM1 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig1


$vmName= "dsVM2"
$VMconfig2 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_2.Id  3> $null
$VM2 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig2
```
## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Visualizza rete virtuale dual stack IPv6 in portale di Azure
È possibile visualizzare la rete virtuale dual stack IPv6 in portale di Azure come indicato di seguito:
1. Nella barra di ricerca del portale immettere *dsVnet*.
2. Selezionare **dsVnet** quando viene visualizzato nei risultati della ricerca. Verrà avviata la pagina **Panoramica** della rete virtuale dual stack denominata *dsVnet*. La rete virtuale dual stack Mostra le due schede NIC con le configurazioni IPv4 e IPv6 presenti nella subnet dello stack doppio denominata *dsSubnet*.

![Rete virtuale dual stack IPv6 con Load Balancer interno standard](./media/ipv6-dual-stack-standard-internal-load-balancer-powershell/ipv6-dual-stack-virtual-network.png)


> [!NOTE]
> Il protocollo IPv6 per la rete virtuale di Azure è disponibile nella portale di Azure in sola lettura per questa versione di anteprima.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile usare il comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```azurepowershell
Remove-AzResourceGroup -Name dsStd_ILB_RG
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato creato un Load Balancer Standard con una doppia configurazione IP front-end (IPv4 e IPv6). Sono state anche create due macchine virtuali che includono nic con doppie configurazioni IP (IPV4 + IPv6) che sono state aggiunte al pool back-end del servizio di bilanciamento del carico. Per altre informazioni sul supporto di IPv6 in reti virtuali di Azure, vedere [che cos'è IPv6 per rete virtuale di Azure?](ipv6-overview.md)