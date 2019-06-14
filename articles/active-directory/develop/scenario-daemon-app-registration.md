---
title: Daemon app chiamare le API web (registrazione dell'app) - piattaforma delle identità Microsoft
description: Informazioni su come compilare un'applicazione daemon che chiama le API - web registrazione dell'app
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
ms.openlocfilehash: 79a355ab226e56a3dde1df5369deda5142d47848
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65076241"
---
# <a name="daemon-app-that-calls-web-apis---app-registration"></a>App daemon che chiama l'API - registrazione dell'app web

Per un'applicazione daemon, ecco cosa occorre sapere quando la registrazione dell'app.

## <a name="supported-account-types"></a>Tipi di account supportati

Dato che le applicazioni daemon solo ha senso nel tenant di Azure AD, quando si crea l'applicazione si dovrà scegliere:

- entrambi **gli account in questa directory dell'organizzazione solo**. Questa scelta è il caso più comune, come le applicazioni daemon sono scritti generalmente dagli sviluppatori di line-of-business (LOB).
- oppure **gli account in qualsiasi directory dell'organizzazione**. Se si è un ISV che fornisce uno strumento di utilità per i clienti verranno apportate questa scelta. È necessario agli amministratori di tenant del cliente di approvarlo.

## <a name="authentication---no-reply-uri-needed"></a>Autenticazione - nessun URI di risposta necessaria

Nel caso in cui viene utilizzata l'applicazione client riservato **solo** le credenziali client del flusso, l'URL di risposta non desidera essere registrati. È non necessaria per la costruzione/configurazione dell'applicazione. Il flusso di credenziali client non lo usa.

## <a name="api-permissions---app-permissions-and-admin-consent"></a>Autorizzazioni API - app autorizzazioni e consenso dell'amministratore

Un'applicazione daemon è possibile richiedere solo autorizzazioni dell'applicazione per le API (non delegate autorizzazioni). Nel **l'autorizzazione API** pagina per la registrazione dell'applicazione, dopo aver selezionato **aggiungere un'autorizzazione** scelto la famiglia di API, scegliere **autorizzazioni applicazione**, e quindi selezionare le autorizzazioni

![App autorizzazioni e consenso dell'amministratore](media/scenario-daemon-app/app-permissions-and-admin-consent.png)

Applicazioni daemon richiedono dispone un amministratore del tenant di pre-fornire il consenso per l'applicazione chiama l'API web. Viene fornito il consenso nella stessa **l'autorizzazione API** pagina, un amministratore di tenant selezionando **concedere il consenso dell'amministratore per *dell'organizzazione***

Se si è un ISV che crea un'applicazione multi-tenant, si desidera controllare la [distribuzione - case di App multi-tenant daemon](scenario-daemon-production.md#deployment---case-of-multi-tenant-daemon-apps) paragrafo.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [App daemon - configurazione del codice app](./scenario-daemon-app-configuration.md)
