---
title: Registrare un'app Web che chiama API Web-piattaforma di identità Microsoft | Azure
description: Informazioni su come registrare un'app Web che chiama API Web
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
ms.openlocfilehash: bcb4fa5eb5889698174b65eb576c9caf0c82f857
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701672"
---
# <a name="web-app-that-calls-web-apis---app-registration"></a>App Web che chiama le API Web-registrazione app

Un'app Web che chiama API Web ha la stessa registrazione degli utenti che accedono ad app Web. Sarà quindi necessario seguire le istruzioni in app Web per l'accesso [degli utenti-registrazione dell'app](scenario-web-app-sign-user-app-registration.md)

Tuttavia, poiché l'app Web chiama ora API Web, diventa un'applicazione client riservata. Questo è il motivo per cui è necessaria una registrazione aggiuntiva: è necessario condividere i segreti (credenziali client) con la piattaforma di identità Microsoft.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Autorizzazioni delle API

Le applicazioni Web chiamano le API per conto dell'utente che ha eseguito l'accesso. Devono richiedere autorizzazioni delegate. Per informazioni dettagliate, vedere [aggiungere autorizzazioni per accedere alle API Web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurazione del codice dell'app](scenario-web-app-call-api-app-configuration.md)
