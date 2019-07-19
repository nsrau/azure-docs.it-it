---
title: App per dispositivi mobili che chiama API Web-Panoramica | Piattaforma di identità Microsoft
description: Informazioni su come creare un'app per dispositivi mobili che chiama API Web (panoramica)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfc5e12bf1633c32a61e03c7df6d6c0320263d6e
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325324"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>Scenario: Applicazione per dispositivi mobili che chiama API Web

Scopri tutte le informazioni necessarie per creare un'app per dispositivi mobili che chiama le API Web.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Introduzione

Creare la prima applicazione per dispositivi mobili e provare una guida introduttiva.

> [!div class="nextstepaction"]
> [Avvio rapido: Acquisire un token e chiamare Microsoft Graph API da un'app Android](./quickstart-v2-android.md)
>
> [Avvio rapido: Acquisire un token e chiamare Microsoft Graph API da un'app iOS](./quickstart-v2-ios.md)
>
> [Avvio rapido: Acquisire un token e chiamare Microsoft Graph API da un'app Novell iOS & Android](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)

## <a name="overview"></a>Panoramica

Un'esperienza utente personalizzata e trasparente è essenziale per le app per dispositivi mobili.  Microsoft Identity Platform consente agli sviluppatori di dispositivi mobili di creare questa esperienza per gli utenti iOS e Android. L'applicazione può accedere Azure Active Directory (Azure AD) utenti, utenti account Microsoft personali e utenti Azure AD B2C e acquisire i token per chiamare un'API Web per loro conto. Per implementare questi flussi, si userà Microsoft Authentication Library (MSAL), che implementa il [flusso del codice di autorizzazione OAuth 2.0](v2-oauth2-auth-code-flow.md)standard di settore.

![App daemon](./media/scenarios/mobile-app.svg)

Considerazioni per le app per dispositivi mobili:

- L' **esperienza utente è chiave**: Consentire agli utenti di visualizzare il valore dell'app prima di richiedere l'accesso e richiedere solo le autorizzazioni necessarie.
- **Supporto per tutte le configurazioni utente**: Molti utenti di business per dispositivi mobili sono sotto l'accesso condizionale e i criteri di conformità dei dispositivi. Assicurarsi di supportare questi scenari chiave.
- **Implementare l'accesso Single Sign-on (SSO)** : MSAL e la piattaforma di identità Microsoft rendono semplice l'abilitazione dell'accesso Single Sign-on tramite il browser del dispositivo o la Microsoft Authenticator (e Portale aziendale Intune in Android).

## <a name="specifics"></a>Specifiche

Tenere presenti queste considerazioni quando si compila un'app per dispositivi mobili nella piattaforma di identità Microsoft:

- A seconda della piattaforma, potrebbe essere necessaria un'interazione dell'utente la prima volta che gli utenti possono accedere. IOS, ad esempio, richiede che le app mostrino l'interazione dell'utente quando si usa SSO la prima volta tramite Microsoft Authenticator (e Portale aziendale Intune in Android).
- In iOS e Android, MSAL può usare un browser esterno (che potrebbe essere visualizzato sopra l'app) per accedere agli utenti. In alternativa, è possibile personalizzare la configurazione per l'uso di WebView in-app.
- Non usare mai un segreto in un'applicazione per dispositivi mobili. Sarà accessibile a tutti gli utenti.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Registrazione delle app](scenario-mobile-app-registration.md)
