<properties 
	pageTitle="Informazioni sulle app per le API" 
	description="Informazioni sui motivi per i quali il servizio app di Azure è la piattaforma migliore per lo sviluppo, la pubblicazione e l'hosting di API RESTful." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/05/2015" 
	ms.author="tdykstra"/>

# Informazioni sulle app per le API

Le app per le API fanno parte della famiglia di prodotti del [servizio app di Azure](app-service-value-prop-what-is.md), che include anche app Web, app per dispositivi mobili e app per la logica.

![](./media/app-service-api-apps-why-best-platform/appservicesuite.png)

La parte relativa alle app per le API di questa famiglia di prodotti fornisce una piattaforma avanzata e un ecosistema per lo sviluppo, l'utilizzo e la distribuzione di API nel cloud e in locale.

>[AZURE.NOTE]Le app per le API sono attualmente disponibili in anteprima pubblica. Sono basate sulle [app Web del servizio app](app-service-web-overview.md), ovvero un servizio disponibile a livello generale progettato per lo sviluppo e l'hosting di applicazioni mission-critical sicure su scala globale. Le app Web sono un'opzione ottimale se si cerca un servizio disponibile a livello generale per lo sviluppo di API. Quando le app per le API saranno disponibili a livello generale, verrà fornito un percorso per permettere di sfruttare le funzionalità delle app per le API nelle app Web esistenti.

## Vantaggi delle app per le API

Un'app per le API e un'[app Web del servizio app](app-service-web-overview.md) con funzionalità aggiuntive che migliorano l'esperienza di sviluppo, distribuzione, pubblicazione, utilizzo, gestione e monetizzazione di API Web RESTful.

Le app per le API condividono quindi con le app Web tutte le funzionalità di hosting della piattaforma del servizio app di Azure:

- Applicazione automatica di patch al sistema operativo
- Sicurezza di livello aziendale
- Disponibilità elevata
- Scalabilità automatica e bilanciamento del carico
- [Processi Web](websites-webjobs-resources.md) per l'elaborazione in background
- Sviluppo semplice e rapido e molte opzioni per la distribuzione continuativa. Per altre informazioni sulla gamma di opzioni di distribuzione disponibili per le app per le API, vedere [Distribuzione di un'app nel servizio app di Azure](web-sites-deploy.md). 

Le funzionalità aggiuntive offerte dalla piattaforma delle app per le API semplificano lo sviluppo, l'hosting e l'utilizzo delle API:

- **Uso dell'API così com'è**: usare ASP.NET, Java, PHP, Node.js o Python per le API. Le API possono sfruttare i vantaggi della piattaforma delle app per le API, senza modifiche.

- **Semplice controllo di accesso**: è possibile configurare le API per l'autenticazione usando Azure Active Directory o servizi di terze parti quali Facebook e Twitter, senza modificare il codice. Usare una sintassi semplificata per il codice di autorizzazione. Per altre informazioni, vedere [Proteggere un'app per le API](app-service-api-dotnet-add-authentication.md).

- **Connessione facile alle piattaforme SaaS**: le [app per le API per i connettori](app-service-logic-what-are-biztalk-api-apps.md) in Azure Marketplace vengono fornite da Microsoft e da terze parti per semplificare il codice scritto per l'interazione con SalesForce, Office 365, Twitter, Facebook, Dropbox e molti altri.

- **Accesso ai dati in locale**: le app per le API possono esporre API che utilizzano i dati del proprio data center tramite [connessioni ibride](integration-hybrid-connection-overview.md) e [rete virtuale](web-sites-integrate-with-vnet.md).

