<properties 
	pageTitle="Panoramica delle app per le API" 
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

# Panoramica delle app per le API

Le app per le API sono uno dei quattro tipi di app offerti da [Servizio app di Azure](../app-service/app-service-value-prop-what-is.md).

![](./media/app-service-api-apps-why-best-platform/appservicesuite.png)

Le app per le API forniscono una piattaforma avanzata per la creazione, l'hosting e l'utilizzo di API nel cloud e in locale. Distribuire la propria API come un'app per le API in Servizio app e trarre vantaggio dalla sicurezza di livello aziendale, dal controllo di accesso semplificato, dalla connettività ibrida, dalla generazione automatica di SDK e dalla perfetta integrazione con le [app per la logica](../app-service-logic/app-service-logic-what-are-logic-apps.md).

## Vantaggi delle app per le API

Le app per le API offrono le seguenti funzionalità attualmente disponibili in anteprima pubblica:

- **Facilità di utilizzo**: grazie al supporto integrato per [Swagger](#concepts) le API sono più facilmente utilizzabili da client diversi. Configurano facilmente [CORS](#concepts) e generano automaticamente codice client per le API in diversi linguaggi, inclusi C#, Java e Javascript.

- **Controllo di accesso semplificato**: è possibile proteggere un'app per le API da accessi non autenticati senza apportare modifiche al codice. I servizi di autenticazione integrati proteggono le API dall'accesso da altri servizi o da client che rappresentano gli utenti. I provider di identità supportati includono Azure Active Directory e i provider di terze parti, ad esempio Facebook e Twitter. I client possono usare Active Directory Authentication Library (ADAL) o Mobile Apps SDK. Per altre informazioni, vedere [Autenticazione per app per le API e per dispositivi mobili nel servizio app di Azure](../app-service/app-service-authentication-overview.md).

- **Integrazione con Visual Studio**: gli strumenti dedicati in Visual Studio semplificano il processo di creazione, distribuzione, utilizzo, debug e gestione delle app per le API.

- **Integrazione con app per la logica**: le app per le API create possono essere utilizzate dalle [app per la logica del servizio app](../app-service-logic/app-service-logic-what-are-logic-apps.md).

- **Uso dell'API esistente**: è possibile usare le API esistenti così come sono, senza modificarne il codice. Per sfruttare le funzionalità delle app per le API, è sufficiente distribuire il codice in un'app per le API. L'API può usare qualsiasi linguaggio o framework supportato dal servizio app, inclusi ASP.NET, C#, Java, PHP, Node. js e Python.

Queste sono solo alcune delle funzionalità disponibili. Per altre funzionalità di cui le app per le API possono usufruire, vedere la [panoramica delle app Web](../app-service-web/app-service-web-overview.md).

>[AZURE.NOTE]È possibile usare [Gestione API di Azure](../api-management/api-management-key-concepts.md) per controllare l'accesso client alle API ospitate dalle app per le API del servizio app. Anche se le app per le API forniscono servizi di autenticazione, non offrono altre funzionalità di gestione degli accessi offerte da Gestione API, ad esempio consolidamento di endpoint e limitazione delle richieste.
>
>Le app per le API sono attualmente disponibili in anteprima pubblica. Le [app Web del servizio app](../app-service-web/app-service-web-overview.md) sono un servizio disponibile a livello generale progettato per lo sviluppo e l'hosting di applicazioni mission-critical sicure su scala globale. Le app Web sono un'opzione ottimale se si cerca un servizio disponibile a livello generale per lo sviluppo di API. Quando le app per le API saranno disponibili a livello generale, verrà fornito un percorso per permettere di sfruttare le funzionalità aggiuntive delle app per le API nelle app Web esistenti.

## Concetti delle app per le API ##

- **Swagger**: framework per la documentazione e per l'individuazione di un'API RESTful, usato per impostazione predefinita nelle app per le API. Per altre informazioni, visitare il sito [http://swagger.io/](http://swagger.io/).
- **Condivisione risorse tra le origini (CORS)**: un meccanismo che consente a JavaScript in esecuzione in un browser di effettuare chiamate a un'API ospitata in un dominio diverso da quello da cui è stata caricata la pagina Web.
- **Trigger**: API REST che può essere chiamata dalle [app per la logica](../app-service-logic/app-service-logic-what-are-logic-apps.md) per avviare un processo di flusso di lavoro quando viene soddisfatta una determinata condizione. Ad esempio, un'app per le API può fornire un metodo che viene chiamato periodicamente dall'app per la logica per cercare una determinata frase in un feed di Twitter. Per altre informazioni, vedere la pagina relativa ai [trigger delle app per le API](app-service-api-dotnet-triggers.md).
- **Azione**: API REST che può essere chiamata dalle [app per la logica](../app-service-logic/app-service-logic-what-are-logic-apps.md) per elaborare i dati dopo l'avvio di un flusso di lavoro da parte di un trigger. Ad esempio, un'app per le API può fornire un metodo che viene chiamato dall'app per la logica per rispondere a un tweet rilevato dal trigger di Twitter. Le azioni sono metodi API esposti da una definizione dell'API Swagger.
- **Gateway**: un'app Web che gestisce le funzioni di amministrazione delle API e l'autenticazione per tutte le app per le API in un gruppo di risorse.

## Introduzione

Per iniziare a usare le app per le API, seguire l'esercitazione [Creare un'app per le API](app-service-dotnet-create-api-app.md).

Per visualizzare un elenco di problemi noti relativi alle app per le API, vedere [il post del forum sui problemi noti delle app per le API](https://social.msdn.microsoft.com/Forums/it-IT/7f8b42f2-ac0d-48b8-a35e-3b4934e1c25e/api-app-known-issues?forum=AzureAPIApps).

Per altre informazioni sulla piattaforma del servizio app di Azure, vedere [Servizio app di Azure](../app-service/app-service-value-prop-what-is.md).

 

<!---HONumber=Nov15_HO2-->