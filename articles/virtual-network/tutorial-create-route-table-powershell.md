---
title: Indirizzare il traffico di rete - PowerShell | Documentazione Microsoft
description: Informazioni su come indirizzare il traffico di rete con una tabella di route usando PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: f91b143c75a82aa6760796770b3ae4d0e4ec53dd
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="route-network-traffic-with-a-route-table-using-powershell"></a>Indirizzare il traffico di rete con una tabella di route usando PowerShell

Per impostazione predefinita, Azure indirizza automaticamente il traffico tra tutte le subnet di una rete virtuale. È possibile creare le proprie route per eseguire l'override del routing predefinito di Azure. La possibilità di creare route personalizzate è utile se, ad esempio, si vuole indirizzare il traffico tra subnet attraverso un firewall. In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Creare una tabella di route
> * Creare una route
> * Associare una tabella di route a una subnet della rete virtuale
> * Testare il routing
> * Risolvere i problemi di indirizzamento

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Se si sceglie di installare e usare PowerShell in locale, per questo articolo è necessario il modulo Azure PowerShell 5.4.1 o versione successiva. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Login-AzureRmAccount` per creare una connessione con Azure. 

## <a name="create-a-route-table"></a>Creare una tabella di route

Per impostazione predefinita, Azure indirizza il traffico tra tutte le subnet in una rete virtuale. Per altre informazioni sulle route predefinite di Azure, vedere [Route di sistema](virtual-networks-udr-overview.md). Per eseguire l'override del routing predefinito di Azure, creare una tabella di route contenente route, quindi associarla a una subnet della rete virtuale.

Per poter creare una tabella di route è prima di tutto necessario creare un gruppo di risorse con il comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* per tutte le risorse create in questo articolo. 

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Creare una tabella di route con [New-AzureRmRouteTable](/powershell/module/azurerm.network/new-azurermroutetable). L'esempio seguente crea una tabella di route denominata *myRouteTablePublic*.

```azurepowershell-interactive
$routeTablePublic = New-AzureRmRouteTable `
  -Name 'myRouteTablePublic' `
  -ResourceGroupName myResourceGroup `
  -location EastUS
```

## <a name="create-a-route"></a>Creare una route

Una tabella di route può contenere zero o più route. Creare una route recuperando l'oggetto tabella di route con [Get-AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable), creare una route con [Add-AzureRmRouteConfig](/powershell/module/azurerm.network/add-azurermrouteconfig), quindi scrivere la configurazione della route nella tabella di route con [Set-AzureRmRouteTable](/powershell/module/azurerm.network/set-azurermroutetable). 

```azurepowershell-interactive
Get-AzureRmRouteTable `
  -ResourceGroupName "myResourceGroup" `
  -Name "myRouteTablePublic" `
  | Add-AzureRmRouteConfig `
  -Name "ToPrivateSubnet" `
  -AddressPrefix 10.0.1.0/24 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress 10.0.2.4 `
 | Set-AzureRmRouteTable
```

