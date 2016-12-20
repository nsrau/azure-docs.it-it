---
title: Introduzione all&quot;ambiente del servizio app
description: "Informazioni sulla funzionalità Ambiente del servizio app che offre unità di scala dedicate, aggiunte alla rete virtuale e sicure per l&quot;esecuzione di tutte le app."
services: app-service
documentationcenter: 
author: stefsch
manager: wpickett
editor: 
ms.assetid: 78e6d4f5-da46-4eb5-a632-b5fdc17d2394
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 465710ca02b8f905b11980e0023d7feceabf34f1


---
# <a name="introduction-to-app-service-environment"></a>Introduzione all'ambiente del servizio app
## <a name="overview"></a>Panoramica
Un ambiente del servizio app è un'opzione del piano di servizio [Premium][PremiumTier] del servizio app di Azure che offre un ambiente completamente isolato e dedicato per l'esecuzione sicura su larga scala delle app del servizio app di Azure, incluse [app Web][WebApps], [app per dispositivi mobili][MobileApps] e [app per le API][APIApps].  

Gli ambienti di servizi di app sono ideali per i carichi di lavoro dell'applicazione che richiedono:

* Scalabilità molto elevata
* Isolamento e accesso alla rete protetto

I clienti possono creare più ambienti di servizi di applicazione in una singola area di Azure, nonché in più aree di Azure.  Questo rende gli Ambienti di servizio dell’App ideali per i livelli dell’applicazione con scalabilità orizzontale senza stato, nel supportare i carichi di lavoro elevati RPS.

Gli ambienti di servizio dell’App sono isolati per eseguire solo le applicazioni di un singolo cliente e sono sempre distribuiti in una rete virtuale.  I clienti hanno un controllo accurato sul traffico di rete sia in ingresso che in uscita dall'applicazione e le applicazioni possono stabilire connessioni protette ad alta velocità su reti virtuali alle risorse aziendali locali.

