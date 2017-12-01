---
title: Panoramica di App Web | Documentazione Microsoft
description: Informazioni sul modo in cui il Servizio app di Azure semplifica lo sviluppo e l'hosting di applicazioni Web
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 94af2caf-a2ec-4415-a097-f60694b860b3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 01/04/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: f8510bb6b412e9af8aad30ba32bc74206c22042f
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2017
---
# <a name="web-apps-overview"></a>Panoramica di App Web

Le *app Web del servizio app di Azure* , o semplicemente app Web, sono un servizio per l'hosting di applicazioni Web, API REST e back-end mobili. È possibile usare il linguaggio di sviluppo preferito tra .NET, .NET Core, Java, Ruby, Node.js, PHP o Python. È possibile eseguire e ridimensionare le app con facilità in macchine virtuali Windows o Linux. Vedere in proposito [Servizio app in Linux](containers/app-service-linux-intro.md). 

Le app Web non si limitano ad aggiungere la potenza di Microsoft Azure all'applicazione, in termini di sicurezza, bilanciamento del carico, ridimensionamento automatico e gestione automatizzata. Permettono anche di usufruire delle funzionalità DevOps, come la distribuzione continua da Visual Studio Team Services, GitHub, Hub Docker e altre origini, la gestione dei pacchetti, gli ambienti di staging, il dominio personalizzato e i certificati SSL. 

Il servizio app permette di pagare solo le risorse di calcolo di Azure usate. La quantità di risorse di calcolo usate è determinata dal _piano di servizio app_ in cui vengono eseguite le app Web. Per altre informazioni, vedere [Panoramica approfondita dei piani del servizio app di Azure](azure-web-sites-web-hosting-plans-in-depth-overview.md).

Il video di 5 minuti disponibile di seguito illustra App Web del servizio app di Azure.

>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Web-Apps-with-Yochay-Kiriaty/player]
>
>

## <a name="why-use-web-apps"></a>Vantaggi dell'uso di app Web
Ecco alcune delle funzionalità principali delle app Web del servizio app:

* **Più linguaggi e framework**: l'app Web offrono un supporto eccellente per ASP.NET, ASP.NET Core, Java, Ruby, Node.js, PHP o Python. È anche possibile eseguire [PowerShell e altri script o eseguibili](web-sites-create-web-jobs.md) come servizi in background.
* **Ottimizzazione della metodologia DevOps**: è possibile configurare l'[integrazione e la distribuzione continua](app-service-continuous-deployment.md) con Visual Studio Team Services, GitHub, BitBucket, Hub Docker o il servizio contenitore di Azure. alzare di livello gli aggiornamenti tramite [ambienti di testing e di staging](web-sites-staged-publishing.md), Gestire le app Web con [Azure PowerShell](/powershell/azureps-cmdlets-docs) o l'[interfaccia della riga di comando multipiattaforma](/cli/azure/install-azure-cli).
* **Scalabilità globale con disponibilità elevata**: è possibile [aumentare le prestazioni](web-sites-scale.md) o il [numero di istanze](../monitoring-and-diagnostics/insights-how-to-scale.md) manualmente o automaticamente. Le app possono essere ospitate ovunque nell'infrastruttura globale di data center Microsoft e il [contratto di servizio](https://azure.microsoft.com/support/legal/sla/app-service/) per il servizio app assicura disponibilità elevata.
* **Connessioni a piattaforme SaaS e dati locali**: è possibile scegliere tra oltre 50 [connettori](../connectors/apis-list.md) per sistemi aziendali (come SAP), servizi SaaS (come Salesforce) e servizi Internet (come Facebook), nonché accedere ai dati locali con [connessioni ibride](../biztalk-services/integration-hybrid-connection-overview.md) e [reti virtuali di Azure](web-sites-integrate-with-vnet.md).
* **Sicurezza e conformità** : il servizio app è [conforme a ISO, SOC e PCI](https://www.microsoft.com/TrustCenter/). Autenticare gli utenti con [Azure Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md) o con l'account di accesso ai social, ad esempio [Google](app-service-mobile-how-to-configure-google-authentication.md), [Facebook](app-service-mobile-how-to-configure-facebook-authentication.md), [Twitter](app-service-mobile-how-to-configure-twitter-authentication.md) o [ Microsoft](app-service-mobile-how-to-configure-microsoft-authentication.md). Creare [restrizioni per gli indirizzi IP](app-service-ip-restrictions.md) e [gestire le identità del servizio](app-service-managed-service-identity.md).
* **Modelli di applicazione**: in [Azure Marketplace](https://azure.microsoft.com/marketplace/) è possibile scegliere da un esteso elenco di modelli di applicazione, come WordPress, Joomla e Drupal.
* **Integrazione con Visual Studio** : gli strumenti dedicati di Visual Studio semplificano il processo di creazione, distribuzione e debug.
* **API e funzionalità mobili**: le app Web offrono un supporto CORS pronto all'uso per gli scenari API RESTful. Inoltre, semplificano gli scenari di app per dispositivi mobili consentendo l'autenticazione, la sincronizzazione dei dati offline, le notifiche push e altro ancora.
* **Codice senza server**:è possibile eseguire un frammento di codice o uno script su richiesta senza dover eseguire il provisioning esplicito o la gestione dell'infrastruttura e pagare solo il tempo di calcolo usato effettivamente dal codice. Vedere in proposito [Funzioni di Azure](/azure/azure-functions/).

Oltre ad App Web nel servizio app, Azure offre altri servizi che possono essere usati per l'hosting di siti e applicazioni Web. Per la maggior parte degli scenari App Web è la scelta migliore.  Per un'architettura di microservizi, prendere in considerazione l'uso di [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric). Se è necessario un maggiore controllo sulle VM in cui viene eseguito il codice, prendere in considerazione l'uso delle [Macchine virtuali di Azure](https://azure.microsoft.com/documentation/services/virtual-machines/). Per altre informazioni su come scegliere uno di questi servizi di Azure, vedere [Confronto tra Servizio app di Azure, Macchine virtuali, Service Fabric e Servizi cloud](choose-web-site-cloud-service-vm.md).

## <a name="next-steps"></a>Passaggi successivi

Creare la prima app Web.

> [!div class="nextstepaction"]
> [ASP.NET](app-service-web-get-started-dotnet.md)

> [!div class="nextstepaction"]
> [PHP](app-service-web-get-started-php.md)

> [!div class="nextstepaction"]
> [Node.JS](app-service-web-get-started-nodejs.md)

> [!div class="nextstepaction"]
> [Java](app-service-web-get-started-java.md)

> [!div class="nextstepaction"]
> [Python](app-service-web-get-started-python.md)

> [!div class="nextstepaction"]
> [HTML](app-service-web-get-started-html.md)

