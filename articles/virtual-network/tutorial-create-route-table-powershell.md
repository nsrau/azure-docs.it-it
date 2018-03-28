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
ms.date: 03/13/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 49c7b6158beee9d47ecd224e6a0750310d2b68c0
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="route-network-traffic-with-a-route-table-using-powershell"></a>Indirizzare il traffico di rete con una tabella di route usando PowerShell

Per impostazione predefinita, Azure indirizza automaticamente il traffico tra tutte le subnet di una rete virtuale. È possibile creare le proprie route per eseguire l'override del routing predefinito di Azure. La possibilità di creare route personalizzate è utile se, ad esempio, si vuole indirizzare il traffico tra subnet attraverso un'appliance virtuale di rete. In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Creare una tabella di route
> * Creare una route
> * Creare una rete virtuale con più subnet
> * Associare una route a una subnet
> * Creare un'appliance virtuale di rete che indirizza il traffico
> * Distribuire le macchine virtuali in subnet diverse
> * Indirizzare il traffico da una subnet a un'altra attraverso un'appliance virtuale di rete

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Se si sceglie di installare e usare PowerShell in locale, per questo articolo è necessario il modulo Azure PowerShell 5.4.1 o versione successiva. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Login-AzureRmAccount` per creare una connessione con Azure. 

## <a name="create-a-route-table"></a>Creare una tabella di route

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

Creare una route recuperando l'oggetto tabella di route con [Get-AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable), creare una route con [Add-AzureRmRouteConfig](/powershell/module/azurerm.network/add-azurermrouteconfig), quindi scrivere la configurazione della route nella tabella di route con [Set-AzureRmRouteTable](/powershell/module/azurerm.network/set-azurermroutetable). 

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

Scrivere le configurazioni subnet nella rete virtuale con [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), che consente di creare le subnet nella rete virtuale:

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

Associare la tabella di route *myRouteTablePublic* alla subnet *Public* con [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) e quindi scrivere la configurazione della subnet nella rete virtuale con [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork).

```azurepowershell-interactive
Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $virtualNetwork `
  -Name 'Public' `
  -AddressPrefix 10.0.0.0/24 `
  -RouteTable $routeTablePublic | `
Set-AzureRmVirtualNetwork
```

## <a name="create-an-nva"></a>Creare un'appliance virtuale di rete

Un'appliance virtuale di rete è una macchina virtuale che svolge una funzione di rete, ad esempio il routing, la protezione tramite firewall o l'ottimizzazione della WAN.

Prima di creare una macchina virtuale occorre creare un'interfaccia di rete.

### <a name="create-a-network-interface"></a>Creare un'interfaccia di rete

Prima di creare un'interfaccia di rete, è necessario recuperare l'ID della rete virtuale con [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork), quindi l'ID di subnet con [Get-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig). Creare un'interfaccia di rete con [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) nella subnet *DMZ* con l'inoltro IP abilitato:

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

### <a name="create-a-vm"></a>Creare una macchina virtuale

Per creare una macchina virtuale a cui collegare un'interfaccia di rete esistente, è prima di tutto necessario creare una configurazione della macchina virtuale con [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig). La configurazione include l'interfaccia di rete creata nel passaggio precedente. Quando richiesto, selezionare il nome utente e la password con cui si vuole accedere alla macchina virtuale. 

```azurepowershell-interactive
# Create a credential object.
$cred = Get-Credential -Message "Enter a username and password for the VM."

# Create a VM configuration.
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

Creare la macchina virtuale usando la configurazione della macchina virtuale con [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). L'esempio seguente crea una macchina virtuale denominata *myVmNva*. 

```azurepowershell-interactive
$vmNva = New-AzureRmVM `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -VM $vmConfig `
  -AsJob
```

L'opzione `-AsJob` crea la macchina virtuale in background, pertanto è possibile continuare con il passaggio successivo.

## <a name="create-virtual-machines"></a>Creare macchine virtuali

Creare due macchine virtuali nella rete virtuale per poter verificare che il traffico dalla subnet *Public* venga indirizzato alla subnet *Private* attraverso l'appliance virtuale di rete in un passaggio successivo. 

Creare una macchina virtuale nella subnet *Public* con [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). L'esempio seguente crea una macchina virtuale denominata *myVmPublic* nella subnet *Public* della rete virtuale *myVirtualNetwork*. 

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Public" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmPublic" `
  -AsJob
```

