---
title: Creare e gestire connessioni VPN da sito a sito di Azure usando PowerShell | Microsoft Docs
description: Esercitazione - Creare e gestire connessioni VPN da sito a sito con il modulo Azure PowerShell
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 02/11/2019
ms.author: yushwang
ms.custom: mvc
ms.openlocfilehash: a9ca626ecf026736617ba495422ed957d03b2b37
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/19/2019
ms.locfileid: "56414601"
---
# <a name="tutorial-create-and-manage-s2s-vpn-connections-using-powershell"></a>Esercitazione: Creare e gestire connessioni VPN da sito a sito con PowerShell

Le connessioni VPN da sito a sito di Azure offrono connettività cross-premise sicura tra l'infrastruttura locale del cliente e Azure. Questa esercitazione illustra in modo dettagliato i cicli di vita della connessione VPN da sito a sito IPsec, ad esempio la creazione e la gestione di una connessione VPN da sito a sito. Si apprenderà come:

> [!div class="checklist"]
> * Creare una connessione VPN da sito a sito
> * Aggiornare le proprietà della connessione: chiave precondivisa, BGP, criteri IPsec/IKE
> * Aggiungere altre connessioni VPN
> * Eliminare una connessione VPN

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Il diagramma seguente illustra la topologia per questa esercitazione:

![Diagramma della connessione VPN da sito a sito](./media/vpn-gateway-tutorial-vpnconnection-powershell/site-to-site-diagram.png)

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

## <a name="requirements"></a>Requisiti

Completare la prima esercitazione: [Creare un gateway VPN con Azure PowerShell](vpn-gateway-tutorial-create-gateway-powershell.md) per creare le risorse seguenti:

1. Gruppo di risorse (TestRG1), rete virtuale (VNet1) e GatewaySubnet
2. Gateway VPN (VNet1GW)

I valori dei parametri della rete virtuale sono indicati di seguito. Osservare i valori aggiuntivi per il gateway di rete locale che rappresentano la rete locale. Modificare i valori seguenti in base alla configurazione dell'ambiente e della rete, quindi copiarli e incollarli per impostare le variabili per questa esercitazione. Se la sessione Cloud Shell scade oppure è necessario usare una finestra PowerShell diversa, copiare e incollare le variabili nella nuova sessione e continuare l'esercitazione.

>[!NOTE]
> Se si usa questa procedura per effettuare una connessione, assicurarsi di modificare i valori in modo che corrispondano alla rete locale. Se questi passaggi vengono eseguiti solo come esercitazione non è necessario apportare modifiche, ma la connessione non funzionerà.
>

```azurepowershell-interactive
# Virtual network
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "East US"
$VNet1Prefix = "10.1.0.0/16"
$VNet1ASN    = 65010
$Gw1         = "VNet1GW"

# On-premises network - LNGIP1 is the VPN device public IP address
$LNG1        = "VPNsite1"
$LNGprefix1  = "10.101.0.0/24"
$LNGprefix2  = "10.101.1.0/24"
$LNGIP1      = "5.4.3.2"

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

Creare un gateway di rete locale con il comando [New-AzLocalNetworkGateway](https://docs.microsoft.com/powershell/module/az.network/new-azlocalnetworkgateway?view=azurermps-6.8.1).

```azurepowershell-interactive
New-AzLocalNetworkGateway -Name $LNG1 -ResourceGroupName $RG1 `
  -Location 'East US' -GatewayIpAddress $LNGIP1 -AddressPrefix $LNGprefix1,$LNGprefix2
```

## <a name="create-a-s2s-vpn-connection"></a>Creare una connessione VPN da sito a sito

Creare quindi una connessione VPN da sito a sito tra il gateway di rete virtuale e il dispositivo VPN con [New-AzVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworkgatewayconnection?view=azurermps-6.8.1). Si noti che il valore di "-ConnectionType" per la connessione VPN da sito a sito è *IPsec*.

