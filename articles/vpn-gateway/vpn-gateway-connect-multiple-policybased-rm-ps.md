---
title: 'Connettere i gateway VPN di Azure a più dispositivi VPN basati su criteri locali: Azure Resource Manager: PowerShell | Microsoft Docs'
description: Configurare un gateway VPN basato su route di Azure per più dispositivi VPN basati su criteri usando Azure Resource Manager e PowerShell.
services: vpn-gateway
documentationcenter: na
author: yushwang
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: yushwang
ms.openlocfilehash: 46555bf121e674b82c0c7dd39f74ee3708fc4439
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52850645"
---
# <a name="connect-azure-vpn-gateways-to-multiple-on-premises-policy-based-vpn-devices-using-powershell"></a>Connettere i gateway VPN di Azure a più dispositivi VPN basati su criteri locali usando PowerShell

Questo articolo illustra la procedura per configurare un gateway VPN basato su route di Azure per la connessione a più dispositivi VPN basati su criteri locali sfruttando i criteri IPsec/IKE personalizzati nelle connessioni VPN da sito a sito.

## <a name="about"></a>Informazioni sui gateway VPN basati su criteri e basati su route

I dispositivi VPN basati su criteri differiscono *da* quelli basati su route per il modo in cui i selettori di traffico IPsec vengono impostati in una connessione:

* I dispositivi VPN **basati su criteri** usano le combinazioni di prefissi di entrambe le reti per definire come crittografare/decrittografare il traffico tramite i tunnel IPsec. Sono basati in genere su dispositivi firewall che eseguono il filtro dei pacchetti. La crittografia e la decrittografia dei tunnel IPsec vengono aggiunte al filtro dei pacchetti e al motore di elaborazione.
* I dispositivi VPN **basati su route** usano selettori di traffico any-to-any (jolly) e consentono alle tabelle di routing/inoltro di indirizzare il traffico a tunnel IPsec diversi. Sono basati in genere su piattaforme router in cui ogni tunnel IPsec è modellato come interfaccia di rete o interfaccia di tunnel virtuale.

I diagrammi seguenti evidenziano i due modelli:

### <a name="policy-based-vpn-example"></a>Esempio di VPN basata su criteri
![basata su criteri](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedmultisite.png)

### <a name="route-based-vpn-example"></a>Esempio di VPN basata su route
![basata su route](./media/vpn-gateway-connect-multiple-policybased-rm-ps/routebasedmultisite.png)

### <a name="azure-support-for-policy-based-vpn"></a>Supporto di Azure per la VPN basata su criteri
Azure supporta attualmente entrambe le modalità di gateway VPN: gateway VPN basati su route e gateway VPN basati su criteri. Sono basati su piattaforme interne diverse e quindi su specifiche diverse:

|                          | **Gateway VPN basato su criteri** | **Gateway VPN basato su route**               |
| ---                      | ---                         | ---                                      |
| **SKU del gateway di Azure**    | Basic                       | Basic, Standard, HighPerformance, VpnGw1, VpnGw2, VpnGw3 |
| **Versione IKE**          | IKEv1                       | IKEv2                                    |
| **Max. connessioni da sito a sito** | **1**                       | Basic/Standard: 10<br> HighPerformance: 30 |
|                          |                             |                                          |

Con i criteri IPsec/IKE personalizzati, ora è possibile configurare i gateway VPN basati su route di Azure per l'uso di selettori di traffico basati su prefissi con l'opzione "**PolicyBasedTrafficSelectors**", per connettersi ai dispositivi VPN basati su criteri locali. Questa funzionalità consente di connettersi da una rete virtuale di Azure e da un gateway VPN a più dispositivi VPN/firewall basati su criteri locali, rimuovendo il limite della connessione singola dai gateway VPN basati su criteri di Azure correnti.

> [!IMPORTANT]
> 1. Per abilitare questa connettività, i dispositivi VPN basati su criteri locali devono supportare **IKEv2** per connettersi ai gateway VPN basati su route di Azure. Controllare le specifiche dei dispositivi VPN.
> 2. Le reti locali che si connettono tramite dispositivi VPN basati su criteri con questo meccanismo possono connettersi solo alla rete virtuale di Azure e **non possono passare ad altre reti locali o reti virtuali tramite lo stesso gateway VPN di Azure**.
> 3. L'opzione di configurazione fa parte dei criteri di connessione IPsec/IKE personalizzati. Se si abilita l'opzione dei selettori di traffico basata su criteri è necessario specificare i criteri completi (algoritmi di crittografia IPsec/IKE e di integrità, attendibilità delle chiavi e durate delle associazioni di sicurezza).

