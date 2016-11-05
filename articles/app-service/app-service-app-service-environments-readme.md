---
title: Ambiente del servizio app | Microsoft Docs
description: Che cos'è un ambiente del servizio app di Azure? Introduzione all'ambiente del servizio app.
keywords: ambiente del servizio app di azure, rete virtuale, rete protetta
services: app-service
documentationcenter: ''
author: yochay
manager: wpickett
editor: ''

ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/15/2016
ms.author: stefsch

---
# Documentazione relativa agli ambienti del servizio app
Un ambiente App di servizio è un’opzione di piano di servizio [Premium][PremiumTier] del servizio dell’App di Azure che fornisce un ambiente completamente isolato e dedicato all'esecuzione in modo sicuro delle app di servizio di Azure a livello di scalabilità elevata, tra cui [App Web][WebApps], [App per dispositivi mobili][MobileApps], e [App API][APIApps].

Gli ambienti di servizi di app sono ideali per i carichi di lavoro dell'applicazione che richiedono:

* Scalabilità molto elevata
* Isolamento e accesso alla rete protetto

I clienti possono creare più ambienti di servizi di applicazione in una singola area di Azure, nonché in più aree di Azure. Questo rende gli Ambienti di servizio dell’App ideali per i livelli dell’applicazione con scalabilità orizzontale senza stato, nel supportare i carichi di lavoro elevati RPS.

Gli ambienti di servizio dell’App sono isolati per eseguire solo le applicazioni di un singolo cliente e sono sempre distribuiti in una rete virtuale. I clienti hanno il controllo con granularità fine del traffico di rete in ingresso e in uscita dell'applicazione tramite [gruppi di sicurezza di rete][NetworkSecurityGroups]. Le applicazioni possono anche stabilire connessioni protette ad alta velocità su reti virtuali con risorse aziendali locali.

Spesso le app devono accedere a risorse aziendali, ad esempio database e servizi Web interni. Le applicazioni in esecuzione in ambienti del servizio app possono accedere alle risorse raggiungibili tramite connessioni VPN [da sito a sito][SiteToSite] e [Azure ExpressRoute][ExpressRoute].

* [Che cos'è un ambiente del servizio app?](../app-service-web/app-service-app-service-environment-intro.md)
* [Creazione di un ambiente del servizio app](../app-service-web/app-service-web-how-to-create-an-app-service-environment.md)
* [Creazione di app in un ambiente del servizio app](../app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase.md)
* [Creazione e uso di un servizio di bilanciamento del carico interno con ambienti del servizio App](../app-service-web/app-service-environment-with-internal-load-balancer.md)
* [Configurazione di un ambiente del servizio app](../app-service-web/app-service-web-configure-an-app-service-environment.md)
* [Scalabilità di app in un ambiente del servizio app](../app-service-web/app-service-web-scale-a-web-app-in-an-app-service-environment.md)
* [Sicurezza di rete e architettura](../app-service-web/app-service-app-service-environment-network-architecture-overview.md)

## Procedure
[!INCLUDE [app-service-blueprint-app-service-environment](../../includes/app-service-blueprint-app-service-environment.md)]

## Video
[!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON325/player]


[!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3715/player]



<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[WebApps]: http://azure.microsoft.com/documentation/articles/app-service-web-overview/
[MobileApps]: http://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop-preview/
[APIApps]: http://azure.microsoft.com/documentation/articles/app-service-api-apps-why-best-platform/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/

<!---HONumber=AcomDC_0720_2016-->