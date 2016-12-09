---
title: Ambiente del servizio app | Documentazione Microsoft
description: "Che cos&quot;è un ambiente del servizio app di Azure? Introduzione all&quot;ambiente del servizio app."
keywords: ambiente del servizio app di azure, rete virtuale, rete protetta
services: app-service
documentationcenter: 
author: stefsch
manager: wpickett
editor: 
ms.assetid: 1db5c057-3c56-4537-b580-cdd21fe3f3a7
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ad067e4152191cd0b324775f16c5dedfeee4a071


---
# <a name="app-service-environment-documentation"></a>Documentazione relativa agli ambienti del servizio app
Un ambiente del servizio app è un'opzione del piano di servizio [Premium][PremiumTier] del servizio app di Azure che offre un ambiente completamente isolato e dedicato per l'esecuzione sicura su larga scala delle app del servizio app di Azure, incluse [app Web][WebApps], [app per dispositivi mobili][MobileApps] e [app per le API][APIApps].  

Gli ambienti di servizi di app sono ideali per i carichi di lavoro dell'applicazione che richiedono:

* Scalabilità molto elevata
* Isolamento e accesso alla rete protetto

I clienti possono creare più ambienti di servizi di applicazione in una singola area di Azure, nonché in più aree di Azure.  Questo rende gli Ambienti di servizio dell’App ideali per i livelli dell’applicazione con scalabilità orizzontale senza stato, nel supportare i carichi di lavoro elevati RPS.

Gli ambienti di servizio dell’App sono isolati per eseguire solo le applicazioni di un singolo cliente e sono sempre distribuiti in una rete virtuale.  I clienti hanno il controllo dettagliato del traffico di rete in ingresso e in uscita dell'applicazione tramite [gruppi di sicurezza di rete][NetworkSecurityGroups].  Le applicazioni possono anche stabilire connessioni protette ad alta velocità su reti virtuali con risorse aziendali locali.

Spesso le app devono accedere a risorse aziendali, ad esempio database e servizi Web interni.  Le applicazioni in esecuzione in ambienti del servizio app possono accedere alle risorse raggiungibili tramite connessioni VPN [da sito a sito][SiteToSite] da sito a sito [Azure ExpressRoute][ExpressRoute].

* [Che cos'è un ambiente del servizio app?](../app-service-web/app-service-app-service-environment-intro.md)
* [Creazione di un ambiente del servizio app](../app-service-web/app-service-web-how-to-create-an-app-service-environment.md)
* [Creazione di app in un ambiente del servizio app](../app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase.md)
* [Creazione e uso di un servizio di bilanciamento del carico interno con ambienti del servizio App](../app-service-web/app-service-environment-with-internal-load-balancer.md)
* [Configurazione di un ambiente del servizio app](../app-service-web/app-service-web-configure-an-app-service-environment.md) 
* [Scalabilità di app in un ambiente del servizio app](../app-service-web/app-service-web-scale-a-web-app-in-an-app-service-environment.md)
* [Sicurezza di rete e architettura](../app-service-web/app-service-app-service-environment-network-architecture-overview.md)

## <a name="how-tos"></a>Procedure
[!INCLUDE [app-service-blueprint-app-service-environment](../../includes/app-service-blueprint-app-service-environment.md)]

## <a name="videos"></a>Video
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



<!--HONumber=Nov16_HO3-->