Il diagramma seguente spiega perché il routing di transito tramite il gateway VPN di Azure non funziona con l'opzione basata su criteri:

![transito basato su criteri](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedtransit.png)

Come illustrato nel diagramma, il gateway VPN di Azure ha selettori di traffico dalla rete virtuale a ogni prefisso di rete locale, ma non ai prefissi di Cross Connection. I siti locali 2, 3 e 4, ad esempio, possono comunicare singolarmente con VNet1, ma non possono connettersi tra essi tramite il gateway VPN di Azure. Il diagramma illustra che i selettori di traffico Cross Connect non sono disponibili nel gateway VPN di Azure con questa configurazione.

## <a name="configurepolicybased"></a>Configurare selettori di traffico basati su criteri in una connessione

Le istruzioni contenute in questo articolo seguono lo stesso esempio descritto in [Configurare criteri IPsec/IKE per connessioni da sito a sito o da rete virtuale a rete virtuale](vpn-gateway-ipsecikepolicy-rm-powershell.md) per stabilire una connessione VPN da sito a sito. Vedere il diagramma seguente:

![s2s-policy](./media/vpn-gateway-connect-multiple-policybased-rm-ps/s2spolicypb.png)

Flusso di lavoro per abilitare questa connettività:
1. Creare la rete virtuale, il gateway VPN e il gateway di rete locale per la connessione cross-premise
2. Creare un criterio IPsec/IKE
3. Applicare i criteri quando si crea la connessione da sito a sito o da rete virtuale a rete virtuale e **abilitare i selettori di traffico basati su criteri** nella connessione
4. Se la connessione è già stata creata, è possibile applicare o aggiornare i criteri per una connessione esistente

## <a name="before-you-begin"></a>Prima di iniziare

