---
title: Crea un'app per dispositivi mobili che chiami le API Web Azure
titleSuffix: Microsoft identity platform | Azure
description: Informazioni su come creare un'app per dispositivi mobili che chiama le API Web (panoramica)
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
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: acd44298e401aabaef03f5ddd84f37f32a3d8bcd
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546084"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>Scenario: applicazione mobile che chiama le API Web

Scopri come creare un'app per dispositivi mobili che chiama le API Web.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Introduzione

Crea la tua prima applicazione mobile e prova una guida introduttiva.

> [!div class="nextstepaction"]
> [Guida introduttiva: Acquisire un token e chiamare l'API Microsoft Graph da un'app Android](./quickstart-v2-android.md)
>
> [Guida introduttiva: Acquisire un token e chiamare l'API Microsoft Graph da un'app iOS](./quickstart-v2-ios.md)
>
> [Guida introduttiva: Acquisire un token e chiamare l'API Microsoft Graph da un'app Xamarin per iOS e Android](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)

## <a name="overview"></a>Panoramica

Un'esperienza utente personalizzata e senza soluzione di continuità è essenziale per le app per dispositivi mobili.  La piattaforma di identità Microsoft consente agli sviluppatori di dispositivi mobili di creare tale esperienza per gli utenti iOS e Android. L'applicazione può accedere agli utenti di Azure Active Directory (Azure AD), agli utenti di account Microsoft personali e agli utenti B2C di Azure AD. Può anche acquisire token per chiamare un'API Web per loro conto. Per implementare questi flussi, useremo Microsoft Authentication Library (MSAL). MSAL implementa il flusso del codice di [autorizzazione OAuth2.0](v2-oauth2-auth-code-flow.md)standard del settore.

![App daemon](./media/scenarios/mobile-app.svg)

Considerazioni per le app per dispositivi mobili:

- **L'esperienza utente è fondamentale:** consente agli utenti di visualizzare il valore dell'app prima di chiedere l'accesso. Richiedere solo le autorizzazioni necessarie.
- **Supporta tutte le configurazioni utente:** molti utenti aziendali mobili devono rispettare i criteri di accesso condizionale e i criteri di conformità dei dispositivi. Assicurarsi di supportare questi scenari chiave.
- **Implementare Single Sign-On (SSO):** tramite MSAL e la piattaforma di identità Microsoft, è possibile abilitare l'accesso Single Sign-On tramite il browser del dispositivo o Microsoft Authenticator (e Intune Company Portal in Android).
- **Implementa modalità dispositivo condiviso:** abilitare l'applicazione da utilizzare in scenari di dispositivi condivisi, ad esempio ospedali, produzione, vendita al dettaglio e finanza. [Ulteriori informazioni sul supporto della modalità dispositivo condiviso](msal-shared-devices.md).

## <a name="specifics"></a>Specifiche

Quando crei un'app per dispositivi mobili sulla piattaforma Microsoft identity, tieni presenti le considerazioni seguenti:Keep in mind the following considerations when you build a mobile app on Microsoft identity platform:

- A seconda della piattaforma, potrebbe essere necessaria un'interazione dell'utente al primo accesso degli utenti. Ad esempio, iOS richiede che le app mostrino l'interazione dell'utente quando usano SSO per la prima volta tramite Microsoft Authenticator (e Intune Company Portal in Android).
- In iOS e Android, MSAL potrebbe utilizzare un browser esterno per accedere agli utenti. Il browser esterno potrebbe essere visualizzato nella parte superiore dell'app.
- Non utilizzare mai un segreto in un'applicazione mobile. In queste applicazioni, i segreti sono accessibili a tutti gli utenti.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Registrazione delle app](scenario-mobile-app-registration.md)
