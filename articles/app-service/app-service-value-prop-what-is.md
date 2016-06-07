<properties 
	pageTitle="Che cos'è il servizio app di Azure | Microsoft Azure" 
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

Il *servizio app* è un'offerta di [piattaforma distribuita come servizio](https://en.wikipedia.org/wiki/Platform_as_a_service) (PaaS) di Microsoft Azure che consente di creare app Web e per dispositivi mobili per qualsiasi piattaforma o dispositivo. È davvero facile facilmente le app con soluzioni SaaS (ad esempio, Office 365, Dynamics CRM, Salesforce, Twilio), connettersi con applicazioni locali (ad esempio, SAP, Oracle, Siebel) e automatizzare i processi aziendali soddisfacendo al tempo stesso severe esigenze di sicurezza, affidabilità e scalabilità.

Il servizio app include le funzionalità Web e per dispositivi mobili prima fornite separatamente come Siti Web di Azure e Servizi mobili di Azure. Include anche nuove funzionalità per l'automazione dei processi aziendali e l'hosting di API cloud.

## Tipi di app nel servizio app

Il servizio app offre i tipi di app seguenti per eseguire il codice dell'applicazione o i processi del flusso di lavoro.

- [**App Web**](../app-service-web/app-service-web-overview.md): per l'hosting di siti Web e applicazioni Web.

- [**App per dispositivi mobili**](../app-service-mobile/app-service-mobile-value-prop.md): per l'hosting di back-end di app per dispositivi mobili.
   
- [**App per le API**](../app-service-api/app-service-api-apps-why-best-platform.md): per l'hosting di API cloud.
 
- [**App per la logica**](../app-service-logic/app-service-logic-what-are-logic-apps.md): per l'automazione dell'accesso e dell'uso dei dati nei cloud senza necessità di scrivere codice.

Come singolo servizio integrato, il servizio app consente di costituire facilmente un'unica soluzione con più tipi di app.

## Piani e ambienti di servizio app

I [piani di servizio app](azure-web-sites-web-hosting-plans-in-depth-overview.md) rappresentano le risorse di calcolo in cui vengono eseguite le app. Con piani tariffari più bassi, le app vengono eseguite in macchine virtuali condivise. Con piani più alti, le app vengono eseguite in VM dedicate. È possibile scegliere tra diverse dimensioni di VM e cambiare il piano tariffario senza tempi di inattività. Se sono necessari maggiore scalabilità e isolamento rete, è possibile eseguire le app in un [ambiente di servizio app](../app-service-web/app-service-app-service-environment-intro.md).

## Perché usare il servizio app?

Ecco alcune caratteristiche e funzionalità chiave del servizio app:

- **Piattaforma completamente gestita**: applicazione automatica di patch del sistema operativo e del framework, supporto predefinito per il backup e il ripristino di emergenza. 

- **Possibilità di usare le competenze esistenti**: per scrivere codice con il linguaggio, il framework e l'ambiente di sviluppo preferiti. Il servizio app supporta .NET, Node.js, Java, PHP e Python.

- **Distribuzione rapida**: per effettuare il provisioning di nuove app Web e distribuirvi il codice in pochi secondi.

