---
title: Come bilanciare il carico delle macchine virtuali di Windows in Azure | Microsoft Docs
description: "Informazioni su come usare Azure Load Balancer per creare un'applicazione sicura e a disponibilità elevata in tre macchine virtuali di Windows"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/02/2017
ms.author: iainfou
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 342a305a4bbdc1526a9316a35819d06996702a5b
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---

# <a name="how-to-load-balance-windows-virtual-machines-in-azure-to-create-a-highly-available-application"></a>Come bilanciare il carico per le macchine virtuali di Windows in Azure per creare un'applicazione a disponibilità elevata
Il bilanciamento del carico offre un livello più elevato di disponibilità distribuendo le richieste in ingresso tra più macchine virtuali. In questa esercitazione vengono illustrati i diversi componenti di Azure Load Balancer che distribuiscono il traffico e garantiscono una disponibilità elevata. Si apprenderà come:

> [!div class="checklist"]
> * Creare un servizio di bilanciamento del carico di Azure
> * Creare un probe di integrità per il servizio di bilanciamento del carico
> * Creare regole del traffico di bilanciamento del carico
> * Usare l'estensione dello script personalizzata per creare un sito IIS di base
> * Creare macchine virtuali e collegarsi a un bilanciamento del carico
> * Visualizzare un bilanciamento del carico in azione
> * Aggiungere e rimuovere macchine virtuali da un bilanciamento del carico

Questa esercitazione richiede il modulo Azure PowerShell 3.6 o versioni successive. Eseguire ` Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps).


## <a name="azure-load-balancer-overview"></a>Panoramica di Azure Load Balancer
Azure Load Balancer è un bilanciamento del carico di livello 4 (TCP, UDP) che offre disponibilità elevata mediante la distribuzione del traffico in ingresso nelle macchine virtuali integre. Un probe di integrità del bilanciamento del carico monitora una determinata porta in ogni VM e distribuisce il traffico solo a una VM operativa.

Definire una configurazione IP front-end che contenga uno o più indirizzi IP pubblici. Questa configurazione IP front-end garantisce l'accessibilità al bilanciamento del carico e alle applicazioni tramite Internet. 

Le macchine virtuali si connettono a un bilanciamento del carico tramite la scheda di interfaccia di rete virtuale (NIC). Per distribuire il traffico alle macchine virtuali, è necessario che un pool di indirizzi back-end contenga gli indirizzi IP delle schede di interfaccia di rete virtuale connesse al bilanciamento del carico.

Per controllare il flusso del traffico, è necessario definire le regole di bilanciamento del carico per porte e protocolli specifici che eseguono il mapping delle macchine virtuali.


## <a name="create-azure-load-balancer"></a>Creare un Azure Load Balancer
Questa sezione descrive dettagliatamente come creare e configurare ogni componente del bilanciamento del carico. Per poter creare un servizio di bilanciamento del carico è prima necessario creare un gruppo di risorse con [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroupLoadBalancer* nella posizione *EastUS*:

```powershell
New-AzureRmResourceGroup `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Location EastUS
```

### <a name="create-a-public-ip-address"></a>Creare un indirizzo IP pubblico
Per accedere all'app in Internet, assegnare un indirizzo IP pubblico al servizio di bilanciamento del carico. Creare un indirizzo IP pubblico con [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress). Nell'esempio seguente viene creato un indirizzo IP pubblico denominato *myPublicIP* nel gruppo di risorse *myResourceGroupLoadBalancer*:

```powershell
$publicIP = New-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Location EastUS `
  -AllocationMethod Static `
  -Name myPublicIP
```

### <a name="create-a-load-balancer"></a>Creare un servizio di bilanciamento del carico
Creare un indirizzo IP di front-end con [New-AzureRmLoadBalancerFrontendIpConfig](/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig). Nell'esempio seguente viene creato un indirizzo IP front-end denominato *myFrontEndPool*: 

```powershell
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig `
  -Name myFrontEndPool `
  -PublicIpAddress $publicIP
```

Creare un pool di indirizzi back-end con [New-AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig). L'esempio seguente crea un pool di indirizzi back-end denominato *myBackEndPool*:

