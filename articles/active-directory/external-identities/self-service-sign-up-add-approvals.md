---
title: 'Aggiungere approvazioni personalizzate ai flussi di iscrizione self-service: Azure AD'
description: Aggiungere i connettori API per i flussi di lavoro di approvazione personalizzati in identità esterne Azure Active Directory iscrizione Self-Service (Azure AD)
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: article
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d664d7cd169593924917bb02a0220e4047eb0cdb
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88165243"
---
# <a name="add-a-custom-approval-workflow-to-self-service-sign-up"></a>Aggiungere un flusso di lavoro di approvazione personalizzato all'iscrizione self-service

Con i [connettori API](api-connectors-overview.md)è possibile eseguire l'integrazione con i flussi di lavoro di approvazione personalizzati con l'iscrizione self-service, in modo da poter gestire gli account utente Guest creati nel tenant.

Questo articolo fornisce un esempio di come eseguire l'integrazione con un sistema di approvazione. In questo esempio, il flusso utente di iscrizione Self-Service raccoglie i dati utente durante il processo di iscrizione e li passa al sistema di approvazione. Il sistema di approvazione può quindi:

- Approvare automaticamente l'utente e consentire Azure AD di creare l'account utente.
- Attivare una revisione manuale. Se la richiesta viene approvata, il sistema di approvazione USA Microsoft Graph per eseguire il provisioning dell'account utente. Il sistema di approvazione può inoltre notificare all'utente che il proprio account è stato creato.

## <a name="register-an-application-for-your-approval-system"></a>Registrare un'applicazione per il sistema di approvazione

È necessario registrare il sistema di approvazione come un'applicazione nel tenant di Azure AD in modo che sia in grado di eseguire l'autenticazione con Azure AD e di disporre dell'autorizzazione per la creazione di utenti. Altre informazioni sulle [nozioni di base sull'autenticazione e sull'autorizzazione per Microsoft Graph](https://docs.microsoft.com/graph/auth/auth-concepts).

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore di Azure AD.
2. In **Servizi di Azure** selezionare **Azure Active Directory**.
3. Nel menu a sinistra selezionare **registrazioni app**, quindi selezionare **nuova registrazione**.
4. Immettere un **nome** per l'applicazione, ad esempio le _approvazioni di iscrizione_.

   <!-- ![Register an application for the approval system](./self-service-sign-up-add-approvals/approvals/register-an-approvals-application.png) -->

