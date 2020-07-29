---
title: 'Azure ExpressRoute: connettersi a Microsoft Cloud usando Copertura globale'
description: Questo articolo illustra Copertura globale di ExpressRoute.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 06/09/2020
ms.author: cherylmc
ms.openlocfilehash: 5022efacf33a2c07f1388666b5de26ee86322739
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84636734"
---
# <a name="expressroute-global-reach"></a>Copertura globale di ExpressRoute
ExpressRoute è uno strumento privato e resiliente per connettere le reti locali a Microsoft Cloud. È possibile accedere a molti servizi cloud Microsoft, ad esempio Azure e Office 365, dal data center privato o dalla rete aziendale. Ad esempio, una delle succursali potrebbe essere a San Francisco con un circuito ExpressRoute nella Silicon Valley e un'altra succursale potrebbe trovarsi a Londra con un circuito ExpressRoute nella stessa città. Entrambi le succursali possono disporre di connettività ad alta velocità alle risorse di Azure negli Stati Uniti occidentali e nel Regno Unito meridionale. Tuttavia, le succursali non possono scambiare dati direttamente tra loro. In altre parole, 10.0.1.0/24 può inviare dati a 10.0.3.0/24 e a 10.0.4.0/24 ma NON a 10.0.2.0/24.

![senza][1]

Con **Copertura globale di ExpressRoute** è possibile collegare circuiti ExpressRoute tra loro per creare una rete privata tra le reti locali. Aggiungendo Copertura globale di ExpressRoute nell'esempio precedente, la sede di San Francisco (10.0.1.0/24) può scambiare dati direttamente con la sede di Londra (10.0.2.0/24) attraverso i circuiti ExpressRoute esistenti e tramite la rete globale di Microsoft. 

![con][2]

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
* Nuova Zelanda
* Norvegia
* Singapore
* Svezia
* Svizzera
* Regno Unito
* Stati Uniti

I circuiti ExpressRoute devono essere creati in [località di peering ExpressRoute](expressroute-locations.md) nei paesi o regioni indicati sopra. Per abilitare Copertura globale di ExpressRoute tra [diverse aree geopolitiche](expressroute-locations.md), i circuiti devono essere SKU Premium.

## <a name="next-steps"></a>Passaggi successivi
1. [Visualizzare le domande frequenti Copertura globale](expressroute-faqs.md#globalreach)
2. [Informazioni su come abilitare Copertura globale](expressroute-howto-set-global-reach.md)
3. [Informazioni su come collegare un circuito ExpressRoute alla rete virtuale](expressroute-howto-linkvnet-arm.md)


<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "Diagramma senza Copertura globale"
[2]: ./media/expressroute-global-reach/2.png "Diagramma con Copertura globale"
[3]: ./media/expressroute-global-reach/3.png "casi d'uso di copertura globale"