```powershell
$backendPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name myBackEndPool
```

Creare il servizio di bilanciamento del carico con [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer). Nell'esempio seguente viene creato un servizio di bilanciamento del carico denominato *myLoadBalancer* con l'indirizzo *myPublicIP*:

```powershell
$lb = New-AzureRmLoadBalancer `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Name myLoadBalancer `
  -Location EastUS `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool
```

### <a name="create-a-health-probe"></a>Creare un probe di integrità
Per consentire al servizio di bilanciamento del carico di monitorare lo stato dell'app, si usa un probe di integrità. Il probe di integrità aggiunge o rimuove in modo dinamico le VM nella rotazione del servizio di bilanciamento del carico in base alla rispettiva risposta ai controlli di integrità. Per impostazione predefinita, una VM viene rimossa dalla distribuzione del servizio di bilanciamento del carico dopo due errori consecutivi a intervalli di 15 secondi. Un probe di integrità viene creato in base a un protocollo o una specifica pagina di controllo integrità per l'app. 

Nell'esempio seguente viene creato un probe TCP. È anche possibile creare probe HTTP personalizzati per i controlli di integrità con granularità fine. Quando si usa un probe HTTP personalizzato, è necessario creare la pagina di controllo integrità, ad esempio *healthcheck.aspx*. Il probe deve restituire la risposta **HTTP 200 OK** affinché il servizio di bilanciamento del carico mantenga l'host nella rotazione.

Per creare un probe integrità con TCP, usare [Add-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/add-azurermloadbalancerprobeconfig). Nell'esempio seguente viene creato un probe di integrità denominato *myHealthProbe* che monitora ogni VM:

```powershell
Add-AzureRmLoadBalancerProbeConfig `
  -Name myHealthProbe `
  -LoadBalancer $lb `
  -Protocol tcp `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
```

Creare il servizio di bilanciamento del carico con [Set-AzureRmLoadBalancer](/powershell/module/azurerm.network/set-azurermloadbalancer):

```powershell
Set-AzureRmLoadBalancer -LoadBalancer $lb
```

### <a name="create-a-load-balancer-rule"></a>Creare una regola di bilanciamento del carico
Una regola di bilanciamento del carico consente di definire come il traffico verrà distribuito alle VM. Definire la configurazione IP front-end per il traffico in ingresso e il pool IP di back-end affinché riceva il traffico, insieme alla porta di origine e di destinazione necessaria. Per assicurarsi che solo le macchine virtuali integre ricevano il traffico, è necessario anche definire il probe di integrità da usare.

Creare una regola di bilanciamento del carico con [Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/add-azurermloadbalancerruleconfig). Nell'esempio seguente viene creata una regola di bilanciamento del carico denominata *myLoadBalancerRule* e il traffico viene bilanciato sulla porta *80*:

```powershell
$probe = Get-AzureRmLoadBalancerProbeConfig -LoadBalancer $lb -Name myHealthProbe

Add-AzureRmLoadBalancerRuleConfig `
  -Name myLoadBalancerRule `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe $probe
```

Creare il servizio di bilanciamento del carico con [Set-AzureRmLoadBalancer](/powershell/module/azurerm.network/set-azurermloadbalancer):

```powershell
Set-AzureRmLoadBalancer -LoadBalancer $lb
```


## <a name="configure-virtual-network"></a>Configurare la rete virtuale
Prima di distribuire alcune macchine virtuali e testare il servizio di bilanciamento, creare le risorse di rete virtuale di supporto. Per altre informazioni sulle reti virtuali, vedere l'esercitazione [Gestire le reti virtuali di Azure](tutorial-virtual-network.md).

### <a name="create-network-resources"></a>Creare risorse di rete
Creare una rete virtuale con [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). Nell'esempio seguente viene creata una rete virtuale denominata *myVnet* con una subnet denominata *mySubnet*:

```powershell
# Create subnet config
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 192.168.1.0/24

# Create the virtual network
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Location EastUS `
  -Name myVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig
```

Creare una regola per il gruppo di sicurezza di rete con [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig), quindi creare un gruppo di sicurezza di rete con [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup). Aggiungere il gruppo di sicurezza di rete alla subnet con [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) e quindi aggiornare la rete virtuale con [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork). 

