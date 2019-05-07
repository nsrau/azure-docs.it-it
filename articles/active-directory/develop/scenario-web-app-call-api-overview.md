---
title: 'App Web che chiama web API (panoramica): piattaforma delle identità Microsoft'
description: Informazioni su come compilare un'app web che chiama web API (panoramica)
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
ms.openlocfilehash: ce45f11a697b72ebdd0fe01166a70e7b47aa8e9f
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076301"
---
# <a name="scenario-web-app-that-calls-web-apis"></a>Scenario: App Web che chiama l'API web

Informazioni su come compilare un'app web di accesso di utenti nella piattaforma delle identità Microsoft e che chiama le API web per conto dell'utente connesso.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

Questo scenario supposes che aver esaminato lo scenario seguente:

> [!div class="nextstepaction"]
> [App Web che gli utenti esegue l'accesso](scenario-web-app-sign-user-overview.md)

## <a name="overview"></a>Panoramica

Verrà aggiunta l'autenticazione all'App Web, che possono pertanto accedere gli utenti e chiama un'API web per conto dell'utente connesso.

![App Web che chiama l'API web](./media/scenario-webapp/web-app.svg)

Le app Web che chiama le API web:

- Sono le applicazioni client riservato.
- Ecco perché è stato registrato un segreto (password applicazione o certificato) con Azure AD. Il segreto è passata durante la chiamata ad Azure AD per ottenere un token

## <a name="specifics"></a>Specifiche

> [!NOTE]
> Aggiunta di accesso a un'App Web non usa le librerie MSAL poiché si tratta di informazioni sulla protezione dell'App Web. La protezione di librerie avviene da parte di librerie denominate Middleware. Questo è l'oggetto dello scenario precedente [Accedi agli utenti di un'App Web](scenario-web-app-sign-user-overview.md)
>
> Quando si chiamano le API web da un'App Web, è necessario ottenere i token di accesso per queste API web. È possibile usare librerie MSAL per acquisire i token.

L'esperienza end to end degli sviluppatori per questo scenario è, di conseguenza, alcuni aspetti specifici come:

- Durante la [registrazione applicazione](scenario-web-app-call-api-app-registration.md), sarà necessario specificarne uno, o diversi (se si distribuisce l'app in diverse posizioni) rispondere URI, i segreti, o i certificati devono essere condivisi con Azure AD.
- Il [configurazione dell'applicazione](scenario-web-app-call-api-app-configuration.md) deve fornire le credenziali del client come condiviso con Azure AD durante la registrazione dell'applicazione

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Registrazione delle app](scenario-web-app-call-api-app-registration.md)
