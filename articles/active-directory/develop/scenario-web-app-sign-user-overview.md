---
title: 'App Web di accesso degli utenti (panoramica): piattaforma delle identità Microsoft'
description: Informazioni su come compilare un'app web di accesso degli utenti (panoramica)
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
ms.openlocfilehash: 6ce534c6eeecba220fd829be829caa679df52055
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65833090"
---
# <a name="scenario-web-app-that-signs-in-users"></a>Scenario: App Web che offre l'accesso agli utenti

Scopri tutto quello che dovete compilare un'app web che gli utenti esegue l'accesso con la piattaforma delle identità Microsoft.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Introduzione

Se si desidera creare le prime portabili (ASP.NET Core) web App che consentono l'accesso degli utenti, seguire questa Guida introduttiva:

> [!div class="nextstepaction"]
> [Avvio rapido: ASP.NET Core che esegue l'accesso per gli utenti di app web](quickstart-v2-aspnet-core-webapp.md)

Se si preferisce usare ASP.NET, provare l'esercitazione seguente:

> [!div class="nextstepaction"]
> [Avvio rapido: App web ASP.NET che esegue l'accesso gli utenti](quickstart-v2-aspnet-webapp.md)

## <a name="overview"></a>Panoramica

Aggiungere l'autenticazione all'App web, in modo che gli utenti e può accedere. Aggiunta di autenticazione consente all'app web di accedere alle informazioni sul profilo e, ad esempio personalizzare l'esperienza che è offrire ai propri utenti. App Web di autenticare un utente in un web browser. In questo scenario l'applicazione Web indirizza il browser dell'utente per l'accesso ad Azure AD. Tramite il browser dell'utente Azure AD restituisce una risposta di accesso che contiene attestazioni sull'utente in un token di sicurezza. Gli utenti di accesso usare le [Openid Connect](./v2-protocols-oidc.md) protocollo standard stesso semplificata dall'uso di middleware [librerie](scenario-web-app-sign-user-app-configuration.md#libraries-used-to-protect-web-apps).

![Esegue l'accesso utenti dell'app Web](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

Come una seconda fase è anche possibile abilitare l'applicazione per chiamare le API Web per conto dell'utente connesso. Questa fase successiva è uno scenario diverso, troverete [App Web chiama le API Web](scenario-web-app-call-api-overview.md)

> [!NOTE]
> Aggiunta di accesso web di app è sulla protezione dell'app web e la convalida di un token utente, cioè che cosa **middleware** librerie si. Questo scenario non richiede ancora il Microsoft Authentication Library (MSAL), ovvero sull'acquisizione di un token per chiamare le API protette. Le librerie di autenticazione verranno introdotte nello scenario di follow-up solo quando l'app web deve chiamare le API web.

## <a name="specifics"></a>Specifiche

- Durante la registrazione dell'applicazione, è necessario fornire uno o diversi (se si distribuisce l'app in diverse posizioni) gli URI di risposta. In alcuni casi (ASP.NET/ASP.NET Core), è necessario abilitare il IDToken. Infine, è opportuno impostare un URI di disconnessione in modo che l'applicazione reagisce agli utenti di firma in uscita.
- Nel codice dell'applicazione, è necessario fornire l'autorità a cui si app delegati accesso web. È possibile personalizzare la convalida dei token (in particolare negli scenari ISV).
- Le applicazioni Web supportano tutti i tipi di account. Per altre informazioni, vedi [tipi di account supportati](v2-supported-account-types.md).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Registrazione delle app](scenario-web-app-sign-user-app-registration.md)
