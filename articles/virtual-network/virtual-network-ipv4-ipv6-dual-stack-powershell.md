---
title: Distribuire un'applicazione dual-stack IPv6 nella rete virtuale di Azure - PowerShell
titlesuffix: Azure Virtual Network
description: Questo articolo illustra come distribuisce un'applicazione di dual-stack IPv6 nella rete virtuale di Azure usando Azure Powershell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: 5ef051f42f3d092cc1d88008eaa8af981684ac6c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66730049"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure---powershell-preview"></a>Distribuire un'applicazione dual-stack IPv6 in Azure PowerShell (anteprima)

Questo articolo illustra come distribuire un'applicazione dual stack (IPv4 + IPv6) in Azure che include una rete virtuale dual-stack e una subnet, un servizio di bilanciamento del carico con configurazioni front-end dual (IPv4 + IPv6), le macchine virtuali con schede di rete con una configurazione IP duale, rete gruppo di sicurezza e gli indirizzi IP pubblici.

> [!Important]
> Supporto di IPv6 per la rete virtuale di Azure è attualmente in anteprima pubblica. Questa anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare PowerShell in locale, questo articolo richiede il modulo Azure PowerShell versione 6.9.0 o versione successiva. Eseguire `Get-Module -ListAvailable Az` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-Az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="prerequisites"></a>Prerequisiti
Prima di distribuire un'applicazione dual-stack in Azure, è necessario configurare la sottoscrizione per questa funzionalità di anteprima usando Azure PowerShell seguente:

Registrare come indicato di seguito:
```azurepowershell
Register-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
```
Sono necessari fino a 30 minuti per completare la registrazione della funzionalità. È possibile controllare lo stato della registrazione eseguendo il comando di Azure PowerShell seguente: Controllare la registrazione come indicato di seguito:
```azurepowershell
Get-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
```
Al termine della registrazione eseguire questo comando:

```azurepowershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Prima di creare la rete virtuale dual stack, è necessario creare un gruppo di risorse [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). L'esempio seguente crea un gruppo di risorse denominato *myRGDualStack* nel *east us* percorso:

```azurepowershell-interactive
   $rg = New-AzResourceGroup `
  -ResourceGroupName "dsRG1"  `
  -Location "east us"
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses"></a>Creare indirizzi IP pubblici IPv4 e IPv6
Per accedere alle macchine virtuali da Internet, è necessario indirizzi IP pubblici IPv4 e IPv6 per il bilanciamento del carico. Creare gli indirizzi IP pubblici con [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). L'esempio seguente crea IPv4 e IPv6 indirizzo IP pubblico denominato *dsPublicIP_v4* e *dsPublicIP_v6* nel *dsRG1* gruppo di risorse:

```azurepowershell-interactive
$PublicIP_v4 = New-AzPublicIpAddress `
  -Name "dsPublicIP_v4" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv4
  
$PublicIP_v6 = New-AzPublicIpAddress `
  -Name "dsPublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv6
```
Per accedere a macchine virtuali tramite una connessione RDP, creare un indirizzi IP pubblici IPV4 per le macchine virtuali con [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress).

```azurepowershell-interactive
  $RdpPublicIP_1 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv4
  
  $RdpPublicIP_2 = New-AzPublicIpAddress `
   -Name "RdpPublicIP_2" `
   -ResourceGroupName $rg.ResourceGroupName `
   -Location $rg.Location  `
   -AllocationMethod Dynamic `
   -IpAddressVersion IPv4
```

## <a name="create-basic-load-balancer"></a>Creare un servizio di bilanciamento del carico di base

In questa sezione viene configurato duale front-end IP (IPv4 e IPv6) e il pool di indirizzi back-end di bilanciamento del carico e quindi creare un Load Balancer Basic.

### <a name="create-front-end-ip"></a>Creare l'IP front-end

Creare un IP front-end con [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig). L'esempio seguente crea front-end IPv4 e IPv6 configurazioni IP denominate *dsLbFrontEnd_v4* e *dsLbFrontEnd_v6*:

```azurepowershell-interactive
$frontendIPv4 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v4" `
  -PublicIpAddress $PublicIP_v4

$frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6

