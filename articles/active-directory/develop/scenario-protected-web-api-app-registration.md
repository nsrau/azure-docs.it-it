---
title: API Web protetta-registrazione app
titleSuffix: Microsoft identity platform
description: Informazioni su come creare un'API Web protetta e le informazioni necessarie per registrare l'app.
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
ms.openlocfilehash: 0c905dfd86fd80b9e55aa7bd5a9b9b03f277570c
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802550"
---
# <a name="protected-web-api-app-registration"></a>API Web protetta: registrazione dell'app

Questo articolo illustra le specifiche della registrazione dell'app per un'API Web protetta.

Per la procedura comune per la registrazione di un'app, vedere [Guida introduttiva: registrare un'applicazione con la piattaforma di identità Microsoft](quickstart-register-app.md) .

## <a name="accepted-token-version"></a>Versione del token accettata

L'endpoint della piattaforma di identità Microsoft può emettere due tipi di token: i token v 1.0 e i token v 2.0. Per altre informazioni su questi token, vedere [token di accesso](access-tokens.md). La versione del token accettata dipende dai **tipi di account supportati** scelti durante la creazione dell'applicazione:

- Se il valore dei **tipi di account supportati** è **account in qualsiasi directory organizzativa e account Microsoft personali (ad esempio Skype, Xbox, Outlook.com)** , la versione del token accettata sarà v 2.0.
- In caso contrario, la versione del token accettata sarà v 1.0.

Dopo aver creato l'applicazione, è possibile determinare o modificare la versione del token accettato attenendosi alla seguente procedura:

1. Nella portale di Azure selezionare l'app e quindi selezionare il **manifesto** per l'app.
2. Nel manifesto cercare **"accessTokenAcceptedVersion"** . Si noti che il valore è **2**. Questa proprietà specifica di Azure Active Directory (Azure AD) che l'API Web accetta i token v 2.0. Se il valore è **null**, la versione del token accettata è v 1.0.
3. Se è stata modificata la versione del token, selezionare **Salva**.

> [!NOTE]
> L'API Web specifica quale versione del token (v 1.0 o v 2.0) accetta. Quando i client richiedono un token per l'API Web dall'endpoint della piattaforma Microsoft Identity Platform (v 2.0), otterranno un token che indica quale versione viene accettata dall'API Web.

## <a name="no-redirect-uri"></a>Nessun URI di Reindirizzamento

Le API Web non devono registrare un URI di reindirizzamento perché nessun utente è connesso in modo interattivo.

## <a name="expose-an-api"></a>Esporre un'API

Un'altra impostazione specifica per le API Web è l'API esposta e gli ambiti esposti.

### <a name="resource-uri-and-scopes"></a>URI di risorsa e ambiti

Gli ambiti sono in genere nel formato `resourceURI/scopeName`. Per Microsoft Graph, gli ambiti hanno tasti di scelta rapida, ad esempio `User.Read`. Questa stringa è un tasto di scelta rapida per `https://graph.microsoft.com/user.read`.

Durante la registrazione dell'app, è necessario definire i parametri seguenti:

- URI della risorsa. Per impostazione predefinita, il portale di registrazione delle applicazioni consiglia di usare `api://{clientId}`. Questo URI di risorsa è univoco, ma non è leggibile. È possibile modificarlo, ma assicurarsi che il nuovo valore sia univoco.
- Uno o più *ambiti*. Per le applicazioni client, verranno visualizzate come *autorizzazioni delegate* per l'API Web.
- Uno o più *ruoli dell'app*. Per le applicazioni client, verranno visualizzate come *autorizzazioni dell'applicazione* per l'API Web.

Gli ambiti vengono visualizzati anche nella schermata di consenso presentata agli utenti finali dell'app. Sarà quindi necessario fornire le stringhe corrispondenti che descrivono l'ambito:

- Come osservato dall'utente finale.
- Come osservato dall'amministratore del tenant, che può concedere il consenso dell'amministratore.

### <a name="exposing-delegated-permissions-scopes"></a>Esposizione delle autorizzazioni delegate (ambiti)

1. Selezionare la sezione **esporre un'API** nella registrazione dell'applicazione.
1. Selezionare **Aggiungi un ambito**.
1. Se richiesto, accettare l'URI dell'ID applicazione (`api://{clientId}`) proposto selezionando **Salva e continua**.
1. Immettere i parametri seguenti:
      - Per **nome ambito**usare **access_as_user**.
      - Per **chi può acconsentire**, assicurarsi che **gli amministratori e gli utenti** siano selezionati.
      - In **nome visualizzato consenso amministratore**immettere **Access TodoListService come utente**.
      - Nella **Descrizione del consenso dell'amministratore**immettere **accede all'API Web TodoListService come utente**.
      - In **nome visualizzato del consenso utente**immettere **Access TodoListService come utente**.
      - In **Descrizione consenso utente**immettere **accede all'API Web TodoListService come utente**.
      - Mantieni **stato** impostato su **abilitato**.
      - Selezionare **Aggiungi ambito**.

