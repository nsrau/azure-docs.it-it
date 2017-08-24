---
title: Introduzione agli ambienti del servizio app di Azure
description: Breve panoramica degli ambienti del servizio app di Azure
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 3c7eaefa-1850-4643-8540-428e8982b7cb
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.translationtype: HT
ms.sourcegitcommit: 79bebd10784ec74b4800e19576cbec253acf1be7
ms.openlocfilehash: e21c4c3e2c212d86a0dbe2211564c2e3a1acf819
ms.contentlocale: it-it
ms.lasthandoff: 08/03/2017

---
# <a name="introduction-to-app-service-environments"></a>Introduzione agli ambienti del servizio app #
 
## <a name="overview"></a>Panoramica ##

Ambiente del servizio app di Azure è una funzionalità del servizio app di Azure che fornisce un ambiente completamente isolato e dedicato per l'esecuzione sicura di app del servizio app di Azure su vasta scala. Questa funzionalità può ospitare [app Web][webapps], [app per dispositivi mobili][mobileapps], [app per le API][APIapps] e [Funzioni][Functions].

Gli ambienti del servizio app sono adatti ai carichi di lavoro dell'applicazione che richiedono:

- Scalabilità molto elevata.
- Isolamento e accesso alla rete protetto.
- Uso intensivo della memoria.

I clienti possono creare più ambienti del servizio app in una singola area di Azure o in più aree di Azure. Questa flessibilità rende gli ambienti del servizio app ideali per i livelli applicazione con scalabilità orizzontale senza stato, nel supportare i carichi di lavoro RPS elevati.

Gli ambienti del servizio app sono isolati per eseguire solo le applicazioni di un singolo cliente e sono sempre distribuiti in una rete virtuale. I clienti hanno il controllo con granularità fine del traffico di rete in ingresso e in uscita dell'applicazione. Le applicazioni possono stabilire connessioni protette ad alta velocità tramite VPN a risorse aziendali locali.

Tutti gli articoli e le istruzioni sulle procedure per gli ambienti del servizio app sono disponibili nel [file LEGGIMI per gli ambienti del servizio app][ASEReadme]:

