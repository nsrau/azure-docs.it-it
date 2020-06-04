---
title: Connettersi in privato a un'app Web usando un endpoint privato di Azure
description: Connettersi in privato a un'app Web usando un endpoint privato di Azure
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 05/25/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.custom: fasttrack-edit
ms.openlocfilehash: 4c48a2fad927812cc45543243b48a2df81acf73b
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83846954"
---
# <a name="using-private-endpoints-for-azure-web-app-preview"></a>Uso di endpoint privati per app Web di Azure (anteprima)

> [!Note]
> Con l'aggiornamento dell'anteprima, è stata rilasciata la funzionalità di protezione dell'esfiltrazione dei dati.
> La versione di anteprima è disponibile nelle aree Stati Uniti orientali e Stati Uniti occidentali 2 per tutte le app Web Windows e Linux PremiumV2 e per le funzioni con livello elastico Premium. 

È possibile usare un endpoint privato per l'app Web di Azure per consentire ai client che si trovano nella rete privata di accedere in modo sicuro all'app tramite un collegamento privato. L'endpoint privato usa un indirizzo IP dello spazio indirizzi della VNet di Azure. Il traffico di rete tra un client nella rete privata e l'app Web attraversa la VNet e un collegamento privato sulla rete backbone Microsoft, eliminando l'esposizione dalla rete Internet pubblica.

L'uso di un endpoint privato per l'app Web consente di:

- Proteggere l'app Web configurando l'endpoint privato, eliminando l'esposizione pubblica.
- Connettersi in modo sicuro all'app Web da reti locali che si connettono alla VNet usando un peering privato VPN o ExpressRoute.
- Evitare l'esfiltrazione dei dati dalla VNet. 

Se è necessaria una connessione sicura tra la VNet e l'app Web, un endpoint servizio è la soluzione più semplice. Se è necessario anche raggiungere l'app Web dall'ambiente locale tramite un gateway di Azure, una VNet con peering a livello di area o una VNet con peering globale, la soluzione è un endpoint privato.  

Per altre informazioni, vedere [Endpoint servizio][serviceendpoint].

## <a name="conceptual-overview"></a>Informazioni generali

Un endpoint privato è un'interfaccia di rete (NIC) speciale per l'app Web di Azure in una subnet nella rete virtuale (VNet).
Quando si crea un endpoint privato per l'app Web, tale endpoint fornisce connettività sicura tra i client nella rete privata e l'app Web. All'endpoint privato viene assegnato un indirizzo IP dall'intervallo di indirizzi IP della VNet.
La connessione tra l'endpoint privato e l'app Web usa un [collegamento privato][privatelink] sicuro. L'endpoint privato viene usato solo per i flussi in ingresso nell'app Web. I flussi in uscita non useranno questo endpoint privato, ma è possibile inserire i flussi in uscita nella rete in una subnet diversa tramite la [funzionalità di integrazione VNet][vnetintegrationfeature].

La subnet a cui si collega l'endpoint privato può includere altre risorse, non è necessaria una subnet vuota dedicata.
È anche possibile distribuire l'endpoint privato in un'area diversa da quella dell'app Web. 

> [!Note]
>La funzionalità di integrazione VNet non può usare la stessa subnet dell'endpoint privato. Si tratta di una limitazione della funzionalità di integrazione VNet.

Dal punto di vista della sicurezza:

- Quando si abilitano endpoint privati nell'app Web, si disabilitano tutti gli accessi pubblici.
- È possibile abilitare più endpoint privati in altre VNet e subnet, incluse VNet in altre aree.
- L'indirizzo IP dell'interfaccia di rete dell'endpoint privato deve essere dinamico, ma rimane invariato finché non si elimina l'endpoint privato.
- All'interfaccia di rete dell'endpoint privato non può essere associato un gruppo di sicurezza di rete.
- La subnet che ospita l'endpoint privato può avere un gruppo di sicurezza di rete associato, ma è necessario disabilitare l'imposizione dei criteri di rete per l'endpoint privato. Vedere [Disabilitare i criteri di rete per gli endpoint privati][disablesecuritype]. Di conseguenza, non è possibile filtrare in base a qualsiasi gruppo di sicurezza di rete l'accesso all'endpoint privato.
- Quando si abilita l'endpoint privato per l'app Web, la configurazione delle [restrizioni di accesso][accessrestrictions] dell'app Web non viene valutata.
- È possibile eliminare il rischio di esfiltrazione dei dati dalla VNet rimuovendo tutte le regole NSG in cui la destinazione corrisponde al tag Internet o servizi di Azure. Quando si distribuisce un endpoint privato per un'app Web, è possibile raggiungere questa specifica app Web solo tramite l'endpoint privato. Se è disponibile un'altra app Web, è necessario distribuire un altro endpoint privato dedicato per quest'altra app Web.

Nei log HTTP Web dell'app Web è presente l'IP di origine del client. L'implementazione avviene tramite il protocollo proxy TCP, che inoltra la proprietà IP del client all'app Web. Per altre informazioni, vedere [Ottenere informazioni sulla connessione usando il protocollo proxy TCP v2][tcpproxy].


  > [!div class="mx-imgBorder"]
  > ![Panoramica globale dell'endpoint privato dell'app Web](media/private-endpoint/global-schema-web-app.png)

## <a name="dns"></a>DNS

Poiché questa funzionalità è in anteprima, la voce DNS non viene modificata durante l'anteprima. È necessario gestire manualmente la voce DNS nel server DNS privato o nella zona privata di DNS di Azure.
Se occorre usare un nome DNS personalizzato, è necessario aggiungere il nome personalizzato nell'app Web. Durante l'anteprima, il nome personalizzato deve essere convalidato come qualsiasi nome personalizzato, usando la risoluzione DNS pubblica. Per altre informazioni, vedere [Convalida DNS personalizzata][dnsvalidation].

Se è necessario usare la console Kudu o l'API REST Kudu (distribuzione con gli agenti self-hosted di Azure DevOps, ad esempio), è necessario creare due record nella zona privata di DNS di Azure o nel server DNS personalizzato. 
- PrivateEndpointIP nomeappweb.azurewebsites.net 
- PrivateEndpointIP nomeappweb.scm.azurewebsites.net 

## <a name="pricing"></a>Prezzi

Per informazioni dettagliate sui prezzi, vedere [Prezzi di Collegamento privato di Azure][pricing].

## <a name="limitations"></a>Limitazioni

Quando si usa una funzione di Azure in un piano elastico Premium con endpoint privato, per eseguire la funzione nel portale Web di Azure, è necessario avere accesso diretto alla rete oppure si riceverà un errore HTTP 403. In altre parole, il browser deve essere in grado di raggiungere l'endpoint privato per eseguire la funzione dal portale Web di Azure. 

Durante l'anteprima, solo lo slot di produzione viene esposto dietro l'endpoint privato. Gli altri slot devono essere raggiunti dall'endpoint pubblico.

Vengono introdotti regolarmente miglioramenti per la funzionalità di collegamento privato e l'endpoint privato. Vedere [questo articolo][pllimitations] per informazioni aggiornate sulle limitazioni.

## <a name="next-steps"></a>Passaggi successivi

Per distribuire un endpoint privato per l'app Web tramite il portale, vedere [Come connettersi privatamente a un'app Web][howtoguide]




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
