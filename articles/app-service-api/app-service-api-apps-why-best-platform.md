<properties 
	pageTitle="Panoramica delle app per le API" 
	description="Informazioni sui motivi per i quali il servizio app di Azure è la piattaforma migliore per lo sviluppo, la pubblicazione e l'hosting di API RESTful." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/08/2016" 
	ms.author="tdykstra"/>

# Panoramica delle app per le API

Le app per le API sono uno dei quattro tipi di app offerti da [Servizio app di Azure](../app-service/app-service-value-prop-what-is.md).

![](./media/app-service-api-apps-why-best-platform/appservicesuite.png)

Il [servizio app](../app-service/app-service-value-prop-what-is.md) è una piattaforma completamente gestita che offre un set completo di funzionalità per scenari Web, mobili e di integrazione. Le app per le API nel servizio App offrono delle funzionalità che rendono più semplice creare, ospitare e utilizzare le API nel cloud e in locale. Distribuire la propria API come un'app per le API in Servizio app e trarre vantaggio dalla sicurezza di livello aziendale, dal controllo di accesso semplificato, dalla connettività ibrida, dalla generazione automatica di SDK e dalla perfetta integrazione con le [app per la logica](../app-service-logic/app-service-logic-what-are-logic-apps.md).

## Vantaggi delle app per le API

Le app per le API offrono le funzionalità seguenti:

- **Facilità di utilizzo**: grazie al supporto integrato per i [metadati dell’API Swagger](#concepts) le API sono più facilmente utilizzabili da client diversi. Viene generato automaticamente il codice client per le API in diversi linguaggi, inclusi C#, Java e Javascript. Configurare facilmente [CORS](#concepts) senza modificare il codice. Per altre informazioni, vedere [Metadati dell’App per le API del servizio app per l’individuazione dell’API e la generazione di codice](app-service-api-metadata.md) e [Consumare un'app API da JavaScript mediante CORS](app-service-api-cors-consume-javascript.md). 

- **Controllo di accesso semplificato**: protegge un'app per le API da accessi non autenticati senza apportare modifiche al codice. I servizi di autenticazione integrati proteggono le API dall'accesso da altri servizi o da client che rappresentano gli utenti. I provider di identità supportati includono Azure Active Directory e i provider di terze parti, ad esempio Facebook e Twitter. I client possono usare Active Directory Authentication Library (ADAL) o Mobile Apps SDK. Per altre informazioni, vedere [Espansione dell’autenticazione/autorizzazione del servizio App](/blog/announcing-app-service-authentication-authorization/) e [App per le API del servizio app: modifiche apportate](app-service-api-whats-changed.md).

- **Integrazione con Visual Studio**: gli strumenti dedicati in Visual Studio semplificano il processo di creazione, distribuzione, utilizzo, debug e gestione delle app per le API. Per altre informazioni, vedere [Annuncio di Azure SDK 2.8.1 per Visual Studio](/blog/announcing-azure-sdk-2-8-1-for-net/).

- **Integrazione con app per la logica**: le app per le API create possono essere utilizzate dalle [app per la logica del servizio app](../app-service-logic/app-service-logic-what-are-logic-apps.md). Per altre informazioni, vedere [Uso dell'API personalizzata ospitata nel servizio app con App per la logica](../app-service-logic/app-service-logic-custom-hosted-api.md). Per informazioni sulle modifiche in corso nelle modalità di integrazione di app per le API con app per la logica, vedere [App per le API del servizio app: modifiche apportate](app-service-api-whats-changed.md).

- **Uso dell'API esistente**: è possibile usare le API esistenti così come sono, senza modificarne il codice. Per sfruttare le funzionalità delle app per le API, è sufficiente distribuire il codice in un'app per le API. L'API può usare qualsiasi linguaggio o framework supportato dal servizio app, inclusi ASP.NET, C#, Java, PHP, Node. js e Python.

Inoltre, le funzionalità offerte da App per le API, App Web e app per dispositivi mobili sono intercambiabili. Questo significa che un'istanza di App per le API può sfruttare i vantaggi delle funzionalità per il web e per lo sviluppo di dispositivi mobili e l'hosting offerto dalle app Web e dalle app per dispositivi mobili. È anche vero il contrario: ad esempio, è possibile usare un'app web per ospitare un'API e sfruttare comunque i vantaggi dei metadati di Swagger per CORS e per la generazione del codice client per l'accesso a browser tra domini. Per altre informazioni, vedere [Panoramica di App Web](../app-service-web/app-service-web-overview.md) e [Informazioni sulle app per dispositivi mobili](../app-service-mobile/app-service-mobile-value-prop.md).

>[AZURE.NOTE]È possibile usare [Gestione API di Azure](../api-management/api-management-key-concepts.md) per controllare l'accesso client alle API ospitate dalle app per le API del servizio app. Anche se le app per le API forniscono servizi di autenticazione, non offrono altre funzionalità di gestione degli accessi offerte da Gestione API, ad esempio consolidamento di endpoint e limitazione delle richieste.

## Concetti delle app per le API <a id="concepts"></a>

- **Swagger**: un framework per la documentazione e per l'individuazione di un'API RESTful, usato per impostazione predefinita nelle app per le API. Per altre informazioni, visitare il sito [http://swagger.io/](http://swagger.io/).
- **Condivisione risorse tra le origini (CORS)**: un meccanismo che consente a JavaScript in esecuzione in un browser di effettuare chiamate a un'API ospitata in un dominio diverso da quello da cui è stata caricata la pagina Web. Per altre informazioni, vedere [Utilizzare un'app per le API da JavaScript tramite CORS](app-service-api-cors-consume-javascript.md). 
- **Trigger**: API REST che può essere chiamata dalle [app per la logica](../app-service-logic/app-service-logic-what-are-logic-apps.md) per avviare un processo di flusso di lavoro quando viene soddisfatta una determinata condizione. Ad esempio, un'app per le API può fornire un metodo che viene chiamato periodicamente dall'app per la logica per cercare una determinata frase in un feed di Twitter. Per altre informazioni, vedere la pagina relativa ai [trigger delle app per le API](app-service-api-dotnet-triggers.md).
- **Azione**: API REST che può essere chiamata dalle [app per la logica](../app-service-logic/app-service-logic-what-are-logic-apps.md) per elaborare i dati dopo l'avvio di un flusso di lavoro da parte di un trigger. Ad esempio, un'app per le API può fornire un metodo che viene chiamato dall'app per la logica per rispondere a un tweet rilevato dal trigger di Twitter. Le azioni sono metodi API esposti da una definizione dell'API Swagger.

## Introduzione

Per iniziare a usare le app per le API, seguire l'esercitazione [Introduzione alle app per le API](app-service-api-dotnet-get-started.md).

Per visualizzare un elenco di problemi noti relativi alle app per le API, vedere [il post del forum sui problemi noti delle app per le API](https://social.msdn.microsoft.com/Forums/it-IT/7f8b42f2-ac0d-48b8-a35e-3b4934e1c25e/api-app-known-issues?forum=AzureAPIApps).

Per altre informazioni sulla piattaforma del servizio app di Azure, vedere [Servizio app di Azure](../app-service/app-service-value-prop-what-is.md).

<!---HONumber=AcomDC_0114_2016-->