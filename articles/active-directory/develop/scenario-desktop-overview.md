---
title: Crea un'app desktop che chiama le API Web - Piattaforma di identità Microsoft Azure
description: Informazioni su come creare un'app desktop che chiama le API Web (panoramica)
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
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 196102769c1f5f68df316918a63079b09baad32d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76702148"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>Scenario: app desktop che chiama le API Web

Scopri tutto ciò che ti serve per creare un'app desktop che chiami le API Web.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="get-started"></a>Introduzione

Se non l'hai già fatto, crea la prima applicazione seguendo la guida introduttiva del desktop .NET, la guida introduttiva della piattaforma UWP (Universal Windows Platform) o la guida introduttiva dell'app nativa di macOS:

> [!div class="nextstepaction"]
> [Guida introduttiva: Acquisire un token e chiamare l'API Microsoft Graph da un'app di Windows Desktop](./quickstart-v2-windows-desktop.md)


> [!div class="nextstepaction"]
> [Guida introduttiva: Acquisire un token e chiamare l'API Microsoft Graph da un'app UWP](./quickstart-v2-uwp.md)

> [!div class="nextstepaction"]
> [Guida introduttiva: Acquisire un token e chiamare l'API Microsoft Graph da un'app nativa di macOS](./quickstart-v2-ios.md)

## <a name="overview"></a>Panoramica

Si scrive un'applicazione desktop e si desidera accedere agli utenti all'applicazione e chiamare le API Web, ad esempio Microsoft Graph, altre API Microsoft o la propria API Web. Hai diverse possibilità:

- È possibile utilizzare l'acquisizione di token interattiva:You can use the interactive token acquisition:

  - Se l'applicazione desktop supporta i controlli grafici, ad esempio se si tratta di un'applicazione Windows.Form, un'applicazione WPF o un'applicazione nativa macOS.
  - In alternativa, se si tratta di un'applicazione .NET Core e si accetta di fare in modo che l'interazione di autenticazione con Azure Active Directory (Azure AD) avvenga nel browser di sistema.

- For Windows hosted applications, it's also possible for applications running on computers joined to a Windows domain or Azure AD joined to acquire a token silently by using Integrated Windows Authentication.
- Infine, e anche se non è consigliabile, è possibile utilizzare un nome utente e una password nelle applicazioni client pubbliche. È ancora necessario in alcuni scenari come DevOps.It's still needed in some scenarios like DevOps. Il suo utilizzo impone vincoli all'applicazione. Ad esempio, non può accedere a un utente che deve eseguire l'autenticazione a più fattori (accesso condizionale). Inoltre, l'applicazione non trarrà vantaggio dall'accesso Single Sign-On (SSO).

  Il flusso è anche contrario ai principi dell'autenticazione moderna e viene fornito solo per motivi legacy.

  ![Applicazione desktop](media/scenarios/desktop-app.svg)

- Se si scrive uno strumento da riga di comando portatile, probabilmente un'applicazione .NET Core che viene eseguita su Linux o Mac e se si accetta che l'autenticazione verrà delegata al browser di sistema, è possibile utilizzare l'autenticazione interattiva. .NET Core non fornisce un [Web browser,](https://aka.ms/msal-net-uses-web-browser)pertanto l'autenticazione avviene nel browser di sistema. In caso contrario, l'opzione migliore in questo caso consiste nell'usare il flusso di codice del dispositivo. Questo flusso viene utilizzato anche per le applicazioni senza un browser, ad esempio le applicazioni IoT.This flow is also used for applications without a browser, such as IoT applications.

  ![Applicazione senza browser](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>Specifiche

Le applicazioni desktop hanno una serie di specificità. Dipendano principalmente dal fatto che l'applicazione utilizzi o meno l'autenticazione interattiva.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [App desktop: registrazione dell'app](scenario-desktop-app-registration.md)
