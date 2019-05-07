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
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb0acd1534bab11eb57a7aa0e695f192b5999ed2
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076496"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>Scenario: Applicazioni per dispositivi mobili che chiama l'API web

Scopri tutto quello che dovete compilare un'app per dispositivi mobili che chiama le API web.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Introduzione

Creare la prima applicazione per dispositivi mobili e provare una Guida introduttiva.

> [!div class="nextstepaction"]
> [Guida introduttiva: Acquisire un token e chiamare API Microsoft Graph da un'app per Android](./quickstart-v2-android.md)
>
> [Guida introduttiva: Acquisire un token e chiamare API Microsoft Graph da un'app per iOS](./quickstart-v2-ios.md)
>
> [Guida introduttiva: Acquisire un token e chiamare l'API Microsoft Graph da un'app Android e Xamarin iOS](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)

## <a name="overview"></a>Panoramica

Quando si compila un'app per dispositivi mobili, un'esperienza utente finale facile personalizzato è essenziale.  Piattaforma delle identità di Microsoft consente agli sviluppatori per dispositivi mobili proprio questa per iOS e Android degli utenti. L'applicazione può accedere ad Azure AD, account Microsoft personale e gli utenti di Azure AD B2C e acquisire i token per chiamare un'API web per loro conto. Per implementare questi flussi, si userà Microsoft Authentication Library (MSAL) che implementa lo standard di settore [flusso del codice di autorizzazione OAuth 2.0](v2-oauth2-auth-code-flow.md).

![App daemon](./media/scenarios/mobile-app.svg)

Considerazioni sulle app per dispositivi mobili:

- ***Esperienza utente è essenziale***: Consentire agli utenti di visualizzare il valore dell'app prima di richiedere per l'accesso e richiedere solo le autorizzazioni necessarie.
- ***Supporta tutte le configurazioni utente***: Numero di utenti aziendali per dispositivi mobili è sotto l'accesso condizionale e criteri di conformità dei dispositivi. Assicurarsi che supportare questi scenari chiave.
- ***Implementare il single sign-on (SSO)***: MSAL e piattaforma delle identità Microsoft semplificano l'abilitazione di single sign-on tramite il browser del dispositivo o il Microsoft Authenticator e portale aziendale di Intune in Android.

## <a name="specifics"></a>Specifiche

Quando si compila un'app per dispositivi mobili su piattaforma delle identità Microsoft, l'esperienza end-to-end ha introdotto alcune considerazioni:

- A seconda della piattaforma, l'accesso senza alcuna interazione potrebbe non essere possibile sul primo accesso. iOS, ad esempio, richiede che le App per mostrare l'interazione dell'utente durante il recupero di tempo prima l'accesso SSO tramite Microsoft Authenticator (e il portale aziendale di Intune in Android).
- In iOS e Android, MSAL può usare un browser esterno (che può essere visualizzata in cima all'app) per consentire agli utenti. Può essere personalizzato per usare invece visualizzazioni nell'app Web.
- Non usare mai un segreto in un'applicazione per dispositivi mobili, sarà accessibile a tutti gli utenti.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Registrazione delle app](scenario-mobile-app-registration.md)
