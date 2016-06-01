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
	ms.date="05/16/2016"
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

Ecco alcune funzionalità principali di App Web:

- **Familiarità e rapidità**: è possibile sfruttare le proprie conoscenze esistenti e scrivere codice nel linguaggio, framework e IDE (Integrated Development Environment) di propria scelta. Per il provisioning di nuove app Web e la distribuzione di codice nelle app Web sono necessari pochi secondi.

- **Applicazioni aziendali**: il servizio app Web è stato ideato per creare e ospitare applicazioni di importanza strategica in tutta sicurezza. È possibile creare app aziendali integrate con Active Directory che si connettono in modo sicuro a risorse locali, quindi ospitarle in una piattaforma cloud sicura conforme agli standard [ISO](https://www.microsoft.com/TrustCenter/Compliance/ISO-IEC-27001), [SOC](https://www.microsoft.com/TrustCenter/Compliance/SOC) e [PCI](https://www.microsoft.com/TrustCenter/Compliance/pci). Per tutte queste app è disponibile un [Contratto di servizio](https://azure.microsoft.com/support/legal/sla/app-service/) di livello aziendale.

- **Scala globale**: il servizio app Web consente di [aumentare le prestazioni](../app-service/app-service-scale.md) o [aumentare il numero di istanze](../azure-portal/insights-how-to-scale.md) in modo rapido, per gestire qualsiasi carico di lavoro proveniente dalla clientela. Selezionare manualmente il numero e le dimensioni delle macchine virtuali o configurare la scalabilità automatica in base al carico o alla pianificazione. L'infrastruttura globale di data center Microsoft esegue l'hosting delle app Web e semplifica la replica di dati e di servizi di hosting in più posizioni.

- **Azure Marketplace**: è possibile scegliere da un [elenco in continua crescita di modelli di applicazione Web](https://azure.microsoft.com/marketplace/). È possibile sfruttare le migliori offerte della community di app OSS per installare in un singolo clic pacchetti come Wordpress, Joomla e Drupal.

- **Integrazione continua**: è possibile configurare flussi di lavoro di [integrazione e distribuzione continua](app-service-continous-deployment.md) con Visual Studio Team Services, GitHub o BitBucket. È possibile compilare, eseguire il test e distribuire automaticamente l'app Web a ogni archiviazione corretta del codice o a ogni test di integrazione.

- **Ambienti di staging e di test**: è possibile implementare una [pre-distribuzione](web-sites-staged-publishing.md) per verificare il codice in un ambiente di pre-produzione identico all'ambiente di produzione. Quando si è pronti, si può rilasciare una nuova versione dell'app con tempo di inattività pari a zero eseguendo un'operazione di scambio.

- **Test in produzione**: è possibile portare la pre-distribuzione al livello successivo ed [eseguire il test A/B](app-service-web-test-in-production-get-start.md) per verificare il nuovo codice con una frazione configurabile del proprio traffico live.

- **Processi Web**: è possibile [eseguire qualsiasi programma o script](web-sites-create-web-jobs.md) nelle VM di App Web. I processi possono essere eseguiti in modo continuo o in base a una pianificazione oppure possono essere attivati da eventi. Azure [WebJobs SDK](websites-dotnet-webjobs-sdk-get-started.md) semplifica il codice scritto per l'integrazione con altri servizi di Azure, ad esempio le code di archiviazione, i BLOB, le tabelle e le code e gli argomenti del bus di servizio.

- **Connessioni ibride**: è possibile accedere ai dati locali tramite [connessioni ibride](../biztalk-services/integration-hybrid-connection-overview.md) e [VNET](../app-service-web/web-sites-integrate-with-vnet.md).

- **Integrazione con Visual Studio**: gli strumenti dedicati in Visual Studio semplificano il processo di creazione, distribuzione, utilizzo, debug e gestione delle app Web. Per altre informazioni, vedere [Annuncio di Azure SDK 2.8.1 per Visual Studio](https://azure.microsoft.com/blog/announcing-azure-sdk-2-8-1-for-net/).

Un'app Web può anche sfruttare le funzionalità offerte dalle [app per le API](../app-service-api/app-service-api-apps-why-best-platform.md), ad esempio il supporto CORS, e dalle [app per dispositivi mobili](../app-service-mobile/app-service-mobile-value-prop.md), ad esempio le notifiche push. Analogamente, è possibile usare un'app per le API o un'app per dispositivi mobili per ospitare un'app Web e sfruttare le funzionalità delle app Web, ad esempio la scalabilità automatica e la pre-distribuzione. L'unica differenza tra i tre tipi di app, ovvero per le API, Web e per dispositivi mobili, è costituita dal nome e dall'icona con cui sono contraddistinte nel portale di Azure. Per altre informazioni sui tipi di app nel servizio app, vedere [Panoramica del servizio app di Azure](../app-service/app-service-value-prop-what-is.md).

## Introduzione

Per iniziare a distribuire codice di esempio in una nuova app Web nel servizio app, seguire l'esercitazione [Distribuire la prima app Web in Azure in 5 minuti](app-service-web-get-started.md). Sarà necessario un account Azure gratuito.

Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

<!---HONumber=AcomDC_0525_2016-->