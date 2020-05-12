---
title: Connettersi privatamente a un'app Web usando un endpoint privato di Azure
description: Connettersi privatamente a un'app Web usando un endpoint privato di Azure
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 05/12/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.custom: fasttrack-edit
ms.openlocfilehash: 6a95c021153a458a4e3f804e64724b73ea1f1937
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83198827"
---
# <a name="using-private-endpoints-for-azure-web-app-preview"></a>Uso di endpoint privati per app Web di Azure (anteprima)

> [!Note]
> La versione di anteprima è disponibile nelle aree Stati Uniti orientali e Stati Uniti occidentali 2 per tutte le app Web PremiumV2 e le funzioni Premium elastiche di Windows e Linux. 

È possibile usare un endpoint privato per l'app Web di Azure per consentire ai client che si trovano nella rete privata di accedere in modo sicuro all'app tramite un collegamento privato. L'endpoint privato usa un indirizzo IP dello spazio di indirizzi della VNet di Azure. Il traffico di rete tra un client nella rete privata e l'app Web attraversa la VNet e un collegamento privato sulla rete dorsale Microsoft, eliminando l'esposizione dalla rete Internet pubblica.

L'uso di un endpoint privato per l'app Web consente di:

- Proteggere l'app Web configurando l'endpoint privato, eliminando l'esposizione pubblica.
- Connettersi in modo sicuro all'app Web da reti locali che si connettono alla VNet usando un peering privato VPN o ExpressRoute.

Se è necessaria una connessione sicura tra il VNet e l'app Web, un endpoint del servizio è la soluzione più semplice. Se è necessario anche raggiungere l'app Web dall'ambiente locale tramite un gateway di Azure, una VNet con peering a livello di area o un VNet con peering globale, l'endpoint privato è la soluzione.  

Per altre informazioni, vedere [endpoint del servizio][serviceendpoint].

## <a name="conceptual-overview"></a>Informazioni generali

Un endpoint privato è un'interfaccia di rete speciale (NIC) per l'app Web di Azure in una subnet nella rete virtuale (VNet).
Quando si crea un endpoint privato per l'app Web, fornisce connettività sicura tra i client nella rete privata e nell'app Web. All'endpoint privato viene assegnato un indirizzo IP dall'intervallo di indirizzi IP della VNet.
La connessione tra l'endpoint privato e l'app Web usa un [collegamento privato][privatelink]protetto. L'endpoint privato viene usato solo per i flussi in ingresso nell'app Web. I flussi in uscita non utilizzeranno questo endpoint privato, ma è possibile inserire i flussi in uscita nella rete in una subnet diversa tramite la [funzionalità di integrazione VNet][vnetintegrationfeature].

La subnet in cui si collega l'endpoint privato può includere altre risorse, non è necessaria una subnet vuota dedicata.
È anche possibile distribuire l'endpoint privato in un'area diversa da quella dell'app Web. 

> [!Note]
>La funzionalità di integrazione VNet non può usare la stessa subnet dell'endpoint privato. si tratta di una limitazione della funzionalità di integrazione di VNet.

Dal punto di vista della sicurezza:

- Quando si abilitano endpoint privati nell'app Web, si disabilita tutti gli accessi pubblici.
- È possibile abilitare più endpoint privati in altre reti virtuali e subnet, incluso reti virtuali in altre aree.
- L'indirizzo IP della scheda di interfaccia di rete dell'endpoint privato deve essere dinamico, ma rimarrà invariato finché non si elimina l'endpoint privato.
- Alla scheda di interfaccia di rete dell'endpoint privato non può essere associato un NSG.
- La subnet che ospita l'endpoint privato può avere un NSG associato, ma è necessario disabilitare l'imposizione dei criteri di rete per l'endpoint privato: vedere [disabilitare i criteri di rete per gli endpoint privati][disablesecuritype]. Di conseguenza, non è possibile filtrare in base a qualsiasi NSG di accesso all'endpoint privato.
- Quando si Abilita l'endpoint privato per l'app Web, la configurazione delle [restrizioni di accesso][accessrestrictions] dell'app Web non viene valutata.
- È possibile ridurre il rischio di exfiltration dei dati da VNet rimuovendo tutte le regole NSG in cui la destinazione è un tag Internet o i servizi di Azure. Tuttavia, se si aggiunge un endpoint privato dell'app Web nella subnet, sarà possibile raggiungere qualsiasi app Web ospitata nello stesso indicatore di distribuzione ed esposta a Internet.

Nei log HTTP Web dell'app Web è presente l'IP di origine del client. Questa operazione viene implementata usando il protocollo proxy TCP, che invia la proprietà IP del client all'app Web. Per ulteriori informazioni, vedere [recupero delle informazioni di connessione tramite TCP proxy v2][tcpproxy].


  > [!div class="mx-imgBorder"]
  > ![Panoramica globale dell'endpoint privato dell'app Web](media/private-endpoint/global-schema-web-app.png)

## <a name="dns"></a>DNS

Poiché questa funzionalità è in anteprima, la voce DNS non viene modificata durante l'anteprima. È necessario gestire manualmente la voce DNS nel server DNS privato o nella zona privata di DNS di Azure.
Se è necessario usare un nome DNS personalizzato, è necessario aggiungere il nome personalizzato nell'app Web. Durante l'anteprima, il nome personalizzato deve essere convalidato come qualsiasi nome personalizzato, usando la risoluzione DNS pubblica. Per altre informazioni, vedere [convalida DNS personalizzata][dnsvalidation].

Se è necessario usare la console Kudu o l'API REST Kudu (distribuzione con gli agenti self-hosted di Azure DevOps, ad esempio), è necessario creare due record nella zona privata di DNS di Azure o nel server DNS personalizzato. 
- Yourwebappname.azurewebsites.net PrivateEndpointIP 
- Yourwebappname.scm.azurewebsites.net PrivateEndpointIP 

## <a name="pricing"></a>Prezzi

Per informazioni dettagliate sui prezzi, vedere [Prezzi di Collegamento privato di Azure][pricing].

## <a name="limitations"></a>Limitazioni

Quando si usa funzioni di Azure in un piano Premium elastico con endpoint privato, per eseguire o eseguire la funzione nel portale Web di Azure, è necessario avere accesso diretto alla rete oppure si riceverà un errore HTTP 403. In altre parole, il browser deve essere in grado di raggiungere l'endpoint privato per eseguire la funzione dal portale Web di Azure. 

Durante l'anteprima solo lo slot di produzione viene esposto dietro l'endpoint privato, gli altri slot sono raggiungibili solo dall'endpoint pubblico.

Sono state migliorate regolarmente le funzionalità di collegamento privato e l'endpoint privato. per informazioni aggiornate sulle limitazioni, vedere [questo articolo][pllimitations] .

## <a name="next-steps"></a>Passaggi successivi

Per distribuire un endpoint privato per l'app Web tramite il portale, vedere la pagina relativa alla [modalità di connessione privata a un'app Web][howtoguide]




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