Verificare di possedere una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile attivare i [vantaggi per i sottoscrittori di MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="enablepolicybased"></a>Abilitare i selettori di traffico basati su criteri in una connessione

Verificare di avere completato la [parte 3 dell'articolo sulla configurazione dei criteri IPsec/IKE](vpn-gateway-ipsecikepolicy-rm-powershell.md) per questa sezione. L'esempio seguente usa gli stessi parametri e passaggi:

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>Passaggio 1: Creare la rete virtuale, il gateway VPN e il gateway di rete locale

#### <a name="1-connect-to-your-subscription-and-declare-your-variables"></a>1. Dichiarare le variabili e connettersi alla sottoscrizione

[!INCLUDE [sign in](../../includes/vpn-gateway-cloud-shell-ps login.md)]

Dichiarare le variabili. Per questo esercizio, utilizziamo le variabili seguenti:

```azurepowershell-interactive
$Sub1          = "<YourSubscriptionName>"
$RG1           = "TestPolicyRG1"
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
$DNS1          = "8.8.8.8"
$GWName1       = "VNet1GW"
$GW1IPName1    = "VNet1GWIP1"
$GW1IPconf1    = "gw1ipconf1"
$Connection16  = "VNet1toSite6"

$LNGName6      = "Site6"
$LNGPrefix61   = "10.61.0.0/16"
$LNGPrefix62   = "10.62.0.0/16"
$LNGIP6        = "131.107.72.22"
```

#### <a name="2-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>2. Creare la rete virtuale, il gateway VPN e il gateway di rete locale

Creare un gruppo di risorse.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name $RG1 -Location $Location1
```

Usare l'esempio seguente per creare la rete virtuale, TestVNet1 con tre subnet e il gateway VPN. Se si desidera sostituire i valori, è importante che la subnet gateway venga denominata sempre esattamente GatewaySubnet. Se si assegnano altri nomi, la creazione del gateway ha esito negativo.

```azurepowershell-interactive
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1    = New-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1      = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1    = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance

New-AzureRmLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix $LNGPrefix61,$LNGPrefix62
```

### <a name="step-2---create-a-s2s-vpn-connection-with-an-ipsecike-policy"></a>Passaggio 2: Creare una connessione VPN da sito a sito con un criterio IPsec/IKE

#### <a name="1-create-an-ipsecike-policy"></a>1. Creare un criterio IPsec/IKE

> [!IMPORTANT]
> È necessario creare un criterio IPsec/IKE per abilitare l'opzione "UsePolicyBasedTrafficSelectors" nella connessione.

L'esempio seguente crea un criterio IPsec/IKE con gli algoritmi e i parametri seguenti:
* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA256, PFS24, durata dell'associazione di sicurezza 3600 secondi e 2048 KB

```azurepowershell-interactive
$ipsecpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup PFS24 -SALifeTimeSeconds 3600 -SADataSizeKilobytes 2048
```

#### <a name="2-create-the-s2s-vpn-connection-with-policy-based-traffic-selectors-and-ipsecike-policy"></a>2. Creare la connessione VPN da sito a sito con i selettori di traffico basati su criteri e il criterio IPsec/IKE
Creare una connessione VPN da sito a sito e applicare il criterio IPsec/IKE creato in precedenza. Si noti il parametro aggiuntivo "-UsePolicyBasedTrafficSelectors $True" per abilitare i selettori di traffico basati su criteri nella connessione.

```azurepowershell-interactive
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng6 = Get-AzureRmLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -UsePolicyBasedTrafficSelectors $True -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
```

Dopo avere completato i passaggi, la connessione VPN da sito a sito userà il criterio IPsec/IKE definito e abiliterà i selettori di traffico basati su criteri nella connessione. È possibile ripetere gli stessi passaggi per aggiungere altre connessioni a dispositivi VPN basati su criteri locali aggiuntivi dallo stesso gateway VPN di Azure.

## <a name="update-policy-based-traffic-selectors-for-a-connection"></a>Aggiornare i selettori di traffico basati su criteri per una connessione
L'ultima sezione illustra come aggiornare l'opzione dei selettori di traffico basati su criteri per una connessione VPN da sito a sito esistente.

### <a name="1-get-the-connection"></a>1. Ottenere la connessione
Ottenere la risorsa di connessione.

```azurepowershell-interactive
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
```

### <a name="2-check-the-policy-based-traffic-selectors-option"></a>2. Selezionare l'opzione dei selettori di traffico basati su criteri
La riga seguente indica se i selettori di traffico basati su criteri vengono usati per la connessione:

```azurepowershell-interactive
$connection6.UsePolicyBasedTrafficSelectors
```

Se la riga restituisce "**True**", i selettori di traffico basati su criteri sono configurati nella connessione. In caso contrario, restituirà "**False**".

### <a name="3-enabledisable-the-policy-based-traffic-selectors-on-a-connection"></a>3. Abilitare/Disabilitare i selettori di traffico basati su criteri in una connessione
Dopo avere ottenuto la risorsa di connessione, è possibile abilitare o disabilitare l'opzione.

#### <a name="to-enable-usepolicybasedtrafficselectors"></a>Per abilitare UsePolicyBasedTrafficSelectors
L'esempio seguente abilita l'opzione dei selettori di traffico basati su criteri, ma lascia invariato il criterio IPsec/IKE:

```azurepowershell-interactive
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $True
```

#### <a name="to-disable-usepolicybasedtrafficselectors"></a>Per disabilitare UsePolicyBasedTrafficSelectors
L'esempio seguente disabilita l'opzione dei selettori di traffico basati su criteri, ma lascia invariato il criterio IPsec/IKE:

```azurepowershell-interactive
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $False
```

## <a name="next-steps"></a>Passaggi successivi
Dopo aver completato la connessione, è possibile aggiungere macchine virtuali alle reti virtuali. Per i passaggi, vedere [Creare la prima macchina virtuale](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) .

Vedere anche [Configure IPsec/IKE policy for S2S VPN or VNet-to-VNet connections](vpn-gateway-ipsecikepolicy-rm-powershell.md) (Configurare i criteri IPsec/IKE per le connessioni da sito a sito o da rete virtuale a rete virtuale) per altre informazioni sui criteri IPsec/IKE personalizzati.
