---
title: Registrazione dell'API Web protetta Azure
titleSuffix: Microsoft identity platform
description: Scopri come creare un'API Web protetta e le informazioni necessarie per registrare l'app.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 997dd98d35b74effe5d195f9a781fa0935286ee9
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537220"
---
# <a name="protected-web-api-app-registration"></a>API Web protetta: registrazione dell'app

Questo articolo illustra le specifiche della registrazione dell'app per un'API Web protetta.

Per la procedura comune di registrazione di un'app, vedere [Guida introduttiva: Registrare un'applicazione con la piattaforma](quickstart-register-app.md)di identità Microsoft .

## <a name="accepted-token-version"></a>Versione del token accettata

L'endpoint della piattaforma di identità Microsoft può rilasciare token v1.0 e token v2.0.The Microsoft identity platform endpoint can issue v1.0 tokens and v2.0 tokens. Per ulteriori informazioni su questi token, vedere Token di [accesso](access-tokens.md).

La versione del token accettata dipende dal valore **Tipi di account supportati** scelti quando si crea l'applicazione.

- Se il valore di Tipi di **account supportati** è Account in qualsiasi **directory dell'organizzazione e account Microsoft personali (ad esempio Skype, Xbox, Outlook.com)**, la versione accettata del token è v2.0.
- In caso contrario, la versione del token accettata è v1.0.Otherwise, the accepted token version is v1.0.

Dopo aver creato l'applicazione, è possibile determinare o modificare la versione del token accettata attenendosi alla seguente procedura:

1. Nel portale di Azure selezionare l'app e quindi **selezionare Manifesto**.
1. Trovare la proprietà **accessTokenAcceptedVersion** nel manifesto. Il valore predefinito della proprietà è 2.The property's default value is 2.
1. Il valore specifica ad Azure Active Directory (Azure AD) la versione del token accettata dall'API Web.The value specifies to Azure Active Directory (Azure AD) which token version the web API accepts.
    - Se il valore è 2, l'API Web accetta token v2.0.
    - Se il valore è **null**, l'API Web accetta token v1.0.
1. Se è stata modificata la versione del token, selezionare **Salva**.

> [!NOTE]
> L'API Web specifica la versione del token accettata. Quando un client richiede un token per l'API Web dall'endpoint Microsoft Identity Platform (v2.0), il client ottiene un token che indica la versione del token accettata dall'API Web.

## <a name="no-redirect-uri"></a>Nessun URI di reindirizzamento

Non è necessario che le API Web registrino un URI di reindirizzamento perché nessun utente ha eseguito l'accesso in modo interattivo.

## <a name="exposed-api"></a>API esposta

Altre impostazioni specifiche delle API Web sono l'API esposta e gli ambiti esposti.

### <a name="application-id-uri-and-scopes"></a>URI e ambiti dell'ID applicazione

Gli ambiti hanno `resourceURI/scopeName`in genere il formato . Per Microsoft Graph, gli ambiti hanno collegamenti. Ad esempio, `User.Read` è `https://graph.microsoft.com/user.read`un collegamento per .

Durante la registrazione dell'app, è necessario definire questi parametri:During app registration, you need to define these parameters:

- URI della risorsa
- Uno o più ambiti
- Uno o più ruoli dell'app

Per impostazione predefinita, il portale di registrazione `api://{clientId}`dell'applicazione consiglia di utilizzare l'URI della risorsa. Questo URI è univoco ma non leggibile. Se si modifica l'URI, assicurarsi che il nuovo valore sia univoco.

Per le applicazioni client, gli ambiti vengono visualizzati come autorizzazioni delegate e i ruoli dell'app vengono visualizzati come *autorizzazioni dell'applicazione per l'API* Web.To client applications, scopes show up as *delegated permissions* and app roles show up as application permissions for your web API.

Gli ambiti vengono visualizzati anche nella finestra di consenso presentata agli utenti dell'app. Quindi è necessario fornire le stringhe corrispondenti che descrivono l'ambito:

- Come visto da un utente.
- Come visto da un amministratore tenant, che può concedere il consenso di amministratore.

### <a name="exposing-delegated-permissions-scopes"></a>Esposizione di autorizzazioni delegate (ambiti)

1. Selezionare **Esporre un'API** nella registrazione dell'applicazione.
1. Selezionare **Aggiungi un ambito**.
1. Se richiesto, accettare l'URI`api://{clientId}`dell'ID applicazione proposto ( ) selezionando **Salva e continua**.
1. Specificare i valori seguenti:Specify these values:
    - Selezionare **Nome ambito** e immettere **access_as_user**.
    - Seleziona **Chi può acconsentire** e assicurati che sia selezionato **Amministratore e utenti.**
    - Selezionare **Il nome visualizzato** consenso amministratore e immettere Access **TodoListService come utente**.
    - Selezionare **Descrizione consenso amministratore** e immettere **Accesses l'API Web TodoListService come utente**.
    - Selezionare **Il nome visualizzato Consenso utente** e immettere Access **TodoListService come utente**.
    - Selezionare **Descrizione consenso utente** e immettere **Accesses l'API Web TodoListService come utente**.
    - Mantenere il valore **Stato** impostato su **Abilitato**.
 1. Selezionare **Aggiungi ambito**.

