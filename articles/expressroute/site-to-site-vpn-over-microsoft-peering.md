---
title: Configurare una VPN da sito a sito tramite Microsoft peering per ExpressRoute di Azure | Documenti Microsoft
description: "Configurare la connettività IPsec/IKE in Azure tramite un circuito di peering ExpressRoute Microsoft tramite un gateway VPN da sito a sito."
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/06/2017
ms.author: cherylmc
ms.openlocfilehash: 64203e2cbac1206224f0e0ad8b7d364f19ad0332
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2017
---
# <a name="configure-a-site-to-site-vpn-over-expressroute-microsoft-peering"></a>Configurare una VPN da sito a sito tramite il peering ExpressRoute Microsoft

In questo articolo consente di configurare la connettività tra la rete locale e le reti virtuali di Azure (Vnet) crittografata protetta tramite una connessione privata ExpressRoute. La configurazione di un tunnel sicuro tramite ExpressRoute consente per lo scambio di dati con riservatezza, l'impossibilità di riproduzione, autenticità e l'integrità.

## <a name="architecture"></a>Architettura

È possibile utilizzare Microsoft peering per stabilire un tunnel VPN IPsec/IKE da sito a sito tra le reti locali selezionati e reti virtuali di Azure.

  ![informazioni generali sulla connettività](./media/site-to-site-vpn-over-microsoft-peering/IPsecER_Overview.png)

