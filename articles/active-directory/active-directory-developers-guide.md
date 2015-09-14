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
	ms.date="08/12/2015"
	ms.author="mbaldwin"/>


# Guida per gli sviluppatori di Azure Active Directory

## Panoramica
Azure Active Directory è una piattaforma IDMaaS (Identity Management As A Service, gestione delle identità come servizio) che offre agli sviluppatori un modo efficace per integrare la gestione delle identità nelle applicazioni. Gli articoli seguenti forniscono panoramiche sull'implementazione e illustrano le funzionalità principali di Azure Active Directory. È consigliabile leggere gli articoli nell'ordine presentato oppure passare alla sezione [Per iniziare](#getting-started) se si è pronti a passare all'azione.


1. [Integrazione con Azure Active Directory](active-directory-how-to-integrate.md): illustra il motivo per cui l'integrazione con Azure Active Directory offre la migliore soluzione per autorizzazione e accesso sicuro.

1. [Scenari di autenticazione di Active Directory](active-directory-authentication-scenarios.md): illustra come sfruttare l'autenticazione semplificata di Azure Active Directory per fornire l'accesso all'applicazione.

1. [API Graph di Azure Active Directory](https://msdn.microsoft.com/library/azure/hh974476.aspx): illustra come usare l'API Graph di Azure Active Directory per accedere a livello di codice ad Azure AD tramite gli endpoint dell'API REST.

1. [Integrazione di applicazioni in Azure Active Directory](active-directory-integrating-applications.md): fornisce informazioni sulla registrazione dell'applicazione e illustra le linee guida per la personalizzazione per le applicazioni multi-tenant.

1. [Azure Active Directory Authentication Library](active-directory-authentication-libraries.md): illustra come autenticare gli utenti in modo semplice per ottenere i token di accesso con le librerie di autenticazione di Azure.

Per visualizzare le panoramiche di Azure Active Directory presentate in occasione della conferenza Build 2015, vedere la sezione [video](#videos) riportata di seguito.


## Introduzione

Queste esercitazioni sono disponibili per diverse piattaforme e consentono di iniziare rapidamente a sviluppare con Azure Active Directory. Come prerequisito, è necessario [ottenere un tenant di Azure Active Directory](active-directory-howto-tenant.md).

### Guide introduttive per applicazioni per dispositivi mobili e PC

|[![iOS](./media/active-directory-developers-guide/ios.png)](active-directory-devquickstarts-ios.md)|[![Android](./media/active-directory-developers-guide/android.png)](active-directory-devquickstarts-android.md)|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-dotnet.md)| [![Windows Phone](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsphone.md)|[![Windows Store](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsstore.md)|[![Xamarin](./media/active-directory-developers-guide/xamarin.png)](active-directory-devquickstarts-xamarin.md)|[![Cordova](./media/active-directory-developers-guide/cordova.png)](active-directory-devquickstarts-cordova.md)
|:--:|:--:|:--:|:--:|:--:|:--:|:--:
|[iOS](active-directory-devquickstarts-ios.md)|[Android](active-directory-devquickstarts-android.md)|[.NET](active-directory-devquickstarts-dotnet.md)|[Windows Phone](active-directory-devquickstarts-windowsphone.md)|[Windows Store](active-directory-devquickstarts-windowsstore.md)|[Xamarin](active-directory-devquickstarts-xamarin.md)|[Cordova](active-directory-devquickstarts-cordova.md)


### Guide introduttive per applicazioni Web e API Web

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapp-dotnet.md)|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapi-dotnet.md)|[![JavaScript](./media/active-directory-developers-guide/javascript.png)](active-directory-devquickstarts-angular.md)|[![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-webapi-nodejs.md)
|:--:|:--:|:--:|:--:
|[App Web .NET](active-directory-devquickstarts-webapp-dotnet.md)|[API Web .NET](active-directory-devquickstarts-webapi-dotnet.md)|[JavaScript](active-directory-devquickstarts-angular.md)|[Node.JS](active-directory-devquickstarts-webapi-nodejs.md)

### Guida rapida all’esecuzione di query nella directory

| [![.NET](./media/active-directory-developers-guide/graph.png)](active-directory-graph-api-quickstart.md)|
|:--:|
|[API Graph](active-directory-graph-api-quickstart.md)|

## Procedure

Questi articoli descrivono come eseguire attività specifiche usando Azure Active Directory:

- [Come ottenere un tenant di Azure Active Directory](active-directory-howto-tenant.md)
- [Inserimento dell'applicazione nella raccolta di applicazioni Azure Active Directory](active-directory-app-gallery-listing.md)
- [Creare un'app con le API di Office 365](https://msdn.microsoft.com/office/office365/howto/getting-started-Office-365-APIs)
- [Inviare app Web per Office 365 al Dashboard venditori](https://msdn.microsoft.com/office/office365/howto/submit-web-apps-seller-dashboard)
- [Anteprima: Come compilare app che consentono agli utenti di accedere sia con un account personale che con un account aziendale o dell'istituto di istruzione](active-directory-appmodel-v2-overview.md)


## Riferimento

Questi articoli forniscono riferimenti di base per endpoint, esempi di codice, errori, protocolli e API della libreria di autenticazione e REST.

###  Supporto
- [Domande con tag](http://stackoverflow.com/questions/tagged/azure-active-directory): in Stack Overflow è possibile trovare soluzioni per Azure Active Directory eseguendo una ricerca in base ai tag [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) e [adal](http://stackoverflow.com/questions/tagged/adal).

### Codice

- [Librerie open source di Azure Active Directory](http://github.com/AzureAD): per trovare rapidamente il codice sorgente per le librerie, è possibile usare l'[elenco di librerie](active-directory-authentication-libraries.md).

- [Esempi per Azure Active Directory](http://github.com/AzureADSamples): per esplorare l'elenco di esempi, è possibile usare l'[indice degli esempi di codice](active-directory-code-samples.md).


### API Graph

- [Informazioni di riferimento sull'API Graph](https://msdn.microsoft.com/library/azure/hh974476.aspx): informazioni di riferimento su REST per l'API Graph di Azure Active Directory. [Visualizzare la nuova esperienza interattiva per le informazioni di riferimento sull'API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

- [Ambiti di autorizzazione dell'API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/graph-api-permission-scopes): ambiti di autorizzazione OAuth 2.0 usati per controllare l'accesso di un'app ai dati della directory in un tenant.


### Protocolli di autenticazione

- [Informazioni di riferimento sul protocollo SAML 2.0](https://msdn.microsoft.com/library/azure/dn195591.aspx): il protocollo SAML 2.0 consente alle applicazioni di fornire agli utenti un'esperienza di accesso Single Sign-On.


- [Informazioni di riferimento sul protocollo OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx): il protocollo OAuth 2.0 consente di autorizzare l'accesso alle applicazioni Web e alle API Web nel tenant di Azure Active Directory.


- [Informazioni di riferimento sul protocollo OpenID Connect 1.0](https://msdn.microsoft.com/library/azure/dn645541.aspx): il protocollo OpenID Connect 1.0 estende OAuth 2.0 per l'uso come protocollo di autenticazione.


- [Informazioni di riferimento sul protocollo WS-Federation 1.2](https://msdn.microsoft.com/library/azure/dn903702.aspx): protocollo WS-Federation 1.2, come indicato nella specifica Web Services Federation versione 1.2.

- [Token e tipi di attestazioni supportati](active-directory-token-and-claims.md): questa guida illustra come valutare le attestazioni nei token SAML 2.0 e JWT (JSON Web Token).

## Video

### Build 2015

Queste presentazioni generali sullo sviluppo di app tramite Azure Active Directory sono realizzate da esperti che collaborano direttamente con il team di progettazione e illustrano gli argomenti fondamentali, tra cui gestione delle identità come servizio (IDMaaS), autenticazione, federazione delle identità e accesso Single Sign-On.

- [Azure Active Directory: gestione delle identità come servizio per le applicazioni moderne](http://azure.microsoft.com/documentation/videos/build-2015-azure-active-directory-identity-management-as-a-service-for-modern-applications)
- [Sviluppare applicazioni Web moderne con Azure Active Directory](http://azure.microsoft.com/documentation/videos/build-2015-develop-modern-web-applications-with-azure-active-directory)
- [Sviluppare applicazioni native moderne con Azure Active Directory](http://azure.microsoft.com/documentation/videos/build-2015-develop-modern-native-applications-with-azure-active-directory)

### Azure Friday
[Azure Friday](http://azure.microsoft.com/documentation/videos/azure-friday/) è una serie di video 1:1 presentata ogni venerdì e dedicata a brevi interviste di 10-15 minuti con esperti su una vasta gamma di argomenti relativi ad Azure. Usare il filtro per i servizi disponibile nella pagina per vedere tutti i video su Azure Active Directory.

- [Identità di Azure 101](http://azure.microsoft.com/documentation/videos/azure-identity-basics/)
- [Identità di Azure 102](http://azure.microsoft.com/documentation/videos/azure-identity-creating-active-directory/)
- [Identità di Azure 103](http://azure.microsoft.com/documentation/videos/azure-identity-application-to-authenticate/)

## Social media

- [Blog del team di Active Directory](http://blogs.technet.com/b/ad/): ultime novità sullo sviluppo nel mondo di Azure Active Directory.

- [Blog del team Graph di Azure Active Directory](http://blogs.msdn.com/b/aadgraphteam): informazioni specifiche per l'API Graph di Azure Active Directory.

- [Identità cloud](http://www.cloudidentity.net): considerazioni sulla gestione delle identità come servizio, da un esperto Project Manager di Azure Active Directory.

- [Azure Active Directory su Twitter](https://twitter.com/azuread): annunci relativi ad Azure Active Directory in 140 caratteri o meno.

<!---HONumber=September15_HO1-->