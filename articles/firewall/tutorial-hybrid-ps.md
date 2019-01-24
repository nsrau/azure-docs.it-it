---
title: 'Esercitazione: Distribuire e configurare Firewall di Azure in una rete ibrida con Azure PowerShell'
description: Questa esercitazione mostra come distribuire e configurare Firewall di Azure tramite il portale di Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 12/14/2018
ms.author: victorh
customer intent: As an administrator, I want to control network access from an on-premises network to an Azure virtual network.
ms.openlocfilehash: 511fb425c6b31d204c88094ec5b1a49316495a0a
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2019
ms.locfileid: "54431521"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-in-a-hybrid-network-using-azure-powershell"></a>Esercitazione: Distribuire e configurare Firewall di Azure in una rete ibrida con Azure PowerShell

Quando si connette la rete locale a una rete virtuale di Azure per creare una rete ibrida, la possibilità di controllare l'accesso alle risorse di rete di Azure è una parte importante di un piano di sicurezza complessivo.

È possibile usare il Firewall di Azure per controllare l'accesso alla rete in una rete ibrida usando le regole che definiscono il traffico di rete consentito e negato.

Per questa esercitazione vengono create tre reti virtuali:

- **VNet-Hub**: in questa rete virtuale si trova la rete virtuale.
- **VNet-Spoke**: la rete virtuale spoke rappresenta il carico di lavoro che si trova in Azure.
- **VNet-Onprem**: rappresenta una rete locale. In una distribuzione reale può essere connessa tramite una connessione VPN o Route. Per semplicità, questa esercitazione usa una connessione gateway VPN e per rappresentare una rete locale viene usata una rete virtuale ubicata in Azure.

![Firewall in una rete ibrida](media/tutorial-hybrid-ps/hybrid-network-firewall.png)

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Dichiarare le variabili
> * Creare la rete virtuale dell'hub del firewall
> * Creare la rete virtuale spoke
> * Creare la rete virtuale locale
> * Configurare e distribuire il firewall
> * Creare e connettere i gateway VPN
> * Eseguire il peering tra le reti virtuali dell'hub e spoke
> * Creare le route
> * Creare le macchine virtuali
> * Testare il firewall

## <a name="prerequisites"></a>Prerequisiti

Ai fini di questa esercitazione, è necessario eseguire PowerShell in locale. Deve essere installato il modulo Azure PowerShell 6.12.0 o versioni successive. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps). Dopo avere verificato la versione di PowerShell, eseguire `Login-AzureRmAccount` per creare una connessione ad Azure.

Per il corretto funzionamento di questo scenario devono essere soddisfatti tre requisiti principali:

- Una route definita dall'utente nella subnet spoke che punti all'indirizzo IP di Firewall di Azure come gateway predefinito. La propagazione della route BGP deve essere impostata su **Disabilitata** in questa tabella di route.
- Una route definita dall'utente nella subnet del gateway dell'hub deve puntare all'indirizzo IP del firewall come hop successivo per le reti spoke.
- Non è richiesta alcuna route definita dall'utente nella subnet di Firewall di Azure, dal momento che le route vengono apprese dal protocollo BGP.
- Assicurarsi di impostare **AllowGatewayTransit** durante il peering di VNet-Hub a VNet-Spoke e usare **UseRemoteGateways** durante il peering di VNet-Spoke a VNet-Hub.