```azurepowershell-interactive
$vng1 = Get-AzVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
$lng1 = Get-AzLocalNetworkGateway   -Name $LNG1 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection1 -ResourceGroupName $RG1 `
  -Location $Location1 -VirtualNetworkGateway1 $vng1 -LocalNetworkGateway2 $lng1 `
  -ConnectionType IPsec -SharedKey "Azure@!b2C3"
```

Aggiungere la proprietà facoltativa "**-EnableBGP $True**" per abilitare il protocollo BGP per la connessione, se si usa tale protocollo. Questo protocollo è disabilitato per impostazione predefinita.

## <a name="update-the-vpn-connection-pre-shared-key-bgp-and-ipsecike-policy"></a>Aggiornare la chiave precondivisa, il protocollo BGP e i criteri IPsec/IKE della connessione VPN

### <a name="view-and-update-your-pre-shared-key"></a>Visualizzare e aggiornare la chiave precondivisa

La connessione VPN da sito a sito di Azure usa una chiave precondivisa (segreto) per eseguire l'autenticazione tra il dispositivo VPN locale e il gateway VPN di Azure. È possibile visualizzare e aggiornare la chiave precondivisa per una connessione con [Get-AzVirtualNetworkGatewayConnectionSharedKey](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetworkgatewayconnectionsharedkey?view=azurermps-6.8.1) e [Set-AzVirtualNetworkGatewayConnectionSharedKey](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworkgatewayconnectionsharedkey?view=azurermps-6.8.1).

> [!IMPORTANT]
> La chiave precondivisa è una stringa di **caratteri ASCII stampabili** composta da un massimo di 128 caratteri.

Questo comando mostra la chiave precondivisa per la connessione:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayConnectionSharedKey `
  -Name $Connection1 -ResourceGroupName $RG1
```

In base all'esempio precedente, l'output sarà "**Azure@!b2C3**". Usare il comando seguente per modificare il valore della chiave precondivisa in "**Azure@!_b2=C3**":

```azurepowershell-interactive
Set-AzVirtualNetworkGatewayConnectionSharedKey `
  -Name $Connection1 -ResourceGroupName $RG1 `
  -Value "Azure@!_b2=C3"
```

### <a name="enable-bgp-on-vpn-connection"></a>Abilitare BGP nella connessione VPN

Il gateway VPN di Azure supporta il protocollo di routing dinamico BGP. È possibile abilitare BGP in ogni singola connessione, a seconda del fatto che il protocollo BGP sia in uso nei dispositivi e nelle reti locali. Specificare le proprietà BGP seguenti prima di abilitare il protocollo BGP per la connessione:

* Numero sistema autonomo (ASN, Autonomous System Number) della rete VPN di Azure
* Numero sistema autonomo del gateway di rete locale
* Indirizzo IP del peer BGP del gateway di rete locale

