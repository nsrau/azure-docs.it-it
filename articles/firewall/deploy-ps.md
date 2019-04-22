---
title: Distribuire e configurare il Firewall di Azure usando Azure PowerShell
description: In questo articolo descrive come distribuire e configurare il Firewall di Azure tramite Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.date: 4/10/2019
ms.author: victorh
ms.openlocfilehash: c2d49defa2e0fbbd12c5403ccca74e91cf4ec981
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59502114"
---
# <a name="deploy-and-configure-azure-firewall-using-azure-powershell"></a>Distribuire e configurare il Firewall di Azure usando Azure PowerShell

Il controllo dell'accesso alla rete in uscita è un componente importante di un piano di sicurezza della rete generale. Ad esempio, potrebbe essere utile limitare l'accesso ai siti Web. In alternativa, potrebbe essere utile limitare gli indirizzi IP e le porte in uscita a cui è possibile accedere.

È possibile controllare l'accesso alla rete in uscita da una subnet di Azure con Firewall di Azure. Con Firewall di Azure, è possibile configurare:

* Regole di applicazione che definiscono i nomi di dominio completi (FQDN) accessibili da una subnet.
* Regole di rete che definiscono l'indirizzo di origine, il protocollo, la porta di destinazione e l'indirizzo di destinazione.

Il traffico di rete è sottoposto alle regole del firewall configurate quando si instrada il traffico di rete al firewall come gateway predefinito della subnet.

In questo articolo si crea una singola rete virtuale di semplificata con tre subnet per semplificare la distribuzione. Per le distribuzioni di produzione è consigliabile un [modello hub e spoke](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke), in cui il firewall si trova nella propria rete virtuale. I server del carico di lavoro si trovano nelle reti virtuali associate all'interno della stessa area con una o più subnet.

