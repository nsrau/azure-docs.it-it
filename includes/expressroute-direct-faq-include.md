---
title: File di inclusione
description: File di inclusione
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: include
ms.date: 02/25/2019
ms.author: jaredro
ms.custom: include file
ms.openlocfilehash: 1bcadc18172535649a0ceb482939ca6a75477e25
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65794747"
---
### <a name="what-is-expressroute-direct"></a>Che cos'è ExpressRoute Direct?

ExpressRoute Direct offre ai clienti la possibilità di connettersi direttamente alla rete globale di Microsoft in località peer distribuite in modo strategico in tutto il mondo. ExpressRoute Direct fornisce doppia connettività a 100 Gbps, che supporta la connettività attivo/attivo su larga scala. 

### <a name="how-do-customers-connect-to-expressroute-direct"></a>Come si connettono i clienti a ExpressRoute Direct? 

I clienti dovranno collaborare con i gestori telefonici locali e i provider di condivisione percorso per ottenere la connettività ai router di ExpressRoute e poter sfruttare ExpressRoute Direct.

### <a name="what-locations-currently-support-expressroute-direct"></a>Attualmente, delle posizioni supportano ExpressRoute Direct? 

Le porte disponibili saranno dinamiche e saranno disponibili tramite PowerShell per visualizzare la capacità. Le località includono e *sono soggette a modifiche in base alla disponibilità*:

* Amsterdam
* Canberra
* Chicago
* Washington DC
* Dallas 
* Hong Kong - R.A.S.
* Londra
* Los Angeles
* New York City
* Parigi
* Perth
* Toronto
* San Antonio
* Seattle
* Seoul
* Silicon Valley
* Singapore 
* Sydney

### <a name="what-is-the-sla-for-expressroute-direct"></a>Qual è il contratto di servizio per ExpressRoute Direct?

ExpressRoute Direct utilizzerà lo stesso [livello aziendale di ExpressRoute](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/). 

### <a name="what-scenarios-should-customers-consider-with-expressroute-direct"></a>Quali scenari devono considerare i clienti con ExpressRoute Direct?  

ExpressRoute Direct fornisce ai clienti coppie di porte dirette da 100 Gbps nella rete backbone globale di Microsoft. Gli scenari che offriranno ai clienti i maggiori vantaggi includono: inserimento di grandi quantità di dati, isolamento fisico per i mercati regolamentati e capacità dedicata per gli scenari continui, ad esempio il rendering. 

### <a name="what-is-the-billing-model-for-expressroute-direct"></a>Qual è il modello di fatturazione per ExpressRoute Direct? 

Per la coppia di porte di ExpressRoute Direct verrà fatturato un importo fisso. I circuiti standard saranno inclusi senza ore aggiuntive, mentre per quelli premium è previsto un piccolo addebito aggiuntivo. Il traffico in uscita verrà fatturato in base ai singoli circuiti a seconda della zona in cui si trova la località di peering.

### <a name="when-does-billing-start-for-the-expressroute-direct-port-pairs"></a>Non avviare fatturazione vengono eseguiti per le coppie di porta diretta di ExpressRoute?

Le coppie di porte di ExpressRoute Direct vengono fatturate 45 giorni dopo la creazione della risorsa di ExpressRoute Direct o quando uno o entrambi i collegamenti vengono abilitati, a seconda della condizione che si verifica prima. Il periodo di prova di 45 giorni è garantito per consentire ai clienti di completare il processo di Cross Connection con il provider del servizio di condivisione del percorso.
