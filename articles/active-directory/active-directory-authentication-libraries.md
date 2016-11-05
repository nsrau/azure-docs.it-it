---
title: Azure Active Directory Authentication Library | Microsoft Docs
description: Azure AD Authentication Library (ADAL) consente agli sviluppatori di applicazioni client di autenticare facilmente gli utenti in Active Directory locale o nel cloud e quindi di ottenere token di accesso per proteggere le chiamate API.
services: active-directory
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: mbaldwin

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/16/2016
ms.author: mbaldwin

---
# Azure Active Directory Authentication Library
Azure AD Authentication Library (ADAL) consente agli sviluppatori di applicazioni client di autenticare facilmente gli utenti in Active Directory locale o nel cloud e quindi di ottenere token di accesso per proteggere le chiamate API. Azure AD Authentication Library offre numerose funzionalità che semplificano l'integrazione dell'autenticazione nelle applicazioni da parte degli sviluppatori, ad esempio il supporto asincrono, la cache di token configurabile per l'archiviazione dei token di accesso e dei token di aggiornamento e l'aggiornamento automatico dei token alla scadenza dei token di accesso, i token di aggiornamento e altre ancora. Poiché Azure AD Authentication Library gestisce la maggior parte della complessità, gli sviluppatori possono concentrarsi sulla logica di business nell'applicazione e di proteggere le risorse pur non avendo competenze elevate in termini di sicurezza.

Azure AD Authentication Library è disponibile per una vasta gamma di piattaforme.

