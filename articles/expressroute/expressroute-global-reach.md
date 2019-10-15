---
title: Connettere reti locali a Microsoft Cloud tramite Copertura globale - Azure ExpressRoute | Microsoft Docs
description: Questo articolo illustra Copertura globale di ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: e522c7ea306667f675af4bbe7486e25fb7f4ec9f
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122794"
---
# <a name="expressroute-global-reach"></a>Copertura globale di ExpressRoute
ExpressRoute è uno strumento privato e resiliente per connettere le reti locali a Microsoft Cloud. È possibile accedere a molti servizi cloud Microsoft, ad esempio Azure e Office 365, dal data center privato o dalla rete aziendale. Ad esempio, una delle succursali potrebbe essere a San Francisco con un circuito ExpressRoute nella Silicon Valley e un'altra succursale potrebbe trovarsi a Londra con un circuito ExpressRoute nella stessa città. Entrambi le succursali possono disporre di connettività ad alta velocità alle risorse di Azure negli Stati Uniti occidentali e nel Regno Unito meridionale. Tuttavia, le succursali non possono scambiare dati direttamente tra loro. In altre parole, 10.0.1.0/24 può inviare dati a 10.0.3.0/24 e a 10.0.4.0/24 ma NON a 10.0.2.0/24.

![Senza][1]

Con **Copertura globale di ExpressRoute** è possibile collegare circuiti ExpressRoute tra loro per creare una rete privata tra le reti locali. Aggiungendo Copertura globale di ExpressRoute nell'esempio precedente, la sede di San Francisco (10.0.1.0/24) può scambiare dati direttamente con la sede di Londra (10.0.2.0/24) attraverso i circuiti ExpressRoute esistenti e tramite la rete globale di Microsoft. 

![Con][2]

## <a name="use-case"></a>Caso d'uso
Copertura globale di ExpressRoute è progettata per integrare l'implementazione WAN del provider di servizi e la connessione delle succursali in tutto il mondo. Ad esempio, se il provider di servizi opera principalmente negli Stati Uniti e tutte le succursali negli Stati Uniti sono collegate, ma il provider di servizi non interagisce in Giappone e a Hong Kong, con Copertura globale di ExpressRoute è possibile lavorare con un provider di servizi locale e Microsoft connetterà le succursali qui a quelle negli Stati Uniti con ExpressRoute e la sua rete globale.

![caso d'uso][3]

## <a name="availability"></a>Disponibilità 
Il servizio Copertura globale di ExpressRoute è attualmente supportato nei paesi seguenti.

* Australia
* Canada
* Francia
* Germania
* RAS di Hong Kong
* Irlanda
* Giappone
* Corea del Sud
* Paesi Bassi
* Singapore
* Svizzera
* Regno Unito
* Stati Uniti

I circuiti ExpressRoute devono essere creati in [località di peering ExpressRoute](expressroute-locations.md) nei paesi o aree geografiche indicati sopra. Per abilitare Copertura globale di ExpressRoute tra [diverse aree geopolitiche](expressroute-locations.md), i circuiti devono essere SKU Premium.

## <a name="next-steps"></a>Passaggi successivi
1. [Altre informazioni su Copertura globale di ExpressRoute](expressroute-faqs.md)
2. [Come abilitare Copertura globale di ExpressRoute](expressroute-howto-set-global-reach.md)
3. [Collegare un circuito ExpressRoute a una rete virtuale di Azure](expressroute-howto-linkvnet-arm.md)


<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "Diagramma senza Copertura globale"
[2]: ./media/expressroute-global-reach/2.png "Diagramma con Copertura globale"
[3]: ./media/expressroute-global-reach/3.png "casi d'uso di copertura globale"
