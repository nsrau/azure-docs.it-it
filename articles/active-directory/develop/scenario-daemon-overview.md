---
title: 'Daemon app chiamare le API web (panoramica): piattaforma delle identità Microsoft'
description: Informazioni su come compilare un'applicazione daemon che chiama le API web
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
ms.openlocfilehash: 578b7cdb38b7df3fab5885d773354a36f76a4cfb
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075881"
---
# <a name="scenario-daemon-application-that-calls-web-apis"></a>Scenario: Applicazione daemon che chiama l'API web

Scopri tutto quello che dovete compilare un'applicazione daemon che chiama le API web.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="overview"></a>Panoramica

L'applicazione può acquisire un token per chiamare un'API web per conto di se stessa (non per conto di un utente). Questo scenario è utile per le applicazioni daemon. Usa il standard di OAuth 2.0 [credenziali client](v2-oauth2-client-creds-grant-flow.md) concedere.

![App daemon](./media/scenario-daemon-app/daemon-app.svg)

Di seguito sono riportati alcuni esempi di casi d'uso per le app daemon:

- Le applicazioni Web che vengono utilizzati per eseguire il provisioning o amministrare gli utenti o processi in una directory in batch
- Applicazioni desktop, ad esempio (servizi di windows in Windows) o i processi di daemon in Linux che eseguono i processi batch o un servizio di sistema operativo in esecuzione in background
- API Web che è necessario modificare le directory, gli utenti non specifici

C'è un altro caso comune in cui le applicazioni non-daemon usano credenziali client: anche quando vengono usate per conto degli utenti, devono accedere a un'API web o una risorsa con la propria identità per motivi tecnici. Un esempio è l'accesso ai segreti in Key Vault o da un database SQL di Azure per una cache.

Applicazioni che acquisiscono un token per le proprie identità:

- Sono le applicazioni client riservato. Queste App, dato che accedono alle risorse indipendentemente da un utente, necessario dimostrare la propria identità. Si tratta anche le app sensibili piuttosto, che devono essere approvati dagli amministratori del tenant Azure Active Directory (Azure AD).
- Essere registrato un segreto (password applicazione o certificato) con Azure AD. Il segreto è passata durante la chiamata ad Azure AD per ottenere un token.

## <a name="specifics"></a>Specifiche

> [!IMPORTANT]
>
> - L'interazione dell'utente non è possibile con un'applicazione daemon. Un'applicazione daemon richiede la propria identità. Questo tipo di applicazione richiede un token di accesso usando la relativa identità applicazione e presentando relativo ID applicazione, le credenziali (password o certificato) e application URI dell'ID Azure AD. Al termine dell'autenticazione, il daemon riceve un token di accesso (e un token di aggiornamento) dall'endpoint della piattaforma di identità Microsoft, che viene quindi usato per chiamare l'API web (e viene aggiornato in base alle esigenze).
> - Poiché l'interazione dell'utente non è possibile, non sarà possibile consenso incrementale. Tutte le autorizzazioni API necessarie dovranno essere configurata al momento della registrazione dell'applicazione e il codice dell'applicazione richiede solo le autorizzazioni definite in modo statico. Questo significa anche che le applicazioni daemon non supportano il consenso incrementale.

Per gli sviluppatori, l'esperienza end-to-end per questo scenario presenta i seguenti aspetti:

- Applicazioni daemon possono funzionare solo nei tenant di Azure AD. Non avrebbe senso per compilare un'applicazione daemon che tenta di modificare gli account personali Microsoft. Se sei uno sviluppatore di app line-of-business (LOB), si creerà l'app daemon nel tenant. Se sei un fornitore di software indipendente, si potrebbe voler creare un'applicazione daemon multi-tenant. Dovrà essere concesso il consenso per ogni amministratore del tenant.
- Durante la [registrazione dell'applicazione](./scenario-daemon-app-registration.md), il **URI di risposta** non è necessaria. È necessario condividere i segreti o certificati con Azure AD ed è necessario richiedere le autorizzazioni delle applicazioni e concedere il consenso dell'amministratore di utilizzare tali autorizzazioni dell'app.
- Il [configurazione dell'applicazione](./scenario-daemon-app-configuration.md) deve fornire le credenziali del client come condiviso con Azure AD durante la registrazione dell'applicazione.
- Il [ambito](scenario-daemon-acquire-token.md#scopes-to-request) usato per acquisire un token con le credenziali client il flusso deve essere un ambito statico.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [App daemon: registrazione dell'app](./scenario-daemon-app-registration.md)
