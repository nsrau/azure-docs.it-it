---
title: API Web che chiama le API Web downstream (panoramica)-piattaforma di identità Microsoft
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
ms.openlocfilehash: 1ef9fc121b16d81eed932d1ab55ca38d2a2f1057
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852506"
---
# <a name="scenario-web-api-that-calls-web-apis"></a>Scenario: App Web che chiama le API Web

Scopri tutto quello che ti serve per creare un'API Web che chiama le API Web.

## <a name="prerequisites"></a>Prerequisiti

Questo scenario, un'API Web protetta che chiama API Web, si basa sullo scenario "Proteggi un'API Web". Per altre informazioni su questo scenario di base, vedere primo [scenario dell'API Web protetta](scenario-protected-web-api-overview.md) .

## <a name="overview"></a>Panoramica

- Un client (applicazione Web, desktop, per dispositivi mobili o a pagina singola), non rappresentato nel diagramma seguente, chiama un'API Web protetta e fornisce un bearer token JWT nell'intestazione http "Authorization".
- L'API Web protetta convalida il token e usa il metodo MSAL `AcquireTokenOnBehalfOf` per richiedere (da Azure ad) un altro token in modo che possa, a sua volta, chiamare una seconda API Web (denominata API Web downstream) per conto dell'utente.
- L'API Web protetta usa questo token per chiamare un'API downstream. Può anche chiamare `AcquireTokenSilent`in un secondo momento per richiedere token per altre API downstream (ma ancora per conto dello stesso utente). `AcquireTokenSilent`Aggiorna il token quando necessario.

![API Web che chiama un'API Web](media/scenarios/web-api.svg)

## <a name="specifics"></a>Specifiche

La parte della registrazione dell'app correlata alle autorizzazioni dell'API è classica. La configurazione dell'applicazione prevede l'uso del flusso OAuth 2,0 per lo scambio di JWT bearer token rispetto a un token per un'API downstream. Questo token viene aggiunto alla cache dei token, in cui è disponibile nei controller dell'API Web, ed è in grado di acquisire un token in modo invisibile all'utente per chiamare le API downstream.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Registrazione delle app](scenario-web-api-call-api-app-registration.md)
