---
title: 'Avvio rapido: Creare un servizio di bilanciamento del carico pubblico - Azure PowerShell'
titleSuffix: Azure Load Balancer
description: Questo argomento di avvio rapido mostra come creare un servizio di bilanciamento del carico usando Azure PowerShell
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer so that I can load balance internet traffic to VMs.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/22/2020
ms.author: allensu
ms:custom: seodec18
ms.openlocfilehash: b9bcdbdf8bdad0d1ad96fc043f29a84b962318c2
ms.sourcegitcommit: 5ae2f32951474ae9e46c0d46f104eda95f7c5a06
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2020
ms.locfileid: "95324011"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-using-azure-powershell"></a>Avvio rapido: Creare un servizio di bilanciamento del carico pubblico per le macchine virtuali tramite Azure PowerShell

Iniziare a usare Azure Load Balancer con Azure PowerShell per creare un servizio di bilanciamento del carico pubblico e tre macchine virtuali.

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Installazione di Azure PowerShell in locale o Azure Cloud Shell.

Se si sceglie di installare e usare PowerShell in locale, per questo articolo è necessario il modulo Azure PowerShell 5.4.1 o versione successiva. Eseguire `Get-Module -ListAvailable Az` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-Az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

Creare un gruppo di risorse con [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

```azurepowershell-interactive
New-AzResourceGroup -Name 'CreatePubLBQS-rg' -Location 'eastus'

```
---

# <a name="standard-sku"></a>[**SKU Standard**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Il bilanciamento del carico di SKU Standard è l'impostazione consigliata per i carichi di lavoro di produzione. Per altre informazioni sugli SKU, vedere **[SKU di Azure Load Balancer](skus.md)** .

## <a name="create-a-public-ip-address---standard"></a>Creare un indirizzo IP pubblico - Standard

Usare [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) per creare un indirizzo IP pubblico.

```azurepowershell-interactive
$publicip = @{
    Name = 'myPublicIP'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'static'
}
New-AzPublicIpAddress @publicip

```

Per creare un indirizzo IP pubblico di zona nella zona 1, usare il comando seguente:

```azurepowershell-interactive
$publicip = @{
    Name = 'myPublicIP'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'static'
    Zone = '1'
}
New-AzPublicIpAddress @publicip

```

## <a name="create-standard-load-balancer"></a>Creare un servizio di bilanciamento del carico standard

Questa sezione descrive dettagliatamente come creare e configurare i componenti seguenti del servizio di bilanciamento del carico:

* Creare un IP front-end con [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) per il pool di indirizzi IP front-end. Questo IP riceve il traffico in ingresso nel servizio di bilanciamento del carico

* Creare un pool di indirizzi back-end con [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) per il traffico inviato dal front-end del servizio di bilanciamento del carico. Questo pool è quello in cui vengono distribuite le macchine virtuali back-end.

* Usare [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) per creare un probe di integrità che determina l'integrità delle istanze di VM back-end.

* Usare [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) per creare una regola di bilanciamento del carico che definisce come viene distribuito il traffico alle VM.

* Creare un servizio di bilanciamento del carico pubblico con [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer).


```azurepowershell-interactive
## Place public IP created in previous steps into variable. ##
$publicIp = Get-AzPublicIpAddress -Name 'myPublicIP' -ResourceGroupName 'CreatePubLBQS-rg'

## Create load balancer frontend configuration and place in variable. ##
$feip = New-AzLoadBalancerFrontendIpConfig -Name 'myFrontEnd' -PublicIpAddress $publicIp

## Create backend address pool configuration and place in variable. ##
$bepool = New-AzLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'

## Create the health probe and place in variable. ##
$probe = @{
    Name = 'myHealthProbe'
    Protocol = 'http'
    Port = '80'
    IntervalInSeconds = '360'
    ProbeCount = '5'
    RequestPath = '/'
}
$healthprobe = New-AzLoadBalancerProbeConfig @probe

## Create the load balancer rule and place in variable. ##
$lbrule = @{
    Name = 'myHTTPRule'
    Protocol = 'tcp'
    FrontendPort = '80'
    BackendPort = '80'
    IdleTimeoutInMinutes = '15'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bePool
}
$rule = New-AzLoadBalancerRuleConfig @lbrule -EnableTcpReset -DisableOutboundSNAT

## Create the load balancer resource. ##
$loadbalancer = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Name = 'myLoadBalancer'
    Location = 'eastus'
    Sku = 'Standard'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bePool
    LoadBalancingRule = $rule
    Probe = $healthprobe
}
New-AzLoadBalancer @loadbalancer

```

## <a name="configure-virtual-network---standard"></a>Configurare la rete virtuale - Standard

Prima di distribuire le macchine virtuali e testare il servizio di bilanciamento del carico, creare le risorse di rete virtuale di supporto.

Creare una rete virtuale per le macchine virtuali back-end.

Creare un gruppo di sicurezza di rete per definire le connessioni in ingresso alla rete virtuale.

### <a name="create-virtual-network-network-security-group-and-bastion-host"></a>Creare una rete virtuale, un gruppo di sicurezza di rete e un host bastion

* Creare una rete virtuale con [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork).

* Creare una regola di gruppo di sicurezza di rete con [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

* Creare un host Azure Bastion con [New-AzBastion](/powershell/module/az.network/new-azbastion).

* Creare un gruppo di sicurezza di rete con [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

```azurepowershell-interactive
## Create backend subnet config ##
$subnet = @{
    Name = 'myBackendSubnet'
    AddressPrefix = '10.0.0.0/24'
}
$subnetConfig = New-AzVirtualNetworkSubnetConfig @subnet 

## Create Azure Bastion subnet. ##
$bastsubnet = @{
    Name = 'AzureBastionSubnet' 
    AddressPrefix = '10.0.1.0/24'
}
$bastsubnetConfig = New-AzVirtualNetworkSubnetConfig @bastsubnet

## Create the virtual network ##
$net = @{
    Name = 'myVNet'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    AddressPrefix = '10.0.0.0/16'
    Subnet = $subnetConfig,$bastsubnetConfig
}
$vnet = New-AzVirtualNetwork @net

## Create public IP address for bastion host. ##
$ip = @{
    Name = 'myBastionIP'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicip = New-AzPublicIpAddress @ip

## Create bastion host ##
$bastion = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Name = 'myBastion'
    PublicIpAddress = $publicip
    VirtualNetwork = $vnet
}
New-AzBastion @bastion -AsJob

## Create rule for network security group and place in variable. ##
$nsgrule = @{
    Name = 'myNSGRuleHTTP'
    Description = 'Allow HTTP'
    Protocol = '*'
    SourcePortRange = '*'
    DestinationPortRange = '80'
    SourceAddressPrefix = 'Internet'
    DestinationAddressPrefix = '*'
    Access = 'Allow'
    Priority = '2000'
    Direction = 'Inbound'
}
$rule1 = New-AzNetworkSecurityRuleConfig @nsgrule

## Create network security group ##
$nsg = @{
    Name = 'myNSG'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    SecurityRules = $rule1
}
New-AzNetworkSecurityGroup @nsg

```

## <a name="create-virtual-machines---standard"></a>Creare macchine virtuali - Standard

In questa sezione verranno create tre macchine virtuali per il pool back-end del servizio di bilanciamento del carico.

* Creare tre interfacce di rete con [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface).

* Impostare nome utente e password dell'amministratore delle macchine virtuali con il comando [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential).

* Creare le macchine virtuali con:
    * [New-AzVM](/powershell/module/az.compute/new-azvm)
    * [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
    * [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
    * [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
    * [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)

```azurepowershell-interactive
# Set the administrator and password for the VMs. ##
$cred = Get-Credential

## Place the virtual network into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'CreatePubLBQS-rg'

## Place the load balancer into a variable. ##
$lb = @{
    Name = 'myLoadBalancer'
    ResourceGroupName = 'CreatePubLBQS-rg'
}
$bepool = Get-AzLoadBalancer @lb  | Get-AzLoadBalancerBackendAddressPoolConfig

## Place the network security group into a variable. ##
$nsg = Get-AzNetworkSecurityGroup -Name 'myNSG' -ResourceGroupName 'CreatePubLBQS-rg'

## For loop with variable to create virtual machines for load balancer backend pool. ##
for ($i=1; $i -le 3; $i++)
{
## Command to create network interface for VMs ##
$nic = @{
    Name = "myNicVM$i"
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    Subnet = $vnet.Subnets[0]
    NetworkSecurityGroup = $nsg
    LoadBalancerBackendAddressPool = $bepool
}
$nicVM = New-AzNetworkInterface @nic

## Create a virtual machine configuration for VMs ##
$vmsz = @{
    VMName = "myVM$i"
    VMSize = 'Standard_DS1_v2'  
}
$vmos = @{
    ComputerName = "myVM$i"
    Credential = $cred
}
$vmimage = @{
    PublisherName = 'MicrosoftWindowsServer'
    Offer = 'WindowsServer'
    Skus = '2019-Datacenter'
    Version = 'latest'    
}
$vmConfig = New-AzVMConfig @vmsz `
    | Set-AzVMOperatingSystem @vmos -Windows `
    | Set-AzVMSourceImage @vmimage `
    | Add-AzVMNetworkInterface -Id $nicVM.Id

## Create the virtual machine for VMs ##
$vm = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    VM = $vmConfig
    Zone = "$i"
}
New-AzVM @vm -AsJob
}

```

Le distribuzioni delle macchine virtuali e dell'host bastion vengono inviate come processi di PowerShell. Per visualizzare lo stato dei processi, usare [Get-Job](/powershell/module/microsoft.powershell.core/get-job):

```azurepowershell-interactive
Get-Job

Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzBastion
2      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzVM
3      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzVM
4      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzVM
```

## <a name="create-outbound-rule-configuration"></a>Creare la configurazione delle regole in uscita
Le regole in uscita del servizio di bilanciamento del carico configurano la funzionalità SNAT (Source Network Address Translation) per le macchine virtuali nel pool back-end. 

Per altre informazioni sulle connessioni in uscita, vedere [Connessioni in uscita in Azure](load-balancer-outbound-connections.md).

### <a name="create-outbound-public-ip-address"></a>Creare un indirizzo IP pubblico in uscita

Usare [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) per creare un indirizzo IP pubblico con ridondanza della zona denominato **myPublicIPOutbound**.

```azurepowershell-interactive
$publicipout = @{
    Name = 'myPublicIPOutbound'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'static'
}
New-AzPublicIpAddress @publicipout

```

Per creare un indirizzo IP pubblico di zona nella zona 1, usare il comando seguente:

```azurepowershell-interactive
$publicipout = @{
    Name = 'myPublicIPOutbound'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'static'
    Zone = '1'
}
New-AzPublicIpAddress @publicipout

```

### <a name="create-outbound-configuration"></a>Creare la configurazione in uscita

* Creare una nuova configurazione IP front-end con [Add-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/add-azloadbalancerfrontendipconfig).

* Creare un nuovo pool in uscita con [Add-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/add-azloadbalancerbackendaddresspoolconfig). 

* Applicare il pool e l'indirizzo IP front-end al servizio di bilanciamento del carico con [Set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer).
*  Creare una nuova regola in uscita per il pool back-end in uscita con [Add-AzLoadBalancerOutboundRuleConfig](/powershell/module/az.network/new-azloadbalanceroutboundruleconfig). 

```azurepowershell-interactive
## Place public IP created in previous steps into variable. ##
$pubip = @{
    Name = 'myPublicIPOutbound'
    ResourceGroupName = 'CreatePubLBQS-rg'
}
$publicIp = Get-AzPublicIpAddress @pubip

## Get the load balancer configuration ##
$lbc = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Name = 'myLoadBalancer'
}
$lb = Get-AzLoadBalancer @lbc

## Create the frontend configuration ##
$fe = @{
    Name = 'myFrontEndOutbound'
    PublicIPAddress = $publicIP
}
$lb | Add-AzLoadBalancerFrontendIPConfig @fe | Set-AzLoadBalancer

## Create the outbound backend address pool ##
$be = @{
    Name = 'myBackEndPoolOutbound'
}
$lb | Add-AzLoadBalancerBackendAddressPoolConfig @be | Set-AzLoadBalancer

## Apply the outbound rule configuration to the load balancer. ##
$rule = @{
    Name = 'myOutboundRule'
    AllocatedOutboundPort = '10000'
    Protocol = 'All'
    IdleTimeoutInMinutes = '15'
    FrontendIPConfiguration = $lb.FrontendIpConfigurations[1]
    BackendAddressPool = $lb.BackendAddressPools[1]
}
$lb | Add-AzLoadBalancerOutBoundRuleConfig @rule | Set-AzLoadBalancer

```

### <a name="add-virtual-machines-to-outbound-pool"></a>Aggiungere le macchine virtuali al pool in uscita

Aggiungere le interfacce di rete delle macchine virtuali al pool in uscita del servizio di bilanciamento del carico con [Add-AzNetworkInterfaceIpConfig](/powershell/module/az.network/add-aznetworkinterfaceipconfig):

```azurepowershell-interactive
## Get the load balancer configuration ##
$lbc = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Name = 'myLoadBalancer'
}
$lb = Get-AzLoadBalancer @lbc

# For loop with variable to add virtual machines to backend outbound pool. ##
for ($i=1; $i -le 3; $i++)
{
$nic = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Name = "myNicVM$i"
}
$nicvm = Get-AzNetworkInterface @nic

## Apply the backend to the network interface ##
$be = @{
    Name = 'ipconfig1'
    LoadBalancerBackendAddressPoolId = $lb.BackendAddressPools[0].id,$lb.BackendAddressPools[1].id
}
$nicvm | Set-AzNetworkInterfaceIpConfig @be | Set-AzNetworkInterface
}

```

# <a name="basic-sku"></a>[**SKU Basic**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Il bilanciamento del carico di SKU Standard è l'impostazione consigliata per i carichi di lavoro di produzione. Per altre informazioni sugli SKU, vedere **[SKU di Azure Load Balancer](skus.md)** .

## <a name="create-a-public-ip-address---basic"></a>Creare un indirizzo IP pubblico - Basic

Usare [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) per creare un indirizzo IP pubblico.

```azurepowershell-interactive
$publicip = @{
    Name = 'myPublicIP'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    Sku = 'Basic'
    AllocationMethod = 'static'
}
New-AzPublicIpAddress @publicip

```

## <a name="create-basic-load-balancer"></a>Creare un servizio di bilanciamento del carico di base

Questa sezione descrive dettagliatamente come creare e configurare i componenti seguenti del servizio di bilanciamento del carico:

* Creare un IP front-end con [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) per il pool di indirizzi IP front-end. Questo IP riceve il traffico in ingresso nel servizio di bilanciamento del carico

* Creare un pool di indirizzi back-end con [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) per il traffico inviato dal front-end del servizio di bilanciamento del carico. Questo pool è quello in cui vengono distribuite le macchine virtuali back-end.

* Usare [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) per creare un probe di integrità che determina l'integrità delle istanze di VM back-end.

* Usare [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) per creare una regola di bilanciamento del carico che definisce come viene distribuito il traffico alle VM.

* Creare un servizio di bilanciamento del carico pubblico con [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer).

```azurepowershell-interactive
## Place public IP created in previous steps into variable. ##
$publicIp = Get-AzPublicIpAddress -Name 'myPublicIP' -ResourceGroupName 'CreatePubLBQS-rg'

## Create load balancer frontend configuration and place in variable. ##
$feip = New-AzLoadBalancerFrontendIpConfig -Name 'myFrontEnd' -PublicIpAddress $publicIp

## Create backend address pool configuration and place in variable. ##
$bepool = New-AzLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'

## Create the health probe and place in variable. ##
$probe = @{
    Name = 'myHealthProbe'
    Protocol = 'http'
    Port = '80'
    IntervalInSeconds = '360'
    ProbeCount = '5'
    RequestPath = '/'
}
$healthprobe = New-AzLoadBalancerProbeConfig @probe

## Create the load balancer rule and place in variable. ##
$lbrule = @{
    Name = 'myHTTPRule'
    Protocol = 'tcp'
    FrontendPort = '80'
    BackendPort = '80'
    IdleTimeoutInMinutes = '15'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bePool
}
$rule = New-AzLoadBalancerRuleConfig @lbrule

## Create the load balancer resource. ##
$loadbalancer = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Name = 'myLoadBalancer'
    Location = 'eastus'
    Sku = 'Basic'
    FrontendIpConfiguration = $feip
    BackendAddressPool = $bePool
    LoadBalancingRule = $rule
    Probe = $healthprobe
}
New-AzLoadBalancer @loadbalancer

