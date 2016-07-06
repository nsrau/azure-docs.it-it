<properties
   pageTitle="Guida per gli sviluppatori di Azure Active Directory | Microsoft Azure"
   description="Questo articolo fornisce una guida completa alle risorse dedicate agli sviluppatori per Azure Active Directory."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/23/2016"
   ms.author="mbaldwin"/>


# Guida per gli sviluppatori di Azure Active Directory

## Panoramica
Azure Active Directory è una piattaforma IDMaaS (Identity Management As A Service, gestione delle identità come servizio) che offre agli sviluppatori un modo efficace per integrare la gestione delle identità nelle applicazioni. Gli articoli seguenti forniscono panoramiche sull'implementazione e illustrano le funzionalità principali di Azure Active Directory. È consigliabile leggere gli articoli nell'ordine presentato oppure passare alla sezione [Per iniziare](#getting-started) se si è pronti a passare all'azione.


1. [I vantaggi dell'integrazione di Azure Active Directory](active-directory-how-to-integrate.md): illustra il motivo per cui l'integrazione con Azure Active Directory offre la migliore soluzione per autorizzazione e accesso sicuro.

1. [Scenari di autenticazione di Active Directory](active-directory-authentication-scenarios.md): illustra come sfruttare l'autenticazione semplificata di Azure Active Directory per fornire l'accesso all'applicazione.

1. [Integrazione di applicazioni con Azure Active Directory](active-directory-integrating-applications.md): informazioni su come aggiungere, aggiornare e rimuovere applicazioni da Azure Active Directory e sulle linee guida del marchio per le applicazioni integrate.

1. [API Graph di Azure Active Directory](active-directory-graph-api.md): illustra come usare l'API Graph di Azure Active Directory per accedere a livello di codice ad Azure Active Directory tramite gli endpoint dell'API REST. Si noti che l'API Graph di Azure AD è accessibile anche tramite [Microsoft Graph](https://graph.microsoft.io/), un’API unificata che consente l'accesso a più API del servizio Microsoft Cloud tramite un singolo endpoint API REST e con un token di accesso singolo.

1. [Azure Active Directory Authentication Library](active-directory-authentication-libraries.md): illustra come autenticare facilmente gli utenti per ottenere i token di accesso mediante le librerie di autenticazione di Azure AD per .NET, JavaScript, Objective-C, Android e molte altre.


## Introduzione

Queste esercitazioni sono disponibili per diverse piattaforme e consentono di iniziare rapidamente a sviluppare con Azure Active Directory. Come prerequisito, è necessario [ottenere un tenant di Azure Active Directory](active-directory-howto-tenant.md).

### Guide introduttive per applicazioni per dispositivi mobili e PC

|[![iOS](./media/active-directory-developers-guide/ios.png)](active-directory-devquickstarts-ios.md)|[![Android](./media/active-directory-developers-guide/android.png)](active-directory-devquickstarts-android.md)|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-dotnet.md)|[![Windows Universal](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsstore.md)|[![Xamarin](./media/active-directory-developers-guide/xamarin.png)](active-directory-devquickstarts-xamarin.md)|[![Cordova](./media/active-directory-developers-guide/cordova.png)](active-directory-devquickstarts-cordova.md)|[![OAuth 2.0](./media/active-directory-developers-guide/oauth-2.png)](active-directory-protocols-oauth-code.md)
|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
|[iOS](active-directory-devquickstarts-ios.md)|[Android](active-directory-devquickstarts-android.md)|[.NET](active-directory-devquickstarts-dotnet.md)|[Windows Universal](active-directory-devquickstarts-windowsstore.md)|[Xamarin](active-directory-devquickstarts-xamarin.md)|[Cordova](active-directory-devquickstarts-cordova.md)|[Integrare direttamente con OAuth 2.0](active-directory-protocols-oauth-code.md)|

### Guide introduttive per applicazioni web

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapp-dotnet.md)|[![Java](./media/active-directory-developers-guide/java.png)](active-directory-devquickstarts-webapp-java.md)|[![AngularJS](./media/active-directory-developers-guide/angularjs.png)](active-directory-devquickstarts-angular.md)|[![JavaScript](./media/active-directory-developers-guide/javascript.png)](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi)|[![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-openidconnect-nodejs.md) | [![OpenID Connect](./media/active-directory-developers-guide/openid-connect.png)](active-directory-protocols-openid-connect-code.md)
|:--:|:--:|:--:|:--:|:--:|:--:|
|[.NET](active-directory-devquickstarts-webapp-dotnet.md)|[Java](active-directory-devquickstarts-webapp-java.md)|[AngularJS](active-directory-devquickstarts-angular.md)|[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi)|[Node.JS](active-directory-devquickstarts-openidconnect-nodejs.md)|[Integrare direttamente con OpenID Connect](active-directory-protocols-openid-connect-code.md)|

### Guide introduttive per API Web

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapi-dotnet.md)|[![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-webapi-nodejs.md)
|:--:|:--:|
|[.NET](active-directory-devquickstarts-webapi-dotnet.md)|[Node.JS](active-directory-devquickstarts-webapi-nodejs.md)

### Guida rapida all’esecuzione di query nella directory

| [![.NET](./media/active-directory-developers-guide/graph.png)](active-directory-graph-api-quickstart.md)|
|:--:|
|[API Graph](active-directory-graph-api-quickstart.md)|

## Procedure

Questi articoli descrivono come eseguire attività specifiche usando Azure Active Directory:

- [Ottenere un tenant Azure Active Directory](active-directory-howto-tenant.md)
- [Inserimento dell'applicazione nella raccolta di applicazioni Azure Active Directory](active-directory-app-gallery-listing.md)
- [Informazioni sul manifesto dell'applicazione in Azure Active Directory](active-directory-application-manifest.md)
- [Creare un'app con le API di Office 365](https://msdn.microsoft.com/office/office365/howto/getting-started-Office-365-APIs)
- [Inviare app Web per Office 365 al Dashboard venditori](https://msdn.microsoft.com/office/office365/howto/submit-web-apps-seller-dashboard)
- Informazioni su come abilitare l'accesso Single Sign-On tra app usando ADAL in dispositivi [Android](active-directory-sso-android.md) e [iOS](active-directory-sso-ios.md)
- [Informazioni sulle linee guida di personalizzazione per i pulsanti di accesso e acquisizione di app nell'applicazione client](active-directory-branding-guidelines.md)
- [Anteprima: Come compilare app che consentono agli utenti di accedere sia con un account personale che con un account aziendale o dell'istituto di istruzione](active-directory-appmodel-v2-overview.md)
- [Anteprima: Come creare applicazioni che permettono ai consumer di iscriversi e accedere](../active-directory-b2c/active-directory-b2c-overview.md)


## Riferimento

Questi articoli forniscono riferimenti di base per endpoint, esempi di codice, errori, protocolli e API della libreria di autenticazione e REST.

###  Supporto
- [Domande con tag](http://stackoverflow.com/questions/tagged/azure-active-directory): in Overflow dello stack è possibile trovare soluzioni per Azure Active Directory eseguendo una ricerca in base ai tag [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) e [adal](http://stackoverflow.com/questions/tagged/adal).

### Codice

- [Librerie open source di Azure Active Directory](http://github.com/AzureAD): per trovare rapidamente il codice sorgente per le librerie, è possibile usare l'[elenco di librerie](active-directory-authentication-libraries.md).

- [Esempi per Azure Active Directory](https://github.com/azure-samples?query=active-directory): per esplorare l'elenco di esempi, è possibile usare l'[indice degli esempi di codice](active-directory-code-samples.md).

- [ADAL per .NET](https://msdn.microsoft.com/library/azure/mt417579.aspx): documentazione per la libreria di autenticazione .NET.

### API Graph

- [Informazioni di riferimento sull'API Graph](https://msdn.microsoft.com/library/azure/hh974476.aspx): informazioni di riferimento su REST per l'API Graph di Azure Active Directory. [Visualizzare l’esperienza interattiva per le informazioni di riferimento sull'API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

- [Ambiti di autorizzazione dell'API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes): ambiti di autorizzazione di OAuth 2.0 usati per controllare l'accesso di un'app ai dati della directory in un tenant.

### Supporto per i protocolli di autenticazione e autorizzazione

- [Rollover della chiave di firma in Azure AD](active-directory-signing-key-rollover.md): informazioni sulla cadenza di rollover della chiave di firma di Azure AD e su come aggiornare la chiave per gli scenari di applicazione più comuni.

- [Informazioni sul flusso di concessione implicita OAuth2 in Azure Active Directory (AD)](active-directory-dev-understanding-oauth2-implicit-grant.md): informazioni sulla concessione implicita di autorizzazioni e indicazioni utili per stabilire se è ottimale per l'applicazione specifica.

- [Informazioni di riferimento sul protocollo SAML 2.0](active-directory-saml-protocol-reference.md): il protocollo SAML 2.0 consente alle applicazioni di fornire agli utenti un'esperienza di accesso Single Sign-On.

- [Protocollo WS-Federation 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html): Azure Active Directory supporta WS-Federation 1.2, come indicato nella specifica Web Services Federation versione 1.2. Per altre informazioni sui documenti di metadati della federazione, vedere [Metadati della federazione](active-directory-federation-metadata.md).

- [Token e tipi di attestazioni supportati](active-directory-token-and-claims.md): questa guida illustra come valutare le attestazioni nei token SAML 2.0 e JWT (JSON Web Token).

## Video

### Compilazione

Queste presentazioni generali sullo sviluppo di app tramite Azure Active Directory sono realizzate da esperti che collaborano direttamente con il team di progettazione e illustrano gli argomenti fondamentali, tra cui gestione delle identità come servizio (IDMaaS), autenticazione, federazione delle identità e accesso Single Sign-On.

- [Microsoft Identity: State of the Union and Future Direction](https://azure.microsoft.com/documentation/videos/build-2016-microsoft-identity-state-of-the-union-and-future-direction/)
- [Azure Active Directory: gestione delle identità come servizio per le applicazioni moderne](https://azure.microsoft.com/documentation/videos/build-2015-azure-active-directory-identity-management-as-a-service-for-modern-applications/)
- [Sviluppare applicazioni Web moderne con Azure Active Directory](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-web-applications-with-azure-active-directory/)
- [Sviluppare applicazioni native moderne con Azure Active Directory](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-native-applications-with-azure-active-directory/)

### Azure Friday
[Azure Friday](https://azure.microsoft.com/documentation/videos/azure-friday/) è una serie di video 1:1 presentata ogni venerdì e dedicata a brevi interviste di 10-15 minuti con esperti su una vasta gamma di argomenti relativi ad Azure. Usare il filtro per i servizi disponibile nella pagina per vedere tutti i video su Azure Active Directory.

- [Identità di Azure 101](https://azure.microsoft.com/documentation/videos/azure-identity-basics/)
- [Identità di Azure 102](https://azure.microsoft.com/documentation/videos/azure-identity-creating-active-directory/)
- [Identità di Azure 103](https://azure.microsoft.com/documentation/videos/azure-identity-application-to-authenticate/)

## Social media

- [Blog del team di Active Directory](http://blogs.technet.com/b/ad/): ultime novità sullo sviluppo nel mondo di Azure Active Directory.

- [Blog del team Graph di Azure Active Directory](http://blogs.msdn.com/b/aadgraphteam): informazioni specifiche per l'API Graph di Azure Active Directory.

- [Identità cloud](http://www.cloudidentity.net): considerazioni sulla gestione delle identità come servizio, da un esperto PM di Azure Active Directory.

- [Azure Active Directory su Twitter](https://twitter.com/azuread): annunci relativi ad Azure Active Directory in 140 caratteri o meno.

<!---HONumber=AcomDC_0629_2016-->