### <a name="if-your-web-api-is-called-by-a-daemon-app"></a>Se l'API Web viene chiamata da un'app daemon

In questa sezione si apprenderà come registrare l'API Web protetta per poterla chiamare in modo sicuro dalle app daemon.

- È necessario esporre le *autorizzazioni dell'applicazione*. Verranno dichiarate solo le autorizzazioni dell'applicazione perché le app daemon non interagiscono con gli utenti, quindi le autorizzazioni delegate non hanno senso.
- Gli amministratori tenant possono richiedere Azure Active Directory (Azure AD) per rilasciare i token per l'API Web solo per le applicazioni che hanno effettuato la registrazione per accedere a una delle autorizzazioni dell'applicazione API Web.

#### <a name="exposing-application-permissions-app-roles"></a>Esposizione delle autorizzazioni dell'applicazione (ruoli app)

Per esporre le autorizzazioni dell'applicazione, è necessario modificare il manifesto.

1. Nella registrazione dell'applicazione per l'applicazione selezionare **manifesto**.
1. Modificare il manifesto individuando l'impostazione `appRoles` e aggiungendo uno o più ruoli applicazione. La definizione del ruolo viene fornita nel blocco JSON di esempio seguente. Lasciare il `allowedMemberTypes` impostato solo su `"Application"`. Verificare che il `id` sia un GUID univoco e che `displayName` e `value` non contengano spazi.
1. Salvare il manifesto.

Nell'esempio seguente viene illustrato il contenuto di `appRoles`. Il `id` può essere un GUID univoco.

```JSon
"appRoles": [
    {
    "allowedMemberTypes": [ "Application" ],
    "description": "Accesses the TodoListService-Cert as an application.",
    "displayName": "access_as_application",
    "id": "ccf784a6-fd0c-45f2-9c08-2f9d162a0628",
    "isEnabled": true,
    "lang": null,
    "origin": "Application",
    "value": "access_as_application"
    }
],
```

#### <a name="ensuring-that-azure-ad-issues-tokens-for-your-web-api-to-only-allowed-clients"></a>Verifica che Azure AD rilascia i token per l'API Web solo ai client autorizzati

L'API Web verifica il ruolo app. Questo è il modo per sviluppatori di esporre le autorizzazioni dell'applicazione. Tuttavia, è anche possibile configurare Azure AD per emettere un token per l'API Web solo per le app approvate dall'amministratore del tenant per accedere all'API. Per aggiungere questa maggiore sicurezza:

1. Nella pagina **Panoramica** app per la registrazione dell'app selezionare il collegamento con il nome dell'app **in applicazione gestita nella directory locale**. Il titolo di questo campo potrebbe essere troncato. È possibile, ad esempio, vedere l' **applicazione gestita in...**

   > [!NOTE]
   >
   > Quando si seleziona questo collegamento, si passa alla pagina di **Panoramica dell'applicazione aziendale** associata all'entità servizio per l'applicazione nel tenant in cui è stata creata. È possibile tornare alla pagina di registrazione dell'app usando il pulsante indietro del browser.

1. Selezionare la pagina **Proprietà** nella sezione **Gestisci** delle pagine dell'applicazione aziendale.
1. Se si desidera Azure AD per consentire l'accesso all'API Web solo da determinati client, impostare **assegnazione utente obbligatoria?** su **Sì**.

   > [!IMPORTANT]
   >
   > Se si imposta l' **assegnazione utente obbligatoria?** su **Sì**, Azure ad verificherà le assegnazioni di ruolo dell'app dei client quando richiedono un token di accesso per l'API Web. Se il client non è assegnato ad alcun ruolo app, Azure AD restituirà l'errore `invalid_client: AADSTS501051: Application <application name> is not assigned to a role for the <web API>`.
   >
   > Se si mantiene l' **assegnazione utente obbligatoria?** impostare su **No**, *Azure ad non verificherà le assegnazioni di ruolo dell'app quando un client richiede un token di accesso per l'API Web*. Qualsiasi client daemon, ovvero qualsiasi client che utilizza il flusso di credenziali client, sarà in grado di ottenere un token di accesso per l'API semplicemente specificandone i destinatari. Qualsiasi applicazione sarà in grado di accedere all'API senza dover richiedere le autorizzazioni. Tuttavia, l'API Web può sempre, come illustrato nella sezione precedente, verificare che l'applicazione disponga del ruolo corretto (autorizzato dall'amministratore del tenant). L'API esegue questa verifica convalidando che il token di accesso disponga di un'attestazione Roles e che il valore di questa attestazione sia corretto. (In questo caso, il valore è `access_as_application`).

1. Selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurazione del codice dell'app](scenario-protected-web-api-app-configuration.md)
