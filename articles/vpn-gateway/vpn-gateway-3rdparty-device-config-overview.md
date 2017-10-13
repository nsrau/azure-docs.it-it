---
title: Configurazioni di dispositivi VPN di partner per la connessione ai gateway VPN di Azure | Microsoft Docs
description: Questo articolo offre una panoramica delle configurazioni di dispositivi VPN di partner per la connessione ai gateway VPN di Azure.
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
ms.openlocfilehash: b3806d16d3b78347e183ecbd2ab5a463a2142110
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-partner-vpn-device-configurations"></a>Panoramica delle configurazioni di dispositivi VPN di partner
L'articolo offre una panoramica della configurazione di dispositivi VPN locali per la connessione ai gateway VPN di Azure. Viene usata una configurazione di rete virtuale e di gateway VPN di Azure di esempio per mostrare come connettersi a diverse configurazioni di dispositivi VPN locali usando gli stessi parametri.

## <a name="device-requirements"></a>Requisiti del dispositivo
I gateway VPN di Azure usano suite di protocolli IPsec/IKE standard per i tunnel per VPN da sito a sito (S2S). Per un elenco dei parametri IPsec/IKE e degli algoritmi di crittografia per i gateway VPN di Azure, vedere [Informazioni sui dispositivi VPN](vpn-gateway-about-vpn-devices.md). È anche possibile specificare gli esatti algoritmi e le attendibilità delle chiavi per una connessione specifica come descritto in [Informazioni sui requisiti di crittografia](vpn-gateway-about-compliance-crypto.md).

## <a name ="singletunnel"></a>Tunnel per VPN unico
La prima configurazione dell'esempio consiste in un singolo tunnel per VPN S2S tra un gateway VPN di Azure e un dispositivo VPN locale. È facoltativamente possibile configurare il [protocollo BGP (Border Gateway Protocol) nel tunnel per VPN](#bgp).

![Diagramma di un singolo tunnel per VPN S2S](./media/vpn-gateway-3rdparty-device-config-overview/singletunnel.png)

Per istruzioni dettagliate su come configurare un singolo tunnel per VPN, vedere [Creare una connessione da sito a sito](vpn-gateway-howto-site-to-site-resource-manager-portal.md). Le sezioni seguenti specificano i parametri di connessione per la configurazione di esempio e includono uno script di PowerShell per iniziare.

### <a name="connection-parameters"></a>Parametri di connessione
Questa sezione elenca i parametri per gli esempi descritti nelle sezioni precedenti.

| **Parametro**                | **Valore**                    |
| ---                          | ---                          |
| Prefissi di indirizzi di rete virtuale        | 10.11.0.0/16<br>10.12.0.0/16 |
| Indirizzo IP del gateway VPN di Azure         | Indirizzo IP del gateway VPN di Azure         |
| Prefissi di indirizzi locali | 10.51.0.0/16<br>10.52.0.0/16 |
| Indirizzo IP del dispositivo VPN locale    | Indirizzo IP del dispositivo VPN locale    |
| * ASN BGP di rete virtuale                | 65010                        |
| * Indirizzo IP del peer BGP di Azure           | 10.12.255.30                 |
| * ASN BGP locale         | 65050                        |
| * Indirizzo IP del peer BGP locale     | 10.52.255.254                |

\* Parametro facoltativo solo per BGP.

### <a name="sample-powershell-script"></a>Script PowerShell di esempio
In questa sezione è riportato uno script di esempio per iniziare. Per istruzioni dettagliate, vedere [Creare una connessione VPN S2S usando PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md).

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

### <a name ="policybased"></a>(Facoltativo) Usare criteri IPsec/IKE personalizzati con l'opzione UsePolicyBasedTrafficSelectors
Se i dispositivi VPN non supportano selettori di traffico "any-to-any", ad esempio le configurazioni basate su route o VTI, creare criteri IPsec/IKE personalizzati con l'opzione [UsePolicyBasedTrafficSelectors](vpn-gateway-connect-multiple-policybased-rm-ps.md).

> [!IMPORTANT]
> È necessario creare criteri IPsec/IKE per abilitare l'opzione **UsePolicyBasedTrafficSelectors** nella connessione.


Lo script di esempio crea criteri IPsec/IKE con gli algoritmi e i parametri seguenti:
* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA1, PFS24, durata dell'associazione di sicurezza 7.200 secondi e 20.480.000 KB (20 GB)

Lo script applica i criteri IPsec/IKE e abilita l'opzione **UsePolicyBasedTrafficSelectors** nella connessione.

```powershell
$ipsecpolicy5 = New-AzureRmIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA1 -PfsGroup PFS24 -SALifeTimeSeconds 7200 -SADataSizeKilobytes 20480000

$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False -IpsecPolicies $ipsecpolicy5 -UsePolicyBasedTrafficSelectors $True
```

### <a name ="bgp"></a>(Facoltativo) Usare il protocollo BGP nella connessione VPN S2S
Quando si crea la connessione VPN S2S, è facoltativamente possibile usare il protocollo [BGP per il gateway VPN](vpn-gateway-bgp-resource-manager-ps.md). Questo approccio presenta due differenze:

* I prefissi degli indirizzi locali possono essere costituiti da un indirizzo host singolo. L'indirizzo IP del peer BGP locale viene specificato come segue:

    ```powershell
    New-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
    ```

* Quando si crea la connessione, è necessario impostare l'opzione **-EnableBGP** su $True:

    ```powershell
    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
    ```

## <a name="next-steps"></a>Passaggi successivi
Per istruzioni dettagliate su come configurare i gateway VPN di tipo attivo/attivo, vedere [Configurazione di gateway VPN di tipo attivo/attivo per connessioni cross-premise e da rete virtuale a rete virtuale](vpn-gateway-activeactive-rm-powershell.md).

