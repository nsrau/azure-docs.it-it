---
title: Creare un'app desktop che chiama le API Web | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come creare un'app desktop che chiama le API Web (panoramica)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: a6cbb574245c7b11d2437693d60fd952becedba3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91257555"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>Scenario: App desktop che chiama le API Web

Tutte le informazioni su come per creare un'app desktop che chiama le API Web.

## <a name="get-started"></a>Operazioni preliminari

Se non è già stato fatto, creare la prima applicazione completando una guida introduttiva:

- [Avvio rapido: Acquisire un token e chiamare l'API Microsoft Graph da un'app desktop Windows](./quickstart-v2-windows-desktop.md)
- [Avvio rapido: Acquisire un token e chiamare l'API Microsoft Graph da un'app UWP](./quickstart-v2-uwp.md)
- [Avvio rapido: Acquisire un token e chiamare l'API Microsoft Graph da un'app nativa macOS](./quickstart-v2-ios.md)

## <a name="overview"></a>Panoramica

Si scrive un'applicazione desktop e si vuole far accedere gli utenti all'applicazione e chiamare API Web, ad esempio Microsoft Graph, altre API Microsoft o un'API Web personalizzata. Sono disponibili diverse opzioni:

- È possibile usare l'acquisizione token interattiva:

  - Se l'applicazione desktop supporta i controlli grafici, ad esempio, se si tratta di un'applicazione Windows.Form, di un'applicazione WPF o di un'applicazione nativa macOS.
  - In alternativa, se si tratta di un'applicazione .NET Core e si accetta che l'interazione di autenticazione con Azure Active Directory (Azure AD) venga eseguita nel browser del sistema.

- Per le applicazioni ospitate in Windows, è anche possibile che le applicazioni in esecuzione su computer aggiunti a un dominio di Windows o Azure AD uniti per acquisire un token in modo invisibile all'utente usando l'autenticazione integrata di Windows.
- Infine, anche se non è consigliabile, è possibile usare un nome utente e una password nelle applicazioni client pubbliche. Questo è ancora necessario in alcuni scenari, come DevOps. Il suo uso impone vincoli alle applicazioni. Ad esempio, non sarà possibile concedere l'accesso a un utente che deve eseguire l'[autenticazione a più fattori](../authentication/concept-mfa-howitworks.md) (accesso condizionale). Inoltre, le applicazioni non usufruiscono dell'accesso Single Sign-On (SSO).

  Il flusso è anche contrario ai principi dell'autenticazione moderna e viene fornito solo per motivi legacy.

  ![Applicazione desktop](media/scenarios/desktop-app.svg)

- Se si scrive uno strumento della riga di comando portabile, probabilmente un'applicazione .NET Core eseguita in Linux o Mac e si accetta che l'autenticazione verrà delegata al browser di sistema, è possibile usare l'autenticazione interattiva. .NET Core non fornisce un [browser Web](https://aka.ms/msal-net-uses-web-browser), quindi l'autenticazione viene eseguita nel browser del sistema. In caso contrario, l'opzione migliore in questo caso consiste nell'usare il flusso di codice del dispositivo. Questo flusso viene usato anche per le applicazioni senza un browser, ad esempio le applicazioni Internet.

  ![Applicazione browser](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>Specifiche

Le applicazioni desktop hanno diverse peculiarità. Dipendono principalmente dal fatto che l'applicazione usi o meno l'autenticazione interattiva.

## <a name="recommended-reading"></a>Letture consigliate

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [App desktop: Registrazione dell'app](scenario-desktop-app-registration.md)
