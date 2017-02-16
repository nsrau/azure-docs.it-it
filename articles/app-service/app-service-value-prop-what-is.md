---
title: Servizio app di Azure per app Web, per dispositivi mobili e per le API | Documentazione Microsoft
description: Informazioni su come il servizio app di Azure consente di sviluppare, distribuire e gestire app Web e per dispositivi mobili.
keywords: "servizio app, servizio app di Azure, costo servizio app, scalabilità, scalabile, distribuzione app, distribuzione app Azure, PaaS, piattaforma distribuita come servizio, sito Web, Web, servizi mobili di Azure"
services: app-service
documentationcenter: 
author: omarkmsft
manager: erikre
editor: cephalin
ms.assetid: 979cafa8-eeb6-4d3b-87cf-764a821c3e4f
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/02/2016
ms.author: byvinyal
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: f361e9c815ae20874fc2e40e39d2c20a415d58f2


---
# <a name="what-is-azure-app-service"></a>Informazioni sul servizio app di Azure
Il *servizio app* è un'offerta di [piattaforma distribuita come servizio](https://en.wikipedia.org/wiki/Platform_as_a_service) (PaaS) di Microsoft Azure. Consente di creare app Web e per dispositivi mobili per qualsiasi piattaforma o dispositivo, integrare le app con soluzioni SaaS, connettersi con applicazioni locali e automatizzare i processi aziendali. Azure esegue le app in macchine virtuali (VM) completamente gestite, con le risorse di VM condivise o le VM dedicate desiderate.

Il servizio app include le funzionalità Web e per dispositivi mobili prima fornite separatamente come Siti Web di Azure e Servizi mobili di Azure. Include anche nuove funzionalità per l'automazione dei processi aziendali e l'hosting di API cloud. Come singolo servizio integrato, il servizio app consente di combinare vari componenti (siti Web, back-end di app per dispositivi mobili, API RESTful e processi aziendali) in un'unica soluzione.

Il video di 4 minuti disponibile di seguito spiega brevemente la relazione tra il servizio app e le offerte Azure precedenti e le novità del servizio app.

> [!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/app-service-history-lesson/player]
> 
> 

## <a name="why-use-app-service"></a>Perché usare il servizio app?
Ecco alcune caratteristiche e funzionalità chiave del servizio app:

