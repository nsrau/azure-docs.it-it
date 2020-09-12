---
title: Registrare un'app Web che chiama API Web-piattaforma di identità Microsoft | Azure
description: Informazioni su come registrare un'app Web che chiama API Web
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
ms.openlocfilehash: f94a3da96243e30faa90277ce86efec037f54672
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2020
ms.locfileid: "89436468"
---
# <a name="a-web-app-that-calls-web-apis-app-registration"></a>Un'app Web che chiama le API Web: registrazione dell'app

Un'app Web che chiama le API Web ha la stessa registrazione di un'app Web che firma gli utenti in. Quindi, seguire le istruzioni in [un'app Web per l'accesso degli utenti: registrazione dell'app](scenario-web-app-sign-user-app-registration.md).

Tuttavia, poiché l'app Web ora chiama anche API Web, diventa un'applicazione client riservata. Per questo motivo è necessaria una registrazione aggiuntiva. L'app deve condividere le credenziali client o i *segreti*con la piattaforma di identità Microsoft.

[!INCLUDE [Registration of client secrets](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Autorizzazioni delle API

Le app Web chiamano le API per conto dell'utente che ha eseguito l'accesso. A tale scopo, devono richiedere *autorizzazioni delegate*. Per informazioni dettagliate, vedere [aggiungere autorizzazioni per accedere all'API Web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [App Web che chiama le API Web: Configurazione del codice](scenario-web-app-call-api-app-configuration.md)