* **AzureFirewallSubnet**: in questa subnet si trova il firewall.
* **Workload-SN**: in questa subnet si trova il server del carico di lavoro. Il traffico di rete di questa subnet passa attraverso il firewall.
* **Jump-SN**: in questa subnet si trova il jump server. Il jump server ha un indirizzo IP pubblico a cui è possibile connettersi con Desktop remoto. Da qui è quindi possibile connettersi al server del carico di lavoro (tramite un'altra istanza di Desktop remoto).

![Infrastruttura di rete dell'esercitazione](media/tutorial-firewall-rules-portal/Tutorial_network.png)

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Configurare un ambiente di rete di test
> * Distribuire un firewall
> * Creare una route predefinita
> * Configurare una regola dell'applicazione per consentire l'accesso a www.google.com
> * Configurare una regola di rete per consentire l'accesso a server DNS esterni
> * Testare il firewall

Se si preferisce, è possibile completare questa procedura mediante il [portale di Azure](tutorial-firewall-deploy-portal.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Questa procedura richiede l'esecuzione di PowerShell in locale. È necessario aver installato il modulo di Azure PowerShell. Eseguire `Get-Module -ListAvailable Az` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps). Dopo avere verificato la versione di PowerShell, eseguire `Connect-AzAccount` per creare una connessione ad Azure.

## <a name="set-up-the-network"></a>Configurare la rete

In primo luogo, creare un gruppo di risorse per contenere le risorse necessarie per distribuire il firewall. Creare quindi una rete virtuale, le subnet e i server di test.

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Il gruppo di risorse contiene tutte le risorse per la distribuzione.

```azurepowershell
New-AzResourceGroup -Name Test-FW-RG -Location "East US"
```

### <a name="create-a-vnet"></a>Creare una rete virtuale

Questa rete virtuale include tre subnet:

```azurepowershell
$FWsub = New-AzVirtualNetworkSubnetConfig -Name AzureFirewallSubnet -AddressPrefix 10.0.1.0/24
$Worksub = New-AzVirtualNetworkSubnetConfig -Name Workload-SN -AddressPrefix 10.0.2.0/24
$Jumpsub = New-AzVirtualNetworkSubnetConfig -Name Jump-SN -AddressPrefix 10.0.3.0/24
```

> [!NOTE]
> La dimensione minima della subnet AzureFirewallSubnet è /26.

A questo punto, creare la rete virtuale:

```azurepowershell
$testVnet = New-AzVirtualNetwork -Name Test-FW-VN -ResourceGroupName Test-FW-RG `
-Location "East US" -AddressPrefix 10.0.0.0/16 -Subnet $FWsub, $Worksub, $Jumpsub
```

### <a name="create-virtual-machines"></a>Creare macchine virtuali

Creare ora le macchine virtuali per il jump server e il server del carico di lavoro e posizionarle nelle subnet appropriate.
Quando richiesto, digitare il nome utente e la password per la macchina virtuale.

Creare la macchina virtuale Srv-Jump.

```azurepowershell
New-AzVm `
    -ResourceGroupName Test-FW-RG `
    -Name "Srv-Jump" `
    -Location "East US" `
    -VirtualNetworkName Test-FW-VN `
    -SubnetName Jump-SN `
    -OpenPorts 3389 `
    -Size "Standard_DS2"
```

Creare una macchina virtuale del carico di lavoro con nessun indirizzo IP pubblico.
Quando richiesto, digitare il nome utente e la password per la macchina virtuale.

```azurepowershell
#Create the NIC
$NIC = New-AzNetworkInterface -Name Srv-work -ResourceGroupName Test-FW-RG `
 -Location "East US" -Subnetid $testVnet.Subnets[1].Id 

#Define the virtual machine
$VirtualMachine = New-AzVMConfig -VMName Srv-Work -VMSize "Standard_DS2"
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName Srv-Work -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2016-Datacenter' -Version latest

#Create the virtual machine
New-AzVM -ResourceGroupName Test-FW-RG -Location "East US" -VM $VirtualMachine -Verbose
```

## <a name="deploy-the-firewall"></a>Distribuire il firewall

Distribuire ora il firewall nella rete virtuale.

```azurepowershell
# Get a Public IP for the firewall
$FWpip = New-AzPublicIpAddress -Name "fw-pip" -ResourceGroupName Test-FW-RG `
  -Location "East US" -AllocationMethod Static -Sku Standard
# Create the firewall
$Azfw = New-AzFirewall -Name Test-FW01 -ResourceGroupName Test-FW-RG -Location "East US" -VirtualNetworkName Test-FW-VN -PublicIpName fw-pip

#Save the firewall private IP address for future use

$AzfwPrivateIP = $Azfw.IpConfigurations.privateipaddress
$AzfwPrivateIP
```

Annotare l'indirizzo IP privato. Sarà necessario più avanti per la creazione della route predefinita.

## <a name="create-a-default-route"></a>Creare una route predefinita

Creare una tabella, con propagazione route BGP disabilitata

```azurepowershell
$routeTableDG = New-AzRouteTable `
  -Name Firewall-rt-table `
  -ResourceGroupName Test-FW-RG `
  -location "East US" `
  -DisableBgpRoutePropagation

#Create a route
 Add-AzRouteConfig `
  -Name "DG-Route" `
  -RouteTable $routeTableDG `
  -AddressPrefix 0.0.0.0/0 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzRouteTable

#Associate the route table to the subnet

Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $testVnet `
  -Name Workload-SN `
  -AddressPrefix 10.0.2.0/24 `
  -RouteTable $routeTableDG | Set-AzVirtualNetwork
```

## <a name="configure-an-application-rule"></a>Configurare una regola di applicazione

La regola delle applicazioni consente l'accesso in uscita a www.google.com.

```azurepowershell
$AppRule1 = New-AzFirewallApplicationRule -Name Allow-Google -SourceAddress 10.0.2.0/24 `
  -Protocol http, https -TargetFqdn www.google.com

$AppRuleCollection = New-AzFirewallApplicationRuleCollection -Name App-Coll01 `
  -Priority 200 -ActionType Allow -Rule $AppRule1

$Azfw.ApplicationRuleCollections = $AppRuleCollection

Set-AzFirewall -AzureFirewall $Azfw
```

Firewall di Azure include una raccolta di regole predefinite per i nomi di dominio completi dell'infrastruttura consentiti per impostazione predefinita. Questi nomi di dominio completi sono specifici per la piattaforma e non possono essere usati per altri scopi. Per altre informazioni, vedere [Infrastructure FQDNs](infrastructure-fqdns.md) (FQDN dell'infrastruttura).

## <a name="configure-a-network-rule"></a>Configurare una regola di rete

La regola di rete consente l'accesso in uscita a due indirizzi IP sulla porta 53 (DNS).

```azurepowershell
$NetRule1 = New-AzFirewallNetworkRule -Name "Allow-DNS" -Protocol UDP -SourceAddress 10.0.2.0/24 `
   -DestinationAddress 209.244.0.3,209.244.0.4 -DestinationPort 53

$NetRuleCollection = New-AzFirewallNetworkRuleCollection -Name RCNet01 -Priority 200 `
   -Rule $NetRule1 -ActionType "Allow"

$Azfw.NetworkRuleCollections = $NetRuleCollection

Set-AzFirewall -AzureFirewall $Azfw
```

### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>Modificare l'indirizzo DNS primario e secondario per l'interfaccia di rete **Srv-Work**

A scopo di test in questa procedura, configurare gli indirizzi DNS primari e secondari del server. Questo non è un requisito generale di Firewall di Azure.

```azurepowershell
$NIC.DnsSettings.DnsServers.Add("209.244.0.3")
$NIC.DnsSettings.DnsServers.Add("209.244.0.4")
$NIC | Set-AzNetworkInterface
```

## <a name="test-the-firewall"></a>Testare il firewall

A questo punto testare il firewall per verificare che funzioni come previsto.

1. Prendere nota dell'indirizzo IP privato per la **lavorative Srv** macchina virtuale:

   ```
   $NIC.IpConfigurations.PrivateIpAddress
   ```

1. Connettere una sessione di Desktop remoto alla macchina virtuale **Srv-Jump** e accedere. Da qui, aprire una connessione desktop remoto per il **lavorative Srv** privata indirizzo IP e accesso.

3. Sul **lavorative SRV**, aprire una finestra di PowerShell ed eseguire i comandi seguenti:

   ```
   nslookup www.google.com
   nslookup www.microsoft.com
   ```

   Entrambi i comandi devono restituire le risposte, che mostra che le query DNS vengano attraverso il firewall.

1. Eseguire i comandi seguenti:

   ```
   Invoke-WebRequest -Uri http://www.google.com
   Invoke-WebRequest -Uri https://www.google.com

   Invoke-WebRequest -Uri http://www.microsoft.com
   Invoke-WebRequest -Uri https://www.microsoft.com
   ```

   Le richieste www.google.com dovrebbero avere esito positivo e devono avere esito negativo le richieste www.microsoft.com. Ciò dimostra che le regole del firewall funzionino come previsto.

A questo punto si è verificato che le regole del firewall funzionano:

* È possibile risolvere i nomi DNS con il server DNS esterno configurato.
* È possibile passare al nome di dominio completo consentito ma non agli altri.

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile mantenere le risorse di firewall per l'esercitazione successiva, o se non è più necessario, eliminare il **Test-FW-RG** gruppo di risorse per eliminare tutte le risorse relative a firewall:

```azurepowershell
Remove-AzResourceGroup -Name Test-FW-RG
```

## <a name="next-steps"></a>Passaggi successivi

* [Esercitazione: monitorare i log del Firewall di Azure](./tutorial-diagnostics.md)
