---
title: App per dispositivi mobili che chiama web API - Panoramica | Piattaforma delle identità Microsoft
description: Informazioni su come compilare un'app per dispositivi mobili di chiamate di web API (panoramica)
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
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
ms.openlocfilehash: 0f007ad1d5bf99136328ec5706f7ccbb5f6593c8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111219"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>Scenario: Applicazioni per dispositivi mobili che chiama l'API web

Scopri tutto che è necessario sapere per creare un'app per dispositivi mobili che chiama le API web.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Introduzione

Creare la prima applicazione per dispositivi mobili e provare una Guida introduttiva.

> [!div class="nextstepaction"]
> [Avvio rapido: Acquisire un token e chiamare API Microsoft Graph da un'app per Android](./quickstart-v2-android.md)
>
> [Avvio rapido: Acquisire un token e chiamare API Microsoft Graph da un'app per iOS](./quickstart-v2-ios.md)
>
> [Avvio rapido: Acquisire un token e chiamare l'API Microsoft Graph da un'app Android e Xamarin iOS](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)

## <a name="overview"></a>Panoramica

Un'esperienza utente trasparente personalizzato è essenziale per le App per dispositivi mobili.  Piattaforma delle identità di Microsoft consente agli sviluppatori per dispositivi mobili creare tale esperienza per gli utenti di Android e iOS. L'applicazione possa accedere gli utenti di Azure Active Directory (Azure AD), gli utenti con account Microsoft personali e gli utenti di Azure AD B2C e acquisire i token per chiamare un'API web per loro conto. Per implementare questi flussi, si userà Microsoft Authentication Library (MSAL), che implementa lo standard di settore [flusso del codice di autorizzazione OAuth 2.0](v2-oauth2-auth-code-flow.md).

![App daemon](./media/scenarios/mobile-app.svg)

Considerazioni per le App per dispositivi mobili:

- **Esperienza utente è essenziale**: Consentire agli utenti di visualizzare il valore dell'app prima di richiedere per l'accesso e richiedere solo le autorizzazioni necessarie.
- **Supporta tutte le configurazioni utente**: Numero di utenti aziendali per dispositivi mobili è sotto l'accesso condizionale e criteri di conformità dei dispositivi. Assicurarsi che supportare questi scenari chiave.
- **Implementare il single sign-on (SSO)** : MSAL e piattaforma delle identità Microsoft semplificano l'abilitazione di single sign-on tramite il browser del dispositivo o il Microsoft Authenticator e portale aziendale di Intune in Android.

## <a name="specifics"></a>Specifiche

Tenere presente queste considerazioni quando si compila un'app per dispositivi mobili su piattaforma delle identità Microsoft:

- A seconda della piattaforma, alcune interazioni dell'utente potrebbero essere necessari al primo accesso degli utenti. Ad esempio, iOS richiede che le App per mostrare l'interazione dell'utente quando si utilizza SSO la prima volta attraverso Microsoft Authenticator (e il portale aziendale di Intune in Android).
- In iOS e Android, MSAL potrebbe usare un browser esterno (che potrebbe essere visualizzata in cima all'app) per consentire agli utenti. È possibile personalizzare la configurazione per usare invece visualizzazioni nell'app Web.
- Non usare mai un segreto in un'applicazione per dispositivi mobili. Sarà accessibile a tutti gli utenti.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Registrazione delle app](scenario-mobile-app-registration.md)