Se le proprietà BGP non sono state configurate, i comandi seguenti aggiungono queste proprietà al gateway VPN e al gateway di rete locale: [Set-AzVirtualNetworkGateway](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworkgateway?view=azurermps-6.8.1) e [Set-AzLocalNetworkGateway](https://docs.microsoft.com/powershell/module/az.network/set-azlocalnetworkgateway?view=azurermps-6.8.1).

Usare l'esempio seguente per configurare le proprietà BGP:

```azurepowershell-interactive
$vng1 = Get-AzVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $vng1 -Asn $VNet1ASN

$lng1 = Get-AzLocalNetworkGateway   -Name $LNG1 -ResourceGroupName $RG1
Set-AzLocalNetworkGateway -LocalNetworkGateway $lng1 `
  -Asn $LNGASN1 -BgpPeeringAddress $BGPPeerIP1
```

Abilitare BGP con [Set-AzVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworkgatewayconnection?view=azurermps-6.8.1).

```azurepowershell-interactive
$connection = Get-AzVirtualNetworkGatewayConnection `
  -Name $Connection1 -ResourceGroupName $RG1

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection `
  -EnableBGP $True
```

È possibile disabilitare BGP modificando il valore della proprietà "-EnableBGP" in **$False**. Per informazioni più dettagliate su BGP nei gateway VPN di Azure, vedere [BGP nei gateway VPN di Azure](vpn-gateway-bgp-resource-manager-ps.md).

### <a name="apply-a-custom-ipsecike-policy-on-the-connection"></a>Applicare criteri IPsec/IKE personalizzati nella connessione

È possibile applicare criteri IPsec/IKE facoltativi per specificare l'esatta combinazione di algoritmi di crittografia IPsec/IKE e attendibilità della chiave per la connessione, invece di usare le [proposte predefinite](vpn-gateway-about-vpn-devices.md#ipsec). Lo script di esempio seguente crea un criterio IPsec/IKE diverso con gli algoritmi e i parametri seguenti:

* IKEv2: AES256, SHA256, DHGroup14
* IPsec: AES128, SHA1, PFS14, durata associazione di sicurezza 14.400 secondi e 102.400.000 KB

```azurepowershell-interactive
$connection = Get-AzVirtualNetworkGatewayConnection -Name $Connection1 `
                -ResourceGroupName $RG1
$newpolicy  = New-AzIpsecPolicy `
                -IkeEncryption AES256 -IkeIntegrity SHA256 -DhGroup DHGroup14 `
                -IpsecEncryption AES128 -IpsecIntegrity SHA1 -PfsGroup PFS2048 `
                -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection `
  -IpsecPolicies $newpolicy
```

Vedere [Criteri IPsec/IKE per connessioni da sito a sito o da rete virtuale a rete virtuale](vpn-gateway-ipsecikepolicy-rm-powershell.md) per un elenco completo di algoritmi e istruzioni.

## <a name="add-another-s2s-vpn-connection"></a>Aggiungere una connessione VPN da sito a sito

Aggiungere un'altra connessione VPN da sito a sito allo stesso gateway VPN, creare un altro gateway di rete locale e creare una nuova connessione tra il nuovo gateway di rete locale e il gateway VPN. Usare gli esempi seguenti assicurandosi di modificare le variabili per riflettere la propria configurazione di rete.

```azurepowershell-interactive
# On-premises network - LNGIP2 is the VPN device public IP address
$LNG2        = "VPNsite2"
$Location2   = "West US"
$LNGprefix21 = "10.102.0.0/24"
$LNGprefix22 = "10.102.1.0/24"
$LNGIP2      = "4.3.2.1"
$Connection2 = "VNet1ToSite2"

New-AzLocalNetworkGateway -Name $LNG2 -ResourceGroupName $RG1 `
  -Location $Location2 -GatewayIpAddress $LNGIP2 -AddressPrefix $LNGprefix21,$LNGprefix22

$vng1 = Get-AzVirtualNetworkGateway -Name $GW1  -ResourceGroupName $RG1
$lng2 = Get-AzLocalNetworkGateway   -Name $LNG2 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection2 -ResourceGroupName $RG1 `
  -Location $Location1 -VirtualNetworkGateway1 $vng1 -LocalNetworkGateway2 $lng2 `
  -ConnectionType IPsec -SharedKey "AzureA1%b2_C3+"
```

A questo punto ci sono due connessioni VPN da sito a sito per il gateway VPN di Azure.

![Connessioni VPN multisito](./media/vpn-gateway-tutorial-vpnconnection-powershell/multisite-connections.png)

## <a name="delete-a-s2s-vpn-connection"></a>Eliminare una connessione VPN da sito a sito

Eliminare una connessione VPN da sito a sito usando [Remove-AzVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetworkgatewayconnection?view=azurermps-6.8.1).

```azurepowershell-interactive
Remove-AzVirtualNetworkGatewayConnection -Name $Connection2 -ResourceGroupName $RG1
```

Eliminare il gateway di rete locale, se non è più necessario. Non è possibile eliminare un gateway di rete locale se al gateway sono associate altre connessioni.

```azurepowershell-interactive
Remove-AzVirtualNetworkGatewayConnection -Name $LNG2 -ResourceGroupName $RG1
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se questa configurazione fa parte di una distribuzione di test o di un prototipo, è possibile usare il comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) per rimuovere il gruppo di risorse, il gateway VPN e tutte le risorse correlate.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $RG1
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
