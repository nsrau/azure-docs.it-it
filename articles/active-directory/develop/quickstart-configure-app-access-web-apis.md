---
title: Configurare un'applicazione per l'accesso ad API Web (anteprima) | Azure
description: Informazioni su come configurare un'applicazione registrata con Microsoft Identity Platform in modo da includere gli URI di reindirizzamento, le credenziali o le autorizzazioni per accedere ad API Web.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/25/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.openlocfilehash: a0082be25ec4ad326e4473cbe69f9ff029898f5b
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988807"
---
# <a name="quickstart-configure-a-client-application-to-access-web-apis-preview"></a>Guida introduttiva: Configurare un'applicazione client per l'accesso ad API Web (anteprima)

Per consentire a un'applicazione Web/client riservata di partecipare a un flusso di concessioni di autorizzazioni che richiede l'autenticazione (e ottenere un token di accesso), è necessario definire credenziali protette. Il metodo di autenticazione predefinito supportato dal portale di Azure è ID client + chiave privata.

Prima che un client possa accedere a un'API Web esposta da un'applicazione della risorsa (come l'API Microsoft Graph), inoltre, il framework di consenso assicura che il client ottenga la concessione di autorizzazioni necessaria in base alle autorizzazioni richieste. Per impostazione predefinita, tutte le applicazioni possono scegliere le autorizzazioni dall'API Microsoft Graph. L'[autorizzazione "Consente l'accesso e la lettura del profilo utente" dell'API Graph](https://developer.microsoft.com/graph/docs/concepts/permissions_reference#user-permissions) è selezionata per impostazione predefinita. È possibile scegliere fra [due tipi di autorizzazioni](developer-glossary.md#permissions) per ogni API Web desiderata:

* **Autorizzazioni di tipo applicazione**: l'applicazione client deve accedere all'API Web direttamente come se stessa (senza contesto utente). Questo tipo di autorizzazione richiede il consenso dell'amministratore e non è disponibile per applicazioni client pubbliche (desktop e per dispositivi mobili).
* **Autorizzazioni delegate**: l'applicazione client deve accedere all'API Web come utente connesso, ma con accesso limitato dall'autorizzazione selezionata. Questo tipo di autorizzazione può essere concesso da un utente, a meno che l'autorizzazione richieda il consenso dell'amministratore.

  > [!NOTE]
  > L'aggiunta di un'autorizzazione delegata a un'applicazione non concede automaticamente il consenso all'utente all'interno del tenant. Gli utenti devono comunque concedere manualmente il consenso per le autorizzazioni delegate aggiuntive in fase di esecuzione, a meno che l'amministratore non conceda il consenso per conto di tutti gli utenti.

In questa guida introduttiva verrà illustrato come configurare l'app per eseguire queste operazioni:

* [Aggiungere URI di reindirizzamento all'applicazione](#add-redirect-uris-to-your-application)
* [Aggiungere credenziali all'applicazione Web](#add-credentials-to-your-web-application)
* [Aggiungere autorizzazioni per accedere ad API Web](#add-permissions-to-access-web-apis)

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, assicurarsi di soddisfare questi prerequisiti:

* Acquisire informazioni su [autorizzazioni supportate e consenso](v2-permissions-and-consent.md), la cui conoscenza è importante per creare applicazioni che dovranno essere usate da altri utenti o applicazioni.
* Avere un tenant in cui sono registrate applicazioni.
  * Se non si hanno app registrate, vedere come [registrare applicazioni con Microsoft Identity Platform](quickstart-register-app.md).
* Acconsentire esplicitamente all'esperienza di anteprima per le registrazioni di app nel portale di Azure. Le procedure illustrate in questa guida introduttiva corrispondono alla nuova interfaccia utente e funzionano solo se si è acconsentito esplicitamente all'esperienza di anteprima.

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Accedere al portale di Azure e selezionare l'app

Per poter configurare l'app, seguire prima questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Se l'account consente di accedere a più tenant, selezionare l'account nell'angolo in alto a destra e impostare la sessione del portale sul tenant di Azure AD desiderato.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory** e quindi **Registrazioni app (anteprima)**.
1. Trovare e selezionare l'applicazione che si vuole configurare. Dopo la selezione dell'app verrà visualizzata la pagina **Panoramica** o la pagina di registrazione principale dell'applicazione.
1. Seguire questa procedura per configurare l'applicazione per l'accesso ad API Web: 
    * [Aggiungere URI di reindirizzamento all'applicazione](#add-redirect-uris-to-your-application)
    * [Aggiungere credenziali all'applicazione Web](#add-credentials-to-your-web-application)
    * [Aggiungere autorizzazioni per accedere ad API Web](#add-permissions-to-access-web-apis)

## <a name="add-redirect-uris-to-your-application"></a>Aggiungere URI di reindirizzamento all'applicazione

[![Aggiungere URI di reindirizzamento personalizzati per app client pubbliche e Web](./media/quickstart-update-azure-ad-app-preview/authentication-redirect-uris-expanded.png)](./media/quickstart-update-azure-ad-app-preview/authentication-redirect-uris-expanded.png#lightbox)

Per aggiungere un URI di reindirizzamento all'applicazione:

1. Nella pagina **Panoramica** dell'app selezionare la sezione **Autenticazione**.

1. Per aggiungere un URI di reindirizzamento personalizzato per applicazioni client pubbliche e Web, seguire questa procedura:

    1. Individuare la sezione **URI di reindirizzamento**.
    1. Selezionare il tipo di applicazione che si sta creando, ossia **Web** o **Client pubblico (per dispositivi mobili e desktop)**.
    1. Immettere l'URI di reindirizzamento per l'applicazione.
        * Per le applicazioni Web, specificare l'URL di base dell'applicazione. L'URL per un'applicazione Web in esecuzione nel computer locale potrebbe essere ad esempio http://localhost:31544. Gli utenti useranno questo URL per accedere a un'applicazione client Web.
        * Per le applicazione pubbliche, specificare l'URI usato da Azure AD per restituire le risposte dei token. Immettere un valore specifico dell'applicazione, ad esempio https://MyFirstApp.

1. Per scegliere tra gli URI di reindirizzamento suggeriti per client pubblici (per dispositivi mobili e desktop), seguire questa procedura:

    1. Individuare la sezione **URI di reindirizzamento suggeriti per client pubblici (dispositivi mobili, desktop)**.
    1. Selezionare uno o più URI di reindirizzamento appropriati per l'applicazione usando le caselle di controllo.

## <a name="add-credentials-to-your-web-application"></a>Aggiungere credenziali all'applicazione Web

[![Aggiungere certificati e segreti client](./media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets-expanded.png)](./media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets-expanded.png#lightbox)

Per aggiungere una credenziale all'applicazione Web:

1. Nella pagina **Panoramica** dell'app selezionare la sezione **Certificati e segreti**.

1. Per aggiungere un certificato, seguire questa procedura:

    1. Selezionare **Carica certificato**.
    1. Selezionare il file da caricare. Il tipo di file deve essere uno dei seguenti: .cer, .pem, .crt.
    1. Selezionare **Aggiungi**.

1. Per aggiungere un segreto client, seguire questa procedura:

    1. Selezionare **Nuovo segreto client**.
    1. Aggiungere una descrizione per il segreto client.
    1. Selezionare una durata.
    1. Selezionare **Aggiungi**.

> [!NOTE]
> Dopo il salvataggio delle modifiche alla configurazione, nella colonna più a destra verrà visualizzato il valore del segreto client. **Assicurarsi di copiare il valore** per usarlo nel codice dell'applicazione client, perché non sarà più accessibile dopo aver lasciato la pagina.

## <a name="add-permissions-to-access-web-apis"></a>Aggiungere autorizzazioni per accedere ad API Web

[![Aggiungere autorizzazioni API](./media/quickstart-update-azure-ad-app-preview/api-permissions-expanded.png)](./media/quickstart-update-azure-ad-app-preview/api-permissions-expanded.png#lightbox)

Per aggiungere autorizzazioni per accedere ad API di risorsa dal client:

1. Nella pagina **Panoramica** dell'app selezionare **Autorizzazioni API**.
1. Selezionare il pulsante **Aggiungi un'autorizzazione**.
1. Per impostazione predefinita, la visualizzazione consente di selezionare tra **API Microsoft**. Selezionare la sezione delle API a cui si è interessati:
    * **API Microsoft** consente di selezionare le autorizzazioni per API Microsoft come Microsoft Graph.
    * **API usate dall'organizzazione** consente di selezionare le autorizzazioni per le API che sono state esposte dall'organizzazione o con cui l'organizzazione ha eseguito l'integrazione.
    * **API personali** consente di selezionare le autorizzazioni per le API esposte dall'utente.
1. Al termine della selezione delle API, verrà visualizzata la pagina **Richiedi le autorizzazioni dell'API**. Se l'API espone autorizzazioni sia delegate che di tipo applicazione, selezionare il tipo di autorizzazione necessario all'applicazione.
1. Al termine, selezionare **Aggiungi autorizzazioni**. Verrà nuovamente visualizzata la pagina **Autorizzazioni API**, con le autorizzazioni salvate e aggiunte alla tabella.

## <a name="next-steps"></a>Passaggi successivi

Vedere le altre guide introduttive correlate sulla gestione delle app:

* [Registrare un'applicazione con Microsoft Identity Platform](quickstart-register-app.md)
* [Configurare un'applicazione per l'esposizione di API Web](quickstart-configure-app-expose-web-apis.md)
* [Modificare gli account supportati da un'applicazione](quickstart-modify-supported-accounts.md)
* [Rimuovere un'applicazione registrata con Microsoft Identity Platform](quickstart-remove-app.md)

Per altre informazioni su due oggetti di Azure AD che rappresentano un'applicazione registrata e la relazione tra essi, vedere [Oggetti applicazione e oggetti entità servizio](app-objects-and-service-principals.md).

Per altre informazioni sulle linee guida sulla personalizzazione da seguire per lo sviluppo di applicazioni con Azure Active Directory, vedere [Linee guida sulla personalizzazione delle applicazioni](howto-add-branding-in-azure-ad-apps.md).