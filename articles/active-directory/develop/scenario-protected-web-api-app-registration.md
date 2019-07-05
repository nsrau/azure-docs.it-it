---
title: API web - registrazione di app protetta | Azure
description: Informazioni su come compilare un'API web protetta e le informazioni necessarie per registrare l'app.
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
ms.openlocfilehash: e4622cffedc159ce85166eafe571ccb26c2c1b4d
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536866"
---
# <a name="protected-web-api-app-registration"></a>API web protetta: Registrazione delle app

Questo articolo illustra le specifiche di registrazione dell'app per un'API web protetta.

Vedere [Avvio rapido: Registrare un'applicazione con la piattaforma delle identità Microsoft](quickstart-register-app.md) per i passaggi comuni per la registrazione di un'app.

## <a name="accepted-token-version"></a>Versione del token accettato

L'endpoint di Microsoft identity platform può rilasciare due tipi di token: token v1.0 e v2.0 token. Per altre informazioni su questi token, vedere [token di accesso](access-tokens.md). La versione del token accettata varia a seconda le **tipi di account supportati** scelto durante la creazione dell'applicazione:

- Se il valore di **tipi di account supportati** viene **gli account in qualsiasi directory dell'organizzazione e gli account Microsoft personali (ad esempio Skype, Xbox, Outlook.com)** , la versione accettata token sarà v2.0.
- In caso contrario, la versione accettata token sarà v1.0.

Dopo aver creato l'applicazione, è possibile determinare o modificare la versione del token accettata, seguire questi passaggi:

1. Nel portale di Azure, selezionare l'app e quindi selezionare il **manifesto** per l'app.
2. Nel manifesto, cercare **"accessTokenAcceptedVersion"** . Si noti che il relativo valore è **2**. Questa proprietà specifica ad Azure Active Directory (Azure AD) che l'API web accetta token v2.0. Se il valore è **null**, la versione del token accettata è v1.0.
3. Se è stata modificata la versione del token, selezionare **salvare**.

> [!NOTE]
> L'API web specifica quale token versione (versione 1.0 o 2.0) accetta. Quando i client richiedono un token per l'API web dall'endpoint v2.0 di Microsoft identity platform, si otterrà un token che indica quale versione è stata accettata dall'API web.

## <a name="no-redirect-uri"></a>Nessun URI di reindirizzamento

Le API Web non necessario registrare un URI di reindirizzamento, perché nessun utente ha eseguito l'accesso in modo interattivo.

## <a name="expose-an-api"></a>Esporre un'API

Un'altra impostazione specifica per le API web è l'API esposta e sugli ambiti esposti.

### <a name="resource-uri-and-scopes"></a>URI della risorsa e gli ambiti

Gli ambiti sono in genere nel formato `resourceURI/scopeName`. Per Microsoft Graph, gli ambiti sono tasti di scelta rapida, ad esempio `User.Read`. Stringa che rappresenta una scelta rapida per `https://graph.microsoft.com/user.read`.

Durante la registrazione dell'app, è necessario definire questi parametri:

- L'URI della risorsa. Per impostazione predefinita, il portale di registrazione applicazione suggerisce che è possibile usare `api://{clientId}`. Questo URI di risorsa sia univoco, ma non umano leggibile. È possibile modificarla, ma assicurarsi che il nuovo valore è univoco.
- Uno o più *ambiti*. (Per le applicazioni client, verranno mostrati come *autorizzazioni delegate* per l'API web.)
- Uno o più *ruoli app*. (Per le applicazioni client, verranno mostrati come *autorizzazioni applicazione* per l'API web.)

Gli ambiti vengono inoltre visualizzati nella schermata di consenso che viene presentata agli utenti finali dell'app. Pertanto, è necessario fornire le stringhe corrispondenti che descrivono l'ambito:

- Stessa forma visualizzata all'utente finale.
- Come illustrato dall'amministratore tenant, che può concedere il consenso dell'amministratore.

### <a name="exposing-delegated-permissions-scopes"></a>Esponendo le autorizzazioni delegate (ambiti)

1. Selezionare il **esporre un'API** sezione nella registrazione dell'applicazione.
1. Selezionare **Aggiungi un ambito**.
1. Se richiesto, accettare l'URI dell'ID applicazione proposta (`api://{clientId}`), selezionando **Salva e continua**.
1. Immettere Questi parametri:
      - Per la **nome ambito**, usare **access_as_user**.
      - Per **che può fornire il consenso**, assicurarsi che **gli amministratori e utenti** sia selezionata.
      - Nelle **nome visualizzazione il consenso dell'amministratore**, immettere **Access TodoListService come un utente**.
      - Nelle **descrizione del consenso dell'amministratore**, immettere **accede all'API Web TodoListService come un utente**.
      - Nelle **nome visualizzato dell'utente il consenso**, immettere **Access TodoListService come un utente**.
      - Nelle **descrizione del consenso dell'utente**, immettere **accede all'API Web TodoListService come un utente**.
      - Mantieni **lo stato** impostata su **abilitato**.
      - Selezionare **Aggiungi ambito**.

### <a name="if-your-web-api-is-called-by-a-daemon-app"></a>Se viene chiamata l'API web da un'app daemon

In questa sezione si apprenderà come registrare l'API web protetta in modo che può essere chiamato in modo sicuro per le app daemon.

- è necessario esporre *autorizzazioni applicazione*. È indicata solo le autorizzazioni dell'applicazione poiché le app daemon non interagiscano con gli utenti, in modo che le autorizzazioni delegate non avrebbero senso.
- Gli amministratori del tenant possono richiedere Azure Active Directory (Azure AD) per rilasciare token per l'API web solo alle applicazioni che hanno registrato per l'accesso a una delle autorizzazioni per l'applicazione dell'API web.

#### <a name="exposing-application-permissions-app-roles"></a>Esponendo le autorizzazioni dell'applicazione (ruoli app)

Per esporre le autorizzazioni dell'applicazione, è necessario modificare il manifesto.

1. Nella registrazione dell'applicazione per l'applicazione, selezionare **manifesto**.
1. Modificare il manifesto individuando il `appRoles` impostazione e l'aggiunta di uno o più ruoli applicazione. Nella sezione JSON di esempio seguente viene fornita la definizione di ruolo. Lasciare il `allowedMemberTypes` impostato su `"Application"` solo. Assicurarsi che il `id` è un GUID univoco e che `displayName` e `value` non contengono spazi.
1. Salvare il manifesto.

L'esempio seguente mostra i contenuti del `appRoles`. (Il `id` può essere qualsiasi GUID univoco.)

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

#### <a name="ensuring-that-azure-ad-issues-tokens-for-your-web-api-to-only-allowed-clients"></a>Verificare che Azure AD rilascia token per il web API per consentiti solo i client

L'API web verifica per il ruolo dell'app. (Che è il modo per gli sviluppatori di esporre le autorizzazioni dell'applicazione). Ma è anche possibile configurare Azure AD per rilasciare un token per l'API web solo alle App che sono approvate dall'amministratore tenant per accedere all'API. Per aggiungere questa maggiore protezione:

1. Nell'app **Overview** pagina per la registrazione dell'app, selezionare il collegamento con il nome dell'app sotto **applicazione gestita nella directory locale**. Il titolo per questo campo potrebbe essere troncato. Potrebbe, ad esempio, vedere **nell'applicazione gestita...**

   > [!NOTE]
   >
   > Quando si seleziona questo collegamento, si passerà al **Panoramica di applicazione Enterprise** pagina associata all'entità servizio per l'applicazione nel tenant in cui è stato creato. È possibile passare nuovamente alla pagina di registrazione dell'app con il pulsante Indietro del browser.

1. Selezionare il **le proprietà** pagina il **Gestisci** sezione delle pagine di applicazione Enterprise.
1. Se si vuole che Azure AD per consentire l'accesso all'API Web da solo alcuni client, impostare **assegnazione utente obbligatoria?** al **Yes**.

   > [!IMPORTANT]
   >
   > Se si imposta **assegnazione utente obbligatoria?** al **Yes**, Azure AD verificherà le assegnazioni di ruolo di client quando richiedono un token di accesso per l'API web. Se il client non è assegnato a tutti i ruoli dell'app, Azure AD restituirà l'errore `invalid_client: AADSTS501051: Application <application name> is not assigned to a role for the <web API>`.
   >
   > Se si mantengono **assegnazione utente obbligatoria?** impostata su **senza**, *Azure AD non controllare le assegnazioni di ruolo app quando un client richiede un token di accesso per l'API web*. Qualsiasi client daemon (vale a dire, qualsiasi client usando il flusso di credenziali client) sarà in grado di ottenere un token di accesso per l'API semplicemente specificando il gruppo di destinatari. Qualsiasi applicazione sarà in grado di accedere all'API senza dover richiedere le autorizzazioni per tale. Tuttavia, l'API web può sempre, come illustrato nella sezione precedente, verificare che l'applicazione abbia il ruolo più appropriato (che è autorizzato dall'amministratore tenant). L'API esegue questa verifica tramite la convalida che il token di accesso ha un ruoli attestazione e che il valore di questa attestazione sia corretto. (In questo caso, il valore è `access_as_application`.)

1. Selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurazione del codice dell'App](scenario-protected-web-api-app-configuration.md)
