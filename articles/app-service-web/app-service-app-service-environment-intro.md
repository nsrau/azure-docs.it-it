<properties 
	pageTitle="Introduzione all'ambiente del servizio app"
	description="Informazioni sulla funzionalità Ambiente del servizio app che offre unità di scala dedicate, aggiunte alla rete virtuale e sicure per l'esecuzione di tutte le app."
	services="app-service\web"
	documentationCenter=""
	authors="ccompy"
	manager="wpickett"
	editor=""/>

<tags 
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/31/2015"
	ms.author="stefsh"/>

# Introduzione all'ambiente del servizio app

## Panoramica ##
Un ambiente del servizio app è un'opzione del piano di servizio [Premium][PremiumTier] di un Servizio app di Azure che offre un ambiente completamente isolato e dedicato per eseguire in modo sicuro tutte le app, tra cui [app Web][WebApps], [app per dispositivi mobili][MobileApps], [app per le API][APIApps] e [app per la logica][LogicApps] con opzioni di scalabilità estese.

Le risorse di calcolo per un ambiente del servizio app sono dedicate esclusivamente all'esecuzione delle proprie app. Un ambiente del servizio app viene sempre creato in una rete virtuale regionale, che offre alle app nuove opzioni per l'isolamento della rete. Un ambiente del servizio app supporta inoltre opzioni di scalabilità aggiuntive e offre fino a un massimo di cinquanta (50) risorse di calcolo per l'esecuzione delle app. All'esterno di un ambiente del servizio app è previsto un limite di 20 risorse di calcolo per ospitare le applicazioni.

## Supporto della rete virtuale ##
È possibile creare un ambiente del servizio app in una rete virtuale regionale preesistente o in una nuova rete virtuale regionale ([altre informazioni sulle reti virtuali][MoreInfoOnVirtualNetworks]). Poiché un ambiente del servizio app è sempre incluso in una rete virtuale regionale, e più precisamente, all'interno di una subnet di una rete virtuale regionale, è possibile usufruire delle funzionalità di sicurezza delle reti virtuali per controllare le comunicazioni di rete in ingresso e in uscita.

È possibile usare i [gruppi di sicurezza di rete][NetworkSecurityGroups] per limitare le comunicazioni di rete in ingresso alla subnet in cui risiede un ambiente del servizio app. In questo modo è possibile eseguire le app protette da dispositivi e servizi upstream, quali firewall per applicazioni Web e provider di servizi SaaS di rete.

Spesso le app devono accedere a risorse aziendali, ad esempio database e servizi Web interni. In base a un approccio comune, questi endpoint vengono resi disponibili solo al traffico di rete interno in una rete virtuale di Azure. Quando un ambiente del servizio app è aggiunto alla stessa rete virtuale dei servizi interni, le app in esecuzione nell'ambiente possono accedere a questi servizi, inclusi gli endpoint raggiungibili tramite connessioni [da sito a sito][SiteToSite] e connessioni [Azure ExpressRoute][ExpressRoute].

## Risorse di calcolo dedicate ##
Tutte le risorse di calcolo in un ambiente del servizio app sono esclusivamente dedicata a una singola sottoscrizione. Un ambiente del servizio app è costituito da un singolo pool di risorse di calcolo front-end e da un numero di pool di risorse di calcolo di lavoro compreso tra uno e tre.

Il pool front-end contiene le risorse di calcolo responsabili della terminazione SSL e del bilanciamento del carico automatico delle richieste delle app all'interno di un ambiente del servizio app.

Ogni pool di lavoro contiene le risorse di calcolo allocate ai [piani di servizio app][AppServicePlan], che a loro volta contengono una o più app del servizio app di Azure. Dato che possono essere presenti fino a tre pool di lavoro diversi in un ambiente del servizio app, è possibile scegliere in modo flessibile diverse risorse di calcolo per ogni pool di lavoro.

È ad esempio possibile creare un pool di lavoro con risorse di calcolo meno potenti per i piani di servizio app dedicati allo sviluppo o al test delle app. Un secondo (o perfino un terzo) pool di lavoro potrebbe usare risorse di calcolo più potenti dedicate ai piani di servizio app che eseguono le app di produzione.

È possibile configurare un ambiente del servizio app con un massimo di cinquanta (50) risorse di calcolo in un singolo pool di lavoro. Per informazioni dettagliate sulla quantità di risorse di calcolo disponibili per il front-end e i pool di lavoro, vedere [Come configurare un ambiente del servizio app][HowToConfigureanAppServiceEnvironment].

Per informazioni dettagliate sulle dimensioni delle risorse di calcolo disponibili supportate in un ambiente del servizio app, visitare la pagina [Dettagli prezzi del servizio app][AppServicePricing] ed esaminare le opzioni disponibili per gli ambienti del servizio app nel piano tariffario Premium.


## Introduzione

Per iniziare a usare gli ambienti del servizio app, vedere [Come creare un ambiente del servizio app][HowToCreateAnAppServiceEnvironment].

Per altre informazioni sulla piattaforma del servizio app di Azure, vedere [Servizio app di Azure][AzureAppService].

Per una panoramica dell'architettura di rete dell'ambiente del servizio app, vedere l'articolo [Panoramica dell'architettura di rete][NetworkArchitectureOverview].

Per altre informazioni sull'uso di un ambiente del servizio app con ExpressRoute, vedere l'articolo seguente su [Express Route e gli ambienti del servizio app][NetworkConfigDetailsForExpressRoute].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[MoreInfoOnVirtualNetworks]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePlan]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[Azure preview portal]: http://portal.azure.com
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[WebApps]: http://azure.microsoft.com/documentation/articles/app-service-web-overview/
[MobileApps]: http://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop-preview/
[APIApps]: http://azure.microsoft.com/documentation/articles/app-service-api-apps-why-best-platform/
[LogicApps]: http://azure.microsoft.com/documentation/articles/app-service-logic-what-are-logic-apps/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[HowToConfigureanAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[NetworkArchitectureOverview]: https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-architecture-overview/
[NetworkConfigDetailsForExpressRoute]: https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/

<!-- IMAGES -->

<!---HONumber=August15_HO9-->