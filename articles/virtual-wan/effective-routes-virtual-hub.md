---
title: 'Visualizzare le route valide di un hub virtuale: rete WAN virtuale di Azure | Microsoft Docs'
description: Route valide per un hub virtuale in una rete WAN virtuale di Azure
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: cherylmc
ms.openlocfilehash: 1173da81736661048d1e4e12d9919bc2aadf73ee
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515850"
---
# <a name="view-effective-routes-of-a-virtual-hub"></a>Visualizzare le route valide di un hub virtuale

È possibile visualizzare tutte le route dell'hub WAN virtuale nel portale di Azure. Per visualizzare le route, passare all'hub virtuale e quindi selezionare **routing-> visualizzare le route valide**.

## <a name="understand"></a>Informazioni sulle route

L'esempio seguente può essere utile per comprendere meglio il modo in cui viene visualizzato il routing WAN virtuale.

In questo esempio è presente una rete WAN virtuale con tre hub. Il primo Hub si trova nell'area Stati Uniti orientali, il secondo hub è nell'area Europa occidentale e il terzo hub si trova nell'area Stati Uniti occidentali. In una rete WAN virtuale tutti gli hub sono interconnessi. In questo esempio si presuppone che gli hub Stati Uniti orientali ed Europa occidentale dispongano di connessioni da rami locali (spoke) e reti virtuali di Azure (spoke).

Il peering di Azure VNet spoke (10.4.0.0/16) con un'appliance virtuale di rete (10.4.0.6) viene ulteriormente associato a un VNet (10.5.0.0/16). Per ulteriori informazioni sulla tabella di route dell'hub, vedere [informazioni aggiuntive](#abouthubroute) più avanti in questo articolo.

In questo esempio si presuppone anche che il ramo 1 dell'Europa occidentale sia connesso all'hub Stati Uniti orientali, oltre che all'hub Europa occidentale. Un circuito ExpressRoute nell'area Stati Uniti orientali connette il ramo 2 all'hub Stati Uniti orientali.

![diagramma](./media/effective-routes-virtual-hub/diagram.png)

## <a name="view"></a>Visualizza route valide

Quando si seleziona "Visualizza route valide" nel portale, viene prodotto l'output visualizzato nella tabella di [Route Hub](#routetable) per l'hub Stati Uniti orientali.

Per applicare questa prospettiva, la prima riga implica che l'hub Stati Uniti orientali ha appreso il percorso di 10.20.1.0/24 (ramo 1) a causa della connessione al *tipo di hop successivo* della VPN ("hop successivo" gateway VPN Instance0 IP 10.1.0.6, Instance1 IP 10.1.0.7). *Origine route* punta all'ID risorsa. *As Path* indica il percorso As per il ramo 1.

### <a name="routetable"></a>Tabella di route Hub

Utilizzare la barra di scorrimento nella parte inferiore della tabella per visualizzare il percorso "AS".

| **Prefisso** |  **Tipo hop successivo** | **Hop successivo** |  **Origine route** |**Percorso AS** |
| ---        | ---                | ---          | ---               | ---         |
| 10.20.1.0/24|VPN |10.1.0.6, 10.1.0.7| /Subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/vpnGateways/343a19aa6ac74e4d81f05ccccf1536cf-eastus-gw| 20000|
|10.21.1.0/24 |ExpressRoute|10.1.0.10, 10.1.0.11|/Subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/expressRouteGateways/4444a6ac74e4d85555-eastus-gw|21000|
|10.23.1.0/24| VPN |10.1.0.6, 10.1.0.7|/Subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/vpnGateways/343a19aa6ac74e4d81f05ccccf1536cf-eastus-gw|23000|
|10.4.0.0/16|Connessione alla rete virtuale| Collegamento |  |  |
|10.5.0.0/16| Indirizzo IP| 10.4.0.6|/Subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/virtualHubs/easthub_1/routeTables/table_1| |
|0.0.0.0/0| Indirizzo IP| `<Azure Firewall IP>` |/Subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/virtualHubs/easthub_1/routeTables/table_1| |
|10.22.1.0/16| Hub remoto|10.8.0.6, 10.8.0.7|/Subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/virtualHubs/westhub_| 4848-22000 |
|10.9.0.0/16| Hub remoto|  Collegamento |/Subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/virtualHubs/westhub_1| |

>[!NOTE]
> Se gli hub Stati Uniti orientali e Europa occidentale non comunicano tra loro nella topologia di esempio, la route appresa (10.9.0.0/16) non esiste. Gli hub pubblicizzano solo le reti direttamente connesse.
>

## <a name="additional"></a>Informazioni aggiuntive

### <a name="abouthubroute"></a>Informazioni sulla tabella di route dell'hub

è possibile creare una route dell'hub virtuale e applicare la route alla tabella della route dell'hub virtuale. È possibile applicare più route alla tabella di route dell'hub virtuale. In questo modo è possibile impostare una route per VNet di destinazione tramite un indirizzo IP (in genere l'appliance virtuale di rete in una VNet spoke). Per altre informazioni su appliance virtuali, vedere [indirizzare il traffico da un hub virtuale a un appliance virtuale di](virtual-wan-route-table-portal.md)dispositivo.

### <a name="aboutdefaultroute"></a>Informazioni sulla route predefinita (0.0.0.0/0)

Un hub virtuale è in grado di propagare una route predefinita acquisita a una rete virtuale, una VPN da sito a sito e una connessione ExpressRoute se il flag è' Enabled ' nella connessione. Questo flag è visibile quando si modifica una connessione di rete virtuale, una connessione VPN o una connessione ExpressRoute. ' EnableInternetSecurity ' è sempre false per impostazione predefinita nelle connessioni VNet, ExpressRoute e VPN dell'hub.

La route predefinita non ha origine nell'hub WAN virtuale. La route predefinita viene propagata se è già stata acquisita dall'hub WAN virtuale come risultato della distribuzione di un firewall nell'hub o se è abilitato il tunneling forzato in un altro sito connesso.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla rete WAN virtuale, vedere la [panoramica sulla rete WAN virtuale](virtual-wan-about.md).