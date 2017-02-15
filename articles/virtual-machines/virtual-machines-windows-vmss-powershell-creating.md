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
ms.date: 09/29/2016
ms.author: danielsollondon
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 14f83c6753ce37639b1b2f78a4c632f1d69f585d


---
# <a name="creating-virtual-machine-scale-sets-using-powershell-cmdlets"></a>Creazione di set di scalabilità di macchine virtuali con i cmdlet di PowerShell
Questo esempio descrive come creare il set di scalabilità di una macchina virtuale (VMSS). Viene creato un set di scalabilità di 3 nodi con tutte le funzionalità di rete e di archiviazione associate.

## <a name="first-steps"></a>Primi passaggi
Assicurarsi di avere installato il modulo Azure PowerShell più recente che include i cmdlet di PowerShell necessari per la gestione e la creazione dei set di scalabilità di macchine virtuali (VMSS).
Passare agli strumenti della riga di comando [qui](http://aka.ms/webpi-azps) per i moduli Azure più recenti.

Per trovare i cmdlet relativi ai set di scalabilità di macchine virtuali, usare la stringa di ricerca \*VMSS\*.

## <a name="creating-a-vmss"></a>Creazione di un set di scalabilità di macchina virtuale (VMSS)
##### <a name="create-resource-group"></a>Crea gruppo di risorse
```
$loc = 'westus';
$rgname = 'mynewrgwu';
  New-AzureRmResourceGroup -Name $rgname -Location $loc -Force;
```

##### <a name="create-storage-account"></a>Create Storage Account
Impostare il tipo e il nome dell'account di archiviazione.

```
$stoname = 'sto' + $rgname;
$stotype = 'Standard_LRS';
 New-AzureRmStorageAccount -ResourceGroupName $rgname -Name $stoname -Location $loc -SkuName $stotype -Kind "Storage";

$stoaccount = Get-AzureRmStorageAccount -ResourceGroupName $rgname -Name $stoname;
```

#### <a name="create-networking-vnet--subnet"></a>Creare la rete (rete virtuale/subnet)
##### <a name="subnet-specification"></a>Specifica della subnet
```
$subnetName = 'websubnet'
  $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix "10.0.0.0/24";
```

##### <a name="vnet-specification"></a>Specifica della rete virtuale
```
$vnet = New-AzureRmVirtualNetwork -Force -Name ('vnet' + $rgname) -ResourceGroupName $rgname -Location $loc -AddressPrefix "10.0.0.0/16" -Subnet $subnet;
$vnet = Get-AzureRmVirtualNetwork -Name ('vnet' + $rgname) -ResourceGroupName $rgname;

#In this case below we assume the new subnet is the only one, note difference if you have one already or have adjusted this code to more than one subnet.
$subnetId = $vnet.Subnets[0].Id;
```

##### <a name="create-public-ip-resource-to-allow-external-access"></a>Creare la risorsa indirizzo IP pubblico per consentire l'accesso esterno
Questo verrà associato al bilanciamento del carico.

```
$pubip = New-AzureRmPublicIpAddress -Force -Name ('pubip' + $rgname) -ResourceGroupName $rgname -Location $loc -AllocationMethod Dynamic -DomainNameLabel ('pubip' + $rgname);
$pubip = Get-AzureRmPublicIpAddress -Name ('pubip' + $rgname) -ResourceGroupName $rgname;
```

##### <a name="create-and-configure-load-balancer"></a>Creare e configurare il bilanciamento del carico
```
$frontendName = 'fe' + $rgname
$backendAddressPoolName = 'bepool' + $rgname
$probeName = 'vmssprobe' + $rgname
$inboundNatPoolName = 'innatpool' + $rgname
$lbruleName = 'lbrule' + $rgname
$lbName = 'vmsslb' + $rgname

#Bind Public IP to Load Balancer
$frontend = New-AzureRmLoadBalancerFrontendIpConfig -Name $frontendName -PublicIpAddress $pubip
```

##### <a name="configure-load-balancer"></a>Configurare il bilanciamento del carico
Creare la configurazione del pool di indirizzi di back-end  che verrà condivida dalle schede di interfaccia di rete delle macchine virtuali nei set di scalabilità.

```
$backendAddressPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name $backendAddressPoolName
```

Impostare la porta probe con bilanciamento del carico, modificare le impostazioni in base all'applicazione.

```
$probe = New-AzureRmLoadBalancerProbeConfig -Name $probeName -RequestPath healthcheck.aspx -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2
```

Creare regole NAT per la connettività diretta indirizzata (senza bilanciamento del carico) alle macchine virtuali nel set di scalabilità tramite il bilanciamento del carico. Si tratta di un'operazione a solo scopo dimostrativo, usare i metodi VNET interni per il protocollo RDP di questi server.

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

Controllare le impostazioni di bilanciamento del carico e verificare le configurazioni della porta con carico bilanciato. Si noti che non vengono visualizzate le regole NAT in ingresso fino a quando non vengono create le macchine virtuali del set di scalabilità.

```
$expectedLb = Get-AzureRmLoadBalancer -Name $lbName -ResourceGroupName $rgname
```

##### <a name="configure-and-create-vmss"></a>Configurare e creare i set di scalabilità di macchine virtuali
Questo esempio di infrastruttura illustra come configurare, distribuire e scalare il traffico Web nel set di scalabilità, ma nelle immagini di macchine virtuali specificate di seguito non sono presenti servizi Web installati.

```
#specify VMSS Name
$vmssName = 'vmss' + $rgname;

##specify VMSS specific details
$adminUsername = 'azadmin';
$adminPassword = "Password1234!";

$PublisherName = 'MicrosoftWindowsServer'
$Offer         = 'WindowsServer'
$Sku          = '2012-R2-Datacenter'
$Version       = 'latest'
$vmNamePrefix = 'winvmss'

$vhdContainer = "https://" + $stoname + ".blob.core.windows.net/" + $vmssName;

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

Creare la configurazione dei set di scalabilità di macchine virtuali

```
#Specify number of nodes
$numberofnodes = 3

$vmss = New-AzureRmVmssConfig -Location $loc -SkuCapacity $numberofnodes -SkuName 'Standard_A2' -UpgradePolicyMode 'automatic' `
    | Add-AzureRmVmssNetworkInterfaceConfiguration -Name $subnetName -Primary $true -IPConfiguration $ipCfg `
    | Set-AzureRmVmssOSProfile -ComputerNamePrefix $vmNamePrefix -AdminUsername $adminUsername -AdminPassword $adminPassword `
    | Set-AzureRmVmssStorageProfile -Name 'test' -OsDiskCreateOption 'FromImage' -OsDiskCaching 'None' `
    -ImageReferenceOffer $Offer -ImageReferenceSku $Sku -ImageReferenceVersion $Version `
    -ImageReferencePublisher $PublisherName -VhdContainer $vhdContainer `
    | Add-AzureRmVmssExtension -Name $extname -Publisher $publisher -Type $exttype -TypeHandlerVersion $extver -AutoUpgradeMinorVersion $true
```

Compilare la configurazione dei set di scalabilità di macchine virtuali

```
New-AzureRmVmss -ResourceGroupName $rgname -Name $vmssName -VirtualMachineScaleSet $vmss -Verbose;
```

Il set di scalabilità è stato creato. In questo esempio è possibile testare la connessione alla singola macchina virtuale tramite RDP:

```
VM0 : pubipmynewrgwu.westus.cloudapp.azure.com:3360
VM1 : pubipmynewrgwu.westus.cloudapp.azure.com:3361
VM2 : pubipmynewrgwu.westus.cloudapp.azure.com:3362
```



<!--HONumber=Nov16_HO3-->


