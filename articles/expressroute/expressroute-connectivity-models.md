---
title: 'Azure ExpressRoute: modelli di connettività'
description: Verificare la connettività tra i servizi di rete, Microsoft Azure e Microsoft 365 del cliente. I clienti possono usare provider MPLS, scambi cloud e Ethernet.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: duau
ms.openlocfilehash: fb35a03b5dd8780445eb27f485966e3c3452feea
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978756"
---
# <a name="expressroute-connectivity-models"></a>Modelli di connettività di ExpressRoute
È possibile creare una connessione tra la rete locale e il cloud Microsoft in quattro modi diversi, la [condivisione percorso CloudExchange](#CloudExchange), la [connessione Ethernet da punto a punto](#Ethernet), la connessione [any-to-any (IPVPN)](#IPVPN)e [ExpressRoute Direct](#Direct). I provider di connettività possono offrire uno o più modelli di connettività. È possibile interagire con il provider di connettività per scegliere il modello più appropriato.
<br><br>

:::image type="content" source="./media/expressroute-connectivity-models/expressroute-connectivity-models-diagram.png" alt-text="Diagramma dei modelli di connettività di ExpressRoute":::

## <a name="co-located-at-a-cloud-exchange"></a><a name="CloudExchange"></a>Percorso condiviso in una struttura Cloud Exchange
Se si dispone di un percorso condiviso in una struttura con scambio cloud, è possibile ordinare le connessioni incrociate virtuali al cloud Microsoft tramite lo scambio Ethernet del provider di condivisione percorso. I provider di condivisione del percorso possono fornire Cross Connection di livello 2 oppure gestite di livello 3 tra l'infrastruttura nella struttura di condivisione percorso e il cloud Microsoft.

## <a name="point-to-point-ethernet-connections"></a><a name="Ethernet"></a>Connessioni Ethernet da punto a punto
È possibile connettere i data center o gli uffici locali al cloud Microsoft tramite collegamenti Ethernet punto a punto. I provider Ethernet punto a punto forniscono connessioni di livello 2 o connessioni gestite di livello 3 tra la sede dell'utente e il cloud Microsoft.

## <a name="any-to-any-ipvpn-networks"></a><a name="IPVPN"></a>Reti Any-to-Any (IPVPN)
È possibile integrare una rete WAN con il cloud Microsoft. I provider IPVPN (in genere VPN MPLS) forniscono connettività any-to-any tra le succursali e i data center. Il cloud Microsoft può essere interconnesso a una rete WAN in modo che abbia l'aspetto di qualsiasi altra succursale. I provider WAN offrono in genere connettività gestita di livello 3. Le caratteristiche e le funzionalità di ExpressRoute sono identiche in tutti i modelli di connettività sopra descritti.

## <a name="direct-from-expressroute-sites"></a><a name="Direct"></a>Direttamente da siti ExpressRoute
Puoi connetterti direttamente alla rete globale di Microsoft in una località di peering distribuita in modo strategico in tutto il mondo. [ExpressRoute Direct](expressroute-erdirect-about.md) offre connettività dual 100 Gbps o 10 Gbps, che supporta la connettività attiva/attiva su larga scala.

## <a name="next-steps"></a>Passaggi successivi
* Informazioni sulle connessioni e i domini di routing ExpressRoute. Vedere [Circuiti e domini di routing ExpressRoute](expressroute-circuit-peerings.md).
* Informazioni sulle funzionalità di ExpressRoute. Vedere [Panoramica tecnica relativa a ExpressRoute](expressroute-introduction.md)
* Trovare un provider di servizi. Vedere [Partner e località di peering per Azure ExpressRoute](expressroute-locations.md).
* Verificare che vengano soddisfatti tutti i prerequisiti. Vedere [Prerequisiti per ExpressRoute](expressroute-prerequisites.md).
* Vedere i requisiti per [Routing](expressroute-routing.md), [NAT](expressroute-nat.md) e [QoS](expressroute-qos.md).
* Configurare la connessione ExpressRoute.
  * [Creare un circuito ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md)
  * [Configurare il routing](expressroute-howto-routing-portal-resource-manager.md)
  * [Collegare una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)