---
title: File di inclusione
description: File di inclusione
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 03/19/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 317a480c13c5c6e00653fd61878a379df3f65ac4
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66170715"
---
### <a name="what-is-expressroute-global-reach"></a>Che cos'è Copertura globale di ExpressRoute?

Copertura globale di ExpressRoute è un servizio di Azure che connette le reti locali tramite il servizio ExpressRoute sulla rete globale di Microsoft. Ad esempio, se si dispone di un data center privato in California connesso a ExpressRoute nella Silicon Valley e di un altro data center privato in Texas connesso a ExpressRoute a Dallas, con Copertura globale di ExpressRoute, è possibile connettere tra loro i data center privati tramite le due connessioni ExpressRoute e il traffico tra i data center attraverserà il backbone della rete Microsoft.

### <a name="how-do-i-enable-or-disable-expressroute-global-reach"></a>Come è possibile abilitare o disabilitare Copertura globale di ExpressRoute?

Per abilitare Copertura globale di ExpressRoute, connettere i circuiti ExpressRoute. Per disabilitare la funzionalità, disconnettere i circuiti. Vedere la [configurazione](../articles/expressroute/expressroute-howto-set-global-reach.md).

### <a name="do-i-need-expressroute-premium-for-expressroute-global-reach"></a>È necessario ExpressRoute Premium per Copertura globale di ExpressRoute?

Se i circuiti ExpressRoute sono nella stessa area geopolitica, non occorre ExpressRoute Premium per collegarli tra loro. Se due circuiti ExpressRoute si trovano in aree geopolitiche diverse, è necessario ExpressRoute Premium per entrambi i circuiti per abilitare la connettività tra di essi. 

### <a name="how-will-i-be-charged-for-expressroute-global-reach"></a>Come viene fatturato il servizio Copertura globale di ExpressRoute?

ExpressRoute abilita la connettività dalla rete locale ai servizi cloud Microsoft. Copertura globale di ExpressRoute abilita la connettività tra le reti locali tramite i circuiti ExpressRoute esistenti, sfruttando la rete globale di Microsoft. Il servizio Copertura globale di ExpressRoute viene fatturato separatamente dal servizio ExpressRoute esistente. È prevista una tariffa aggiuntiva per abilitare questa funzionalità in ogni circuito ExpressRoute. Il traffico tra le reti locali abilitato da Copertura globale di ExpressRoute verrà fatturato in base a una tariffa per il traffico in uscita all'origine e per il traffico in ingresso nella destinazione. Le tariffe si basano sulla zona in cui si trovano i circuiti.

### <a name="where-is-expressroute-global-reach-supported"></a>Dove è supportato il servizio Copertura globale di ExpressRoute?

Raggiungere globale ExpressRoute è supportato in [selezionare i paesi/aree geografiche o punti](../articles/expressroute/expressroute-global-reach.md). I circuiti ExpressRoute devono essere creati nei percorsi peering in tali paesi/aree geografiche o punti.

### <a name="i-have-more-than-two-on-premises-networks-each-connected-to-an-expressroute-circuit-can-i-enable-expressroute-global-reach-to-connect-all-of-my-on-premises-networks-together"></a>Se esistono più di due reti locali, ciascuna connessa a un circuito ExpressRoute, è possibile abilitare Copertura globale di ExpressRoute per connettere tra loro tutte le reti locali?

Sì, è possibile, purché i circuiti siano in paesi/aree geografiche supportati. È necessario connettere due circuiti ExpressRoute alla volta. Per creare una rete di peer completa, è necessario enumerare tutte le coppie di circuiti e ripetere la configurazione. 

### <a name="can-i-enable-expressroute-global-reach-between-two-expressroute-circuits-at-the-same-peering-location"></a>È possibile abilitare Copertura globale di ExpressRoute tra due circuiti ExpressRoute nella stessa località di peering?

 No. I due circuiti devono appartenere a posizioni di peering diverse. Se un'area metropolitana in un paese/area geografica supportate sono presenti più di una località di peering di ExpressRoute, è possibile connettere tra loro i circuiti ExpressRoute creati in diverse località di peering in tale metro. 

### <a name="if-expressroute-global-reach-is-enabled-between-circuit-x-and-circuit-y-and-between-circuit-y-and-circuit-z-will-my-on-premises-networks-connected-to-circuit-x-and-circuit-z-talk-to-each-other-via-microsofts-network"></a>Se il servizio Copertura globale di ExpressRoute è abilitato tra il circuito X e il circuito Y e tra il circuito Y e il circuito Z, le reti locali connesse al circuito X e al circuito Z potranno comunicare tra loro tramite la rete Microsoft?

 No. Per abilitare la connettività tra due delle reti locali, è necessario connettere in modo esplicito i circuiti ExpressRoute corrispondenti. Nell'esempio precedente, è necessario connettere il circuito X e il circuito Z. 

### <a name="what-is-the-network-throughput-i-can-expect-between-my-on-premises-networks-after-i-enable-expressroute-global-reach"></a>Quale velocità effettiva della rete ci si può aspettare tra le reti locali dopo l'abilitazione di Copertura globale di ExpressRoute?

La velocità effettiva di rete tra le reti locali, resa possibile da Copertura globale di ExpressRoute, è limitata dal più piccolo tra i due circuiti ExpressRoute. Il traffico da locale ad Azure e il traffico da locale a locale condividono stesso circuito e sono soggetti al limite della larghezza di banda stesso. 

### <a name="with-expressroute-global-reach-what-are-the-limits-on-the-number-of-routes-i-can-advertise-and-the-number-of-routes-i-will-receive"></a>Con Copertura globale di ExpressRoute, qual è il limite al numero di route che è possibile annunciare e al numero di route che si riceverà?

Il numero di route che è possibile annunciare a Microsoft nel peering privato di Azure è fissato a 4000 su un circuito Standard o 10.000 su un circuito Premium. Il numero di route che si riceverà da Microsoft nel peering privato di Azure sarà la somma di tutte le route delle reti virtuali di Azure e delle route dalle altre reti locali connesse tramite Copertura globale di ExpressRoute. Verificare di aver impostato sul router locale un limite di prefisso massimo appropriato. 

### <a name="what-is-the-sla-for-expressroute-global-reach"></a>Qual è il contratto di servizio per Copertura globale di ExpressRoute?

Copertura globale di ExpressRoute fornisce lo stesso [contratto di servizio relativo alla disponibilità](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/) del servizio ExpressRoute.
