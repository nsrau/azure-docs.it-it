---
title: 'API Web che chiama downstream web API (panoramica): piattaforma delle identità Microsoft'
description: Informazioni su come creare una web API che le chiamate a valle web API (panoramica).
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
ms.openlocfilehash: 497134b7f3cc535f7b3f180db13cd04ef56787db
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075401"
---
# <a name="scenario-web-api-that-calls-web-apis"></a>Scenario: API Web che chiama le API web

Scopri tutto quello che serve per compilare un'API web che chiama le API web.

## <a name="prerequisites"></a>Prerequisiti

Questo scenario, API web protetta di chiamate di web API, si basa lo scenario "Proteggere un'API web". Per altre informazioni su questo scenario di base, vedere [API Web protetta da - Scenario](scenario-protected-web-api-overview.md) prima.

## <a name="overview"></a>Panoramica

- Un client (web, desktop, applicazioni o per dispositivi mobili, a pagina singola) - non rappresentato nel diagramma seguente: - chiama un'API web protetta e fornisce un token di connessione JWT nell'intestazione "Authorization" Http.
- L'API web protetta convalida il token e Usa il MSAL `AcquireTokenOnBehalfOf` chiamata del metodo per richiedere un altro token (da Azure AD) in modo che sia possibile, stessa, un secondo web API (API web downstream denominata) per conto dell'utente.
- L'API web protetta Usa questo token per chiamare un'API downstream. Può anche chiamare `AcquireTokenSilent`successiva per richiedere token per le altre API downstream (ma comunque per conto dell'utente stesso). `AcquireTokenSilent` Aggiorna il token quando necessario.

![Chiamare un'API web API Web](media/scenarios/web-api.svg)

## <a name="specifics"></a>Specifiche

La parte della registrazione dell'app relative alle autorizzazioni API è classica. La configurazione dell'applicazione prevede l'utilizzo il flusso on-behalf-of di OAuth 2.0 per scambiare il token di connessione JWT rispetto a un token per un'API downstream. Questo token viene aggiunto alla cache dei token, in cui è disponibile nel controller dell'API web e può acquisire un token in modo invisibile per chiamare le API downstream.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Registrazione delle app](scenario-web-api-call-api-app-registration.md)
