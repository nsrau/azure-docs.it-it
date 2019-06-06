---
title: Informazioni su Microsoft Authentication Library (MSAL) | Azure
description: Microsoft Authentication Library (MSAL) consente agli sviluppatori di applicazioni di acquisire token per chiamare API Web protette. Queste API Web possono essere Microsoft Graph, altre API Microsoft, API Web di terze parti o un'API Web propria. MSAL supporta più architetture e piattaforme applicative.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/25/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4bd3e7d47b6e3083af6f388a5cd750da240a76b6
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66392893"
---
# <a name="overview-of-microsoft-authentication-library-msal"></a>Panoramica di Microsoft Authentication Library (MSAL)
Microsoft Authentication Library (MSAL) consente agli sviluppatori di acquisire [token](developer-glossary.md#security-token) dall'endpoint di Microsoft Identity Platform per accedere ad API Web protette. Queste API Web possono essere Microsoft Graph, altre API Microsoft, API Web di terze parti o un'API Web propria. MSAL è disponibile per .NET, JavaScript, Android e iOS, che supportano molte architetture e piattaforme applicative diverse.

MSAL offre molti modi per ottenere i token con un'API coerente per diverse piattaforme. L'uso di MSAL offre i vantaggi seguenti:

* Non richiede l'uso diretto del codice o delle librerie OAuth con il protocollo nell'applicazione.
* Acquisisce i token per conto di un utente o di un'applicazione (se applicabile alla piattaforma).
* Mantiene una cache dei token, che vengono aggiornati automaticamente quando stanno per scadere. Non è necessario occuparsi personalmente della scadenza dei token.
* Consente di specificare a quali destinatari si desidera far accedere l'applicazione (la propria organizzazione, diverse organizzazioni, account aziendali o degli istituti di istruzione e account personali Microsoft, identità di social networking con Azure AD B2C e utenti in cloud sovrani e nazionali).
* Consente di impostare l'applicazione da file di configurazione.
* Consente di risolvere i problemi dell'app mediante l'esposizione delle eccezioni su cui è possibile eseguire azioni, nonché dei dati di registrazione e di telemetria.

## <a name="application-types-and-scenarios"></a>Tipi di applicazioni e scenari
Con MSAL è possibile acquisire un token da diversi tipi di applicazioni: applicazioni Web, API Web, app a pagina singola (JavaScript), applicazioni native e per dispositivi mobili, daemon e applicazioni sul lato server. 

È possibile usare MSAL in numerosi scenari applicativi, inclusi i seguenti:

* [Applicazioni a pagina singola (JavaScript)](scenario-spa-overview.md) 
* [App Web per l'accesso degli utenti](scenario-web-app-sign-user-overview.md)
* [Applicazione Web per l'accesso di un utente e la chiamata di un'API Web per conto dell'utente](scenario-web-app-call-api-overview.md)
* [Protezione di un'API Web in modo che possano accedervi solo gli utenti autenticati](scenario-protected-web-api-overview.md)
* [API Web che chiama un'altra API Web downstream per conto dell'utente che ha eseguito l'accesso](scenario-web-api-call-api-overview.md)
* [Applicazione desktop che chiama un'API Web per conto dell'utente che ha eseguito l'accesso](scenario-desktop-overview.md)
* [Applicazione per dispositivi mobili che chiama un'API Web per conto dell'utente che ha eseguito l'accesso in modo interattivo](scenario-mobile-overview.md)
* [Applicazione daemon di servizio/desktop che chiama un'API Web per proprio conto](scenario-daemon-overview.md)

## <a name="languages-and-frameworks"></a>Linguaggi e framework

| Libreria | Piattaforme e framework supportati|
| --- | --- | 
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/>[MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)| .NET Framework, .NET Core, Xamarin Android, Xamarin iOS, piattaforma UWP (Universal Windows Platform)|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/>[MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)| Framework JavaScript/TypeScript come AngularJS, Ember.js o Durandal.js|
| ![MSAL per Android](media/sample-v2-code/logo_Android.png) <br/>[MSAL per Android (anteprima)](https://github.com/AzureAD/microsoft-authentication-library-for-android)|Android|
| ![MSAL per iOS](media/sample-v2-code/logo_iOS.png) <br/>[MSAL.Objective-C (anteprima)](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|iOS|

## <a name="differences-between-adal-and-msal"></a>Differenze tra ADAL e MSAL
Active Directory Authentication Library (ADAL) si integra con l'endpoint di Azure AD per sviluppatori (v1.0), dove MSAL si integra con l'endpoint di Microsoft Identity Platform (v2.0). L'endpoint v1.0 supporta gli account aziendali, ma non quelli personali. L'endpoint v2.0 riunisce gli account personali e aziendali Microsoft in un unico sistema di autenticazione. Con MSAL è anche possibile ottenere le autenticazioni per Azure AD B2C.

Per informazioni più specifiche, leggere gli articoli relativi alla [migrazione a MSAL.NET da ADAL.NET](msal-net-migration.md) e alla [migrazione a MSAL.js da ADAL.js](msal-compare-msal-js-and-adal-js.md).

            