---
title: Connessioni VPN da sito a sito tramite peering privato di ExpressRoute
description: Questo articolo consente di abilitare la VPN da sito a sito tramite peering privato di ExpressRoute per crittografare il traffico.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/16/2020
ms.author: cherylmc
ms.openlocfilehash: 62ceafad0210065700e5c9734cfe9a055208ef35
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94657891"
---
# <a name="configure-a-site-to-site-vpn-connection-over-expressroute-private-peering"></a>Configurare una connessione VPN da sito a sito tramite peering privato di ExpressRoute

È possibile configurare una VPN da sito a sito a un gateway di rete virtuale tramite un peering privato ExpressRoute usando un indirizzo IP RFC 1918. Questa configurazione offre i vantaggi seguenti:

* Il traffico sul peering privato è crittografato.

* Gli utenti da punto a sito che si connettono a un gateway di rete virtuale possono usare ExpressRoute (tramite il tunnel da sito a sito) per accedere alle risorse locali.

>[!NOTE]
>Questa funzionalità è supportata solo nei gateway con ridondanza della zona. Ad esempio, VpnGw1AZ, VpnGw2AZ e così via.
>

Per completare questa configurazione, verificare che siano soddisfatti i prerequisiti seguenti:

* Si dispone di un circuito ExpressRoute funzionante collegato al VNet in cui si trova il gateway VPN (o verrà creato).

* È possibile raggiungere le risorse tramite IP RFC1918 (privato) nella VNet tramite il circuito ExpressRoute.

## <a name="routing"></a><a name="routing"></a>Routing

**Nella figura 1** viene illustrato un esempio di connettività VPN tramite peering privato di ExpressRoute. In questo esempio viene visualizzata una rete all'interno della rete locale connessa al gateway VPN dell'hub di Azure tramite il peering privato di ExpressRoute. Un aspetto importante di questa configurazione è il routing tra le reti locali e Azure tramite i percorsi ExpressRoute e VPN.

Figura 1

:::image type="content" source="media/site-to-site-vpn-private-peering/routing.png" alt-text="Figura 1":::

Stabilire la connettività è semplice:

1. Stabilire la connettività ExpressRoute con un circuito ExpressRoute e un peering privato.

1. Stabilire la connettività VPN usando la procedura descritta in questo articolo.

### <a name="traffic-from-on-premises-networks-to-azure"></a>Traffico dalle reti locali ad Azure

Per il traffico da reti locali ad Azure, i prefissi di Azure vengono annunciati tramite il protocollo BGP del peering privato ExpressRoute e il protocollo BGP VPN. Il risultato è due route di rete (percorsi) verso Azure dalle reti locali:

• Una route di rete sul percorso protetto da IPsec.

• Un'unica route di rete direttamente su ExpressRoute senza protezione IPsec.

Per applicare la crittografia alla comunicazione, è necessario assicurarsi che per la rete connessa alla VPN nella **Figura 1**, le route di Azure tramite il gateway VPN locale siano preferite rispetto al percorso ExpressRoute diretto.

### <a name="traffic-from-azure-to-on-premises-networks"></a>Traffico da Azure alle reti locali

Lo stesso requisito si applica al traffico da Azure alle reti locali. Per assicurarsi che il percorso IPsec sia preferibile rispetto al percorso ExpressRoute diretto (senza IPsec), sono disponibili due opzioni:

• **Annunciare prefissi più specifici nella sessione BGP VPN per la rete connessa alla VPN**. È possibile annunciare un intervallo più ampio che include la rete connessa alla VPN tramite il peering privato di ExpressRoute, quindi intervalli più specifici nella sessione BGP VPN. Ad esempio, annunciare 10.0.0.0/16 su ExpressRoute e 10.0.1.0/24 su VPN.

• **Annunciare i prefissi non contigui per VPN e ExpressRoute**. Se gli intervalli di rete connessi alla VPN sono disgiunti da altre reti connesse a ExpressRoute, è possibile annunciare rispettivamente i prefissi nelle sessioni BGP VPN e ExpressRoute. Ad esempio, annunciare 10.0.0.0/24 su ExpressRoute e 10.0.1.0/24 su VPN.

In entrambi questi esempi, Azure invierà il traffico a 10.0.1.0/24 attraverso la connessione VPN anziché direttamente tramite ExpressRoute senza protezione VPN.

