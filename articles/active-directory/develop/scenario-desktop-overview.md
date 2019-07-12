---
title: 'App desktop di chiamate di web API (panoramica): piattaforma delle identità Microsoft'
description: Informazioni su come creare un'app Desktop di chiamate di web API (panoramica)
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 476703b52813e6b3081dcfb3ab5a2fb4f3a7bfc5
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67785625"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>Scenario: App desktop che chiama le API Web

Scopri tutto quello che dovete compilare un'app Desktop che chiama le API web

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Introduzione

Se hai già fatto, creare la prima applicazione seguendo la Guida introduttiva a .NET desktop o la Guida introduttiva UWP:

> [!div class="nextstepaction"]
> [Avvio rapido: Acquisire un token e chiamare l'API Microsoft Graph da un'app desktop di Windows](./quickstart-v2-windows-desktop.md)


> [!div class="nextstepaction"]
> [Avvio rapido: Acquisire un token e chiamare l'API Microsoft Graph da un'app UWP](./quickstart-v2-uwp.md)

## <a name="overview"></a>Panoramica

Si scrive un'applicazione desktop e si vuole accedere gli utenti all'applicazione e chiamare le API, ad esempio Microsoft Graph, altre APIs Microsoft o un'API web web. Sono disponibili diverse possibilità:

- È possibile usare l'acquisizione dei token interattiva:

  - Se l'applicazione desktop supporta i controlli con interfaccia grafici, ad esempio se si tratta di un'applicazione Windows.Form o un'applicazione WPF.
  - Di se si tratta di un'applicazione .NET Core e accetti di svolgere l'interazione di autenticazione con Azure AD si verificano nel browser del sistema

- Per le applicazioni Windows ospitato, è anche possibile per le applicazioni in esecuzione in computer aggiunti a un dominio Windows o AAD si farebbe per acquisire un token invisibile all'utente tramite l'autenticazione integrata di Windows.
- Infine, e sebbene non sia consigliabile, è possibile usare nome utente/Password nelle applicazioni client pubblici. È ancora necessario in alcuni scenari (ad esempio DevOps), ma tenere presente che utilizzarlo impongono vincoli sull'applicazione. Ad esempio, è possibile accedere gli utenti che devono eseguire l'autenticazione a più fattori (accesso condizionale). Anche l'applicazione non trarre vantaggio dall'accesso single sign-on (SSO).

  È anche contro i principi dell'autenticazione moderna e viene fornito solo per motivi di compatibilità.

  ![Applicazione desktop](media/scenarios/desktop-app.svg)

- Se si sta scrivendo uno strumento da riga di comando portabile - probabilmente un'applicazione .NET Core in esecuzione su Linux o Mac - e se si accetta di delegare l'autenticazione per il browser del sistema, sarà possibile usare l'autenticazione interattiva. (.NET core non offre ancora una [browser Web](https://aka.ms/msal-net-uses-web-browser) e pertanto l'autenticazione viene eseguita nel browser del sistema), in caso contrario, l'opzione migliore in questo caso è usare il flusso di codice di dispositivo. Questo flusso viene usato anche per le applicazioni senza un browser, ad esempio applicazioni IoT

  ![Applicazione browserless](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>Specifiche

Applicazioni desktop con un numero di caratteristiche, che dipende principalmente dal fatto che l'applicazione usa l'autenticazione interattiva o No.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [App desktop: registrazione dell'app](scenario-desktop-app-registration.md)