| Piattaforma | Nome del pacchetto | Client/Server | Scaricare | Codice sorgente | Esempi e documentazione |
| --- | --- | --- | --- | --- | --- |
| .NET Client, Windows Store, Windows Phone (8.1) |Active Directory Authentication Library (ADAL) per .NET |Client |[Microsoft.IdentityModel.Clients.ActiveDirectory (NuGet)](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[ADAL per .NET (GitHub)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) |[Documentazione](https://msdn.microsoft.com/library/azure/mt417579.aspx) |
| JavaScript |Active Directory Authentication Library (ADAL) per JavaScript |Client |[ADAL per JavaScript (GitHub)](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[ADAL per JavaScript (GitHub)](https://github.com/AzureAD/azure-activedirectory-library-for-js) |Esempio: [SinglePageApp-DotNet (GitHub)](https://github.com/AzureADSamples/SinglePageApp-DotNet) |
| OS X, iOS |Active Directory Authentication Library (ADAL) per Objective-C |Client |[ADAL per Objective-C (CocoaPods)](https://cocoapods.org/?q=adal%20io) |[ADAL per Objective-C (GitHub)](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |Esempio: [NativeClient-iOS (GitHub)](https://github.com/AzureADSamples/NativeClient-iOS) |
| Android |Active Directory Authentication Library (ADAL) per Android |Client |[ADAL per Android (The Central Repository)](http://search.maven.org/remotecontent?filepath=com/microsoft/aad/adal/) |[ADAL per Android (GitHub)](https://github.com/AzureAD/azure-activedirectory-library-for-android) |Esempio: [NativeClient-Android (GitHub)](https://github.com/AzureADSamples/NativeClient-Android) |
| Node.js |Active Directory Authentication Library (ADAL) per Node.js |Client |[ADAL per Node.js (npm)](https://www.npmjs.com/package/adal-node) |[ADAL per Node.js (GitHub)](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) |Esempio: [WebAPI-Nodejs (GitHub)](https://github.com/AzureADSamples/WebAPI-Nodejs) |
| Node.js |Middleware di autenticazione Passport di Azure Active Directory per Node |Client |[Azure Active Directory Passport per Node.js (npm)](https://www.npmjs.com/package/passport-azure-ad) |[Azure Active Directory per Node.js (Github)](https://github.com/AzureAD/passport-azure-ad) | |
| Java |Active Directory Authentication Library (ADAL) per Java |Client |[ADAL per Java (GitHub)](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[ADAL per Java (GitHub)](https://github.com/AzureAD/azure-activedirectory-library-for-java) | |
| .NET |Estensioni del protocollo di identità per Microsoft .NET Framework 4.5 |Server |[Microsoft.IdentityModel.Protocol.Extensions (NuGet)](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[Estensioni del modello di identità di Azure AD per .NET (GitHub)](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | |
| .NET |Gestore del token Web JSON per Microsoft .Net Framework 4.5 |Server |[System.IdentityModel.Tokens.Jwt (NuGet)](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[Estensioni del modello di identità di Azure AD per .NET (GitHub)](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | |
| .NET |Middleware OWIN che consente a un'applicazione di usare la tecnologia di Microsoft per l'autenticazione. |Server |[Microsoft.Owin.Security.ActiveDirectory (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[OWIN (CodePlex)](http://katanaproject.codeplex.com) | |
| .NET |Middleware OWIN che consente a un'applicazione di usare OpenIDConnect per l'autenticazione. |Server |[Microsoft.Owin.Security.OpenIdConnect (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[OWIN (CodePlex)](http://katanaproject.codeplex.com) |Esempio: [WebApp-OpenIDConnecty-DotNet (GitHub)](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) |
| .NET |Middleware OWIN che consente a un'applicazione di usare WS-Federation per l'autenticazione. |Server |[Microsoft.Owin.Security.WsFederation (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[OWIN (CodePlex)](http://katanaproject.codeplex.com) |Esempio: [WebApp-WSFederation-DotNet (GitHub)](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) |

## Scenari
Di seguito sono illustrati i tre scenari comuni in cui è possibile usare Azure AD Authentication Library per l'autenticazione.

### Autenticazione degli utenti di un'applicazione client in una risorsa remota
In questo scenario, uno sviluppatore ha un client, ad esempio un'applicazione WPF, che deve accedere a una risorsa remota protetta tramite Azure AD, ad esempio un'API Web. Ha una sottoscrizione di Azure, sa come richiamare l'API Web downstream e sa qual è il tenant di Azure AD usato dall'API Web. Può quindi usare Azure AD Authentication Library (ADAL) per semplificare l'autenticazione con Azure AD, sia delegando l'esperienza di autenticazione ad ADAL sia gestendo in modo esplicito le credenziali degli utenti. ADAL agevola l'autenticazione dell'utente, il recupero di un token di accesso e di un token di aggiornamento da Azure AD e quindi usa il token di accesso per inviare le richieste all'API Web.

Per un esempio di codice che illustra questo scenario usando l'autenticazione ad Azure AD, vedere [chiamata di un'API Web da parte di un'applicazione WPF client nativa](https://github.com/azureadsamples/nativeclient-dotnet).

### Autenticazione di un'applicazione server in una risorsa remota
In questo scenario, uno sviluppatore ha un'applicazione in esecuzione in un server che deve accedere a una risorsa remota protetta tramite Azure AD, ad esempio un'API Web. Ha una sottoscrizione di Azure, sa come richiamare il servizio downstream e sa qual è il tenant di Azure AD usato dall'API Web. Può quindi usare Azure AD Authentication Library (ADAL) per semplificare l'autenticazione con Azure AD gestendo in modo esplicito le credenziali dell'applicazione. ADAL semplifica il recupero di un token da Azure AD perché usa le credenziali client dell'applicazione e quindi il token per inviare richieste all'API Web. ADAL gestisce anche la durata del token di accesso memorizzandolo nella cache e rinnovandolo quando necessario. Per un esempio di codice che illustra questo scenario, vedere [chiamata di un'API Web da parte di un'applicazione console](https://github.com/AzureADSamples/Daemon-DotNet).

### Autenticazione di un'applicazione server per conto di un utente per accedere a una risorsa remota
In questo scenario, uno sviluppatore ha un'applicazione in esecuzione in un server che deve accedere a una risorsa remota protetta tramite Azure AD, ad esempio un'API Web. La richiesta deve essere inviata per conto di un utente in Azure AD. Ha una sottoscrizione di Azure, sa come richiamare l'API Web downstream e sa qual è il tenant di Azure AD usato dal servizio. Una volta che l'utente ha eseguito l'autenticazione con l'applicazione Web, l'applicazione può ottenere un codice di autorizzazione per l'utente da Azure AD. L'applicazione Web può quindi usare Azure AD Authentication Library (ADAL) per ottenere un token di accesso e un token di aggiornamento per conto di un utente da Azure AD usando il codice di autorizzazione e le credenziali client associati all'applicazione. Non appena l'applicazione Web è in possesso del token di accesso, può chiamare l'API Web fino a quando il token non raggiunge la data di scadenza. Alla scadenza del token, l'applicazione Web potrà usare ADAL per ottenere un nuovo token di accesso usando il token di aggiornamento ricevuto in precedenza.

## Vedere anche
[Guida per gli sviluppatori di Azure Active Directory](active-directory-developers-guide.md)

[Scenari di autenticazione per Azure Active Directory](active-directory-authentication-scenarios.md)

[Esempi di codice per Azure Active Directory](active-directory-code-samples.md)

<!---HONumber=AcomDC_0921_2016-->