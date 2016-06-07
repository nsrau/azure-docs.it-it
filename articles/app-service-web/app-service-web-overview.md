<properties
	pageTitle="Panoramica di App Web | Microsoft Azure"
	description="Informazioni sul modo in cui il Servizio app di Azure semplifica lo sviluppo e l'hosting di applicazioni Web"
	services="app-service\web"
	documentationCenter=""
	authors="jaime-espinosa"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="05/25/2016"
	ms.author="tdykstra"/>

# Panoramica di App Web

*App Web del servizio app* è una piattaforma di calcolo completamente gestita, ottimizzata per l'hosting di siti Web e applicazioni Web. Questa offerta [PaaS (Platform As a Service)](https://en.wikipedia.org/wiki/Platform_as_a_service) di Microsoft Azure consente di concentrarsi sulla logica di business, mentre Azure gestisce l'infrastruttura per l'esecuzione e il ridimensionamento delle app.

Per una panoramica di cinque minuti, guardare il video relativo ad [App Web del servizio app di Azure con Yochay Kiriaty](https://azure.microsoft.com/documentation/videos/azure-app-service-web-apps-with-yochay-kiriaty/).

## Informazioni sull'app Web in un servizio app

Un'*app Web* del servizio app è una risorsa di calcolo offerta da Azure per l'hosting di un sito Web o di un'applicazione Web.

Le risorse di calcolo possono trovarsi in macchine virtuali condivise o dedicate, in base al piano tariffario scelto. Il codice dell'applicazione viene eseguito in una VM gestita, isolata da altri clienti.

È possibile usare per il codice qualsiasi linguaggio o framework supportato dal [Servizio app di Azure](../app-service/app-service-value-prop-what-is.md), ad esempio ASP.NET, Node.js, Java, PHP o Python. È anche possibile eseguire script che usano [PowerShell e altri linguaggi di scripting](web-sites-create-web-jobs.md#acceptablefiles) in un'app Web.

Per esempi di scenari di applicazione tipici per cui è possibile usare le app Web, vedere [Scenari con app Web](https://azure.microsoft.com/documentation/scenarios/web-app/).

## Vantaggi dell'uso di App Web

Ecco alcune delle funzionalità principali del servizio app applicabili ad App Web:

- **Piattaforma completamente gestita**: applicazione automatica di patch del sistema operativo e del framework, supporto predefinito per il backup e il ripristino di emergenza. 

- **Possibilità di usare le competenze esistenti**: per scrivere codice con il linguaggio, il framework e l'ambiente di sviluppo preferiti. Il servizio app supporta .NET, Node.js, Java, PHP e Python.

- **Distribuzione rapida**: per effettuare il provisioning di nuove app Web e distribuirvi il codice in pochi secondi.

- **Integrazione continua**: è possibile configurare [l'integrazione e la distribuzione continua](../app-service-web/app-service-continous-deployment.md) con Visual Studio Team Services, GitHub o BitBucket.

- **Ambienti di staging e di test**: è possibile implementare una [pre-distribuzione](../app-service-web/web-sites-staged-publishing.md) per verificare il codice in un ambiente di pre-produzione identico all'ambiente di produzione. Quando si è pronti, si può rilasciare una nuova versione dell'app con tempo di inattività pari a zero eseguendo un'operazione di scambio.

- **Test in produzione**: è possibile ottimizzare la pre-distribuzione ed [eseguire il test A/B](../app-service-web/app-service-web-test-in-production-get-start.md) per verificare il nuovo codice con una frazione configurabile del traffico live.

- **Autenticazione e autorizzazione**: si può proteggere un'app dall'accesso non autenticato senza apportare modifiche al codice. I servizi di autenticazione predefiniti proteggono le app dall'accesso da parte di utenti, client che rappresentano utenti o servizi. I provider di identità supportati includono Azure Active Directory, Facebook, Twitter, Google e account Microsoft. Per altre informazioni, vedere [Autenticazione e autorizzazione nel servizio app di Azure](../app-service/app-service-authentication-overview.md).

- **Possibilità di connettersi a qualsiasi servizio**: per la connessione dell'app a sistemi aziendali o piattaforme software come un servizio (SaaS) in pochi minuti, grazie ai [connettori](../connectors/apis-list.md) predefiniti. È possibile scegliere tra oltre 50 connettori per sistemi aziendali quali SAP, Siebel e Oracle, servizi aziendali SaaS più diffusi, quali Salesforce e Office 365 e i servizi Internet più comuni, come Facebook, Twitter e Dropbox.

- **Scalabilità globale**: per [aumentare le prestazioni](../app-service/app-service-scale.md) o [aumentare le istanze](../azure-portal/insights-how-to-scale.md) in modo da gestire qualsiasi carico di lavoro in ingresso dei clienti. Selezionare manualmente il numero e le dimensioni delle macchine virtuali o configurare la scalabilità automatica in base al carico o alla pianificazione. L'infrastruttura globale dei data center Microsoft ospita le app e facilita la replica dei dati e i servizi di hosting in più località.

- **Livello aziendale**: il servizio app è progettato per compilare e ospitare applicazioni cruciali sicure. Si possono creare app aziendali integrate di Active Directory che si connettono in modo sicuro alle risorse locali e ospitarle quindi in una piattaforma cloud sicura [conforme agli standard ISO, SOC e PCI](https://www.microsoft.com/TrustCenter/). Per tutto questo è disponibile un [Contratto di servizio](https://azure.microsoft.com/support/legal/sla/app-service/) di livello aziendale.

- **Azure Marketplace**: è possibile scegliere da un [elenco di modelli di applicazioni](https://azure.microsoft.com/marketplace/) in continua crescita. Si possono sfruttare le migliori offerte della community di app OSS per installare pacchetti come WordPress, Joomla e Drupal con un solo clic.

- **Processi Web**: consentono di [eseguire qualsiasi programma o script](../app-service-web/web-sites-create-web-jobs.md) nelle macchine virtuali del servizio app. I processi possono essere eseguiti in modo continuo o in base a una pianificazione oppure possono essere attivati da eventi. Azure [WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk.md) semplifica il codice scritto per l'integrazione con altri servizi di Azure e di terze parti.

- **Connessioni ibride**: consentono di accedere ai dati locali tramite [connessioni ibride](../biztalk-services/integration-hybrid-connection-overview.md) e [reti virtuali di Azure](../app-service-web/web-sites-integrate-with-vnet.md).

- **Integrazione di Visual Studio**: gli strumenti dedicati in Visual Studio semplificano l'attività di creazione, distribuzione, utilizzo, debug e gestione di app Web, app per dispositivi mobili e app per le API.

Un'app Web può anche sfruttare le funzionalità offerte dalle [app per le API](../app-service-api/app-service-api-apps-why-best-platform.md), ad esempio il supporto di CORS, e dalle [app per dispositivi mobili](../app-service-mobile/app-service-mobile-value-prop.md), ad esempio le notifiche push. L'unica differenza tra i tre tipi di app, ovvero per le API, Web e per dispositivi mobili, è costituita dal nome e dall'icona con cui sono contraddistinte nel portale di Azure. Per altre informazioni sui tipi di app nel servizio app, vedere [Informazioni sul servizio app di Azure](../app-service/app-service-value-prop-what-is.md).

Oltre ad App Web nel servizio app, Azure offre altri servizi che possono essere usati per l'hosting di siti e applicazioni Web. Per la maggior parte degli scenari App Web è la scelta migliore. Per un'architettura di microservizi, prendere in considerazione [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric). Se è necessario un maggiore controllo delle macchine virtuali in cui viene eseguito il codice, considerare l'uso di [Macchine virtuali di Azure](https://azure.microsoft.com/documentation/services/virtual-machines/). Per altre informazioni su come scegliere uno di questi servizi di Azure, vedere [Confronto tra Servizio app di Azure, Macchine virtuali, Service Fabric e Servizi cloud](choose-web-site-cloud-service-vm.md).

## Introduzione

Per iniziare a distribuire codice di esempio in una nuova app Web nel servizio app, seguire l'esercitazione [Distribuire la prima app Web in Azure in 5 minuti](app-service-web-get-started.md). Sarà necessario un account Azure gratuito.

Per iniziare a usare il servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

<!---HONumber=AcomDC_0601_2016-->