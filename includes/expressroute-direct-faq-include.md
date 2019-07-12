---
title: File di inclusione
description: File di inclusione
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: include
ms.date: 07/09/2019
ms.author: jaredro
ms.custom: include file
ms.openlocfilehash: 2e6eb449f4e7a8dcd6c4547162a575d21f303f83
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712492"
---
### <a name="what-is-expressroute-direct"></a>Che cos'è ExpressRoute Direct?

ExpressRoute Direct offre ai clienti la possibilità di connettersi direttamente alla rete globale di Microsoft in località peer distribuite in modo strategico in tutto il mondo. ExpressRoute Direct fornisce duale 100 o 10 Gbps la connettività, che supporta la connettività di attivo/attivo su larga scala. 

### <a name="how-do-customers-connect-to-expressroute-direct"></a>Come si connettono i clienti a ExpressRoute Direct? 

I clienti dovranno collaborare con i gestori telefonici locali e i provider di condivisione percorso per ottenere la connettività ai router di ExpressRoute e poter sfruttare ExpressRoute Direct.

### <a name="what-locations-currently-support-expressroute-direct"></a>Attualmente, delle posizioni supportano ExpressRoute Direct? 

Le porte disponibili saranno dinamiche e saranno disponibili tramite PowerShell per visualizzare la capacità. Le località includono e *sono soggette a modifiche in base alla disponibilità*:

* Amsterdam
* Amsterdam2
* Auckland 
* Chicago
* Dallas
* Dublino
* RAS di Hong Kong
* Londra
* Los Angeles
* Melbourne
* New York City
* Perth
* San Antonio
* Seattle
* Seul
* Silicon Valley
* Singapore2 
* Sydney
* Taipei
* Tokyo
* Toronto
* Washington DC
* Washington DC2

### <a name="what-is-the-sla-for-expressroute-direct"></a>Qual è il contratto di servizio per ExpressRoute Direct?

ExpressRoute Direct utilizzerà lo stesso [livello aziendale di ExpressRoute](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/). 

### <a name="what-scenarios-should-customers-consider-with-expressroute-direct"></a>Quali scenari devono considerare i clienti con ExpressRoute Direct?  

ExpressRoute Direct offre ai clienti diretti 100 o 10 Gbps coppie di porte al backbone globale di Microsoft. Gli scenari che offriranno ai clienti i maggiori vantaggi includono: inserimento di grandi quantità di dati, isolamento fisico per i mercati regolamentati e capacità dedicata per gli scenari continui, ad esempio il rendering. 

### <a name="what-is-the-billing-model-for-expressroute-direct"></a>Qual è il modello di fatturazione per ExpressRoute Direct? 

Per la coppia di porte di ExpressRoute Direct verrà fatturato un importo fisso. I circuiti standard saranno inclusi senza ore aggiuntive, mentre per quelli premium è previsto un piccolo addebito aggiuntivo. Il traffico in uscita verrà fatturato in base ai singoli circuiti a seconda della zona in cui si trova la località di peering.

### <a name="when-does-billing-start-for-the-expressroute-direct-port-pairs"></a>Non avviare fatturazione vengono eseguiti per le coppie di porta diretta di ExpressRoute?

Le coppie di porte di ExpressRoute Direct vengono fatturate 45 giorni dopo la creazione della risorsa di ExpressRoute Direct o quando uno o entrambi i collegamenti vengono abilitati, a seconda della condizione che si verifica prima. Il periodo di prova di 45 giorni è garantito per consentire ai clienti di completare il processo di Cross Connection con il provider del servizio di condivisione del percorso.
