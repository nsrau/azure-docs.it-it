---
title: "Esercitazione: Proteggere l'hub virtuale con Azure PowerShell"
description: Questo articolo descrive come creare una rete WAN virtuale di Azure in un'area con Firewall di Azure abilitato nell'hub.
services: firewall-manager
author: jomore
ms.topic: tutorial
ms.service: firewall-manager
ms.date: 10/22/2020
ms.author: victorh
ms.openlocfilehash: d9d9da9cd01b4bb9b3cabcf069990cd3cbd38d38
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92428552"
---
# <a name="tutorial-secure-your-virtual-hub-using-azure-powershell"></a>Esercitazione: Proteggere l'hub virtuale con Azure PowerShell

In questa esercitazione si crea un'istanza di rete WAN virtuale con un hub virtuale in un'area, quindi si distribuisce un'istanza di Firewall di Azure nell'hub virtuale per proteggere la connettività. In questo esempio viene illustrata la connettività sicura tra reti virtuali. L'hub virtuale sicuro protegge il traffico tra reti virtuali e anche tra rami da sito a sito, da punto a sito o ExpressRoute.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Distribuire la rete WAN virtuale
> * Distribuire Firewall di Azure e configurare il routing personalizzato
> * Testare la connettività

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

- PowerShell 7

   Per questa esercitazione è necessario eseguire Azure PowerShell in locale in PowerShell 7. Per installare PowerShell 7, vedere [Migrazione da Windows PowerShell 5.1 a PowerShell 7](https://docs.microsoft.com/powershell/scripting/install/migrating-from-windows-powershell-51-to-powershell-7?view=powershell-7).
- Az.Network versione 3.2.0

    Se è disponibile il modulo Az.Network versione 3.4.0 o successiva, sarà necessario eseguire il downgrade per usare alcuni comandi di questa esercitazione. Per controllare la versione del modulo Az.Network, eseguire il comando `Get-InstalledModule -Name Az.Network`. Per disinstallare il modulo Az.Network, eseguire `Uninstall-Module -name az.network`. Per installare il modulo Az.Network 3.2.0, eseguire `Install-Module az.network -RequiredVersion 3.2.0 -force`.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="initial-virtual-wan-deployment"></a>Distribuzione iniziale della rete WAN virtuale

Come primo passaggio, è necessario impostare alcune variabili e creare il gruppo di risorse, l'istanza della rete WAN virtuale e l'hub virtuale:

```azurepowershell
# Variable definition
$RG = "vwan-rg"
$Location = "westeurope"
$VwanName = "vwan"
$HubName =  "hub1"
# Create Resource Group, Virtual WAN and Virtual Hub
New-AzResourceGroup -Name $RG -Location $Location
$Vwan = New-AzVirtualWan -Name $VwanName -ResourceGroupName $RG -Location $Location -AllowVnetToVnetTraffic -AllowBranchToBranchTraffic -VirtualWANType "Standard"
$Hub = New-AzVirtualHub -Name $HubName -ResourceGroupName $RG -VirtualWan $Vwan -Location $Location -AddressPrefix "192.168.1.0/24" -Sku "Standard"
```

Creare due reti virtuali e connetterle all'hub come spoke:

```azurepowershell
# Create Virtual Network
$Spoke1 = New-AzVirtualNetwork -Name "spoke1" -ResourceGroupName $RG -Location $Location -AddressPrefix "10.1.1.0/24"
$Spoke2 = New-AzVirtualNetwork -Name "spoke2" -ResourceGroupName $RG -Location $Location -AddressPrefix "10.1.2.0/24"
# Connect Virtual Network to Virtual WAN
$Spoke1Connection = New-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke1" -RemoteVirtualNetwork $Spoke1
$Spoke2Connection = New-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke2" -RemoteVirtualNetwork $Spoke2
```

A questo punto, è disponibile una rete WAN virtuale completamente funzionante che fornisce connettività Any-To-Any. Per migliorare la sicurezza, è necessario distribuire un'istanza di Firewall di Azure in ogni hub virtuale. Per gestire efficientemente l'istanza di Firewall di Azure nella rete WAN virtuale, è possibile usare i criteri firewall. Quindi in questo esempio viene creato anche un criterio firewall:

```azurepowershell
# New Firewall Policy
$FWPolicy = New-AzFirewallPolicy -Name "VwanFwPolicy" -ResourceGroupName $RG -Location $Location
# New Firewall Public IP
$AzFWPIPs = New-AzFirewallHubPublicIpAddress -Count 1
$AzFWHubIPs = New-AzFirewallHubIpAddress -PublicIP $AzFWPIPs
# New Firewall
$AzFW = New-AzFirewall -Name "azfw1" -ResourceGroupName $RG -Location $Location `
            -VirtualHubId $Hub.Id -FirewallPolicyId $FWPolicy.Id `
            -Sku AZFW_Hub -HubIPAddress $AzFWHubIPs
```

L'abilitazione della registrazione da Firewall di Azure a Monitoraggio di Azure è facoltativa, ma in questo esempio si usano i log di Firewall per dimostrare che il traffico attraversa il firewall:

```azurepowershell
# Optionally, enable looging of Azure Firewall to Azure Monitor
$LogWSName = "vwan-" + (Get-Random -Maximum 99999) + "-" + $RG
$LogWS = New-AzOperationalInsightsWorkspace -Location $Location -Name $LogWSName -Sku Standard -ResourceGroupName $RG
Set-AzDiagnosticSetting -ResourceId $AzFW.Id -Enabled $True -Category AzureFirewallApplicationRule, AzureFirewallNetworkRule -WorkspaceId $LogWS.ResourceId
```

## <a name="deploy-azure-firewall-and-configure-custom-routing"></a>Distribuire Firewall di Azure e configurare il routing personalizzato

A questo punto è disponibile un'istanza di Firewall di Azure nell'hub, ma è comunque necessario modificare il routing in modo che la rete WAN virtuale invii il traffico proveniente dalle reti virtuali e dai rami attraverso il firewall. A questo scopo, eseguire due passaggi:

1. Configurare tutte le connessioni di rete virtuale (e le connessioni ai rami, se presenti), in modo che vengano propagate alla tabella di route `None`. L'effetto di questa configurazione è che altre reti virtuali e rami non riconosceranno i prefissi e quindi non verranno raggiunte da alcun routing.
1. È ora possibile inserire route statiche nella tabella di route `Default` (in cui tutte le reti virtuali e tutti i rami sono associati per impostazione predefinita), in modo che tutto il traffico venga inviato a Firewall di Azure.

> [!NOTE]
> Questa è la configurazione distribuita quando si protegge la connettività dal portale di Azure con Gestione firewall di Azure

Iniziare con il primo passaggio per configurare le connessioni di rete virtuale da propagare alla tabella di route `None`:

```azurepowershell
# Configure Virtual Network connections in hub to propagate to None
$VnetRoutingConfig = $Spoke1Connection.RoutingConfiguration    # We take $Spoke1Connection as baseline for the future vnet config, all vnets will have an identical config
$NoneRT = Get-AzVhubRouteTable -ResourceGroupName $RG -HubName $HubName -Name "noneRouteTable"
$NewPropRT = @{}
$NewPropRT.Add('Id', $NoneRT.id)
$PropRTList = @()
$PropRTList += $NewPropRT
$VnetRoutingConfig.PropagatedRouteTables.Ids = $PropRTList
$VnetRoutingConfig.PropagatedRouteTables.Labels = @()
$Spoke1Connection = Update-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke1" -RoutingConfiguration $VnetRoutingConfig
$Spoke2Connection = Update-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke2" -RoutingConfiguration $VnetRoutingConfig
```

È ora possibile continuare con il secondo passaggio per aggiungere le route statiche alla tabella di route `Default`. In questo esempio viene applicata la configurazione predefinita che verrà generata da Gestione firewall di Azure per la protezione della connettività in una rete WAN virtuale, ma è possibile cambiare l'elenco dei prefissi nella route statica per soddisfare requisiti specifici:

```azurepowershell
# Create static routes in default Route table
$AzFWId = $(Get-AzVirtualHub -ResourceGroupName $RG -name  $HubName).AzureFirewall.Id
$AzFWRoute = New-AzVHubRoute -Name "private-traffic" -Destination @("0.0.0.0/0", "10.0.0.0/8", "172.16.0.0/12", "192.168.0.0/16") -DestinationType "CIDR" -NextHop $AzFWId -NextHopType "ResourceId"
$DefaultRT = Update-AzVHubRouteTable -Name "defaultRouteTable" -ResourceGroupName $RG -VirtualHubName  $HubName -Route @($AzFWRoute)
```

## <a name="test-connectivity"></a>Testare la connettività

A questo punto è disponibile un hub sicuro completamente operativo. Per testare la connettività, è necessario avere una macchina virtuale in ogni rete virtuale spoke connessa all'hub:

```azurepowershell
# Create VMs in spokes for testing
$VMLocalAdminUser = "lab-user"
$VMLocalAdminSecurePassword = ConvertTo-SecureString -AsPlainText -Force
$VMCredential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);
$VMSize = "Standard_B2ms"
# Spoke1
$Spoke1 = Get-AzVirtualNetwork -ResourceGroupName $RG -Name "spoke1"
Add-AzVirtualNetworkSubnetConfig -Name "vm" -VirtualNetwork $Spoke1 -AddressPrefix "10.1.1.0/26"
$Spoke1 | Set-AzVirtualNetwork
$VM1 = New-AzVM -Name "spoke1-vm" -ResourceGroupName $RG -Location $Location `
            -Image "UbuntuLTS" -credential $VMCredential `
            -VirtualNetworkName "spoke1" -SubnetName "vm" -PublicIpAddressName "spoke1-pip"
$NIC1 = Get-AzNetworkInterface -ResourceId $($VM1.NetworkProfile.NetworkInterfaces[0].Id)
$Spoke1VMPrivateIP = $NIC1.IpConfigurations[0].PrivateIpAddress
$Spoke1VMPIP = $(Get-AzPublicIpAddress -ResourceGroupName $RG -Name "spoke1-pip")
# Spoke2
$Spoke2 = Get-AzVirtualNetwork -ResourceGroupName $RG -Name "spoke2"
Add-AzVirtualNetworkSubnetConfig -Name "vm" -VirtualNetwork $Spoke2 -AddressPrefix "10.1.2.0/26"
$Spoke2 | Set-AzVirtualNetwork
$VM2 = New-AzVM -Name "spoke2-vm" -ResourceGroupName $RG -Location $Location `
            -Image "UbuntuLTS" -credential $VMCredential `
            -VirtualNetworkName "spoke2" -SubnetName "vm" -PublicIpAddressName "spoke2-pip"
$NIC2 = Get-AzNetworkInterface -ResourceId $($VM2.NetworkProfile.NetworkInterfaces[0].Id)
$Spoke2VMPrivateIP = $NIC2.IpConfigurations[0].PrivateIpAddress
$Spoke2VMPIP = $(Get-AzPublicIpAddress -ResourceGroupName $RG -Name "spoke2-pip")
```

La configurazione predefinita nei criteri firewall prevede l'eliminazione di tutto il traffico. Quindi è necessario configurare alcune regole. Iniziare con le regole DNAT, in modo che le macchine virtuali di test siano accessibili tramite l'indirizzo IP pubblico del firewall:

```azurepowershell
# Adding DNAT rules for virtual machines in the spokes
$AzFWPublicAddress = $AzFW.HubIPAddresses.PublicIPs.Addresses[0].Address
$NATRuleSpoke1 = New-AzFirewallPolicyNatRule -Name "Spoke1SSH" -Protocol "TCP" `
        -SourceAddress "*" -DestinationAddress $AzFWPublicAddress -DestinationPort 10001 `
        -TranslatedAddress $Spoke1VMPrivateIP -TranslatedPort 22
$NATRuleSpoke2 = New-AzFirewallPolicyNatRule -Name "Spoke2SSH" -Protocol "TCP" `
        -SourceAddress "*" -DestinationAddress $AzFWPublicAddress -DestinationPort 10002 `
        -TranslatedAddress $Spoke2VMPrivateIP -TranslatedPort 22
$NATCollection = New-AzFirewallPolicyNatRuleCollection -Name "SSH" -Priority 100 `
        -Rule @($NATRuleSpoke1, $NATRuleSpoke2) -ActionType "Dnat"
$NATGroup = New-AzFirewallPolicyRuleCollectionGroup -Name "NAT" -Priority 100 -RuleCollection $NATCollection -FirewallPolicyObject $FWPolicy
```

Ora è possibile configurare alcune regole di esempio. Definire una regola di rete che consenta il traffico SSH, oltre a una regola dell'applicazione che consenta l'accesso a Internet al nome di dominio completo `ifconfig.co`. Questo URL restituisce l'indirizzo IP di origine visualizzato nella richiesta HTTP:

```azurepowershell
# Add Network Rule
$SSHRule = New-AzFirewallPolicyNetworkRule -Name PermitSSH -Protocol TCP `
        -SourceAddress "10.0.0.0/8" -DestinationAddress "10.0.0.0/8" -DestinationPort 22
$NetCollection = New-AzFirewallPolicyFilterRuleCollection -Name "Management" -Priority 100 -ActionType Allow -Rule $SSHRule
$NetGroup = New-AzFirewallPolicyRuleCollectionGroup -Name "Management" -Priority 200 -RuleCollection $NetCollection -FirewallPolicyObject $FWPolicy
# Add Application Rul
$ifconfigRule = New-AzFirewallPolicyApplicationRule -Name PermitIfconfig -SourceAddress "10.0.0.0/8" -TargetFqdn "ifconfig.co" -Protocol "http:80","https:443"
$AppCollection = New-AzFirewallPolicyFilterRuleCollection -Name "TargetURLs" -Priority 300 -ActionType Allow -Rule $ifconfigRule
$NetGroup = New-AzFirewallPolicyRuleCollectionGroup -Name "TargetURLs" -Priority 300 -RuleCollection $AppCollection -FirewallPolicyObject $FWPolicy
```

Prima di inviare effettivamente il traffico, è possibile esaminare le route effettive delle macchine virtuali. Dovrebbero contenere i prefissi riconosciuti dalla WAN virtuale (`0.0.0.0/0` più RFC1918), ma non il prefisso dell'altro spoke:

```azurepowershell
# Check effective routes in the VM NIC in spoke 1
# Note that 10.1.2.0/24 (the prefix for spoke2) should not appear
Get-AzEffectiveRouteTable -ResourceGroupName $RG -NetworkInterfaceName $NIC1.Name | ft
# Check effective routes in the VM NIC in spoke 2
# Note that 10.1.1.0/24 (the prefix for spoke1) should not appear
Get-AzEffectiveRouteTable -ResourceGroupName $RG -NetworkInterfaceName $NIC2.Name | ft
```

Generare ora il traffico da una macchina virtuale all'altra e verificare che venga eliminato in Firewall di Azure. Nei comandi SSH seguenti è necessario accettare le impronte digitali delle macchine virtuali e fornire la password definita al momento della relativa creazione. In questo esempio si invieranno cinque pacchetti di richiesta echo ICMP dalla macchina virtuale in spoke1 a spoke2, oltre a eseguire un tentativo di connessione TCP sulla porta 22 con l'utilità Linux `nc` (con i flag `-vz` viene semplicemente inviata una richiesta di connessione e viene visualizzato il risultato). Si noterà che il ping non riesce, mentre il tentativo di connessione TCP sulla porta 22 riesce, perché è consentito dalla regola di rete configurata in precedenza:

```azurepowershell
# Connect to one VM and ping the other. It shouldnt work, because the firewall should drop the traffic, since no rule for ICMP is configured
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "ping $Spoke2VMPrivateIP -c 5"
# Connect to one VM and send a TCP request on port 22 to the other. It should work, because the firewall is configured to allow SSH traffic (port 22)
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "nc -vz $Spoke2VMPrivateIP 22"
```

È anche possibile verificare il traffico Internet. Le richieste HTTP tramite l'utilità `curl` al nome di dominio completo consentito nei criteri firewall (`ifconfig.co`) dovrebbero funzionare, ma le richieste HTTP inviate a qualsiasi altra destinazione avranno esito negativo (in questo esempio si esegue il test con `bing.com`):

```azurepowershell
# This HTTP request should succeed, since it is allowed in an app rule in the AzFW, and return the public IP of the FW
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "curl -s4 ifconfig.co"
# This HTTP request should fail, since the FQDN bing.com is not in any app rule in the firewall policy
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "curl -s4 bing.com"
```

Il modo più semplice per verificare se i pacchetti vengono eliminati dal firewall consiste nel controllare i log. Poiché Firewall di Azure è stato configurato per l'invio di log a Monitoraggio di Azure, è possibile usare il linguaggio di query Kusto per recuperare i log appropriati da Monitoraggio di Azure:

> [!NOTE]
> Può essere necessario circa 1 minuto prima che i log vengano inviati a Monitoraggio di Azure

```azurepowershell
# Getting Azure Firewall network rule Logs
$LogWS = Get-AzOperationalInsightsWorkspace -ResourceGroupName $RG
$LogQuery = 'AzureDiagnostics 
| where Category == "AzureFirewallNetworkRule" 
| where TimeGenerated >= ago(5m) 
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " to " TargetIP ":" TargetPortInt:int * 
| parse msg_s with * ". Action: " Action1a 
| parse msg_s with * " was " Action1b " to " NatDestination 
| parse msg_s with Protocol2 " request from " SourceIP2 " to " TargetIP2 ". Action: " Action2 
| extend SourcePort = tostring(SourcePortInt),TargetPort = tostring(TargetPortInt) 
| extend Action = case(Action1a == "", case(Action1b == "",Action2,Action1b), Action1a),Protocol = case(Protocol == "", Protocol2, Protocol),SourceIP = case(SourceIP == "", SourceIP2, SourceIP),TargetIP = case(TargetIP == "", TargetIP2, TargetIP),SourcePort = case(SourcePort == "", "N/A", SourcePort),TargetPort = case(TargetPort == "", "N/A", TargetPort),NatDestination = case(NatDestination == "", "N/A", NatDestination) 
| project TimeGenerated, Protocol, SourceIP,SourcePort,TargetIP,TargetPort,Action, NatDestination, Resource 
| take 25 '
$(Invoke-AzOperationalInsightsQuery -Workspace $LogWS -Query $LogQuery).Results | ft
```

Nel comando precedente dovrebbero essere visualizzate voci diverse:

* La connessione SSH sottoposta a regole DNAT
* I pacchetti ICMP eliminati tra le VM negli spoke (10.1.1.4 e 10.1.2.4)
* Le connessioni SSH consentite tra le VM negli spoke

Ecco un output di esempio prodotto dal comando precedente:

```
TimeGenerated            Protocol    SourceIP       SourcePort TargetIP      TargetPort Action  NatDestination Resource
-------------            --------    --------       ---------- --------      ---------- ------  -------------- --------
2020-10-04T20:53:02.41Z  TCP         109.125.122.99 62281      51.105.224.44 10001      DNAT'ed 10.1.1.4:22    AZFW1
2020-10-04T20:53:07.045Z TCP         10.1.1.4       35932      10.1.2.4      22         Allow   N/A            AZFW1
2020-10-04T20:53:50.119Z TCP         109.125.122.99 62293      51.105.224.44 10001      DNAT'ed 10.1.2.4:22    AZFW1
2020-10-04T20:52:47.475Z TCP         109.125.122.99 62273      51.105.224.44 10001      DNAT'ed 10.1.2.4:22    AZFW1
2020-10-04T20:51:04.682Z TCP         109.125.122.99 62200      51.105.224.44 10001      DNAT'ed 10.1.2.4:22    AZFW1
2020-10-04T20:51:17.031Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:18.049Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:19.075Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:20.097Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:21.121Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:52:52.356Z TCP         10.1.1.4       53748      10.1.2.4      22         Allow   N/A            AZFW1
```

Per visualizzare i log per le regole dell'applicazione, che includono le connessioni HTTP consentite e negate, o per cambiare la modalità di visualizzazione dei log, è possibile provare altre query KQL. Per altri esempi, vedere [Log di Monitoraggio di Azure per Firewall di Azure](../firewall/log-analytics-samples.md).


## <a name="clean-up-resources"></a>Pulire le risorse

Per eliminare l'ambiente di test, è possibile rimuovere il gruppo di risorse con tutti gli oggetti che contiene:

```azurepowershell
# Delete resource group and all contained resources
Remove-AzResourceGroup -Name $RG
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Vedere le informazioni sui partner di sicurezza affidabili](trusted-security-partners.md)