---
title: File di inclusione
description: File di inclusione
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: include
ms.date: 10/07/2019
ms.author: jaredro
ms.custom: include file
ms.openlocfilehash: 9c53aac47e965a6eba681008d6b6476142190ef8
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2019
ms.locfileid: "72023084"
---
### <a name="what-is-expressroute-direct"></a>Che cos'è ExpressRoute Direct?

ExpressRoute Direct offre ai clienti la possibilità di connettersi direttamente alla rete globale di Microsoft in località peer distribuite in modo strategico in tutto il mondo. ExpressRoute Direct offre connettività dual 100 o 10 Gbps, che supporta la connettività attiva/attiva su larga scala. 

### <a name="how-do-customers-connect-to-expressroute-direct"></a>Come si connettono i clienti a ExpressRoute Direct? 

I clienti dovranno collaborare con i gestori telefonici locali e i provider di condivisione percorso per ottenere la connettività ai router di ExpressRoute e poter sfruttare ExpressRoute Direct.

### <a name="what-locations-currently-support-expressroute-direct"></a>Quali percorsi attualmente supportano ExpressRoute Direct? 

Le porte disponibili saranno dinamiche e saranno disponibili tramite PowerShell per visualizzare la capacità. Le località includono e *sono soggette a modifiche in base alla disponibilità*:

* Amsterdam
* Amsterdam2
* Auckland 
* Chicago
* Dallas
* Dublino
* RAS di Hong Kong
* Londra
* Londra2
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

ExpressRoute Direct fornisce ai clienti le coppie di porte dirette a 100 o 10 Gbps nella backbone globale di Microsoft. Gli scenari che offriranno ai clienti i maggiori vantaggi includono: inserimento di grandi quantità di dati, isolamento fisico per i mercati regolamentati e capacità dedicata per gli scenari continui, ad esempio il rendering. 

### <a name="what-is-the-billing-model-for-expressroute-direct"></a>Qual è il modello di fatturazione per ExpressRoute Direct? 

Per la coppia di porte di ExpressRoute Direct verrà fatturato un importo fisso. I circuiti standard saranno inclusi senza ore aggiuntive, mentre per quelli premium è previsto un piccolo addebito aggiuntivo. Il traffico in uscita verrà fatturato in base ai singoli circuiti a seconda della zona in cui si trova la località di peering.

### <a name="when-does-billing-start-for-the-expressroute-direct-port-pairs"></a>Quando viene avviata la fatturazione per le coppie di porte dirette ExpressRoute?

Le coppie di porte di ExpressRoute Direct vengono fatturate 45 giorni dopo la creazione della risorsa di ExpressRoute Direct o quando uno o entrambi i collegamenti vengono abilitati, a seconda della condizione che si verifica prima. Il periodo di prova di 45 giorni è garantito per consentire ai clienti di completare il processo di Cross Connection con il provider del servizio di condivisione del percorso.
