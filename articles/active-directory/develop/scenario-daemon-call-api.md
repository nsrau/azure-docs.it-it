---
title: Daemon app chiamare le API web (chiamare le API web) - piattaforma delle identità Microsoft
description: Informazioni su come compilare un'applicazione daemon che chiama web API (chiamare le API web)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
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
ms.openlocfilehash: aff375f996126d9e8b64361fc0e5673c25d30c19
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65076271"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>App daemon che le chiamate API - web chiama un'API web dall'app

Un'app daemon può chiamare un'API web da un'applicazione daemon .NET o chiamare le API web preapprovata diversi.

## <a name="calling-a-web-api-from-a-net-daemon-application"></a>Chiamare un'API web da un'applicazione daemon .NET

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

<!--
More includes will come later for Python and Java
-->

## <a name="calling-several-apis"></a>La chiamata a varie API

Per le app daemon, l'API web che si chiamano necessario pre-approvazione. Non saranno qualsiasi consenso incrementale con le app daemon (non vi è alcun intervento dell'utente). L'amministratore del tenant deve di pre-autorizzare l'applicazione e tutte le autorizzazioni API. Se si desidera chiamare le API diverse, è necessario acquisire un token per ogni risorsa, ogni chiamata tempo `AcquireTokenForClient`. MSAL utilizzerà la cache dei token dell'applicazione per evitare chiamate ai servizi non necessari.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [App daemon - spostamento nell'ambiente di produzione](./scenario-daemon-production.md)