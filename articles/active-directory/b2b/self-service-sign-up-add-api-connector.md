---
title: Aggiungere connettori API a flussi di iscrizione self-service-Azure AD
description: Configurare un'API Web da usare in un flusso utente.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0498a2015b75221763ab5fdd4f6e94428922bd6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85386743"
---
# <a name="add-an-api-connector-to-a-user-flow"></a>Aggiungere un connettore API a un flusso utente

Per usare un [connettore API](api-connectors-overview.md), è necessario innanzitutto creare il connettore API e quindi abilitarlo in un flusso utente.

## <a name="create-an-api-connector"></a>Creare un connettore API

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore di Azure AD.
2. In **Servizi di Azure** selezionare **Azure Active Directory**.
3. Nel menu a sinistra selezionare **Identità esterne**.
4. Selezionare **tutti i connettori API (anteprima)** e quindi selezionare **nuovo connettore API**.

   ![Aggiungere un nuovo connettore API](./media/self-service-sign-up-add-api-connector/api-connector-new.png)

5. Consente di specificare un nome visualizzato per la chiamata. Ad esempio, **verificare lo stato di approvazione**.
6. Specificare l' **URL dell'endpoint** per la chiamata API.
7. Fornire le informazioni di autenticazione per l'API.

   - Attualmente è supportata solo l'autenticazione di base. Se si vuole usare un'API senza autenticazione di base a scopo di sviluppo, è sufficiente immettere un **nome utente** e una **password** fittizi che l'API può ignorare. Per l'uso con una funzione di Azure con una chiave API, è possibile includere il codice come parametro di query nell' **URL dell'endpoint** (ad esempio, HTTPS []() ://contoso.azurewebsites.NET/API/endpoint<b>? Code = 0123456789</b>).

   ![Aggiungere un nuovo connettore API](./media/self-service-sign-up-add-api-connector/api-connector-config.png)

8. Selezionare le attestazioni che si desidera inviare all'API.
9. Selezionare le attestazioni che si prevede di ricevere nuovamente dall'API.

   ![Impostazione delle attestazioni del connettore API](./media/self-service-sign-up-add-api-connector/api-connector-claims.png)

10. Selezionare **Salva**.

## <a name="enable-the-api-connector-in-a-user-flow"></a>Abilitare il connettore API in un flusso utente

Seguire questi passaggi per aggiungere un connettore API a un flusso utente di iscrizione self-service.

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore di Azure AD.
2. In **Servizi di Azure** selezionare **Azure Active Directory**.
3. Nel menu a sinistra selezionare **Identità esterne**.
4. Selezionare **flussi utente (anteprima)** e quindi selezionare il flusso utente a cui si vuole aggiungere il connettore API.
5. Selezionare **connettori API**e quindi selezionare gli endpoint API da richiamare nei passaggi seguenti del flusso utente:

   - **Dopo aver eseguito l'accesso con un provider di identità**
   - **Prima della creazione dell'utente**

   ![Aggiungere API al flusso utente](./media/self-service-sign-up-add-api-connector/api-connectors-user-flow-select.png)

6. Selezionare **Salva**.

Informazioni su [dove è possibile abilitare un connettore API in un flusso utente](api-connectors-overview.md#where-you-can-enable-an-api-connector-in-a-user-flow).

## <a name="request-sent-to-the-api"></a>Richiesta inviata all'API

Un connettore API viene materializzato come una richiesta HTTP POST, inviando attestazioni selezionate come coppie chiave-valore in un corpo JSON. La risposta deve avere anche l'intestazione HTTP `Content-Type: application/json` . Gli attributi vengono serializzati in modo analogo agli attributi Microsoft Graph utente. <!--# TODO: Add link to MS Graph or create separate reference.-->

### <a name="example-request"></a>Richiesta di esempio

```http
POST <API-endpoint>
Content-type: application/json

{
 "email_address": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "postalCode": "33971",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```

L'attestazione delle **impostazioni locali dell'interfaccia utente (' ui_locales ')** viene inviata per impostazione predefinita in tutte le richieste. Fornisce le impostazioni locali di un utente e può essere usato dall'API per restituire le risposte internazionalizzate. Non viene visualizzato nel riquadro di configurazione dell'API.

Se un'attestazione da inviare non ha un valore nel momento in cui viene chiamato l'endpoint dell'API, l'attestazione non verrà inviata all'API.

Gli attributi personalizzati possono essere creati per l'utente utilizzando il formato **extension_ \<extensions-app-id> _AttributeName** . L'API deve essere in attesa di ricevere attestazioni in questo stesso formato serializzato. L'API può restituire attestazioni con o senza `<extensions-app-id>` . Per altre informazioni sugli attributi personalizzati, vedere [definire attributi personalizzati per i flussi di iscrizione self-service](user-flow-add-custom-attributes.md).

> [!TIP] 
> le [**identità ("identità")**](https://docs.microsoft.com/graph/api/resources/objectidentity?view=graph-rest-1.0) e l' **indirizzo di posta elettronica ("email_address")** possono essere usate per identificare un utente prima di avere un account nel tenant. L'attestazione ' identitys ' viene inviata quando un utente esegue l'autenticazione con Google o Facebook è email_address ' viene sempre inviato.

## <a name="expected-response-types-from-the-web-api"></a>Tipi di risposta previsti dall'API Web

Quando l'API Web riceve una richiesta HTTP da Azure AD durante un flusso utente, può restituire le risposte seguenti:

- [Risposta continuazione](#continuation-response)
- [Risposta di blocco](#blocking-response)
- [Convalida-risposta di errore](#validation-error-response)

### <a name="continuation-response"></a>Risposta continuazione

Una risposta di continuazione indica che il flusso utente deve continuare con il passaggio successivo. In una risposta di continuazione, l'API può restituire attestazioni.

Se un'attestazione viene restituita dall'API ed è selezionata come **attestazione per la ricezione**, l'attestazione esegue le operazioni seguenti:

- Pre-riempie i campi di input nella pagina della raccolta di attributi se il connettore API viene richiamato prima che venga visualizzata la pagina.
- Esegue l'override di qualsiasi valore che è già stato assegnato all'attestazione.
- Assegna un valore all'attestazione se era in precedenza null.

#### <a name="example-of-a-continuation-response"></a>Esempio di risposta di continuazione

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue",
    "postalCode": "12349", // return claim
    "extension_<extensions-app-id>_CustomAttribute": "value" // return claim
}
```

| Parametro                                          | Type              | Obbligatoria | Descrizione                                                                                                                                                                                                                                                                            |
| -------------------------------------------------- | ----------------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| version                                            | string            | Sì      | Versione dell'API.                                                                                                                                                                                                                                                                |
| action                                             | string            | Sì      | Il valore deve essere `Continue`.                                                                                                                                                                                                                                                              |
| \<builtInUserAttribute>                            | \<attribute-type> | No       | I valori possono essere archiviati nella directory se hanno selezionato come **attestazione per la ricezione** nella configurazione del connettore API e negli **attributi utente** per un flusso utente. I valori possono essere restituiti nel token se selezionato come **attestazione dell'applicazione**.                                              |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | No       | L'attestazione restituita può facoltativamente non contenere `_<extensions-app-id>_` . I valori vengono archiviati nella directory se hanno selezionato come **attestazione per la ricezione** nella configurazione del connettore API e nell' **attributo utente** per un flusso utente. Gli attributi personalizzati non possono essere restituiti nel token. |

### <a name="blocking-response"></a>Risposta di blocco

Una risposta di blocco esce dal flusso utente. Può essere rilasciata intenzionalmente dall'API per arrestare la continuazione del flusso utente visualizzando una pagina di blocco per l'utente. Nella pagina blocco viene visualizzato l'oggetto `userMessage` fornito dall'API.

Di seguito è riportato un esempio della risposta di blocco:

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "There was a problem with your request. You are not able to sign up at this time.",
    "code": "CONTOSO-BLOCK-00"
}

```

| Parametro   | Type   | Obbligatoria | Descrizione                                                                |
| ----------- | ------ | -------- | -------------------------------------------------------------------------- |
| version     | string | Sì      | Versione dell'API.                                                    |
| action      | string | Sì      | Il valore deve essere`ShowBlockPage`                                              |
| userMessage | string | Sì      | Messaggio da visualizzare all'utente.                                            |
| codice        | string | No       | Codice di errore. Può essere usato a scopo di debug. Non viene visualizzato all'utente. |

#### <a name="end-user-experience-with-a-blocking-response"></a>Esperienza dell'utente finale con una risposta di blocco

![Pagina blocco di esempio](./media/api-connectors-overview/blocking-page-response.png)

### <a name="validation-error-response"></a>Convalida-risposta di errore

Una chiamata API richiamata dopo una pagina della raccolta di attributi può restituire una risposta di errore di convalida. Quando si esegue questa operazione, il flusso utente rimane nella pagina raccolta attributi e `userMessage` viene visualizzato all'utente. L'utente può quindi modificare e inviare nuovamente il modulo. Questo tipo di risposta può essere usato per la convalida dell'input.

#### <a name="example-of-a-validation-error-response"></a>Esempio di una risposta di errore di convalida

```http
HTTP/1.1 400 Bad Request
Content-type: application/json

{
    "version": "1.0.0",
    "status": 400,
    "action": "ValidationError",
    "userMessage": "Please enter a valid Postal Code.",
    "code": "CONTOSO-VALIDATION-00"
}
```

| Parametro   | Type    | Obbligatoria | Descrizione                                                                |
| ----------- | ------- | -------- | -------------------------------------------------------------------------- |
| version     | string  | Sì      | Versione dell'API.                                                    |
| action      | string  | Sì      | Il valore deve essere `ValidationError`.                                           |
| status      | Integer | Sì      | Deve essere `400` un valore per una risposta ValidationError.                        |
| userMessage | string  | Sì      | Messaggio da visualizzare all'utente.                                            |
| codice        | string  | No       | Codice di errore. Può essere usato a scopo di debug. Non viene visualizzato all'utente. |

#### <a name="end-user-experience-with-a-validation-error-response"></a>Esperienza dell'utente finale con una risposta di errore di convalida

![Pagina di convalida di esempio](./media/api-connectors-overview/validation-error-postal-code.png)

### <a name="integration-with-azure-functions"></a>Integrazione con Funzioni di Azure
È possibile usare un trigger HTTP in funzioni di Azure come metodo semplice per creare un'API da usare con il connettore API. Usare la funzione di Azure per, [ad esempio](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts), per eseguire la logica di convalida e limitare l'accesso a domini specifici. È anche possibile chiamare e richiamare altre API Web, archivi utente e altri servizi cloud.

## <a name="next-steps"></a>Passaggi successivi

<!-- - Learn [where you can enable an API connector](api-connectors-overview.md#where-you-can-enable-an-api-connector-in-a-user-flow) -->
- Informazioni su come [aggiungere un flusso di lavoro di approvazione personalizzato all'iscrizione self-service](self-service-sign-up-add-approvals.md)
- Per iniziare, vedere gli [esempi di avvio rapido di funzioni di Azure](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts).
<!-- - Learn how to [use API connectors to verify a user identity](code-samples-self-service-sign-up.md#identity-verification) -->
