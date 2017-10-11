---
title: Azure Active Directory Authentication Library | Microsoft Docs
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
ms.date: 08/25/2017
ms.author: bryanla
ms.custom: aaddev
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: 1b79fb5b280b0cb4e087c2acde07796fd51e81fb
ms.contentlocale: it-it
ms.lasthandoff: 09/13/2017

---
# <a name="azure-active-directory-authentication-libraries"></a>Azure Active Directory Authentication Library
Azure Active Directory Authentication Library (ADAL) consente agli sviluppatori di applicazioni di autenticare gli utenti in Active Directory (AD) locale o nel cloud e di ottenere token per proteggere le chiamate API. ADAL semplifica l'autenticazione per gli sviluppatori grazie a funzionalità come:
 - Cache dei token configurabili in cui archiviare i token di accesso e i token di aggiornamento
 - Aggiornamento del token automatico quando un token di accesso scade ed è disponibile un token di aggiornamento
 - Supporto per le chiamate di metodo asincrone
 - Altro

> [!NOTE]
> Si cercano le librerie v2.0 di Azure AD (MSAL)? Controllare la [Guida alle librerie MSAL](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-v2-libraries). 
> 
> 

### <a name="client-libraries"></a>Librerie client

| Piattaforma | Libreria | Download | Codice sorgente | Esempio | Riferimento
| --- | --- | --- | --- | --- | --- |
| .NET Client, Windows Store, UWP, Xamarin iOS e Android |ADAL .NET v3 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) | [App desktop](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-dotnet) |[Riferimento](https://docs.microsoft.com/dotnet/api/?view=identitymodelclientsad-3.13.9) | 
| .NET Client, Windows Store, Windows Phone 8.1 |ADAL .NET v2 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.4) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/releases/tag/v2.28.4) | [App desktop](https://github.com/AzureADQuickStarts/NativeClient-DotNet/releases/tag/v2.X) | | 
| JavaScript |ADAL.js |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[App a pagina singola](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | |
| iOS, macOS |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |[App iOS](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-ios) | [Riferimento](https://cocoapods.org/pods/ADAL)|
| Android |ADAL |[Repository centrale](http://search.maven.org/remotecontent?filepath=com/microsoft/aad/adal/) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-android) |[App Android](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-android) | [JavaDocs](http://javadoc.io/doc/com.microsoft.aad/adal/)|
| Node.js |ADAL |[npm](https://www.npmjs.com/package/adal-node) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) | | |
| Java |ADAL4J |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[App Web Java](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapp-java) | |
| Python |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) | | |

### <a name="server-libraries"></a>Librerie server 

| Piattaforma | Libreria | Download | Codice sorgente | Esempio | Riferimenti
| --- | --- | --- | --- | --- | --- |
| .NET |OWIN per AzureAD|[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[CodePlex](http://katanaproject.codeplex.com) |[Applicazione MVC](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapp-dotnet) | |
| .NET |OWIN per OpenIDConnect |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[CodePlex](http://katanaproject.codeplex.com) |[App Web](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | |
| Node.js |Passport Azure AD |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [API Web](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapi-nodejs)| |
| .NET |OWIN per WS-Federation |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[CodePlex](http://katanaproject.codeplex.com) |[App Web MVC](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | |
| .NET |Estensioni del protocollo di identità per .NET 4.5 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET |Gestore token JWT per .NET 4.5 |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |

### <a name="v20-client-libraries-msal"></a>Librerie client v2.0 (MSAL)

L'[endpoint di Azure AD v 2.0](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-v2-compare) combina Azure AD e Account Microsoft in un singolo endpoint. Per accedere a questo endpoint, gli sviluppatori possono usare le [librerie MSAL di anteprima supportate dalla produzione](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-v2-libraries) anziché ADAL.

| Piattaforma | Libreria | Download | Codice sorgente | Esempio | Riferimento
| --- | --- | --- | --- | --- | --- |
| .NET Client, Windows Store, UWP, Xamarin iOS e Android |MSAL per .NET (anteprima) |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client/1.1.0-preview) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [App desktop](~/articles/active-directory/develop/guidedsetups/active-directory-windesktop.md) |[Riferimento](https://docs.microsoft.com/dotnet/api/?view=identityclient-1.1.0-preview) | 
| JavaScript |MSAL per JavaScript (anteprima) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [App a pagina singola](~/articles/active-directory/develop/GuidedSetups/active-directory-javascriptspa.md) | [Riferimento](https://htmlpreview.github.io/?https://raw.githubusercontent.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/docs/classes/_useragentapplication_.msal.useragentapplication.html) | 
| iOS |MSAL per iOS (anteprima) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [App iOS](~/articles/active-directory/develop/GuidedSetups/active-directory-ios.md) | [Riferimento](https://azuread.github.io/docs/objc/) |
| Android |MSAL per Android (anteprima) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [App Android](~/articles/active-directory/develop/GuidedSetups/active-directory-android.md) | [Riferimento](http://javadoc.io/doc/com.microsoft.identity.client/msal/0.1.1) |

## <a name="scenarios"></a>Scenari

Di seguito sono illustrati i tre scenari comuni in cui è possibile usare Azure AD Authentication Library per l'autenticazione di un client che accede a una risorsa remota:  

### <a name="authenticating-users-of-a-native-client-application-running-on-a-device"></a>Autenticazione di utenti di un'applicazione client nativa in esecuzione su un dispositivo 

In questo scenario, uno sviluppatore ha un'applicazione client WPF, che deve accedere a una risorsa remota protetta da Azure AD, ad esempio un'API Web. Ha una sottoscrizione di Azure, sa come richiamare l'API Web downstream e sa qual è il tenant di Azure AD usato dall'API Web. Può quindi usare Azure AD Authentication Library (ADAL) per semplificare l'autenticazione con Azure AD, sia delegando l'esperienza di autenticazione ad ADAL sia gestendo in modo esplicito le credenziali degli utenti. ADAL agevola l'autenticazione dell'utente, il recupero di un token di accesso e di un token di aggiornamento da Azure AD e quindi usa il token di accesso per inviare le richieste all'API Web.

Per un esempio di codice che illustra questo scenario usando l'autenticazione ad Azure AD, vedere [chiamata di un'API Web da parte di un'applicazione WPF client nativa](https://github.com/azureadsamples/nativeclient-dotnet).

### <a name="authenticating-a-confidential-client-application-running-on-a-web-server"></a>Autenticazione di un'applicazione client riservata in esecuzione in un server Web

In questo scenario, uno sviluppatore ha un'applicazione in esecuzione in un server che deve accedere a una risorsa remota protetta tramite Azure AD, ad esempio un'API Web. Ha una sottoscrizione di Azure, sa come richiamare il servizio downstream e sa qual è il tenant di Azure AD usato dall'API Web. Può quindi usare Azure AD Authentication Library (ADAL) per semplificare l'autenticazione con Azure AD gestendo in modo esplicito le credenziali dell'applicazione. ADAL semplifica il recupero di un token da Azure AD perché usa le credenziali client dell'applicazione e quindi il token per inviare richieste all'API Web. ADAL gestisce anche la durata del token di accesso memorizzandolo nella cache e rinnovandolo quando necessario. Per un esempio di codice che illustra questo scenario, vedere [Daemon console Application to Web API](https://github.com/AzureADSamples/Daemon-DotNet) (Accesso di un'applicazione console daemon a un'API Web).

### <a name="authenticating-a-confidential-client-application-running-on-a-server-on-behalf-of-a-user"></a>Autenticazione di un'applicazione client riservata in esecuzione in un server, per conto di un utente 

In questo scenario, uno sviluppatore ha un'applicazione in esecuzione in un server che deve accedere a una risorsa remota protetta tramite Azure AD, ad esempio un'API Web. La richiesta deve essere inviata per conto di un utente di Azure AD. Ha una sottoscrizione di Azure, sa come richiamare l'API Web downstream e sa qual è il tenant di Azure AD usato dal servizio. Una volta che l'utente ha eseguito l'autenticazione con l'applicazione Web, l'applicazione può ottenere un codice di autorizzazione per l'utente da Azure AD. L'applicazione Web può quindi usare Azure AD Authentication Library (ADAL) per ottenere un token di accesso e un token di aggiornamento per conto di un utente da Azure AD usando il codice di autorizzazione e le credenziali client associati all'applicazione. Non appena l'applicazione Web è in possesso del token di accesso, può chiamare l'API Web fino a quando il token non raggiunge la data di scadenza. Alla scadenza del token, l'applicazione Web potrà usare ADAL per ottenere un nuovo token di accesso usando il token di aggiornamento ricevuto in precedenza. Per un esempio di codice che illustra questo scenario, vedere [Native client to Web API to Web API](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof) (Accesso di un client nativo a un'API Web).

## <a name="see-also"></a>Vedere anche

- [Guida per gli sviluppatori di Azure Active Directory](active-directory-developers-guide.md)
- [Scenari di autenticazione per Azure Active Directory](active-directory-authentication-scenarios.md)
- [Esempi di codice per Azure Active Directory](active-directory-code-samples.md)