```

## <a name="configure-virtual-network---basic"></a>Configurare la rete virtuale - Basic

Prima di distribuire le macchine virtuali e testare il servizio di bilanciamento del carico, creare le risorse di rete virtuale di supporto.

Creare una rete virtuale per le macchine virtuali back-end.

Creare un gruppo di sicurezza di rete per definire le connessioni in ingresso alla rete virtuale.

### <a name="create-virtual-network-network-security-group-and-bastion-host"></a>Creare una rete virtuale, un gruppo di sicurezza di rete e un host bastion

* Creare una rete virtuale con [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork).

* Creare una regola di gruppo di sicurezza di rete con [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig).

* Creare un host Azure Bastion con [New-AzBastion](/powershell/module/az.network/new-azbastion).

* Creare un gruppo di sicurezza di rete con [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

```azurepowershell-interactive
## Create backend subnet config ##
$subnet = @{
    Name = 'myBackendSubnet'
    AddressPrefix = '10.0.0.0/24'
}
$subnetConfig = New-AzVirtualNetworkSubnetConfig @subnet 

## Create Azure Bastion subnet. ##
$bastsubnet = @{
    Name = 'AzureBastionSubnet' 
    AddressPrefix = '10.0.1.0/24'
}
$bastsubnetConfig = New-AzVirtualNetworkSubnetConfig @bastsubnet