>[!NOTE]
>Quando si configura VPN da sito a sito tramite Microsoft peering, viene addebitato per il gateway VPN e in uscita VPN. Per ulteriori informazioni, vedere [Gateway VPN prezzi](https://azure.microsoft.com/pricing/details/vpn-gateway).
>
>

Per la disponibilità elevata e ridondanza, è possibile configurare più tunnel su due coppie MSEE PE di un circuito ExpressRoute e abilitare il bilanciamento del carico tra i tunnel.

  ![Opzioni di disponibilità elevata](./media/site-to-site-vpn-over-microsoft-peering/HighAvailability.png)

Tunnel VPN su peering Microsoft può essere terminato tramite gateway VPN, o un appropriato rete virtuale accessorio (NVA) disponibile tramite Azure Marketplace. È possibile scambiare le route collegato staticamente o dinamicamente attraverso i tunnel crittografati senza esporre lo scambio delle route per il peering Microsoft sottostante. Negli esempi in questo articolo, il protocollo BGP (diversi dalla sessione BGP utilizzata per creare il peering Microsoft) viene utilizzato per scambiare in modo dinamico i prefissi attraverso i tunnel crittografati.

>[!IMPORTANT]
>Per il lato locale, in genere peering Microsoft viene interrotta nella rete Perimetrale e peering privato viene terminata nella zona rete core. Due zone verranno isolate tramite i firewall. Se si configura Microsoft peering esclusivamente per l'abilitazione del tunneling protetto tramite ExpressRoute, è necessario filtrare solo gli indirizzi IP pubblici di interesse recupero annunciati tramite Microsoft peering.
>
>

## <a name="workflow"></a>Flusso di lavoro

1. Configurare Microsoft peering per il circuito ExpressRoute.
2. Annunciare selezionati Azure regionali prefissi pubblici per la rete locale tramite il peering Microsoft.
3. Configurare un gateway VPN e stabilire i tunnel IPsec
4. Configurare il dispositivo VPN locale.
5. Creare la connessione IPsec/IKE site-to-site.
6. (Facoltativo) Configurare il firewall/filtro sul dispositivo VPN locale.
7. Testare e convalidare le comunicazioni IPsec tramite il circuito ExpressRoute.

## <a name="peering"></a>1. configurare peering Microsoft

Per configurare una connessione VPN da sito a sito tramite ExpressRoute, è necessario sfruttare peering ExpressRoute Microsoft.

* Per configurare un nuovo circuito ExpressRoute, iniziare con il [prerequisiti di ExpressRoute](expressroute-prerequisites.md) articolo, quindi [creare e modificare un circuito ExpressRoute](expressroute-howto-circuit-arm.md).

* Se già di un circuito ExpressRoute, ma non Microsoft peering configurato, configurare peering Microsoft utilizzando il [creare e modificare peering per un circuito ExpressRoute](expressroute-howto-routing-arm.md#msft) articolo.

Dopo aver configurato il circuito e peering Microsoft, è possibile visualizzare facilmente tramite il **Panoramica** pagina nel portale di Azure.

![circuito](./media/site-to-site-vpn-over-microsoft-peering/ExpressRouteCkt.png)

## <a name="routefilter"></a>2. Configurare i filtri di route

Un filtro di route consente di identificare i servizi da usare tramite il peering Microsoft del circuito di ExpressRoute. È essenzialmente un elenco di tutti i valori di community BGP. 

![filtro di route](./media/site-to-site-vpn-over-microsoft-peering/route-filter.png)

In questo esempio, la distribuzione è solo nel *Azure Stati Uniti occidentali 2* area. Aggiunta di una regola di filtro di route per consentire solo l'annuncio di Azure Stati Uniti occidentali 2 internazionali prefissi, che ha il valore di community BGP *12076:51026*. Specificare i prefissi internazionali che si desidera consentire selezionando **Gestisci regole**.

All'interno del filtro di route, è inoltre necessario scegliere i circuiti ExpressRoute per il quale si applica il filtro di route. È possibile scegliere i circuiti ExpressRoute selezionando **aggiungere circuito**. Nella figura precedente, il filtro di route è associato al circuito ExpressRoute di esempio.

### <a name="configfilter"></a>2.1 configurare il filtro di route

Configurare un filtro di route. Per istruzioni, vedere [configura i filtri di route per il peering Microsoft](how-to-routefilter-portal.md).

### <a name="verifybgp"></a>2.2 verificare delle route BGP

Dopo aver creato Microsoft peering tramite il circuito ExpressRoute e associato un filtro di route del circuito, è possibile verificare le route BGP ricevuto da MSEEs sui dispositivi che sono peering con il MSEEs PE. Il comando verifica varia a seconda del sistema operativo dei dispositivi PE.

#### <a name="cisco-examples"></a>Esempi di Cisco

In questo esempio Usa un comando di Cisco IOS-XE. Nell'esempio, un routing virtuale e l'inoltro di istanza (VRF) viene utilizzato per isolare il traffico peering.

```
show ip bgp vpnv4 vrf 10 summary
```

L'output di parziale seguente viene mostrato che i 68 prefissi sono stati ricevuti dal router adiacente *.243.229.34 con ASN 12076 (MSEE):

```
...

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
X.243.229.34    4        12076   17671   17650    25228    0    0 1w4d           68
```

Per visualizzare l'elenco di prefissi ricevuti dal router adiacente, usare l'esempio seguente:

```
sh ip bgp vpnv4 vrf 10 neighbors X.243.229.34 received-routes
```

Per confermare che si riceve il set corretto di prefissi, è possibile cross-verificare. L'output del comando di Azure PowerShell seguente sono elencati i prefissi annunciati tramite Microsoft peering per ciascuno dei servizi e per ogni area di Azure:

```powershell
Get-AzureRmBgpServiceCommunity
```

## <a name="vpngateway"></a>3. Configurare il gateway VPN e tunnel IPsec

In questa sezione vengono creati i tunnel VPN IPsec tra il gateway VPN di Azure e il dispositivo VPN locale. Gli esempi usano dispositivi VPN Cisco Router per servizio Cloud (CSR1000).

Il diagramma seguente mostra la VPN IPsec tunnel stabilite tra il dispositivo VPN locale 1 e la coppia di istanza di gateway VPN di Azure. I due tunnel VPN IPsec stabilite tra il dispositivo VPN locale 2 e la coppia di istanza di gateway VPN di Azure non è illustrata nel diagramma non sono elencati i dettagli di configurazione. Tuttavia, con ulteriori tunnel VPN migliora la disponibilità elevata.

  ![Tunnel VPN](./media/site-to-site-vpn-over-microsoft-peering/EstablishTunnels.png)

Tramite la coppia di tunnel IPsec, viene stabilita una sessione di eBGP per scambiare le route di rete privata. Il diagramma seguente mostra la sessione di eBGP stabilita attraverso la coppia di tunnel IPsec:

  ![sessioni di eBGP sulla coppia di tunnel](./media/site-to-site-vpn-over-microsoft-peering/TunnelBGP.png)

Il diagramma seguente mostra la panoramica astratta della rete di esempio:

  ![rete di esempio](./media/site-to-site-vpn-over-microsoft-peering/OverviewRef.png)

### <a name="about-the-azure-resource-manager-template-examples"></a>Informazioni sugli esempi di modello di gestione risorse di Azure

Negli esempi, il gateway VPN e interruzioni impreviste dei tunnel IPsec vengono configurati utilizzando un modello di gestione risorse di Azure. Se si ha familiarità con l'utilizzo di modelli di gestione risorse, o per comprendere le nozioni di base di modello di gestione delle risorse, vedere [comprendere la struttura e la sintassi dei modelli di Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). In questa sezione viene creata una base ambiente Azure (VNet). Tuttavia, se si dispone di una rete virtuale di esistente, è possibile fare riferimento, nel modello. Se non si ha familiarità con le configurazioni di site-to-site VPN gateway IPsec/IKE, vedere [creare una connessione site-to-site](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md).

>[!NOTE]
>Non è necessario utilizzare i modelli di gestione risorse di Azure per creare questa configurazione. È possibile creare la configurazione utilizzando il portale di Azure o PowerShell.
>
>

### <a name="variables3"></a>3.1 dichiarare le variabili

In questo esempio, le dichiarazioni di variabili corrispondono per la rete di esempio. Quando si dichiarano variabili, è possibile modificare questa sezione per riflettere l'ambiente.

* La variabile **localAddressPrefix** è una matrice di indirizzi IP locale per terminare i tunnel IPsec.
* Il **gatewaySku** determina la velocità effettiva VPN. Per ulteriori informazioni su gatewaySku e vpnType, vedere [le impostazioni di configurazione di Gateway VPN](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku). Per i prezzi, vedere [Gateway VPN prezzi](https://azure.microsoft.com/pricing/details/vpn-gateway).
* Impostare il **vpnType** a **tipo RouteBased**.

```json
"variables": {
  "virtualNetworkName": "SecureVNet",       // Name of the Azure VNet
  "azureVNetAddressPrefix": "10.2.0.0/24",  // Address space assigned to the VNet
  "subnetName": "Tenant",                   // subnet name in which tenants exists
  "subnetPrefix": "10.2.0.0/25",            // address space of the tenant subnet
  "gatewaySubnetPrefix": "10.2.0.224/27",   // address space of the gateway subnet
  "localGatewayName": "localGW1",           // name of remote gateway (on-premises)
  "localGatewayIpAddress": "X.243.229.110", // public IP address of the on-premises VPN device
  "localAddressPrefix": [
    "172.16.0.1/32",                        // termination of IPsec tunnel-1 on-premises 
    "172.16.0.2/32"                         // termination of IPsec tunnel-2 on-premises 
  ],
  "gatewayPublicIPName1": "vpnGwVIP1",    // Public address name of the first VPN gateway instance
  "gatewayPublicIPName2": "vpnGwVIP2",    // Public address name of the second VPN gateway instance 
  "gatewayName": "vpnGw",                 // Name of the Azure VPN gateway
  "gatewaySku": "VpnGw1",                 // Azure VPN gateway SKU
  "vpnType": "RouteBased",                // type of VPN gateway
  "sharedKey": "string",                  // shared secret needs to match with on-premise configuration
  "asnVpnGateway": 65000,                 // BGP Autonomous System number assigned to the VPN Gateway 
  "asnRemote": 65010,                     // BGP Autonmous Syste number assigned to the on-premises device
  "bgpPeeringAddress": "172.16.0.3",      // IP address of the remote BGP peer on-premises
  "connectionName": "vpn2local1",
  "vnetID": "[resourceId('Microsoft.Network/virtualNetworks', variables('virtualNetworkName'))]",
  "gatewaySubnetRef": "[concat(variables('vnetID'),'/subnets/','GatewaySubnet')]",
  "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]",
  "api-version": "2017-06-01"
},
```

### <a name="vnet"></a>3.2 crea rete virtuale (VNet)

Se si desidera associare una rete virtuale di esistente con i tunnel VPN, è possibile ignorare questo passaggio.

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/virtualNetworks",
  "name": "[variables('virtualNetworkName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "[variables('azureVNetAddressPrefix')]"
      ]
    },
    "subnets": [
      {
        "name": "[variables('subnetName')]",
        "properties": {
          "addressPrefix": "[variables('subnetPrefix')]"
        }
      },
      {
        "name": "GatewaySubnet",
        "properties": {
          "addressPrefix": "[variables('gatewaySubnetPrefix')]"
        }
      }
    ]
  },
  "comments": "Create a Virtual Network with Subnet1 and Gatewaysubnet"
},
```

### <a name="ip"></a>3.3 assegnare indirizzi IP pubblici a istanze del gateway VPN
 
Assegnare un indirizzo IP pubblico per ogni istanza di un gateway VPN.

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/publicIPAddresses",
    "name": "[variables('gatewayPublicIPName1')]",
    "location": "[resourceGroup().location]",
    "properties": {
      "publicIPAllocationMethod": "Dynamic"
    },
    "comments": "Public IP for the first instance of the VPN gateway"
  },
  {
    "apiVersion": "[variables('api-version')]",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[variables('gatewayPublicIPName2')]",
    "location": "[resourceGroup().location]",
    "properties": {
      "publicIPAllocationMethod": "Dynamic"
    },
    "comments": "Public IP for the second instance of the VPN gateway"
  },
```

