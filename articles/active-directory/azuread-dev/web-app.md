---
title: App Web in Azure Active Directory
description: Descrive che cosa sono le app Web e presenta le nozioni di base su flusso del protocollo, registrazione e scadenza del token per questo tipo di app.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: e4a7fb72d40f5db65e8e30264e9d68b2727749e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154407"
---
# <a name="web-apps"></a>App Web

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Le app Web sono applicazioni che autenticano un utente in un Web browser per un'applicazione Web. In questo scenario, l'applicazione web indica al browser dell'utente di accedere ad Azure AD. Azure AD restituisce una risposta di accesso tramite il browser dell'utente, che contiene attestazioni sull'utente in un token di sicurezza. Questo scenario supporta l'accesso mediante i protocolli OpenID Connect, SAML 2.0 e WS-Federation.

## <a name="diagram"></a>Diagramma

![Flusso di autenticazione da browser ad applicazione Web](./media/authentication-scenarios/web-browser-to-web-api.png)

## <a name="protocol-flow"></a>Flusso del protocollo

1. Quando un utente visita l'applicazione e deve eseguire l'accesso, viene reindirizzato tramite una richiesta di accesso all'endpoint di autenticazione in Azure AD.
1. L'utente accede nella pagina di accesso.
1. Se l'autenticazione ha esito positivo, Azure AD crea un token di autenticazione e restituisce una risposta di accesso all'URL di risposta dell'applicazione configurato nel portale di Azure.If authentication is successful, Azure AD creates an authentication token and returns a sign-in response to the application's Reply URL that was configured in the Azure portal. Per un'applicazione di produzione, questo URL di risposta deve essere HTTPS. Il token restituito include le attestazioni sull'utente e Azure AD richieste dall'applicazione per convalidare il token.
1. L'applicazione convalida il token usando le informazioni sulla chiave di firma pubblica e sull'autorità emittente disponibili nel documento metadati federazione per Azure AD. In seguito alla convalida del token da parte dell’applicazione, inizia una nuova sessione con l'utente. La sessione consente all'utente di accedere all'applicazione fino alla scadenza.

## <a name="code-samples"></a>Esempi di codice

Vedere gli esempi di codice per gli scenari da Web browser ad applicazione Web. Consultare spesso questa pagina perché vengono aggiunti regolarmente nuovi esempi.

## <a name="app-registration"></a>Registrazione delle app

Per registrare un'app Web, vedere [Registrare un'app.](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)

* Singolo tenant: se si compila un'applicazione solo per l'organizzazione, è necessario registrarla nella directory aziendale tramite il portale di Azure.Single tenant - If you are building an application just for your organization, it must be registered in your company's directory by using the Azure portal.
* Multi-tenant: se si compila un'applicazione che può essere utilizzata da utenti esterni all'organizzazione, è necessario che venga registrata nella directory aziendale, ma deve anche essere registrata nella directory di ogni organizzazione che utilizzerà l'applicazione. Per rendere disponibile l'applicazione nella propria directory, è possibile includere un processo di accesso per i clienti che permetta loro di concedere il consenso all'applicazione. Al momento dell'iscrizione all'applicazione, viene visualizzata una finestra di dialogo in cui sono indicate le autorizzazioni richieste dall'applicazione e quindi viene presentata l'opzione per il consenso. A seconda delle autorizzazioni richieste, è possibile che il consenso debba essere fornito da un amministratore dell'altra organizzazione. Quando l'utente o l'amministratore acconsente, l'applicazione viene registrata nella directory.

## <a name="token-expiration"></a>Scadenza del token

La sessione dell'utente scade alla scadenza della durata del token rilasciato da Azure AD. Se necessario, l'applicazione può abbreviare questo periodo di validità, ad esempio disconnettendo gli utenti dopo un determinato tempo di inattività. Quando la sessione scade, all'utente viene chiesto di accedere di nuovo.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su altri [tipi di applicazione e scenari](app-types.md)
* Informazioni sulle nozioni di [base sull'autenticazione](v1-authentication-scenarios.md) di Azure ADLearn about the Azure AD authentication basics
