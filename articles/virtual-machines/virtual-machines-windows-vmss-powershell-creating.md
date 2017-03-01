---
title: "Creazione di set di scalabilità di macchine virtuali con i cmdlet di PowerShell | Documentazione Microsoft"
description: "Introduzione alla creazione e alla gestione dei set di scalabilità delle macchine virtuali di Azure tramite i cmdlet di Azure PowerShell"
services: virtual-machines-windows
documentationcenter: 
author: danielsollondon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 430d9d64-1f35-48f0-a4fd-9b69910ffa59
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: danielsollondon
translationtype: Human Translation
ms.sourcegitcommit: b2e89f0d5e6b14ce8d5847f8adc3d57a6122172e
ms.openlocfilehash: a3a36028a75d6cb7eb36277f3e2b5ab833c96a96
ms.lasthandoff: 02/21/2017


---
# <a name="creating-virtual-machine-scale-sets-using-powershell-cmdlets"></a>Creazione di set di scalabilità di macchine virtuali con i cmdlet di PowerShell
Questo articolo illustra un esempio di come creare un set di scalabilità di macchine virtuali (VMSS). Crea un set di scalabilità di tre nodi, con la rete e la risorsa di archiviazione associate.

## <a name="first-steps"></a>Primi passaggi
Assicurarsi di avere installato il modulo Azure PowerShell più recente o di avere i cmdlet di PowerShell necessari per la gestione e la creazione dei set di scalabilità di macchine virtuali.
Passare agli strumenti della riga di comando [qui](http://aka.ms/webpi-azps) per i moduli Azure più recenti.

Per trovare i cmdlet relativi ai set di scalabilità di macchine virtuali, usare la stringa di ricerca \*VMSS\*. Ad esempio _gcm *vmss*_

## <a name="creating-a-vmss"></a>Creazione di un set di scalabilità di macchina virtuale (VMSS)
#### <a name="create-resource-group"></a>Crea gruppo di risorse
```
$loc = 'westus';
$rgname = 'mynewrgwu';
  New-AzureRmResourceGroup -Name $rgname -Location $loc -Force;
```

### <a name="create-networking-vnet--subnet"></a>Creare la rete (rete virtuale/subnet)
#### <a name="subnet-specification"></a>Specifica della subnet
```
$subnetName = 'websubnet'
  $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix "10.0.0.0/24";
```

#### <a name="vnet-specification"></a>Specifica della rete virtuale
```
$vnet = New-AzureRmVirtualNetwork -Force -Name ('vnet' + $rgname) -ResourceGroupName $rgname -Location $loc -AddressPrefix "10.0.0.0/16" -Subnet $subnet;
$vnet = Get-AzureRmVirtualNetwork -Name ('vnet' + $rgname) -ResourceGroupName $rgname;

# In this case assume the new subnet is the only one
$subnetId = $vnet.Subnets[0].Id;
```

#### <a name="create-public-ip-resource-to-allow-external-access"></a>Creare la risorsa indirizzo IP pubblico per consentire l'accesso esterno
Questo verrà associato al bilanciamento del carico.

```
$pubip = New-AzureRmPublicIpAddress -Force -Name ('pubip' + $rgname) -ResourceGroupName $rgname -Location $loc -AllocationMethod Dynamic -DomainNameLabel ('pubip' + $rgname);
$pubip = Get-AzureRmPublicIpAddress -Name ('pubip' + $rgname) -ResourceGroupName $rgname;
```

#### <a name="create-load-balancer"></a>Crea servizio di bilanciamento del carico
```
$frontendName = 'fe' + $rgname
$backendAddressPoolName = 'bepool' + $rgname
$probeName = 'vmssprobe' + $rgname
$inboundNatPoolName = 'innatpool' + $rgname
$lbruleName = 'lbrule' + $rgname
$lbName = 'vmsslb' + $rgname

# Bind Public IP to Load Balancer
$frontend = New-AzureRmLoadBalancerFrontendIpConfig -Name $frontendName -PublicIpAddress $pubip
```

#### <a name="configure-load-balancer"></a>Configurare il bilanciamento del carico
Creare la configurazione del pool di indirizzi di back-end che verrà condivisa dalle schede di interfaccia di rete delle VM nel set di scalabilità.

```
$backendAddressPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name $backendAddressPoolName
```

Impostare la porta probe con bilanciamento del carico, modificare le impostazioni in base all'applicazione.

```
$probe = New-AzureRmLoadBalancerProbeConfig -Name $probeName -RequestPath healthcheck.aspx -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2
```

Creare un pool NAT in ingresso per la connettività diretta indirizzata (non con carico bilanciato) verso le VM del set di scalabilità tramite il bilanciamento del carico. Questo serve a dimostrare l'utilizzo di RDP e potrebbe non essere necessario nell'applicazione.

```
$frontendpoolrangestart = 3360
$frontendpoolrangeend = 3370
$backendvmport = 3389
$inboundNatPool = New-AzureRmLoadBalancerInboundNatPoolConfig -Name $inboundNatPoolName -FrontendIPConfigurationId `
$frontend.Id -Protocol Tcp -FrontendPortRangeStart $frontendpoolrangestart -FrontendPortRangeEnd $frontendpoolrangeend -BackendPort $backendvmport;
```

Creare la regola con carico bilanciato. Questo esempio mostra il bilanciamento del carico delle richieste della porta 80 con le impostazioni dei passaggi precedenti.

```
$protocol = 'Tcp'
$feLBPort = 80
$beLBPort = 80

$lbrule = New-AzureRmLoadBalancerRuleConfig -Name $lbruleName `
-FrontendIPConfiguration $frontend -BackendAddressPool $backendAddressPool `
-Probe $probe -Protocol $protocol -FrontendPort $feLBPort -BackendPort $beLBPort `
-IdleTimeoutInMinutes 15 -EnableFloatingIP -LoadDistribution SourceIP -Verbose;
```

Creare il bilanciamento del carico con la configurazione.

```
$actualLb = New-AzureRmLoadBalancer -Name $lbName -ResourceGroupName $rgname -Location $loc `
-FrontendIpConfiguration $frontend -BackendAddressPool $backendAddressPool `
-Probe $probe -LoadBalancingRule $lbrule -InboundNatPool $inboundNatPool -Verbose;
```

Controllare le impostazioni di bilanciamento del carico e verificare le configurazioni della porta con carico bilanciato. Si noti che non vengono visualizzate le regole NAT in ingresso fino a quando non vengono create le VM del set di scalabilità.

```
$expectedLb = Get-AzureRmLoadBalancer -Name $lbName -ResourceGroupName $rgname
```

##### <a name="configure-and-create-the-scale-set"></a>Configurare e creare il set di scalabilità
Questo esempio di infrastruttura illustra come configurare, distribuire e scalare il traffico Web nel set di scalabilità, ma nelle immagini di VM specificate di seguito non sono presenti servizi Web installati.

```
# specify scale set Name
$vmssName = 'vmss' + $rgname;

## specify VMSS specific details
$adminUsername = 'azadmin';
$adminPassword = "Password1234!";

$PublisherName = 'MicrosoftWindowsServer'
$Offer         = 'WindowsServer'
$Sku          = '2012-R2-Datacenter'
$Version       = 'latest'
$vmNamePrefix = 'winvmss'

###add an extension
$extname = 'BGInfo';
$publisher = 'Microsoft.Compute';
$exttype = 'BGInfo';
$extver = '2.1';
```

Associare NIC a bilanciamento del carico e subnet

```
$ipCfg = New-AzureRmVmssIPConfig -Name 'nic' `
-LoadBalancerInboundNatPoolsId $actualLb.InboundNatPools[0].Id `
-LoadBalancerBackendAddressPoolsId $actualLb.BackendAddressPools[0].Id `
-SubnetId $subnetId;
```

Creare la configurazione del set di scalabilità

```
# Specify number of nodes
$numberofnodes = 3

$vmss = New-AzureRmVmssConfig -Location $loc -SkuCapacity $numberofnodes -SkuName 'Standard_A2' -UpgradePolicyMode 'automatic' `
    | Add-AzureRmVmssNetworkInterfaceConfiguration -Name $subnetName -Primary $true -IPConfiguration $ipCfg `
    | Set-AzureRmVmssOSProfile -ComputerNamePrefix $vmNamePrefix -AdminUsername $adminUsername -AdminPassword $adminPassword `
    | Set-AzureRmVmssStorageProfile -OsDiskCreateOption 'FromImage' -OsDiskCaching 'None' `
    -ImageReferenceOffer $Offer -ImageReferenceSku $Sku -ImageReferenceVersion $Version `
    -ImageReferencePublisher $PublisherName `
    | Add-AzureRmVmssExtension -Name $extname -Publisher $publisher -Type $exttype -TypeHandlerVersion $extver -AutoUpgradeMinorVersion $true
```

Definire la configurazione del set di scalabilità

```
New-AzureRmVmss -ResourceGroupName $rgname -Name $vmssName -VirtualMachineScaleSet $vmss -Verbose;
```

Il set di scalabilità è stato creato. In questo esempio è possibile testare la connessione alla singola macchina virtuale tramite RDP:

```
VM0 : pubipmynewrgwu.westus.cloudapp.azure.com:3360
VM1 : pubipmynewrgwu.westus.cloudapp.azure.com:3361
VM2 : pubipmynewrgwu.westus.cloudapp.azure.com:3362
```

