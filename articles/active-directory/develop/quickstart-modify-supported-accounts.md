---
title: Modificare gli account supportati da un'applicazione registrata con Microsoft Identity Platform | Azure
description: Configurare un'applicazione registrata con Microsoft Identity Platform per modificare gli utenti o gli account che possono accedere all'applicazione.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/08/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: aragra, lenalepa, sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2c52bd0bf6c1e385248bd379a8001756d28ac2d
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65413591"
---
# <a name="quickstart-modify-the-accounts-supported-by-an-application"></a>Guida introduttiva: Modificare gli account supportati da un'applicazione

Quando si registra un'applicazione in Microsoft Identity Platform, potrebbe essere opportuno consentire l'accesso all'applicazione solo agli utenti dell'organizzazione. In alternativa, potrebbe anche essere opportuno che l'applicazione sia accessibile da utenti di organizzazioni esterne oppure sia da utenti di organizzazioni esterne che da utenti non necessariamente parte di un'organizzazione (account personali).

In questa guida introduttiva verrà illustrato come modificare la configurazione dell'applicazione per modificare gli utenti o gli account che possono accedervi.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, assicurarsi di soddisfare questi prerequisiti:

* Acquisire informazioni su [autorizzazioni supportate e consenso](v2-permissions-and-consent.md), la cui conoscenza è importante per creare applicazioni che dovranno essere usate da altri utenti o applicazioni.
* Avere un tenant in cui sono registrate applicazioni.
  * Se non si hanno app registrate, vedere come [registrare applicazioni con Microsoft Identity Platform](quickstart-register-app.md).

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Accedere al portale di Azure e selezionare l'app

Per poter configurare l'app, seguire prima questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Se l'account consente di accedere a più tenant, selezionare l'account nell'angolo in alto a destra e impostare la sessione del portale sul tenant di Azure Active Directory desiderato.
1. Nel riquadro di spostamento sinistro selezionare il servizio **Azure Active Directory** e quindi **Registrazioni app**.
1. Trovare e selezionare l'applicazione che si vuole configurare. Dopo la selezione dell'app verrà visualizzata la pagina **Panoramica** o la pagina di registrazione principale dell'applicazione.
1. Seguire la procedura per [modificare la registrazione dell'applicazione per supportare account diversi](#change-the-application-registration-to-support-different-accounts).
1. Se si ha un'applicazione a pagina singola, [abilitare la concessione implicita OAuth 2.0](#enable-oauth-20-implicit-grant-for-single-page-applications).

## <a name="change-the-application-registration-to-support-different-accounts"></a>Modificare la registrazione dell'applicazione per supportare account diversi

Se si scrive un'applicazione che si vuole rendere disponibile a clienti o partner esterni all'organizzazione, è necessario aggiornare la definizione dell'applicazione nel portale di Azure.

> [!IMPORTANT]
> Per le applicazioni multi-tenant, Azure AD richiede che l'URI dell'ID applicazione sia univoco a livello globale. L'URI dell'ID App è uno dei modi in cui un'applicazione viene identificata nei messaggi di protocollo. Per un'applicazione a tenant singolo, è sufficiente che l'URI dell'ID app sia univoco all'interno del tenant. Per un'applicazione multi-tenant, è necessario che sia univoco a livello globale in modo da Azure AD possa trovare l'applicazione in tutti i tenant. L'univocità globale viene applicata richiedendo che l'URI dell'ID App abbia un nome host corrispondente a un dominio verificato del tenant di Azure AD. Se il nome del tenant è contoso.onmicrosoft.com, ad esempio, un URI dell'ID app valido sarà https://contoso.onmicrosoft.com/myapp. Se il tenant dispone del dominio verificato contoso.com, anche https://contoso.com/myapp è un URI di ID app valido. Se l'URI dell'ID App non segue questo modello, l'impostazione di un'applicazione come multi-tenant ha esito negativo.

### <a name="to-change-who-can-access-your-application"></a>Per modificare gli utenti che hanno accesso all'applicazione

1. Nella pagina **Panoramica** dell'app selezionare la sezione **Autenticazione** e modificare il valore selezionato in **Tipi di account supportati**.
    * Selezionare **Solo gli account in questa directory** se si crea un'applicazione line-of-business. Questa opzione non è disponibile se l'applicazione non è registrata in una directory.
    * Selezionare **Account in qualsiasi directory organizzativa** se si preferisce includere tutti i clienti aziendali o di istituti di istruzione.
    * Selezionare **Account in qualsiasi directory organizzativa e account Microsoft personali** per includere il set più ampio possibile di clienti.
1. Selezionare **Salva**.

## <a name="enable-oauth-20-implicit-grant-for-single-page-applications"></a>Abilitare la concessione implicita OAuth 2.0 per le applicazioni a pagina singola

Le applicazioni a pagina singola (SPA) sono in genere strutturate con un front-end JavaScript eseguito nel browser, che chiama il back-end dell'API Web dell'applicazione per eseguirne la logica di business. Per le applicazioni a singola pagina ospitate in Azure AD, è possibile usare la concessione implicita OAuth 2.0 per autenticare l'utente con Azure AD e ottenere un token da usare per chiamate protette dal client JavaScript dell'applicazione all'API Web di back-end.

Dopo che l'utente ha concesso il consenso, lo stesso protocollo di autenticazione può essere usato per ottenere token per proteggere le chiamate tra il client e altre risorse dell'API Web configurate per l'applicazione. Per altre informazioni sulla concessione implicita di autorizzazioni e per stabilire se sia adatta allo scenario dell'applicazione, vedere le informazioni sul flusso di concessione implicita OAuth 2.0 in Azure AD [v1.0](v1-oauth2-implicit-grant-flow.md) e [v2.0](v2-oauth2-implicit-grant-flow.md).

Per impostazione predefinita, la concessione implicita OAuth 2.0 è disabilitata per le applicazioni. È possibile abilitare la concessione implicita OAuth 2.0 per l'applicazione seguendo la procedura illustrata di seguito.

### <a name="to-enable-oauth-20-implicit-grant"></a>Per abilitare la concessione implicita OAuth 2.0

1. Nella pagina **Panoramica** dell'app selezionare la sezione **Autenticazione**.
1. In **Impostazioni avanzate** individuare la sezione **Concessione implicita**.
1. Selezionare **Token ID**, **Token di accesso** o entrambe le opzioni.
1. Selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi

Vedere le altre guide introduttive correlate sulla gestione delle app:

* [Registrare un'applicazione con Microsoft Identity Platform](quickstart-register-app.md)
* [Configurare un'applicazione client per l'accesso ad API Web](quickstart-configure-app-access-web-apis.md)
* [Configurare un'applicazione per l'esposizione di API Web](quickstart-configure-app-expose-web-apis.md)
* [Rimuovere un'applicazione registrata con Microsoft Identity Platform](quickstart-remove-app.md)

Per altre informazioni su due oggetti di Azure AD che rappresentano un'applicazione registrata e la relazione tra essi, vedere [Oggetti applicazione e oggetti entità servizio](app-objects-and-service-principals.md).

Per altre informazioni sulle linee guida sulla personalizzazione da seguire per lo sviluppo di applicazioni con Azure Active Directory, vedere [Linee guida sulla personalizzazione delle applicazioni](howto-add-branding-in-azure-ad-apps.md).
