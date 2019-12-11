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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3888c7f838d6009382f849bc7d3e34c49b3b70a4
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74962135"
---
# <a name="scenario-web-app-that-calls-web-apis"></a>Scenario: app Web che chiama le API Web

Informazioni su come creare un'app Web che esegue l'accesso degli utenti nella piattaforma di identità Microsoft e quindi chiama le API Web per conto dell'utente che ha eseguito l'accesso.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

In questo scenario si presuppone che sia stato effettuato lo scenario seguente:

> [!div class="nextstepaction"]
> [App Web per l'accesso degli utenti](scenario-web-app-sign-user-overview.md)

## <a name="overview"></a>Panoramica

Si aggiunge l'autenticazione all'app Web, che può quindi accedere agli utenti e chiama un'API Web per conto dell'utente che ha eseguito l'accesso.

![App Web che chiama le API Web](./media/scenario-webapp/web-app.svg)

App Web che chiamano API Web:

- sono applicazioni client riservate.
- per questo motivo hanno registrato un segreto (password dell'applicazione o certificato) con Azure AD. Questo segreto viene passato durante la chiamata a Azure AD per ottenere un token

## <a name="specifics"></a>Specifiche

> [!NOTE]
> L'aggiunta di un accesso a un'app Web non usa le librerie MSAL, perché si tratta di proteggere l'app Web. La protezione delle librerie viene eseguita dalle librerie denominate middleware. Questo è l'oggetto dello scenario precedente [utenti di accesso a un'app Web](scenario-web-app-sign-user-overview.md)
>
> Quando si chiamano API Web da un'app Web, sarà necessario ottenere i token di accesso per queste API Web. Per acquisire questi token, è possibile usare le librerie MSAL.

L'esperienza end-to-end degli sviluppatori per questo scenario ha, di conseguenza, aspetti specifici come:

- Durante la [registrazione dell'applicazione](scenario-web-app-call-api-app-registration.md), è necessario specificarne uno o diversi (se si distribuisce l'app in diversi percorsi), è necessario condividere gli URI di risposta, i segreti o i certificati con Azure ad.
- La [configurazione dell'applicazione](scenario-web-app-call-api-app-configuration.md) deve fornire le credenziali client come condivise con Azure ad durante la registrazione dell'applicazione

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Registrazione delle app](scenario-web-app-call-api-app-registration.md)
