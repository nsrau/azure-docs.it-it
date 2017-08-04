---
title: Configurazione di un dispositivo VPN di terze parti per connettersi a gateway VPN di Azure | Microsoft Docs
description: Questo articolo offre una panoramica delle configurazioni di dispositivi VPN di terze parti per la connessione a gateway VPN di Azure.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: 
ms.assetid: a8bfc955-de49-4172-95ac-5257e262d7ea
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/20/2017
ms.author: yushwang
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 72dab85bb882b05d72cef26bef70437695b70416
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="overview-of-3rd-party-vpn-device-configurations"></a>Panoramica delle configurazioni di dispositivi VPN di terze parti
Questo articolo offre una panoramica delle configurazioni di dispositivi VPN locali per la connessione a gateway VPN di Azure. La configurazione di esempio della rete virtuale di Azure e del gateway VPN verrà usata per connettersi a diversi dispositivi VPN locali con gli stessi parametri.

## <a name="device-requirements"></a>Requisiti del dispositivo
I gateway VPN di Azure usano gruppi di protocollo IPsec/IKE standard per i tunnel VPN S2S. Fare riferimento a [Informazioni sui dispositivi VPN](vpn-gateway-about-vpn-devices.md) per i parametri di protocollo IPsec/IKE dettagliati e gli algoritmi di crittografia predefinita per i gateway VPN di Azure. È possibile specificare facoltativamente l'esatta combinazione di algoritmi di crittografia e la complessità della chiave per una connessione specifica come descritto in [Informazioni sui requisiti di crittografia](vpn-gateway-about-compliance-crypto.md).

## <a name ="singletunnel"></a>Tunnel per VPN unico
La prima topologia consiste in un unico tunnel per VPN S2S tra un gateway VPN di Azure e il dispositivo VPN locale. Se necessario, è possibile configurare BGP nel tunnel VPN.

![tunnel unico](./media/vpn-gateway-3rdparty-device-config-overview/singletunnel.png)

Fare riferimento a [Creare una connessione da sito a sito nel portale di Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md) per indicazioni dettagliate. Nelle sezioni seguenti sono elencati i parametri ed è riportato uno script di PowerShell di esempio per iniziare.

### <a name="network-and-vpn-gateway-information"></a>Informazioni sul gateway di rete e VPN
Questa sezione elenca i parametri per gli esempi sopra riportati.

| **Parametro**                | **Valore**                    |
| ---                          | ---                          |
| Prefissi di indirizzi di rete virtuale        | 10.11.0.0/16<br>10.12.0.0/16 |
| Indirizzo IP del gateway VPN di Azure         | Indirizzo IP del gateway VPN di Azure         |
| Prefissi di indirizzi locali | 10.51.0.0/16<br>10.52.0.0/16 |
| Indirizzo IP del dispositivo VPN locale    | Indirizzo IP del dispositivo VPN locale    |
| *BGP ASN della rete virtuale                | 65010                        |
| *Indirizzo IP del peer BGP di Azure           | 10.12.255.30                 |
| *ASN BGP locale         | 65050                        |
| *Indirizzo IP del peer BGP locale     | 10.52.255.254                |

* (*) Parametri facoltativi solo per BGP

### <a name="sample-powershell-script"></a>Script PowerShell di esempio
In [Creare una rete virtuale con una connessione VPN da sito a sito usando PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md) sono disponibili istruzioni dettagliate. In questa sezione è riportato uno script di esempio per iniziare.

```powershell
# Declare your variables

$Sub1          = "Replace_With_Your_Subcription_Name"
$RG1           = "TestRG1"
$Location1     = "East US 2"
$VNetName1     = "TestVNet1"
$FESubName1    = "FrontEnd"
$BESubName1    = "Backend"
$GWSubName1    = "GatewaySubnet"
$VNetPrefix11  = "10.11.0.0/16"
$VNetPrefix12  = "10.12.0.0/16"
$FESubPrefix1  = "10.11.0.0/24"
$BESubPrefix1  = "10.12.0.0/24"
$GWSubPrefix1  = "10.12.255.0/27"
$VNet1ASN      = 65010
$DNS1          = "8.8.8.8"
$GWName1       = "VNet1GW"
$GWIPName1     = "VNet1GWIP"
$GWIPconfName1 = "gwipconf1"
$Connection15  = "VNet1toSite5"
$LNGName5      = "Site5"
$LNGPrefix50   = "10.52.255.254/32"
$LNGPrefix51   = "10.51.0.0/16"
$LNGPrefix52   = "10.52.0.0/16"
$LNGIP5        = "Your_VPN_Device_IP"
$LNGASN5       = 65050
$BGPPeerIP5    = "10.52.255.254"

# Connect to your subscription and create a new resource group

Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1

# Create virtual network

$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1 $besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

# Create VPN gateway

$gwpip1    = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1     = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1

New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN

# Create local network gateway

New-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix51,$LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5

# Create the S2S VPN connection

$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False
```

### <a name ="policybased"></a>[Facoltativo] Usare criteri IPsec/IKE personalizzati con "UsePolicyBasedTrafficSelectors"
Se i dispositivi VPN non supportano selettori di traffico "any-to-any" (configurazione basata su route/basata su VTI), è necessario creare criteri IPsec/IKE personalizzati e configurare l'opzione "UsePolicyBasedTrafficSelectors" come descritto in [questo articolo](vpn-gateway-connect-multiple-policybased-rm-ps.md).

> [!IMPORTANT]
> È necessario creare un criterio IPsec/IKE per abilitare l'opzione "UsePolicyBasedTrafficSelectors" nella connessione.

Lo script di esempio seguente crea un criterio IPsec/IKE con gli algoritmi e i parametri seguenti:
* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA1, PFS24, durata dell'associazione di sicurezza 7200 secondi e 20480000KB (20 GB)

Applica quindi i criteri e abilita "UesPolicyBasedTrafficSelectors" nella connessione.

```powershell
$ipsecpolicy5 = New-AzureRmIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA1 -PfsGroup PFS24 -SALifeTimeSeconds 7200 -SADataSizeKilobytes 20480000

$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False -IpsecPolicies $ipsecpolicy5 -UsePolicyBasedTrafficSelectors $True
```

### <a name ="bgp"></a>[Facoltativo] Usare BGP per la connessione VPN da sito a sito
Facoltativamente, è possibile usare il protocollo BGP per la connessione. Vedere [BGP per gateway VPN](vpn-gateway-bgp-resource-manager-ps.md). Sono presenti due differenze:

I prefissi di indirizzi locali possono essere costituiti da un indirizzo a singolo host, l'indirizzo IP del peer BGP locale:

```powershell
New-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
```

Quando si crea la connessione, è necessario impostare "-EnableBGP" su $True:

```powershell
New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

## <a name="next-steps"></a>Passaggi successivi
Per informazioni sulla procedura per configurare connessioni cross-premise e da rete virtuale a rete virtuale di tipo attivo/attivo, vedere [Configurazione di gateway VPN di tipo attivo/attivo per connessioni cross-premise e da rete virtuale a rete virtuale](vpn-gateway-activeactive-rm-powershell.md) .