Nell'esempio seguente viene creata una regola del gruppo di sicurezza di rete denominata *myNetworkSecurityGroup* che viene applicata a *mySubnet*:

```powershell
# Create security rule config
$nsgRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNetworkSecurityGroupRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1001 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow

# Create the network security group
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Location EastUS `
  -Name myNetworkSecurityGroup `
  -SecurityRules $nsgRule

# Apply the network security group to a subnet
Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name mySubnet `
  -NetworkSecurityGroup $nsg `
  -AddressPrefix 192.168.1.0/24

# Update the virtual network
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

Vengono create schede di interfaccia di rete virtuale con [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface). L'esempio seguente crea tre schede di interfaccia di rete virtuali, Una scheda di interfaccia di rete virtuale per ogni VM creata per l'app nei passaggi successivi. È possibile creare altre schede di interfaccia di rete virtuale e macchine virtuali in qualsiasi momento e aggiungerle al bilanciamento del carico:

```powershell
for ($i=1; $i -le 3; $i++)
{
   New-AzureRmNetworkInterface `
     -ResourceGroupName myResourceGroupLoadBalancer `
     -Name myNic$i `
     -Location EastUS `
     -Subnet $vnet.Subnets[0] `
     -LoadBalancerBackendAddressPool $lb.BackendAddressPools[0]
}
```

## <a name="create-virtual-machines"></a>Creare macchine virtuali
Per aumentare la disponibilità elevata dell'app, posizionare le macchine virtuali in un set di disponibilità.

Creare un set di disponibilità con [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset). Nell'esempio seguente viene creato un set di disponibilità denominato *myAvailabilitySet*:

```powershell
$availabilitySet = New-AzureRmAvailabilitySet `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Name myAvailabilitySet `
  -Location EastUS `
  -Managed `
  -PlatformFaultDomainCount 3 `
  -PlatformUpdateDomainCount 2
```

Impostare nome utente e password dell'amministratore delle macchine virtuali con il comando [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```powershell
$cred = Get-Credential
```

A questo punto è possibile creare le macchine virtuale con [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). L'esempio seguente crea tre macchine virtuali:

```powershell
for ($i=1; $i -le 3; $i++)
{
  $vm = New-AzureRmVMConfig `
    -VMName myVM$i `
    -VMSize Standard_D1 `
    -AvailabilitySetId $availabilitySet.Id
  $vm = Set-AzureRmVMOperatingSystem `
    -VM $vm `
    -Windows `
    -ComputerName myVM$i `
    -Credential $cred `
    -ProvisionVMAgent `
    -EnableAutoUpdate
  $vm = Set-AzureRmVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
  $vm = Set-AzureRmVMOSDisk `
    -VM $vm `
    -Name myOsDisk$i `
    -DiskSizeInGB 128 `
    -CreateOption FromImage `
    -Caching ReadWrite
  $nic = Get-AzureRmNetworkInterface `
    -ResourceGroupName myResourceGroupLoadBalancer `
    -Name myNic$i
  $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
  New-AzureRmVM `
    -ResourceGroupName myResourceGroupLoadBalancer `
    -Location EastUS `
    -VM $vm
}
```

La creazione e la configurazione di tutte e tre le VM richiedono alcuni minuti.

### <a name="install-iis-with-custom-script-extension"></a>Installare IIS con l'estensione dello script personalizzata
Nell'esercitazione precedente su [Come personalizzare una macchina virtuale di Windows](tutorial-automate-vm-deployment.md), si è appreso come automatizzare la personalizzazione della macchina virtuale con l'estensione dello script personalizzata per Windows. È possibile usare lo stesso approccio per installare e configurare IIS nelle macchine virtuali.

Usare il comando [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) per installare l'estensione dello script personalizzata. L'estensione esegue `powershell Add-WindowsFeature Web-Server` per installare il server Web IIS e quindi aggiorna la pagina *Default.htm* per visualizzare il nome host della VM:

```powershell
for ($i=1; $i -le 3; $i++)
{
   Set-AzureRmVMExtension `
     -ResourceGroupName myResourceGroupLoadBalancer `
     -ExtensionName IIS `
     -VMName myVM$i `
     -Publisher Microsoft.Compute `
     -ExtensionType CustomScriptExtension `
     -TypeHandlerVersion 1.4 `
     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
     -Location EastUS
}
```

## <a name="test-load-balancer"></a>Testare il bilanciamento del carico
Ottenere l'indirizzo IP pubblico del servizio di bilanciamento del carico con il comando [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). Nell'esempio seguente si ottiene l'indirizzo IP per *myPublicIP* creato in precedenza:

```powershell
Get-AzureRmPublicIPAddress `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Name myPublicIP | select IpAddress
```

Sarà quindi possibile immettere l'indirizzo IP pubblico in un Web browser. Verrà visualizzato il sito Web, con il nome host della macchina virtuale a cui il servizio di bilanciamento del carico ha distribuito il traffico, come nell'esempio seguente:

![Esecuzione del sito Web IIS](./media/tutorial-load-balancer/running-iis-website.png)

Per verificare la distribuzione del traffico tra tutte e tre le VM che eseguono l'app da parte del servizio di bilanciamento del carico, forzare l'aggiornamento del Web browser.


## <a name="add-and-remove-vms"></a>aggiunta e rimozione di VM
Potrebbe essere necessario eseguire attività di manutenzione sulle VM che eseguono l'app, ad esempio per installare aggiornamenti del sistema operativo, oppure aggiungere altre VM per gestire un aumento del traffico verso l'app. Questa sezione illustra come rimuovere o aggiungere una VM nel servizio di bilanciamento del carico.

### <a name="remove-a-vm-from-the-load-balancer"></a>Rimuovere una VM dal servizio di bilanciamento del carico
Ottenere la scheda di interfaccia di rete con [Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface) e quindi impostare la proprietà *LoadBalancerBackendAddressPools* della scheda di interfaccia di rete virtuale su *$null*. Infine, aggiornare la scheda di interfaccia di rete virtuale:

```powershell
$nic = Get-AzureRmNetworkInterface `
    -ResourceGroupName myResourceGroupLoadBalancer `
    -Name myNic2
$nic.Ipconfigurations[0].LoadBalancerBackendAddressPools=$null
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

Per verificare la distribuzione del traffico nelle due VM restanti che eseguono l'app da parte del servizio di bilanciamento del carico, forzare l'aggiornamento del Web browser. È ora possibile eseguire attività di manutenzione sulla VM, ad esempio installare aggiornamenti del sistema operativo o eseguire un riavvio della VM.

### <a name="add-a-vm-to-the-load-balancer"></a>Aggiungere una VM al servizio di bilanciamento del carico
Dopo avere eseguito la manutenzione della VM o se è necessario espandere la capacità, impostare la proprietà *LoadBalancerBackendAddressPools* della scheda di interfaccia di rete virtuale su *BackendAddressPool* in [Get-AzureRMLoadBalancer](/powershell/module/azurerm.network/get-azurermloadbalancer):

Ottenere il servizio di bilanciamento del carico:

```powershell
$lb = Get-AzureRMLoadBalancer `
    -ResourceGroupName myResourceGroupLoadBalancer `
    -Name myLoadBalancer 
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$lb.BackendAddressPools[0]
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione viene creato un bilanciamento del carico e vengono collegate le macchine virtuali. Si è appreso come:

> [!div class="checklist"]
> * Creare un servizio di bilanciamento del carico di Azure
> * Creare un probe di integrità per il servizio di bilanciamento del carico
> * Creare regole del traffico di bilanciamento del carico
> * Usare l'estensione dello script personalizzata per creare un sito IIS di base
> * Creare macchine virtuali e collegarsi a un bilanciamento del carico
> * Visualizzare un bilanciamento del carico in azione
> * Aggiungere e rimuovere macchine virtuali da un bilanciamento del carico

Passare all'esercitazione successiva per imparare a gestire la rete delle macchine virtuali.

> [!div class="nextstepaction"]
> [Gestire le VM e le reti virtuali](./tutorial-virtual-network.md)

