---
title: Registrare un'app Web che chiama le API Web - Piattaforma di identità Microsoft Azure
description: Informazioni su come registrare un'app Web che chiama le API Web
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
ms.openlocfilehash: 5a57fcef3569734964bf6e8a41faa49800798f9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759058"
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
