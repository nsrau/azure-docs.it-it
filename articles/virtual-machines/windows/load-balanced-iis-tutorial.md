---
title: "Esercitazione: Creare un&quot;applicazione a disponibilità elevata nelle VM di Azure | Microsoft Docs"
description: "Informazioni su come creare un&quot;applicazione sicura e a disponibilità elevata in tre macchine virtuali Windows con un servizio di bilanciamento del carico in Azure"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/30/2017
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 61fd0dc45cfa2d320713819be3db811be58ab77e
ms.lasthandoff: 04/27/2017

---

# <a name="build-a-load-balanced-highly-available-application-on-windows-virtual-machines-in-azure"></a>Creare un'applicazione con un servizio di bilanciamento del carico a disponibilità elevata in macchine virtuali Windows in Azure

In questa esercitazione viene creata un'applicazione a disponibilità elevata resiliente agli eventi di manutenzione. L'app usa un servizio di bilanciamento del carico, un set di disponibilità e tre macchine virtuali (VM) Windows. Questa esercitazione installa IIS ma può essere usata per distribuire un diverso framework applicazione sfruttando le stesse linee guida e gli stessi componenti a disponibilità elevata. 

## <a name="step-1---azure-prerequisites"></a>Passaggio 1: Prerequisiti di Azure

Per completare questa esercitazione, verificare di aver installato l'ultima versione del modulo [Azure PowerShell](/powershell/azure/overview).

Accedere prima alla sottoscrizione di Azure con il comando Login-AzureRmAccount e seguire le istruzioni visualizzate.

```powershell
Login-AzureRmAccount
```

Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Per poter creare qualsiasi altra risorsa di Azure, è necessario prima creare un gruppo di risorse con [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). L'esempio seguente crea un gruppo di risorse denominato `myResourceGroup` nell'area `westeurope`: 

```powershell
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location westeurope
```

## <a name="step-2---create-availability-set"></a>Passaggio 2: Creare un set di disponibilità

Le macchine virtuali possono essere create in domini logici di errore e di aggiornamento. Ogni dominio logico rappresenta una parte dell'hardware del data center di Azure sottostante. Quando si creano due o più VM, le risorse di calcolo e di archiviazione vengono distribuite in tali domini. Questa distribuzione assicura la disponibilità dell'app in caso di manutenzione di un componente hardware. I set di disponibilità consentono di definire questi domini logici di errore e di aggiornamento.

Creare un set di disponibilità con [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset). Nell'esempio seguente viene creato un set di disponibilità chiamato `myAvailabilitySet`:

```powershell
$availabilitySet = New-AzureRmAvailabilitySet `
  -ResourceGroupName myResourceGroup `
  -Name myAvailabilitySet `
  -Location westeurope `
  -Managed `
  -PlatformFaultDomainCount 3 `
  -PlatformUpdateDomainCount 2
```

## <a name="step-3---create-load-balancer"></a>Passaggio 3: Creare il servizio di bilanciamento del carico

Un servizio di bilanciamento del carico di Azure distribuisce il traffico tra un set di VM definite usando regole di bilanciamento del carico. Un probe di integrità monitora una determinata porta in ogni VM e distribuisce il traffico solo a una VM operativa.

### <a name="create-public-ip-address"></a>Creare un indirizzo IP pubblico

Per accedere all'app in Internet, assegnare un indirizzo IP pubblico al servizio di bilanciamento del carico. Creare un indirizzo IP pubblico con [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress). L'esempio seguente crea un indirizzo IP pubblico denominato `myPublicIP`:

```powershell
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroup `
  -Location westeurope `
  -AllocationMethod Static `
  -Name myPublicIP
```

### <a name="create-load-balancer"></a>Creare un servizio di bilanciamento del carico

Creare un indirizzo IP di front-end con [New-AzureRmLoadBalancerFrontendIpConfig](/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig). L'esempio seguente crea un indirizzo IP front-end denominato `myFrontEndPool`: 

```powershell
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name myFrontEndPool -PublicIpAddress $pip
```

Creare un pool di indirizzi back-end con [New-AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig). L'esempio seguente crea un pool di indirizzi IP back-end denominato `myBackEndPool`:

```powershell
$backendPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name myBackEndPool
```

Creare il servizio di bilanciamento del carico con [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer). L'esempio seguente crea un servizio di bilanciamento del carico denominato `myLoadBalancer` con l'indirizzo `myPublicIP`:

```powershell
$lb = New-AzureRmLoadBalancer `
  -ResourceGroupName myResourceGroup `
  -Name myLoadBalancer `
  -Location westeurope `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool
```

### <a name="create-health-probe"></a>Creare un probe integrità

Per consentire al servizio di bilanciamento del carico di monitorare lo stato dell'app, si usa un probe di integrità. Il probe di integrità aggiunge o rimuove in modo dinamico le VM nella rotazione del servizio di bilanciamento del carico in base alla rispettiva risposta ai controlli di integrità. Per impostazione predefinita, una VM viene rimossa dalla distribuzione del servizio di bilanciamento del carico dopo due errori consecutivi a intervalli di 15 secondi.

Creare un probe integrità con [Add-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/add-azurermloadbalancerprobeconfig). L'esempio seguente crea un probe di integrità denominato `myHealthProbe` che monitora ogni VM:

```powershell
Add-AzureRmLoadBalancerProbeConfig -Name myHealthProbe `
  -LoadBalancer $lb `
  -Protocol tcp `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
```