### <a name="termination"></a>3.4 specificare la terminazione di tunnel VPN locale (gateway di rete locale)

I dispositivi VPN locali vengono dette il **gateway di rete locale**. Il seguente frammento di codice json specifica inoltre i dettagli di peer BGP remoti:

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/localNetworkGateways",
  "name": "[variables('localGatewayName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "localNetworkAddressSpace": {
      "addressPrefixes": "[variables('localAddressPrefix')]"
    },
    "gatewayIpAddress": "[variables('localGatewayIpAddress')]",
    "bgpSettings": {
      "asn": "[variables('asnRemote')]",
      "bgpPeeringAddress": "[variables('bgpPeeringAddress')]",
      "peerWeight": 0
    }
  },
  "comments": "Local Network Gateway (referred to your on-premises location) with IP address of remote tunnel peering and IP address of remote BGP peer"
},
```

### <a name="creategw"></a>3.5 creare il gateway VPN

In questa sezione del modello consente di configurare il gateway VPN con le impostazioni necessarie per una configurazione attivo-attivo. Tenere presente quanto segue:

* Creazione di un gateway VPN con un **"Tipo RouteBased"** VpnType. Questa impostazione è obbligatoria se si desidera abilitare il routing BGP tra il gateway VPN e la VPN locale.
* Per stabilire il tunnel VPN tra le due istanze del gateway VPN e un dispositivo locale specificato in modalità attivo-attivo, il **"activeActive"** parametro è impostato su **true** nel modello di gestione risorse . Per ulteriori informazioni sui gateway VPN a disponibilità elevata, vedere [connettività a disponibilità elevata di gateway VPN](../vpn-gateway/vpn-gateway-highlyavailable.md).
* Per configurare le sessioni di eBGP tra i tunnel VPN, è necessario specificare due ASN diversi su entrambi i lati. È preferibile specificare numeri ASN privati. Per ulteriori informazioni, vedere [gateway Panoramica di BGP e VPN di Azure](../vpn-gateway/vpn-gateway-bgp-overview.md).

```json
{
"apiVersion": "[variables('api-version')]",
"type": "Microsoft.Network/virtualNetworkGateways",
"name": "[variables('gatewayName')]",
"location": "[resourceGroup().location]",
"dependsOn": [
  "[concat('Microsoft.Network/publicIPAddresses/', variables('gatewayPublicIPName1'))]",
  "[concat('Microsoft.Network/publicIPAddresses/', variables('gatewayPublicIPName2'))]",
  "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
],
"properties": {
  "ipConfigurations": [
    {
      "properties": {
        "privateIPAllocationMethod": "Dynamic",
        "subnet": {
          "id": "[variables('gatewaySubnetRef')]"
        },
        "publicIPAddress": {
          "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('gatewayPublicIPName1'))]"
        }
      },
      "name": "vnetGtwConfig1"
    },
    {
      "properties": {
        "privateIPAllocationMethod": "Dynamic",
        "subnet": {
          "id": "[variables('gatewaySubnetRef')]"
        },
        "publicIPAddress": {
          "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('gatewayPublicIPName2'))]"
        }
      },
          "name": "vnetGtwConfig2"
        }
      ],
      "sku": {
        "name": "[variables('gatewaySku')]",
        "tier": "[variables('gatewaySku')]"
      },
      "gatewayType": "Vpn",
      "vpnType": "[variables('vpnType')]",
      "enableBgp": true,
      "activeActive": true,
      "bgpSettings": {
        "asn": "[variables('asnVpnGateway')]"
      }
    },
    "comments": "VPN Gateway in active-active configuration with BGP support"
  },