* **Più linguaggi e framework** : il servizio app offre un supporto ottimale per ASP.NET, Node.js, Java, PHP e Python. È anche possibile eseguire [Windows PowerShell e altri script o eseguibili](../app-service-web/web-sites-create-web-jobs.md) nelle VM del servizio app.
* **Ottimizzazione della metodologia DevOps**: è possibile configurare [l'integrazione e la distribuzione continua](../app-service-web/app-service-continuous-deployment.md) con Visual Studio Team Services, GitHub o BitBucket, alzare di livello gli aggiornamenti tramite [ambienti di testing e di staging](../app-service-web/web-sites-staged-publishing.md), eseguire [test A/B](../app-service-web/app-service-web-test-in-production-get-start.md) e gestire le app nel servizio app con [Azure PowerShell](/powershell/azureps-cmdlets-docs) o l'[interfaccia della riga di comando multipiattaforma](../xplat-cli-install.md).
* **Scalabilità globale con disponibilità elevata**: è possibile [aumentare le prestazioni](../app-service-web/web-sites-scale.md) o il [numero di istanze](../monitoring-and-diagnostics/insights-how-to-scale.md) manualmente o automaticamente. Le app possono essere ospitate ovunque nell'infrastruttura globale di data center Microsoft e il [contratto di servizio](https://azure.microsoft.com/support/legal/sla/app-service/) per il servizio app assicura disponibilità elevata.
* **Connessioni a piattaforme SaaS e dati locali**: è possibile scegliere tra oltre 50 [connettori](../connectors/apis-list.md) per sistemi aziendali (come SAP, Siebel e Oracle), servizi SaaS (come Salesforce e Office 365) e servizi Internet (come Facebook e Twitter), nonché accedere ai dati locali con [connessioni ibride](../biztalk-services/integration-hybrid-connection-overview.md) e [reti virtuali di Azure](../app-service-web/web-sites-integrate-with-vnet.md).
* **Sicurezza e conformità** : il servizio app è [conforme a ISO, SOC e PCI](https://www.microsoft.com/TrustCenter/).
* **Modelli di applicazione**: in [Azure Marketplace](https://azure.microsoft.com/marketplace/) è possibile scegliere da un esteso elenco di modelli che consentono di usare una procedura guidata per installare software open source comune come WordPress, Joomla e Drupal.
* **Integrazione con Visual Studio** : gli strumenti dedicati di Visual Studio semplificano il processo di creazione, distribuzione e debug.

## <a name="app-types-in-app-service"></a>Tipi di app nel servizio app
Il servizio app offre diversi *tipi di app*, ognuno dei quali è progettato per l'hosting di uno specifico carico di lavoro:

* [**App Web**](../app-service-web/app-service-web-overview.md): per l'hosting di siti Web e applicazioni Web.
* [**App per dispositivi mobili**](../app-service-mobile/app-service-mobile-value-prop.md): per l'hosting di back-end di app per dispositivi mobili.
* [**App per le API**](../app-service-api/app-service-api-apps-why-best-platform.md): per l'hosting di API cloud.
* [**App per la logica**](../app-service-logic/app-service-logic-what-are-logic-apps.md): per l'automazione dei processi aziendali e l'integrazione dei sistemi e dei dati nei cloud senza scrivere codice.

Il termine *app* fa qui riferimento alle risorse di hosting dedicate all'esecuzione di un carico di lavoro. Prendendo ad esempio le "app Web", si è probabilmente abituati a considerare un'app Web come costituita dalle risorse di calcolo e dal codice dell'applicazione che insieme forniscono funzionalità a un browser. Nel servizio app, invece, un' *app Web* è costituita dalle risorse di calcolo offerte da Azure per l'hosting del codice dell'applicazione. 

Un'applicazione può essere costituita da più app del servizio app di diversi tipi. Ad esempio se l'applicazione è costituita da un front-end Web e un back-end di API RESTful è possibile:

- Distribuire in un'unica app Web sia il front-end che l'API  
- Distribuire il codice front-end a un'app Web e il codice back-end a un'app per le API 



## <a name="app-service-plans"></a>Piani di servizio app
I [piani di servizio app](azure-web-sites-web-hosting-plans-in-depth-overview.md) rappresentano l'insieme di risorse fisiche usate per ospitare le app.

I piani di servizio app definiscono:

- **Area** (Stati Uniti occidentali, Stati Uniti orientali e così via)
- **Numero di scala** (uno, due, tre istanze e così via)
- **Dimensione dell'istanza** (Small, Medium, Large)
- **Unità SKU** (Free, Shared, Basic, Standard, Premium)

Tutte le applicazioni assegnate a un **piano di servizio app** condividono le risorse definite dal piano che consentono di risparmiare sui costi quando si ospitano più app.

Il **piano di servizio app** può eseguire la scalabilità da unità SKU **Free** e **Shared** a unità SKU **Basic**, **Standard** e **Premium** fornendo anche l'accesso ad altre risorse e funzionalità. Dopo che il piano di servizio app è impostato su **Basic** o versione successiva è inoltre possibile controllare le **dimensioni** e il numero di scala delle macchine virtuali.

L'unità **SKU** e la **scalabilità** del piano di servizio app determina il costo e non il numero di app ospitate nel piano. 

Se si necessita di maggiore scalabilità e isolamento rete, è possibile eseguire le app in un [ambiente del servizio app](../app-service-web/app-service-app-service-environment-intro.md).

## <a name="pricing"></a>Prezzi
Per informazioni sul costo del servizio app, vedere [Prezzi del servizio app](https://azure.microsoft.com/pricing/details/app-service/).

## <a name="test-drive-app-service"></a>Provare il servizio app
[Creare un'app Web, un'app per dispositivi mobili o un'app per la logica di esempio](http://go.microsoft.com/fwlink/?LinkId=523751) e provarla per un'ora, senza alcun impegno e senza l'uso di una carta di credito.

In alternativa, aprire un [account Azure gratuito](https://azure.microsoft.com/pricing/free-trial/)e provare una delle esercitazioni introduttive:

* [Esercitazione sulla creazione di un'app Web](../app-service-web/app-service-web-get-started.md)
* [Esercitazione sulla creazione di un'app per dispositivi mobili](../app-service-mobile/app-service-mobile-android-get-started.md)
* [Esercitazione sulla creazione di un'app per le API](../app-service-api/app-service-api-dotnet-get-started.md)
* [Esercitazione sulla creazione di un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md)




<!--HONumber=Dec16_HO2-->