### <a name="create-load-balancer-rule"></a>Creare le regole del bilanciamento del carico

Una regola di bilanciamento del carico consente di definire come il traffico verrà distribuito alle VM.

Creare una regola di bilanciamento del carico con [Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/add-azurermloadbalancerruleconfig). L'esempio seguente crea una regola di bilanciamento del carico denominata `myLoadBalancerRule` e bilancia il traffico sulla porta `80`:

```powershell
Add-AzureRmLoadBalancerRuleConfig -Name myLoadBalancerRule `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80
```

Creare il servizio di bilanciamento del carico con [Set-AzureRmLoadBalancer](/powershell/module/azurerm.network/set-azurermloadbalancer):

```powershell
Set-AzureRmLoadBalancer -LoadBalancer $lb
```

## <a name="step-4---configure-networking"></a>Passaggio 4: Configurare la rete

Ogni VM ha una o più schede di interfaccia di rete (NIC) virtuali per la connessione a una rete virtuale. Tale rete virtuale è protetta in modo da filtrare il traffico in base alle regole di accesso definite.

### <a name="create-virtual-network"></a>Creare una rete virtuale

Per prima cosa, configurare una subnet con [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Nell'esempio seguente viene creata una subnet denominata `mySubnet`:

```powershell
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
```

Per fornire la connettività di rete alle macchine virtuali, creare una rete virtuale con [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). L'esempio seguente crea una rete virtuale denominata `myVnet` con `mySubnet`:

```powershell
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location westeurope `
  -Name myVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig
```

### <a name="configure-network-security"></a>Configurare la sicurezza di rete

Un [gruppo di sicurezza di rete](../../virtual-network/virtual-networks-nsg.md) di Azure consente di controllare il traffico in ingresso e in uscita per una o più macchine virtuali. Le regole di un gruppo di sicurezza di rete consentono o impediscono il traffico di rete su una porta specifica o un intervallo di porte. Queste regole possono includere un prefisso dell'indirizzo di origine, in modo che solo il traffico proveniente da un'origine specificata possa comunicare con una macchina virtuale.

Per consentire al traffico Web di raggiungere l'app, creare una regola del gruppo di sicurezza di rete con [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig). L'esempio seguente crea una regola del gruppo di sicurezza di rete denominata `myNetworkSecurityGroupRule`:

```powershell
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
```

Creare un gruppo di sicurezza di rete con [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup). L'esempio seguente crea un gruppo di sicurezza di rete denominato `myNetworkSecurityGroup`:

```powershell
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location westeurope `
  -Name myNetworkSecurityGroup `
  -SecurityRules $nsgRule
```

Aggiungere il gruppo di sicurezza di rete alla subnet con [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig):

```powershell
Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet `
  -Name mySubnet `
  -NetworkSecurityGroup $nsg `
  -AddressPrefix 192.168.1.0/24
```

Aggiornare la rete virtuale con [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork):

```powershell
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

### <a name="create-virtual-network-interface-cards"></a>Creare le schede di interfaccia di rete virtuali

Per il funzionamento dei servizi di bilanciamento del carico viene usata la risorsa NIC virtuale anziché la VM effettiva. La scheda di interfaccia di rete virtuale è connessa al servizio di bilanciamento del carico e quindi collegata a una VM.

Creare una scheda di interfaccia di rete virtuale con [New AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface). L'esempio seguente crea tre schede di interfaccia di rete virtuali, una per ogni VM creata per l'app nei passaggi successivi:


```powershell
for ($i=1; $i -le 3; $i++)
{
   New-AzureRmNetworkInterface -ResourceGroupName myResourceGroup `
     -Name myNic$i `
     -Location westeurope `
     -Subnet $vnet.Subnets[0] `
     -LoadBalancerBackendAddressPool $lb.BackendAddressPools[0]
}

```

## <a name="step-5---create-virtual-machines"></a>Passaggio 5 - Creare le macchine virtuali

Dopo aver implementato tutti i componenti sottostanti, è possibile creare VM a disponibilità elevata per eseguire l'app. 

