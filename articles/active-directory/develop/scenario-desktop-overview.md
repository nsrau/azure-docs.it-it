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
ms.openlocfilehash: 44d31011ca70bbebaf994b5fb80a45eee8dbde40
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076946"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>Scenario: App desktop che chiama l'API web

Scopri tutto quello che dovete compilare un'app Desktop che chiama le API web

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Introduzione

Se hai già fatto, creare la prima applicazione seguendo la Guida introduttiva a .NET desktop o la Guida introduttiva UWP:

> [!div class="nextstepaction"]
> [Guida introduttiva: Acquisire un token e chiamare l'API Microsoft Graph da un'app desktop di Windows](./quickstart-v2-windows-desktop.md)


> [!div class="nextstepaction"]
> [Guida introduttiva: Acquisire un token e chiamare l'API Microsoft Graph da un'app UWP](./quickstart-v2-uwp.md)

## <a name="overview"></a>Panoramica

Si scrive un'applicazione desktop e si vuole accedere gli utenti all'applicazione e chiamare le API, ad esempio Microsoft Graph, altre APIs Microsoft o un'API web web. Sono disponibili diverse possibilità:

- Se l'applicazione desktop supporta i controlli con interfaccia grafici, ad esempio se si tratta di un'applicazione Windows.Form o un'applicazione WPF, è possibile usare l'acquisizione dei token interattiva.
- Per le applicazioni Windows ospitato, è anche possibile per le applicazioni in esecuzione in computer aggiunti a un dominio Windows o AAD si farebbe per acquisire un token invisibile all'utente tramite l'autenticazione integrata di Windows.
- Infine, e sebbene non sia consigliabile, è possibile usare nome utente/Password nelle applicazioni client pubblici. È ancora necessario in alcuni scenari (ad esempio DevOps), ma tenere presente che utilizzarlo impongono vincoli sull'applicazione. Ad esempio, è possibile accedere gli utenti che devono eseguire l'autenticazione a più fattori (accesso condizionale). Anche l'applicazione non trarre vantaggio dall'accesso single sign-on (SSO).

  È anche contro i principi dell'autenticazione moderna e viene fornito solo per motivi di compatibilità.

  ![Applicazione desktop](media/scenarios/desktop-app.svg)

- Se si sta scrivendo uno strumento da riga di comando portabile: probabilmente un'applicazione .NET Core in esecuzione in Linux o Mac, non potrai usare né l'autenticazione interattiva (come .NET Core non fornisce una [browser Web](https://aka.ms/msal-net-uses-web-browser)), né integrata Autenticazione di Windows. L'opzione migliore in questo caso consiste nell'utilizzare il flusso di codice di dispositivo. Questo flusso viene usato anche per le applicazioni senza un browser, ad esempio applicazioni iOT

  ![Applicazione browserless](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>Specifiche

Applicazioni desktop con un numero di caratteristiche, che dipende principalmente dal fatto che l'applicazione usa l'autenticazione interattiva o No.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [App desktop: registrazione dell'app](scenario-desktop-app-registration.md)