Vedere la sezione [Creare route](#create-routes) in questa esercitazione per vedere come vengono create le route.

>[!NOTE]
>Connettività diretta al Firewall di Azure. Se è stato abilitato il tunneling forzato in locale tramite ExpressRoute o gateway applicazione, è necessario configurare una route definita dall'utente 0.0.0.0/0 con il valore **NextHopType** impostato come **Internet**e quindi assegnarla ad  **AzureFirewallSubnet**.

>[!NOTE]
>Il traffico tra reti virtuali direttamente con peering viene instradato direttamente anche se una route definita dall'utente punta al firewall di Azure come gateway predefinito. Per inviare il traffico da subnet a subnet al firewall in questo scenario, una route definita dall'utente deve contenere il prefisso di rete subnet di destinazione in modo esplicito su entrambe le subnet.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="declare-the-variables"></a>Dichiarare le variabili

L'esempio seguente dichiara le variabili usando i valori per questa esercitazione. In alcuni casi, potrebbe essere necessario sostituirne alcuni con valori personalizzati per usare la propria sottoscrizione. Modificare le variabili se necessario, quindi copiarle e incollarle nella console di PowerShell.

```azurepowershell
$RG1 = "FW-Hybrid-Test"
$Location1 = "East US"

# Variables for the firewall hub VNet

$VNetnameHub = "VNet-hub"
$SNnameHub = "AzureFirewallSubnet"
$VNetHubPrefix = "10.5.0.0/16"
$SNHubPrefix = "10.5.0.0/24"
$SNGWHubPrefix = "10.5.1.0/24"
$GWHubName = "GW-hub"
$GWHubpipName = "VNet-hub-GW-pip"
$GWIPconfNameHub = "GW-ipconf-hub"
$ConnectionNameHub = "hub-to-Onprem"

# Variables for the spoke virtual network

$VnetNameSpoke = "VNet-Spoke"
$SNnameSpoke = "SN-Workload"
$VNetSpokePrefix = "10.6.0.0/16"
$SNSpokePrefix = "10.6.0.0/24"
$SNSpokeGWPrefix = "10.6.1.0/24"

# Variables for the on-premises virtual network

$VNetnameOnprem = "Vnet-Onprem"
$SNNameOnprem = "SN-Corp"
$VNetOnpremPrefix = "192.168.0.0/16"
$SNOnpremPrefix = "192.168.1.0/24"
$SNGWOnpremPrefix = "192.168.2.0/24"
$GWOnpremName = "GW-Onprem"
$GWIPconfNameOnprem = "GW-ipconf-Onprem"
$ConnectionNameOnprem = "Onprem-to-hub"
$GWOnprempipName = "VNet-Onprem-GW-pip"

$SNnameGW = "GatewaySubnet"
```


## <a name="create-the-firewall-hub-virtual-network"></a>Creare la rete virtuale dell'hub del firewall

Prima di tutto, creare un gruppo di risorse per contenere tutte le risorse per questa esercitazione:

```azurepowershell
  New-AzureRmResourceGroup -Name $RG1 -Location $Location1
  ```

Definire le subnet da includere nella rete virtuale:

```azurepowershell
$FWsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameHub -AddressPrefix $SNHubPrefix
$GWsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWHubPrefix
```

Creare ora la rete virtuale dell'hub del firewall:

```azurepowershell
$VNetHub = New-AzureRmVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetHubPrefix -Subnet $FWsub,$GWsub
```

Richiedere un indirizzo IP pubblico da allocare per il gateway VPN che verrà creato per la rete virtuale. Si noti che *AllocationMethod* è **dinamico**. Non è possibile specificare l'indirizzo IP che si desidera usare. Viene allocato in modo dinamico per il gateway VPN. 

  ```azurepowershell
  $gwpip1 = New-AzureRmPublicIpAddress -Name $GWHubpipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```

## <a name="create-the-spoke-virtual-network"></a>Creare la rete virtuale spoke

Definire le subnet da includere nella rete virtuale spoke:

```azurepowershell
$Spokesub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameSpoke -AddressPrefix $SNSpokePrefix
$GWsubSpoke = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNSpokeGWPrefix
```

Creare la rete virtuale spoke:

```azurepowershell
$VNetSpoke = New-AzureRmVirtualNetwork -Name $VnetNameSpoke -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetSpokePrefix -Subnet $Spokesub,$GWsubSpoke
```

## <a name="create-the-on-premises-virtual-network"></a>Creare la rete virtuale locale

Definire le subnet da includere nella rete virtuale:

```azurepowershell
$Onpremsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNNameOnprem -AddressPrefix $SNOnpremPrefix
$GWOnpremsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWOnpremPrefix
```

Creare ora la rete virtuale locale:

```azurepowershell
$VNetOnprem = New-AzureRmVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetOnpremPrefix -Subnet $Onpremsub,$GWOnpremsub
```

Richiedere un indirizzo IP pubblico da allocare per il gateway che verrà creato per la rete virtuale. Si noti che *AllocationMethod* è **dinamico**. Non è possibile specificare l'indirizzo IP che si desidera usare. Viene allocato in modo dinamico per il gateway. 

  ```azurepowershell
  $gwOnprempip = New-AzureRmPublicIpAddress -Name $GWOnprempipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```

## <a name="configure-and-deploy-the-firewall"></a>Configurare e distribuire il firewall

Distribuire ora il firewall nella rete virtuale dell'hub.

```azurepowershell
# Get a Public IP for the firewall
$FWpip = New-AzureRmPublicIpAddress -Name "fw-pip" -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Static -Sku Standard
# Create the firewall
$Azfw = New-AzureRmFirewall -Name AzFW01 -ResourceGroupName $RG1 -Location $Location1 -VirtualNetworkName $VNetnameHub -PublicIpName fw-pip

#Save the firewall private IP address for future use

$AzfwPrivateIP = $Azfw.IpConfigurations.privateipaddress
$AzfwPrivateIP

```

### <a name="configure-network-rules"></a>Configurare le regole di rete

<!--- $Rule3 = New-AzureRmFirewallNetworkRule -Name "AllowPing" -Protocol ICMP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort *--->

```azurepowershell
$Rule1 = New-AzureRmFirewallNetworkRule -Name "AllowWeb" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 80

$Rule2 = New-AzureRmFirewallNetworkRule -Name "AllowRDP" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 3389

$NetRuleCollection = New-AzureRmFirewallNetworkRuleCollection -Name RCNet01 -Priority 100 `
   -Rule $Rule1,$Rule2 -ActionType "Allow"
$Azfw.NetworkRuleCollections = $NetRuleCollection
Set-AzureRmFirewall -AzureFirewall $Azfw
```

## <a name="create-and-connect-the-vpn-gateways"></a>Creare e connettere i gateway VPN

Le reti virtuali dell'hub e locale sono connesse tramite gateway VPN.

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>Creare un gateway VPN per la rete virtuale dell'hub

Creare la configurazione del gateway VPN. La configurazione del gateway VPN definisce la subnet e l'indirizzo IP pubblico da usare.

  ```azurepowershell
  $vnet1 = Get-AzureRmVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1
  $subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
  $gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfNameHub `
  -Subnet $subnet1 -PublicIpAddress $gwpip1
  ```

Creare ora un gateway VPN per la rete virtuale dell'hub. Le configurazioni da rete a rete richiedono un tipo di VpnType RouteBased. La creazione di un gateway VPN spesso richiede anche più di 45 minuti, a seconda della SKU del gateway VPN selezionata.

```azurepowershell
New-AzureRmVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```

### <a name="create-a-vpn-gateway-for-the-on-premises-virtual-network"></a>Creare un gateway VPN per la rete virtuale locale

Creare la configurazione del gateway VPN. La configurazione del gateway VPN definisce la subnet e l'indirizzo IP pubblico da usare.

  ```azurepowershell
$vnet2 = Get-AzureRmVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1
$subnet2 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfNameOnprem `
  -Subnet $subnet2 -PublicIpAddress $gwOnprempip
  ```

Creare ora un gateway VPN per la rete virtuale locale. Le configurazioni da rete a rete richiedono un tipo di VpnType RouteBased. La creazione di un gateway VPN spesso richiede anche più di 45 minuti, a seconda della SKU del gateway VPN selezionata.

```azurepowershell
New-AzureRmVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf2 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```

### <a name="create-the-vpn-connections"></a>Creare le connessioni VPN

A questo punto è possibile creare le connessioni VPN tra l'hub e i gateway locali

#### <a name="get-the-vpn-gateways"></a>Ottenere i gateway VPN

```azurepowershell
$vnetHubgw = Get-AzureRmVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1
$vnetOnpremgw = Get-AzureRmVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1
```

#### <a name="create-the-connections"></a>Creare le connessioni

In questo passaggio si crea la connessione dalla rete virtuale dell'hub alla rete virtuale locale. Verrà visualizzata una chiave condivisa a cui si fa riferimento negli esempi. È possibile utilizzare i propri valori specifici per la chiave condivisa. L'importante è che la chiave condivisa corrisponda per entrambe le configurazioni. Il completamento della creazione di una connessione può richiedere un po' di tempo.

```azurepowershell
New-AzureRmVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1 `
-VirtualNetworkGateway1 $vnetHubgw -VirtualNetworkGateway2 $vnetOnpremgw -Location $Location1 `
-ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
```
Creare la connessione dalla rete virtuale locale alla rete virtuale dell'hub. Questo passaggio è simile a quello precedente, con la differenza che viene creata la connessione da VNet-Onprem a VNet-hub. Assicurarsi che le chiavi condivise corrispondano. Dopo alcuni minuti la connessione verrà stabilita.

  ```azurepowershell
  New-AzureRmVirtualNetworkGatewayConnection -Name $ConnectionNameOnprem -ResourceGroupName $RG1 `
  -VirtualNetworkGateway1 $vnetOnpremgw -VirtualNetworkGateway2 $vnetHubgw -Location $Location1 `
  -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```

#### <a name="verify-the-connection"></a>Verificare la connessione

Per verificare l'esito positivo della connessione, è possibile usare il cmdlet *Get-AzureRmVirtualNetworkGatewayConnection* con o senza *-Debug*. Per configurare i valori in modo che corrispondano ai propri, usare l'esempio di cmdlet seguente. Quando richiesto, selezionare **A** per eseguire **Tutti**. Il valore *-Name* nell'esempio fa riferimento al nome della connessione che si vuole testare.

```azurepowershell
Get-AzureRmVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1
```

Al termine dell'esecuzione del cmdlet, visualizzare i valori. Nell'esempio seguente lo stato di connessione risulta *Connesso* ed è possibile visualizzare i byte in ingresso e in uscita.

```
"connectionStatus": "Connected",
"ingressBytesTransferred": 33509044,
"egressBytesTransferred": 4142431
```

## <a name="peer-the-hub-and-spoke-virtual-networks"></a>Eseguire il peering tra le reti virtuali dell'hub e spoke

Eseguire ora il peering tra le reti virtuali dell'hub e spoke.

```azurepowershell
# Peer hub to spoke
Add-AzureRmVirtualNetworkPeering -Name HubtoSpoke -VirtualNetwork $VNetHub -RemoteVirtualNetworkId $VNetSpoke.Id -AllowGatewayTransit

# Peer spoke to hub
Add-AzureRmVirtualNetworkPeering -Name SpoketoHub -VirtualNetwork $VNetSpoke -RemoteVirtualNetworkId $VNetHub.Id -AllowForwardedTraffic -UseRemoteGateways
```

## <a name="create-the-routes"></a>Creare le route

Creare quindi due route:

- Una route dalla subnet del gateway dell'hub alla subnet spoke attraverso l'indirizzo IP del firewall
- Una route predefinita dalla subnet spoke attraverso l'indirizzo IP del firewall

```azurepowershell
#Create a route table
$routeTableHubSpoke = New-AzureRmRouteTable `
  -Name 'UDR-Hub-Spoke' `
  -ResourceGroupName $RG1 `
  -location $Location1

#Create a route
Get-AzureRmRouteTable `
  -ResourceGroupName $RG1 `
  -Name UDR-Hub-Spoke `
  | Add-AzureRmRouteConfig `
  -Name "ToSpoke" `
  -AddressPrefix $VNetSpokePrefix `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzureRmRouteTable

#Associate the route table to the subnet

Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $VNetHub `
  -Name $SNnameGW `
  -AddressPrefix $SNGWHubPrefix `
  -RouteTable $routeTableHubSpoke | `
Set-AzureRmVirtualNetwork

#Now create the default route

#Create a table, with BGP route propagation disabled
$routeTableSpokeDG = New-AzureRmRouteTable `
  -Name 'UDR-DG' `
  -ResourceGroupName $RG1 `
  -location $Location1 `
  -DisableBgpRoutePropagation

#Create a route
Get-AzureRmRouteTable `
  -ResourceGroupName $RG1 `
  -Name UDR-DG `
  | Add-AzureRmRouteConfig `
  -Name "ToSpoke" `
  -AddressPrefix 0.0.0.0/0 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzureRmRouteTable

#Associate the route table to the subnet

Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $VNetSpoke `
  -Name $SNnameSpoke `
  -AddressPrefix $SNSpokePrefix `
  -RouteTable $routeTableSpokeDG | `
Set-AzureRmVirtualNetwork
```

## <a name="create-virtual-machines"></a>Creare macchine virtuali

Creare ora le macchine virtuali locali e per il carico di lavoro spoke e posizionarle nelle subnet appropriate.

### <a name="create-the-workload-virtual-machine"></a>Creare la macchina virtuale per il carico di lavoro

Creare una macchina virtuale nella rete virtuale spoke, con IIS in esecuzione, senza indirizzo IP pubblico e abilitata per il ping.
Quando richiesto, digitare il nome utente e la password per la macchina virtuale.

```azurepowershell
# Create an inbound network security group rule for ports 3389 and 80
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name Allow-RDP  -Protocol Tcp `
  -Direction Inbound -Priority 200 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix $SNSpokePrefix -DestinationPortRange 3389 -Access Allow
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name Allow-web  -Protocol Tcp `
  -Direction Inbound -Priority 202 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix $SNSpokePrefix -DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $RG1 -Location $Location1 -Name NSG-Spoke02 -SecurityRules $nsgRuleRDP,$nsgRuleWeb

#Create the NIC
$NIC = New-AzureRmNetworkInterface -Name spoke-01 -ResourceGroupName $RG1 -Location $Location1 -SubnetId $VnetSpoke.Subnets[0].Id -NetworkSecurityGroupId $nsg.Id

#Define the virtual machine
$VirtualMachine = New-AzureRmVMConfig -VMName VM-Spoke-01 -VMSize "Standard_DS2"
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName Spoke-01 -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2016-Datacenter' -Version latest

#Create the virtual machine
New-AzureRmVM -ResourceGroupName $RG1 -Location $Location1 -VM $VirtualMachine -Verbose

#Install IIS on the VM
Set-AzureRmVMExtension `
    -ResourceGroupName $RG1 `
    -ExtensionName IIS `
    -VMName VM-Spoke-01 `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server"}' `
    -Location $Location1
```

<!---#Create a host firewall rule to allow ping in
Set-AzureRmVMExtension `
    -ResourceGroupName $RG1 `
    -ExtensionName IIS `
    -VMName VM-Spoke-01 `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4"}' `
    -Location $Location1--->

### <a name="create-the-on-premises-virtual-machine"></a>Creare la macchina virtuale locale

Si tratta di una macchina virtuale semplice usata per connettersi all'indirizzo IP pubblico usando Desktop remoto. Da qui, ci si connette al server locale attraverso il firewall. Quando richiesto, digitare il nome utente e la password per la macchina virtuale.

```azurepowershell
New-AzureRmVm `
    -ResourceGroupName $RG1 `
    -Name "VM-Onprem" `
    -Location $Location1 `
    -VirtualNetworkName $VNetnameOnprem `
    -SubnetName $SNNameOnprem `
    -OpenPorts 3389 `
    -Size "Standard_DS2"
```

## <a name="test-the-firewall"></a>Testare il firewall

Prima di tutto, ottenere e quindi annotare l'indirizzo IP privato per la macchina virtuale **VM-spoke-01**.

```azurepowershell
$NIC.IpConfigurations.privateipaddress
```

Dal portale di Azure connettersi alla macchina virtuale **VM-Onprem**.
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
Aprire un Web browser in **VM-Onprem** e andare a http://\<IP privato VM-spoke-01\>.

Dovrebbe essere visualizzata la pagina predefinita di Internet Information Services.

Da **VM-Onprem** aprire un desktop remoto per **VM-spoke-01** all'indirizzo IP privato.

La connessione dovrebbe avere esito positivo e dovrebbe essere possibile accedere usando il nome utente e la password scelti.

A questo punto si è verificato che le regole del firewall funzionano:

<!---- You can ping the server on the spoke VNet.--->
- È possibile esplorare il Web server nella rete virtuale spoke.
- È possibile connettersi al server nella rete virtuale spoke con RDP.

Modificare quindi l'azione della raccolta di regole di rete del firewall impostandola su **Nega** per verificare che le regole del firewall funzionino come previsto. Eseguire lo script seguente per modificare l'azione della raccolta di regole impostandola su **Nega**.

```azurepowershell
$rcNet = $azfw.GetNetworkRuleCollectionByName("RCNet01")
$rcNet.action.type = "Deny"

Set-AzureRmFirewall -AzureFirewall $azfw
```

A questo punto rieseguire i test, che dovrebbero avere tutti esito negativo. Chiudere eventuali desktop remoti esistenti prima di testare le regole modificate.

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile conservare le risorse del firewall per l'esercitazione successiva oppure, se non è più necessario, eliminare il gruppo di risorse **FW-Hybrid-Test** per eliminare tutte le risorse correlate al firewall.

## <a name="next-steps"></a>Passaggi successivi

È possibile ora monitorare i log di Firewall di Azure.

> [!div class="nextstepaction"]
> [Esercitazione: monitorare i log del Firewall di Azure](./tutorial-diagnostics.md)