## Create the virtual network ##
$net = @{
    Name = 'myVNet'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    AddressPrefix = '10.0.0.0/16'
    Subnet = $subnetConfig,$bastsubnetConfig
}
$vnet = New-AzVirtualNetwork @net

## Create public IP address for bastion host. ##
$ip = @{
    Name = 'myBastionIP'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicip = New-AzPublicIpAddress @ip

## Create bastion host ##
$bastion = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Name = 'myBastion'
    PublicIpAddress = $publicip
    VirtualNetwork = $vnet
}
New-AzBastion @bastion -AsJob

## Create rule for network security group and place in variable. ##
$nsgrule = @{
    Name = 'myNSGRuleHTTP'
    Description = 'Allow HTTP'
    Protocol = '*'
    SourcePortRange = '*'
    DestinationPortRange = '80'
    SourceAddressPrefix = 'Internet'
    DestinationAddressPrefix = '*'
    Access = 'Allow'
    Priority = '2000'
    Direction = 'Inbound'
}
$rule1 = New-AzNetworkSecurityRuleConfig @nsgrule

## Create network security group ##
$nsg = @{
    Name = 'myNSG'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    SecurityRules = $rule1
}
New-AzNetworkSecurityGroup @nsg

```

## <a name="create-virtual-machines---basic"></a>Creare le macchine virtuali - Basic

In questa sezione verranno create le macchine virtuali per il pool back-end del servizio di bilanciamento del carico.

* Creare tre interfacce di rete con [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface).

* Impostare nome utente e password dell'amministratore delle macchine virtuali con il comando [Get-Credential](/powershell/reference/5.1/microsoft.powershell.security/Get-Credential).

* Usare [New-AzAvailabilitySet](/powershell/module/az.compute/new-azvm) per creare un set di disponibilità per le macchine virtuali.

* Creare le macchine virtuali con:
    * [New-AzVM](/powershell/module/az.compute/new-azvm)
    * [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
    * [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
    * [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
    * [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)

```azurepowershell-interactive
# Set the administrator and password for the VMs. ##
$cred = Get-Credential

