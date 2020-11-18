---
title: 'Avvio rapido: Creare un servizio di bilanciamento del carico interno - Azure PowerShell'
titleSuffix: Azure Load Balancer
description: Questo argomento di avvio rapido illustra come creare un servizio di bilanciamento del carico interno con Azure PowerShell
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer so that I can load balance internal traffic to VMs.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/27/2020
ms.author: allensu
ms:custom: seodec18
ms.openlocfilehash: e1fa97fb8a3dcae3a78ba0bc85cf59db2c167dea
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94696779"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-azure-powershell"></a>Avvio rapido: Creare un servizio di bilanciamento del carico interno per le macchine virtuali con Azure PowerShell

Iniziare a usare Azure Load Balancer con Azure PowerShell per creare un servizio di bilanciamento del carico interno e due macchine virtuali.

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Installazione di Azure PowerShell in locale o Azure Cloud Shell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare PowerShell in locale, per questo articolo è necessario il modulo Azure PowerShell 5.4.1 o versione successiva. Eseguire `Get-Module -ListAvailable Az` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-Az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

Creare un gruppo di risorse con [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

* Con nome **CreateIntLBQS-rg**.
* Nella posizione **eastus**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'

New-AzResourceGroup -Name $rg -Location $loc
```
---

# <a name="standard-sku"></a>[**SKU Standard**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Il bilanciamento del carico di SKU Standard è l'impostazione consigliata per i carichi di lavoro di produzione. Per altre informazioni sugli SKU, vedere **[SKU di Azure Load Balancer](skus.md)** .

## <a name="configure-virtual-network"></a>Configurare la rete virtuale

Prima di distribuire le macchine virtuali e testare il servizio di bilanciamento del carico, creare le risorse di rete virtuale di supporto.

### <a name="create-a-virtual-network-and-azure-bastion-host"></a>Creare una rete virtuale e un host di Azure Bastion

Creare una rete virtuale con [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork):

* Denominata **myVNet**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Con una subnet denominata **myBackendSubnet**.
* Rete virtuale **10.0.0.0/16**.
* Subnet **10.0.0.0/24**.
* Subnet denominata **AzureBastionSubnet**.
* Subnet **10.0.1.0/24**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$sub = 'myBackendSubnet'
$spfx = '10.0.0.0/24'
$vnm = 'myVNet'
$vpfx = '10.0.0.0/16'
$bsub = 'AzureBastionSubnet'
$bpfx = '10.0.1.0/24'


## Create backend subnet config ##
$subnetConfig = 
New-AzVirtualNetworkSubnetConfig -Name $sub -AddressPrefix $spfx

## Create Azure Bastion subnet 
$bassubConfig =
New-AzVirtualNetworkSubnetConfig -Name $bsub -AddressPrefix $bpfx

## Create the virtual network ##
$vnet = 
New-AzVirtualNetwork -ResourceGroupName $rg -Location $loc -Name $vnm -AddressPrefix $vpfx -Subnet $subnetConfig,$bassubConfig
```

### <a name="create-public-ip-address-for-azure-bastion-host"></a>Creare un indirizzo IP pubblico per l'host Azure Bastion

Usare [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) per creare un indirizzo IP pubblico per l'host bastion:

* Denominato **myPublicIPBastion**
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Nella posizione **eastus**.
* Metodo di allocazione **static**.
* SKU **Standard**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$ipn = 'myPublicIPBastion'
$all = 'static'
$sku = 'standard'

$publicip = 
New-AzPublicIpAddress -ResourceGroupName $rg -Location $loc -Name $ipn -AllocationMethod $all -Sku $sku
```

### <a name="create-azure-bastion-host"></a>Creare l'host Azure Bastion

Usare [New-AzBastion](/powershell/module/az.network/new-azbastion) per creare un host bastion:

* Denominato **myBastion**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Nella rete virtuale **myVNet**.
* Associato all'indirizzo IP pubblico **myPublicIPBastion**.

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'CreateIntLBQS-rg'
$nmn = 'myBastion'

## Command to create bastion host. $vnet and $publicip are from the previous steps ##
New-AzBastion -ResourceGroupName $rg -Name $nmn -PublicIpAddress $publicip -VirtualNetwork $vnet

```

La distribuzione dell'host Azure Bastion può richiedere qualche minuto.

### <a name="create-network-security-group"></a>Creare un gruppo di sicurezza di rete
Creare un gruppo di sicurezza di rete per definire le connessioni in ingresso alla rete virtuale.

#### <a name="create-a-network-security-group-rule-for-port-80"></a>Creare una regola del gruppo di sicurezza di rete per la porta 80
Creare una regola di gruppo di sicurezza di rete con [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig):

* Denominata **myNSGRuleHTTP**.
* Descrizione **Allow HTTP**.
* Accesso **Allow**.
* Protocollo **(*)** .
* Direzione **Inbound**.
* Priorità **2000**.
* Origine **Internet**.
* Intervallo di porte di origine **(*)** .
* Prefisso dell'indirizzo di destinazione **(*)** .
* Porta di destinazione **80**.

```azurepowershell-interactive
## Variables for command ##
$rnm = 'myNSGRuleHTTP'
$des = 'Allow HTTP'
$acc = 'Allow'
$pro = '*'
$dir = 'Inbound'
$pri = '2000'
$spfx = 'Internet'
$spr = '*'
$dpfx = '*'
$dpr = '80'

$rule1 = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $des -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix $spfx -SourcePortRange $spr -DestinationAddressPrefix $dpfx -DestinationPortRange $dpr
```

#### <a name="create-a-network-security-group"></a>Creare un gruppo di sicurezza di rete

Creare un gruppo di sicurezza di rete con [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup):

* Denominato **myNSG**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Nella posizione **eastus**.
* Con le regole di sicurezza create nei passaggi precedenti archiviati in una variabile.

```azurepowershell
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nmn = 'myNSG'

## $rule1 contains configuration information from the previous steps. ##
$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg -Location $loc -Name $nmn -SecurityRules $rule1
```

## <a name="create-standard-load-balancer"></a>Creare un servizio di bilanciamento del carico standard

Questa sezione descrive dettagliatamente come creare e configurare i componenti seguenti del servizio di bilanciamento del carico:

  * Un pool IP front-end che riceve il traffico di rete in ingresso sul servizio di bilanciamento del carico.
  * Un pool IP back-end a cui il pool front-end invia il traffico di rete con carico bilanciato.
  * Un probe di integrità che determina l'integrità delle istanze delle macchine virtuali back-end.
  * Una regola di bilanciamento del carico che definisce come verrà distribuito il traffico alle macchine virtuali.

### <a name="create-frontend-ip"></a>Creare l'indirizzo IP front-end

Creare un IP front-end con [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig):

* Denominato **myFrontEnd**.
* Indirizzo IP privato **10.0.0.4**.

```azurepowershell-interactive
## Variables for the commands ##
$fe = 'myFrontEnd'
$rg = 'CreateIntLBQS-rg'
$ip = '10.0.0.4'

## Command to create frontend configuration. The variable $vnet is from the previous commands. ##
$feip = 
New-AzLoadBalancerFrontendIpConfig -Name $fe -PrivateIpAddress $ip -SubnetId $vnet.subnets[0].Id
```

### <a name="configure-back-end-address-pool"></a>Configurare il pool di indirizzi back-end

Creare un pool di indirizzi back-end con [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig): 

* Denominarlo **myBackendPool**.
* Le macchine virtuali si collegano a questo pool back-end nei passaggi rimanenti.

```azurepowershell-interactive
## Variable for the command ##
$be = 'myBackEndPool'

$bepool = 
New-AzLoadBalancerBackendAddressPoolConfig -Name $be
```

### <a name="create-the-health-probe"></a>Creare il probe di integrità

Un probe di integrità controlla tutte le istanze di macchine virtuali per verificare che possano inviare traffico di rete. 

Una macchina virtuale con un controllo probe non riuscito viene rimossa dal servizio di bilanciamento del carico. La macchina virtuale viene nuovamente aggiunta al servizio di bilanciamento del carico quando l'errore viene risolto.

Creare un probe di integrità con [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig):

* Esegue il monitoraggio dello stato delle macchine virtuali.
* Denominato **myHealthProbe**.
* Protocollo **TCP**.
* Porta **80** per il monitoraggio.

```azurepowershell-interactive
## Variables for the command ##
$hp = 'myHealthProbe'
$pro = 'http'
$port = '80'
$int = '360'
$cnt = '5'

$probe = 
New-AzLoadBalancerProbeConfig -Name $hp -Protocol $pro -Port $port -RequestPath / -IntervalInSeconds $int -ProbeCount $cnt
```

### <a name="create-the-load-balancer-rule"></a>Creare la regola di bilanciamento del carico

Una regola di bilanciamento del carico definisce:

* La configurazione IP front-end per il traffico in ingresso.
* Il pool IP back-end in cui ricevere il traffico.
* La porta di origine e destinazione richiesta. 

Creare una regola di bilanciamento del carico con [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig): 

* Denominata **myHTTPRule**.
* In attesa sulla porta **80** nel pool front-end **myFrontEnd**.
* Per l'invio del traffico di rete con carico bilanciato al pool di indirizzi back-end **myBackEndPool** tramite la porta **80**. 
* Che usa il probe di integrità **myHealthProbe**.
* Protocollo **TCP**.
* Timeout di inattività di **15 minuti**.
* Abilitare la reimpostazione TCP.

```azurepowershell-interactive
## Variables for the command ##
$lbr = 'myHTTPRule'
$pro = 'tcp'
$port = '80'
$idl = '15'

## $feip and $bePool are the variables from previous steps. ##

$rule = 
New-AzLoadBalancerRuleConfig -Name $lbr -Protocol $pro -Probe $probe -FrontendPort $port -BackendPort $port -FrontendIpConfiguration $feip -BackendAddressPool $bePool -DisableOutboundSNAT -IdleTimeoutInMinutes $idl -EnableTcpReset
```
>[!NOTE]
>Le macchine virtuali nel pool back-end non avranno connettività Internet in uscita con questa configurazione. </br> Per altre informazioni su come fornire la connettività in uscita, vedere: </br> **[Connessioni in uscita in Azure](load-balancer-outbound-connections.md)**</br> Opzioni per fornire la connettività: </br> **[Configurazione del servizio di bilanciamento del carico solo in uscita](egress-only.md)** </br> **[Che cos'è NAT di rete virtuale?](../virtual-network/nat-overview.md)**


### <a name="create-load-balancer-resource"></a>Creare la risorsa di bilanciamento del carico

Creare un servizio di bilanciamento del carico interno con [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer):

* Denominato **myLoadBalancer**.
* Posizionato in **eastus**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.

```azurepowershell-interactive
## Variables for the command ##
$lbn = 'myLoadBalancer'
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$sku = 'Standard'

## $feip, $bepool, $probe, $rule are variables with configuration information from previous steps. ##

$lb = 
New-AzLoadBalancer -ResourceGroupName $rg -Name $lbn -SKU $sku -Location $loc -FrontendIpConfiguration $feip -BackendAddressPool $bepool -Probe $probe -LoadBalancingRule $rule
```

### <a name="create-network-interfaces"></a>Creare interfacce di rete

Creare tre interfacce di rete con [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface):

#### <a name="vm-1"></a>VM 1

* Denominata **myNicVM1**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Nella posizione **eastus**.
* Nella rete virtuale **myVNet**.
* Nella subnet **myBackendSubnet**.
* Nel gruppo di sicurezza di rete **myNSG**.
* Associata al servizio di bilanciamento del carico **myLoadBalancer** in **myBackEndPool**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nic1 = 'myNicVM1'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM1 ##
$nicVM1 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic1 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-2"></a>VM 2

* Denominata **myNicVM2**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Nella posizione **eastus**.
* Nella rete virtuale **myVNet**.
* Nella subnet **myBackendSubnet**.
* Nel gruppo di sicurezza di rete **myNSG**.
* Associata al servizio di bilanciamento del carico **myLoadBalancer** in **myBackEndPool**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nic2 = 'myNicVM2'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM2 ##
$nicVM2 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic2 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

### <a name="create-virtual-machines"></a>Creare macchine virtuali

Impostare nome utente e password dell'amministratore delle macchine virtuali con il comando [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```azurepowershell
$cred = Get-Credential
```

Creare le macchine virtuali con:

* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


#### <a name="vm1"></a>VM1

* Denominata **myVM1**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Con collegamento all'interfaccia di rete **myNicVM1**.
* Con collegamento al servizio di bilanciamento del carico **myLoadBalancer**.
* Nella zona **1**.
* Nella posizione **eastus**.

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreateIntLBQS-rg'
$vm = 'myVM1'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$zn = '1'
$loc = 'eastus'

## Create a virtual machine configuration. $cred and $nicVM1 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM1.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Zone $zn -Location $loc -VM $vmConfig
```


#### <a name="vm2"></a>VM2

* Denominata **myVM2**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Con collegamento all'interfaccia di rete **myNicVM2**.
* Con collegamento al servizio di bilanciamento del carico **myLoadBalancer**.
* Nella zona **2**.
* Nella posizione **eastus**.

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreateIntLBQS-rg'
$vm = 'myVM2'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$zn = '2'
$loc = 'eastus'

## Create a virtual machine configuration. $cred and $nicVM2 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM2.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Zone $zn -Location $loc -VM $vmConfig
```

# <a name="basic-sku"></a>[**SKU Basic**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Il bilanciamento del carico di SKU Standard è l'impostazione consigliata per i carichi di lavoro di produzione. Per altre informazioni sugli SKU, vedere **[SKU di Azure Load Balancer](skus.md)** .

## <a name="configure-virtual-network"></a>Configurare la rete virtuale

Prima di distribuire le macchine virtuali e testare il servizio di bilanciamento del carico, creare le risorse di rete virtuale di supporto.

### <a name="create-a-virtual-network-and-azure-bastion-host"></a>Creare una rete virtuale e un host di Azure Bastion

Creare una rete virtuale con [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork):

* Denominata **myVNet**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Con una subnet denominata **myBackendSubnet**.
* Rete virtuale **10.0.0.0/16**.
* Subnet **10.0.0.0/24**.
* Subnet denominata **AzureBastionSubnet**.
* Subnet **10.0.1.0/24**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$sub = 'myBackendSubnet'
$spfx = '10.0.0.0/24'
$vnm = 'myVNet'
$vpfx = '10.0.0.0/16'
$bsub = 'AzureBastionSubnet'
$bpfx = '10.0.1.0/24'


## Create backend subnet config ##
$subnetConfig = 
New-AzVirtualNetworkSubnetConfig -Name $sub -AddressPrefix $spfx

## Create Azure Bastion subnet 
$bassubConfig =
New-AzVirtualNetworkSubnetConfig -Name $bsub -AddressPrefix $bpfx

## Create the virtual network ##
$vnet = 
New-AzVirtualNetwork -ResourceGroupName $rg -Location $loc -Name $vnm -AddressPrefix $vpfx -Subnet $subnetConfig,$bassubConfig
```

### <a name="create-public-ip-address-for-azure-bastion-host"></a>Creare un indirizzo IP pubblico per l'host Azure Bastion

Usare [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) per creare un indirizzo IP pubblico per l'host bastion:

* Denominato **myPublicIPBastion**
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Nella posizione **eastus**.
* Metodo di allocazione **static**.
* SKU **Standard**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$ipn = 'myPublicIPBastion'
$all = 'static'
$sku = 'standard'

$publicip = 
New-AzPublicIpAddress -ResourceGroupName $rg -Location $loc -Name $ipn -AllocationMethod $all -Sku $sku
```

### <a name="create-azure-bastion-host"></a>Creare l'host Azure Bastion

Usare [New-AzBastion](/powershell/module/az.network/new-azbastion) per creare un host bastion:

* Denominato **myBastion**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Nella rete virtuale **myVNet**.
* Associato all'indirizzo IP pubblico **myPublicIPBastion**.

```azurepowershell-interactive
## Variables for the commands ##
$rg = 'CreateIntLBQS-rg'
$nmn = 'myBastion'

## Command to create bastion host. $vnet and $publicip are from the previous steps ##
New-AzBastion -ResourceGroupName $rg -Name $nmn -PublicIpAddress $publicip -VirtualNetwork $vnet

```

La distribuzione dell'host Azure Bastion può richiedere qualche minuto.


### <a name="create-network-security-group"></a>Creare un gruppo di sicurezza di rete
Creare un gruppo di sicurezza di rete per definire le connessioni in ingresso alla rete virtuale.

#### <a name="create-a-network-security-group-rule-for-port-80"></a>Creare una regola del gruppo di sicurezza di rete per la porta 80
Creare una regola di gruppo di sicurezza di rete con [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig):

* Denominata **myNSGRuleHTTP**.
* Descrizione **Allow HTTP**.
* Accesso **Allow**.
* Protocollo **(*)** .
* Direzione **Inbound**.
* Priorità **2000**.
* Origine **Internet**.
* Intervallo di porte di origine **(*)** .
* Prefisso dell'indirizzo di destinazione **(*)** .
* Porta di destinazione **80**.

```azurepowershell-interactive
## Variables for command ##
$rnm = 'myNSGRuleHTTP'
$des = 'Allow HTTP'
$acc = 'Allow'
$pro = '*'
$dir = 'Inbound'
$pri = '2000'
$spfx = 'Internet'
$spr = '*'
$dpfx = '*'
$dpr = '80'

$rule1 = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $des -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix $spfx -SourcePortRange $spr -DestinationAddressPrefix $dpfx -DestinationPortRange $dpr
```

#### <a name="create-a-network-security-group"></a>Creare un gruppo di sicurezza di rete

Creare un gruppo di sicurezza di rete con [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup):

* Denominato **myNSG**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Nella posizione **eastus**.
* Con le regole di sicurezza create nei passaggi precedenti archiviati in una variabile.

```azurepowershell
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nmn = 'myNSG'

## $rule1 and $rule2 are variables with configuration information from the previous steps. ##
$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg -Location $loc -Name $nmn -SecurityRules $rule1
```

## <a name="create-basic-load-balancer"></a>Creare un servizio di bilanciamento del carico di base

Questa sezione descrive dettagliatamente come creare e configurare i componenti seguenti del servizio di bilanciamento del carico:

  * Un pool IP front-end che riceve il traffico di rete in ingresso sul servizio di bilanciamento del carico.
  * Un pool IP back-end a cui il pool front-end invia il traffico di rete con carico bilanciato.
  * Un probe di integrità che determina l'integrità delle istanze delle macchine virtuali back-end.
  * Una regola di bilanciamento del carico che definisce come verrà distribuito il traffico alle macchine virtuali.

### <a name="create-frontend-ip"></a>Creare l'indirizzo IP front-end

Creare un IP front-end con [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig):

* Denominato **myFrontEnd**.
* Indirizzo IP privato **10.0.0.4**.

```azurepowershell-interactive
## Variables for the commands ##
$fe = 'myFrontEnd'
$rg = 'CreateIntLBQS-rg'
$ip = '10.0.0.4'

## Command to create frontend configuration. The variable $vnet is from the previous commands. ##
$feip = 
New-AzLoadBalancerFrontendIpConfig -Name $fe -PrivateIpAddress $ip -SubnetId $vnet.subnets[0].Id
```

### <a name="configure-back-end-address-pool"></a>Configurare il pool di indirizzi back-end

Creare un pool di indirizzi back-end con [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig): 

* Denominarlo **myBackendPool**.
* Le macchine virtuali si collegano a questo pool back-end nei passaggi rimanenti.

```azurepowershell-interactive
## Variable for the command ##
$be = 'myBackEndPool'

$bepool = 
New-AzLoadBalancerBackendAddressPoolConfig -Name $be
```

### <a name="create-the-health-probe"></a>Creare il probe di integrità

Un probe di integrità controlla tutte le istanze di macchine virtuali per verificare che possano inviare traffico di rete. 

Una macchina virtuale con un controllo probe non riuscito viene rimossa dal servizio di bilanciamento del carico. La macchina virtuale viene nuovamente aggiunta al servizio di bilanciamento del carico quando l'errore viene risolto.

Creare un probe di integrità con [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig):

* Esegue il monitoraggio dello stato delle macchine virtuali.
* Denominato **myHealthProbe**.
* Protocollo **TCP**.
* Porta **80** per il monitoraggio.

```azurepowershell-interactive
## Variables for the command ##
$hp = 'myHealthProbe'
$pro = 'http'
$port = '80'
$int = '360'
$cnt = '5'

$probe = 
New-AzLoadBalancerProbeConfig -Name $hp -Protocol $pro -Port $port -RequestPath / -IntervalInSeconds $int -ProbeCount $cnt
```

### <a name="create-the-load-balancer-rule"></a>Creare la regola di bilanciamento del carico

Una regola di bilanciamento del carico definisce:

* La configurazione IP front-end per il traffico in ingresso.
* Il pool IP back-end in cui ricevere il traffico.
* La porta di origine e destinazione richiesta. 

Creare una regola di bilanciamento del carico con [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig): 

* Denominata **myHTTPRule**.
* In attesa sulla porta **80** nel pool front-end **myFrontEnd**.
* Per l'invio del traffico di rete con carico bilanciato al pool di indirizzi back-end **myBackEndPool** tramite la porta **80**. 
* Che usa il probe di integrità **myHealthProbe**.
* Protocollo **TCP**.
* Timeout di inattività di **15 minuti**.

```azurepowershell-interactive
## Variables for the command ##
$lbr = 'myHTTPRule'
$pro = 'tcp'
$port = '80'
$idl = '15'

## $feip and $bePool are the variables from previous steps. ##

$rule = 
New-AzLoadBalancerRuleConfig -Name $lbr -Protocol $pro -Probe $probe -FrontendPort $port -BackendPort $port -FrontendIpConfiguration $feip -BackendAddressPool $bePool -IdleTimeoutInMinutes $idl
```

### <a name="create-load-balancer-resource"></a>Creare la risorsa di bilanciamento del carico

Creare un servizio di bilanciamento del carico pubblico con [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer):

* Denominato **myLoadBalancer**.
* Posizionato in **eastus**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.

```azurepowershell-interactive
## Variables for the command ##
$lbn = 'myLoadBalancer'
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$sku = 'Basic'

## $feip, $bepool, $probe, $rule are variables with configuration information from previous steps. ##

$lb = 
New-AzLoadBalancer -ResourceGroupName $rg -Name $lbn -SKU $sku -Location $loc -FrontendIpConfiguration $feip -BackendAddressPool $bepool -Probe $probe -LoadBalancingRule $rule
```

### <a name="create-network-interfaces"></a>Creare interfacce di rete

Creare tre interfacce di rete con [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface):

#### <a name="vm-1"></a>VM 1

* Denominata **myNicVM1**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Nella posizione **eastus**.
* Nella rete virtuale **myVNet**.
* Nella subnet **myBackendSubnet**.
* Nel gruppo di sicurezza di rete **myNSG**.
* Associata al servizio di bilanciamento del carico **myLoadBalancer** in **myBackEndPool**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nic1 = 'myNicVM1'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM1 ##
$nicVM1 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic1 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-2"></a>VM 2

* Denominata **myNicVM2**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Nella posizione **eastus**.
* Nella rete virtuale **myVNet**.
* Nella subnet **myBackendSubnet**.
* Nel gruppo di sicurezza di rete **myNSG**.
* Associata al servizio di bilanciamento del carico **myLoadBalancer** in **myBackEndPool**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nic2 = 'myNicVM2'
$vnt = 'myVNet'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM2 ##
$nicVM2 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic2 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

### <a name="create-availability-set-for-virtual-machines"></a>Creare il set di disponibilità per le macchine virtuali

Usare [New-AzAvailabilitySet](/powershell/module/az.compute/new-azvm) per creare un set di disponibilità per le macchine virtuali:

* Denominato **myAvSet**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Nella posizione **eastus**.

```azurepowershell-interactive
## Variables used for the command. ##
$rg = 'CreateIntLBQS-rg'
$avs = 'myAvSet'
$loc = 'eastus'

New-AzAvailabilitySet -ResourceGroupName $rg -Name $avs -Location $loc
```

### <a name="create-virtual-machines"></a>Creare macchine virtuali

Impostare nome utente e password dell'amministratore delle macchine virtuali con il comando [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```azurepowershell
$cred = Get-Credential
```

Creare le macchine virtuali con:

* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


#### <a name="vm1"></a>VM1

* Denominata **myVM1**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Con collegamento all'interfaccia di rete **myNicVM1**.
* Con collegamento al servizio di bilanciamento del carico **myLoadBalancer**.
* Nella posizione **eastus**.
* Nel set di disponibilità **myAvSet**.

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreateIntLBQS-rg'
$vm = 'myVM1'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'
$avs = 'myAvSet'

## Create a virtual machine configuration. $cred and $nicVM1 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM1.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig -AvailabilitySetName $avs
```


#### <a name="vm2"></a>VM2

* Denominata **myVM2**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Con collegamento all'interfaccia di rete **myNicVM2**.
* Con collegamento al servizio di bilanciamento del carico **myLoadBalancer**.
* Nella posizione **eastus**.
* Nel set di disponibilità **myAvSet**.

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreateIntLBQS-rg'
$vm = 'myVM2'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'
$avs = 'myAvSet'

## Create a virtual machine configuration. $cred and $nicVM2 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM2.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig -AvailabilitySetName $avs
```

La creazione e la configurazione delle tre macchine virtuali richiedono alcuni minuti.

---

## <a name="install-iis"></a>Installare IIS

Usare [Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension?view=latest) per installare l'estensione per script personalizzati. 

L'estensione esegue il comando PowerShell Add-WindowsFeature Web-Server per installare il server Web IIS e quindi aggiorna la pagina Default.htm per visualizzare il nome host della VM:

### <a name="vm1"></a>VM1 

```azurepowershell-interactive
## Variables for command. ##
$rg = 'CreateIntLBQS-rg'
$enm = 'IIS'
$vmn = 'myVM1'
$loc = 'eastus'
$pub = 'Microsoft.Compute'
$ext = 'CustomScriptExtension'
$typ = '1.8'

Set-AzVMExtension -ResourceGroupName $rg -ExtensionName $enm -VMName $vmn -Location $loc -Publisher $pub -ExtensionType $ext -TypeHandlerVersion $typ -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```

### <a name="vm2"></a>VM2 

```azurepowershell-interactive
## Variables for command. ##
$rg = 'CreateIntLBQS-rg'
$enm = 'IIS'
$vmn = 'myVM2'
$loc = 'eastus'
$pub = 'Microsoft.Compute'
$ext = 'CustomScriptExtension'
$typ = '1.8'

Set-AzVMExtension -ResourceGroupName $rg -ExtensionName $enm -VMName $vmn -Location $loc -Publisher $pub -ExtensionType $ext -TypeHandlerVersion $typ -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```

## <a name="test-the-load-balancer"></a>Testare il servizio di bilanciamento del carico

### <a name="create-network-interface"></a>Creare l'interfaccia di rete

Creare un'interfaccia di rete con [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface):

#### <a name="mytestvm"></a>myTestVM

* Denominata **myNicTestVM**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Nella posizione **eastus**.
* Nella rete virtuale **myVNet**.
* Nella subnet **myBackendSubnet**.
* Nel gruppo di sicurezza di rete **myNSG**.

```azurepowershell-interactive
## Variables for command ##
$rg = 'CreateIntLBQS-rg'
$loc = 'eastus'
$nic1 = 'myNicTestVM'
$vnt = 'myVNet'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for myTestVM ##
$nicTestVM = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic1 -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

### <a name="create-virtual-machine"></a>Creare macchina virtuale

Impostare nome utente e password dell'amministratore della macchina virtuale con il comando [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```azurepowershell
$cred = Get-Credential
```

Creare la macchina virtuale con:

* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


#### <a name="mytestvm"></a>myTestVM

* Denominata **myTestVM**.
* Nel gruppo di risorse **CreateIntLBQS-rg**.
* Con collegamento all'interfaccia di rete **myNicTestVM**.
* Nella posizione **eastus**.

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'CreateIntLBQS-rg'
$vm = 'myTestVM'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'


## Create a virtual machine configuration. $cred and $nicTestVM are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicTestVM.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig
```

### <a name="test"></a>Test

1. [Accedere](https://portal.azure.com) al portale di Azure.

1. Individuare l'indirizzo IP privato del servizio di bilanciamento del carico nella schermata **Panoramica**. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine selezionare **myLoadBalancer**.

2. Annotare o copiare l'indirizzo accanto a **Indirizzo IP privato** nella pagina **Panoramica** di **myLoadBalancer**.

3. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine nell'elenco di risorse selezionare **myTestVM**, che si trova nel gruppo di risorse **CreateIntLBQS-rg**.

4. Nella pagina **Panoramica** selezionare **Connetti**, quindi **Bastion**.

6. Immettere il nome utente e la password specificati durante la creazione della VM.

7. Aprire **Internet Explorer** in **myTestVM**.

8. Immettere l'indirizzo IP del passaggio precedente nella barra degli indirizzi del browser. Nel browser verrà visualizzata la pagina predefinita del server Web IIS.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="Creare un servizio di bilanciamento del carico interno standard" border="true":::
   
Per visualizzare il servizio di bilanciamento del carico distribuire il traffico tra tutte e tre macchine virtuali, è possibile personalizzare la pagina predefinita del server Web IIS di ciascuna macchina virtuale e quindi forzare l'aggiornamento del Web browser dal computer client.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile usare il comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) per rimuovere il gruppo di risorse, il servizio di bilanciamento del carico e le risorse rimanenti.

```azurepowershell-interactive
## Variable for command. ##
$rg = 'CreateIntLBQS-rg'

Remove-AzResourceGroup -Name $rg
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido

* È stato creato un servizio di bilanciamento del carico interno Standard o Basic
* Sono state collegate le macchine virtuali. 
* Sono stati configurati la regola del traffico di bilanciamento del carico e il probe di integrità.
* È stato testato il servizio di bilanciamento del carico.

Per altre informazioni su Azure Load Balancer, passare a...
> [!div class="nextstepaction"]
> [Informazioni su Azure Load Balancer](load-balancer-overview.md)