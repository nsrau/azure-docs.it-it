---
title: Connettersi privatamente a un'app Web usando l'endpoint privato di AzureConnect privately to a Web App using Azure Private Endpoint
description: Connettersi privatamente a un'app Web usando l'endpoint privato di AzureConnect privately to a Web App using Azure Private Endpoint
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 03/18/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.custom: fasttrack-edit
ms.openlocfilehash: c2717b1f29af39c6fdc4602b11acba131d959f03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534389"
---
# <a name="using-private-endpoints-for-azure-web-app-preview"></a>Uso di endpoint privati per l'app Web di Azure (anteprima)Using Private Endpoints for Azure Web App (Preview)

> [!Note]
> L'anteprima è disponibile nelle regioni East US e West US 2 per tutte le funzioni PremiumV2 Windows e Linux Web Apps e Elastic Premium. 

È possibile usare l'endpoint privato per l'app Web di Azure per consentire ai client che si trovano nella rete privata di accedere in modo sicuro all'app tramite Private Link.You can use Private Endpoint for your Azure Web App to allow clients located in your private network to secure access the app over Private Link. L'endpoint privato usa un indirizzo IP dallo spazio di indirizzi della rete virtuale di Azure.The Private Endpoint uses an IP address from your Azure VNet address space. Il traffico di rete tra un client nella rete privata e l'app Web attraversa la rete virtuale e un collegamento privato nella rete backbone Microsoft, eliminando l'esposizione da Internet pubblico.

L'utilizzo di Private Endpoint per l'app Web consente di:

- Proteggere l'app Web configurando l'endpoint del servizio, eliminando l'esposizione pubblica.
- Connettersi in modo sicuro ad app Web da reti locali che si connettono alla rete virtuale tramite una VPN o il peering privato ExpressRoute.Securely connect to Web App from on-premises networks that connect to the VNet using a VPN or ExpressRoute private peering.

Se è sufficiente una connessione sicura tra la rete virtuale e l'app Web, un endpoint del servizio è la soluzione più semplice. Se è necessario raggiungere anche l'app Web in locale tramite un gateway di Azure, una rete virtuale con peering regionale o una rete virtuale con mapping globale, l'endpoint privato è la soluzione.  

Per ulteriori informazioni, vedere [Endpoint del servizio][serviceendpoint].

## <a name="conceptual-overview"></a>Informazioni generali

Un endpoint privato è un'interfaccia di rete speciale (NIC) per l'app Web di Azure in una subnet nella rete virtuale (VNet).
Quando si crea un endpoint privato per l'app Web, fornisce connettività sicura tra i client nella rete privata e l'app Web. All'endpoint privato viene assegnato un indirizzo IP dall'intervallo di indirizzi IP della rete virtuale.
La connessione tra l'endpoint privato e l'app Web utilizza un [collegamento privato][privatelink]protetto . Endpoint privato viene utilizzato solo per i flussi in ingresso nell'app Web. I flussi in uscita non utilizzeranno questo endpoint privato, ma è possibile inserire flussi in uscita nella rete in una subnet diversa tramite la funzionalità di [integrazione della rete virtuale.][vnetintegrationfeature]

La subnet in cui si collega l'endpoint privato può avere altre risorse, non è necessaria una subnet vuota dedicata.
È inoltre possibile distribuire l'endpoint privato in un'area diversa rispetto all'app Web. 

> [!Note]
>La funzionalità di integrazione della rete virtuale non può utilizzare la stessa subnet dell'endpoint privato, si tratta di una limitazione della funzionalità di integrazione della rete virtuale.

Dal punto di vista della sicurezza:

- Quando si abilitano gli endpoint privati nell'app Web, si disabilita l'accesso pubblico.
- È possibile abilitare più endpoint privati in altre reti virtuali e subnet, incluse le reti virtuali in altre aree.
- L'indirizzo IP della scheda di interfaccia di rete dell'endpoint privato deve essere dinamico, ma rimarrà invariato fino a quando non si elimina l'endpoint privato.
- Alla scheda di interfaccia di rete dell'endpoint privato non può essere associato un gruppo di sicurezza di rete.
- Subnet che ospita l'endpoint privato può avere un gruppo di sicurezza di rete associato, ma è necessario disabilitare l'imposizione dei criteri di rete per l'endpoint privato: vedere Disabilitare i criteri di [rete per gli endpoint privati.][disablesecuritype] Di conseguenza, non è possibile filtrare in base a qualsiasi gruppo di sicurezza di rete l'accesso all'endpoint privato.
- Quando si abilita l'endpoint privato nell'app Web, la configurazione delle restrizioni di [accesso][accessrestrictions] dell'app Web non viene valutata.
- È possibile ridurre il rischio di esfiltrazione dei dati dalla rete virtuale rimuovendo tutte le regole del gruppo di sicurezza di rete in cui la destinazione è contrassegnare Internet o i servizi di Azure.You can reduce the data exfiltration risk from the VNet by removing all NSG rules where destination is tag Internet or Azure services. Tuttavia, l'aggiunta di un endpoint privato di un'app Web nella subnet consentirà di raggiungere qualsiasi app Web ospitata nello stesso timbro di distribuzione ed esposta a Internet.

Nei registri HTTP Web dell'app Web è possibile trovare l'indirizzo IP di origine client. Questo viene implementato utilizzando il protocollo proxy TCP, l'inoltro della proprietà IP client fino all'applicazione Web. Per ulteriori informazioni, vedere Recupero delle informazioni di connessione mediante il [proxy TCP v2][tcpproxy].


  > [!div class="mx-imgBorder"]
  > ![Panoramica globale dell'endpoint privato dell'applicazione Web](media/private-endpoint/global-schema-web-app.png)

## <a name="dns"></a>DNS

Poiché questa funzionalità è in anteprima, non si modifica la voce DNS durante l'anteprima. È necessario gestire manualmente la voce DNS nel server DNS privato o nella zona privata DNS di Azure.You need to manage the DNS entry in your private DNS server or Azure DNS private zone yourself.
Se è necessario utilizzare un nome DNS personalizzato, è necessario aggiungere il nome personalizzato nell'app Web. Durante l'anteprima, il nome personalizzato deve essere convalidato come qualsiasi nome personalizzato, utilizzando la risoluzione DNS pubblica. Per ulteriori informazioni, [vedere la convalida DNS personalizzata.][dnsvalidation]

## <a name="pricing"></a>Prezzi

Per informazioni dettagliate sui prezzi, vedere [Prezzi di Collegamento privato di Azure][pricing].

## <a name="limitations"></a>Limitazioni

Stiamo migliorando regolarmente la funzionalità Private Link e l'endpoint privato, consulta [questo articolo][pllimitations] per informazioni aggiornate sulle limitazioni.

## <a name="next-steps"></a>Passaggi successivi

Per distribuire l'endpoint privato per l'app Web tramite il portale, vedere [Come connettersi privatamente a un'app Web][howtoguide]




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
