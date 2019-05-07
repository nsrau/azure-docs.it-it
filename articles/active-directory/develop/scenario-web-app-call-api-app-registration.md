---
title: App Web che chiama web API (registrazione dell'app) - piattaforma delle identità Microsoft
description: Informazioni su come compilare un'app web che chiama web API (registrazione dell'app)
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
ms.openlocfilehash: 5becdc287f7cad28aa6c7c07ebc107586e9a2b2a
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075191"
---
# <a name="web-app-that-calls-web-apis---app-registration"></a>App Web che chiama l'API - registrazione dell'app web

Un'API web di Web app chiama ha la stessa registrazione come un'App Web di firma di utenti. Sarà quindi necessario seguire le istruzioni in [app Web che esegue l'accesso gli utenti - registrazione dell'app](scenario-web-app-sign-user-app-registration.md)

Tuttavia poiché l'App Web a questo punto chiama web API, diventa un'applicazione client riservata. Ecco perché è un po' di registrazione aggiuntiva necessaria: è necessario condividere i segreti (credenziali client) con la piattaforma delle identità Microsoft.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Autorizzazioni delle API

Le applicazioni Web di chiamare le API per conto dell'utente connesso. È necessario richiedere autorizzazioni delegate. Per informazioni dettagliate vedere [aggiungere le autorizzazioni per accedere alle API web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurazione del codice dell'App](scenario-web-app-call-api-app-configuration.md)
