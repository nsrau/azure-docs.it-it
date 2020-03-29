---
title: Creare un'app Web che chiami le API Web - Piattaforma di identità Microsoft . Azure
description: Informazioni su come creare un'app Web che chiama le API Web (panoramica)
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
ms.openlocfilehash: d121d6c198cb0d92cd098a40096e2f2300f65537
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76758990"
---
# <a name="scenario-a-web-app-that-calls-web-apis"></a>Scenario: un'app Web che chiama le API Web

Informazioni su come creare un'app Web che acceda agli utenti alla piattaforma di identità Microsoft e quindi chiami le API Web per conto dell'utente connesso.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

Questo scenario presuppone che sia già stato esaminato lo scenario seguente:This scenario assumes that you've already gone through the following scenario:

> [!div class="nextstepaction"]
> [App Web che accede agli utenti](scenario-web-app-sign-user-overview.md)

## <a name="overview"></a>Panoramica

Aggiungere l'autenticazione all'app Web in modo che possa accedere agli utenti e chiamare un'API Web per conto dell'utente connesso.

![App Web che chiama le API Web](./media/scenario-webapp/web-app.svg)

Le app Web che chiamano API Web sono applicazioni client riservate.
Ecco perché registrano un segreto (una password o un certificato dell'applicazione) con Azure Active Directory (Azure AD). Questo segreto viene passato durante la chiamata ad Azure AD per ottenere un token.

## <a name="specifics"></a>Specifiche

> [!NOTE]
> L'aggiunta dell'accesso a un'app Web riguarda la protezione dell'app Web stessa. Tale protezione viene ottenuta utilizzando librerie *middleware,* non Microsoft Authentication Library (MSAL). Lo scenario precedente, [App Web che accede agli utenti,](scenario-web-app-sign-user-overview.md)ha trattato l'argomento.
>
> Questo scenario illustra come chiamare le API Web da un'app Web.This scenario covers how to call web APIs from a web app. È necessario ottenere i token di accesso per tali API Web.You must get access tokens for those web APIs. Per acquisire tali token, utilizzare le librerie MSAL per acquisire questi token.

Lo sviluppo per questo scenario comporta queste attività specifiche:Development for this scenario involves these specific tasks:

- Durante [la registrazione dell'applicazione,](scenario-web-app-call-api-app-registration.md)è necessario fornire un URI di risposta, un segreto o un certificato da condividere con Azure AD. Se distribuisci l'app in più posizioni, devi fornire queste informazioni per ogni posizione.
- La [configurazione dell'applicazione](scenario-web-app-call-api-app-configuration.md) deve fornire le credenziali client condivise con Azure AD durante la registrazione dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Un'app Web che chiama le API Web: Registrazione dell'app](scenario-web-app-call-api-app-registration.md)
