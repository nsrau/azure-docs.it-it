---
title: Creare un'app per dispositivi mobili che chiama API Web | Azure
titleSuffix: Microsoft identity platform | Azure
description: Informazioni su come creare un'app per dispositivi mobili che chiama API Web (panoramica)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 853ab82ab40cc7ebee1c74feaf6aad87adf39ece
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94443110"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>Scenario: Applicazione per dispositivi mobili che chiama le API Web

Informazioni su come creare un'app per dispositivi mobili che chiama API Web

## <a name="getting-started"></a>Introduzione

Se non è già stato fatto, creare la prima applicazione completando una guida introduttiva:

- [Guida introduttiva: acquisire un token e chiamare Microsoft Graph API da un'app Android](./quickstart-v2-android.md)
- [Guida introduttiva: acquisire un token e chiamare Microsoft Graph API da un'app iOS](./quickstart-v2-ios.md)
- [Guida introduttiva: acquisire un token e chiamare Microsoft Graph API da un'app Novell iOS e Android](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) (GitHub)

## <a name="overview"></a>Panoramica

Un'esperienza utente personalizzata e trasparente è essenziale per le app per dispositivi mobili.  Microsoft Identity Platform consente agli sviluppatori di dispositivi mobili di creare questa esperienza per gli utenti iOS e Android. L'applicazione può accedere Azure Active Directory (Azure AD) utenti, utenti account Microsoft personali e utenti Azure AD B2C. Può inoltre acquisire token per chiamare un'API Web per loro conto. Per implementare questi flussi, si userà Microsoft Authentication Library (MSAL). MSAL implementa il flusso del [codice di autorizzazione OAuth 2.0](v2-oauth2-auth-code-flow.md)standard di settore.

![App daemon](./media/scenarios/mobile-app.svg)

Considerazioni per le app per dispositivi mobili:

- L' **esperienza utente è chiave** : consentire agli utenti di visualizzare il valore dell'app prima di richiedere l'accesso. Richiedere solo le autorizzazioni necessarie.
- **Supportare tutte le configurazioni utente** : molti utenti di business per dispositivi mobili devono rispettare i criteri di accesso condizionale e i criteri di conformità dei dispositivi. Assicurarsi di supportare questi scenari chiave.
- **Implementare Single Sign-on (SSO)** : usando MSAL e la piattaforma di identità Microsoft, è possibile abilitare Single Sign-on tramite il browser o la Microsoft Authenticator del dispositivo (e portale aziendale Intune in Android).
- **Implementare la modalità dispositivo condiviso** : consentire l'uso dell'applicazione in scenari di dispositivi condivisi, ad esempio ospedali, produzione, vendite al dettaglio e finanza. [Altre informazioni sul supporto della modalità dispositivo condiviso](msal-shared-devices.md).

## <a name="specifics"></a>Specifiche

Quando si compila un'app per dispositivi mobili nella piattaforma di identità Microsoft, tenere presenti le considerazioni seguenti:

- A seconda della piattaforma, potrebbe essere necessaria un'interazione dell'utente la prima volta che gli utenti possono accedere. IOS, ad esempio, richiede che le app mostrino l'interazione dell'utente quando usano SSO per la prima volta tramite Microsoft Authenticator (e Portale aziendale Intune in Android).
- In iOS e Android MSAL può usare un browser esterno per accedere agli utenti. Il browser esterno potrebbe essere visualizzato nella parte superiore dell'app.
- Non usare mai un segreto in un'applicazione per dispositivi mobili. In queste applicazioni, i segreti sono accessibili a tutti gli utenti.

## <a name="recommended-reading"></a>Letture consigliate

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Passaggi successivi

Passare all'articolo successivo in questo scenario, registrazione dell' [app](scenario-mobile-app-registration.md).