## Place the virtual network into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'CreatePubLBQS-rg'

## Place the load balancer into a variable. ##
$lb = @{
    Name = 'myLoadBalancer'
    ResourceGroupName = 'CreatePubLBQS-rg'
}
$bepool = Get-AzLoadBalancer @lb  | Get-AzLoadBalancerBackendAddressPoolConfig

## Place the network security group into a variable. ##
$nsg = Get-AzNetworkSecurityGroup -Name 'myNSG' -ResourceGroupName 'CreatePubLBQS-rg'

## Create availability set for the virtual machines. ##
$set = @{
    Name = 'myAvSet'
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    Sku = 'Aligned'
    PlatformFaultDomainCount = '2'
    PlatformUpdateDomainCount =  '2'
}
$avs = New-AzAvailabilitySet @set

## For loop with variable to create virtual machines. ##
for ($i=1; $i -le 3; $i++)
{
## Command to create network interface for VMs ##
$nic = @{
    Name = "myNicVM$i"
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    Subnet = $vnet.Subnets[0]
    NetworkSecurityGroup = $nsg
    LoadBalancerBackendAddressPool = $bepool
}
$nicVM = New-AzNetworkInterface @nic

## Create a virtual machine configuration for VMs ##
$vmsz = @{
    VMName = "myVM$i"
    VMSize = 'Standard_DS1_v2'
    AvailabilitySetId = $avs.Id   
}
$vmos = @{
    ComputerName = "myVM$i"
    Credential = $cred
}
$vmimage = @{
    PublisherName = 'MicrosoftWindowsServer'
    Offer = 'WindowsServer'
    Skus = '2019-Datacenter'
    Version = 'latest'    
}
$vmConfig = New-AzVMConfig @vmsz `
    | Set-AzVMOperatingSystem @vmos -Windows `
    | Set-AzVMSourceImage @vmimage `
    | Add-AzVMNetworkInterface -Id $nicVM.Id

## Create the virtual machine for VMs ##
$vm = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Location = 'eastus'
    VM = $vmConfig
}
New-AzVM @vm -AsJob
}

```

Le distribuzioni delle macchine virtuali e dell'host bastion vengono inviate come processi di PowerShell. Per visualizzare lo stato dei processi, usare [Get-Job](/powershell/module/microsoft.powershell.core/get-job):

```azurepowershell-interactive
Get-Job

Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzBastion
2      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzVM
3      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzVM
4      Long Running O… AzureLongRunni… Completed     True            localhost            New-AzVM
```

---

## <a name="install-iis"></a>Installare IIS

Usare [Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) per installare l'estensione per script personalizzati. 

L'estensione esegue `PowerShell Add-WindowsFeature Web-Server` per installare il server Web IIS e quindi aggiorna la pagina Default.htm per visualizzare il nome host della VM:

> [!IMPORTANT]
> Assicurarsi che le distribuzioni delle macchine virtuali siano state completate nel passaggio precedente prima di procedere.  Usare `Get-Job` per verificare lo stato dei processi di distribuzione delle macchine virtuali.

```azurepowershell-interactive
## For loop with variable to install custom script extension on virtual machines. ##
for ($i=1; $i -le 3; $i++)
{
$ext = @{
    Publisher = 'Microsoft.Compute'
    ExtensionType = 'CustomScriptExtension'
    ExtensionName = 'IIS'
    ResourceGroupName = 'CreatePubLBQS-rg'
    VMName = "myVM$i"
    Location = 'eastus'
    TypeHandlerVersion = '1.8'
    SettingString = '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
}
Set-AzVMExtension @ext -AsJob
}
```

Le estensioni vengono distribuite come processi di PowerShell. Per visualizzare lo stato dei processi di installazione, usare [Get-Job](/powershell/module/microsoft.powershell.core/get-job):


```azurepowershell-interactive
Get-Job

Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
8      Long Running O… AzureLongRunni… Running       True            localhost            Set-AzVMExtension
9      Long Running O… AzureLongRunni… Running       True            localhost            Set-AzVMExtension
10     Long Running O… AzureLongRunni… Running       True            localhost            Set-AzVMExtension
```

## <a name="test-the-load-balancer"></a>Testare il servizio di bilanciamento del carico

Usare [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) per ottenere l'indirizzo IP pubblico del servizio di bilanciamento del carico:

```azurepowershell-interactive
$ip = @{
    ResourceGroupName = 'CreatePubLBQS-rg'
    Name = 'myPublicIP'
}  
Get-AzPublicIPAddress @ip | select IpAddress

```

Copiare l'indirizzo IP pubblico e quindi incollarlo nella barra degli indirizzi del browser. Nel browser verrà visualizzata la pagina predefinita del server Web IIS.

   ![Server Web IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Per visualizzare il servizio di bilanciamento del carico distribuire il traffico tra tutte e tre macchine virtuali, è possibile personalizzare la pagina predefinita del server Web IIS di ciascuna macchina virtuale e quindi forzare l'aggiornamento del Web browser dal computer client.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile usare il comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) per rimuovere il gruppo di risorse, il servizio di bilanciamento del carico e le risorse rimanenti.

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'CreatePubLBQS-rg'

```

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido:

* È stato creato un servizio di bilanciamento del carico standard o pubblico di base.
* Sono state collegate le macchine virtuali. 
* Sono stati configurati la regola del traffico di bilanciamento del carico e il probe di integrità.
* È stato testato il servizio di bilanciamento del carico.

Per altre informazioni su Azure Load Balancer, passare a:
> [!div class="nextstepaction"]
> [Informazioni su Azure Load Balancer](load-balancer-overview.md)