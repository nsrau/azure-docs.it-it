---
title: Applicazione a singola pagina (registrazione dell'app) - piattaforma delle identità Microsoft
description: Informazioni su come compilare un'applicazione a singola pagina (registrazione dell'App)
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
ms.openlocfilehash: b1faf4669dca2aaaf3f873e66f859473ccd99f10
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65074831"
---
# <a name="single-page-application---app-registration"></a>Applicazione a singola pagina: registrazione dell'app

Questa pagina illustra le specifiche di registrazione di app per un'applicazione a pagina singola (SPA).

Seguire i passaggi necessari per [registrare una nuova applicazione con la piattaforma Microsoft identity](quickstart-register-app.md)e selezionare gli account supportati per l'applicazione. Lo scenario SPA può supportare l'autenticazione con account personali nell'organizzazione o tutte le organizzazioni e gli account Microsoft personali.

Successivamente, per informazioni su aspetti specifici della registrazione dell'applicazione che si applicano alle applicazioni a pagina singola.

## <a name="register-a-redirect-uri"></a>Registrare un URI di reindirizzamento

Il flusso implicito invia i token in un reindirizzamento all'applicazione a pagina singola in esecuzione in un web browser. Pertanto, è un importante requisito per registrare un URI in cui l'applicazione può ricevere i token di reindirizzamento. Verificare che l'URI corrisponde esattamente con l'URI per l'applicazione di reindirizzamento.

Nel [portale di Azure](https://go.microsoft.com/fwlink/?linkid=2083908), passare all'applicazione registrata, nella **Authentication** pagina dell'applicazione, selezionare il **Web** piattaforma e immettere il valore del URI di reindirizzamento per l'applicazione nel **URI di reindirizzamento** campo.

## <a name="enable-the-implicit-flow"></a>Abilitare il flusso implicito

Nella stessa **Authentication** nella pagina **impostazioni avanzate**, è necessario abilitare anche il **concessione implicita**. Se l'applicazione sta eseguendo solo di accesso di utenti e ottenere i token ID, è sufficiente per abilitare **token ID** casella di controllo.

Se l'applicazione deve anche ottenere i token di accesso per chiamare le API, assicurarsi di abilitare la **token di accesso** anche la casella di controllo. Per altre informazioni, vedere [token ID](./id-tokens.md) e [i token di accesso](./access-tokens.md).

## <a name="api-permissions"></a>Autorizzazioni delle API

Le applicazioni a pagina singola possono chiamare le API per conto dell'utente connesso. È necessario richiedere autorizzazioni delegate. Per informazioni dettagliate, vedere [aggiungere le autorizzazioni per accedere alle API web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurazione del codice dell'App](scenario-spa-app-configuration.md)