```

### <a name="ipsectunnel"></a>3.6 stabilire il tunnel IPsec

L'azione finale dello script crea il tunnel IPsec tra il gateway VPN di Azure e il dispositivo VPN locale.

```json
{
  "apiVersion": "[variables('api-version')]",
  "name": "[variables('connectionName')]",
  "type": "Microsoft.Network/connections",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Network/virtualNetworkGateways/', variables('gatewayName'))]",
    "[concat('Microsoft.Network/localNetworkGateways/', variables('localGatewayName'))]"
  ],
  "properties": {
    "virtualNetworkGateway1": {
      "id": "[resourceId('Microsoft.Network/virtualNetworkGateways', variables('gatewayName'))]"
    },
    "localNetworkGateway2": {
      "id": "[resourceId('Microsoft.Network/localNetworkGateways', variables('localGatewayName'))]"
    },
    "connectionType": "IPsec",
    "routingWeight": 0,
    "sharedKey": "[variables('sharedKey')]",
    "enableBGP": "true"
  },
  "comments": "Create a Connection type site-to-site (IPsec) between the Azure VPN Gateway and the VPN device on-premises"
  }
```

## <a name="device"></a>4. Configurare il dispositivo VPN locale

Il gateway VPN di Azure è compatibile con molti dispositivi VPN di fornitori diversi. Per informazioni sulla configurazione e i dispositivi che sono stati convalidati per l'utilizzo con gateway VPN, vedere [informazioni sui dispositivi VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md).

Quando si configura il dispositivo VPN, è necessario quanto segue:

* Chiave condivisa. Questa è la stessa chiave condivisa specificate al momento della creazione della connessione VPN da sito a sito. Gli esempi usano una chiave condivisa di base. È consigliabile generare una chiave più complessa per l'uso effettivo.
* L'indirizzo IP pubblico del gateway VPN. È possibile visualizzare l'indirizzo IP pubblico usando il portale di Azure, PowerShell o l'interfaccia della riga di comando. Per trovare l'indirizzo IP pubblico del gateway VPN tramite il portale di Azure, passare al gateway di rete virtuale, quindi fare clic sul nome del gateway.

In genere peer eBGP direttamente connessi (spesso tramite una connessione WAN). Tuttavia, quando si configura tramite il tunnel VPN IPsec tramite Microsoft ExpressRoute di peering eBGP, esistono più domini di routing tra i peer eBGP. Utilizzare il **ebgp multihop** comando per stabilire la relazione adiacente eBGP tra i due non-direttamente connessi peer. Il valore integer che segue ebgp multihop comando specifica il valore di durata (TTL) nei pacchetti BGP. Il comando **eibgp i percorsi di massimo 2** consente il bilanciamento del carico del traffico tra i due percorsi BGP.

### <a name="cisco1"></a>Esempio CSR1000 Cisco

Nell'esempio seguente viene illustrata la configurazione per Cisco CSR1000 in una macchina virtuale Hyper-V del dispositivo VPN locale:

```
!
crypto ikev2 proposal az-PROPOSAL
 encryption aes-cbc-256 aes-cbc-128 3des
 integrity sha1
 group 2
