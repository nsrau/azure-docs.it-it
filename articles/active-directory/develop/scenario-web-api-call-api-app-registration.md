---
title: Web API che le chiamate a valle web API (registrazione dell'app) - piattaforma delle identità Microsoft
description: Informazioni su come creare una web API che le chiamate a valle web API (registrazione dell'app)
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
ms.openlocfilehash: fb03869cdea2150b6e922e2d6d81e577c3be02da
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65075386"
---
# <a name="web-api-that-calls-web-apis---app-registration"></a>Web API che chiama l'API - registrazione dell'app web

Un'API web che chiama le API web downstream ha la stessa registrazione di un'API web protetta. Pertanto, è necessario seguire le istruzioni in [API Web protetta da - registrazione dell'app](scenario-protected-web-api-app-registration.md).

Tuttavia, poiché l'app web a questo punto chiama le API, web diventa un'applicazione client riservata. Ecco perché è presente le informazioni di registrazione aggiuntiva necessaria: l'app deve condividere i segreti (credenziali client) con la piattaforma delle identità Microsoft.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Autorizzazioni delle API

Le applicazioni Web di chiamare le API per conto dell'utente per il quale è stato ricevuto il token di connessione. È necessario richiedere autorizzazioni delegate. Per informazioni dettagliate, vedere [aggiungere le autorizzazioni per accedere alle API web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurazione del codice dell'App](scenario-web-api-call-api-app-configuration.md)
