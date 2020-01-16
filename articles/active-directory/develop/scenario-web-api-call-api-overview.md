---
title: Creare un'API Web che chiama API Web-piattaforma di identità Microsoft | Azure
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: b67507daf8005f3f9a299b778f1fba4ce67d46d4
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76044152"
---
# <a name="scenario-a-web-api-that-calls-web-apis"></a>Scenario: un'API Web che chiama le API Web

Informazioni su ciò che è necessario sapere per creare un'API Web che chiama le API Web.

## <a name="prerequisites"></a>Prerequisiti

Questo scenario, in cui un'API Web protetta chiama API Web, si basa sullo scenario "Proteggi un'API Web". Per altre informazioni su questo scenario di base, vedere [scenario: API Web protetta](scenario-protected-web-api-overview.md).

## <a name="overview"></a>Overview

- Un client di applicazioni Web, desktop, per dispositivi mobili o a singola pagina (non rappresentato nel diagramma associato) chiama un'API Web protetta e fornisce un token JSON Web (JWT) bearer token nell'intestazione HTTP "Authorization".
- L'API Web protetta convalida il token e usa il metodo `AcquireTokenOnBehalfOf` Microsoft Authentication Library (MSAL) per richiedere un altro token da Azure Active Directory (Azure AD), in modo che l'API Web protetta possa chiamare una seconda API Web o un'API Web downstream per conto dell'utente.
- L'API Web protetta può anche chiamare `AcquireTokenSilent`in un secondo momento per richiedere token per altre API downstream per conto dello stesso utente. Quando necessario, `AcquireTokenSilent` aggiorna il token.

![Diagramma di un'API Web che chiama un'API Web](media/scenarios/web-api.svg)

## <a name="specifics"></a>Specifiche

La parte relativa alla registrazione dell'app correlata alle autorizzazioni API è classica. La configurazione dell'app prevede l'uso del flusso OAuth 2,0 per lo scambio di JWT bearer token rispetto a un token per un'API downstream. Questo token viene aggiunto alla cache dei token, dove è disponibile nei controller dell'API Web e può quindi acquisire un token in modo invisibile all'utente per chiamare le API downstream.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Registrazione delle app](scenario-web-api-call-api-app-registration.md)
