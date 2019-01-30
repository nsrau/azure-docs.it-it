---
title: Creare e gestire connessioni VPN da sito a sito di Azure usando PowerShell | Microsoft Docs
description: Esercitazione - Creare e gestire connessioni VPN da sito a sito con il modulo Azure PowerShell
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure
ms.date: 05/08/2018
ms.author: yushwang
ms.custom: mvc
ms.openlocfilehash: 0c71062bded65f8aa7c259c0678ee6675e2dab38
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2019
ms.locfileid: "54432218"
---
# <a name="create-and-manage-s2s-vpn-connections-with-the-azure-powershell-module"></a>Creare e gestire connessioni VPN da sito a sito con il modulo Azure PowerShell

Le connessioni VPN da sito a sito di Azure offrono connettività cross-premise sicura tra l'infrastruttura locale del cliente e Azure. Questa esercitazione illustra in modo dettagliato i cicli di vita della connessione VPN da sito a sito IPsec, ad esempio la creazione e la gestione di una connessione VPN da sito a sito. Si apprenderà come:

> [!div class="checklist"]
> * Creare una connessione VPN da sito a sito
> * Aggiornare le proprietà della connessione: chiave precondivisa, BGP, criteri IPsec/IKE
> * Aggiungere altre connessioni VPN
> * Eliminare una connessione VPN

Il diagramma seguente illustra la topologia per questa esercitazione:

![Diagramma della connessione VPN da sito a sito](./media/vpn-gateway-tutorial-vpnconnection-powershell/site-to-site-diagram.png)

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario il modulo Azure PowerShell versione 5.3 o successiva. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Login-AzureRmAccount` per creare una connessione con Azure.

## <a name="requirements"></a>Requisiti

Completare la prima esercitazione: "[Creare un gateway VPN con Azure PowerShell](vpn-gateway-tutorial-create-gateway-powershell.md)" per creare le risorse seguenti:

1. Gruppo di risorse (TestRG1), rete virtuale (VNet1) e GatewaySubnet
2. Gateway VPN (VNet1GW)

I valori dei parametri della rete virtuale sono indicati di seguito. Osservare i valori aggiuntivi per consentire al gateway di rete locale di rappresentare la rete locale. Modificare i valori in base all'ambiente e alla configurazione di rete.

```azurepowershell-interactive
# Virtual network
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "East US"
$VNet1Prefix = "10.1.0.0/16"
$VNet1ASN    = 65010
$Gw1         = "VNet1GW"

# On-premises network
$LNG1        = "VPNsite1"
$LNGprefix1  = "10.101.0.0/24"
$LNGprefix2  = "10.101.1.0/24"
$LNGIP1      = "YourDevicePublicIP"

# Optional - on-premises BGP properties
$LNGASN1     = 65011
$BGPPeerIP1  = "10.101.1.254"

# Connection
$Connection1 = "VNet1ToSite1"
```

Il flusso di lavoro per creare una connessione VPN da sito a sito è semplice:

1. Creare un gateway di rete locale per rappresentare la rete locale
2. Creare una connessione tra il gateway VPN di Azure e il gateway di rete locale

## <a name="create-a-local-network-gateway"></a>Creare un gateway di rete locale

Un gateway di rete locale rappresenta la rete locale. È possibile specificare le proprietà della rete locale nel gateway di rete locale, tra cui:

* Indirizzo IP pubblico del dispositivo VPN
* Spazio indirizzi locale
* (Facoltativo) Attributi BGP (indirizzo IP del peer BGP e numero di sistema autonomo)

Creare un gateway di rete locale con il comando [New-AzureRmLocalNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermlocalnetworkgateway?view=azurermps-6.8.1).

```azurepowershell-interactive
New-AzureRmLocalNetworkGateway -Name $LNG1 -ResourceGroupName $RG1 `
  -Location 'East US' -GatewayIpAddress $LNGIP1 -AddressPrefix $LNGprefix1,$LNGprefix2
```

## <a name="create-a-s2s-vpn-connection"></a>Creare una connessione VPN da sito a sito

Creare quindi una connessione VPN da sito a sito tra il gateway di rete virtuale e il dispositivo VPN con [New-AzureRmVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayconnection?view=azurermps-6.8.1). Si noti che il valore di "-ConnectionType" per la connessione VPN da sito a sito è *IPsec*.

