---
title: Registrare un'API Web che chiama le API Web - Piattaforma di identità Microsoft Azure
description: Scopri come creare un'API Web che chiama le API Web downstream (registrazione dell'app).
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
ms.openlocfilehash: bafd71f34602535bb6193a8d8114a1182e4e8f40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701791"
---
# <a name="a-web-api-that-calls-web-apis-app-registration"></a>Un'API Web che chiama le API Web: Registrazione dell'app

Un'API Web che chiama le API Web downstream ha la stessa registrazione di un'API Web protetta. Pertanto, è necessario seguire le istruzioni in [API Web protette: registrazione dell'app](scenario-protected-web-api-app-registration.md).

Poiché l'app Web ora chiama le API Web, diventa un'applicazione client riservata. Ecco perché sono necessarie informazioni aggiuntive sulla registrazione: l'app deve condividere segreti (credenziali client) con la piattaforma di identità Microsoft.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Autorizzazioni delle API

Le app Web chiamano API per conto degli utenti per i quali è stato ricevuto il token di connessione. Le app Web devono richiedere autorizzazioni delegate. Per ulteriori informazioni, consultate [Aggiungere autorizzazioni per accedere alle API Web.](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Un'API Web che chiama le API Web: Configurazione del codice](scenario-web-api-call-api-app-configuration.md)