Ottenere il nome utente e la password necessari per l'account amministratore nella macchina virtuale con [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```powershell
$cred = Get-Credential
```

Creare le macchine virtuali con [New-AzureRmVMConfig](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/new-azurermvmconfig), [Set-AzureRmVMOperatingSystem](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/set-azurermvmoperatingsystem), [Set-AzureRmVMSourceImage](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/set-azurermvmsourceimage), [Set-AzureRmVMOSDisk](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/set-azurermvmosdisk), [Add-AzureRmVMNetworkInterface](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/add-azurermvmnetworkinterface) e [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). L'esempio seguente crea tre macchine virtuali:

```powershell
for ($i=1; $i -le 3; $i++)
{
   $vm = New-AzureRmVMConfig -VMName myVM$i -VMSize Standard_D1 -AvailabilitySetId $availabilitySet.Id
   $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName myVM$i -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
   $vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest
   $vm = Set-AzureRmVMOSDisk -VM $vm -Name myOsDisk$i -StorageAccountType StandardLRS -DiskSizeInGB 128 -CreateOption FromImage -Caching ReadWrite
   $nic = Get-AzureRmNetworkInterface -ResourceGroupName myResourceGroup -Name myNic$i
   $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
   New-AzureRmVM -ResourceGroupName myResourceGroup -Location westeurope -VM $vm
}

```

La creazione e la configurazione di tutte e tre le macchine virtuali richiedono vari minuti. Il probe di integrità del servizio di bilanciamento del carico rileva quando l'app è in esecuzione in ogni VM. Quando l'app è in esecuzione, la regola di bilanciamento del carico inizia a distribuire il traffico.

### <a name="install-the-app"></a>Installare l'app 

Le estensioni delle macchine virtuali di Azure vengono usate per automatizzare le attività di configurazione delle macchine virtuali, ad esempio l'installazione di applicazioni e la configurazione del sistema operativo. L'[estensione personalizzata dello script per Windows](./../virtual-machines-windows-extensions-customscript.md) viene usata per eseguire qualsiasi script PowerShell in una macchina virtuale. Lo script può essere salvato nell'archiviazione di Azure, in qualsiasi endpoint HTTP accessibile o incorporato nella configurazione dell'estensione dello script personalizzata. Quando si usa l'estensione dello script personalizzata, l'agente VM di Azure gestisce l'esecuzione dello script.

Usare [AzureRmVMExtension Set](/powershell/module/azurerm.compute/set-azurermvmextension) per installare l'estensione dello script personalizzata. L'estensione esegue `powershell Add-WindowsFeature Web-Server` per l'installazione del server Web IIS:

```powershell
for ($i=1; $i -le 3; $i++)
{
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup `
     -ExtensionName IIS `
     -VMName myVM$i `
     -Publisher Microsoft.Compute `
     -ExtensionType CustomScriptExtension `
     -TypeHandlerVersion 1.4 `
     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server"}' `
     -Location westeurope
}
```

### <a name="test-your-app"></a>Test dell'app

Ottenere l'indirizzo IP pubblico del servizio di bilanciamento del carico con [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). L'esempio seguente ottiene l'indirizzo IP `myPublicIP` creato in precedenza:

```powershell
Get-AzureRmPublicIPAddress -ResourceGroupName myResourceGroup -Name myPublicIP | select IpAddress
```

Immettere l'indirizzo IP pubblico in un Web browser. Dopo aver aggiunto la regola del gruppo di sicurezza di rete, viene visualizzato il sito Web IIS predefinito. 

![Sito IIS predefinito](media/load-balanced-iis-tutorial/iis.png)

## <a name="step-6--management-tasks"></a>Passaggio 6 - Attività di gestione

Potrebbe essere necessario eseguire attività di manutenzione sulle VM che eseguono l'app, ad esempio per installare aggiornamenti del sistema operativo, oppure aggiungere altre VM per gestire un aumento del traffico verso l'app. Questa sezione illustra come rimuovere o aggiungere una VM nel servizio di bilanciamento del carico. 

### <a name="remove-a-vm-from-the-load-balancer"></a>Rimuovere una VM dal servizio di bilanciamento del carico

Rimuovere una macchina virtuale dal pool di indirizzi back-end reimpostando la proprietà LoadBalancerBackendAddressPools della scheda di interfaccia di rete.

Ottenere una scheda di interfaccia di rete con [Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface):

```powershell
$nic = Get-AzureRmNetworkInterface -ResourceGroupName myResourceGroup -Name myNic2
``` 

Impostare la proprietà LoadBalancerBackendAddressPools della scheda di interfaccia di rete su $null:

```powershell
$nic.Ipconfigurations[0].LoadBalancerBackendAddressPools=$null
```

Aggiornare la scheda di interfaccia di rete:

```powershell
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

### <a name="add-a-vm-to-the-load-balancer"></a>Aggiungere una VM al servizio di bilanciamento del carico

Al termine della manutenzione di una macchina virtuale o se è necessario espandere la capacità, aggiungere la scheda di interfaccia di rete di una macchina virtuale al pool di indirizzi back-end del servizio di bilanciamento del carico.

Ottenere il servizio di bilanciamento del carico:

```powershell
$lb = Get-AzureRMLoadBalancer -ResourceGroupName myResourceGroup -Name myLoadBalancer 
```

Aggiungere il pool di indirizzi back-end del servizio di bilanciamento del carico alla scheda di interfaccia di rete:

```powershell
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$lb.BackendAddressPools[0]
```

Aggiornare la scheda di interfaccia di rete:

```powershell
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

## <a name="next-steps"></a>Passaggi successivi

Esempi: [script di esempio di PowerShell per macchine virtuali di Azure](./../virtual-machines-windows-powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

