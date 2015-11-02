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
	ms.date="10/15/2015" 
	ms.author="tdykstra"/>

# Informazioni sulle app per le API

Le app per le API forniscono una piattaforma avanzata per la creazione, l’hosting, e l’utilizzo di API nel cloud e in locale. Distribuire le API come un’app per le API e trarre vantaggio dalla sicurezza di livello aziendale, dal controllo di accesso semplificato, dalla connettività ibrida e SaaS, dalla generazione automatica di SDK e dalla perfetta integrazione con le [app per la logica](../app-service-logic/app-service-logic-what-are-logic-apps.md).

Le app per le API fanno parte del [Azure App Service](../app-service/app-service-value-prop-what-is.md), che include anche app Web, app per dispositivi mobili e app per la logica.

![](./media/app-service-api-apps-why-best-platform/appservicesuite.png)

## Vantaggi delle app per le API

Le app per le API forniscono funzionalità per lo sviluppo, la distribuzione, la pubblicazione, l’utilizzo e la gestione di API web RESTful. Il servizio App offre le seguenti funzionalità attualmente disponibili in anteprima pubblica:

- **Facilità di utilizzo**: grazie al supporto integrato per [Swagger](http://swagger.io/) le API sono più facilmente utilizzabili da client diversi. È possibile usare l'API Apps SDK per generare codice client per le API in diversi linguaggi, inclusi C#, Java e Javascript.

- **Controllo di accesso semplificato**: i servizi di autenticazione incorporati supportano Azure Active Directory o servizi di terze parti quali Facebook e Twitter. È possibile proteggere un’app per le API da accessi non autenticati senza apportare modifiche al codice. Se si conoscono già i servizi di autenticazione forniti da [Servizi mobili di Azure](../mobile-services-windows-dotnet-how-to-use-client-library.md#authentication), le app per le API si basano su tale framework e lo estendono alle API ospitate. App Service SDK consente inoltre di usare una sintassi semplificata per il codice di autorizzazione. Per altre informazioni, vedere [Autenticazione per app per le API e per dispositivi mobili nel servizio app di Azure](../app-service/app-service-authentication-overview.md).

- **Connessione facile alle piattaforme SaaS**: le [app per le API per i connettori](../app-service-logic/app-service-logic-what-are-biztalk-api-apps.md) in Azure Marketplace vengono fornite da Microsoft e da terze parti per semplificare il codice scritto per l'interazione con SalesForce, Office 365, Twitter, Facebook, Dropbox e molti altri.

- **Integrazione con app per la logica**: le app per le API create possono essere utilizzate dalle [app per la logica del servizio app](../app-service-logic/app-service-logic-what-are-logic-apps.md).

- **Integrazione con Visual Studio**: gli strumenti dedicati in Visual Studio semplificano il processo di [creazione](app-service-dotnet-create-api-app.md), [distribuzione](app-service-dotnet-deploy-api-app.md), [debug](app-service-dotnet-remotely-debug-api-app) e gestione delle app per le API.

È possibile usare le API esistenti così come sono, senza modificarne il codice. Per sfruttare le funzionalità delle app per le API, è sufficiente distribuire il codice in un'app per le API. Per le API è possibile usare ASP.NET, Java, PHP, Node.js o Python.

Le app per le API includono inoltre le [funzionalità di app Web del servizio app](../app-service-web/app-service-web-overview.md).

>[AZURE.NOTE] [Azure API Management](/services/api-management/) è un servizio a parte che offre funzionalità come la limitazione e il consolidamento degli endpoint. È possibile usare Gestione API con le app per le API.
>
>Le app per le API sono attualmente disponibili in anteprima pubblica. Le [app Web del servizio app](../app-service-web/app-service-web-overview.md) sono un servizio disponibile a livello generale progettato per lo sviluppo e l'hosting di applicazioni mission-critical sicure su scala globale. Le app Web sono un'opzione ottimale se si cerca un servizio disponibile a livello generale per lo sviluppo di API. Quando le app per le API saranno disponibili a livello generale, verrà fornito un percorso per permettere di sfruttare le funzionalità aggiuntive delle app per le API nelle app Web esistenti.

## Concetti delle app per le API ##

- **Gateway**: un'app Web che gestisce le funzioni di amministrazione delle API e l'autenticazione per tutte le app per le API in un gruppo di risorse. 
- **Swagger**: framework per la documentazione interattiva e per l'individuazione di un'API RESTful, usato per impostazione predefinita nelle app per le API. Per altre informazioni, visitare il sito [http://swagger.io/](http://swagger.io/).
- **Connettore**: un tipo di app per le API che consente di connettersi facilmente a piattaforme SaaS come Salesforce e Office 365. Per altre informazioni, vedere [Informazioni sui connettori e sulle app per le API BizTalk](../app-service-logic/app-service-logic-what-are-biztalk-api-apps.md).
- **Trigger**: API REST che può essere chiamata dalle [app per la logica](../app-service-logic/app-service-logic-what-are-logic-apps.md) per avviare un processo di flusso di lavoro quando viene soddisfatta una determinata condizione. Ad esempio, un'app per le API può fornire un metodo che viene chiamato periodicamente dall'app per la logica per cercare una determinata frase in un feed di Twitter. Per altre informazioni, vedere la pagina relativa ai [trigger delle app per le API](app-service-api-dotnet-triggers.md).
- **Azione**: API REST che può essere chiamata dalle [app per la logica](../app-service-logic/app-service-logic-what-are-logic-apps.md) per elaborare i dati dopo l'avvio di un flusso di lavoro da parte di un trigger. Ad esempio, un'app per le API può fornire un metodo che viene chiamato dall'app per la logica per rispondere a un tweet rilevato dal trigger di Twitter. Le azioni sono metodi API esposti da una definizione dell'API Swagger.

## Introduzione

Per iniziare a usare le app per le API, seguire l'esercitazione [Creare un'app per le API](app-service-dotnet-create-api-app.md).

Per visualizzare un elenco di problemi noti relativi alle app per le API, vedere [questo post del forum MSDN](https://social.msdn.microsoft.com/Forums/it-IT/7f8b42f2-ac0d-48b8-a35e-3b4934e1c25e/api-app-known-issues?forum=AzureAPIApps).

Per altre informazioni sulla piattaforma del servizio app di Azure, vedere [Servizio app di Azure](../app-service/app-service-value-prop-what-is.md).

 

<!---HONumber=Oct15_HO4-->