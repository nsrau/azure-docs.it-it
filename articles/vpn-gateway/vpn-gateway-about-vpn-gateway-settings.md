---
title: Impostazioni del gateway VPN per connessioni di Azure cross-premise | Documentazione Microsoft
description: Informazioni sulle impostazioni del gateway VPN per i gateway di rete virtuale di Azure.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: ae665bc5-0089-45d0-a0d5-bc0ab4e79899
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/26/2017
ms.author: cherylmc
ms.openlocfilehash: 07aa6946b9c3994c5afc5c88837f23567b95d8a5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="about-vpn-gateway-configuration-settings"></a>Informazioni sulle impostazioni di configurazione del gateway VPN

Un gateway VPN è un tipo di gateway di rete virtuale che invia traffico crittografato tra la rete virtuale e la posizione locale tramite una connessione pubblica. È possibile usare un gateway VPN anche per inviare il traffico tra reti virtuali attraverso il backbone di Azure.

Una connessione di gateway VPN si basa sulla configurazione di più risorse, ognuna delle quali contiene impostazioni configurabili. Le sezioni di questo articolo descrivono le risorse e le impostazioni correlate a un gateway VPN per una rete virtuale creata nel modello di distribuzione Resource Manager. Le descrizioni e i diagrammi della topologia per ogni soluzione di connessione sono disponibili nell'articolo [Informazioni sul gateway VPN](vpn-gateway-about-vpngateways.md).

## <a name="gwtype"></a>Tipi di gateway

Ogni rete virtuale può avere un solo gateway di rete virtuale per tipo. Quando si crea un gateway di rete virtuale, è necessario assicurarsi che il tipo di gateway sia corretto per la configurazione in uso.

I valori disponibili per GatewayType sono:

* VPN
* ExpressRoute

Un gateway VPN richiede il valore `-GatewayType` *Vpn* per.

Esempio:

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased
```

## <a name="gwsku"></a>SKU del gateway

[!INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

### <a name="configure-the-gateway-sku"></a>Configurare lo SKU del gateway

#### <a name="azure-portal"></a>Portale di Azure

Se si usa il portale di Azure per creare un gateway di rete virtuale di Resource Manager, è possibile selezionare lo SKU del dal menu a discesa. Le opzioni disponibili corrispondono al tipo di gateway e al tipo di VPN selezionati.

#### <a name="powershell"></a>PowerShell

L'esempio seguente di PowerShell specifica `-GatewaySku` come VpnGw1.

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig -GatewaySku VpnGw1 `
-GatewayType Vpn -VpnType RouteBased
```

#### <a name="resize"></a>Modificare (ridimensionare) uno SKU del gateway

Per aggiornare lo SKU del gateway a uno SKU più potente, è possibile usare il cmdlet di PowerShell `Resize-AzureRmVirtualNetworkGateway`. È anche possibile eseguire il downgrade delle dimensioni dello SKU del gateway usando questo cmdlet.

L'esempio di PowerShell seguente illustra uno SKU del gateway che viene ridimensionato come VpnGw2.

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

## <a name="connectiontype"></a>Tipi di connessione

Nel modello di distribuzione Resource Manager ogni configurazione richiede un tipo di connessione di gateway di rete virtuale specifico. I valori di PowerShell per Resource Manager disponibili per `-ConnectionType` sono:

* IPsec
* Vnet2Vnet
* ExpressRoute
* VPNClient

L'esempio PowerShell seguente crea una connessione S2S che richiede il tipo di connessione *IPsec*.

```powershell
New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

## <a name="vpntype"></a>Tipi di VPN

Quando si crea il gateway di rete virtuale per una configurazione di gateway VPN, è necessario specificare un tipo di VPN. Il tipo di VPN scelto dipende dalla topologia di connessione che si desidera creare. Ad esempio, una connessione P2S richiede un tipo di VPN RouteBased. Un tipo di VPN può anche dipendere dall'hardware usato. Le configurazioni S2S richiedono un dispositivo VPN. Alcuni dispositivi VPN supportano solo un determinato tipo di VPN.

Il tipo di VPN selezionato deve soddisfare tutti i requisiti di connessione per la soluzione da creare. Ad esempio, per creare una connessione di gateway VPN S2S, una connessione di gateway VPN P2S sulla stessa rete virtuale, usare il tipo *RouteBased* poiché P2S richiede questo tipo specifico. È inoltre necessario verificare che il dispositivo VPN supporti una connessione VPN di tipo RouteBased. 

Dopo avere creato un gateway di rete virtuale, non è possibile modificare il tipo di VPN. È necessario eliminare il gateway di rete virtuale e crearne uno nuovo. Sono disponibili due tipi di VPN:

