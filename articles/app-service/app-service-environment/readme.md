---
title: File Leggimi dell'ambiente del servizio app di Azure
description: Elenca la documentazione che descrive l'ambiente del servizio app di Azure
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 77452413-5193-4762-8b3d-5fa8e4edf1ca
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 8b401036d4fa8e6c4e6430433b1641f5cb4ae010
ms.contentlocale: it-it
ms.lasthandoff: 06/26/2017

---

# <a name="app-service-environment-documentation"></a>Documentazione relativa agli ambienti del servizio app
L'ambiente del servizio app è una funzionalità del servizio app di Azure che offre un ambiente completamente isolato e dedicato per l'esecuzione sicura di app del servizio app di Azure su vasta scala. Questa funzionalità è in grado di ospitare [app Web][webapps], [app per dispositivi mobili][mobileapps], [app per le API][APIApps] e [funzioni][Functions].

Gli ambienti del servizio app sono ideali per i carichi di lavoro dell'applicazione che richiedono:

* Scalabilità molto elevata
* Isolamento e accesso alla rete protetto

I clienti possono creare più ambienti di servizi di applicazione in una singola area di Azure, nonché in più aree di Azure. Questo rende gli Ambienti di servizio dell’App ideali per i livelli dell’applicazione con scalabilità orizzontale senza stato, nel supportare i carichi di lavoro elevati RPS.

Gli ambienti di servizio dell’App sono isolati per eseguire solo le applicazioni di un singolo cliente e sono sempre distribuiti in una rete virtuale. I clienti hanno il controllo con granularità fine del traffico di rete in ingresso e in uscita dell'applicazione tramite [gruppi di sicurezza di rete][NSGs]. Le applicazioni possono anche stabilire connessioni protette ad alta velocità su reti virtuali con risorse aziendali locali.

Spesso le app devono accedere a risorse aziendali, ad esempio database e servizi Web interni. Le applicazioni in esecuzione in ambienti del servizio app possono accedere alle risorse raggiungibili tramite connessioni VPN [da sito a sito][SiteToSite] e [Azure ExpressRoute][ExpressRoute].

* [Che cos'è un ambiente del servizio app?][Intro]
* [Creazione di un ambiente del servizio app][MakeExternalASE]
* [Creazione di un servizio di bilanciamento del carico interno con un ambiente del servizio app][MakeILBASE]
* [Uso di un ambiente del servizio app][UsingASE]
* [Considerazioni sulla rete e ambiente del servizio app][ASENetwork]
* [Creazione di un ambiente del servizio app da un modello][MakeASEfromTemplate]


## <a name="videos"></a>Video
Master Modern PaaS for the Enterprise with Azure App Service (Gestire una PaaS aziendale moderna con il Servizio app di Azure)
>[!VIDEO https://channel9.msdn.com/Events/Ignite/2016/BRK3205/player]

Deploying Highly Scalable and Secure Apps (Distribuzione di app altamente scalabili e sicure)
>[!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON325/player]

Running Enterprise Web and Mobile Apps on Azure App Service (Esecuzione di app aziendali Web e per dispositivi mobili nel Servizio app di Azure)
>[!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3715/player]

## <a name="asev1"></a>ASEv1 ##
Esistono due versioni dell'ambiente del servizio app: ASEv1 e ASEv2. Per informazioni sulla versione ASEv1, vedere [Documentazione relativa agli ambienti del servizio app][ASEv1README].


<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[ASEReadme]: ./readme.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: ../../app-service-web/app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: ../../app-service-web/app-service-app-service-environment-intro.md
[webapps]: ../../app-service-web/app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[APIapps]: ../../app-service-api/app-service-api-apps-why-best-platform.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../../app-service-web/web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../../app-service-web/web-sites-deploy.md
[ASEWAF]: ../../app-service-web/app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[ASEv1README]: ../app-service-app-service-environments-readme.md
[SiteToSite]: ../../vpn-gateway/vpn-gateway-site-to-site-create.md
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/

