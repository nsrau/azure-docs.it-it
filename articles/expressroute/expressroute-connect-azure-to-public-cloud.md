---
title: Connessione di Azure ai cloud pubblici | Microsoft Docs
description: Descrivere diversi modi per connettere Azure ad altri cloud pubblici
services: expressroute
author: osamazia
ms.service: expressroute
ms.topic: article
ms.date: 07/24/2019
ms.author: osamaz
ms.openlocfilehash: 681a99d31e7ededda74aa186ff36cc837125bbda
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70052212"
---
# <a name="connecting-azure-with-public-clouds"></a>Connessione di Azure con cloud pubblici

Molte aziende stanno seguendo una strategia basata su più cloud a causa di obiettivi aziendali e tecnici. Questi includono costi, flessibilità, disponibilità delle funzionalità, ridondanza, sovranità dei dati e così via. Questa strategia consente di sfruttare al meglio entrambi i cloud. 

Questo approccio pone anche le difficoltà per l'azienda in termini di architettura di rete e di applicazione. Alcuni di questi problemi sono la latenza e la velocità effettiva dei dati. Per risolvere questi problemi, i clienti stanno cercando di connettersi direttamente a più cloud. Alcuni provider di servizi offrono una soluzione per connettere più provider di servizi cloud per i clienti. In altri casi, il cliente può distribuire il proprio router per connettere più cloud pubblici.
## <a name="connectivity-via-expressroute"></a>Connettività tramite ExpressRoute
ExpressRoute consente ai clienti di estendere le proprie reti locali nel cloud Microsoft tramite una connessione privata facilitata da un provider di connettività. Con ExpressRoute, i clienti possono stabilire connessioni ai servizi cloud Microsoft.

Esistono tre modi per connettersi tramite ExpressRoute.

1. Provider riceveranno
2. Provider layer2
3. Connessione diretta

### <a name="layer3-provider"></a>Provider riceveranno

I provider riceveranno sono comunemente noti come VPN IP o provider VPN MPLS. I clienti usano questi provider per la connettività multipoint tra i Data Center, i rami e il cloud. I clienti si connettono al provider L3 tramite BGP o la route predefinita statica. Il provider di servizi annuncia le route tra i siti dei clienti, i Data Center e il cloud pubblico. 
 
Quando ci si connette tramite il provider riceveranno, Microsoft annuncia le route VNET del cliente al provider di servizi tramite BGP. Il provider può avere due implementazioni diverse.

![](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l3.png)

È possibile che il provider sbarchi ogni provider di servizi cloud in un VRF separato. Se il traffico da tutti i provider di cloud raggiungerà il router del cliente. Se il cliente esegue BGP con il provider di servizi, queste route verranno annunciate nuovamente ad altri provider di servizi cloud per impostazione predefinita. 

Se il provider di servizi sta per sbarcare tutti i provider di cloud nella stessa VRF, le route verranno annunciate ad altri provider di servizi cloud direttamente dal provider di servizi. Si presuppone che l'operazione BGP standard in cui le route eBGP siano annunciate ad altre eBGP adiacenti per impostazione predefinita.

Ogni cloud pubblico presenta un limite di prefisso diverso, quindi la distribuzione del provider di servizi route deve prestare attenzione nella distribuzione delle route.

### <a name="layer2-provider-and-direct-connection"></a>Provider layer2 e connessione diretta

Anche se la connettività fisica in entrambi i modelli è diversa, ma in riceveranno BGP viene stabilita direttamente tra MSEE e il router del cliente. Per il cliente ExpressRoute Direct si connette direttamente a MSEE. Nel caso di Layer2, il provider di servizi estende la VLAN dalle sedi dei clienti al cloud. I clienti eseguono BGP sulla rete layer2 per connettere i controller di dominio al cloud.
![](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l2.png)
In entrambi i casi, il cliente avrà connessioni Point-to-Point a ogni cloud pubblico. Il cliente stabilirà una connessione BGP separata a ogni cloud pubblico. Per impostazione predefinita, le route ricevute da un provider di cloud verranno annunciate ad altri provider di servizi cloud. Ogni provider di servizi cloud ha un limite di prefisso diverso, quindi, quando si annunciano le route, il cliente deve occuparsi di questi limiti. Il cliente può usare le normali manopole BGP con Microsoft, mentre annuncia le route da altri cloud pubblici.

## <a name="direct-connection-with-expressroute"></a>Connessione diretta a ExpressRoute

I clienti possono scegliere di connettere ExpressRoute direttamente all'offerta di connettività diretta del provider di servizi cloud. Due provider di servizi cloud verranno connessi di nuovo e BGP verrà stabilito direttamente tra i rispettivi router. Questo tipo di connessione è attualmente disponibile con Oracle.

## <a name="site-to-site-vpn"></a>VPN da sito a sito

I clienti possono sfruttare Internet per connettere le istanze in Azure con altri cloud pubblici. Quasi tutti i provider di servizi cloud offrono funzionalità VPN da sito a sito. Tuttavia, potrebbero verificarsi incompatibilità a causa della mancanza di determinate varianti. Alcuni provider di servizi cloud, ad esempio, supportano solo IKEv1, quindi è necessario un endpoint di terminazione VPN in tale cloud. Per i provider di servizi cloud che supportano IKEv2 è possibile stabilire un tunnel diretto tra gateway VPN in entrambi i provider di servizi cloud.

La VPN da sito a sito non è considerata una soluzione ad alta velocità effettiva e a bassa latenza. Tuttavia, può essere utilizzato come backup per la connettività fisica.

## <a name="next-steps"></a>Passaggi successivi
Per altre domande su ExpressRoute e sulla connettività di rete virtuale, vedere domande [frequenti su ExpressRoute][ER-FAQ] .

Vedere [configurare la connessione diretta tra Azure e Oracle Cloud][ER-OCI] per la connettività tra Azure e Oracle

<!--Link References-->
[ER-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[ER-OCI]: https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/oracle/configure-azure-oci-networking



