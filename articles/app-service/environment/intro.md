---
title: Introduzione agli ambienti del servizio app - Azure
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
ms.topic: overview
ms.date: 04/19/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 48b053b6520bff2ac83cd02af31194f81413e92c
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2018
ms.locfileid: "53598755"
---
# <a name="introduction-to-the-app-service-environments"></a>Introduzione agli ambienti del servizio app #
 
## <a name="overview"></a>Panoramica ##

L'ambiente del servizio app di Azure è una funzionalità del servizio app di Azure che fornisce un ambiente completamente isolato e dedicato per l'esecuzione sicura di app del servizio app di Azure su vasta scala. Questa funzionalità può ospitare gli elementi seguenti:

* App Web di Windows
* App Web Linux 
* Contenitori Docker
* App per dispositivi mobili
* Funzioni

Gli ambienti del servizio app sono adatti ai carichi di lavoro dell'applicazione che richiedono:

* Scalabilità molto elevata.
* Isolamento e accesso alla rete protetto.
* Uso intensivo della memoria.

I clienti possono creare più ambienti del servizio app in una singola area di Azure o in più aree di Azure. Questa flessibilità rende gli ambienti del servizio app ideali per i livelli applicazione con scalabilità orizzontale senza stato, nel supportare i carichi di lavoro RPS elevati.

Gli ambienti del servizio app sono isolati per eseguire solo le applicazioni di un singolo cliente e sono sempre distribuiti in una rete virtuale. I clienti hanno il controllo con granularità fine del traffico di rete in ingresso e in uscita dell'applicazione. Le applicazioni possono stabilire connessioni protette ad alta velocità tramite VPN a risorse aziendali locali.

