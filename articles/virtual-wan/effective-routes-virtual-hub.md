---
title: 'Visualizzare le route effettive di un hub virtuale: RETE WAN virtuale di Azure Documenti Microsoft'
description: Percorsi efficaci per un hub virtuale nella rete WAN virtuale di Azure
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: cherylmc
ms.openlocfilehash: 1173da81736661048d1e4e12d9919bc2aadf73ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73515850"
---
# <a name="view-effective-routes-of-a-virtual-hub"></a>Visualizzare percorsi efficaci di un hub virtuale

È possibile visualizzare tutte le route dell'hub WAN virtuale nel portale di Azure.You can view all the routes of your Virtual WAN hub in the Azure portal. Per visualizzare le route, passare all'hub virtuale, quindi selezionare **Routing -> Visualizza route effettive**.

## <a name="understanding-routes"></a><a name="understand"></a>Informazioni sui percorsi

L'esempio seguente consente di comprendere meglio l'aspetto del routing della rete WAN virtuale.

In questo esempio, abbiamo una rete WAN virtuale con tre hub. Il primo hub si trova nell'area degli Stati Uniti orientali, il secondo hub si trova nella regione dell'Europa occidentale e il terzo hub si trova nell'area degli Stati Uniti occidentali. In una rete WAN virtuale, tutti gli hub sono interconnessi. In questo esempio si presuppone che gli hub degli Stati Uniti orientali e dell'Europa occidentale dispongano di connessioni da filiali locali (spoke) e reti virtuali di Azure (spoke).

Un spoke della rete virtuale di Azure (10.4.0.0/16) con un'appliance virtuale di rete (10.4.0.6) viene ulteriormente sottoposto a peered a una rete virtuale (10.5.0.0/16). Per altre informazioni sulla tabella di route hub, vedere [Informazioni aggiuntive](#abouthubroute) più avanti in questo articolo.

In questo esempio, si presuppone inoltre che la filiale 1 dell'Europa occidentale sia collegata all'hub degli Stati Uniti orientali e all'hub dell'Europa occidentale. Un circuito ExpressRoute negli Stati Uniti orientali connette il ramo 2 all'hub degli Stati Uniti orientali.

![diagramma](./media/effective-routes-virtual-hub/diagram.png)

## <a name="view-effective-routes"></a><a name="view"></a>Visualizzare le route valide

Quando si seleziona "Visualizza route valide" nel portale, viene generato l'output visualizzato nella tabella route [Hub](#routetable) per l'hub degli Stati Uniti orientali.

Per mettere questo in prospettiva, la prima linea implica che l'hub Stati Uniti orientali ha imparato la route di 10.20.1.0/24 (Branch 1) a causa della connessione di *tipo Hop successivo* VPN ('Next hop' VPN Gateway Instance0 IP 10.1.0.6, Instance1 IP 10.1.0.7). *Route Origin* punta all'ID risorsa. *Percorso AS* indica il percorso AS per il ramo 1.

### <a name="hub-route-table"></a><a name="routetable"></a>Tabella di route hub

Utilizzare la barra di scorrimento nella parte inferiore della tabella per visualizzare il "Percorso AS".

| **Prefisso** |  **Tipo di hop successivo** | **Hop successivo** |  **Origine percorso** |**Percorso AS** |
| ---        | ---                | ---          | ---               | ---         |
| 10.20.1.0/24|VPN |10.1.0.6, 10.1.0.7| /subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/vpnGateways/343a19aa6ac74e4d81f05ccf1536cf-eastus-gw| 20000|
|10.21.1.0/24 |ExpressRoute|10.1.0.10, 10.1.0.11|/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/expressRouteGateways/4444a6ac74e4d85555-eastus-gw|21000|
|10.23.1.0/24| VPN |10.1.0.6, 10.1.0.7|/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/vpnGateways/343a19aa6ac74e4d81f05ccf1536cf-eastus-gw|23000|
|10.4.0.0/16|Connessione di rete virtuale| Collegamento |  |  |
|10.5.0.0/16| Indirizzo IP| 10.4.0.6|/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/virtualHubs/easthub_1/routeTables/table_1| |
|0.0.0.0/0| Indirizzo IP| `<Azure Firewall IP>` |/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/virtualHubs/easthub_1/routeTables/table_1| |
|10.22.1.0/16| Hub remoto|10.8.0.6, 10.8.0.7|/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/virtualHubs/westhub_| 4848-22000 |
|10.9.0.0/16| Hub remoto|  Collegamento |/subscriptions/`<sub>`/resourceGroups/`<rg>`/providers/Microsoft.Network/virtualHubs/westhub_1| |

>[!NOTE]
> Se gli hub degli Stati Uniti orientali e dell'Europa occidentale non comunicassero tra loro nella topologia di esempio, il percorso appreso (10.9.0.0/16) non esisterebbe. Gli hub annunciano solo le reti a cui sono direttamente connesse.
>

## <a name="additional-information"></a><a name="additional"></a>Ulteriori informazioni

### <a name="about-the-hub-route-table"></a><a name="abouthubroute"></a>Informazioni sulla tabella di route hub

è possibile creare una route dell'hub virtuale e applicare la route alla tabella della route dell'hub virtuale. È possibile applicare più route alla tabella di route dell'hub virtuale. Ciò consente di impostare una route per la rete virtuale di destinazione tramite un indirizzo IP (in genere Network Virtual Appliance (NVA) in una rete virtuale a raggi). Per ulteriori informazioni sulle nVA, consultate [Instradare il traffico da un hub virtuale a un'unità di rete.](virtual-wan-route-table-portal.md)

### <a name="about-default-route-00000"></a><a name="aboutdefaultroute"></a>Informazioni sulla route predefinita (0.0.0.0/0)

Un hub virtuale ha la possibilità di propagare una route predefinita appresa a una rete virtuale, una VPN da sito a sito e una connessione ExpressRoute se il flag è 'Abilitato' nella connessione. Questo flag è visibile quando si modifica una connessione di rete virtuale, una connessione VPN o una connessione ExpressRoute.This flag is visible when you edit a virtual network connection, a VPN connection, or an ExpressRoute connection. 'EnableInternetSecurity' è sempre false per impostazione predefinita nelle connessioni di rete virtuale hub, ExpressRoute e VPN.

La route predefinita non ha origine nell'hub WAN virtuale. La route predefinita viene propagata se viene già appresa dall'hub WAN virtuale in seguito alla distribuzione di un firewall nell'hub o se il tunneling forzato è abilitato.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla rete WAN virtuale, vedere [Panoramica](virtual-wan-about.md)della rete WAN virtuale .