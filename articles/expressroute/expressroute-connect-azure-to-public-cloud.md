---
title: Connessione di Azure a cloud pubblici Documenti Microsoft
description: Descrivere vari modi per connettere Azure ad altri cloud pubblici
services: expressroute
author: osamazia
ms.service: expressroute
ms.topic: article
ms.date: 07/24/2019
ms.author: osamaz
ms.openlocfilehash: b8a454c2a104dfe8545cf734bf0b020b8f749bb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73889634"
---
# <a name="connecting-azure-with-public-clouds"></a>Connessione di Azure con cloud pubbliciConnecting Azure with public clouds

Molte aziende stanno perseguendo una strategia multi-cloud a causa di obiettivi aziendali e tecnici. Questi includono costi, flessibilità, disponibilità delle funzionalità, ridondanza, sovranità dei dati, ecc. Questa strategia li aiuta a sfruttare al meglio entrambi i cloud. 

Questo approccio pone anche sfide per l'azienda in termini di architettura di rete e applicazione. Alcune di queste sfide sono la latenza e la velocità effettiva dei dati. Per affrontare queste sfide, i clienti stanno cercando di connettersi direttamente a più cloud. Alcuni provider di servizi forniscono una soluzione per connettere più provider di servizi cloud per i clienti. In altri casi, il cliente può distribuire il proprio router per connettere più cloud pubblici.
## <a name="connectivity-via-expressroute"></a>Connettività tramite ExpressRouteConnectivity via ExpressRoute
ExpressRoute consente ai clienti di estendere le reti locali nel cloud Microsoft tramite una connessione privata facilitata da un provider di connettività. Con ExpressRoute, i clienti possono stabilire connessioni ai servizi cloud Microsoft.With ExpressRoute, customers can establish connections to Microsoft cloud services.

Esistono tre modi per connettersi tramite ExpressRoute.There are three ways to connect via ExpressRoute.

1. Provider Layer3
2. Provider Layer2
3. Connessione diretta

### <a name="layer3-provider"></a>Layer3 Provider

I provider Layer3 sono comunemente noti come provider VPN IP o VPN MPLS. I clienti sfruttano questi provider per la connettività multipunto tra i loro data center, le filiali e il cloud. I clienti si connettono al provider L3 tramite BGP o tramite route predefinita statica. Il provider di servizi annuncia le route tra i siti dei clienti, i data center e il cloud pubblico. 
 
Quando ci si connette tramite il provider Layer3, Microsoft annuncierà le route del VNET del cliente al provider di servizi tramite BGP. Il provider può avere due implementazioni diverse.

![](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l3.png)

Provider può essere atterraggio ogni provider di cloud in un VRF separato, se il traffico da tutti i provider di cloud raggiungerà al router del cliente. Se il cliente esegue BGP con il provider di servizi, queste route verranno annunciate nuovamente ad altri provider di cloud per impostazione predefinita. 

Se il provider di servizi sta atterrando tutti i provider di servizi nello stesso VRF, le route verranno annunciate direttamente ad altri provider di servizi dal provider di servizi. Si presuppone un'operazione BGP standard in cui le route eBGP vengono annunciate ad altri vicini eBGP per impostazione predefinita.

Ogni cloud pubblico ha un limite di prefisso diverso, pertanto durante la distribuzione del provider di servizi di route deve prestare attenzione nella distribuzione delle route.

### <a name="layer2-provider-and-direct-connection"></a>Provider Layer2 e connessione diretta

Anche se la connettività fisica in entrambi i modelli è diversa, ma a layer3 BGP viene stabilita direttamente tra MSEE e il router del cliente. Per ExpressRoute il cliente Direct si connette direttamente a MSEE.For ExpressRoute Direct customer connects to MSEE directly. Nel caso di Layer2, il provider di servizi estende VLAN dalla sede del cliente al cloud. I clienti eseguono BGP sulla rete di livello 2 per connettere i controller di dominio al cloud.
![](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l2.png)
In entrambi i casi, il cliente avrà connessioni point-to-point a ciascuno dei cloud pubblici. Il cliente stabilirà una connessione BGP separata a ogni cloud pubblico. Per impostazione predefinita, le route ricevute da un provider di cloud verranno annunciate ad altri provider di cloud. Ogni provider di cloud ha un limite di prefisso diverso, quindi durante la pubblicità il cliente deve prendersi cura di questi limiti. Il cliente può utilizzare le normali manopole BGP con Microsoft durante la pubblicità di percorsi da altri cloud pubblici.

## <a name="direct-connection-with-expressroute"></a>Connessione diretta con ExpressRouteDirect connection with ExpressRoute

I clienti possono scegliere di connettere ExpressRoute direttamente all'offerta di connettività diretta del provider cloud. Due provider di cloud saranno connessi back to back e BGP sarà stabilito direttamente tra i loro router. Questo tipo di connessione è disponibile con Oracle oggi.

## <a name="site-to-site-vpn"></a>Da sito a VPN

I clienti possono sfruttare Internet per connettere le proprie istanze in Azure con altri cloud pubblici. Quasi tutti i provider cloud offrono funzionalità VPN da sito a sito. Tuttavia, ci potrebbero essere incompatibilità a causa della mancanza di alcune varianti. Ad esempio, alcuni provider di cloud supportano solo IKEv1, pertanto è necessario un endpoint di terminazione VPN in tale cloud. Per i provider di cloud che supportano IKEv2 è possibile stabilire un tunnel diretto tra gateway VPN in entrambi i provider di cloud.

La VPN da sito a sito non è considerata una soluzione ad alta velocità effettiva e a bassa latenza. Tuttavia, può essere utilizzato come backup della connettività fisica.

## <a name="next-steps"></a>Passaggi successivi
Vedere Domande frequenti su [ExpressRoute][ER-FAQ] per ulteriori domande su ExpressRoute e sulla connettività di rete virtuale.

Vedere [Configurare][ER-OCI] la connessione diretta tra Azure e Oracle Cloud per la connettività tra Azure e OracleSee Set up direct connection between Azure and Oracle Cloud for connectivity between Azure and Oracle

<!--Link References-->
[ER-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[ER-OCI]: https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-azure-oci-networking