Tutti gli articoli e le procedure sugli ambienti del servizio app sono disponibili nel [File LEGGIMI per gli ambienti di servizio dell'applicazione](../app-service/app-service-app-service-environments-readme.md).

Per una panoramica del modo in cui gli ambienti del servizio app consentono la scalabilità elevata e proteggono l'accesso di rete, vedere l'[approfondimento di AzureCon][AzureConDeepDive] relativo agli ambienti del servizio app.

Per un approfondimento sull'aumento del numero di istanze usando più ambienti del servizio app, vedere l'articolo relativo alla configurazione di un [footprint di app con distribuzione geografica][GeodistributedAppFootprint].

Per vedere come è stata configurata l'architettura di sicurezza illustrata negli approfondimenti di AzureCon, vedere l'articolo sull'implementazione di una [architettura di sicurezza a più livelli](app-service-app-service-environment-layered-security.md) con gli ambienti del servizio app.

Le app in esecuzione in ambienti di servizio app possono avere l'accesso controllato da dispositivi upstream, quali i firewall di applicazione web (WAF).  L'articolo sulla [configurazione di un WAF per gli ambienti di servizio app](app-service-app-service-environment-web-application-firewall.md) tratta di questo scenario. 

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="dedicated-compute-resources"></a>Risorse di calcolo dedicate
Tutte le risorse di calcolo in un ambiente di servizio dell’app sono dedicate esclusivamente a una singola sottoscrizione e un ambiente di servizio dell’App può essere configurato con un massimo di cinquanta (50) risorse di calcolo per l'utilizzo esclusivo da parte un'unica applicazione.

Un ambiente del servizio app è costituito da un pool di risorse di calcolo front-end e da un numero di pool di risorse di calcolo di lavoro compreso tra uno e tre. 

Il pool front-end contiene le risorse di calcolo responsabili della terminazione SSL e del bilanciamento del carico automatico delle richieste delle app all'interno di un ambiente del servizio app. 

Ogni pool di lavoro contiene le risorse di calcolo allocate ai [piani del servizio app][AppServicePlan], che a loro volta contengono una o più app del servizio app di Azure.  Dato che possono essere presenti fino a tre pool di lavoro diversi in un ambiente del servizio app, è possibile scegliere in modo flessibile diverse risorse di calcolo per ogni pool di lavoro.  

È ad esempio possibile creare un pool di lavoro con risorse di calcolo meno potenti per i piani di servizio app dedicati allo sviluppo o al test delle app.  Un secondo (o perfino un terzo) pool di lavoro potrebbe usare risorse di calcolo più potenti dedicate ai piani di servizio app che eseguono le app di produzione.

Per informazioni dettagliate sulla quantità di risorse di calcolo disponibili per il front-end e i pool di lavoro, vedere [Come configurare un ambiente del servizio app][HowToConfigureanAppServiceEnvironment].  

Per informazioni dettagliate sulle dimensioni delle risorse di calcolo disponibili supportate in un ambiente del servizio app, visitare la pagina [Prezzi del servizio app][AppServicePricing] ed esaminare le opzioni disponibili per gli ambienti del servizio app nel piano tariffario Premium.

## <a name="virtual-network-support"></a>Supporto della rete virtuale
Un ambiente del servizio app può essere creato **in** una rete virtuale di Azure Resource Manager **o** in una rete virtuale del modello di distribuzione classica. [Altre informazioni sulle reti virtuali][MoreInfoOnVirtualNetworks].  Poiché un ambiente del servizio app è sempre incluso in una rete virtuale, e più precisamente, all'interno di una subnet di una rete virtuale, è possibile usufruire delle funzionalità di sicurezza delle reti virtuali per controllare le comunicazioni di rete in ingresso e in uscita.  

Un ambiente del servizio app può avere connessione a Internet con un indirizzo IP pubblico o connessione interna con un indirizzo del Servizio di bilanciamento del carico interno di Azure.

È possibile usare i [gruppi di sicurezza di rete][NetworkSecurityGroups] per limitare le comunicazioni di rete in ingresso alla subnet in cui risiede un ambiente del servizio app.  In questo modo è possibile eseguire le app protette da dispositivi e servizi upstream, quali firewall per applicazioni Web e provider di servizi SaaS di rete.

Spesso le app devono accedere a risorse aziendali, ad esempio database e servizi Web interni.  In base a un approccio comune, questi endpoint vengono resi disponibili solo al traffico di rete interno in una rete virtuale di Azure.  Quando un ambiente del servizio app è aggiunto alla stessa rete virtuale dei servizi interni, le app in esecuzione nell'ambiente possono accedere a questi servizi, inclusi gli endpoint raggiungibili tramite connessioni [da sito a sito][SiteToSite] e connessioni [Azure ExpressRoute][ExpressRoute].

Per altre informazioni sul funzionamento di ambienti di servizio app con reti virtuali e reti locali, vedere gli articoli seguenti su [architettura di rete][NetworkArchitectureOverview], [controllo del traffico in ingresso][ControllingInboundTraffic] e [connessione sicura ai back-end][SecurelyConnectingToBackends]. 

## <a name="getting-started"></a>Introduzione
Per iniziare a usare gli ambienti del servizio app, vedere [Come creare un ambiente del servizio app][HowToCreateAnAppServiceEnvironment]

Tutti gli articoli e le procedure sugli ambienti del servizio app sono disponibili nel [file LEGGIMI per gli ambienti di servizio dell'applicazione](../app-service/app-service-app-service-environments-readme.md).

Per altre informazioni sulla piattaforma del servizio app di Azure, vedere [Servizio app di Azure][AzureAppService].

Per una panoramica dell'architettura di rete dell'ambiente del servizio app, vedere l'articolo [Panoramica dell'architettura di rete][NetworkArchitectureOverview].

Per altre informazioni sull'uso di un ambiente del servizio app con ExpressRoute, vedere l'articolo seguente su [Express Route e gli ambienti del servizio app][NetworkConfigDetailsForExpressRoute].

[!INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[MoreInfoOnVirtualNetworks]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePlan]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[WebApps]: http://azure.microsoft.com/documentation/articles/app-service-web-overview/
[MobileApps]: http://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop-preview/
[APIApps]: http://azure.microsoft.com/documentation/articles/app-service-api-apps-why-best-platform/
[LogicApps]: http://azure.microsoft.com/documentation/articles/app-service-logic-what-are-logic-apps/
[AzureConDeepDive]:  https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/
[GeodistributedAppFootprint]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-geo-distributed-scale/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[HowToConfigureanAppServiceEnvironment]:  http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[ControllingInboundTraffic]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[SecurelyConnectingToBackends]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-securely-connecting-to-backend-resources/
[NetworkArchitectureOverview]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-architecture-overview/
[NetworkConfigDetailsForExpressRoute]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/ 

<!-- IMAGES -->





<!--HONumber=Nov16_HO3-->


