---
title: Connettersi privatamente a un'app Web usando un endpoint privato di Azure
description: Connettersi privatamente a un'app Web usando un endpoint privato di Azure
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 03/12/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: ad7e04d611129766fe9fb72285fe35ccfbb17626
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2020
ms.locfileid: "79136673"
---
# <a name="using-private-endpoints-for-azure-web-app-preview"></a>Uso di endpoint privati per app Web di Azure (anteprima)

È possibile usare un endpoint privato per l'app Web di Azure per consentire ai client che si trovano nella rete privata di accedere in modo sicuro all'app tramite un collegamento privato. L'endpoint privato usa un indirizzo IP dello spazio di indirizzi della VNet di Azure. Il traffico di rete tra il client nella rete privata e l'app Web attraversa la VNET e un collegamento privato sulla rete dorsale Microsoft, eliminando l'esposizione dalla rete Internet pubblica.

L'uso di un endpoint privato per l'app Web consente di:

- Proteggere l'app Web configurando l'endpoint del servizio eliminando l'esposizione pubblica
- Connettersi in modo sicuro all'app Web da reti locali che si connettono alla VNET usando un peering privato VPN o ExpressRoute.

Se è necessaria una connessione sicura tra il VNET e l'app Web, endpoint servizio è la soluzione più semplice. Se è necessario anche raggiungere l'app Web dall'ambiente locale tramite un gateway di Azure, un VNET con peering a livello di area o un VNET con peering globale, endpoint privato è la soluzione.  

Per ulteriori informazioni sull' [endpoint di servizio][serviceendpoint]

## <a name="conceptual-overview"></a>Informazioni generali

Un endpoint privato è un'interfaccia di rete speciale (NIC) per l'app Web di Azure nella subnet della rete virtuale (VNET).
Quando si crea un endpoint privato per l'app Web, fornisce una connettività sicura tra i client nella rete privata e l'app Web. All'endpoint privato viene assegnato un indirizzo IP dall'intervallo di indirizzi IP della vnet.
La connessione tra l'endpoint privato e l'app Web usa un [collegamento privato][privatelink]protetto. L'endpoint privato viene usato solo per i flussi in ingresso nell'app Web. I flussi in uscita non utilizzeranno questo endpoint privato, ma è possibile inserire i flussi in uscita nella rete in una subnet diversa tramite la [funzionalità di integrazione VNET][vnetintegrationfeature].

La subnet in cui si collega l'endpoint privato può includere altre risorse, non è necessaria una subnet vuota dedicata.
È possibile distribuire un endpoint privato in un'area diversa da quella dell'app Web. 

> [!Note]
>La funzionalità di integrazione VNET non può usare la stessa subnet dell'endpoint privato. si tratta di una limitazione della funzionalità di integrazione di VNET

Dal punto di vista della sicurezza:

- Quando si Abilita l'endpoint di servizio per l'app Web, si disabilita l'accesso pubblico
- È possibile abilitare più endpoint privati in altre reti virtuali e subnet, incluso reti virtuali in altre aree
- L'indirizzo IP della scheda di interfaccia di rete dell'endpoint privato deve essere dinamico, ma rimarrà invariato finché non si elimina l'endpoint privato
- Alla scheda di interfaccia di rete dell'endpoint privato non può essere associato un NSG
- La subnet che ospita l'endpoint privato può avere un NSG associato, ma è necessario disabilitare l'imposizione dei criteri di rete per l'endpoint privato. vedere [questo articolo][disablesecuritype]. Di conseguenza, non è possibile filtrare in base a qualsiasi NSG di accesso all'endpoint privato
- Quando si Abilita l'endpoint privato per l'app Web, la configurazione delle [restrizioni di accesso][accessrestrictions] dell'app Web non viene valutata.
- È possibile ridurre il rischio di exfiltration dei dati da VNET rimuovendo tutte le regole NSG in cui la destinazione è un tag Internet o i servizi di Azure. Tuttavia, se si aggiunge un endpoint del servizio app Web nella subnet, sarà possibile raggiungere qualsiasi app Web ospitata nello stesso timbro ed esposta a Internet.

L'endpoint privato per l'app Web è disponibile per il livello PremiumV2 e viene isolato con un ambiente del servizio app esterno.

Nei log HTTP Web dell'app Web è presente l'IP di origine del client. Il protocollo proxy TCP è stato implementato e l'inoltro all'app Web è la proprietà IP del client. Per altre informazioni, vedere [questo articolo][tcpproxy].

![Panoramica globale][1]


## <a name="dns"></a>DNS

Poiché questa funzionalità è in anteprima, la voce DNS non viene modificata durante l'anteprima. È necessario gestire autonomamente la voce DNS nel server DNS privato o nella zona privata di DNS di Azure. Se è necessario usare un nome DNS personalizzato, è necessario aggiungere il nome personalizzato nell'app Web. Durante l'anteprima, il nome personalizzato deve essere convalidato come qualsiasi nome personalizzato, usando la risoluzione DNS pubblica. [riferimento tecnico per la convalida DNS personalizzata][dnsvalidation]

## <a name="pricing"></a>Prezzi

Per informazioni dettagliate sui prezzi, vedere [Prezzi di Collegamento privato di Azure][pricing].

## <a name="limitations"></a>Limitazioni

Sono state migliorate regolarmente le funzionalità di collegamento privato e l'endpoint privato. per informazioni aggiornate sulle limitazioni, vedere [questo articolo][pllimitations] .

## <a name="next-steps"></a>Passaggi successivi

Per distribuire un endpoint privato per l'app Web tramite il portale, vedere la pagina relativa alla [modalità di connessione privata a un'app Web][howtoguide]


<!--Image references-->
[1]: ./media/private-endpoint/schemaglobaloverview.png

<!--Links-->
[serviceendpoint]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
[privatelink]: https://docs.microsoft.com/azure/private-link/private-link-overview
[vnetintegrationfeature]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[disablesecuritype]: https://docs.microsoft.com/azure/private-link/disable-private-endpoint-network-policy
[accessrestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[tcpproxy]: ../../private-link/private-link-service-overview.md#getting-connection-information-using-tcp-proxy-v2
[dnsvalidation]: https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain
[pllimitations]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview#limitations
[pricing]: https://azure.microsoft.com/pricing/details/private-link/
[howtoguide]: https://docs.microsoft.com/azure/private-link/create-private-endpoint-webapp-portal
