---
title: Creare un'app Web che chiama API Web-piattaforma di identità Microsoft | Azure
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
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2020
ms.locfileid: "76758990"
---
# <a name="scenario-a-web-app-that-calls-web-apis"></a>Scenario: un'app Web che chiama le API Web

Informazioni su come creare un'app Web che consente agli utenti di accedere alla piattaforma di identità Microsoft e quindi chiama le API Web per conto dell'utente che ha eseguito l'accesso.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

In questo scenario si presuppone che sia già stato effettuato lo scenario seguente:

> [!div class="nextstepaction"]
> [App Web che offre l'accesso agli utenti](scenario-web-app-sign-user-overview.md)

## <a name="overview"></a>Overview

È possibile aggiungere l'autenticazione all'app Web in modo da consentire agli utenti di accedere e chiamare un'API Web per conto dell'utente che ha eseguito l'accesso.

![App Web che chiama le API Web](./media/scenario-webapp/web-app.svg)

Le app Web che chiamano API Web sono applicazioni client riservate.
Per questo motivo viene registrato un segreto (una password o un certificato dell'applicazione) con Azure Active Directory (Azure AD). Questo segreto viene passato durante la chiamata a Azure AD per ottenere un token.

## <a name="specifics"></a>Specifiche

> [!NOTE]
> Per aggiungere l'accesso a un'app Web, è necessario proteggere l'app Web. Tale protezione viene eseguita utilizzando le librerie *middleware* , non Microsoft Authentication Library (MSAL). Lo scenario precedente, l' [app Web che](scenario-web-app-sign-user-overview.md)esegue l'accesso degli utenti, ha coperto questo argomento.
>
> Questo scenario illustra come chiamare le API Web da un'app Web. È necessario ottenere i token di accesso per tali API Web. Per acquisire questi token, è possibile usare le librerie MSAL per acquisire questi token.

Lo sviluppo per questo scenario comporta le attività specifiche seguenti:

- Durante la [registrazione dell'applicazione](scenario-web-app-call-api-app-registration.md), è necessario specificare un URI di risposta, un segreto o un certificato da condividere con Azure ad. Se si distribuisce l'app in diverse posizioni, verranno fornite queste informazioni per ogni località.
- La [configurazione dell'applicazione](scenario-web-app-call-api-app-configuration.md) deve fornire le credenziali client condivise con Azure ad durante la registrazione dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Un'app Web che chiama le API Web: registrazione dell'app](scenario-web-app-call-api-app-registration.md)