- **Integrazione continua**: è possibile configurare [l'integrazione e la distribuzione continua](../app-service-web/app-service-continous-deployment.md) con Visual Studio Team Services, GitHub o BitBucket.

- **Ambienti di staging e di test**: è possibile implementare una [pre-distribuzione](../app-service-web/web-sites-staged-publishing.md) per verificare il codice in un ambiente di pre-produzione identico all'ambiente di produzione. Quando si è pronti, si può rilasciare una nuova versione dell'app con tempo di inattività pari a zero eseguendo un'operazione di scambio.

- **Test in produzione**: è possibile ottimizzare la pre-distribuzione ed [eseguire il test A/B](../app-service-web/app-service-web-test-in-production-get-start.md) per verificare il nuovo codice con una frazione configurabile del traffico live.

- **Autenticazione e autorizzazione**: si può proteggere un'app dall'accesso non autenticato senza apportare modifiche al codice. I servizi di autenticazione predefiniti proteggono le app dall'accesso da parte di utenti, client che rappresentano utenti o servizi. I provider di identità supportati includono Azure Active Directory, Facebook, Twitter, Google e account Microsoft. Per altre informazioni, vedere [Autenticazione e autorizzazione nel servizio app di Azure](app-service-authentication-overview.md).

- **Possibilità di connettersi a qualsiasi servizio**: per la connessione dell'app a sistemi aziendali o piattaforme software come un servizio (SaaS) in pochi minuti, grazie ai [connettori](../connectors/apis-list.md) predefiniti. È possibile scegliere tra oltre 50 connettori per sistemi aziendali quali SAP, Siebel e Oracle, servizi aziendali SaaS più diffusi, quali Salesforce e Office 365 e i servizi Internet più comuni, come Facebook, Twitter e Dropbox.

- **Scalabilità globale**: per [aumentare le prestazioni](../app-service/app-service-scale.md) o [aumentare le istanze](../azure-portal/insights-how-to-scale.md) in modo da gestire qualsiasi carico di lavoro in ingresso dei clienti. Selezionare manualmente il numero e le dimensioni delle macchine virtuali o configurare la scalabilità automatica in base al carico o alla pianificazione. L'infrastruttura globale dei data center Microsoft ospita le app e facilita la replica dei dati e i servizi di hosting in più località.

- **Livello aziendale**: il servizio app è progettato per compilare e ospitare applicazioni cruciali sicure. Si possono creare app aziendali integrate di Active Directory che si connettono in modo sicuro alle risorse locali e ospitarle quindi in una piattaforma cloud sicura [conforme agli standard ISO, SOC e PCI](https://www.microsoft.com/TrustCenter/). Per tutto questo è disponibile un [Contratto di servizio](https://azure.microsoft.com/support/legal/sla/app-service/) di livello aziendale.

- **Azure Marketplace**: è possibile scegliere da un [elenco di modelli di applicazioni](https://azure.microsoft.com/marketplace/) in continua crescita. Si possono sfruttare le migliori offerte della community di app OSS per installare pacchetti come WordPress, Joomla e Drupal con un solo clic.

- **Processi Web**: consentono di [eseguire qualsiasi programma o script](../app-service-web/web-sites-create-web-jobs.md) nelle macchine virtuali del servizio app. I processi possono essere eseguiti in modo continuo o in base a una pianificazione oppure possono essere attivati da eventi. Azure [WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk.md) semplifica il codice scritto per l'integrazione con altri servizi di Azure e di terze parti.

- **Connessioni ibride**: consentono di accedere ai dati locali tramite [connessioni ibride](../biztalk-services/integration-hybrid-connection-overview.md) e [reti virtuali di Azure](../app-service-web/web-sites-integrate-with-vnet.md).

- **Integrazione di Visual Studio**: gli strumenti dedicati in Visual Studio semplificano l'attività di creazione, distribuzione, utilizzo, debug e gestione di app Web, app per dispositivi mobili e app per le API.

## Introduzione al servizio app

[Creare un'app Web, un'app per dispositivi mobili o un'app per la logica temporanea](http://go.microsoft.com/fwlink/?LinkId=523751) immediatamente e in modo totalmente gratuito, senza carta di credito, senza impegno, senza problemi.

In alternativa, aprire un [account Azure gratuito](https://azure.microsoft.com/pricing/free-trial/) e provare una delle esercitazioni introduttive:

* [App Web](https://azure.microsoft.com/documentation/services/app-service/web/)
* [App per dispositivi mobili](https://azure.microsoft.com/documentation/services/app-service/mobile/)
* [App per le API](https://azure.microsoft.com/documentation/services/app-service/api/)
* [App per la logica](https://azure.microsoft.com/documentation/services/app-service/logic/)

<!---HONumber=AcomDC_0601_2016-->