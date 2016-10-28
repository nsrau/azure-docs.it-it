<properties
	pageTitle="Autenticazione e autorizzazione per app per le API nel servizio app di Azure | Microsoft Azure"
	description="Informazioni sui servizi di autenticazione e autorizzazione forniti dal servizio app di Azure per app per le API."
	services="app-service\api"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/23/2016"
	ms.author="rachelap"/>

# Autenticazione e autorizzazione per app per le API nel servizio app di Azure

## Panoramica 

> [AZURE.NOTE] Questo argomento verrà trasferito in un unico argomento [Autenticazione/autorizzazione del servizio app](../app-service/app-service-authentication-overview.md), che illustra l'autenticazione e l'autorizzazione per app Web, app per dispositivi mobili e app per le API.

Il servizio app di Azure offre servizi di autenticazione e autorizzazione incorporati che implementano [OAuth 2.0](#oauth) e [OpenID Connect](#oauth). Questo articolo descrive i servizi e le opzioni disponibili per le app per le API nel servizio app di Azure.

Il diagramma seguente illustra alcune caratteristiche chiave dell'autenticazione del servizio app:

* Pre-elabora le richieste API in ingresso, quindi usa qualsiasi linguaggio o framework supportato dal servizio app.
* Offre diverse opzioni per la quantità di autenticazione da eseguire nel proprio codice.
* Funziona per l'autenticazione sia dell'utente finale che dell'account del servizio.
* Supporta cinque provider di identità: Azure Active Directory, Facebook, Google, Twitter e account Microsoft.
* Presenta lo stesso funzionamento per le app per le API, le app Web e le app per dispositivi mobili.

![](./media/app-service-api-authentication/api-apps-overview.png)

## Indipendente dal linguaggio

L'elaborazione dell'autenticazione del servizio app viene eseguita prima che le richieste raggiungano l'app per le API, ciò significa che le funzionalità di autenticazione sono compatibili con app per le API scritte in qualsiasi linguaggio o framework. L'API può essere basata su ASP.NET, Java, Node.js o qualsiasi framework che supporta il servizio app.

Il servizio app passa il token Web JSON (JWT) nell'intestazione dell'autorizzazione di una richiesta HTTP e il codice scritto in qualsiasi linguaggio o framework può ottenere le informazioni necessarie dal token. Il servizio app permette anche di accedere più facilmente alle attestazioni più usate impostando alcune intestazioni speciali, ad esempio:

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID
* X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN
* X-MS-TOKEN-FACEBOOK-EXPIRES-ON
 
In un'API .NET è possibile usare l'attributo `Authorize` e per l'autorizzazione con granularità fine è possibile scrivere facilmente codice basato su attestazioni, perché le informazioni sulle attestazioni sono popolate automaticamente nelle classi .NET.

## Opzioni di protezione multiple

Il servizio app può impedire alle richieste HTTP anonime di raggiungere l'app per le API, può passare tutte le richieste e convalidare i token per le richieste che li includono oppure può accettare tutte le richieste senza eseguire alcuna azione su di esse:

1. Consentire solo alle richieste autenticate di raggiungere l'app per le API.

	Se una richiesta anonima viene ricevuta da un browser, il servizio app la reindirizzerà alla pagina di accesso per il provider di autenticazione scelto, ad esempio Azure Active Directory, Google, Twitter e così via.

	Con questa opzione non è necessario scrivere codice di autenticazione nell'app e il codice di autorizzazione risulta semplificato perché le attestazioni più importanti sono fornite nelle intestazioni HTTP.

2. Consentire a tutte le richieste di raggiungere l'app per le API, ma convalidare le richieste autenticate e passare le informazioni di autenticazione nelle intestazioni HTTP.

	Questa opzione offre maggiore flessibilità nella gestione delle richieste anonime, ma è necessario scrivere codice se si vuole impedire agli utenti anonimi di usare l'API. Poiché le attestazioni più diffuse vengono passate nelle intestazioni delle richieste HTTP, il codice di autorizzazione è relativamente semplice.
	
3. Consentire a tutte le richieste di raggiungere l'API e non eseguire alcuna operazione sulle informazioni di autenticazione nelle richieste.

	Questa opzione lascia le attività di autenticazione e autorizzazione interamente al codice dell'applicazione.

Le opzioni sono selezionabili nel pannello **Autenticazione/Autorizzazione** del [portale di Azure](https://portal.azure.com/).

![](./media/app-service-api-authentication/authblade.png)

Per le opzioni 1 e 2, attivare l'**autenticazione del servizio app** e scegliere **Accedi** o **Consenti richiesta (nessuna azione)** nell'elenco a discesa **Azione da eseguire quando la richiesta non è autenticata**. Se si seleziona **Accedi**, è necessario scegliere un provider di autenticazione e configurarlo.

![](./media/app-service-api-authentication/actiontotake.png)

Per informazioni dettagliate sulla configurazione dell'autenticazione, vedere [Come configurare un'applicazione del servizio app per usare l'account di accesso di Azure Active Directory](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md). L'articolo si applica alle app per le API, nonché alle app per dispositivi mobili e vengono forniti collegamenti ad altri articoli relativi ad altri provider di autenticazione.
 
## <a id="internal"></a> Autenticazione dell'account del servizio

L'autenticazione del servizio app funziona per gli scenari interni, ad esempio per la chiamata da un'app per le API a un'altra. In questo scenario si ottiene un token usando le credenziali per un account del servizio invece delle credenziali dell'utente finale. Un account del servizio è noto anche come *entità servizio* in Azure Active Directory e l'autenticazione che usa un account di questo tipo è nota anche come scenario da servizio a servizio.

Per gli scenari da servizio a servizio, proteggere l'app per le API chiamata usando Azure Active Directory e fornire un token AAD di autorizzazione dell'entità servizio durante la chiamata all'app per le API. Per ottenere un token, è possibile fornire l'ID client e il segreto client dell'applicazione AAD. Non è necessario alcun codice speciale di Azure, come quello usato per la gestione del token zumo dei servizi mobili. Un esempio di questo scenario che usa le app per le API ASP.NET è illustrato nell'esercitazione [Autenticazione dell'entità servizio per app per le API](app-service-api-dotnet-service-principal-auth.md).

Per gestire uno scenario da servizio a servizio senza l'autenticazione del servizio app, è possibile usare i certificati client o l'autenticazione di base. Per informazioni sui certificati client in Azure, vedere [Come configurare l'autenticazione reciproca TLS per un'app Web](../app-service-web/app-service-web-configure-tls-mutual-auth.md). Per informazioni sull'autenticazione di base in ASP.NET, vedere il blog sui [filtri di autenticazione nell'API Web 2 ASP.NET](http://www.asp.net/web-api/overview/security/authentication-filters).

L'autenticazione dell'account da un'app per la logica del servizio app per un'app per le API è un caso speciale ed è illustrata nell'articolo [Uso dell'API personalizzata ospitata nel servizio app con App per la logica](../app-service-logic/app-service-logic-custom-hosted-api.md).

## Autenticazione dei client per dispositivi mobili

Per informazioni su come gestire l'autenticazione dai client per dispositivi mobili, vedere la [documentazione sull'autenticazione per le app per dispositivi mobili](../app-service-mobile/app-service-mobile-ios-get-started-users.md). L'autenticazione del servizio app funziona allo stesso modo per le app per dispositivi mobili e per le app per le API.
  
## Altre informazioni

Per altre informazioni su autenticazione e autorizzazione nel servizio app di Azure, vedere le risorse seguenti:

* [Espansione dell'autenticazione/autorizzazione del servizio App](/blog/announcing-app-service-authentication-authorization/)
* [Come configurare un'applicazione del servizio app per usare l'account di accesso di Azure Active Directory](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md). Include i collegamenti per altri provider di autenticazione in alto nella pagina.

Per altre informazioni su OAuth 2.0, OpenID Connect e i token Web JSON (JWT), vedere le risorse seguenti.

* [Introduzione a OAuth 2.0](http://shop.oreilly.com/product/0636920021810.do "Introduzione a OAuth 2.0")
* [Introduzione a OAuth2, OpenID Connect e JSON Web Tokens (JWT) - corso PluralSight](http://www.pluralsight.com/courses/oauth2-json-web-tokens-openid-connect-introduction)
* [Creazione e protezione di un'API RESTful per più client in ASP.NET - corso PluralSight](http://www.pluralsight.com/courses/building-securing-restful-api-aspdotnet)

Per altre informazioni su Azure Active Directory, vedere le risorse seguenti.

* [Scenari per Azure AD](http://aka.ms/aadscenarios)
* [Guida per sviluppatori Azure AD](http://aka.ms/aaddev)
* [Esempi di Azure AD](http://aka.ms/aadsamples)

## Passaggi successivi

Questo articolo ha illustrato le funzionalità di autenticazione e autorizzazione del servizio app che è possibile usare per le app per le API. La prossima esercitazione della serie introduttiva illustra come implementare l’[autenticazione utente per le app per le API nel servizio app di Azure](app-service-api-dotnet-user-principal-auth.md).

<!---HONumber=AcomDC_0713_2016-->