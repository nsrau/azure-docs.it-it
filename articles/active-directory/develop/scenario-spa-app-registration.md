---
title: Registrare le app a pagina singola - Piattaforma di identità Microsoft Azure
description: Scopri come creare un'applicazione a pagina singola (registrazione dell'app)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 5b18b10748e0587920c6965f1d235376da928469
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701893"
---
# <a name="single-page-application-app-registration"></a>Applicazione a pagina singola: Registrazione app

Questa pagina spiega le specifiche di registrazione dell'app per un'applicazione a pagina singola (SPA).

Seguire i passaggi per [registrare una nuova applicazione con la piattaforma](quickstart-register-app.md)di identità Microsoft e selezionare gli account supportati per l'applicazione. Lo scenario SPA può supportare l'autenticazione con account nell'organizzazione o con qualsiasi organizzazione e account Microsoft personali.

Successivamente, apprendere gli aspetti specifici della registrazione delle applicazioni che si applicano alle applicazioni a pagina singola.

## <a name="register-a-redirect-uri"></a>Registrare un URI di reindirizzamentoRegister a redirect URI

Il flusso implicito invia i token in un reindirizzamento all'applicazione a pagina singola in esecuzione in un Web browser. È quindi importante registrare un URI di reindirizzamento in cui l'applicazione può ricevere i token. Assicurarsi che l'URI di reindirizzamento corrisponda esattamente all'URI dell'applicazione.

Nel [portale di Azure](https://go.microsoft.com/fwlink/?linkid=2083908)passare all'applicazione registrata. Nella pagina **Autenticazione** dell'applicazione selezionare la piattaforma **Web.** Immettere il valore dell'URI di reindirizzamento per l'applicazione nel campo **URI di reindirizzamento.**

## <a name="enable-the-implicit-flow"></a>Abilitare il flusso implicitoEnable the implicit flow

Nella stessa pagina **Autenticazione,** in **Impostazioni avanzate**, è inoltre necessario abilitare **la concessione implicita.** Se l'applicazione accede solo agli utenti e ottiene i token ID, è sufficiente selezionare la casella di controllo **Token ID.**

Se l'applicazione deve anche ottenere i token di accesso per chiamare le API, assicurarsi di selezionare anche la casella di controllo **Token** di accesso. Per ulteriori informazioni, vedere [token ID](./id-tokens.md) e token di [accesso](./access-tokens.md).

## <a name="api-permissions"></a>Autorizzazioni delle API

Le applicazioni a pagina singola possono chiamare le API per conto dell'utente connesso. Devono richiedere autorizzazioni delegate. Per informazioni dettagliate, vedere [Aggiungere autorizzazioni per accedere alle API Web.](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurazione del codice dell'app](scenario-spa-app-configuration.md)