5. Selezionare **Registra**. È possibile lasciare invariati i valori predefiniti di altri campi.

   ![Pagina Registra un'applicazione](media/self-service-sign-up-add-approvals/register-approvals-app.png)

6. In **Gestisci** nel menu a sinistra selezionare **autorizzazioni API**e quindi selezionare **Aggiungi un'autorizzazione**.
7. Nella pagina **autorizzazioni API richiesta** selezionare **Microsoft Graph**e quindi selezionare **Autorizzazioni applicazione**.
8. In **Seleziona autorizzazioni**espandere **utente**e quindi selezionare la casella di controllo **User. ReadWrite. All** . Questa autorizzazione consente al sistema di approvazione di creare l'utente al momento dell'approvazione. Selezionare quindi **Aggiungi autorizzazioni**.

   ![Pagina Registra un'applicazione](media/self-service-sign-up-add-approvals/request-api-permissions.png)

9. Nella pagina **autorizzazioni API** selezionare **concedi il consenso dell'amministratore per (nome del tenant)**, quindi selezionare **Sì**.
10. In **Gestisci** nel menu a sinistra selezionare **certificati & segreti**, quindi selezionare **nuovo segreto client**.
11. Immettere una **Descrizione** per il segreto, ad esempio il _segreto client per le approvazioni_e selezionare la durata di **scadenza**del segreto client. Quindi selezionare **Aggiungi**.
12. Copiare il valore del segreto client.

    ![Copiare il segreto client da usare nel sistema di approvazione](media/self-service-sign-up-add-approvals/client-secret-value-copy.png)

13. Configurare il sistema di approvazione in modo da usare l' **ID applicazione** come ID client e il **segreto client** generato per l'autenticazione con Azure ad.

## <a name="create-the-api-connectors"></a>Creare i connettori API

Verranno quindi [creati i connettori API](self-service-sign-up-add-api-connector.md#create-an-api-connector) per il flusso utente di iscrizione self-service. L'API del sistema di approvazione necessita di due connettori e degli endpoint corrispondenti, come negli esempi illustrati di seguito. Questi connettori API eseguono le operazioni seguenti:

- **Verificare lo stato di approvazione**. Inviare una chiamata al sistema di approvazione immediatamente dopo che un utente ha effettuato l'accesso con un provider di identità per verificare se l'utente dispone di una richiesta di approvazione esistente o è già stata negata. Se il sistema di approvazione esegue solo decisioni di approvazione automatica, questo connettore API potrebbe non essere necessario. Esempio di connettore API "Verifica stato approvazione".

  ![Controllare la configurazione del connettore API stato approvazione](./media/self-service-sign-up-add-approvals/check-approval-status-api-connector-config-alt.png)

- **Richiedi approvazione** : Invia una chiamata al sistema di approvazione dopo che un utente ha completato la pagina raccolta attributi, ma prima che venga creato l'account utente per richiedere l'approvazione. La richiesta di approvazione può essere concessa automaticamente o esaminata manualmente. Esempio di connettore API "Richiedi approvazione". 

  ![Richiedi configurazione connettore API](./media/self-service-sign-up-add-approvals/create-approval-request-api-connector-config-alt.png)

Per creare questi connettori, seguire i passaggi descritti in [creare un connettore API](self-service-sign-up-add-api-connector.md#create-an-api-connector).

## <a name="enable-the-api-connectors-in-a-user-flow"></a>Abilitare i connettori API in un flusso utente

A questo punto, aggiungere i connettori API a un flusso utente di iscrizione self-service con i passaggi seguenti:

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore di Azure AD.
2. In **Servizi di Azure** selezionare **Azure Active Directory**.
3. Nel menu a sinistra selezionare **Identità esterne**.
4. Selezionare **flussi utente (anteprima)** e quindi selezionare il flusso utente per il quale si vuole abilitare il connettore API.
5. Selezionare **connettori API**e quindi selezionare gli endpoint API da richiamare nei passaggi seguenti del flusso utente:

   - **Dopo aver eseguito l'accesso con un provider di identità**: selezionare il connettore dell'API stato di approvazione, ad esempio _verificare lo stato di approvazione_.
   - **Prima di creare l'utente**: selezionare il connettore dell'API per la richiesta di approvazione, ad esempio _Richiedi approvazione_.

   ![Aggiungere API al flusso utente](./media/self-service-sign-up-add-approvals/api-connectors-user-flow-api.png)

6. Selezionare **Salva**.

## <a name="control-the-sign-up-flow-with-api-responses"></a>Controllare il flusso di iscrizione con risposte API

Il sistema di approvazione può usare le risposte quando viene chiamato per controllare il flusso di iscrizione. 

### <a name="request-and-responses-for-the-check-approval-status-api-connector"></a>Richiesta e risposte per il connettore API "Verifica stato approvazione"

Esempio della richiesta ricevuta dall'API dal connettore API "Verifica stato approvazione":

```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "lastName":"Smith",
 "ui_locales":"en-US"
}
```

Le attestazioni esatte inviate all'API dipendono dalle informazioni fornite dal provider di identità. ' email ' viene sempre inviato.

#### <a name="continuation-response-for-check-approval-status"></a>Risposta di continuazione per "Verifica stato approvazione"

L'endpoint dell'API **Controlla stato approvazione** deve restituire una risposta di continuazione se:

- L'utente non ha richiesto in precedenza un'approvazione.

Esempio della risposta di continuazione:

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue"
}
```

#### <a name="blocking-response-for-check-approval-status"></a>Risposta di blocco per "Verifica stato approvazione"

L'endpoint dell'API **Controlla stato approvazione** deve restituire una risposta di blocco se:

- L'approvazione dell'utente è in sospeso.
- L'utente è stato negato e non dovrebbe essere autorizzato a richiedere nuovamente l'approvazione.

Di seguito sono riportati alcuni esempi di risposte di blocco:

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your access request is already processing. You'll be notified when your request has been approved.",
    "code": "CONTOSO-APPROVAL-PENDING"
}
```

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your sign up request has been denied. Please contact an administrator if you believe this is an error",
    "code": "CONTOSO-APPROVAL-DENIED"
}
```

### <a name="request-and-responses-for-the-request-approval-api-connector"></a>Richiesta e risposte per il connettore API "Richiedi approvazione"

Esempio di richiesta HTTP ricevuta dall'API dal connettore API "Richiedi approvazione":

```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```

Le attestazioni esatte inviate all'API dipendono dalle informazioni raccolte dall'utente o fornite dal provider di identità.

#### <a name="continuation-response-for-request-approval"></a>Risposta di continuazione per "Richiedi approvazione"

L'endpoint dell'API di **approvazione della richiesta** deve restituire una risposta di continuazione se:

- L'utente può essere **_approvato automaticamente_**.

Esempio della risposta di continuazione:

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue"
}
```

> [!IMPORTANT]
> Se viene ricevuta una risposta di continuazione, Azure AD crea un account utente e indirizza l'utente all'applicazione.

#### <a name="blocking-response-for-request-approval"></a>Risposta di blocco per "approvazione della richiesta"

L'endpoint dell'API di **approvazione della richiesta** deve restituire una risposta di blocco se:

- Una richiesta di approvazione utente è stata creata ed è ora in sospeso.
- Una richiesta di approvazione utente è stata negata automaticamente.

Di seguito sono riportati alcuni esempi di risposte di blocco:

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your account is now waiting for approval. You'll be notified when your request has been approved.",
    "code": "CONTOSO-APPROVAL-REQUESTED"
}
```

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your sign up request has been denied. Please contact an administrator if you believe this is an error",
    "code": "CONTOSO-APPROVAL-AUTO-DENIED"
}
```

L'oggetto `userMessage` nella risposta viene visualizzato all'utente, ad esempio:

![Esempio di pagina di approvazione in sospeso](./media/self-service-sign-up-add-approvals/approval-pending.png)

## <a name="user-account-creation-after-manual-approval"></a>Creazione dell'account utente dopo l'approvazione manuale

Dopo aver ottenuto l'approvazione manuale, il sistema di approvazione personalizzato crea un account [utente](https://docs.microsoft.com/graph/azuread-users-concept-overview) usando [Microsoft Graph](https://docs.microsoft.com/graph/use-the-api). Il modo in cui il sistema di approvazione effettua il provisioning dell'account utente dipende dal provider di identità usato dall'utente.

### <a name="for-a-federated-google-or-facebook-user"></a>Per un utente di Google o Facebook federato

> [!IMPORTANT]
> Il sistema di approvazione deve verificare in modo esplicito che `identities` `identities[0]` e `identities[0].issuer` siano presenti e che sia `identities[0].issuer` uguale a "Facebook" o "Google" per usare questo metodo.

Se l'utente ha effettuato l'accesso con un account Google o Facebook, è possibile usare l' [API di creazione dell'utente](https://docs.microsoft.com/graph/api/user-post-users?view=graph-rest-1.0&tabs=http).

1. Il sistema di approvazione USA riceve la richiesta HTTP dal flusso utente.

```http
POST <Approvals-API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@outlook.com",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "city": "Redmond",
 "extension_<extensions-app-id>_CustomAttribute": "custom attribute value",
 "ui_locales":"en-US"
}
```

2. Il sistema di approvazione USA Microsoft Graph per creare un account utente.

```http
POST https://graph.microsoft.com/v1.0/users
Content-type: application/json

{
 "userPrincipalName": "johnsmith_outlook.com#EXT@contoso.onmicrosoft.com",
 "accountEnabled": true,
 "mail": "johnsmith@outlook.com",
 "userType": "Guest",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "city": "Redmond",
 "extension_<extensions-app-id>_CustomAttribute": "custom attribute value"
}
```

| Parametro                                           | Obbligatorio | Descrizione                                                                                                                                                            |
| --------------------------------------------------- | -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| userPrincipalName                                   | Sì      | Può essere generato accettando l' `email` attestazione inviata all'API, sostituendo il `@` carattere con `_` e pre-in sospeso a `#EXT@<tenant-name>.onmicrosoft.com` . |
| accountEnabled                                      | Sì      | Il valore deve essere impostato su `true`.                                                                                                                                                 |
| mail                                                | Sì      | Equivalente all' `email` attestazione inviata all'API.                                                                                                               |
| userType                                            | Sì      | Deve essere `Guest`. Designa questo utente come utente Guest.                                                                                                                 |
| identità                                          | Sì      | Informazioni sull'identità federata.                                                                                                                                    |
| \<otherBuiltInAttribute>                            | No       | Altri attributi predefiniti, ad esempio `displayName` , `city` e altri. I nomi dei parametri sono gli stessi dei parametri inviati dal connettore API.                            |
| \<extension\_\{extensions-app-id}\_CustomAttribute> | No       | Attributi personalizzati relativi all'utente. I nomi dei parametri sono gli stessi dei parametri inviati dal connettore API.                                                            |

### <a name="for-a-federated-azure-active-directory-user"></a>Per un utente Azure Active Directory federato

Se un utente accede con un account di Azure Active Directory federato, è necessario usare l' [API di invito](https://docs.microsoft.com/graph/api/invitation-post?view=graph-rest-1.0) per creare l'utente e, facoltativamente, l' [API di aggiornamento utente](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0) per assegnare altri attributi all'utente.

1. Il sistema di approvazione riceve la richiesta HTTP dal flusso utente.

```http
POST <Approvals-API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "displayName": "John Smith",
 "city": "Redmond",
 "extension_<extensions-app-id>_CustomAttribute": "custom attribute value",
 "ui_locales":"en-US"
}
```

2. Il sistema di approvazione crea l'invito usando il `email` fornito dal connettore API.

```http
POST https://graph.microsoft.com/v1.0/invitations
Content-type: application/json

{
    "invitedUserEmailAddress":"johnsmith@fabrikam.onmicrosoft.com",
    "inviteRedirectUrl" : "https://myapp.com"
}
```

Esempio di risposta:

```http
HTTP/1.1 201 OK
Content-type: application/json

{
    ...
    "invitedUser": {
        "id": "<generated-user-guid>"
    }
}
```

3. Il sistema di approvazione usa l'ID dell'utente invitato per aggiornare l'account dell'utente con gli attributi utente raccolti (facoltativo).

```http
PATCH https://graph.microsoft.com/v1.0/users/<generated-user-guid>
Content-type: application/json

{
    "displayName": "John Smith",
    "city": "Redmond",
    "extension_<extensions-app-id>_AttributeName": "custom attribute value"
}
```

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare, vedere gli [esempi di avvio rapido di funzioni di Azure](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts).
- Controllare l' [iscrizione self-service per gli utenti guest con l'esempio di approvazione manuale](code-samples-self-service-sign-up.md#custom-approval-workflows). 
