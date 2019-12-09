---
title: Registrare un'API Web che chiama API Web-piattaforma di identità Microsoft | Azure
description: Informazioni su come creare un'API Web che chiama le API Web downstream (registrazione dell'app)
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
ms.openlocfilehash: cb5f4763e13935b99564bfcb6d8b6e7f463ed59e
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74919801"
---
# <a name="web-api-that-calls-web-apis---app-registration"></a>API Web che chiama API Web-registrazione app

Un'API Web che chiama le API Web downstream ha la stessa registrazione di un'API Web protetta. Pertanto, è necessario seguire le istruzioni riportate in [API Web protette-registrazione app](scenario-protected-web-api-app-registration.md).

Tuttavia, poiché l'app Web chiama ora API Web, diventa un'applicazione client riservata. Questo è il motivo per cui sono necessarie informazioni aggiuntive sulla registrazione: l'app deve condividere i segreti (credenziali client) con la piattaforma di identità Microsoft.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Autorizzazioni delle API

Le applicazioni Web chiamano le API per conto dell'utente per il quale è stata ricevuta la bearer token. Devono richiedere autorizzazioni delegate. Per informazioni dettagliate, vedere [aggiungere autorizzazioni per accedere alle API Web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurazione del codice dell'app](scenario-web-api-call-api-app-configuration.md)
