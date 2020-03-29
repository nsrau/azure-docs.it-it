---
title: Crea un'API Web che chiami le API Web - Piattaforma di identità Microsoft Azure
description: Informazioni su come creare un'API Web che chiama le API Web downstream (panoramica).
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
ms.openlocfilehash: 467ff2f789cc83bc5651d831838da0b5c922c839
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701740"
---
# <a name="scenario-a-web-api-that-calls-web-apis"></a>Scenario: un'API Web che chiama le API Web

Scopri cosa devi sapere per creare un'API Web che chiami le API Web.

## <a name="prerequisites"></a>Prerequisiti

Questo scenario, in cui un'API Web protetta chiama le API Web, si basa sullo scenario "Proteggi un'API Web". Per ulteriori informazioni su questo scenario di base, vedere [Scenario: API Web protetta](scenario-protected-web-api-overview.md).

## <a name="overview"></a>Panoramica

- Un client dell'applicazione Web, desktop, mobile o a pagina singola (non rappresentato nel diagramma di accompagnamento) chiama un'API Web protetta e fornisce un token di connessione JSON Web Token (JWT) nell'intestazione HTTP "Authorization".
- L'API Web protetta convalida il token e usa `AcquireTokenOnBehalfOf` il metodo Microsoft Authentication Library (MSAL) per richiedere un altro token da Azure Active Directory (Azure AD) in modo che l'API Web protetta possa chiamare una seconda API Web, o API Web downstream, per conto dell'utente.
- L'API Web protetta può anche chiamare `AcquireTokenSilent`in un secondo momento per richiedere i token per altre API downstream per conto dello stesso utente. `AcquireTokenSilent`aggiorna il token quando necessario.

![Diagramma di un'API Web che chiama un'API Web](media/scenarios/web-api.svg)

## <a name="specifics"></a>Specifiche

La parte di registrazione dell'app correlata alle autorizzazioni API è classica. La configurazione dell'app prevede l'uso del flusso OAuth 2.0 On-Behalf-Of per scambiare il token di connessione JWT con un token per un'API downstream. Questo token viene aggiunto alla cache dei token, dove è disponibile nei controller dell'API Web, e può quindi acquisire un token in modo invisibile all'utente per chiamare le API downstream.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Registrazione delle app](scenario-web-api-call-api-app-registration.md)