!
crypto ikev2 policy az-POLICY
 proposal az-PROPOSAL
!
crypto ikev2 keyring key-peer1
 peer azvpn1
  address 52.175.253.112
  pre-shared-key secret*1234
 !
!
crypto ikev2 keyring key-peer2
 peer azvpn2
  address 52.175.250.191
  pre-shared-key secret*1234
 !
!
!
crypto ikev2 profile az-PROFILE1
 match address local interface GigabitEthernet1
 match identity remote address 52.175.253.112 255.255.255.255
 authentication remote pre-share
 authentication local pre-share
 keyring local key-peer1
!
crypto ikev2 profile az-PROFILE2
 match address local interface GigabitEthernet1
 match identity remote address 52.175.250.191 255.255.255.255
 authentication remote pre-share
 authentication local pre-share
 keyring local key-peer2
!
crypto ikev2 dpd 10 2 on-demand
!
!
crypto ipsec transform-set az-IPSEC-PROPOSAL-SET esp-aes 256 esp-sha-hmac
 mode tunnel
!
crypto ipsec profile az-VTI1
 set transform-set az-IPSEC-PROPOSAL-SET
 set ikev2-profile az-PROFILE1
!
crypto ipsec profile az-VTI2
 set transform-set az-IPSEC-PROPOSAL-SET
 set ikev2-profile az-PROFILE2