```

### <a name="configure-back-end-address-pool"></a>Configurare il pool di indirizzi back-end

Creare un pool di indirizzi back-end con [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig). Le macchine virtuali si collegano a questo pool back-end nei passaggi rimanenti. L'esempio seguente crea i pool di indirizzi back-end denominati *dsLbBackEndPool_v4* e *dsLbBackEndPool_v6* per includere le macchine virtuali con configurazioni IPV4 e IPv6 NIC:

```azurepowershell-interactive
$backendPoolv4 = New-AzLoadBalancerBackendAddressPoolConfig `
-Name "dsLbBackEndPool_v4"

$backendPoolv6 = New-AzLoadBalancerBackendAddressPoolConfig `
-Name "dsLbBackEndPool_v6"
```

### <a name="create-a-load-balancer-rule"></a>Creare una regola di bilanciamento del carico

Una regola di bilanciamento del carico consente di definire come il traffico verrà distribuito alle VM. Definire la configurazione IP front-end per il traffico in ingresso e il pool IP back-end che riceve il traffico, insieme alle porte di origine e di destinazione necessarie. Per rendere integro assicurarsi che solo le macchine virtuali ricevono il traffico, è possibile definire un probe di integrità. Servizio di bilanciamento del carico di base usa un probe IPv4 per valutare l'integrità per gli endpoint IPv4 e IPv6 nelle macchine virtuali. Load balancer standard include supporto per IPv6 in modo esplicito i probe di integrità.

Creare una regola di bilanciamento del carico con [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig). L'esempio seguente crea regole di bilanciamento del carico denominate *dsLBrule_v4* e *dsLBrule_v6* e bilancia il traffico sul *TCP* porta *80* per le configurazioni di IP front-end IPv4 e IPv6:

```azurepowershell-interactive
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

Creare l'istanza di Load Balancer Basic con [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). L'esempio seguente crea un pubblico Basic Load Balancer denominato *myLoadBalancer* usando l'indirizzo IP front-end IPv4 e IPv6 configurazioni, i pool back-end e le regole di bilanciamento del carico creato nei passaggi precedenti:

```azurepowershell-interactive
$lb = New-AzLoadBalancer `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "MyLoadBalancer" `
-Sku "Basic" `
-FrontendIpConfiguration $frontendIPv4,$frontendIPv6 `
-BackendAddressPool $backendPoolv4,$backendPoolv6 `
-LoadBalancingRule $lbrule_v4,$lbrule_v6

```

## <a name="create-network-resources"></a>Creare risorse di rete
Prima di distribuire alcune macchine virtuali e testare il servizio di bilanciamento, è necessario creare risorse di rete supporto - set di disponibilità, il gruppo di sicurezza di rete, reti virtuali e schede di rete virtuale. 
### <a name="create-an-availability-set"></a>Creare un set di disponibilità
Per aumentare la disponibilità elevata dell'app, posizionare le macchine virtuali in un set di disponibilità.

Creare un set di disponibilità con [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset). L'esempio seguente crea un set di disponibilità denominato *myAvailabilitySet*:

```azurepowershell-interactive
$avset = New-AzAvailabilitySet `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsAVset" `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2 `
  -Sku aligned
```

### <a name="create-network-security-group"></a>Creare un gruppo di sicurezza di rete

Creare un gruppo di sicurezza di rete per le regole che prevarranno le comunicazioni in ingresso e in uscita nella rete virtuale.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Creare una regola del gruppo di sicurezza di rete per la porta 3389

