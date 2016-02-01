<properties 
	pageTitle="Ambiente del servizio app di Azure" 
	description="Informazioni sul funzionamento del servizio app" 
	keywords="ambiente del servizio app, ambiente del servizio app di azure"
	services="app-service" 
	documentationCenter="" 
	authors="yochay" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/08/2015" 
	ms.author="yochay"/>

# Panoramica

Un ambiente App di servizio è un’opzione di piano di servizio [Premium][PremiumTier] del servizio dell’App di Azure che fornisce un ambiente completamente isolato e dedicato all'esecuzione in modo sicuro delle app di servizio di Azure a livello di scalabilità elevata, tra cui [App Web][WebApps], [App per dispositivi mobili][MobileApps], e [App API][APIApps].

Gli ambienti di servizi di app sono ideali per i carichi di lavoro dell'applicazione che richiedono:

- Scalabilità molto elevata
- Isolamento e accesso alla rete protetto

I clienti possono creare più ambienti di servizi di applicazione in una singola area di Azure, nonché in più aree di Azure. Questo rende gli Ambienti di servizio dell’App ideali per i livelli dell’applicazione con scalabilità orizzontale senza stato, nel supportare i carichi di lavoro elevati RPS.

Gli ambienti di servizio dell’App sono isolati per eseguire solo le applicazioni di un singolo cliente e sono sempre distribuiti in una rete virtuale. I clienti hanno il controllo con granularità fine del traffico di rete in ingresso e in uscita dell'applicazione tramite [gruppi di sicurezza di rete][NetworkSecurityGroups]. Le applicazioni possono anche stabilire connessioni protette ad alta velocità su reti virtuali con risorse aziendali locali.

Spesso le app devono accedere a risorse aziendali, ad esempio database e servizi Web interni. Le applicazioni in esecuzione in ambienti del servizio app possono accedere alle risorse raggiungibili tramite connessioni VPN [da sito a sito][SiteToSite] e [Azure ExpressRoute][ExpressRoute].

[AZURE.INCLUDE [app-service-blueprint-app-service-environment](../../includes/app-service-blueprint-app-service-environment.md)]

<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[WebApps]: http://azure.microsoft.com/documentation/articles/app-service-web-overview/
[MobileApps]: http://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop-preview/
[APIApps]: http://azure.microsoft.com/documentation/articles/app-service-api-apps-why-best-platform/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/

<!---HONumber=AcomDC_0121_2016-->