* Gli ambienti del servizio app consentono l'hosting di app su vasta scala con accesso di rete protetto. Per altre informazioni, vedere gli [approfondimenti di AzureCon ](https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/) sugli ambienti del servizio app.
* Per ottenere la scalabilità orizzontale è possibile usare più ambienti del servizio app. Per altre informazioni, vedere [come configurare un footprint dell'app con distribuzione geografica](https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-geo-distributed-scale/).
* Gli ambienti del servizio app consentono di configurare l'architettura di sicurezza, come illustrato negli approfondimenti di AzureCon. Per vedere come è stata configurata l'architettura di sicurezza illustrata negli approfondimenti di AzureCon, vedere l'articolo relativo all'[implementazione di un'architettura di sicurezza a più livelli](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-app-service-environment-layered-security) con gli ambienti del servizio app.
* Le app in esecuzione in ambienti del servizio app possono avere l'accesso controllato da dispositivi upstream, quali Web application firewall. Per altre informazioni, vedere [Configurazione di un Web application firewall (WAF) per l'ambiente del servizio app](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-app-service-environment-web-application-firewall).

## <a name="dedicated-environment"></a>Ambiente dedicato ##

Un ambiente del servizio app è dedicato esclusivamente a una singola sottoscrizione e può ospitare 100 istanze. Può comprendere da 100 istanze contenute in un singolo piano di servizio app a 100 piani di servizio app da un'istanza singola o qualsiasi combinazione intermedia.

Un ambiente del servizio app è composto da front-end e ruoli di lavoro. I front-end sono responsabili della terminazione HTTP/HTTPS e del bilanciamento del carico automatico delle richieste di app all'interno di un ambiente del servizio app. I front-end vengono aggiunti automaticamente con l'aumentare dei piani di servizio app nell'ambiente del servizio app.

I ruoli di lavoro ospitano app di clienti e sono disponibili in tre dimensioni fisse:

* Un core/3,5 GB di RAM
* Due core/7 GB di RAM
* Quattro core/14 GB di RAM

I clienti non sono tenuti a gestire i front-end e i ruoli di lavoro. Tutta l'infrastruttura viene aggiunta automaticamente nel momento in cui i clienti aumentano il numero di istanze nei rispettivi piani di servizio app. Man mano che i piani di servizio app vengono aggiunti o ridimensionati in un ambiente del servizio app, l'infrastruttura necessaria viene aggiunta o rimossa in base alle esigenze.

Per gli ambienti del servizio app è prevista una tariffa fissa mensile che copre l'infrastruttura e non varia a seconda delle dimensioni dell'ambiente del servizio app. A questa si aggiunge il costo previsto per ogni core del piano di servizio app. Tutte le app ospitate in un ambiente del servizio app fanno parte di uno SKU di prezzi isolato. Per informazioni sui prezzi di un ambiente del servizio app, vedere la pagina [Prezzi del servizio app][Pricing] ed esaminare le opzioni disponibili per gli ambienti del servizio app.

## <a name="virtual-network-support"></a>Supporto della rete virtuale ##

È possibile creare un ambiente del servizio app solo in una rete virtuale di Azure Resource Manager. Per altre informazioni sulle reti virtuali di Azure, vedere le [domande frequenti sulle reti virtuali di Azure](https://azure.microsoft.com/documentation/articles/virtual-networks-faq/). Un ambiente del servizio app esiste sempre in una rete virtuale e, più precisamente, all'interno di una subnet di una rete virtuale. È possibile usare le funzionalità di sicurezza delle reti virtuali per controllare le comunicazioni di rete in ingresso e in uscita per le app.

Un ambiente del servizio app può avere una connessione a Internet con un indirizzo IP pubblico o una connessione interna con il solo indirizzo del servizio di bilanciamento del carico interno di Azure.

I [gruppi di sicurezza di rete][NSGs] permettono di limitare le comunicazioni di rete in ingresso alla subnet in cui risiede un ambiente del servizio app. È possibile usare i gruppi di sicurezza di rete per eseguire le app protette da dispositivi e servizi upstream, quali Web application firewall e provider di servizi SaaS di rete.

Spesso le app devono accedere a risorse aziendali, ad esempio database e servizi Web interni. Se si distribuisce un ambiente del servizio app in una rete virtuale che ha una connessione VPN alla rete locale, le app dell'ambiente del servizio app possono accedere alle risorse locali. Questo vale indipendentemente dal fatto che la VPN sia di tipo [da sito a sito](https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/) o [Azure ExpressRoute](http://azure.microsoft.com/services/expressroute/).

Per altre informazioni sul funzionamento degli ambienti del servizio app con reti virtuali e reti locali, vedere [Considerazioni sulla rete per un ambiente del servizio app][ASENetwork].

## <a name="app-service-environment-v1"></a>Ambiente del servizio app 1 ##

Esistono due versioni dell'ambiente del servizio app: ASEv1 e ASEv2. Le informazioni sopra riportate si basano sulla versione ASEv2. Questa sezione illustra le differenze tra ASEv1 e ASEv2. 

In ASEv1 è necessario gestire manualmente tutte le risorse, inclusi front-end, ruoli di lavoro e indirizzi IP usati per la connessione SSL basata su IP. Per poter aumentare il numero di istanze di un piano di servizio app, è necessario aumentare il numero di istanze del pool di lavoro in cui si vuole ospitare il piano.

ASEv1 usa un modello tariffario diverso rispetto a ASEv2. Nella versione ASEv1, in particolare, si paga per tutti i core allocati, inclusi i core usati per i front-end o i ruoli di lavoro in cui non sono ospitati carichi di lavoro. In ASEv1 la dimensione massima predefinita di un ambiente del servizio app è di 55 host complessivi, inclusi ruoli di lavoro e front-end. Un vantaggio di ASEv1 è quello di poter essere distribuito in una rete virtuale classica e in una rete virtuale di Resource Manager. Per altre informazioni sull'ambiente del servizio app 1, vedere [Introduzione all'ambiente del servizio app 1][ASEv1Intro].

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

