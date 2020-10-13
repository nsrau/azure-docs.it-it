---
title: Creare un'API Web che chiama API Web-piattaforma di identità Microsoft | Azure
description: Informazioni su come creare un'API Web che chiama le API Web downstream (panoramica).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 4dcf377797709b56b4db735dabf4d48cfae4fc06
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91257164"
---
# <a name="scenario-a-web-api-that-calls-web-apis"></a>Scenario: un'API Web che chiama le API Web

Informazioni su ciò che è necessario sapere per creare un'API Web che chiama le API Web.

## <a name="prerequisites"></a>Prerequisiti

Questo scenario, in cui un'API Web protetta chiama altre API Web, si basa sullo [scenario: API Web protetta](scenario-protected-web-api-overview.md).

## <a name="overview"></a>Panoramica

- Un client di applicazioni Web, desktop, per dispositivi mobili o a singola pagina (non rappresentato nel diagramma associato) chiama un'API Web protetta e fornisce un token JSON Web (JWT) bearer token nell'intestazione HTTP "Authorization".
- L'API Web protetta convalida il token e usa il metodo Microsoft Authentication Library (MSAL) `AcquireTokenOnBehalfOf` per richiedere un altro token da Azure Active Directory (Azure ad), in modo che l'API Web protetta possa chiamare una seconda API Web o un'API Web downstream per conto dell'utente.
- L'API Web protetta può anche chiamare `AcquireTokenSilent` in un secondo momento per richiedere token per altre API downstream per conto dello stesso utente. `AcquireTokenSilent` Aggiorna il token quando necessario.

![Diagramma di un'API Web che chiama un'API Web](media/scenarios/web-api.svg)

## <a name="specifics"></a>Specifiche

La parte relativa alla registrazione dell'app correlata alle autorizzazioni API è classica. La configurazione dell'app prevede l'uso del flusso OAuth 2,0 per lo scambio di JWT bearer token rispetto a un token per un'API downstream. Questo token viene aggiunto alla cache dei token, dove è disponibile nei controller dell'API Web e può quindi acquisire un token in modo invisibile all'utente per chiamare le API downstream.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Registrazione delle app](scenario-web-api-call-api-app-registration.md)