>[!Warning]
>Se si annunciano gli stessi prefissi tramite connessioni ExpressRoute e VPN, >Azure utilizzerà il percorso ExpressRoute direttamente senza la protezione VPN.
>

## <a name="portal-steps"></a><a name="portal"></a>Passaggi del portale

1. Configurare una connessione da sito a sito. Per la procedura, vedere l'articolo relativo alla [configurazione da sito a sito](vpn-gateway-howto-site-to-site-resource-manager-portal.md) . Assicurarsi di selezionare uno SKU del gateway con ridondanza della zona per il gateway. 

   Gli SKU con ridondanza della zona hanno "AZ" alla fine dello SKU. Ad esempio, **VpnGw1AZ**. I gateway con ridondanza della zona sono disponibili solo nelle aree in cui è disponibile il servizio zona di disponibilità. Per informazioni sulle aree in cui sono supportate le zone di disponibilità, vedere [aree che supportano le zone di disponibilità](../availability-zones/az-region.md).

   :::image type="content" source="media/site-to-site-vpn-private-peering/gateway.png" alt-text="Indirizzi IP privati del gateway":::
1. Abilitare gli indirizzi IP privati nel gateway. Selezionare **configurazione**, quindi impostare **indirizzi IP privati del gateway** su **abilitato**. Fare clic su **Salva** per salvare le modifiche.
1. Nella pagina **Panoramica** selezionare **Visualizza altro** per visualizzare l'indirizzo IP privato. Prendere nota di queste informazioni da usare più avanti nei passaggi di configurazione.

   :::image type="content" source="media/site-to-site-vpn-private-peering/gateway-overview.png" alt-text="Pagina di panoramica" lightbox="media/site-to-site-vpn-private-peering/gateway-overview.png":::
1. Per abilitare l' **uso dell'indirizzo IP privato di Azure** per la connessione, selezionare  **configurazione**. Impostare **Usa indirizzo IP privato di Azure** su **abilitato**, quindi selezionare **Salva**.

   :::image type="content" source="media/site-to-site-vpn-private-peering/connection.png" alt-text="Indirizzi IP privati del gateway abilitati":::
1. Dal firewall, effettuare il ping dell'indirizzo IP privato scritto nel passaggio 3. L'IP privato deve essere raggiungibile tramite il peering privato di ExpressRoute.
1. Usare questo indirizzo IP privato come IP remoto nel firewall locale per stabilire il tunnel da sito a sito tramite il peering privato di ExpressRoute.

## <a name="powershell-steps"></a><a name="powershell"></a>passaggi di PowerShell

1. Configurare una connessione da sito a sito. Per la procedura, vedere l'articolo [configurare una VPN da sito a sito](vpn-gateway-howto-site-to-site-resource-manager-portal.md) . Assicurarsi di selezionare uno SKU del gateway con ridondanza della zona per il gateway. Gli SKU con ridondanza della zona hanno "AZ" alla fine dello SKU. Ad esempio, VpnGw1AZ.
1. Impostare il flag per l'uso dell'indirizzo IP privato nel gateway usando i comandi di PowerShell seguenti:

   ```azurepowershell-interactive
   $Gateway = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroup <name of resource group>

   Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -EnablePrivateIpAddress $true
   ```

   Verrà visualizzato un indirizzo IP pubblico e privato. Annotare l'indirizzo IP nella sezione "TunnelIpAddresses" dell'output. Queste informazioni vengono usate in un passaggio successivo.
1. Impostare la connessione per usare l'indirizzo IP privato usando il comando PowerShell seguente:

   ```azurepowershell-interactive
   $Connection = get-AzVirtualNetworkGatewayConnection -Name <name of the connection> -ResourceGroupName <name of resource group>

   Set-AzVirtualNetworkGatewayConnection --VirtualNetworkGatewayConnection $Connection -UseLocalAzureIpAddress $true
   ```
1. Dal firewall, effettuare il ping dell'indirizzo IP privato scritto nel passaggio 2. Deve essere raggiungibile tramite il peering privato di ExpressRoute.
1. Usare questo indirizzo IP privato come IP remoto nel firewall locale per stabilire il tunnel da sito a sito tramite il peering privato di ExpressRoute.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul gateway VPN, vedere [che cos'è il gateway VPN?](vpn-gateway-about-vpngateways.md)
