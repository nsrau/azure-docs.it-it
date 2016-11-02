<properties 
	pageTitle="Servizio app di Azure per app Web e app per dispositivi mobili | Microsoft Azure" 
	description="Informazioni su come il servizio app di Azure consente di sviluppare, distribuire e gestire app Web e per dispositivi mobili." 
	keywords="servizio app, servizio app di Azure, costo servizio app, scalabilità, scalabile, distribuzione app, distribuzione app di Azure, PaaS, piattaforma distribuita come servizio"
	services="app-service" 
	documentationCenter="" 
	authors="omarkmsft" 
	manager="dwrede" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="05/25/2016" 
	ms.author="omark"/>

# Informazioni sul servizio app di Azure

Il *servizio app* è un'offerta di [piattaforma distribuita come servizio](https://en.wikipedia.org/wiki/Platform_as_a_service) (PaaS) di Microsoft Azure. Consente di creare app Web e per dispositivi mobili per qualsiasi piattaforma o dispositivo, integrare le app con soluzioni SaaS, connettersi con applicazioni locali e automatizzare i processi aziendali. Azure esegue le app in macchine virtuali (VM) completamente gestite, con le risorse di VM condivise o le VM dedicate desiderate.

Il servizio app include le funzionalità Web e per dispositivi mobili prima fornite separatamente come Siti Web di Azure e Servizi mobili di Azure. Include anche nuove funzionalità per l'automazione dei processi aziendali e l'hosting di API cloud. Come singolo servizio integrato, il servizio app consente di combinare vari componenti (siti Web, back-end di app per dispositivi mobili, API RESTful e processi aziendali) in un'unica soluzione.

Il video di 4 minuti disponibile di seguito spiega brevemente la relazione tra il servizio app e le offerte Azure precedenti e le novità del servizio app.

\+[AZURE.VIDEO app-service-history-lesson] 

## Perché usare il servizio app?

Ecco alcune caratteristiche e funzionalità chiave del servizio app:

- **Più linguaggi e framework**: il servizio app offre un supporto ottimale per ASP.NET, Node.js, Java, PHP e Python. È anche possibile eseguire [Windows PowerShell e altri script o eseguibili](../app-service-web/web-sites-create-web-jobs.md) nelle VM del servizio app.

- **Ottimizzazione della metodologia DevOps**: è possibile configurare [l'integrazione e la distribuzione continua](../app-service-web/app-service-continuous-deployment.md) con Visual Studio Team Services, GitHub o BitBucket, alzare di livello gli aggiornamenti tramite [ambienti di testing e di staging](../app-service-web/web-sites-staged-publishing.md), eseguire [test A/B](../app-service-web/app-service-web-test-in-production-get-start.md) e gestire le app nel servizio app con [Azure PowerShell](../powershell-install-configure.md) o l'[interfaccia della riga di comando multipiattaforma](../xplat-cli-install.md).
 
- **Scalabilità globale con disponibilità elevata**: è possibile [aumentare le prestazioni](../app-service-web/web-sites-scale.md) o [aumentare il numero di istanze](../azure-portal/insights-how-to-scale.md) manualmente o automaticamente. Le app possono essere ospitate ovunque nell'infrastruttura globale di data center Microsoft e il [contratto di servizio](https://azure.microsoft.com/support/legal/sla/app-service/) per il servizio app assicura disponibilità elevata.

- **Connessioni a piattaforme SaaS e dati locali**: è possibile scegliere tra oltre 50 [connettori](../connectors/apis-list.md) per sistemi aziendali (come SAP, Siebel e Oracle), servizi SaaS (come Salesforce e Office 365) e servizi Internet (come Facebook e Twitter), nonché accedere ai dati locali con [connessioni ibride](../biztalk-services/integration-hybrid-connection-overview.md) e [reti virtuali di Azure](../app-service-web/web-sites-integrate-with-vnet.md).

- **Sicurezza e conformità**: il servizio app è [conforme a ISO, SOC e PCI](https://www.microsoft.com/TrustCenter/).

- **Modelli di applicazione**: in [Azure Marketplace](https://azure.microsoft.com/marketplace/) è possibile scegliere da un esteso elenco di modelli di applicazione che consentono di usare una procedura guidata per installare software open source comune come WordPress, Joomla e Drupal.

- **Integrazione con Visual Studio**: gli strumenti dedicati di Visual Studio semplificano il processo di creazione, distribuzione e debug.

## Tipi di app nel servizio app

Il servizio app offre diversi *tipi di app*, ognuno dei quali è progettato per l'hosting di uno specifico tipo di carico di lavoro.

- [**App Web**](../app-service-web/app-service-web-overview.md): per l'hosting di siti Web e applicazioni Web.

- [**App per dispositivi mobili**](../app-service-mobile/app-service-mobile-value-prop.md): per l'hosting di back-end di app per dispositivi mobili.
   
- [**App per le API**](../app-service-api/app-service-api-apps-why-best-platform.md): per l'hosting di API cloud.
 
- [**App per la logica**](../app-service-logic/app-service-logic-what-are-logic-apps.md): per l'automazione dell'accesso e dell'uso dei dati nei cloud senza necessità di scrivere codice.

Il termine *app* fa qui riferimento alle risorse di hosting dedicate all'esecuzione di un carico di lavoro. Prendendo ad esempio le "app Web", si è probabilmente abituati a considerare un'app Web come costituita dalle risorse di calcolo e dal codice dell'applicazione che insieme forniscono funzionalità a un browser. Nel servizio app, invece, un'*app Web* è costituita dalle risorse di calcolo offerte da Azure per l'hosting del codice dell'applicazione. Se l'applicazione è costituita da un front-end Web e da un back-end API RESTful, è possibile distribuire entrambi in un'app Web oppure distribuire il codice di front-end in un'app Web e il codice di back-end in un'app per le API. Un'applicazione può essere costituita da più app del servizio app di diversi tipi.

## Piani di servizio app

I [piani di servizio app](azure-web-sites-web-hosting-plans-in-depth-overview.md) specificano il tipo di risorse di calcolo in cui vengono eseguite le app. Se si prevedono carichi di traffico ridotti, è possibile usare macchine virtuali (VM) condivise. Per carichi più elevati, si possono scegliere VM dedicate di diverse dimensioni. Lo stesso piano può essere condiviso da più app del servizio app, le cui prestazioni vengono aumentate e ridotte insieme al piano.

Se si necessita di maggiore scalabilità e isolamento rete, è possibile eseguire le app in un [ambiente del servizio app](../app-service-web/app-service-app-service-environment-intro.md).

## Prezzi

Per informazioni sul costo del servizio app, vedere [Prezzi del servizio app](https://azure.microsoft.com/pricing/details/app-service/).

## Introduzione al servizio app

È possibile [creare un'app Web, un'app per la logica o un'app per dispositivi mobili temporanea](http://go.microsoft.com/fwlink/?LinkId=523751) immediatamente e in modo gratuito, senza carta di credito, senza impegno e senza problemi.

In alternativa, aprire un [account Azure gratuito](https://azure.microsoft.com/pricing/free-trial/) e provare una delle esercitazioni introduttive:

* [Esercitazione sulla creazione di un'app Web](../app-service-web/app-service-web-get-started.md)
* [Esercitazione sulla creazione di un'app per dispositivi mobili](../app-service-mobile/app-service-mobile-android-get-started.md)
* [Esercitazione sulla creazione di un'app per le API](../app-service-api/app-service-api-dotnet-get-started.md)
* [Esercitazione sulla creazione di un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0803_2016-->