### <a name="if-your-web-api-is-called-by-a-daemon-app"></a>Se l'API Web viene chiamata da un'app daemon

In questa sezione viene illustrato come registrare l'API Web protetta in modo che le app daemon possano chiamarla in modo sicuro.

- Dichiarare ed esporre solo *le autorizzazioni dell'applicazione* perché le app daemon non interagiscono con gli utenti. Le autorizzazioni delegate non avrebbero senso.
- Gli amministratori tenant possono richiedere ad Azure AD di rilasciare token API Web solo alle applicazioni registrate per accedere a una delle autorizzazioni dell'applicazione dell'API.

#### <a name="exposing-application-permissions-app-roles"></a>Esposizione delle autorizzazioni dell'applicazione (ruoli dell'app)Exposing application permissions (app roles)

Per esporre le autorizzazioni dell'applicazione, è necessario modificare il manifesto.

1. Nella registrazione dell'applicazione selezionare **Manifesto**.
1. Per modificare il manifesto, individuare l'impostazione `appRoles` e aggiungere ruoli applicazione. Le definizioni di ruolo vengono fornite nel blocco JSON di esempio seguente.
1. Lasciare `allowedMemberTypes` impostato `"Application"` su solo.
1. Assicurarsi `id` che sia un GUID univoco.
1. Assicurarsi `displayName` `value` che non contengano spazi.
1. Salvare il manifesto.

Nell'esempio seguente viene `appRoles`illustrato il `id` contenuto di , dove il valore di può essere qualsiasi GUID univoco.

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

#### <a name="ensuring-that-azure-ad-issues-tokens-for-your-web-api-to-only-allowed-clients"></a>Verificare che Azure AD emetta token per l'API Web solo ai client consentitiEnsuring that Azure AD issues tokens for your web API to only allowed clients

L'API Web controlla il ruolo dell'app. Questo ruolo è il modo di uno sviluppatore di software per esporre le autorizzazioni dell'applicazione. È anche possibile configurare Azure AD per rilasciare token API solo per le app approvate dall'amministratore tenant per l'accesso alle API.

Per aggiungere questa maggiore sicurezza:

1. Vai alla pagina **Panoramica** dell'app per la registrazione dell'app.
1. In **Applicazione gestita nella directory locale**selezionare il collegamento con il nome dell'app. L'etichetta per questa selezione potrebbe essere troncata. Ad esempio, è possibile che **l'applicazione gestita sia visualizzata in ...**

   > [!NOTE]
   >
   > Quando si seleziona questo collegamento, si passa alla pagina **Panoramica applicazione enterprise.** Questa pagina è associata all'entità servizio per l'applicazione nel tenant in cui è stata creata. Puoi accedere alla pagina di registrazione dell'app utilizzando il pulsante Indietro del browser.

1. Selezionare la pagina **Proprietà** nella sezione **Gestisci** delle pagine Applicazione aziendale.
1. Se si vuole che Azure AD consenta l'accesso all'API Web solo da determinati client, impostare **Assegnazione utente obbligatoria?** su **Sì**.

   > [!IMPORTANT]
   >
   > Se si imposta **Assegnazione utente richiesta?** su **Sì**, Azure AD controlla le assegnazioni di ruolo dell'app di un client quando richiede un token di accesso all'API Web. Se il client non è assegnato ad alcun ruolo dell'app, Azure AD restituirà il messaggio \<di\> errore "invalid_client: AADSTS501051: Il nome dell'applicazione non è assegnato a un ruolo per l'API \<\>Web ".
   >
   > Se si mantiene **Assegnazione utente obbligatoria?** impostata su No , Azure AD non controllera le assegnazioni di ruolo dell'app quando un client richiede un token di accesso per l'API Web.If you keep User assignment required? set to **No**, Azure AD won't check the app role assignments when a client requests an access token for your web API. Qualsiasi client daemon, ovvero qualsiasi client che usa il flusso di credenziali client, può ottenere un token di accesso per l'API semplicemente specificandone il pubblico. Qualsiasi applicazione può accedere all'API senza dover richiedere le autorizzazioni per tale applicazione.
   >
   > Tuttavia, come spiegato nella sezione precedente, l'API Web può sempre verificare che l'applicazione disponga del ruolo corretto, autorizzato dall'amministratore tenant. L'API esegue questa verifica convalidando che il token di accesso dispone di un'attestazione di ruoli e che il valore per questa attestazione sia corretto. Nell'esempio JSON precedente, `access_as_application`il valore è .

1. Selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurazione del codice dell'app](scenario-protected-web-api-app-configuration.md)
