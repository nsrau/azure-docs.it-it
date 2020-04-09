---
title: Registra le app daemon che chiamano api Web - Piattaforma di identità Microsoft Azure
description: Informazioni su come creare un'app daemon che chiama le API Web - Registrazione dell'app
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 508101ad615dd96559b1c68a61be7c08772545db
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885481"
---
# <a name="daemon-app-that-calls-web-apis---app-registration"></a>App Daemon che chiama API Web - registrazione dell'app

Per un'applicazione daemon, ecco cosa devi sapere quando registri l'app.

## <a name="supported-account-types"></a>Tipi di account supportati

Le applicazioni Daemon hanno senso solo nei tenant di Azure AD. Pertanto, quando si crea l'applicazione, è necessario scegliere una delle seguenti opzioni:

- **Solo gli account in questa directory dell'organizzazione**. Questa scelta è quella più comune perché le applicazioni daemon sono in genere scritte da sviluppatori line-of-business (LOB).
- **Account in qualsiasi directory dell'organizzazione**. Farai questa scelta se sei un ISV che fornisce uno strumento di utilità ai tuoi clienti. Per approvarlo, è necessario che gli amministratori tenant dei clienti vengano approvati.

## <a name="authentication---no-reply-uri-needed"></a>Autenticazione - nessun URI di risposta necessario

Nel caso in cui l'applicazione client riservata utilizzi *solo* il flusso di credenziali client, l'URI di risposta non deve essere registrato. Non è necessario per la configurazione o la costruzione dell'applicazione. Il flusso delle credenziali del client non lo usa.

## <a name="api-permissions---app-permissions-and-admin-consent"></a>Autorizzazioni API - autorizzazioni per le app e consenso dell'amministratore

Un'applicazione daemon può richiedere solo le autorizzazioni dell'applicazione per le API (non le autorizzazioni delegate). Nella pagina **Autorizzazioni API** per la registrazione dell'applicazione, dopo aver selezionato **Aggiungi un'autorizzazione** e scelto la famiglia di API, scegliere **Autorizzazioni applicazione**, quindi selezionare le autorizzazioni.

![Autorizzazioni per le app e consenso dell'amministratore](media/scenario-daemon-app/app-permissions-and-admin-consent.png)

> [!NOTE]
> L'API Web che si desidera chiamare deve definire *le autorizzazioni dell'applicazione (ruoli dell'app),* non le autorizzazioni delegate. Per informazioni dettagliate su come esporre un'API di questo tipo, vedere [API Web protetta: registrazione dell'app: quando l'API Web viene chiamata da un'app daemon](scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app).

Le applicazioni Daemon richiedono che un amministratore tenant pre-consenso per l'applicazione che chiama l'API web. Gli amministratori tenant forniscono questo consenso nella stessa pagina di **autorizzazione API** selezionando **Concedi il consenso *dell'amministratore* alla nostra organizzazione**

Se sei un ISV che crea un'applicazione multi-tenant, dovresti leggere la sezione [Distribuzione - caso di app daemon multi-tenant](scenario-daemon-production.md#deployment---multitenant-daemon-apps).

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [App Daemon - configurazione del codice dell'app](./scenario-daemon-app-configuration.md)