* L'ambiente del servizio app viene fornito con un piano tariffario proprio. Vedere come l'[offerta Isolato](https://channel9.msdn.com/Shows/Azure-Friday/Security-and-Horsepower-with-App-Service-The-New-Isolated-Offering?term=app%20service%20environment) sia utile per promuovere iperscalabilità e sicurezza.
* La [versione 2 degli ambienti del servizio app](https://channel9.msdn.com/Blogs/Azure/Azure-Application-Service-Environments-v2-Private-PaaS-Environments-in-the-Cloud?term=app%20service%20environment) offre un framework per la salvaguardia delle app in una subnet della rete e fornisce una distribuzione privata propria di Servizio app di Azure.
* Per ottenere la scalabilità orizzontale è possibile usare più ambienti del servizio app. Per altre informazioni, vedere [come configurare un footprint dell'app con distribuzione geografica](app-service-app-service-environment-geo-distributed-scale.md).
* Gli ambienti del servizio app consentono di configurare l'architettura di sicurezza, come illustrato negli approfondimenti di AzureCon. Per vedere come è stata configurata l'architettura di sicurezza illustrata negli approfondimenti di AzureCon, vedere l'articolo relativo all'[implementazione di un'architettura di sicurezza a più livelli](app-service-app-service-environment-layered-security.md) con gli ambienti del servizio app.
* Le app in esecuzione in ambienti del servizio app possono avere l'accesso controllato da dispositivi upstream, quali Web application firewall. Per altre informazioni, vedere [Web application firewall (WAF)][AppGW].

## <a name="dedicated-environment"></a>Ambiente dedicato ##

Un ambiente del servizio app è dedicato esclusivamente a una singola sottoscrizione e può ospitare 100 istanze del piano di servizio app. Può comprendere da 100 istanze contenute in un singolo piano di servizio app a 100 piani di servizio app da un'istanza singola o qualsiasi combinazione intermedia.

Un ambiente del servizio app è composto da front-end e ruoli di lavoro. I front-end sono responsabili della terminazione HTTP/HTTPS e del bilanciamento del carico automatico delle richieste di app all'interno di un ambiente del servizio app. I front-end vengono aggiunti automaticamente con l'aumentare dei piani di servizio app nell'ambiente del servizio app.

I ruoli di lavoro ospitano app di clienti e sono disponibili in tre dimensioni fisse:

* Un vCPU/3,5 GB di RAM
* Due vCPU/7 GB di RAM
* Quattro vCPU/14 GB di RAM

I clienti non sono tenuti a gestire i front-end e i ruoli di lavoro. Tutta l'infrastruttura viene aggiunta automaticamente nel momento in cui i clienti aumentano il numero di istanze nei rispettivi piani di servizio app. Man mano che i piani di servizio app vengono aggiunti o ridimensionati in un ambiente del servizio app, l'infrastruttura necessaria viene aggiunta o rimossa in base alle esigenze.

Per gli ambienti del servizio app è prevista una tariffa fissa mensile che copre l'infrastruttura e non varia a seconda delle dimensioni dell'ambiente del servizio app. A questa si aggiunge il costo previsto per ogni vCPU del piano di servizio app. Tutte le app ospitate in un ambiente del servizio app fanno parte di uno SKU di prezzi isolato. Per informazioni sui prezzi di un ambiente del servizio app, vedere la pagina [Prezzi del servizio app][Pricing] ed esaminare le opzioni disponibili per gli ambienti del servizio app.

## <a name="virtual-network-support"></a>Supporto della rete virtuale ##

L'ambiente del servizio app è una distribuzione del sevizio app di Azure direttamente in una rete virtuale di Azure Resource Manager del cliente. Per altre informazioni sulle reti virtuali di Azure, vedere le [domande frequenti sulle reti virtuali di Azure](https://azure.microsoft.com/documentation/articles/virtual-networks-faq/). Un ambiente del servizio app esiste sempre in una rete virtuale e, più precisamente, all'interno di una subnet di una rete virtuale. È possibile usare le funzionalità di sicurezza delle reti virtuali per controllare le comunicazioni di rete in ingresso e in uscita per le app.

Un ambiente del servizio app può avere una connessione a Internet con un indirizzo IP pubblico o una connessione interna con il solo indirizzo del servizio di bilanciamento del carico interno di Azure.

I [gruppi di sicurezza di rete][NSGs] permettono di limitare le comunicazioni di rete in ingresso alla subnet in cui risiede un ambiente del servizio app. È possibile usare i gruppi di sicurezza di rete per eseguire le app protette da dispositivi e servizi upstream, quali Web application firewall e provider di servizi SaaS di rete.

Spesso le app devono accedere a risorse aziendali, ad esempio database e servizi Web interni. Se si distribuisce un ambiente del servizio app in una rete virtuale che ha una connessione VPN alla rete locale, le app dell'ambiente del servizio app possono accedere alle risorse locali. Questo vale indipendentemente dal fatto che la VPN sia di tipo [da sito a sito](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site) o [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/).

Per altre informazioni sul funzionamento degli ambienti del servizio app con reti virtuali e reti locali, vedere [Considerazioni sulla rete per un ambiente del servizio app][ASENetwork].

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Application-Service-Environments-v2-Private-PaaS-Environments-in-the-Cloud/player]

## <a name="app-service-environment-v1"></a>Ambiente del servizio app 1 ##

Esistono due versioni dell'ambiente del servizio app: ASEv1 e ASEv2. Le informazioni sopra riportate si basano sulla versione ASEv2. Questa sezione illustra le differenze tra ASEv1 e ASEv2. 

In ASEv1 è necessario gestire manualmente tutte le risorse, inclusi front-end, ruoli di lavoro e indirizzi IP usati per la connessione SSL basata su IP. Per poter aumentare il numero di istanze di un piano di servizio app, è necessario aumentare il numero di istanze del pool di lavoro in cui si vuole ospitare il piano.

ASEv1 usa un modello tariffario diverso rispetto a ASEv2. Nella versione ASEv1, in particolare, si paga per tutti i vCPU allocati, inclusi i vCPU usati per i front-end o i ruoli di lavoro in cui non sono ospitati carichi di lavoro. In ASEv1 la dimensione massima predefinita di un ambiente del servizio app è di 55 host complessivi, inclusi ruoli di lavoro e front-end. Un vantaggio di ASEv1 è quello di poter essere distribuito in una rete virtuale classica e in una rete virtuale di Resource Manager. Per altre informazioni sull'ambiente del servizio app 1, vedere [Introduzione all'ambiente del servizio app 1][ASEv1Intro].

<!--Links-->
[App Service Environments v2]: https://channel9.msdn.com/Blogs/Azure/Azure-Application-Service-Environments-v2-Private-PaaS-Environments-in-the-Cloud?term=app%20service%20environment
[Isolated offering]: https://channel9.msdn.com/Shows/Azure-Friday/Security-and-Horsepower-with-App-Service-The-New-Isolated-Offering?term=app%20service%20environment
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[webapps]: ../overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/waf-overview.md
