---
title: Registrare un'API Web che chiama le API Web - Piattaforma di identità Microsoft Azure
description: Scopri come creare un'API Web che chiama le API Web downstream (registrazione dell'app).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 048f7d41bd9d106121859e6b1fc013258067af9c
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885124"
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
