---
title: Introduzione ad Ambiente del servizio app di Azure
description: Breve panoramica di Ambiente del servizio app di Azure
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
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 0d078aefbf73a45298f397d02ab24b2c8232ecef
ms.contentlocale: it-it
ms.lasthandoff: 06/26/2017

---
# <a name="introduction-to-the-app-service-environment"></a>Introduzione ad Ambiente del servizio app #
 
## <a name="overview"></a>Panoramica ##

Ambiente del servizio app è una funzionalità del Servizio app di Azure che fornisce un ambiente completamente isolato e dedicato per l'esecuzione sicura di app del Servizio app di Azure su vasta scala. Questa funzionalità è in grado di ospitare [app Web][webapps], [app per dispositivi mobili][mobileapps], [app per le API][APIapps] e [Funzioni][Functions].

Gli ambienti del servizio app sono ideali per i carichi di lavoro dell'applicazione che richiedono:

- Scalabilità molto elevata
- Isolamento e accesso alla rete protetto
- Uso intensivo della memoria

I clienti possono creare più ambienti del servizio app sia in una singola area di Azure sia in più aree di Azure. Questo rende gli Ambienti di servizio dell’App ideali per i livelli dell’applicazione con scalabilità orizzontale senza stato, nel supportare i carichi di lavoro elevati RPS.

Gli ambienti di servizio dell’App sono isolati per eseguire solo le applicazioni di un singolo cliente e sono sempre distribuiti in una rete virtuale. I clienti hanno un controllo accurato sul traffico di rete dell'applicazione, sia in ingresso che in uscita, e le applicazioni possono stabilire connessioni protette ad alta velocità su reti VPN a risorse aziendali locali.

Tutti gli articoli e le procedure sugli ambienti del servizio app sono disponibili nel [File LEGGIMI per gli ambienti del servizio app][ASEReadme].

Per una panoramica del modo in cui gli ambienti del servizio app abilitano la scalabilità elevata e proteggono l'accesso alla rete, vedere l'[approfondimento di AzureCon](https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/) relativo agli ambienti del servizio app.

Per un approfondimento sulla scalabilità orizzontale usando più ambienti del servizio app, vedere l'articolo sulle modalità di configurazione di un [footprint di app con distribuzione geografica](https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-geo-distributed-scale/).

Per vedere come è stata configurata l'architettura di sicurezza illustrata negli approfondimenti di AzureCon, vedere l'articolo sull'implementazione di una [architettura di sicurezza a più livelli](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-app-service-environment-layered-security) con gli ambienti del servizio app.

Le app in esecuzione in ambienti di servizio app possono avere l'accesso controllato da dispositivi upstream, quali i firewall di applicazione web (WAF). L'articolo sulla [configurazione di un WAF per gli ambienti di servizio app](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-app-service-environment-web-application-firewall) tratta di questo scenario.

## <a name="dedicated-environment"></a>Ambiente dedicato ##

Un ambiente del servizio App è dedicato esclusivamente a una singola sottoscrizione e può ospitare 100 istanze, che possono essere costituite da 100 istanze contenute in un singolo piano di servizio app, 100 piani di servizio app a singola istanza o qualsiasi combinazione intermedia.

Gli ambienti del servizio app sono costituiti da front-end e ruoli di lavoro. I front-end sono responsabili della terminazione HTTP/HTTPS e del bilanciamento del carico automatico di richieste di app all'interno di un ambiente del servizio app. Per i front-end aggiunti automaticamente come piani di servizio app nell'ambiente del servizio app viene aumentato il numero di istanze.

I ruoli di lavoro ospitano app di clienti e sono disponibili in tre dimensioni fisse:
* 1 core/3,5 GB di RAM
* 2 core/7 GB di RAM
* 4 core/14 GB di RAM