Creare una macchina virtuale nella subnet *Private*.

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Private" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmPrivate"
```

La creazione della VM richiede alcuni minuti. Non proseguire con il passaggio successivo fino a quando non viene creata la macchina virtuale e Azure non restituisce l'output a PowerShell.

## <a name="route-traffic-through-an-nva"></a>Indirizzare il traffico attraverso un'appliance virtuale di rete

Usare [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) per restituire l'indirizzo IP pubblico della macchina virtuale *myVmPrivate*. L'esempio seguente restituisce l'indirizzo IP pubblico della macchina virtuale *myVmPrivate*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Usare il comando seguente per creare una sessione Desktop remoto con la macchina virtuale *myVmPrivate* dal computer locale. Sostituire `<publicIpAddress>` con l'indirizzo IP restituito dal comando precedente.

```
mstsc /v:<publicIpAddress>
```

Aprire il file RDP scaricato. Quando richiesto, selezionare **Connetti**.

Immettere il nome utente e la password specificati durante la creazione della macchina virtuale (potrebbe essere necessario selezionare **Altre opzioni**, quindi **Usa un account diverso** per specificare le credenziali immesse quando è stata creata la macchina virtuale), quindi scegliere **OK**. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Selezionare **Sì** per procedere con la connessione. 

In un passaggio successivo viene usato il comando tracert.exe per testare il routing. Tracert usa il protocollo ICMP (Internet Control Message Protocol), che viene rifiutato tramite Windows Firewall. Abilitare il protocollo ICMP tramite Windows Firewall immettendo il comando seguente da PowerShell:

```powershell
New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
```

Anche se in questo articolo viene usato tracert per testare il routing, non è consigliabile consentire il protocollo ICMP tramite Windows Firewall per le distribuzioni di produzione.

Abilitare l'inoltro IP nel sistema operativo della macchina virtuale *myVmNva* completando i passaggi seguenti dalla macchina virtuale *myVmPrivate*:

Creare una sessione Desktop remoto alla macchina virtuale *myVmNva* con il comando seguente di PowerShell:

``` 
mstsc /v:myvmnva
```
    
Per abilitare l'inoltro IP nel sistema operativo, immettere il comando seguente in PowerShell:

```powershell
Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
```
    
Riavviare la macchina virtuale. Con questa operazione verrà anche disconnessa la sessione Desktop remoto.

Mentre si è ancora connessi alla macchina virtuale *myVmPrivate*, dopo il riavvio della macchina virtuale *myVmNva*, creare una sessione Desktop remoto alla macchina virtuale *myVmPublic* con il comando seguente:

``` 
mstsc /v:myVmPublic
```
    
Abilitare il protocollo ICMP tramite Windows Firewall immettendo il comando seguente da PowerShell:

```powershell
New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
```

Per testare il routing del traffico di rete alla macchina virtuale *myVmPrivate* dalla macchina virtuale *myVmPublic*, immettere il comando seguente da PowerShell:

```
tracert myVmPrivate
```

La risposta restituita è simile all'esempio seguente:
    
```
Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
over a maximum of 30 hops:
        
1    <1 ms     *        1 ms  10.0.2.4
2     1 ms     1 ms     1 ms  10.0.1.4
        
Trace complete.
```
      
È possibile notare che il primo hop è 10.0.2.4, cioè l'indirizzo IP privato dell'appliance virtuale di rete. Il secondo hop è 10.0.1.4, ossia l'indirizzo IP privato della macchina virtuale *myVmPrivate*. A causa della route aggiunta alla tabella di route *myRouteTablePublic* e associata alla subnet *Public*, Azure ha indirizzato il traffico di Azure attraverso l'appliance virtuale di rete, invece che direttamente alla subnet *Private*.

Chiudere la sessione Desktop remoto alla macchina virtuale *myVmPublic*. Si rimarrà tuttavia connessi alla macchina virtuale *myVmPrivate*.
Per testare il routing del traffico di rete alla macchina virtuale *myVmPublic* dalla macchina virtuale *myVmPrivate*, immettere il comando seguente da un prompt dei comandi:

```
tracert myVmPublic
```

La risposta restituita è simile all'esempio seguente:

```
Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
over a maximum of 30 hops:
    
1     1 ms     1 ms     1 ms  10.0.0.4
    
Trace complete.
```

Come si può osservare, il traffico viene indirizzato direttamente dalla macchina virtuale *myVmPrivate* alla macchina virtuale *myVmPublic*. Per impostazione predefinita, Azure instrada il traffico direttamente tra subnet.

Chiudere la sessione Desktop remoto alla macchina virtuale *myVmPrivate*.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il gruppo di risorse e tutte le risorse in esso contenute non sono più necessari, usare [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) per rimuoverli.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stata creata una tabella di route per poi associarla a una subnet. È stata creata una semplice appliance virtuale di rete che ha indirizzato il traffico da una subnet pubblica a una subnet privata. Distribuire una varietà di appliance virtuali di rete preconfigurate che svolgono funzioni di rete come la protezione tramite firewall e l'ottimizzazione della WAN da [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Prima di distribuire tabelle di route per l'uso in produzione, è consigliabile familiarizzare con il [routing in Azure](virtual-networks-udr-overview.md), la [gestione delle tabelle di route](manage-route-table.md) e i [limiti di Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

Benché sia possibile distribuire molte risorse di Azure all'interno di una rete virtuale, non è possibile distribuire le risorse per alcuni servizi PaaS di Azure in una rete virtuale. È comunque possibile limitare l'accesso alle risorse di alcuni servizi PaaS di Azure al traffico da una sola subnet della rete virtuale. Passare all'esercitazione successiva per informazioni su come limitare l'accesso alla rete alle risorse PaaS di Azure.

> [!div class="nextstepaction"]
> [Limitare l'accesso alla rete alle risorse PaaS](virtual-network-service-endpoints-configure.md#azure-powershell)