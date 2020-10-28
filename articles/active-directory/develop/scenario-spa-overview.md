---
title: Scenario di app a singola pagina JavaScript-piattaforma di identità Microsoft | Azure
description: Informazioni su come creare un'applicazione a singola pagina (panoramica dello scenario) usando la piattaforma di identità Microsoft.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40, devx-track-js
ms.openlocfilehash: 658e7f206f892c9dc241aab228aa933884f95b94
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675625"
---
# <a name="scenario-single-page-application"></a>Scenario: Applicazione a pagina singola

Scopri tutto quello che ti serve per creare un'applicazione a singola pagina (SPA).

## <a name="getting-started"></a>Introduzione

Se non è già stato fatto, creare la prima app completando la Guida introduttiva a JavaScript SPA:

[Guida introduttiva: applicazione a pagina singola](./quickstart-v2-javascript.md)

## <a name="overview"></a>Panoramica

Molte applicazioni Web moderne sono compilate come applicazioni a singola pagina sul lato client. Gli sviluppatori le scrivono usando JavaScript o un framework di applicazioni a pagina singola come Angular, Vue e React. Queste applicazioni vengono eseguite in un Web browser e hanno caratteristiche di autenticazione diverse rispetto alle applicazioni Web lato server tradizionali.

La piattaforma Microsoft Identity offre **due** opzioni per consentire alle applicazioni a singola pagina di accedere agli utenti e ottenere i token per accedere ai servizi back-end o alle API Web:

- [Flusso di codice di autorizzazione OAuth 2.0 (con PKCE)](./v2-oauth2-auth-code-flow.md). Il flusso di codice di autorizzazione consente all'applicazione di scambiare un codice di autorizzazione con i token **ID** per rappresentare l'utente autenticato e i token di **accesso** necessari per chiamare le API protette. Restituisce inoltre **token di aggiornamento** che consentono l'accesso a lungo termine alle risorse per conto degli utenti senza richiedere l'interazione con tali utenti. Questo è l'approccio **consigliato** .

![Single-page applications-auth](./media/scenarios/spa-app-auth.svg)

- [Flusso implicito OAuth 2.0](./v2-oauth2-implicit-grant-flow.md). Questo tipo di flusso consente all'applicazione di ottenere i token **ID** e **di accesso** . A differenza del flusso di codice di autorizzazione, il flusso di concessione implicita non restituisce un **token di aggiornamento** .

![Single-page applications-implicit](./media/scenarios/spa-app.svg)

Questo flusso di autenticazione non include scenari di applicazioni che usano framework JavaScript multipiattaforma, come Electron e React-Native. Sono necessarie altre funzionalità per l'interazione con le piattaforme native.

## <a name="specifics"></a>Specifiche

Per abilitare questo scenario per l'applicazione, è necessario:

* Registrazione dell'applicazione con Azure Active Directory (Azure AD). I passaggi di registrazione differiscono tra il flusso di concessione implicito e il flusso del codice di autorizzazione.
* Configurazione dell'applicazione con le proprietà dell'applicazione registrate, ad esempio l'ID applicazione.
* Uso di Microsoft Authentication Library per JavaScript (MSAL.js) per eseguire il flusso di autenticazione per l'accesso e l'acquisizione di token.

## <a name="recommended-reading"></a>Letture consigliate

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Registrazione dell'app](scenario-spa-app-registration.md)