I clienti non sono tenuti a gestire i front-end e i ruoli di lavoro. Tutta l'infrastruttura viene aggiunta automaticamente nel momento in cui i clienti aumentano il numero di istanze nei rispettivi piani di servizio app. Quando in un ambiente del servizio app si creano piani di servizio app o se ne aumenta il numero di istanze, viene aggiunta o rimossa l'infrastruttura necessaria, in base alle esigenze.

Per gli ambienti del servizio app è prevista una tariffa mensile fissa che copre l'infrastruttura e non cambia in base alle dimensioni dell'ambiente del servizio app. A questa è necessario aggiungere il costo previsto per ogni core del piano di servizio app. Tutte le app ospitate in un ambiente del servizio app fanno parte dello SKU di prezzi Isolato. Per informazioni dettagliate sui prezzi relativi a un ambiente del servizio app, visitare la pagina [Prezzi del servizio app][Pricing] ed esaminare le opzioni disponibili per gli ambienti del servizio app.

## <a name="virtual-network-support"></a>Supporto della rete virtuale ##

È possibile creare un ambiente del servizio app solo in una rete virtuale di Azure Resource Manager. Per altre informazioni sulle reti virtuali di Azure, vedere [Domande frequenti sulla rete virtuale di Azure](https://azure.microsoft.com/documentation/articles/virtual-networks-faq/). Poiché un ambiente del servizio app è sempre incluso in una rete virtuale e, più precisamente, all'interno di una subnet di una rete virtuale, è possibile usufruire delle funzionalità di sicurezza delle reti virtuali per controllare le comunicazioni di rete in ingresso e in uscita delle app.

Un ambiente del servizio app può avere connessione a Internet con un indirizzo IP pubblico o connessione interna con un indirizzo del Servizio di bilanciamento del carico interno di Azure.

È possibile usare i [gruppi di sicurezza di rete][NSGs] per limitare le comunicazioni di rete in ingresso alla subnet in cui risiede un ambiente del servizio app. In questo modo è possibile eseguire le app protette da dispositivi e servizi upstream, quali firewall per applicazioni Web e provider di servizi SaaS di rete.

Spesso le app devono accedere a risorse aziendali, ad esempio database e servizi Web interni. Se l'ambiente del servizio app è distribuito in una rete virtuale di Azure con una connessione VPN alla rete locale, le app presenti nell'ambiente del servizio app potranno accedere alle risorse locali. Questo vale indipendentemente dal fatto che la rete VPN sia di tipo [Da sito a sito](https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/) o [Azure ExpressRoute](http://azure.microsoft.com/services/expressroute/).

Per altre informazioni sul funzionamento degli ambienti del servizio app con reti virtuali e reti locali, vedere [App Service Environment network considerations][ASENetwork] (Considerazioni di rete sull'ambiente del servizio app).

## <a name="asev1"></a>ASEv1 ##

Esistono due versioni dell'ambiente del servizio app: ASEv1 e ASEv2. Le informazioni sopra riportate facevano riferimento alla versione ASEv2. Questa sezione illustra le differenze tra ASEv1 e ASEv2. 

In ASEv1 è necessario gestire manualmente tutte le risorse, inclusi front-end, ruoli di lavoro e indirizzi IP usati per la connessione SSL basata su IP. Per poter aumentare il numero di istanze di un piano di servizio app, è necessario prima aumentare il numero di istanze del pool di lavoro in cui si vuole ospitare il piano.

ASEv1 usa un modello tariffario diverso rispetto a ASEv2. Nella versione ASEv1, in particolare, è necessario pagare per tutti i core allocati, inclusi i core usati per i front-end o i ruoli di lavoro in cui non sono ospitati carichi di lavoro. In ASEv1, la dimensione massima predefinita per un piano in un ambiente del servizio app è di 55 host complessivi, inclusi ruoli di lavoro e front-end. ASEv1 ha anche il vantaggio di poter essere distribuito sia in una rete virtuale classica sia in una rete virtuale di Resource Manager. Per altre informazioni su ASEv1, vedere: [Introduzione all'ambiente del servizio app][ASEv1Intro]

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

