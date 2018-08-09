---
title: Azure Active Directory per sviluppatori | Microsoft Docs
description: Questo articolo offre una panoramica dell'accesso agli account Microsoft aziendali e dell'istituto di istruzione tramite Azure Active Directory.
services: active-directory
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/30/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 666a677943811af05cd3403eab4887271c1f87b3
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2018
ms.locfileid: "39591211"
---
# <a name="azure-active-directory-for-developers"></a>Azure Active Directory per sviluppatori

Azure Active Directory (Azure AD) è un servizio cloud di gestione delle identità che consente agli sviluppatori di creare app che permettono l'accesso sicuro agli utenti con un account Microsoft aziendale o dell'istituto di istruzione. Azure AD supporta gli sviluppatori che creano app line-of-business (LOB) a singolo tenant e gli sviluppatori che vogliono sviluppare app multi-tenant. Oltre all'accesso di base, Azure AD consente alle app di chiamare anche API Microsoft come [Microsoft Graph](https://developer.microsoft.com/en-us/graph/docs/concepts/overview) e API personalizzate create sulla piattaforma di Azure AD. Questa documentazione illustra come aggiungere il supporto di Azure AD all'applicazione tramite protocolli standard di settore, come OAuth2.0 e OpenID Connect.

> [!NOTE]
> La maggior parte del contenuto di questa pagina è incentrato sull'endpoint di Azure AD v1.0, che supporta solo account Microsoft aziendali o dell'istituto di istruzione. Se si vuole consentire l'accesso ad account Microsoft consumer o personali, vedere le informazioni sull'[endpoint di Azure AD v2.0](active-directory-appmodel-v2-overview.md). L'endpoint di Azure AD v2.0 offre un'esperienza unificata per gli sviluppatori per app che vogliono consentire l'accesso degli utenti con account di Azure AD (aziendali e dell'istituto di istruzione) e con account Microsoft personali.

| | |
| --- | --- |
|[Nozioni di base sull'autenticazione](authentication-scenarios.md) | Introduzione all'autenticazione con Azure AD. |
|[Tipi di applicazioni](authentication-scenarios.md#application-types-and-scenarios) | Panoramica degli scenari di autenticazione supportati da Azure AD. |      
| | |

## <a name="get-started"></a>Attività iniziali
Le configurazioni guidate seguenti illustrano in modo dettagliato la creazione di un'app sulla propria piattaforma preferita tramite Azure AD Authentication Library (ADAL) SDK. Per informazioni sull'uso di Microsoft Authentication Library (MSAL), vedere la documentazione sull'[endpoint di Azure AD v2.0](active-directory-appmodel-v2-overview.md).

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![App per dispositivi mobili e desktop](./media/azure-ad-developers-guide/NativeApp_Icon.png)<br />App per dispositivi mobili e desktop</center> | [Overview](authentication-scenarios.md#native-application-to-web-api)<br /><br />[iOS](quickstart-v1-ios.md)<br /><br />[Android](quickstart-v1-android.md) | [.NET (Windows Presentation Foundation)](quickstart-v1-dotnet.md)<br /><br />[Xamarin](quickstart-v1-xamarin.md) |
| <center>![App Web](./media/azure-ad-developers-guide/Web_app.png)<br />App Web</center> | [Overview](authentication-scenarios.md#web-browser-to-web-application)<br /><br />[ASP.NET](quickstart-v1-aspnet-webapp.md)<br /><br />[Java](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect) | [Python](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)<br/><br/> [Node.JS](quickstart-v1-openid-connect-code.md) |
| <center>![App a singola pagina](./media/azure-ad-developers-guide/SPA.png)<br />App a singola pagina</center> | [Overview](authentication-scenarios.md#single-page-application-spa)<br /><br />[AngularJS](quickstart-v1-angularjs-spa.md)<br /><br />[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |  |
| <center>![API Web](./media/azure-ad-developers-guide/Web_API.png)<br />API Web</center> | [Overview](authentication-scenarios.md#web-application-to-web-api)<br /><br />[ASP.NET](quickstart-v1-dotnet-webapi.md)<br /><br />[Node.JS](quickstart-v1-nodejs-webapi.md) | &nbsp; |
| <center>![Da servizio a servizio](./media/azure-ad-developers-guide/Service_App.png)<br />Da servizio a servizio</center> | [Overview](authentication-scenarios.md#daemon-or-server-application-to-web-api)<br /><br />[.NET](sample-v1-code.md#daemon-applications-accessing-web-apis-with-the-applications-identity)|  |
|  |  |  |  |  |

## <a name="how-to-guides"></a>Guide alle procedure
Queste guide illustrano in modo dettagliato alcune delle attività più comuni in Azure AD.

|                                                                           |  |
|---------------------------------------------------------------------------| --- |
|[Registrazione dell'applicazione](quickstart-v1-integrate-apps-with-azure-ad.md)           | Come registrare un'applicazione in Azure AD. |
|[Applicazioni multi-tenant](howto-convert-app-to-be-multi-tenant.md)    | Come accedere a un account aziendale Microsoft. |
|[Protocolli OAuth e OpenID Connect](v1-protocols-openid-connect-code.md)| Come consentire l'accesso agli utenti e chiamare le API Web usando i protocolli di autenticazione Microsoft. |
|  |  |

## <a name="reference-topics"></a>Argomenti di riferimento
Gli articoli seguenti forniscono informazioni dettagliate su API, messaggi di protocollo e termini usati in Azure AD.

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [Librerie di autenticazione (ADAL)](active-directory-authentication-libraries.md)   | Panoramica delle librerie e degli SDK forniti da Azure AD. |
| [Esempi di codice](sample-v1-code.md)                                  | Elenco di tutti gli esempi di codice di Azure AD. |
| [Glossario](developer-glossary.md)                                      | Terminologia e definizioni dei termini usati nella documentazione. |
|  |  |


[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