```azurepowershell-interactive
$vng1 = Get-AzureRmVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
$lng1 = Get-AzureRmLocalNetworkGateway   -Name $LNG1 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection1 -ResourceGroupName $RG1 `
  -Location $Location1 -VirtualNetworkGateway1 $vng1 -LocalNetworkGateway2 $lng1 `
  -ConnectionType IPsec -SharedKey "Azure@!b2C3"
```

Aggiungere la proprietà facoltativa "**-EnableBGP $True**" per abilitare il protocollo BGP per la connessione, se si usa tale protocollo. Questo protocollo è disabilitato per impostazione predefinita.

## <a name="update-the-vpn-connection-pre-shared-key-bgp-and-ipsecike-policy"></a>Aggiornare la chiave precondivisa, il protocollo BGP e i criteri IPsec/IKE della connessione VPN

### <a name="view-and-update-your-pre-shared-key"></a>Visualizzare e aggiornare la chiave precondivisa

La connessione VPN da sito a sito di Azure usa una chiave precondivisa (segreto) per eseguire l'autenticazione tra il dispositivo VPN locale e il gateway VPN di Azure. È possibile visualizzare e aggiornare la chiave precondivisa per una connessione con [Get-AzureRmVirtualNetworkGatewayConnectionSharedKey](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworkgatewayconnectionsharedkey?view=azurermps-6.8.1) e [Set-AzureRmVirtualNetworkGatewayConnectionSharedKey](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgatewayconnectionsharedkey?view=azurermps-6.8.1).

> [!IMPORTANT]
> La chiave precondivisa è una stringa di **caratteri ASCII stampabili** composta da un massimo di 128 caratteri.

Questo comando mostra la chiave precondivisa per la connessione:

```azurepowershell-interactive
Get-AzureRmVirtualNetworkGatewayConnectionSharedKey `
  -Name $Connection1 -ResourceGroupName $RG1
```

In base all'esempio precedente, l'output sarà "**Azure@!b2C3**". Usare il comando seguente per modificare il valore della chiave precondivisa in "**Azure@!_b2=C3**":

```azurepowershell-interactive
Set-AzureRmVirtualNetworkGatewayConnectionSharedKey `
  -Name $Connection1 -ResourceGroupName $RG1 `
  -Value "Azure@!_b2=C3"
```

### <a name="enable-bgp-on-vpn-connection"></a>Abilitare BGP nella connessione VPN

Il gateway VPN di Azure supporta il protocollo di routing dinamico BGP. È possibile abilitare BGP in ogni singola connessione, a seconda del fatto che il protocollo BGP sia in uso nei dispositivi e nelle reti locali. Specificare le proprietà BGP seguenti prima di abilitare il protocollo BGP per la connessione:

* Numero sistema autonomo (ASN, Autonomous System Number) della rete VPN di Azure
* Numero sistema autonomo del gateway di rete locale
* Indirizzo IP del peer BGP del gateway di rete locale

Se le proprietà BGP non sono state configurate, usare i comandi seguenti per aggiungere queste proprietà al gateway VPN e al gateway di rete locale: [Set-AzureRmVirtualNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgateway?view=azurermps-6.8.1) e [Set-AzureRmLocalNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermlocalnetworkgateway?view=azurermps-6.8.1).

```azurepowershell-interactive
$vng1 = Get-AzureRmVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $vng1 -Asn $VNet1ASN

$lng1 = Get-AzureRmLocalNetworkGateway   -Name $LNG1 -ResourceGroupName $RG1
Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $lng1 `
  -Asn $LNGASN1 -BgpPeeringAddress $BGPPeerIP1
```

Abilitare BGP usando [Set-AzureRmVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgatewayconnection?view=azurermps-6.8.1).

```azurepowershell-interactive
$connection = Get-AzureRmVirtualNetworkGatewayConnection `
  -Name $Connection1 -ResourceGroupName $RG1

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection `
  -EnableBGP $True
```

È possibile disabilitare BGP modificando il valore della proprietà "-EnableBGP" in **$False**. Per informazioni più dettagliate su BGP nei gateway VPN di Azure, vedere [BGP nei gateway VPN di Azure](vpn-gateway-bgp-resource-manager-ps.md).

