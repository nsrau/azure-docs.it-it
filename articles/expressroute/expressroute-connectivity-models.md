---
title: "Modelli di connettività di ExpressRoute: connettersi a Microsoft Azure tramite provider di servizi di rete, scambi e provider Ethernet | Documentazione Microsoft"
description: "Questo articolo illustra le diverse modalità di connessione tra la rete del cliente e i servizi di Microsoft Azure, Office 365 e Dynamics 365. I clienti possono usare provider MPLS, Cloud Exchange ed Ethernet."
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2017
ms.author: cherylmc
ms.openlocfilehash: 00f97da2189491103c461b49ac82feb92d8f4b9b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="expressroute-connectivity-models"></a>Modelli di connettività di ExpressRoute
È possibile creare una connessione tra la rete locale e Microsoft Cloud in tre modi diversi, ovvero [condivisione percorso di CloudExchange](#CloudExchange), [connessione Ethernet da punto a punto](#Ethernet) e [connessione (IPVPN) any-to-any](#IPVPN). I provider di connettività possono fornire uno o più modelli di connettività. È possibile interagire con il provider di connettività per scegliere il modello più appropriato.
<br><br>

![Diagramma dei modelli di connettività di ExpressRoute](./media/expressroute-connectivity-models/expressroute-connectivity-models-diagram.png)

## <a name="CloudExchange"></a>Percorso condiviso in una struttura Cloud Exchange
Nel caso di percorso condiviso in una struttura con scambio cloud, è possibile ordinare Cross Connection virtuali con il cloud Microsoft tramite lo scambio Ethernet del provider di condivisione del percorso. I provider di condivisione del percorso possono fornire Cross Connection di livello 2 oppure gestite di livello 3 tra l'infrastruttura nella struttura di condivisione percorso e il cloud Microsoft.

## <a name="Ethernet"></a>Connessioni Ethernet da punto a punto
È possibile connettere i data center o gli uffici locali al cloud Microsoft tramite collegamenti Ethernet punto a punto. I provider Ethernet punto a punto forniscono connessioni di livello 2 o connessioni gestite di livello 3 tra la sede dell'utente e il cloud Microsoft.

## <a name="IPVPN"></a>Reti (IPVPN) any-to-any
È possibile integrare una rete WAN con il cloud Microsoft. I provider IPVPN (in genere VPN MPLS) forniscono connettività any-to-any tra le succursali e i data center. Il cloud Microsoft può essere interconnesso a una rete WAN in modo che abbia l'aspetto di qualsiasi altra succursale. I provider WAN offrono in genere connettività gestita di livello 3. Le caratteristiche e le funzionalità di ExpressRoute sono identiche in tutti i modelli di connettività sopra descritti. 

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