---
title: includere file
description: includere file
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: include
ms.date: 10/29/2019
ms.author: jaredro
ms.custom: include file
ms.openlocfilehash: 1f6c65d6d8da275d6c52ac2802092dbb5e4e507d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "73182230"
---
### <a name="what-is-expressroute-direct"></a>Che cos'è ExpressRoute Direct?

ExpressRoute Direct offre ai clienti la possibilità di connettersi direttamente alla rete globale di Microsoft in località peer distribuite in modo strategico in tutto il mondo. ExpressRoute Direct fornisce doppia connettività a 100 o a 10 Gbps, che supporta la connettività attiva-attiva su larga scala. 

### <a name="how-do-customers-connect-to-expressroute-direct"></a>Come si connettono i clienti a ExpressRoute Direct? 

I clienti dovranno collaborare con i gestori telefonici locali e i provider di condivisione percorso per ottenere la connettività ai router di ExpressRoute e poter sfruttare ExpressRoute Direct.

### <a name="what-locations-currently-support-expressroute-direct"></a>Quali località supportano attualmente ExpressRoute Direct? 

Controllare la disponibilità nella [pagina delle località](../articles/expressroute/expressroute-locations-providers.md). 

### <a name="what-is-the-sla-for-expressroute-direct"></a>Qual è il contratto di servizio per ExpressRoute Direct?

ExpressRoute Direct utilizzerà lo stesso [livello aziendale di ExpressRoute](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/). 

### <a name="what-scenarios-should-customers-consider-with-expressroute-direct"></a>Quali scenari devono considerare i clienti con ExpressRoute Direct?  

ExpressRoute Direct fornisce ai clienti coppie di porte dirette a 100 o 10 Gbps nella rete backbone globale di Microsoft. Gli scenari che offriranno ai clienti i maggiori vantaggi includono: inserimento di grandi quantità di dati, isolamento fisico per i mercati regolamentati e capacità dedicata per gli scenari continui, ad esempio il rendering. 

### <a name="what-is-the-billing-model-for-expressroute-direct"></a>Qual è il modello di fatturazione per ExpressRoute Direct? 

Per la coppia di porte di ExpressRoute Direct verrà fatturato un importo fisso. I circuiti standard saranno inclusi senza ore aggiuntive, mentre per quelli premium è previsto un piccolo addebito aggiuntivo. Il traffico in uscita verrà fatturato in base ai singoli circuiti a seconda della zona in cui si trova la località di peering.

### <a name="when-does-billing-start-for-the-expressroute-direct-port-pairs"></a>Quando viene avviata la fatturazione per le coppie di porte di ExpressRoute Direct?

Le coppie di porte di ExpressRoute Direct vengono fatturate 45 giorni dopo la creazione della risorsa di ExpressRoute Direct o quando uno o entrambi i collegamenti vengono abilitati, a seconda della condizione che si verifica prima. Il periodo di prova di 45 giorni è garantito per consentire ai clienti di completare il processo di Cross Connection con il provider del servizio di condivisione del percorso.
