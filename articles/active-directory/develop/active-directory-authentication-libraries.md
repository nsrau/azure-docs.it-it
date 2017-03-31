---
title: Azure Active Directory Authentication Library | Documentazione Microsoft
description: Azure AD Authentication Library (ADAL) consente agli sviluppatori di applicazioni client di autenticare facilmente gli utenti in Active Directory locale o nel cloud e quindi di ottenere token di accesso per proteggere le chiamate API.
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: mbaldwin
ms.assetid: 2e4fc79a-0285-40be-8c77-65edee408a22
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/13/2017
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 2ad287d383f65ea66382d1f449a76db596963f46
ms.lasthandoff: 03/29/2017


---
# <a name="azure-active-directory-authentication-libraries"></a>Azure Active Directory Authentication Library
Azure AD Authentication Library (ADAL) consente agli sviluppatori di applicazioni client di autenticare facilmente gli utenti in Active Directory locale o nel cloud e quindi di ottenere token di accesso per proteggere le chiamate API. Azure AD Authentication Library offre numerose funzionalità che semplificano l'integrazione dell'autenticazione nelle applicazioni da parte degli sviluppatori, ad esempio il supporto asincrono, la cache di token configurabile per l'archiviazione dei token di accesso e dei token di aggiornamento e l'aggiornamento automatico dei token alla scadenza dei token di accesso, i token di aggiornamento e altre ancora. Poiché Azure AD Authentication Library gestisce la maggior parte della complessità, gli sviluppatori possono concentrarsi sulla logica di business nell'applicazione e di proteggere le risorse pur non avendo competenze elevate in termini di sicurezza.

Azure AD Authentication Library è disponibile per una vasta gamma di piattaforme.

### <a name="client-libraries"></a>Librerie client

