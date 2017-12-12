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
ms.openlocfilehash: 1614e37693adad268965e586f919a842c8f42e6a
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2017
---
# <a name="azure-active-directory-for-developers"></a>Azure Active Directory per sviluppatori
Azure Active Directory (Azure AD) è un servizio cloud di gestione delle identità con il quale gli sviluppatori possono consentire l'accesso sicuro agli utenti con un account Microsoft aziendale o dell'istituto di istruzione. Questa documentazione illustra come aggiungere il supporto di Azure AD all'applicazione tramite protocolli standard di settore: OAuth e OpenID Connect.

| | |
| --- | --- |
|[Nozioni di base sull'autenticazione](active-directory-authentication-scenarios.md) | Introduzione all'autenticazione con Azure AD. |
|[Tipi di applicazioni](active-directory-authentication-scenarios.md#application-types-and-scenarios) | Panoramica degli scenari di autenticazione supportati da Azure AD. |                                
                                                                              
## <a name="get-started"></a>Introduzione
Le configurazioni guidate seguenti illustrano l'uso delle librerie di autenticazione Microsoft per l'accesso degli utenti di Azure AD.

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![App per dispositivi mobili e desktop](./media/active-directory-developers-guide/NativeApp_Icon.png)<br />App per dispositivi mobili e desktop</center> | [Panoramica](active-directory-authentication-scenarios.md#native-application-to-web-api)<br /><br />[iOS](active-directory-devquickstarts-ios.md)<br /><br />[Android](active-directory-devquickstarts-android.md) | [.NET](active-directory-devquickstarts-dotnet.md)<br /><br />[Windows](active-directory-devquickstarts-windowsstore.md)<br /><br />[Xamarin](active-directory-devquickstarts-xamarin.md) | [Cordova](active-directory-devquickstarts-cordova.md)<br /><br />[OAuth 2.0](active-directory-protocols-oauth-code.md) |
| <center>![App Web](./media/active-directory-developers-guide/Web_app.png)<br />App Web</center> | [Panoramica](active-directory-authentication-scenarios.md#web-browser-to-web-application)<br /><br />[ASP.NET](active-directory-devquickstarts-webapp-dotnet.md)<br /><br />[Java](active-directory-devquickstarts-webapp-java.md) | [Node.js](active-directory-devquickstarts-openidconnect-nodejs.md)<br /><br />[OpenID Connect 1.0](active-directory-protocols-openid-connect-code.md) |  |
| <center>![App a singola pagina](./media/active-directory-developers-guide/SPA.png)<br />App a singola pagina</center> | [Panoramica](active-directory-authentication-scenarios.md#single-page-application-spa)<br /><br />[AngularJS](active-directory-devquickstarts-angular.md)<br /><br />[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |  |  |
| <center>![API Web](./media/active-directory-developers-guide/Web_API.png)<br />API Web</center> | [Panoramica](active-directory-authentication-scenarios.md#web-application-to-web-api)<br /><br />[ASP.NET](active-directory-devquickstarts-webapi-dotnet.md)<br /><br />[Node.js](active-directory-devquickstarts-webapi-nodejs.md) | &nbsp; |
| <center>![Da servizio a servizio](./media/active-directory-developers-guide/Service_App.png)<br />Da servizio a servizio</center> | [Panoramica](active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api)<br /><br />[.NET](active-directory-code-samples.md#server-or-daemon-application-to-web-api)<br /><br />[Credenziali client OAuth 2.0](active-directory-protocols-oauth-service-to-service.md) |  |

## <a name="how-to-guides"></a>Guide alle procedure
Le guide seguenti illustrano come eseguire attività comuni con Azure AD.

|                                                                           |  |
|---------------------------------------------------------------------------| --- |
|[Registrazione dell'applicazione](active-directory-integrating-applications.md)           | Come registrare un'applicazione in Azure AD. |
|[Applicazioni multi-tenant](active-directory-devhowto-multi-tenant-overview.md)    | Come accedere a un account aziendale Microsoft. |
|[Protocolli OAuth e OpenID Connect](active-directory-protocols-openid-connect-code.md)| Come consentire l'accesso agli utenti e chiamare le API Web usando i protocolli di autenticazione Microsoft. |
|[Guide aggiuntive](active-directory-developers-guide-index.md#guides)        |  Elenco di guide disponibili per Azure AD.   |

## <a name="reference-topics"></a>Argomenti di riferimento
Gli articoli seguenti forniscono informazioni dettagliate su API, messaggi di protocollo e termini usati in Azure AD.

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [Librerie di autenticazione (ADAL)](active-directory-authentication-libraries.md)   | Panoramica delle librerie e degli SDK forniti da Azure AD. |
| [Esempi di codice](active-directory-code-samples.md)                                  | Elenco di tutti gli esempi di codice di Azure AD. |
| [Glossario](active-directory-dev-glossary.md)                                      | Terminologia e definizioni dei termini usati nella documentazione. |
| [Argomenti di riferimento aggiuntivi](active-directory-developers-guide-index.md#reference)| Elenco di argomenti di riferimento disponibili per Azure AD.   |


> [!NOTE]
> Se è necessario accedere ad account personali Microsoft, è consigliabile usare l'[endpoint di Azure AD 2.0](active-directory-appmodel-v2-overview.md). L'endpoint di Azure AD 2.0 riunisce account personali e aziendali di Microsoft (di Azure AD) in un unico sistema di autenticazione.


[!INCLUDE  [Help and support](../../../includes/active-directory-develop-help-support-include.md)]