Per consentire le connessioni RDP tramite la porta 3389, creare una regola del gruppo di sicurezza di rete con [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell-interactive
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

Creare una regola di gruppo di sicurezza di rete per consentire le connessioni internet attraverso la porta 80 con [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

```azurepowershell-interactive
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

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "dsNSG1"  `
-SecurityRules $rule1,$rule2
```
### <a name="create-a-virtual-network"></a>Crea rete virtuale

Creare una rete virtuale con [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Nell'esempio seguente viene creata una rete virtuale denominata *myVnet* con una subnet denominata *mySubnet*:

```azurepowershell-interactive
# Create dual stack subnet
$subnet = New-AzVirtualNetworkSubnetConfig `
-Name "dsSubnet" `
-AddressPrefix "10.0.0.0/24","ace:cab:deca:deed::/64"

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsVnet" `
  -AddressPrefix "10.0.0.0/16","ace:cab:deca::/48"  `
  -Subnet $subnet
```

### <a name="create-nics"></a>Creare NIC

Creare schede di rete virtuale con [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). L'esempio seguente crea due NIC virtuali entrambi con configurazioni IPv4 e IPv6. Una scheda di interfaccia di rete virtuale per ogni VM creata per l'app nei passaggi successivi.

```azurepowershell-interactive
  $Ip4Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp4Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv4 `
    -LoadBalancerBackendAddressPool $backendPoolv4 `
    -PublicIpAddress  $RdpPublicIP_1
    
  $Ip6Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp6Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv6 `
    -LoadBalancerBackendAddressPool $backendPoolv6
    
  $NIC_1 = New-AzNetworkInterface `
    -Name "dsNIC1" `
    -ResourceGroupName $rg.ResourceGroupName `
    -Location $rg.Location  `
    -NetworkSecurityGroupId $nsg.Id `
    -IpConfiguration $Ip4Config,$Ip6Config 
    
  $Ip4Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp4Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv4 `
    -LoadBalancerBackendAddressPool $backendPoolv4 `
    -PublicIpAddress  $RdpPublicIP_2  

  $NIC_2 = New-AzNetworkInterface `
    -Name "dsNIC2" `
    -ResourceGroupName $rg.ResourceGroupName `
    -Location $rg.Location  `
    -NetworkSecurityGroupId $nsg.Id `
    -IpConfiguration $Ip4Config,$Ip6Config 

```

### <a name="create-virtual-machines"></a>Creare macchine virtuali

Impostare nome utente e password dell'amministratore delle macchine virtuali con il comando [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = get-credential -Message "DUAL STACK VNET SAMPLE:  Please enter the Administrator credential to log into the VMs."
```

A questo punto è possibile creare le VM con [New-AzVM](/powershell/module/az.compute/new-azvm). Nell'esempio seguente vengono creare due macchine virtuali e i componenti di rete virtuale necessari, se non esistono già. 

```azurepowershell-interactive
$vmsize = "Standard_A2"
$ImagePublisher = "MicrosoftWindowsServer"
$imageOffer = "WindowsServer"
$imageSKU = "2016-Datacenter"

$vmName= "dsVM1"
$VMconfig1 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_1.Id  3> $null 
$VM1 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig1 

$vmName= "dsVM2"
$VMconfig2 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_2.Id  3> $null 
$VM2 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig2
```

## <a name="determine-ip-addresses-of-the-ipv4-and-ipv6-endpoints"></a>Determinare gli indirizzi IP degli endpoint IPv4 e IPv6
Ottenere tutti gli oggetti di interfaccia di rete nel gruppo di risorse per riepilogare l'indirizzo IP usato in questa distribuzione con `get-AzNetworkInterface`. Ottieni anche indirizzi front-end di Load Balancer con gli endpoint IPv4 e IPv6 `get-AzpublicIpAddress`.

```azurepowershell-interactive
$rgName= "dsRG1"
$NICsInRG= get-AzNetworkInterface -resourceGroupName $rgName 
write-host `nSummary of IPs in this Deployment: 
write-host ******************************************
foreach ($NIC in $NICsInRG) {
 
    $VMid= $NIC.virtualmachine.id 
    $VMnamebits= $VMid.split("/") 
    $VMname= $VMnamebits[($VMnamebits.count-1)] 
    write-host `nPrivate IP addresses for $VMname 
    $IPconfigsInNIC= $NIC.IPconfigurations 
    foreach ($IPconfig in $IPconfigsInNIC) {
 
        $IPaddress= $IPconfig.privateipaddress 
        write-host "    "$IPaddress 
        IF ($IPconfig.PublicIpAddress.ID) {
 
            $IDbits= ($IPconfig.PublicIpAddress.ID).split("/")
            $PipName= $IDbits[($IDbits.count-1)]
            $PipObject= get-azPublicIpAddress -name $PipName -resourceGroup $rgName
            write-host "    "RDP address:  $PipObject.IpAddress
                 }
         }
 }
 
 
 
  write-host `nPublic IP addresses on Load Balancer:
 
  (get-AzpublicIpAddress -resourcegroupname $rgName | where { $_.name -notlike "RdpPublicIP*" }).IpAddress
```
La figura seguente mostra un esempio di output che elenca gli indirizzi IPv4 e IPv6 privati delle due macchine virtuali e gli indirizzi IP IPv4 e IPv6 di front-end del bilanciamento del carico.

![Riepilogo IP dual stack (IPv4 e IPv6) di distribuzione dell'applicazione in Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-application-summary.png)

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Rete virtuale dual-stack IPv6 di visualizzazione nel portale di Azure
È possibile visualizzare la rete virtuale dual-stack IPv6 nel portale di Azure come indicato di seguito:
1. Nella barra di ricerca del portale, immettere *dsVnet*.
2. Selezionare **myVirtualNetwork** quando viene visualizzato nei risultati della ricerca. Verrà avviata il **Overview** pagina della rete virtuale dual-stack denominata *dsVnet*. La rete virtuale dual-stack Mostra due schede di rete con IPv4 e IPv6 configurazioni che si trova nella subnet dual-stack denominata *dsSubnet*.

  ![Rete virtuale di dual-stack IPv6 in Azure](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)

> [!NOTE]
> IPv6 per la rete virtuale di Azure è disponibile nel portale di Azure in sola lettura per questa versione di anteprima.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile usare il comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```azurepowershell-interactive
Remove-AzResourceGroup -Name dsRG1
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è creato un servizio Load Balancer Basic con una configurazione IP front-end dual (IPv4 e IPv6). È stato creato anche un due macchine virtuali incluse interfacce di rete con due configurazioni IP (IPV4 + IPv6) che sono stati aggiunti al pool back-end del servizio di bilanciamento del carico. Per altre informazioni sul supporto IPv6 in reti virtuali di Azure, vedere [What ' s IPv6 per la rete virtuale di Azure?](ipv6-overview.md)