| Piattaforma | Libreria | Download | Codice sorgente | Esempio | Riferimento
| --- | --- | --- | --- | --- | --- |
| .NET Client, Windows Store, UWP, Xamarin iOS e Android |ADAL .NET v3 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) | [App desktop](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-dotnet) |[Riferimento](https://docs.microsoft.com/active-directory/adal/microsoft.identitymodel.clients.activedirectory) | 
| .NET Client, Windows Store, Windows Phone 8.1 |ADAL .NET v2 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.2) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/releases/tag/v2.28.2) | [App desktop](https://github.com/AzureADQuickStarts/NativeClient-DotNet/releases/tag/v2.X) |[Riferimento](https://docs.microsoft.com/en-us/active-directory/adal//v2/microsoft.identitymodel.clients.activedirectory) | 
| JavaScript |ADAL.js |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[App a pagina singola](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | |
| iOS, macOS |ADAL |[CocoaPods](http://cocoadocs.org/docsets/ADAL/) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |[App iOS](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-ios) | [Riferimento](http://cocoadocs.org/docsets/ADAL/)|
| Android |ADAL |[Repository centrale](http://search.maven.org/remotecontent?filepath=com/microsoft/aad/adal/) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-android) |[App Android](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-android) | [JavaDocs](http://javadoc.io/doc/com.microsoft.aad/adal/)|
| Node.js |ADAL |[npm](https://www.npmjs.com/package/adal-node) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) | | |
| Java |ADAL4J |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[App Web Java](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-webapp-java) | |

### <a name="server-libraries"></a>Librerie server 

| Piattaforma | Libreria | Download | Codice sorgente | Esempio | Riferimenti
| --- | --- | --- | --- | --- | --- |
| .NET |OWIN per AzureAD|[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[CodePlex](http://katanaproject.codeplex.com) |[Applicazione MVC](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-webapp-dotnet) | |
| .NET |OWIN per OpenIDConnect |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[CodePlex](http://katanaproject.codeplex.com) |[App Web](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | |
| Node.js |Passport Azure AD |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [API Web](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-webapi-nodejs)| |
| .NET |OWIN per WS-Federation |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[CodePlex](http://katanaproject.codeplex.com) |[App Web MVC](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | |
| .NET |Estensioni del protocollo di identità per .NET 4.5 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET |Gestore token JWT per .NET 4.5 |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |



## <a name="scenarios"></a>Scenari
Di seguito sono illustrati i tre scenari comuni in cui è possibile usare Azure AD Authentication Library per l'autenticazione.  

### <a name="authenticating-users-of-a-client-application-to-a-remote-resource"></a>Autenticazione degli utenti di un'applicazione client in una risorsa remota
In questo scenario, uno sviluppatore ha un client, ad esempio un'applicazione WPF, che deve accedere a una risorsa remota protetta tramite Azure AD, ad esempio un'API Web. Ha una sottoscrizione di Azure, sa come richiamare l'API Web downstream e sa qual è il tenant di Azure AD usato dall'API Web. Può quindi usare Azure AD Authentication Library (ADAL) per semplificare l'autenticazione con Azure AD, sia delegando l'esperienza di autenticazione ad ADAL sia gestendo in modo esplicito le credenziali degli utenti. ADAL agevola l'autenticazione dell'utente, il recupero di un token di accesso e di un token di aggiornamento da Azure AD e quindi usa il token di accesso per inviare le richieste all'API Web.

Per un esempio di codice che illustra questo scenario usando l'autenticazione ad Azure AD, vedere [chiamata di un'API Web da parte di un'applicazione WPF client nativa](https://github.com/azureadsamples/nativeclient-dotnet).

### <a name="authenticating-a-server-application-to-a-remote-resource"></a>Autenticazione di un'applicazione server in una risorsa remota
In questo scenario, uno sviluppatore ha un'applicazione in esecuzione in un server che deve accedere a una risorsa remota protetta tramite Azure AD, ad esempio un'API Web. Ha una sottoscrizione di Azure, sa come richiamare il servizio downstream e sa qual è il tenant di Azure AD usato dall'API Web. Può quindi usare Azure AD Authentication Library (ADAL) per semplificare l'autenticazione con Azure AD gestendo in modo esplicito le credenziali dell'applicazione. ADAL semplifica il recupero di un token da Azure AD perché usa le credenziali client dell'applicazione e quindi il token per inviare richieste all'API Web. ADAL gestisce anche la durata del token di accesso memorizzandolo nella cache e rinnovandolo quando necessario. Per un esempio di codice che illustra questo scenario, vedere [chiamata di un'API Web da parte di un'applicazione console](https://github.com/AzureADSamples/Daemon-DotNet).

### <a name="authenticating-a-server-application-on-behalf-of-a-user-to-access-a-remote-resource"></a>Autenticazione di un'applicazione server per conto di un utente per accedere a una risorsa remota
In questo scenario, uno sviluppatore ha un'applicazione in esecuzione in un server che deve accedere a una risorsa remota protetta tramite Azure AD, ad esempio un'API Web. La richiesta deve essere inviata per conto di un utente in Azure AD. Ha una sottoscrizione di Azure, sa come richiamare l'API Web downstream e sa qual è il tenant di Azure AD usato dal servizio. Una volta che l'utente ha eseguito l'autenticazione con l'applicazione Web, l'applicazione può ottenere un codice di autorizzazione per l'utente da Azure AD. L'applicazione Web può quindi usare Azure AD Authentication Library (ADAL) per ottenere un token di accesso e un token di aggiornamento per conto di un utente da Azure AD usando il codice di autorizzazione e le credenziali client associati all'applicazione. Non appena l'applicazione Web è in possesso del token di accesso, può chiamare l'API Web fino a quando il token non raggiunge la data di scadenza. Alla scadenza del token, l'applicazione Web potrà usare ADAL per ottenere un nuovo token di accesso usando il token di aggiornamento ricevuto in precedenza.

## <a name="see-also"></a>Vedere anche
[Guida per gli sviluppatori di Azure Active Directory](active-directory-developers-guide.md)

[Scenari di autenticazione per Azure Active Directory](active-directory-authentication-scenarios.md)

[Esempi di codice per Azure Active Directory](active-directory-code-samples.md)

