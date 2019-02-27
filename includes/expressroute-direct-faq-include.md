---
title: File di inclusione
description: File di inclusione
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: include
ms.date: 02/19/2018
ms.author: jaredro
ms.custom: include file
ms.openlocfilehash: 9e16c3bb4e4e5ba384bdc664e2c056acb0b70caf
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2019
ms.locfileid: "56448229"
---
### <a name="what-is-expressroute-direct"></a>Che cos'è ExpressRoute Direct?

ExpressRoute Direct offre ai clienti la possibilità di connettersi direttamente alla rete globale di Microsoft in località peer distribuite in modo strategico in tutto il mondo. ExpressRoute Direct fornisce doppia connettività a 100 Gbps, che supporta la connettività attivo/attivo su larga scala. 

### <a name="how-do-customers-connect-to-expressroute-direct"></a>Come si connettono i clienti a ExpressRoute Direct? 

I clienti dovranno collaborare con i gestori telefonici locali e i provider di condivisione percorso per ottenere la connettività ai router di ExpressRoute e poter sfruttare ExpressRoute Direct.

### <a name="what-locations-will-the-100-gbps-expressroute-direct-be-available-for-public-preview"></a>In quali località sarà disponibile per l'anteprima pubblica ExpressRoute Direct da 100 Gbps? 

Un numero selezionato di località di peering di ExpressRoute lo supporterà in fase di anteprima pubblica. Le porte disponibili saranno dinamiche e saranno disponibili tramite PowerShell per visualizzare la capacità. Le località includono e *sono soggette a modifiche in base alla disponibilità*:

* Amsterdam
* Canberra
* Chicago
* Washington DC
* Dallas 
* RAS di Hong Kong
* Los Angeles
* New York City
* Parigi
* San Antonio
* Silicon Valley
* Singapore 

### <a name="what-is-the-sla-for-expressroute-direct"></a>Qual è il contratto di servizio per ExpressRoute Direct?

ExpressRoute Direct utilizzerà lo stesso [livello aziendale di ExpressRoute](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/). 

### <a name="what-scenarios-should-customers-consider-with-expressroute-direct"></a>Quali scenari devono considerare i clienti con ExpressRoute Direct?  

ExpressRoute Direct fornisce ai clienti coppie di porte dirette da 100 Gbps nella rete backbone globale di Microsoft. Gli scenari che offriranno ai clienti i maggiori vantaggi includono: inserimento di grandi quantità di dati, isolamento fisico per i mercati regolamentati e capacità dedicata per gli scenari continui, ad esempio il rendering. 

### <a name="what-is-the-billing-model-for-expressroute-direct"></a>Qual è il modello di fatturazione per ExpressRoute Direct? 

Per la coppia di porte di ExpressRoute Direct verrà fatturato un importo fisso. I circuiti standard saranno inclusi senza ore aggiuntive, mentre per quelli premium è previsto un piccolo addebito aggiuntivo. Il traffico in uscita verrà fatturato in base ai singoli circuiti a seconda della zona in cui si trova la località di peering.

### <a name="when-does-billing-state-for-the-expressroute-direct-port-pairs"></a>Quando avviene la fatturazione per le coppie di porte di ExpressRoute Direct?

Le coppie di porte di ExpressRoute Direct vengono fatturate 45 giorni dopo la creazione della risorsa di ExpressRoute Direct o quando uno o entrambi i collegamenti vengono abilitati, a seconda della condizione che si verifica prima. Il periodo di prova di 45 giorni è garantito per consentire ai clienti di completare il processo di Cross Connection con il provider del servizio di condivisione del percorso.
