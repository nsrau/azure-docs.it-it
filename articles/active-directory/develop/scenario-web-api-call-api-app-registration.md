---
title: Registrare un'API Web che chiama API Web-piattaforma di identità Microsoft | Azure
description: Informazioni su come creare un'API Web che chiama le API Web downstream (registrazione dell'app).
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
ms.openlocfilehash: 790580160ec236d1923dc28b9990d2675c253b44
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94442685"
---
# <a name="a-web-api-that-calls-web-apis-app-registration"></a>API Web che chiama API Web: registrazione dell'app

Un'API Web che chiama le API Web downstream ha la stessa registrazione di un'API Web protetta. Pertanto, è necessario seguire le istruzioni in [API Web protetta: registrazione dell'app](scenario-protected-web-api-app-registration.md).

Poiché l'app Web chiama ora API Web, diventa un'applicazione client riservata. Ecco perché sono necessarie informazioni aggiuntive sulla registrazione: l'app deve condividere i segreti (credenziali client) con la piattaforma di identità Microsoft.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Autorizzazioni delle API

Le app Web chiamano le API per conto di utenti per cui è stata ricevuta la bearer token. Le app Web devono richiedere autorizzazioni delegate. Per altre informazioni, vedere [aggiungere autorizzazioni per accedere all'API Web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api).

## <a name="next-steps"></a>Passaggi successivi

Passare all'articolo successivo in questo scenario, configurazione del [codice dell'app](scenario-web-api-call-api-app-configuration.md).