[!INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

Nel seguente esempio di PowerShell `-VpnType` viene specificato come *RouteBased*. Quando si crea un gateway, è necessario assicurarsi che -VpnType sia corretto per la configurazione.

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig `
-GatewayType Vpn -VpnType RouteBased
```

## <a name="requirements"></a>Requisiti del gateway

[!INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]

## <a name="gwsub"></a>Subnet del gateway

Prima di creare un gateway VPN, è necessario creare una subnet del gateway. La subnet del gateway contiene gli indirizzi IP usati dalle VM e dai servizi del gateway di rete virtuale. Quando si crea il gateway di rete virtuale, le VM del gateway vengono distribuite nella subnet del gateway e configurate con le impostazioni del gateway VPN necessarie. Non si deve mai distribuire altro (ad esempio, VM aggiuntive) nella subnet del gateway. Per poter funzionare correttamente, la subnet del gateway deve essere denominata "GatewaySubnet". Denominando la subnet del gateway 'GatewaySubnet', Azure riconosce questa subnet come quella in cui distribuire i servizi e le VM del gateway di rete virtuale.

Quando si crea la subnet del gateway, si specifica il numero di indirizzi IP inclusi nella subnet. Gli indirizzi IP inclusi nella subnet del gateway sono allocati alle VM del gateway e ai servizi del gateway. Alcune configurazioni richiedono più indirizzi IP di altre. Esaminare le istruzioni per la configurazione da creare e verificare che la subnet del gateway che si vuole creare soddisfi tali requisiti. È anche consigliabile verificare che la subnet del gateway contenga una quantità di indirizzi IP sufficiente per supportare possibili future configurazioni aggiuntive. Anche se è possibile creare una subnet del gateway con dimensioni pari a /29, è consigliabile crearne una con dimensioni pari a /28 o superiori, ad esempio /28, /27, /26 e così via. In questo modo, se si aggiungono funzionalità in futuro, non sarà necessario rimuovere il gateway ed eliminare e ricreare la subnet del gateway per consentire altri indirizzi IP.

L'esempio seguente di PowerShell Resource Manager illustra una subnet del gateway denominata GatewaySubnet. La notazione CIDR specifica /27. Questa dimensione ammette un numero di indirizzi IP sufficiente per la maggior parte delle configurazioni attualmente esistenti.

```powershell
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="lng"></a>Gateway di rete locali

Quando si crea una configurazione per il gateway VPN, il gateway di rete locale rappresenta spesso la posizione locale. Nel modello di distribuzione classica il gateway di rete locale è definito come sito locale. 

Assegnare un nome e l'indirizzo IP pubblico del dispositivo VPN locale al gateway di rete locale e specificare i prefissi di indirizzo che si trovano nel percorso locale. Azure esamina i prefissi di indirizzo di destinazione per il traffico di rete, consulta la configurazione specificata per il gateway di rete locale e indirizza i pacchetti di conseguenza. È anche possibile specificare i gateway di rete locale per le configurazioni da rete virtuale a rete virtuale che usano una connessione di gateway VPN.

L'esempio seguente di PowerShell consente di creare un nuovo gateway di rete locale:

```powershell
New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```

Talvolta è necessario modificare le impostazioni di gateway di rete locale. Ad esempio, quando si aggiunge o si modifica l'intervallo di indirizzi oppure se viene modificato l'indirizzo IP del dispositivo VPN. Per una rete virtuale classica, è possibile modificare queste impostazioni nella pagina relativa alle reti locali del portale classico. Per Resource Manager, vedere [Modificare le impostazioni del gateway di rete locale usando PowerShell](vpn-gateway-modify-local-network-gateway.md).

## <a name="resources"></a>API REST e cmdlet PowerShell

Per altre risorse tecniche e requisiti di sintassi specifici quando si usano le API REST, i cmdlet PowerShell o l'interfaccia della riga di comando di Azure per le configurazioni di Gateway VPN, vedere le pagine seguenti:

| **Classico** | **Gestione risorse** |
| --- | --- |
| [PowerShell](/powershell/module/azure#networking) |[PowerShell](/powershell/module/azurerm.network#vpn) |
| [API REST](https://msdn.microsoft.com/library/jj154113) |[API REST](/rest/api/network/virtualnetworkgateways) |
| Non supportate | [Interfaccia della riga di comando di Azure](/cli/azure/network/vnet-gateway)|

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle configurazioni delle connessioni disponibili, vedere [Informazioni sul gateway VPN](vpn-gateway-about-vpngateways.md).