- **Facilità di utilizzo**: è possibile usare il supporto integrato per [Swagger](http://swagger.io/) per rendere le API facilmente utilizzabili da diversi client. In alternativa, è possibile usare l'API Apps SDK per generare codice client per le API in diversi linguaggi, inclusi C#, Java e Javascript.

- **Integrazione con app per la logica**: le app per le API create possono essere utilizzate dalle app per la logica del servizio app.

- **Integrazione con Visual Studio**: gli strumenti dedicati in Visual Studio semplificano il processo di [creazione](app-service-dotnet-create-api-app.md), [distribuzione](app-service-dotnet-deploy-api-app.md), [debug](app-service-dotnet-remotely-debug-api-app) e gestione delle app per le API.

Prossimamente la piattaforma delle app per le API permetterà anche di creare un ecosistema avanzato di API, semplificando la condivisione del codice:

- **Marketplace pubblici e privati**: [Azure Marketplace](http://azure.microsoft.com/marketplace/) semplificherà l'individuazione e la distribuzione nella sottoscrizione di Azure delle app per le API incluse nel pacchetto e sviluppate da Microsoft e da terze parti. Sarà anche possibile includere nel pacchetto e pubblicare app per le API personalizzate sviluppate dall'utente, in modo che gli altri sviluppatori possano distribuirle nelle proprie sottoscrizioni di Azure. Quando si pubblicano le API in Azure Marketplace, sarà possibile renderle visibili solo agli altri membri dell'organizzazione. 

- **Distribuzione automatica delle dipendenze**: quando si distribuisce un'app per le API dal Marketplace nella sottoscrizione di Azure, le app per le API dipendenti verranno distribuite automaticamente da Azure e verranno create le risorse necessarie. Un pacchetto di app per le API specificherà le app per le API da cui dipende e le risorse di Azure necessarie.

- **Aggiornamenti automatici**: quando si aggiorna il codice per uno dei pacchetti di app per le API condivisi, sarà possibile rendere disponibile l'aggiornamento a tutti gli utenti che hanno installato e stanno eseguendo l'app per le API. Questa procedura è consentita per modifiche che non causano interruzioni e per gli utenti che hanno scelto esplicitamente di ricevere aggiornamenti.

Molte di queste funzionalità, ad esempio il Marketplace pubblico e gli aggiornamenti automatici, sono già disponibili per le app per le API fornite da Microsoft.

## Concetti delle app per le API ##

- **Gateway**: un'app Web che gestisce le funzioni di amministrazione delle API e l'autenticazione per tutte le app per le API in un gruppo di risorse. 
- **Swagger**: framework per la documentazione interattiva e per l'individuazione di un'API RESTful, usato per impostazione predefinita nelle app per le API. Per altre informazioni, visitare il sito [http://swagger.io/](http://swagger.io/).
- **Connettore**: un tipo di app per le API che consente di connettersi facilmente a piattaforme SaaS come Salesforce e Office 365. Per altre informazioni, vedere [Informazioni sui connettori e sulle app per le API BizTalk](app-service-logic-what-are-biztalk-api-apps.md).
- **Trigger**: API REST che può essere chiamata dalle [app per la logica](app-service-logic-what-are-logic-apps.md) per avviare un processo di flusso di lavoro quando viene soddisfatta una determinata condizione. Ad esempio, un'app per le API può fornire un metodo che viene chiamato periodicamente dall'app per la logica per cercare una determinata frase in un feed di Twitter. Per altre informazioni, vedere la pagina relativa ai [trigger delle app per le API](app-service-api-dotnet-triggers.md).
- **Azione**: API REST che può essere chiamata dalle [app per la logica](app-service-logic-what-are-logic-apps.md) per elaborare i dati dopo l'avvio di un flusso di lavoro da parte di un trigger. Ad esempio, un'app per le API può fornire un metodo che viene chiamato dall'app per la logica per rispondere a un tweet rilevato dal trigger di Twitter. Le azioni sono metodi API esposti da una definizione dell'API Swagger.

## Introduzione

Per iniziare a usare le app per le API, seguire l'esercitazione [Creare un'app per le API](app-service-dotnet-create-api-app.md).

Per visualizzare un elenco di problemi noti relativi alle app per le API, vedere [questo post del forum MSDN](https://social.msdn.microsoft.com/Forums/en-US/7f8b42f2-ac0d-48b8-a35e-3b4934e1c25e/api-app-known-issues?forum=AzureAPIApps).

Per altre informazioni sulla piattaforma del servizio app di Azure, vedere [Servizio app di Azure](app-service-value-prop-what-is.md).


<!--HONumber=54-->