La route indirizzerà tutto il traffico destinato al prefisso dell'indirizzo 10.0.1.0/24 attraverso un'appliance virtuale di rete con l'indirizzo IP 10.0.2.4. L'appliance virtuale di rete e la subnet con il prefisso dell'indirizzo specificato vengono create nei passaggi successivi. La route esegue l'override del routing predefinito di Azure, che indirizza il traffico tra subnet direttamente. Ogni route specifica un tipo di hop successivo. Il tipo di hop successivo indica ad Azure come indirizzare il traffico. In questo esempio il tipo di hop successivo è *VirtualAppliance*. Per altre informazioni su tutti i tipi di hop successivi disponibili in Azure e su quando usarli, vedere [Tipi di hop successivi](virtual-networks-udr-overview.md#custom-routes).

## <a name="associate-a-route-table-to-a-subnet"></a>Associare una route a una subnet

Prima di poter associare una tabella di route a una subnet, è necessario creare una rete virtuale e una subnet. Creare una rete virtuale con [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). L'esempio seguente crea una rete virtuale denominata *myVirtualNetwork* con il prefisso dell'indirizzo *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Creare tre subnet tramite la creazione di tre configurazioni di subnet con [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). L'esempio seguente crea tre configurazioni subnet per le subnet *Public*, *Private* e *DMZ*:

```azurepowershell-interactive
$subnetConfigPublic = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigPrivate = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigDmz = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name DMZ `
  -AddressPrefix 10.0.2.0/24 `
  -VirtualNetwork $virtualNetwork
```

I prefissi dell'indirizzo specificato per una subnet devono essere compresi nel prefisso dell'indirizzo definito per la rete virtuale. I prefissi dell'indirizzo della subnet non possono sovrapporsi tra loro.

Scrivere le configurazioni subnet nella rete virtuale con [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), che consente di creare le subnet nella rete virtuale:

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

È possibile associare una tabella di route a zero o più subnet. A una subnet può essere associata una o nessuna tabella di route. Il traffico in uscita da una subnet viene indirizzato in base alle route predefinite di Azure e alle eventuali route personalizzate che sono state aggiunte a una tabella di route associata a una subnet. Associare la tabella di route *myRouteTablePublic* alla subnet *Public* con [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) e quindi scrivere la configurazione della subnet nella rete virtuale con [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork).

```azurepowershell-interactive
Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $virtualNetwork `
  -Name 'Public' `
  -AddressPrefix 10.0.0.0/24 `
  -RouteTable $routeTablePublic | `
Set-AzureRmVirtualNetwork
```

Prima di distribuire tabelle di route per l'uso in produzione, è consigliabile familiarizzare con il [routing in Azure](virtual-networks-udr-overview.md) e i [limiti di Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="test-routing"></a>Testare il routing

Per testare il routing, verrà creata una macchina virtuale che funge da appliance virtuale di rete attraverso la quale viene indirizzata la route creata in un passaggio precedente. Dopo la creazione dell'appliance virtuale di rete, verrà distribuita una macchina virtuale nelle subnet *Public* e *Private*. Verrà quindi indirizzato il traffico dalla subnet *Public* alla subnet *Private* attraverso l'appliance virtuale di rete.

### <a name="create-a-network-virtual-appliance"></a>Creare un'appliance virtuale di rete

Una macchina virtuale che esegue un'applicazione di rete è spesso definita appliance virtuale di rete. Le appliance virtuali di rete ricevono in genere il traffico, eseguono alcune azioni, quindi inoltrano o rilasciano il traffico di rete in base alla logica configurata nell'applicazione di rete. 

#### <a name="create-a-network-interface"></a>Creare un'interfaccia di rete

In un passaggio precedente è stata creata una route che ha specificato un'appliance virtuale di rete come tipo di hop successivo. Una macchina virtuale che esegue un'applicazione di rete è spesso definita appliance virtuale di rete. Negli ambienti di produzione l'appliance virtuale di rete distribuita è spesso una macchina virtuale preconfigurata. Diverse appliance virtuali di rete sono disponibili in [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1). In questo articolo viene creata una macchina virtuale di base.

A una macchina virtuale sono collegate una o più interfacce di rete che le consentono di comunicare con la rete. Perché un'interfaccia di rete possa inoltrare il traffico ricevuto, non destinato al rispettivo indirizzo IP, occorre abilitare l'inoltro IP per l'interfaccia di rete. Prima di creare un'interfaccia di rete, è necessario recuperare l'ID della rete virtuale con [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork), quindi l'ID di subnet con [Get-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig). Creare un'interfaccia di rete con [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) nella subnet *DMZ* con l'inoltro IP abilitato:

```azurepowershell-interactive
# Retrieve the virtual network object into a variable.
$virtualNetwork=Get-AzureRmVirtualNetwork `
  -Name myVirtualNetwork `
  -ResourceGroupName myResourceGroup

# Retrieve the subnet configuration into a variable.
$subnetConfigDmz = Get-AzureRmVirtualNetworkSubnetConfig `
  -Name DMZ `
  -VirtualNetwork $virtualNetwork

# Create the network interface.
$nic = New-AzureRmNetworkInterface `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name 'myVmNva' `
  -SubnetId $subnetConfigDmz.Id `
  -EnableIPForwarding
```

#### <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

Per creare una macchina virtuale e a cui collegare un'interfaccia di rete esistente, è prima di tutto necessario creare una configurazione della macchina virtuale con [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig). La configurazione include l'interfaccia di rete creata nel passaggio precedente. Quando richiesto, selezionare il nome utente e la password con cui si vuole accedere alla macchina virtuale. 

```azurepowershell-interactive
# Create a credential object.
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a virtual machine configuration.
$vmConfig = New-AzureRmVMConfig `
  -VMName 'myVmNva' `
  -VMSize Standard_DS2 | `
  Set-AzureRmVMOperatingSystem -Windows `
    -ComputerName 'myVmNva' `
    -Credential $cred | `
  Set-AzureRmVMSourceImage `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest | `
  Add-AzureRmVMNetworkInterface -Id $nic.Id
```

Creare la macchina virtuale usando la configurazione della macchina virtuale con [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). L'esempio seguente crea una rete virtuale denominata *myVmNva*. 

```azurepowershell-interactive
$vmNva = New-AzureRmVM `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -VM $vmConfig `
  -AsJob
```

L'opzione `-AsJob` crea la macchina virtuale in background, pertanto è possibile continuare con il passaggio successivo. Quando richiesto, immettere il nome utente e la password con cui si vuole accedere alla macchina virtuale. Negli ambienti di produzione l'appliance virtuale di rete distribuita è spesso una macchina virtuale preconfigurata. Diverse appliance virtuali di rete sono disponibili in [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1).

Azure ha assegnato 10.0.2.4 come indirizzo IP privato della macchina virtuale, perché 10.0.2.4 è il primo indirizzo IP disponibile nella subnet *DMZ* di *myVirtualNetwork*.

### <a name="create-virtual-machines"></a>Creare macchine virtuali

Creare due macchine virtuali nella rete virtuale per poter verificare che il traffico dalla subnet *Public* venga indirizzato alla subnet *Private* attraverso l'appliance virtuale di rete in un passaggio successivo. 

Creare una macchina virtuale nella subnet *Public* con [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). L'esempio seguente crea una macchina virtuale denominata *myVmWeb* nella subnet *Public* della rete virtuale *myVirtualNetwork*. 

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Public" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmWeb" `
  -AsJob
```

Azure ha assegnato 10.0.0.4 come indirizzo IP privato della macchina virtuale, perché 10.0.1.4 è il primo indirizzo IP disponibile nella subnet *Public* di *myVirtualNetwork*.

Creare una macchina virtuale nella subnet *Private*.

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Private" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmMgmt"
```

La creazione della macchina virtuale richiede alcuni minuti. Azure ha assegnato 10.0.1.4 come indirizzo IP privato della macchina virtuale, perché 10.0.1.4 è il primo indirizzo IP disponibile nella subnet *Private* di *myVirtualNetwork*. 

Non proseguire con il passaggio successivo fino a quando non viene creata la macchina virtuale e Azure non restituisce l'output a PowerShell.

### <a name="route-traffic-through-a-network-virtual-appliance"></a>Instradare il traffico attraverso un'appliance virtuale di rete

Usare [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) per abilitare ICMP in ingresso nelle macchine virtuali *myVmWeb* e *myVmMgmt* attraverso il firewall di Windows per l'uso di tracert per testare la comunicazione tra le macchine virtuali in un passaggio successivo:

```powershell-interactive
Set-AzureRmVMExtension `
  -ResourceGroupName myResourceGroup `
  -VMName myVmWeb `
  -ExtensionName AllowICMP `
  -Publisher Microsoft.Compute `
  -ExtensionType CustomScriptExtension `
  -TypeHandlerVersion 1.8 `
  -SettingString '{"commandToExecute": "netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}' `
  -Location EastUS

Set-AzureRmVMExtension `
  -ResourceGroupName myResourceGroup `
  -VMName myVmMgmt `
  -ExtensionName AllowICMP `
  -Publisher Microsoft.Compute `
  -ExtensionType CustomScriptExtension `
  -TypeHandlerVersion 1.8 `
  -SettingString '{"commandToExecute": "netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}' `
  -Location EastUS
```

Il completamento dei comandi precedenti può richiedere alcuni minuti. Non proseguire con il passaggio successivo fino a quando i comandi non saranno stati completati e l'output non sarà stato restituito a PowerShell. Anche se in questo articolo viene usato tracert per testare il routing, non è consigliabile consentire il protocollo ICMP tramite Windows Firewall per le distribuzioni di produzione.

Connettersi all'indirizzo IP pubblico di una macchina virtuale da Internet. Usare [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) per restituire l'indirizzo IP pubblico di una macchina virtuale. L'esempio seguente restituisce l'indirizzo IP pubblico della macchina virtuale *myVmMgmt*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Usare il comando seguente per creare una sessione Desktop remoto con la macchina virtuale *myVmMgmt* dal computer locale. Sostituire `<publicIpAddress>` con l'indirizzo IP restituito dal comando precedente.

```
mstsc /v:<publicIpAddress>
```

Viene creato, scaricato nel computer e aperto un file Remote Desktop Protocol con estensione rdp. Immettere il nome utente e la password specificati durante la creazione della macchina virtuale (potrebbe essere necessario selezionare **More choices** (Altre opzioni), quindi **Usa un account diverso** per specificare le credenziali immesse quando è stata creata la macchina virtuale) e quindi scegliere **OK**. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Fare clic su **Sì** o **Continua** per procedere con la connessione. 

In [Enable IP fowarding](#enable-ip-forwarding) (Abilitare l'inoltro IP) è stato abilitato l'inoltro IP all'interno di Azure per l'interfaccia di rete della macchina virtuale. All'interno della macchina virtuale è necessario che il sistema operativo o un'applicazione in esecuzione nella macchina virtuale sia anche in grado di inoltrare il traffico di rete. Quando si implementa un'appliance virtuale di rete in un ambiente di produzione, l'appliance in genere filtra, registra o esegue un'altra funzione prima di inoltrare il traffico. In questo articolo, tuttavia, il sistema operativo inoltra semplicemente tutto il traffico ricevuto. Abilitare l'inoltro IP all'interno del sistema operativo della macchina virtuale *myVmNva*:

Da un prompt dei comandi nella macchina virtuale *myVmMgmt*, avviare una sessione Desktop remoto con la macchina virtuale *myVmNva*:

``` 
mstsc /v:myVmNva
```
    
Per abilitare l'inoltro IP all'interno del sistema operativo della macchina virtuale *myVmNva*, immettere il comando seguente in PowerShell nella macchina virtuale *myVmNva*:

```powershell
Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
```
    
Riavviare la macchina virtuale *myVmNva*, che disconnetterà inoltre la sessione Desktop remoto, lasciando aperta la sessione Desktop remoto con la macchina virtuale *myVmMgmt*.

Dopo il riavvio della macchina virtuale *myVmNva*, usare il comando seguente per testare il routing per il traffico di rete alla macchina virtuale *myVmWeb* dalla macchina virtuale *myVmMgmt*.

```
tracert myvmweb
```

La risposta restituita è simile all'esempio seguente:

```
Tracing route to myvmweb.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
over a maximum of 30 hops:
    
1     1 ms     1 ms     1 ms  10.0.0.4
  
Trace complete.
```

Come si può osservare, il traffico viene indirizzato direttamente dalla macchina virtuale *myVmMgmt* alla macchina virtuale *myVmWeb*. Le route predefinite di Azure indirizzano il traffico direttamente tra subnet.

Usare il comando seguente per eseguire una connessione Desktop remoto alla macchina virtuale *myVmWeb* dalla macchina virtuale *myVmMgmt*:

``` 
mstsc /v:myVmWeb
```

Per testare il routing per il traffico di rete alla macchina virtuale *myVmMgmt* dalla macchina virtuale *myVmWeb*, immettere il comando seguente da un prompt dei comandi:

```
tracert myvmmgmt
```

La risposta restituita è simile all'esempio seguente:

```
Tracing route to myvmmgmt.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
over a maximum of 30 hops:
        
1    <1 ms     *        1 ms  10.0.2.4
2     1 ms     1 ms     1 ms  10.0.1.4
        
Trace complete.
```

È possibile notare che il primo hop è 10.0.2.4, cioè l'indirizzo IP privato dell'appliance virtuale di rete. Il secondo hop è 10.0.1.4, ovvero l'indirizzo IP della macchina virtuale *myVmMgmt*. A causa della route aggiunta alla tabella di route *myRouteTablePublic* e associata alla subnet *Public*, Azure ha indirizzato il traffico di Azure attraverso l'appliance virtuale di rete, invece che direttamente alla subnet *Private*.

Chiudere le sessioni Desktop remoto per entrambe le macchine virtuali *myVmWeb* e *myVmMgmt*.

## <a name="troubleshoot-routing"></a>Risolvere i problemi di indirizzamento

Come illustrato nei passaggi precedenti, Azure applica route predefinite, di cui è possibile facoltativamente eseguire l'override con route personalizzate. In alcuni casi il traffico non può essere indirizzato come previsto. Usare [New-AzureRmNetworkWatcher](/powershell/module/azurerm.network/new-azurermnetworkwatcher) per abilitare un Network Watcher nell'area Stati Uniti orientali, se non si ha già un Network Watcher in tale area:

```azurepowershell-interactive
# Enable network watcher for east region, if you don't already have a network watcher enabled for the region:
$nw = New-AzureRmNetworkWatcher `
 -Location eastus `
 -Name myNetworkWatcher_eastus `
 -ResourceGroupName myResourceGroup
```

Usare [Get-AzureRmNetworkWatcherNextHop](/powershell/module/azurerm.network/get-azurermnetworkwatchernexthop) per determinare come viene indirizzato il traffico tra due macchine virtuali. Ad esempio, il comando seguente testa il routing del traffico dalla macchina virtuale *myVmWeb* (10.0.0.4) alla macchina virtuale *myVmMgmt* (10.0.1.4):

```azurepowershell-interactive
$vmWeb = Get-AzureRmVM `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup

Get-AzureRmNetworkWatcherNextHop `
  -DestinationIPAddress 10.0.1.4 `
  -NetworkWatcherName myNetworkWatcher_eastus `
  -ResourceGroupName myResourceGroup `
  -SourceIPAddress 10.0.0.4 `
  -TargetVirtualMachineId $vmWeb.Id
```
Dopo una breve attesa viene restituito l'output seguente:

```azurepowershell
NextHopIpAddress    NextHopType       RouteTableId
------------------  ---------------- ---------------------------------------------------------------------------------------------------------------------------
10.0.2.4            VirtualAppliance  /subscriptions/<Subscription-Id>/resourceGroups/myResourceGroup/providers/Microsoft.Network/routeTables/myRouteTablePublic
```

L'output indica che l'indirizzo IP dell'hop successivo per il traffico proveniente dalla macchina virtuale *myVmWeb* alla macchina virtuale *myVmMgmt* è 10.0.2.4 (la macchina virtuale *myVmNva*), che il tipo di hop successivo è *VirtualAppliance* e che la tabella di route che determina il routing è *myRouteTablePublic*.

Le route valide per ogni interfaccia di rete sono una combinazione delle route predefinite di Azure e di qualsiasi route definita dall'utente. Per visualizzare tutte le route valide per un'interfaccia di rete in una macchina virtuale usare [Get-AzureRmEffectiveRouteTable](/powershell/module/azurerm.network/get-azurermeffectiveroutetable). Ad esempio, per visualizzare le route valide per l'interfaccia di rete *myVmWeb* nella macchina virtuale *myVmWeb*, immettere il comando seguente:

```azurepowershell-interactive
Get-AzureRmEffectiveRouteTable `
  -NetworkInterfaceName myVmWeb `
  -ResourceGroupName myResourceGroup `
  | Format-Table
```

Vengono restituite tutte le route predefinite e la route che è stata aggiunta in un passaggio precedente.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il gruppo di risorse e tutte le risorse in esso contenute non sono più necessari, usare [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) per rimuoverli.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stata creata una tabella di route per poi associarla a una subnet. È stata creata un'appliance virtuale di rete che ha indirizzato il traffico da una subnet pubblica a una subnet privata. Benché sia possibile distribuire molte risorse di Azure all'interno di una rete virtuale, non è possibile distribuire le risorse per alcuni servizi PaaS di Azure in una rete virtuale. È comunque possibile limitare l'accesso alle risorse di alcuni servizi PaaS di Azure al traffico da una sola subnet della rete virtuale. Passare all'articolo successivo per informazioni su come limitare l'accesso alla rete alle risorse PaaS di Azure.

> [!div class="nextstepaction"]
> [Limitare l'accesso alla rete alle risorse PaaS](virtual-network-service-endpoints-configure.md#azure-powershell)