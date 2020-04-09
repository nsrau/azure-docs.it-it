---
title: Registrare un'app Web che chiama le API Web - Piattaforma di identità Microsoft Azure
description: Informazioni su come registrare un'app Web che chiama le API Web
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
ms.openlocfilehash: 8cb7d86bd419563363779c499962c81f0c59e3b6
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881877"
---
# <a name="a-web-app-that-calls-web-apis-app-registration"></a>Un'app Web che chiama le API Web: Registrazione dell'app

Un'app Web che chiama le API Web ha la stessa registrazione di un'app Web che consente di accedere agli utenti. Quindi, seguire le istruzioni in [Un'app web che accede agli utenti: registrazione dell'app](scenario-web-app-sign-user-app-registration.md).

Tuttavia, poiché l'app Web ora chiama anche le API Web, diventa un'applicazione client riservata. Ecco perché è necessaria una registrazione aggiuntiva. L'app deve condividere le credenziali client, o *segreti,* con la piattaforma di identità Microsoft.

[!INCLUDE [Registration of client secrets](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Autorizzazioni delle API

Le app Web chiamano API per conto dell'utente connesso. A tale scopo, devono richiedere *autorizzazioni delegate*. Per informazioni dettagliate, vedere [Aggiungere autorizzazioni per accedere alle API Web.](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Un'app Web che chiama le API Web: Configurazione del codice](scenario-web-app-call-api-app-configuration.md)