!
!
interface Loopback0
 ip address 172.16.0.3 255.255.255.255
!
interface Tunnel0
 ip address 172.16.0.1 255.255.255.255
 ip tcp adjust-mss 1350
 tunnel source GigabitEthernet1
 tunnel mode ipsec ipv4
 tunnel destination 52.175.253.112
 tunnel protection ipsec profile az-VTI1
!
interface Tunnel1
 ip address 172.16.0.2 255.255.255.255
 ip tcp adjust-mss 1350
 tunnel source GigabitEthernet1
 tunnel mode ipsec ipv4
 tunnel destination 52.175.250.191
 tunnel protection ipsec profile az-VTI2
!
interface GigabitEthernet1
 description External interface
 ip address x.243.229.110 255.255.255.252
 negotiation auto
 no mop enabled
 no mop sysid
!
interface GigabitEthernet2
 ip address 10.0.0.1 255.255.255.0
 negotiation auto
 no mop enabled
 no mop sysid
!
router bgp 65010
 bgp router-id interface Loopback0
 bgp log-neighbor-changes
 network 10.0.0.0 mask 255.255.255.0
 network 10.1.10.0 mask 255.255.255.128
 neighbor 10.2.0.228 remote-as 65000
 neighbor 10.2.0.228 ebgp-multihop 5
 neighbor 10.2.0.228 update-source Loopback0
 neighbor 10.2.0.228 soft-reconfiguration inbound
 neighbor 10.2.0.228 filter-list 10 out
 neighbor 10.2.0.229 remote-as 65000    
 neighbor 10.2.0.229 ebgp-multihop 5
 neighbor 10.2.0.229 update-source Loopback0
 neighbor 10.2.0.229 soft-reconfiguration inbound
 maximum-paths eibgp 2
!
ip route 0.0.0.0 0.0.0.0 10.1.10.1
ip route 10.2.0.228 255.255.255.255 Tunnel0
ip route 10.2.0.229 255.255.255.255 Tunnel1
!
```

## <a name="firewalls"></a>5. Configurare il filtraggio dei dispositivi VPN e firewall (facoltativo)

Configurare il firewall e filtri in base alle esigenze.

## <a name="testipsec"></a>6. Testare e convalidare il tunnel IPsec

Lo stato dei tunnel IPsec può essere verificato nel gateway VPN di Azure tramite i comandi di Powershell:

```powershell
Get-AzureRmVirtualNetworkGatewayConnection -Name vpn2local1 -ResourceGroupName myRG | Select-Object  ConnectionStatus,EgressBytesTransferred,IngressBytesTransferred | fl
```

Output di esempio:

```powershell
ConnectionStatus        : Connected
EgressBytesTransferred  : 17734660
IngressBytesTransferred : 10538211
```

Per controllare lo stato dei tunnel nelle istanze del gateway VPN di Azure in modo indipendente, utilizzare l'esempio seguente:

```powershell
Get-AzureRmVirtualNetworkGatewayConnection -Name vpn2local1 -ResourceGroupName myRG | Select-Object -ExpandProperty TunnelConnectionStatus
```

Output di esempio:

```powershell
Tunnel                           : vpn2local1_52.175.250.191
ConnectionStatus                 : Connected
IngressBytesTransferred          : 4877438
EgressBytesTransferred           : 8754071
LastConnectionEstablishedUtcTime : 11/04/2017 17:03:30

