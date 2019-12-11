---
title: Creare un'app daemon che chiama API Web-piattaforma di identità Microsoft | Azure
description: Informazioni su come creare un'app daemon che chiama API Web
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
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70a8c97ee1e10d53cd8da9687a9109eb4ce8ae3d
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965569"
---
# <a name="scenario-daemon-application-that-calls-web-apis"></a>Scenario: applicazione daemon che chiama API Web

Scopri tutto quello che ti serve per creare un'applicazione daemon che chiama le API Web.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="overview"></a>Panoramica

L'applicazione può acquisire un token per chiamare un'API Web per conto di se stesso (non per conto di un utente). Questo scenario è utile per le applicazioni daemon. Usa la concessione di [credenziali client](v2-oauth2-client-creds-grant-flow.md) OAuth 2,0 standard.

![App daemon](./media/scenario-daemon-app/daemon-app.svg)

Di seguito sono riportati alcuni esempi di casi d'uso per le app daemon:

- Applicazioni Web utilizzate per il provisioning o l'amministrazione di utenti o per l'esecuzione di processi batch in una directory
- Applicazioni desktop (ad esempio, servizi Windows in Windows o processi daemon in Linux) che eseguono processi batch o un servizio del sistema operativo in esecuzione in background
- API Web che devono modificare le directory, non utenti specifici

Un altro caso comune in cui le applicazioni non daemon usano le credenziali client: anche quando agiscono per conto degli utenti, devono accedere a un'API Web o a una risorsa con la loro identità per motivi tecnici. Un esempio è l'accesso ai segreti nell'insieme di credenziali delle chiavi o un database SQL di Azure per una cache.

Applicazioni che acquisiscono un token per le proprie identità:

- sono applicazioni client riservate. Queste app, dato che accedono alle risorse indipendentemente da un utente, devono dimostrare la propria identità. Si tratta anche di app piuttosto sensibili, che devono essere approvate dagli amministratori del tenant di Azure Active Directory (Azure AD).
- Hanno registrato un segreto (password dell'applicazione o certificato) con Azure AD. Questo segreto viene passato durante la chiamata a Azure AD per ottenere un token.

## <a name="specifics"></a>Specifiche

> [!IMPORTANT]
>
> - L'interazione con l'utente non è possibile con un'applicazione daemon. Un'applicazione daemon richiede una propria identità. Questo tipo di applicazione richiede un token di accesso usando la relativa identità dell'applicazione e presentando l'ID applicazione, le credenziali (password o certificato) e l'URI dell'ID applicazione per Azure AD. Al termine dell'autenticazione, il daemon riceve un token di accesso (e un token di aggiornamento) dall'endpoint della piattaforma di identità Microsoft, che viene quindi usato per chiamare l'API Web (e viene aggiornato in base alle esigenze).
> - Poiché l'interazione con l'utente non è possibile, il consenso incrementale non sarà possibile. Tutte le autorizzazioni API necessarie devono essere configurate alla registrazione dell'applicazione e il codice dell'applicazione richiede solo le autorizzazioni definite in modo statico. Ciò significa anche che le applicazioni daemon non supporteranno il consenso incrementale.

Per gli sviluppatori, l'esperienza end-to-end per questo scenario presenta gli aspetti seguenti:

- Le applicazioni daemon possono funzionare solo in Azure AD tenant. Non avrebbe senso creare un'applicazione daemon che tenti di modificare gli account personali Microsoft. Gli sviluppatori di app line-of-business (LOB) creeranno l'app daemon nel tenant. Se si è un ISV, potrebbe essere necessario creare un'applicazione daemon multi-tenant. Dovrà essere acconsentito da ogni amministratore tenant.
- Durante la [registrazione dell'applicazione](./scenario-daemon-app-registration.md), l' **URI di risposta** non è necessario. È necessario condividere i segreti o i certificati o le asserzioni firmate con Azure AD ed è necessario richiedere le autorizzazioni per le applicazioni e concedere il consenso dell'amministratore per l'uso di tali autorizzazioni dell'app.
- La [configurazione dell'applicazione](./scenario-daemon-app-configuration.md) deve fornire le credenziali client come condivise con Azure ad durante la registrazione dell'applicazione.
- L' [ambito](scenario-daemon-acquire-token.md#scopes-to-request) usato per acquisire un token con il flusso di credenziali client deve essere un ambito statico.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [App daemon-registrazione app](./scenario-daemon-app-registration.md)