### <a name="apply-a-custom-ipsecike-policy-on-the-connection"></a>Applicare criteri IPsec/IKE personalizzati nella connessione

È possibile applicare criteri IPsec/IKE facoltativi per specificare l'esatta combinazione di algoritmi di crittografia IPsec/IKE e attendibilità della chiave per la connessione, invece di usare le [proposte predefinite](vpn-gateway-about-vpn-devices.md#ipsec). Lo script di esempio seguente crea un criterio IPsec/IKE diverso con gli algoritmi e i parametri seguenti:

* IKEv2: AES256, SHA256, DHGroup14
* IPsec: AES128, SHA1, PFS14, durata associazione di sicurezza 14.400 secondi e 102.400.000 KB

```azurepowershell-interactive
$connection = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection1 `
                -ResourceGroupName $RG1
$newpolicy  = New-AzureRmIpsecPolicy `
                -IkeEncryption AES256 -IkeIntegrity SHA256 -DhGroup DHGroup14 `
                -IpsecEncryption AES128 -IpsecIntegrity SHA1 -PfsGroup PFS2048 `
                -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection `
  -IpsecPolicies $newpolicy
```

Vedere [Criteri IPsec/IKE per connessioni da sito a sito o da rete virtuale a rete virtuale](vpn-gateway-ipsecikepolicy-rm-powershell.md) per un elenco completo di algoritmi e istruzioni.

## <a name="add-another-s2s-vpn-connection"></a>Aggiungere una connessione VPN da sito a sito

Per aggiungere un'altra connessione VPN da sito a sito allo stesso gateway VPN, creare un altro gateway di rete locale e creare una nuova connessione tra il nuovo gateway di rete locale e il gateway VPN. Seguire l'esempio in questo articolo.

```azurepowershell-interactive
# On-premises network
$LNG2        = "VPNsite2"
$Location2   = "West US"
$LNGprefix21 = "10.102.0.0/24"
$LNGprefix22 = "10.102.1.0/24"
$LNGIP2      = "YourDevicePublicIP"
$Connection2 = "VNet1ToSite2"

New-AzureRmLocalNetworkGateway -Name $LNG2 -ResourceGroupName $RG1 `
  -Location $Location2 -GatewayIpAddress $LNGIP2 -AddressPrefix $LNGprefix21,$LNGprefix22

$vng1 = Get-AzureRmVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
$lng2 = Get-AzureRmLocalNetworkGateway   -Name $LNG2 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection2 -ResourceGroupName $RG1 `
  -Location $Location1 -VirtualNetworkGateway1 $vng1 -LocalNetworkGateway2 $lng2 `
  -ConnectionType IPsec -SharedKey "AzureA1%b2_C3+"
```

A questo punto ci sono due connessioni VPN da sito a sito per il gateway VPN di Azure.

![Connessioni VPN multisito](./media/vpn-gateway-tutorial-vpnconnection-powershell/multisite-connections.png)

## <a name="delete-a-s2s-vpn-connection"></a>Eliminare una connessione VPN da sito a sito

Eliminare una connessione VPN da sito a sito usando [Remove-AzureRmVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/azurerm.network/remove-azurermvirtualnetworkgatewayconnection?view=azurermps-6.8.1).

```azurepowershell-interactive
Remove-AzureRmVirtualNetworkGatewayConnection -Name $Connection2 -ResourceGroupName $RG1
```

Eliminare il gateway di rete locale, se non è più necessario. Non è possibile eliminare un gateway di rete locale se al gateway sono associate altre connessioni.

```azurepowershell-interactive
Remove-AzureRmVirtualNetworkGatewayConnection -Name $LNG2 -ResourceGroupName $RG1
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come creare e gestire connessioni VPN da sito a sito, eseguendo operazioni come:

> [!div class="checklist"]
> * Creare una connessione VPN da sito a sito
> * Aggiornare le proprietà della connessione: chiave precondivisa, BGP, criteri IPsec/IKE
> * Aggiungere altre connessioni VPN
> * Eliminare una connessione VPN

Passare alle esercitazioni seguenti per informazioni sulle connessioni da sito a sito, da rete virtuale a rete virtuale e da punto a sito.

> [!div class="nextstepaction"]
> * [Creare connessioni da rete virtuale a rete virtuale](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Creare connessioni da punto a sito](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
