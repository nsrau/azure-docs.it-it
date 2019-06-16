---
title: API web - registrazione di app protetta | Azure
description: Informazioni su come compilare un'API Web protetta e le informazioni necessarie per registrare l'app.
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
ms.openlocfilehash: 22fe71c38678ae789a93ecbc956f24f0b0ebeb01
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111121"
---
# <a name="protected-web-api---app-registration"></a>API - registrazione di app web protetta

Questo articolo illustra le specifiche di registrazione di app per un'API web protetta.

Vedere [Avvio rapido: Registrare un'applicazione con la piattaforma delle identità Microsoft](quickstart-register-app.md) per i comuni passaggi su come registrare l'applicazione.

## <a name="accepted-token-version"></a>Versione del token accettato

L'endpoint di Microsoft identity platform può rilasciare due tipi di token: token v1.0 e v2.0 token. Altre informazioni su questi token nel [token di accesso](access-tokens.md). La versione del token accettata varia a seconda le **tipi di account supportati** scelto durante la creazione dell'applicazione:

- Se il valore di **tipi di account supportati** viene **gli account in qualsiasi directory dell'organizzazione e gli account Microsoft personali (ad esempio Skype, Xbox, Outlook.com)** , la versione accettata token sarà v2.0.
- In caso contrario, la versione accettata token sarà v1.0.

Dopo aver creato l'applicazione, è possibile modificare la versione del token accettata, seguire questi passaggi:

1. Nel portale di Azure, selezionare l'app e quindi selezionare il **manifesto** per l'app.
2. Nel manifesto, cercare **"accessTokenAcceptedVersion"** e riscontrerebbe che il relativo valore è **2**. Questa proprietà consente ad Azure AD sa che l'API web accetta token v2.0. Se si tratta **null**, la versione accettata token sarà v1.0.
3. Selezionare **Salva**.

> [!NOTE]
> Spetta all'API web per decidere quale token versione (versione 1.0 o 2.0) accetta. Quando i client richiedono un token per l'API web usando l'endpoint 2.0 di Microsoft identity platform, si otterrà un token che indica quale versione è stata accettata dall'API web.

## <a name="no-redirect-uri"></a>Nessun URI di reindirizzamento

Le API Web non necessario registrare un URI di reindirizzamento, perché nessun utente è connesso in modo interattivo.

## <a name="expose-an-api"></a>Esporre un'API

Un'altra impostazione specifica per le API web è l'API esposta e sugli ambiti esposti.

### <a name="resource-uri-and-scopes"></a>URI della risorsa e gli ambiti

Gli ambiti sono in genere nel formato `resourceURI/scopeName`. Dispongono di tali tasti, ad esempio per Microsoft Graph, gli ambiti `User.Read`, ma questa stringa è solo una scorciatoia per `https://graph.microsoft.com/user.read`.

Durante la registrazione dell'app, è necessario definire i parametri seguenti:

- La risorsa URI - per impostazione predefinita il portale di registrazione applicazione suggerisce che è possibile usare `api://{clientId}`. Questo URI di risorsa sia univoco, ma non umano leggibile. È possibile modificarla, ma assicurarsi che sia univoco.
- Uno o più **ambiti** (per le applicazioni client, verranno visualizzati come **autorizzazioni delegate** per l'API Web)
- Uno o più **ruoli dell'app** (per le applicazioni client, verranno visualizzati come **autorizzazioni applicazione** per l'API Web)

Gli ambiti vengono inoltre visualizzati nella schermata di consenso che viene presentata agli utenti finali che usano l'applicazione. Pertanto, è necessario fornire le stringhe corrispondenti che descrivono l'ambito:

- Stessa forma visualizzata all'utente finale
- Come illustrato dall'amministratore tenant, che può concedere il consenso dell'amministratore

### <a name="how-to-expose-delegated-permissions-scopes"></a>Come esporre le autorizzazioni delegate (ambiti)

1. Selezionare il **esporre un'API** sezione nella registrazione dell'applicazione, e:
   1. Selezionare **Aggiungi un ambito**.
   1. Se richiesto, accettare l'URI dell'ID applicazione proposta (api :// {clientId}) selezionando **Salva e continua**.
   1. Immettere i parametri seguenti:
      - Per la **nome ambito**, usare `access_as_user`.
      - Per **che può fornire il consenso**, assicurarsi che le **gli amministratori e utenti** opzione è selezionata.
      - Nelle **nome visualizzazione il consenso dell'amministratore**, tipo `Access TodoListService as a user`.
      - Nelle **descrizione del consenso dell'amministratore**, tipo `Accesses the TodoListService Web API as a user`.
      - Nelle **nome visualizzato dell'utente il consenso**, tipo `Access TodoListService as a user`.
      - Nelle **descrizione del consenso dell'utente**, tipo `Accesses the TodoListService Web API as a user`.
      - Mantieni **lo stato** impostata su **abilitato**.
      - Selezionare **Aggiungi ambito**.

### <a name="case-where-your-web-api-is-called-by-daemon-application"></a>Casi in cui viene chiamata l'API Web da applicazione daemon

In questo paragrafo, verrà illustrato come registrare l'API Web protetta in modo che possa essere chiamato in modo sicuro dalle applicazioni daemon:

- è necessario esporre **autorizzazioni applicazione**. Autorizzazioni per l'applicazione verrà dichiarato solo come applicazioni daemon non interagiscono con gli utenti e pertanto le autorizzazioni delegate non avrebbe senso.
- gli amministratori tenant possono richiedere ad Azure AD di rilasciare token per l'App Web per solo le applicazioni che hanno registrato che desiderano accedere a una delle autorizzazioni delle App Web API.

#### <a name="how-to-expose-application-permissions-app-roles"></a>Come esporre le autorizzazioni dell'applicazione (ruoli app)

Per esporre le autorizzazioni dell'applicazione, è necessario modificare il manifesto.

1. Nella registrazione dell'applicazione per l'applicazione, fare clic su **manifesto**.
1. Modificare il manifesto individuando il `appRoles` impostazione e l'aggiunta di uno o più ruoli applicazione. Nel blocco JSON di esempio riportato di seguito viene fornita la definizione di ruolo.  Lasciare il `allowedMemberTypes` su "Application" solo. Assicurarsi che il **id** è un guid univoco e **displayName** e **valore** non può contenere spazi.
1. Salvare il manifesto.

Il contenuto del `appRoles` riportato di seguito (il `id` può essere qualsiasi GUID univoco)

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

#### <a name="how-to-ensure-that-azure-ad-issues-tokens-for-your-web-api-only-to-allowed-clients"></a>Come garantire che Azure AD rilascia token per l'API Web solo a client è consentito

Controlla il ruolo dell'app, ovvero il modo per gli sviluppatori di eseguire questa operazione, l'API Web. Ma è anche possibile configurare Azure Active Directory per emettere un token per l'API Web solo alle applicazioni che sono state approvate dall'amministratore tenant per accedere all'API. Per aggiungere questa protezione aggiuntiva:

1. Nell'app **Overview** pagina per la registrazione dell'app, selezionare il collegamento ipertestuale con il nome dell'applicazione nel **applicazione gestita nella directory locale**. Il titolo per questo campo può essere troncato. È possibile, ad esempio, leggere: `Managed application in ...`

   > [!NOTE]
   >
   > Quando si seleziona questo collegamento sposterà il **Panoramica di applicazione Enterprise** pagina associata all'entità servizio per l'applicazione nel tenant in cui è stato creato. È possibile passare nuovamente alla pagina di registrazione dell'app con il pulsante Indietro del browser.

1. Selezionare il **le proprietà** pagina il **Gestisci** sezione delle pagine di applicazione Enterprise
1. Se si vuole AAD per applicare l'accesso all'API Web da solo alcuni client, impostare **assegnazione utente obbligatoria?** al **Yes**.

   > [!IMPORTANT]
   >
   > Impostando **assegnazione utente obbligatoria?** al **Yes**, AAD controllerà le assegnazioni di ruolo dei client quando richiedono un token di accesso per l'API Web. Se non è stato assegnato il client per qualsiasi AppRoles, AAD restituirà solo l'errore seguente: `invalid_client: AADSTS501051: Application xxxx is not assigned to a role for the xxxx`
   >
   > Se si mantengono **assegnazione utente obbligatoria?** al **senza**, <span style='background-color:yellow; display:inline'>Azure AD non controllare le assegnazioni di ruolo app quando un client richiede un token di accesso per l'API Web</span>. Pertanto, qualsiasi client daemon, ovvero qualsiasi client usando il flusso di credenziali client, sarebbe comunque in grado di ottenere un token di accesso per l'API semplicemente specificando il gruppo di destinatari. Qualsiasi applicazione, potrebbe essere in grado di accedere all'API senza dover richiedere le autorizzazioni per tale. A questo punto, non si tratta quindi fine, come l'API Web può sempre, come illustrato nella sezione successiva, verificare che l'applicazione abbia il ruolo più appropriato (che è stato autorizzato dall'amministratore tenant), tramite la convalida che il token di accesso abbia un `roles` attestazione e il valore corretto per t la domanda (in questo caso `access_as_application`).

1. Selezionare **Salva**

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurazione del codice dell'App](scenario-protected-web-api-app-configuration.md)
