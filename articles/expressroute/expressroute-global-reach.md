---
title: 'Azure ExpressRoute: connettersi a Microsoft Cloud usando Copertura globale'
description: Informazioni su come Azure ExpressRoute Copertura globale possibile collegare i circuiti ExpressRoute per creare una rete privata tra le reti locali.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2020
ms.author: duau
ms.custom: references_regions
ms.openlocfilehash: e3f9105037c049a53f1b7b99da96dd857070fcc7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90987620"
---
# <a name="expressroute-global-reach"></a>Copertura globale di ExpressRoute
ExpressRoute è un modo privato e resiliente per connettere le reti locali al Microsoft Cloud. È possibile accedere a molti servizi cloud Microsoft, ad esempio Azure, e Microsoft 365 dal data center privato o dalla rete aziendale. Ad esempio, una delle succursali potrebbe essere a San Francisco con un circuito ExpressRoute nella Silicon Valley e un'altra succursale potrebbe trovarsi a Londra con un circuito ExpressRoute nella stessa città. Entrambe le filiali hanno connettività ad alta velocità alle risorse di Azure negli Stati Uniti occidentali e Regno Unito meridionale. Tuttavia, le succursali non possono connettersi e inviare dati direttamente tra loro. In altre parole, 10.0.1.0/24 può inviare i dati alla rete 10.0.3.0/24 e 10.0.4.0/24, ma non alla rete 10.0.2.0/24.

![Diagramma che mostra i circuiti non collegati insieme a Express Route Copertura globale.][1]

Con **Copertura globale di ExpressRoute** è possibile collegare circuiti ExpressRoute tra loro per creare una rete privata tra le reti locali. Aggiungendo Copertura globale di ExpressRoute nell'esempio precedente, la sede di San Francisco (10.0.1.0/24) può scambiare dati direttamente con la sede di Londra (10.0.2.0/24) attraverso i circuiti ExpressRoute esistenti e tramite la rete globale di Microsoft. 

![Diagramma che mostra i circuiti collegati insieme a Express Route Copertura globale.][2]

## <a name="use-case"></a>Caso d'uso
Copertura globale di ExpressRoute è progettata per integrare l'implementazione WAN del provider di servizi e la connessione delle succursali in tutto il mondo. Ad esempio, se il provider di servizi opera principalmente negli Stati Uniti e tutte le succursali negli Stati Uniti sono collegate, ma il provider di servizi non interagisce in Giappone e a Hong Kong, con Copertura globale di ExpressRoute è possibile lavorare con un provider di servizi locale e Microsoft connetterà le succursali qui a quelle negli Stati Uniti con ExpressRoute e la sua rete globale.

![Diagramma che mostra un caso d'uso per Express Route Copertura globale.][3]

## <a name="availability"></a>Disponibilità 
ExpressRoute Copertura globale è supportato nelle posizioni seguenti. 

> [!NOTE] 
> Per abilitare la Copertura globale ExpressRoute tra [diverse aree geopolitiche](expressroute-locations-providers.md#locations), i circuiti devono essere **SKU Premium**.

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

## <a name="next-steps"></a>Passaggi successivi
- Visualizzare le [domande frequenti copertura globale](expressroute-faqs.md#globalreach).
- Informazioni su come [abilitare copertura globale](expressroute-howto-set-global-reach.md).
- Informazioni su come [collegare un circuito ExpressRoute alla rete virtuale](expressroute-howto-linkvnet-arm.md).

<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "Diagramma senza Copertura globale"
[2]: ./media/expressroute-global-reach/2.png "Diagramma con Copertura globale"
[3]: ./media/expressroute-global-reach/3.png "casi d'uso di copertura globale"