Tunnel                           : vpn2local1_52.175.253.112
ConnectionStatus                 : Connected
IngressBytesTransferred          : 5660773
EgressBytesTransferred           : 8980589
LastConnectionEstablishedUtcTime : 11/04/2017 17:03:13
```

È inoltre possibile controllare lo stato di tunnel sul dispositivo VPN locale.

Esempio di Cisco CSR1000:

```
show crypto session detail
show crypto ikev2 sa
show crypto ikev2 session detail
show crypto ipsec sa
```

Output di esempio:

```
csr1#show crypto session detail

Crypto session current status

Code: C - IKE Configuration mode, D - Dead Peer Detection
K - Keepalives, N - NAT-traversal, T - cTCP encapsulation
X - IKE Extended Authentication, F - IKE Fragmentation
R - IKE Auto Reconnect

Interface: Tunnel1
Profile: az-PROFILE2
Uptime: 00:52:46
Session status: UP-ACTIVE
Peer: 52.175.250.191 port 4500 fvrf: (none) ivrf: (none)
      Phase1_id: 52.175.250.191
      Desc: (none)
  Session ID: 3
  IKEv2 SA: local 10.1.10.50/4500 remote 52.175.250.191/4500 Active
          Capabilities:DN connid:3 lifetime:23:07:14
  IPSEC FLOW: permit ip 0.0.0.0/0.0.0.0 0.0.0.0/0.0.0.0
        Active SAs: 2, origin: crypto map
        Inbound:  #pkts dec'ed 279 drop 0 life (KB/Sec) 4607976/433
        Outbound: #pkts enc'ed 164 drop 0 life (KB/Sec) 4607992/433

Interface: Tunnel0
Profile: az-PROFILE1
Uptime: 00:52:43
Session status: UP-ACTIVE
Peer: 52.175.253.112 port 4500 fvrf: (none) ivrf: (none)
      Phase1_id: 52.175.253.112
      Desc: (none)
  Session ID: 2
  IKEv2 SA: local 10.1.10.50/4500 remote 52.175.253.112/4500 Active
          Capabilities:DN connid:2 lifetime:23:07:17
  IPSEC FLOW: permit ip 0.0.0.0/0.0.0.0 0.0.0.0/0.0.0.0
        Active SAs: 2, origin: crypto map
        Inbound:  #pkts dec'ed 668 drop 0 life (KB/Sec) 4607926/437
        Outbound: #pkts enc'ed 477 drop 0 life (KB/Sec) 4607953/437
```

Il protocollo di riga in virtuale Tunnel interfaccia (VTI) non cambia per "backup" fino a quando non IKE fase 2 è stata completata. Il comando seguente verifica l'associazione di sicurezza:

```
csr1#show crypto ikev2 sa

IPv4 Crypto IKEv2  SA

Tunnel-id Local                 Remote                fvrf/ivrf            Status
2         10.1.10.50/4500       52.175.253.112/4500   none/none            READY
      Encr: AES-CBC, keysize: 256, PRF: SHA1, Hash: SHA96, DH Grp:2, Auth sign: PSK, Auth verify: PSK
      Life/Active Time: 86400/3277 sec

Tunnel-id Local                 Remote                fvrf/ivrf            Status
3         10.1.10.50/4500       52.175.250.191/4500   none/none            READY
      Encr: AES-CBC, keysize: 256, PRF: SHA1, Hash: SHA96, DH Grp:2, Auth sign: PSK, Auth verify: PSK
      Life/Active Time: 86400/3280 sec

IPv6 Crypto IKEv2  SA

csr1#show crypto ipsec sa | inc encaps|decaps
    #pkts encaps: 177, #pkts encrypt: 177, #pkts digest: 177
    #pkts decaps: 296, #pkts decrypt: 296, #pkts verify: 296
    #pkts encaps: 554, #pkts encrypt: 554, #pkts digest: 554
    #pkts decaps: 746, #pkts decrypt: 746, #pkts verify: 746
