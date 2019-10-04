---
title: App daemon che chiama le API Web (registrazione app)-piattaforma di identità Microsoft
description: Informazioni su come creare un'app daemon che chiama API Web-registrazione di app
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
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fddd95564e3683aaee067f0442573c41e93376dd
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71264385"
---
# <a name="daemon-app-that-calls-web-apis---app-registration"></a>App daemon che chiama le API Web-registrazione app

Per un'applicazione daemon, di seguito sono riportate le informazioni necessarie per la registrazione dell'app.

## <a name="supported-account-types"></a>Tipi di account supportati

Dato che le applicazioni daemon hanno senso solo nei tenant Azure AD, quando si crea l'applicazione è necessario scegliere:

- **account solo in questa directory aziendale**. Questa scelta è la causa più comune, perché le applicazioni daemon vengono in genere scritte da sviluppatori line-of-business (LOB).
- o **account in qualsiasi directory dell'organizzazione**. Questa scelta è consigliabile se si è un ISV che fornisce agli utenti uno strumento di utilità. Per approvare l'approvazione, saranno necessari gli amministratori dei tenant del cliente.

## <a name="authentication---no-reply-uri-needed"></a>Autenticazione-nessun URI di risposta necessario

Nel caso in cui l'applicazione client riservata usi **solo** il flusso di credenziali client, non è necessario registrare l'URL di risposta. Non è necessario per la configurazione o la costruzione dell'applicazione. Il flusso di credenziali client non lo usa.

## <a name="api-permissions---app-permissions-and-admin-consent"></a>Autorizzazioni per le API: autorizzazioni per le app e consenso dell'amministratore

Un'applicazione daemon può richiedere solo le autorizzazioni dell'applicazione per le API (non le autorizzazioni delegate). Nella pagina **autorizzazione API** per la registrazione dell'applicazione, dopo aver selezionato **Aggiungi un'autorizzazione** e scelto la famiglia di API, scegliere **Autorizzazioni applicazione**, quindi selezionare le autorizzazioni

![Autorizzazioni dell'app e consenso dell'amministratore](media/scenario-daemon-app/app-permissions-and-admin-consent.png)

> [!NOTE]
> L'API Web che si vuole chiamare deve definire le **autorizzazioni dell'applicazione (ruoli app)** , non le autorizzazioni delegate. Per informazioni dettagliate su come esporre un'API di questo tipo [, vedere API Web protetta: Registrazione dell'app: quando l'API Web viene chiamata da un'app daemon](https://docs.microsoft.com/en-us/azure/active-directory/develop/scenario-protected-web-api-app-registration#if-your-web-api-is-called-by-a-daemon-app)

Per le applicazioni daemon è necessario che l'amministratore del tenant preacconsente all'applicazione che chiama l'API Web. Questo consenso viene fornito nella stessa pagina di **autorizzazione dell'API** , da un amministratore del tenant che seleziona **concedi il consenso dell'amministratore per *l'organizzazione***

Se si è un ISV che compila un'applicazione multi-tenant, è opportuno controllare il paragrafo [distribuzione-caso di app daemon multi-tenant](scenario-daemon-production.md#deployment---case-of-multi-tenant-daemon-apps) .

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [App daemon-configurazione del codice dell'app](./scenario-daemon-app-configuration.md)
