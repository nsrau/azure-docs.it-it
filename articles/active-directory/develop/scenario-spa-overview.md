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
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 60e4ca80faa2c8787a13d87ab06cad9243299e50
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89291952"
---
# <a name="scenario-single-page-application"></a>Scenario: applicazione a pagina singola

Scopri tutto quello che ti serve per creare un'applicazione a singola pagina (SPA).

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Introduzione

È possibile creare la prima applicazione seguendo la Guida introduttiva a JavaScript SPA:

> [!div class="nextstepaction"]
> [Guida introduttiva: applicazione a pagina singola](./quickstart-v2-javascript.md)

## <a name="overview"></a>Panoramica

Molte applicazioni Web moderne sono compilate come applicazioni a singola pagina sul lato client. Gli sviluppatori li scrivono usando JavaScript o un Framework di applicazione a singola pagina, ad esempio angolare, VME e React. Queste applicazioni vengono eseguite in un browser Web e presentano caratteristiche di autenticazione diverse rispetto alle applicazioni Web tradizionali sul lato server. 

La piattaforma Microsoft Identity offre **due** opzioni per consentire alle applicazioni a singola pagina di accedere agli utenti e ottenere i token per accedere ai servizi back-end o alle API Web:

- [Flusso del codice di autorizzazione OAuth 2,0 (con PKCE)](./v2-oauth2-auth-code-flow.md). Il flusso del codice di autorizzazione consente all'applicazione di scambiare un codice di autorizzazione per i token **ID** per rappresentare l'utente autenticato e i token di **accesso** necessari per chiamare le API protette. Restituisce inoltre i token di **aggiornamento** che forniscono l'accesso a lungo termine alle risorse per conto degli utenti senza richiedere l'interazione con tali utenti. Questo è l'approccio **consigliato** .

![Applicazioni a pagina singola-autenticazione](./media/scenarios/spa-app-auth.svg)

- [Flusso implicito OAuth 2,0](./v2-oauth2-implicit-grant-flow.md). Il flusso di concessione implicita consente all'applicazione di ottenere i token di **accesso** e **ID** . A differenza del flusso del codice di autorizzazione, il flusso di concessione implicita non restituisce un **token di aggiornamento**.

![Applicazioni a pagina singola-implicite](./media/scenarios/spa-app.svg)

Questo flusso di autenticazione non include scenari di applicazioni che usano framework JavaScript multipiattaforma, ad esempio Electron e React-native. Sono necessarie altre funzionalità per l'interazione con le piattaforme native.

## <a name="specifics"></a>Specifiche

Per abilitare questo scenario per l'applicazione, è necessario:

* Registrazione dell'applicazione con Azure Active Directory (Azure AD). I passaggi di registrazione differiscono tra il flusso di concessione implicito e il flusso del codice di autorizzazione.
* Configurazione dell'applicazione con le proprietà dell'applicazione registrate, ad esempio l'ID applicazione.
* Uso di Microsoft Authentication Library per JavaScript (MSAL.js) per eseguire il flusso di autenticazione per l'accesso e l'acquisizione di token.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Registrazione dell'app](scenario-spa-app-registration.md)
