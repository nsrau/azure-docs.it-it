---
title: Panoramica di App Web | Documentazione Microsoft
description: Informazioni sul modo in cui il Servizio app di Azure semplifica lo sviluppo e l&quot;hosting di applicazioni Web
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
ms.topic: get-started-article
ms.date: 10/28/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: ee8c76d383867ef4a545f690e75b8246a03d1cc4
ms.openlocfilehash: e9e5408c60332e3def9c312ce06a9a27ecefefb2


---
# <a name="web-apps-overview"></a>Panoramica di App Web
*App Web del servizio app* è una piattaforma di calcolo completamente gestita, ottimizzata per l'hosting di siti Web e applicazioni Web. Questa offerta [PaaS (Platform As a Service)](https://en.wikipedia.org/wiki/Platform_as_a_service) di Microsoft Azure consente di concentrarsi sulla logica di business, mentre Azure gestisce l'infrastruttura per l'esecuzione e il ridimensionamento delle app.

Il video di 5 minuti disponibile di seguito illustra App Web del servizio app di Azure.

>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Web-Apps-with-Yochay-Kiriaty/player]
>
>

> [!INCLUDE [app-service-linux](../../includes/app-service-linux.md)]
> 
> 

## <a name="what-is-a-web-app-in-app-service"></a>Informazioni sull'app Web in un servizio app
Un' *app Web* del servizio app è una risorsa di calcolo offerta da Azure per l'hosting di un sito Web o di un'applicazione Web.  

Le risorse di calcolo possono trovarsi in macchine virtuali condivise o dedicate, in base al piano tariffario scelto. Il codice dell'applicazione viene eseguito in una VM gestita, isolata da altri clienti.

È possibile usare per il codice qualsiasi linguaggio o framework supportato dal [Servizio app di Azure](../app-service/app-service-value-prop-what-is.md), ad esempio ASP.NET, Node.js, Java, PHP o Python. È anche possibile eseguire script che usano [PowerShell e altri linguaggi di scripting](web-sites-create-web-jobs.md#acceptablefiles) in un'app Web.

Per esempi di scenari di applicazione tipici per cui è possibile usare App Web, vedere la pagina relativa agli [scenari con app Web](https://azure.microsoft.com/documentation/scenarios/web-app/) e la sezione **Scenari e indicazioni** dell'articolo [Confronto tra Servizio app di Azure, Macchine virtuali, Service Fabric e Servizi cloud](choose-web-site-cloud-service-vm.md#scenarios).

## <a name="why-use-web-apps"></a>Vantaggi dell'uso di app Web
Ecco alcune delle funzionalità principali del servizio app applicabili ad App Web.

* **Più linguaggi e framework** : il servizio app offre un supporto ottimale per ASP.NET, Node.js, Java, PHP e Python. È anche possibile eseguire [PowerShell e altri script o eseguibili](web-sites-create-web-jobs.md) in VM del servizio app.
* **Ottimizzazione della metodologia DevOps**: è possibile configurare [l'integrazione e la distribuzione continua](app-service-continuous-deployment.md) con Visual Studio Team Services, GitHub o BitBucket, alzare di livello gli aggiornamenti tramite [ambienti di testing e di staging](web-sites-staged-publishing.md), eseguire [test A/B](app-service-web-test-in-production-get-start.md) e gestire le app nel servizio app con [Azure PowerShell](../powershell-install-configure.md) o l'[interfaccia della riga di comando multipiattaforma](../xplat-cli-install.md).
* **Scalabilità globale con disponibilità elevata**: è possibile [aumentare le prestazioni](web-sites-scale.md) o il [numero di istanze](../monitoring-and-diagnostics/insights-how-to-scale.md) manualmente o automaticamente. Le app possono essere ospitate ovunque nell'infrastruttura globale di data center Microsoft e il [contratto di servizio](https://azure.microsoft.com/support/legal/sla/app-service/) per il servizio app assicura disponibilità elevata.
* **Connessioni a piattaforme SaaS e dati locali**: è possibile scegliere tra oltre 50 [connettori](../connectors/apis-list.md) per sistemi aziendali (come SAP, Siebel e Oracle), servizi SaaS (come Salesforce e Office 365) e servizi Internet (come Facebook e Twitter), nonché accedere ai dati locali con [connessioni ibride](../biztalk-services/integration-hybrid-connection-overview.md) e [reti virtuali di Azure](web-sites-integrate-with-vnet.md).
* **Sicurezza e conformità** : il servizio app è [conforme a ISO, SOC e PCI](https://www.microsoft.com/TrustCenter/).
* **Modelli di applicazione** : in [Azure Marketplace](https://azure.microsoft.com/marketplace/) è possibile scegliere da un esteso elenco di modelli di applicazione che consentono di usare una procedura guidata per installare software open source comune come WordPress, Joomla e Drupal.
* **Integrazione con Visual Studio** : gli strumenti dedicati di Visual Studio semplificano il processo di creazione, distribuzione e debug.

Un'app Web può anche sfruttare le funzionalità offerte dalle [app per le API](../app-service-api/app-service-api-apps-why-best-platform.md), ad esempio il supporto di CORS, e dalle [app per dispositivi mobili](../app-service-mobile/app-service-mobile-value-prop.md), ad esempio le notifiche push. Per altre informazioni sui tipi di app nel servizio app, vedere [Informazioni sul servizio app di Azure](../app-service/app-service-value-prop-what-is.md).

Oltre ad App Web nel servizio app, Azure offre altri servizi che possono essere usati per l'hosting di siti e applicazioni Web. Per la maggior parte degli scenari App Web è la scelta migliore.  Per un'architettura di microservizi, prendere in considerazione [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric). Se è necessario maggiore controllo sulle VM in cui viene eseguito il codice, prendere in considerazione [Macchine virtuali di Azure](https://azure.microsoft.com/documentation/services/virtual-machines/). Per altre informazioni su come scegliere uno di questi servizi di Azure, vedere [Confronto tra Servizio app di Azure, Macchine virtuali, Service Fabric e Servizi cloud](choose-web-site-cloud-service-vm.md).

## <a name="getting-started"></a>Introduzione
Per iniziare a distribuire codice di esempio in una nuova app Web nel servizio app, seguire l'esercitazione [Distribuire la prima app Web in Azure in 5 minuti](app-service-web-get-started.md) . Sarà necessario un account Azure gratuito.

Per iniziare a usare il servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.




<!--HONumber=Nov16_HO2-->


