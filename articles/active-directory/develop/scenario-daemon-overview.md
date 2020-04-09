---
title: Crea un'app daemon che chiama le API Web - Piattaforma di identità Microsoft . Azure
description: Informazioni su come creare un'app daemon che chiama le API Web
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/31/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: df06c4c55941f4424d6b90d2846af17bf055b2e4
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885464"
---
# <a name="scenario-daemon-application-that-calls-web-apis"></a>Scenario: applicazione Daemon che chiama le API Web

Scopri tutto ciò che ti serve per creare un'applicazione daemon che chiama le API web.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="overview"></a>Panoramica

L'applicazione può acquisire un token per chiamare un'API Web per conto di se stessa (non per conto di un utente). Questo scenario è utile per le applicazioni daemon. Utilizza la concessione di [credenziali client](v2-oauth2-client-creds-grant-flow.md) OAuth 2.0 standard.

![App daemon](./media/scenario-daemon-app/daemon-app.svg)

Ecco alcuni esempi di casi d'uso per le app daemon:

- Applicazioni Web utilizzate per eseguire il provisioning o l'amministrazione di utenti o di eseguire processi batch in una directory
- Applicazioni desktop (ad esempio i servizi di Windows in Windows o i processi daemon in Linux) che eseguono processi batch o un servizio del sistema operativo eseguito in background
- API Web che devono modificare le directory, non utenti specifici

C'è un altro caso comune in cui le applicazioni non daemon utilizzano le credenziali client: anche quando agiscono per conto degli utenti, hanno bisogno di accedere a un'API web o una risorsa con la propria identità per motivi tecnici. Un esempio è l'accesso ai segreti in Azure Key Vault o in un database SQL di Azure per una cache.

Applicazioni che acquisiscono un token per le proprie identità:Applications that acquire a token for their own identities:

- Sono applicazioni client riservate. Queste app, dato che accedono alle risorse indipendentemente dagli utenti, devono dimostrare la propria identità. Sono anche app piuttosto sensibili. Devono essere approvati dagli amministratori tenant di Azure Active Directory (Azure AD).
- È stato registrato un segreto (password dell'applicazione o certificato) con Azure AD. Questo segreto viene passato durante la chiamata ad Azure AD per ottenere un token.

## <a name="specifics"></a>Specifiche

> [!IMPORTANT]
>
> - Gli utenti non possono interagire con un'applicazione daemon. Un'applicazione daemon richiede la propria identità. Questo tipo di applicazione richiede un token di accesso usando l'identità dell'applicazione e presentando l'ID applicazione, le credenziali (password o il certificato) e l'URI dell'ID applicazione ad Azure AD. Dopo l'autenticazione, il daemon riceve un token di accesso (e un token di aggiornamento) dall'endpoint della piattaforma di identità Microsoft.After successful authentication, the daemon receives an access token (and a refresh token) from the Microsoft identity platform endpoint. Questo token viene quindi utilizzato per chiamare l'API Web (e viene aggiornato in base alle esigenze).
> - Poiché gli utenti non possono interagire con le applicazioni daemon, il consenso incrementale non è possibile. Tutte le autorizzazioni API necessarie devono essere configurate al momento della registrazione dell'applicazione. Il codice dell'applicazione richiede solo autorizzazioni definite staticamente. Ciò significa anche che le applicazioni daemon non supporteranno il consenso incrementale.

Per gli sviluppatori, l'esperienza end-to-end per questo scenario presenta gli aspetti seguenti:For developers, the end-to-end experience for this scenario has the following aspects:

- Le applicazioni Daemon possono funzionare solo nei tenant di Azure AD. Non avrebbe senso costruire un'applicazione daemon che tenta di manipolare gli account personali Microsoft. Se sei uno sviluppatore di app line-of-business (LOB), creerai l'app daemon nel tuo tenant. Se sei un ISV, potresti voler creare un'applicazione daemon multi-tenant. Ogni amministratore tenant dovrà fornire il consenso.
- Durante [la registrazione dell'applicazione,](./scenario-daemon-app-registration.md)l'URI di risposta non è necessario. È necessario condividere segreti o certificati o asserzioni firmate con Azure AD. È inoltre necessario richiedere le autorizzazioni dell'applicazione e concedere il consenso di amministratore per utilizzare tali autorizzazioni.
- La [configurazione dell'applicazione](./scenario-daemon-app-configuration.md) deve fornire le credenziali client condivise con Azure AD durante la registrazione dell'applicazione.
- [L'ambito](scenario-daemon-acquire-token.md#scopes-to-request) utilizzato per acquisire un token con il flusso di credenziali client deve essere un ambito statico.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [App Daemon - registrazione dell'app](./scenario-daemon-app-registration.md)
