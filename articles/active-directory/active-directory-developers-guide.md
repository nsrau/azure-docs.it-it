---
title: Guida per gli sviluppatori di Azure Active Directory | Documentazione Microsoft
description: Questo articolo fornisce una guida completa alle risorse dedicate agli sviluppatori per Azure Active Directory.
services: active-directory
documentationcenter: dev-center-name
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/09/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 0500d903438e0fb062ca848e6f488860e214a705
ms.openlocfilehash: 55b6f6e03bfd1877d4f204cb5751ae46379d6951


---
# <a name="azure-active-directory-developers-guide"></a>Guida per gli sviluppatori di Azure Active Directory
## <a name="overview"></a>Overview
Azure Active Directory (AD) è una piattaforma IDMaaS (Identity Management As A Service, gestione delle identità come servizio) che offre agli sviluppatori un modo efficace per integrare la gestione delle identità nelle applicazioni. Gli articoli seguenti offrono panoramiche sull'implementazione e illustrano le funzionalità principali di Azure AD. È consigliabile leggere gli articoli nell'ordine presentato oppure passare alla sezione [Per iniziare](#getting-started) se si è pronti a passare all'azione.

1. [Integrazione con Azure Active Directory](develop/active-directory-how-to-integrate.md): illustra il motivo per cui l'integrazione con Azure AD offre la migliore soluzione per autorizzazione e accesso sicuro.
2. [Scenari di autenticazione per Azure AD](active-directory-authentication-scenarios.md): illustra come sfruttare l'autenticazione semplificata di Azure AD per fornire l'accesso all'applicazione.
3. [Integrazione di applicazioni con Azure Active Directory](active-directory-integrating-applications.md): illustra come aggiungere, aggiornare e rimuovere applicazioni da Azure AD e le linee guida alla personalizzazione per le app integrate.
4. [API Graph di Azure Active Directory](active-directory-graph-api.md): illustra come usare l'API Graph di Azure AD per accedere a livello di codice ad Azure AD tramite gli endpoint dell'API REST. L'API Graph di Azure AD è accessibile anche da [Microsoft Graph](https://graph.microsoft.io/). Microsoft Graph fornisce un'API unificata che consente l'accesso a più API del servizio Microsoft Cloud tramite un singolo endpoint API REST e con un token di accesso singolo.
5. [Azure Active Directory Authentication Library](active-directory-authentication-libraries.md): illustra come autenticare facilmente gli utenti per ottenere i token di accesso mediante le librerie di autenticazione di Azure AD per .NET, JavaScript, Objective-C, Android e molte altre.

## <a name="getting-started"></a>Per iniziare
Queste esercitazioni sono disponibili per diverse piattaforme e consentono di iniziare rapidamente a sviluppare con Azure Active Directory. Come prerequisito, è necessario [ottenere un tenant di Azure Active Directory](active-directory-howto-tenant.md).

### <a name="mobile-and-pc-application-quick-start-guides"></a>Guide introduttive per applicazioni per dispositivi mobili e PC
| [![iOS](./media/active-directory-developers-guide/ios.png)](active-directory-devquickstarts-ios.md) | [![Android](./media/active-directory-developers-guide/android.png)](active-directory-devquickstarts-android.md) | [![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-dotnet.md) | [![Windows Universal](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsstore.md) | [![Xamarin](./media/active-directory-developers-guide/xamarin.png)](active-directory-devquickstarts-xamarin.md) | [![Cordova](./media/active-directory-developers-guide/cordova.png)](active-directory-devquickstarts-cordova.md) | [![OAuth 2.0](./media/active-directory-developers-guide/oauth-2.png)](active-directory-protocols-oauth-code.md) |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| [iOS](active-directory-devquickstarts-ios.md) |[Android](active-directory-devquickstarts-android.md) |[.NET](active-directory-devquickstarts-dotnet.md) |[Windows Universal](active-directory-devquickstarts-windowsstore.md) |[Xamarin](active-directory-devquickstarts-xamarin.md) |[Cordova](active-directory-devquickstarts-cordova.md) |[Integrare direttamente con OAuth 2.0](active-directory-protocols-oauth-code.md) |

### <a name="web-application-quick-start-guides"></a>Guide introduttive per applicazioni web
| [![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapp-dotnet.md) | [![Java](./media/active-directory-developers-guide/java.png)](active-directory-devquickstarts-webapp-java.md) | [![AngularJS](./media/active-directory-developers-guide/angularjs.png)](active-directory-devquickstarts-angular.md) | [![JavaScript](./media/active-directory-developers-guide/javascript.png)](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | [![Node.JS](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-openidconnect-nodejs.md) | [![OpenID Connect](./media/active-directory-developers-guide/openid-connect.png)](active-directory-protocols-openid-connect-code.md) |
|:---:|:---:|:---:|:---:|:---:|:---:|
| [.NET](active-directory-devquickstarts-webapp-dotnet.md) |[Java](active-directory-devquickstarts-webapp-java.md) |[AngularJS](active-directory-devquickstarts-angular.md) |[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |[Node.JS](active-directory-devquickstarts-openidconnect-nodejs.md) |[Integrare direttamente con OpenID Connect](active-directory-protocols-openid-connect-code.md) |

### <a name="web-api-quick-start-guides"></a>Guide introduttive per API Web
| [![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapi-dotnet.md) | [![Node.JS](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-webapi-nodejs.md) |
|:---:|:---:|
| [.NET](active-directory-devquickstarts-webapi-dotnet.md) |[Node.JS](active-directory-devquickstarts-webapi-nodejs.md) |

### <a name="querying-the-directory-quickstart-guide"></a>Guida rapida all’esecuzione di query nella directory
| [![.NET](./media/active-directory-developers-guide/graph.png)](active-directory-graph-api-quickstart.md) |
|:---:|
| [API Graph](active-directory-graph-api-quickstart.md) |

## <a name="how-tos"></a>Procedure
Questi articoli descrivono come eseguire attività specifiche usando Azure Active Directory:

* [Come ottenere un tenant di Azure Active Directory](active-directory-howto-tenant.md)
* [How to sign in any Azure Active Directory (AD) user using the multi-tenant application pattern](active-directory-devhowto-multi-tenant-overview.md)
* [Autenticazione di un'applicazione di servizio/daemon con un certificato](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/)
* Come abilitare l'accesso Single Sign-On tra app tramite ADAL in dispositivi [Android](active-directory-sso-android.md) e [iOS](active-directory-sso-ios.md)
* [How to get AppSource Certified for Azure Active Directory (AD)](active-directory-devhowto-appsource-certified.md)
* [Inserimento dell'applicazione nella raccolta di applicazioni Azure Active Directory](active-directory-app-gallery-listing.md)
* [Inviare app Web per Office 365 al Dashboard venditori](https://msdn.microsoft.com/office/office365/howto/submit-web-apps-seller-dashboard)
* [Registrare un'applicazione con Azure Active Directory con il Portale di Azure](active-directory-app-registration.md)
* [Informazioni sul manifesto dell'applicazione in Azure Active Directory](active-directory-application-manifest.md)
* [Informazioni sulle linee guida di personalizzazione per i pulsanti di accesso e acquisizione di app nell'applicazione client](active-directory-branding-guidelines.md)
* [Anteprima: Come compilare app che consentono agli utenti di accedere sia con un account personale che con un account aziendale o dell'istituto di istruzione](active-directory-appmodel-v2-overview.md)
* [Anteprima: Come compilare applicazioni che permettono ai consumer di iscriversi e accedere](../active-directory-b2c/active-directory-b2c-overview.md)
* [Preview: Configuring Token Lifetimes in Azure AD](active-directory-configurable-token-lifetimes.md) (Anteprima: Configurazione della durata dei token in Azure AD) tramite PowerShell. Per informazioni dettagliate sulla configurazione tramite l'API Graph di Azure AD, vedere [Policy operations](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) (Operazioni sui criteri) e [Policy entity](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#policy-entity) (Entità dei criteri).

## <a name="reference"></a>riferimento
Questi articoli forniscono riferimenti di base per endpoint, esempi di codice, errori, protocolli e API della libreria di autenticazione e REST.  

### <a name="support"></a>Supporto
* [Domande con tag](http://stackoverflow.com/questions/tagged/azure-active-directory): in Stack Overflow è possibile trovare soluzioni di Azure Active Directory cercando i tag [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) e [adal](http://stackoverflow.com/questions/tagged/adal).
* Vedere il [glossario per gli sviluppatori di Azure AD](active-directory-dev-glossary.md) per le definizioni di alcuni dei termini di uso comune correlati allo sviluppo e all'integrazione di applicazioni.

### <a name="code"></a>Codice
* [Librerie open source di Azure Active Directory](http://github.com/AzureAD): per trovare rapidamente il codice sorgente di una libreria, è possibile usare l'[elenco di librerie](active-directory-authentication-libraries.md).
* [Esempi per Azure Active Directory](https://github.com/azure-samples?query=active-directory): per esplorare facilmente l'elenco di esempi, è possibile usare l'[indice degli esempi di codice](active-directory-code-samples.md).
* [Active Directory Authentication Library (ADAL) per .NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet): è disponibile la documentazione di riferimento sia per la [versione principale più recente](https://docs.microsoft.com/active-directory/adal/microsoft.identitymodel.clients.activedirectory) che per la [versione principale precedente](https://docs.microsoft.com/active-directory/adal/v2/microsoft.identitymodel.clients.activedirectory).

### <a name="graph-api"></a>API Graph
* [Informazioni di riferimento sull'API Graph](https://msdn.microsoft.com/library/azure/hh974476.aspx): informazioni di riferimento su REST per l'API Graph di Azure Active Directory. [Visualizzare l’esperienza interattiva per le informazioni di riferimento sull'API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
* [Ambiti di autorizzazione dell'API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes): ambiti di autorizzazione di OAuth 2.0 usati per controllare l'accesso di un'app ai dati della directory in un tenant.

### <a name="authentication-and-authorization-protocols"></a>Supporto per i protocolli di autenticazione e autorizzazione
* [Rollover della chiave di firma in Azure Active Directory](active-directory-signing-key-rollover.md): informazioni sulla cadenza di rollover della chiave di firma di Azure AD e su come aggiornare la chiave per gli scenari di applicazione più comuni.
* [Autorizzare l'accesso ad applicazioni Web con OAuth 2.0 e Azure Active Directory](active-directory-protocols-oauth-code.md): è possibile usare la concessione del codice di autorizzazione del protocollo OAuth 2.0 per autorizzare l'accesso alle applicazioni Web e alle API Web nel tenant di Azure Active Directory.
* [Informazioni sul flusso di concessione implicita OAuth2 in Azure Active Directory (AD)](active-directory-dev-understanding-oauth2-implicit-grant.md): informazioni sulla concessione implicita di autorizzazioni e indicazioni utili per stabilire se è adatta all'applicazione specifica.
* [Chiamate da servizio a servizio mediante le credenziali client](active-directory-protocols-oauth-service-to-service.md): la concessione delle credenziali client OAuth 2.0 consente a un servizio Web, ovvero un client riservato, di usare le proprie credenziali per l'autenticazione durante la chiamata a un altro servizio Web invece di rappresentare un utente. In questo scenario il client è in genere un servizio Web di livello intermedio, un servizio daemon o un sito Web.
* [Autorizzare l'accesso ad applicazioni Web con OpenID Connect e Azure Active Directory](active-directory-protocols-openid-connect-code.md): il protocollo OpenID Connect 1.0 estende OAuth 2.0 per l'uso come protocollo di autenticazione. Un'applicazione client può ricevere un valore id_token per gestire il processo di accesso o per integrare il flusso del codice di autorizzazione in modo da ricevere sia un valore id_token che un codice di autorizzazione.
* [Informazioni di riferimento sul protocollo SAML 2.0](active-directory-saml-protocol-reference.md): il protocollo SAML 2.0 consente alle applicazioni di fornire agli utenti un'esperienza di accesso Single Sign-On.
* [Protocollo WS-Federation 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html): Azure Active Directory supporta WS-Federation 1.2, come indicato nella specifica Web Services Federation versione 1.2. Per altre informazioni sui documenti di metadati della federazione, vedere [Metadati della federazione](active-directory-federation-metadata.md).
* [Token e tipi di attestazioni supportati](active-directory-token-and-claims.md): questa guida illustra come valutare le attestazioni nei token SAML 2.0 e JWT (JSON Web Token).

## <a name="videos"></a>Video
### <a name="build"></a>Compilazione
Queste presentazioni generali sullo sviluppo di app tramite Azure Active Directory sono realizzate da esperti che collaborano direttamente con il team di progettazione e illustrano gli argomenti fondamentali, tra cui gestione delle identità come servizio (IDMaaS), autenticazione, federazione delle identità e accesso Single Sign-On.

* [Microsoft Identity: State of the Union and Future Direction](https://azure.microsoft.com/documentation/videos/build-2016-microsoft-identity-state-of-the-union-and-future-direction/)
* [Azure Active Directory: gestione delle identità come servizio per le applicazioni moderne](https://azure.microsoft.com/documentation/videos/build-2015-azure-active-directory-identity-management-as-a-service-for-modern-applications/)
* [Sviluppare applicazioni Web moderne con Azure Active Directory](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-web-applications-with-azure-active-directory/)
* [Sviluppare applicazioni native moderne con Azure Active Directory](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-native-applications-with-azure-active-directory/)

### <a name="azure-friday"></a>Azure Friday
[Azure Friday](https://azure.microsoft.com/documentation/videos/azure-friday/) è una serie di video 1:1 presentata ogni venerdì e dedicata a brevi interviste di 10-15 minuti con esperti su una vasta gamma di argomenti relativi ad Azure.  Usare il filtro per i servizi disponibile nella pagina per vedere tutti i video su Azure Active Directory.

* [Identità di Azure 101](https://azure.microsoft.com/documentation/videos/azure-identity-basics/)
* [Identità di Azure 102](https://azure.microsoft.com/documentation/videos/azure-identity-creating-active-directory/)
* [Identità di Azure 103](https://azure.microsoft.com/documentation/videos/azure-identity-application-to-authenticate/)

## <a name="social"></a>Social media
* [Blog del team di Active Directory](http://blogs.technet.com/b/ad/): ultime novità sullo sviluppo nel mondo di Azure Active Directory.
* [Blog del team Graph di Azure Active Directory](http://blogs.msdn.com/b/aadgraphteam): informazioni specifiche per l'API Graph di Azure Active Directory.
* [Identità cloud](http://www.cloudidentity.net): considerazioni sulla gestione delle identità come servizio, da un esperto PM di Azure Active Directory.  
* [Azure Active Directory su Twitter](https://twitter.com/azuread): annunci relativi ad Azure Active Directory in 140 caratteri o meno.

## <a name="windows-server-on-premises-development"></a>Sviluppo locale con Windows Server
Per indicazioni su come usare lo sviluppo con Windows Server e Active Directory Federation Services (AD FS), vedere:

* [Scenari di AD FS per gli sviluppatori](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/overview/ad-fs-scenarios-for-developers): panoramica dei componenti di AD FS e del relativo funzionamento, con informazioni dettagliate sugli scenari di autenticazione/autorizzazione supportati.
* [Procedure dettagliate per AD FS](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/ad-fs-development): elenco di articoli contenenti istruzioni dettagliate per l'implementazione dei flussi di autenticazione/autorizzazione correlati.



<!--HONumber=Dec16_HO2-->