```

### <a name="verifye2e"></a>Verificare la connettività end-to-end tra all'interno di rete locale e la rete virtuale di Azure

Se i tunnel IPsec siano attivi e le route statiche sono state impostate correttamente, è necessario eseguire il ping dell'indirizzo IP del peer BGP remoti:

```
csr1#ping 10.2.0.228
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.2.0.228, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 5/5/5 ms

#ping 10.2.0.229
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.2.0.229, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 4/5/6 ms
```

### <a name="verifybgp"></a>Verificare le sessioni BGP su IPsec

Nel gateway VPN di Azure, verificare lo stato del peer BGP:

```powershell
Get-AzureRmVirtualNetworkGatewayBGPPeerStatus -VirtualNetworkGatewayName vpnGtw -ResourceGroupName SEA-C1-VPN-ER | ft
```

Output di esempio:

```powershell
  Asn ConnectedDuration LocalAddress MessagesReceived MessagesSent Neighbor    RoutesReceived State    
  --- ----------------- ------------ ---------------- ------------ --------    -------------- -----    
65010 00:57:19.9003584  10.2.0.228               68           72   172.16.0.10              2 Connected
65000                   10.2.0.228                0            0   10.2.0.228               0 Unknown  
65000 07:13:51.0109601  10.2.0.228              507          500   10.2.0.229               6 Connected
```

Per verificare l'elenco di prefissi di rete ricevuti tramite eBGP da VPN concentratore locale, è possibile filtrare per "Origine" attributo:

```powershell
Get-AzureRmVirtualNetworkGatewayLearnedRoute -VirtualNetworkGatewayName vpnGtw -ResourceGroupName myRG  | Where-Object Origin -eq "EBgp" |ft
```

Nell'esempio di output, 65010 il numero ASN è il numero sistema autonomo BGP VPN locale.

```powershell
AsPath LocalAddress Network      NextHop     Origin SourcePeer  Weight
------ ------------ -------      -------     ------ ----------  ------
65010  10.2.0.228   10.1.10.0/25 172.16.0.10 EBgp   172.16.0.10  32768
65010  10.2.0.228   10.0.0.0/24  172.16.0.10 EBgp   172.16.0.10  32768
```

Per visualizzare l'elenco di route annunciate:

```powershell
Get-AzureRmVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName vpnGtw -ResourceGroupName myRG -Peer 10.2.0.228 | ft
```

Output di esempio:

```powershell
AsPath LocalAddress Network        NextHop    Origin SourcePeer Weight
------ ------------ -------        -------    ------ ---------- ------
       10.2.0.229   10.2.0.0/24    10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.10/32 10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.5/32  10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.1/32  10.2.0.229 Igp                  0
65010  10.2.0.229   10.1.10.0/25   10.2.0.229 Igp                  0
65010  10.2.0.229   10.0.0.0/24    10.2.0.229 Igp                  0
```

Esempio per il CSR1000 Cisco locale:

```
csr1#show ip bgp neighbors 10.2.0.228 routes
BGP table version is 7, local router ID is 172.16.0.10
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter,
              x best-external, a additional-path, c RIB-compressed,
              t secondary path,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>   10.2.0.0/24      10.2.0.228                             0 65000 i
 r>   172.16.0.1/32    10.2.0.228                             0 65000 i
 r>   172.16.0.2/32    10.2.0.228                             0 65000 i
 r>   172.16.0.3/32   10.2.0.228                             0 65000 i

Total number of prefixes 4
```

Elenco delle reti annunciato dal CSR1000 Cisco locale per il gateway VPN di Azure può essere elencato con il comando seguente:

```powershell
csr1#show ip bgp neighbors 10.2.0.228 advertised-routes
BGP table version is 7, local router ID is 172.16.0.10
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter,
              x best-external, a additional-path, c RIB-compressed,
              t secondary path,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>   10.0.0.0/24      0.0.0.0                  0         32768 i
 *>   10.1.10.0/25     0.0.0.0                  0         32768 i

Total number of prefixes 2
```

## <a name="next-steps"></a>Passaggi successivi

* [Configurare Monitoraggio prestazioni rete per ExpressRoute](how-to-npm.md)

* [Aggiungere una connessione site-to-site a una rete virtuale con una connessione gateway VPN esistente](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)