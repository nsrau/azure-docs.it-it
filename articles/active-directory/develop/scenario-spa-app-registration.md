---
title: Registrare applicazioni a pagina singola-piattaforma di identità Microsoft | Azure
description: Informazioni su come creare un'applicazione a singola pagina (registrazione dell'app)
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 03115db0ad286c7a5c24590906d8e3715e43bac7
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74962441"
---
# <a name="single-page-application-app-registration"></a>Applicazione a pagina singola: registrazione dell'app

Questa pagina illustra le specifiche di registrazione delle app per un'applicazione a singola pagina (SPA).

Seguire i passaggi per [registrare una nuova applicazione con la piattaforma di identità Microsoft](quickstart-register-app.md)e selezionare gli account supportati per l'applicazione. Lo scenario SPA può supportare l'autenticazione con account dell'organizzazione o qualsiasi organizzazione e account Microsoft personali.

Successivamente, vengono illustrati gli aspetti specifici della registrazione dell'applicazione che si applicano alle applicazioni a singola pagina.

## <a name="register-a-redirect-uri"></a>Registrare un URI di Reindirizzamento

Il flusso implicito invia i token in un reindirizzamento all'applicazione a singola pagina in esecuzione in un Web browser. È quindi importante registrare un URI di reindirizzamento in cui l'applicazione può ricevere i token. Verificare che l'URI di reindirizzamento corrisponda esattamente all'URI per l'applicazione.

Nella [portale di Azure](https://go.microsoft.com/fwlink/?linkid=2083908)passare all'applicazione registrata. Nella pagina **autenticazione** dell'applicazione selezionare la piattaforma **Web** . Immettere il valore dell'URI di reindirizzamento per l'applicazione nel campo **URI di reindirizzamento** .

## <a name="enable-the-implicit-flow"></a>Abilitare il flusso implicito

Nella stessa pagina di **autenticazione** , in **Impostazioni avanzate**, è necessario abilitare anche la **concessione implicita**. Se l'applicazione sta effettuando l'accesso solo agli utenti e ricevendo i token ID, è sufficiente selezionare la casella di controllo **token ID** .

Se l'applicazione deve anche ottenere i token di accesso per chiamare le API, assicurarsi di selezionare la casella di controllo **token di accesso** . Per altre informazioni, vedere [token ID](./id-tokens.md) e [token di accesso](./access-tokens.md).

## <a name="api-permissions"></a>Autorizzazioni delle API

Le applicazioni a singola pagina possono chiamare le API per conto dell'utente che ha eseguito l'accesso. Devono richiedere autorizzazioni delegate. Per informazioni dettagliate, vedere [aggiungere autorizzazioni per accedere alle API Web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurazione del codice dell'app](scenario-spa-app-configuration.md)
