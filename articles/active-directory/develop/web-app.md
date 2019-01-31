---
title: App Web in Azure Active Directory
description: Descrive che cosa sono le app Web e presenta le nozioni di base su flusso del protocollo, registrazione e scadenza del token per questo tipo di app.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.openlocfilehash: a54a2624d15aad2be6135b233be5900c8d83e567
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2019
ms.locfileid: "55095124"
---
# <a name="web-apps"></a>App Web

Le app Web sono applicazioni che autenticano un utente in un Web browser per un'applicazione Web. In questo scenario l'applicazione Web indirizza il browser dell'utente per l'accesso ad Azure AD. Tramite il browser dell'utente Azure AD restituisce una risposta di accesso che contiene attestazioni sull'utente in un token di sicurezza. Questo scenario supporta l'accesso mediante i protocolli OpenID Connect, SAML 2.0 e WS-Federation.

## <a name="diagram"></a>Diagramma

![Flusso di autenticazione da browser ad applicazione Web](./media/authentication-scenarios/web_browser_to_web_api.png)

## <a name="protocol-flow"></a>Flusso del protocollo

1. Quando un utente visita l'applicazione e deve eseguire l'accesso, viene reindirizzato tramite una richiesta di accesso all'endpoint di autenticazione in Azure AD.
1. L'utente accede nella pagina di accesso.
1. Se l'autenticazione ha esito positivo, Azure AD crea un token di autenticazione e restituisce una risposta di accesso all'URL di risposta dell'applicazione configurato nel portale di Azure. Per un'applicazione di produzione, questo URL di risposta deve essere HTTPS. Il token restituito include le attestazioni sull'utente e Azure AD richieste dall'applicazione per convalidare il token.
1. L'applicazione convalida il token usando le informazioni sulla chiave di firma pubblica e sull'autorità emittente disponibili nel documento metadati federazione per Azure AD. In seguito alla convalida del token da parte dell’applicazione, inizia una nuova sessione con l'utente. La sessione consente all'utente di accedere all'applicazione fino alla scadenza.

## <a name="code-samples"></a>Esempi di codice

Vedere gli esempi di codice per gli scenari da Web browser ad applicazione Web. Consultare spesso questa pagina perché vengono aggiunti regolarmente nuovi esempi.

## <a name="app-registration"></a>Registrazione delle app

Per registrare un'app Web, vedere [Registrare un'app con l'endpoint Azure AD v1.0](quickstart-v1-add-azure-ad-app.md).

* Tenant singolo: se si crea un'applicazione solo per la propria organizzazione, è necessario registrarla nella directory aziendale tramite il portale di Azure.
* Multi-tenant: se si crea un'applicazione che può essere usata da utenti esterni all'organizzazione, è necessario registrarla nella directory aziendale, ma anche nella directory di ogni organizzazione che userà l'applicazione. Per rendere disponibile l'applicazione nella propria directory, è possibile includere un processo di accesso per i clienti che permetta loro di concedere il consenso all'applicazione. Al momento dell'iscrizione all'applicazione, viene visualizzata una finestra di dialogo in cui sono indicate le autorizzazioni richieste dall'applicazione e quindi viene presentata l'opzione per il consenso. A seconda delle autorizzazioni richieste, è possibile che il consenso debba essere fornito da un amministratore dell'altra organizzazione. Quando l'utente o l'amministratore acconsente, l'applicazione viene registrata nella directory.

## <a name="token-expiration"></a>Scadenza del token

La sessione dell'utente scade al termine del periodo di validità del token emesso da Azure AD. Se necessario, l'applicazione può abbreviare questo periodo di validità, ad esempio disconnettendo gli utenti dopo un determinato tempo di inattività. Quando la sessione scade, all'utente viene chiesto di accedere di nuovo.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su altri [tipi di applicazione e scenari](app-types.md)
* [Nozioni di base sull'autenticazione](authentication-scenarios.md) in Azure AD
