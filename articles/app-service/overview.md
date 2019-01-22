---
title: Panoramica del servizio app di Azure | Microsoft Docs
description: Informazioni sul modo in cui il Servizio app di Azure semplifica lo sviluppo e l'hosting di applicazioni Web
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: 94af2caf-a2ec-4415-a097-f60694b860b3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 01/04/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 5f3fed9255b86696b0e71c70adcd7ff23c0573d9
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2019
ms.locfileid: "54352445"
---
# <a name="app-service-overview"></a>Panoramica del Servizio app di Azure

*Il servizio app di Azure* è un servizio per l'hosting di applicazioni Web, API REST e back-end mobili. È possibile usare il linguaggio di sviluppo preferito tra .NET, .NET Core, Java, Ruby, Node.js, PHP o Python. Le applicazioni vengono eseguite e ridimensionate con facilità negli ambienti basati sia su Windows che su Linux. Per gli ambienti basati su Linux, vedere [Servizio app in Linux](containers/app-service-linux-intro.md). 

Il servizio app non si limita ad aggiungere la potenza di Microsoft Azure all'applicazione, in termini di sicurezza, bilanciamento del carico, ridimensionamento automatico e gestione automatizzata. Permettono anche di usufruire delle funzionalità DevOps, come la distribuzione continua da Azure DevOps, GitHub, Hub Docker e altre origini, la gestione dei pacchetti, gli ambienti di gestione temporanea, il dominio personalizzato e i certificati SSL. 

Il servizio app permette di pagare solo le risorse di calcolo di Azure usate. La quantità di risorse di calcolo usate è determinata dal _piano di servizio app_ in cui vengono eseguite le app. Per altre informazioni, vedere [Panoramica approfondita dei piani del servizio app di Azure](overview-hosting-plans.md).

## <a name="why-use-app-service"></a>Perché usare il servizio app?

Ecco alcune delle funzionalità principali del servizio app:

* **Più linguaggi e framework**: il servizio app offre un supporto eccellente per ASP.NET, ASP.NET Core, Java, Ruby, Node.js, PHP o Python. È anche possibile eseguire [PowerShell e altri script o eseguibili](webjobs-create.md) come servizi in background.
* **Ottimizzazione della metodologia DevOps**: è possibile configurare [l'integrazione continua e la distribuzione continua](deploy-continuous-deployment.md) con Azure DevOps, GitHub, BitBucket, Hub Docker o Registro contenitori di Azure, alzare di livello gli aggiornamenti tramite [ambienti di testing e di staging](deploy-staging-slots.md), e gestire le app nel servizio app con [Azure PowerShell](/powershell/azureps-cmdlets-docs) o l'[interfaccia della riga di comando multipiattaforma](/cli/azure/install-azure-cli).
* **Scalabilità globale con disponibilità elevata**: è possibile [aumentare le prestazioni](web-sites-scale.md) o il [numero di istanze](../monitoring-and-diagnostics/insights-how-to-scale.md) manualmente o automaticamente. Le app possono essere ospitate ovunque nell'infrastruttura globale di data center Microsoft e il [contratto di servizio](https://azure.microsoft.com/support/legal/sla/app-service/) per il servizio app assicura disponibilità elevata.
* **Connessioni a piattaforme SaaS e dati locali**: è possibile scegliere tra oltre 50 [connettori](../connectors/apis-list.md) per sistemi aziendali (come SAP), servizi SaaS (come Salesforce) e servizi Internet (come Facebook), nonché accedere ai dati locali con [connessioni ibride](app-service-hybrid-connections.md) e [reti virtuali di Azure](web-sites-integrate-with-vnet.md).
* **Sicurezza e conformità** : il servizio app è [conforme a ISO, SOC e PCI](https://www.microsoft.com/en-us/trustcenter). Autenticare gli utenti con [Azure Active Directory](configure-authentication-provider-aad.md) o con l'account di accesso ai social, ad esempio [Google](configure-authentication-provider-google.md), [Facebook](configure-authentication-provider-facebook.md), [Twitter](configure-authentication-provider-twitter.md) o [ Microsoft](configure-authentication-provider-microsoft.md). Creare [restrizioni per gli indirizzi IP](app-service-ip-restrictions.md) e [gestire le identità del servizio](overview-managed-identity.md).
* **Modelli di applicazione**: in [Azure Marketplace](https://azure.microsoft.com/marketplace/) è possibile scegliere da un esteso elenco di modelli di applicazione, come WordPress, Joomla e Drupal.
* **Integrazione con Visual Studio** : gli strumenti dedicati di Visual Studio semplificano il processo di creazione, distribuzione e debug.
* **API e funzionalità mobili**: il servizio app offre un supporto CORS pronto all'uso per gli scenari API RESTful. Inoltre, semplifica gli scenari di app per dispositivi mobili consentendo l'autenticazione, la sincronizzazione dei dati offline, le notifiche push e altro ancora.
* **Codice senza server**:è possibile eseguire un frammento di codice o uno script su richiesta senza dover eseguire il provisioning esplicito o la gestione dell'infrastruttura e pagare solo il tempo di calcolo usato effettivamente dal codice. Vedere in proposito [Funzioni di Azure](/azure/azure-functions/).

Oltre al servizio app, Azure offre altri servizi che possono essere usati per l'hosting di siti e applicazioni Web. Per la maggior parte degli scenari il servizio app è la scelta migliore.  Per un'architettura di microservizi, prendere in considerazione l'uso di [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric). Se è necessario un maggiore controllo sulle VM in cui viene eseguito il codice, prendere in considerazione l'uso delle [Macchine virtuali di Azure](https://azure.microsoft.com/documentation/services/virtual-machines/). Per altre informazioni su come scegliere uno di questi servizi di Azure, vedere [Confronto tra Servizio app di Azure, Macchine virtuali, Service Fabric e Servizi cloud](overview-compare.md).

## <a name="next-steps"></a>Passaggi successivi

Creare la prima app Web.

> [!div class="nextstepaction"]
> [ASP.NET Core](app-service-web-get-started-dotnet.md)

> [!div class="nextstepaction"]
> [ASP.NET](app-service-web-get-started-dotnet-framework.md)

> [!div class="nextstepaction"]
> [PHP](app-service-web-get-started-php.md)

> [!div class="nextstepaction"]
> [Ruby (in Linux)](containers/quickstart-ruby.md)

> [!div class="nextstepaction"]
> [Node.js](app-service-web-get-started-nodejs.md)

> [!div class="nextstepaction"]
> [Java](app-service-web-get-started-java.md)

> [!div class="nextstepaction"]
> [Python (in Linux)](containers/quickstart-python.md)

> [!div class="nextstepaction"]
> [HTML](app-service-web-get-started-html.md)
