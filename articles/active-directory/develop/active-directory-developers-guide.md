---
title: Azure Active Directory per sviluppatori | Microsoft Docs
description: Questo articolo offre una panoramica dell'accesso agli account Microsoft aziendali e dell'istituto di istruzione tramite Azure Active Directory.
services: active-directory
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 1baf98616eb48bd3ae45b78ab1ac31e711fa655d
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2017
---
# <a name="azure-active-directory-for-developers"></a>Azure Active Directory per sviluppatori
Azure Active Directory è un servizio cloud di gestione delle identità con il quale gli sviluppatori possono consentire l'accesso in modo sicuro a qualsiasi utente con un account aziendale o dell'istituto di istruzione supportato da Microsoft.  Questa documentazione illustra come aggiungere il supporto di Azure AD all'applicazione tramite protocolli di autenticazione standard di settore, OAuth e OpenID Connect.

| | |
| --- | --- |
|[Nozioni di base sull'autenticazione](active-directory-authentication-scenarios.md) | Introduzione all'autenticazione con Azure AD |
|[Tipi di applicazioni](active-directory-authentication-scenarios.md#application-types-and-scenarios) | Panoramica degli scenari di autenticazione supportati da Azure AD |                                
                                                                              
## <a name="get-started"></a>Introduzione
Queste configurazioni guidate illustrano l'uso delle librerie di autenticazione di Microsoft per consentire l'accesso agli utenti di Azure Active Directory.

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![App per dispositivi mobili e desktop](./media/active-directory-developers-guide/NativeApp_Icon.png)<br />App per dispositivi mobili e desktop</center> | [Panoramica](active-directory-authentication-scenarios.md#native-application-to-web-api)<br /><br />[iOS](active-directory-devquickstarts-ios.md)<br /><br />[Android](active-directory-devquickstarts-android.md) | [.NET](active-directory-devquickstarts-dotnet.md)<br /><br />[Windows](active-directory-devquickstarts-windowsstore.md)<br /><br />[Xamarin](active-directory-devquickstarts-xamarin.md) | [Cordova](active-directory-devquickstarts-cordova.md)<br /><br />[OAuth 2.0](active-directory-protocols-oauth-code.md) |
| <center>![App Web](./media/active-directory-developers-guide/Web_app.png)<br />App Web</center> | [Panoramica](active-directory-authentication-scenarios.md#web-browser-to-web-application)<br /><br />[ASP.NET](active-directory-devquickstarts-webapp-dotnet.md)<br /><br />[Java](active-directory-devquickstarts-webapp-java.md) | [NodeJS](active-directory-devquickstarts-openidconnect-nodejs.md)<br /><br />[OpenID Connect 1.0](active-directory-protocols-openid-connect-code.md) |  |
| <center>![App a singola pagina](./media/active-directory-developers-guide/SPA.png)<br />App a singola pagina</center> | [Panoramica](active-directory-authentication-scenarios.md#single-page-application-spa)<br /><br />[AngularJS](active-directory-devquickstarts-angular.md)<br /><br />[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |  |  |
| <center>![API Web](./media/active-directory-developers-guide/Web_API.png)<br />API Web</center> | [Panoramica](active-directory-authentication-scenarios.md#web-application-to-web-api)<br /><br />[ASP.NET](active-directory-devquickstarts-webapi-dotnet.md)<br /><br />[NodeJS](active-directory-devquickstarts-webapi-nodejs.md) | &nbsp; |
| <center>![Da servizio a servizio](./media/active-directory-developers-guide/Service_App.png)<br />Da servizio a servizio</center> | [Panoramica](active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api)<br /><br />[.NET](active-directory-code-samples.md#server-or-daemon-application-to-web-api)<br /><br />[Credenziali client OAuth 2.0](active-directory-protocols-oauth-service-to-service.md) |  |

## <a name="guides"></a>Guide
Questi articoli descrivono come eseguire attività comuni con Azure Active Directory.

|                                                                           |  |
|---------------------------------------------------------------------------| --- |
|[Registrazione delle app](active-directory-integrating-applications.md)           | Come registrare un'app in Azure AD |
|[App multi-tenant](active-directory-devhowto-multi-tenant-overview.md)    | Informazioni su come consentire l'accesso a qualsiasi account aziendale Microsoft |
|[OAuth e OpenID Connect](active-directory-protocols-openid-connect-code.md)| Come consentire l'accesso agli utenti e chiamare le API Web con i protocolli di autenticazione moderni di Microsoft |
|[Altre guide](active-directory-developers-guide-index.md#guides)        |     |

## <a name="reference"></a>Riferimento
Questi articoli offrono informazioni dettagliate su API, messaggi di protocollo e termini usati in Azure Active Directory.

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [Librerie di autenticazione (ADAL)](active-directory-authentication-libraries.md)   | Panoramica delle librerie e degli SDK messi a disposizione da Azure AD |
| [Esempi di codice](active-directory-code-samples.md)                                  | Elenco di tutti gli esempi di codice di Azure AD |
| [Glossario](active-directory-dev-glossary.md)                                      | Terminologia e definizioni dei termini usati nella documentazione |
| [Altro materiale di riferimento](active-directory-developers-guide-index.md#reference)|     |


> [!NOTE]
> Se è necessario l'accesso ad account personali Microsoft, è consigliabile usare l'[endpoint di Azure AD 2.0 ](active-directory-appmodel-v2-overview.md).  L'endpoint Azure AD 2.0 è l'unificazione degli account personali e aziendali di Microsoft (Azure AD) in un solo sistema di autenticazione.


[!INCLUDE  [Help and Support Options](../../../includes/active-directory-develop-help-support-include.md)]