---
title: Proteggere un'API di Gestione API di Azure con Azure Active Directory B2C
description: Informazioni su come usare i token di accesso emessi da Azure Active Directory B2C per proteggere l'endpoint di un'API di Gestione API di Azure.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0e8802d612f2497cc58c90856e9a5a5572a142f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87482839"
---
# <a name="secure-an-azure-api-management-api-with-azure-ad-b2c"></a>Proteggere un'API di Gestione API di Azure con Azure AD B2C

Informazioni su come limitare l'accesso dell'API di Gestione API di Azure ai client che hanno eseguito l'autenticazione con Azure Active Directory B2C (Azure AD B2C). Seguire la procedura descritta in questo articolo per creare e testare un criterio in ingresso in Gestione API che limiti l'accesso solo alle richieste che includono un token di accesso valido emesso da Azure AD B2C.

## <a name="prerequisites"></a>Prerequisiti

Per continuare con la procedura descritta in questo articolo, è necessario implementare le risorse seguenti:

* [Tenant di Azure AD B2C](tutorial-create-tenant.md)
* [Applicazione registrata](tutorial-register-applications.md) nel tenant
* [Flussi utente creati](tutorial-create-user-flows.md) nel tenant
* [API pubblicata](../api-management/import-and-publish.md) in Gestione API di Azure
* [Postman](https://www.getpostman.com/) per testare l'accesso protetto (facoltativo)

## <a name="get-azure-ad-b2c-application-id"></a>Ottenere l'ID applicazione di Azure AD B2C

Per la protezione di un'API in Gestione API di Azure con Azure AD B2C sono necessari alcuni valori per il [criterio in ingresso](../api-management/api-management-howto-policies.md) creato in Gestione API. Registrare prima l'ID di un'applicazione creata in precedenza nel tenant di Azure AD B2C. Se si usa l'applicazione creata nei prerequisiti, usare l'ID applicazione per *webbapp1*.

Per registrare un'applicazione nel tenant di Azure AD B2C, è possibile usare la nuova esperienza unificata **Registrazioni app** oppure l'esperienza legacy **Applicazioni (legacy)** . [Altre informazioni sulla nuova esperienza](https://aka.ms/b2cappregtraining).

#### <a name="app-registrations"></a>[Registrazioni per l'app](#tab/app-reg-ga/)

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel menu a sinistra selezionare **Azure AD B2C**. In alternativa, selezionare **Tutti i servizi** e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **registrazioni app**, quindi selezionare la scheda **applicazioni di proprietà** .
1. Registrare il valore presente nella colonna **ID client applicazione** per *webapp1* o un'altra applicazione creata in precedenza.

#### <a name="applications-legacy"></a>[Applicazioni (legacy)](#tab/applications-legacy/)

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel menu a sinistra selezionare **Azure AD B2C**. In alternativa, selezionare **Tutti i servizi** e quindi cercare e selezionare **Azure AD B2C**.
1. In **Gestisci**selezionare **applicazioni (legacy)**.
1. Registrare il valore presente nella colonna **ID APPLICAZIONE** per *webapp1* o un'altra applicazione creata in precedenza.

* * *

## <a name="get-token-issuer-endpoint"></a>Ottenere l'endpoint dell'autorità emittente di token

Successivamente, ottenere l'URL di configurazione well-known di uno dei flussi utente di Azure AD B2C. È anche necessario l'URI dell'endpoint dell'autorità emittente di token da supportare in Gestione API di Azure.

1. Passare al tenant di Azure AD B2C nel [portale di Azure](https://portal.azure.com).
1. In **Criteri** selezionare **Flussi utente**.
1. Selezionare un criterio esistente, ad esempio *B2C_1_signupsignin1*, e quindi **Esegui il flusso utente**.
1. Registrare l'URL nel collegamento ipertestuale visualizzato sotto l'intestazione **Esegui il flusso utente** nella parte superiore della pagina. Questo URL costituisce l'endpoint di individuazione well-known di OpenID Connect per il flusso utente e verrà usato nella prossima sezione per la configurazione del criterio in ingresso in Gestione API di Azure.

    ![Collegamento ipertestuale URI well-known nella pagina Esegui adesso del portale di Azure](media/secure-apim-with-b2c-token/portal-01-policy-link.png)

1. Selezionare il collegamento ipertestuale per passare alla pagina di configurazione well-known di OpenID Connect.
1. Nella pagina visualizzata nel browser registrare il valore di `issuer`, ad esempio:

    `https://<tenant-name>.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

    Questo valore verrà usato nella prossima sezione per la configurazione dell'API in Gestione API di Azure.

A questo punto sono disponibili due URL registrati da usare nella prossima sezione: l'URL dell'endpoint di configurazione well-known di OpenID Connect e l'URI dell'autorità emittente. Ad esempio:

```
https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1_signupsignin1/v2.0/.well-known/openid-configuration
https://<tenant-name>.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/
```

## <a name="configure-inbound-policy-in-azure-api-management"></a>Configurare il criterio in ingresso in Gestione API di Azure

A questo punto è possibile aggiungere il criterio in ingresso in Gestione API di Azure per la convalida delle chiamate API. Con l'aggiunta di un criterio di [convalida JWT](../api-management/api-management-access-restriction-policies.md#ValidateJWT) che verifica i destinatari e l'autorità emittente in un token di accesso, è possibile assicurarsi che vengano accettate solo le chiamate API con un token valido.

1. Selezionare l'istanza di Gestione API di Azure nel [portale di Azure](https://portal.azure.com).
1. Selezionare **API**.
1. Selezionare l'API che si vuole proteggere con Azure AD B2C.
1. Selezionare la scheda **Progettazione**.
1. In **Elaborazione in ingresso** selezionare **\</\>** per aprire l'editor del codice dei criteri.
1. Inserire il tag `<validate-jwt>` seguente all'interno del criterio `<inbound>`.

    1. Aggiornare il valore `url` nell'elemento `<openid-config>` con l'URL di configurazione well-known del criterio.
    1. Aggiornare l'elemento `<audience>` con l'ID dell'applicazione creata in precedenza nel tenant di B2C (ad esempio, *webapp1*).
    1. Aggiornare l'elemento `<issuer>` con l'endpoint dell'autorità emittente di token registrato in precedenza.

    ```xml
    <policies>
        <inbound>
            <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
                <openid-config url="https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1_signupsignin1/v2.0/.well-known/openid-configuration" />
                <audiences>
                    <audience>44444444-0000-0000-0000-444444444444</audience>
                </audiences>
                <issuers>
                    <issuer>https://<tenant-name>.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
                </issuers>
            </validate-jwt>
            <base />
        </inbound>
        <backend> <base /> </backend>
        <outbound> <base /> </outbound>
        <on-error> <base /> </on-error>
    </policies>
    ```

## <a name="validate-secure-api-access"></a>Convalidare l'accesso API sicuro

Per garantire che solo i chiamanti autenticati possano accedere all'API, è possibile convalidare la configurazione di Gestione API di Azure chiamando l'API con [Postman](https://www.getpostman.com/).

Per chiamare l'API sono necessari un token di accesso emesso da Azure AD B2C e una chiave di sottoscrizione di Gestione API.

### <a name="get-an-access-token"></a>Ottenere un token di accesso

È prima necessario un token emesso da Azure AD B2C da usare nell'intestazione `Authorization` in Postman. È possibile ottenerne uno con la funzionalità **Esegui adesso** del flusso utente di iscrizione/accesso creato come prerequisito.

1. Passare al tenant di Azure AD B2C nel [portale di Azure](https://portal.azure.com).
1. In **Criteri** selezionare **Flussi utente**.
1. Selezionare un flusso utente di iscrizione/accesso esistente, ad esempio *B2C_1_signupsignin1*.
1. Per **Applicazione** selezionare *webapp1*.
1. Per **URL di risposta** scegliere `https://jwt.ms`.
1. Selezionare **Esegui il flusso utente**.

    ![Pagina Esegui il flusso utente per il flusso utente di iscrizione/accesso nel portale di Azure](media/secure-apim-with-b2c-token/portal-03-user-flow.png)

1. Completare il processo di accesso. Verrà eseguito il reindirizzamento a `https://jwt.ms`.
1. Registrare il valore del token codificato visualizzato nel browser. Questo valore del token viene usato per l'intestazione Authorization in Postman.

    ![Valore del token codificato visualizzato in jwt.ms](media/secure-apim-with-b2c-token/jwt-ms-01-token.png)

### <a name="get-api-subscription-key"></a>Ottenere la chiave di sottoscrizione dell'API

Un'applicazione client (in questo caso, Postman) che chiama un'API pubblicata deve includere nelle richieste HTTP all'API una chiave di sottoscrizione di Gestione API valida. Per ottenere una chiave di sottoscrizione da includere nella richiesta HTTP di Postman:

1. Passare all'istanza del servizio Gestione API di Azure nel [portale di Azure](https://portal.azure.com).
1. Selezionare **Sottoscrizioni**.
1. Selezionare i puntini di sospensione per **Prodotto: Senza limiti** e quindi scegliere **Mostra/Nascondi chiavi**.
1. Registrare la **CHIAVE PRIMARIA** per il prodotto. Questa chiave viene usata per l'intestazione `Ocp-Apim-Subscription-Key` nella richiesta HTTP di Postman.

![Pagina della chiave di sottoscrizione con l'opzione Mostra/Nascondi chiavi selezionata nel portale di Azure](media/secure-apim-with-b2c-token/portal-04-api-subscription-key.png)

### <a name="test-a-secure-api-call"></a>Testare una chiamata API sicura

Con il token di accesso e la chiave di sottoscrizione di Gestione API registrati, è ora possibile verificare se l'accesso sicuro all'API è stato configurato correttamente.

1. Creare una nuova richiesta `GET` in [Postman](https://www.getpostman.com/). Per l'URL della richiesta, specificare l'endpoint dell'elenco dei relatori dell'API pubblicata come prerequisito. Ad esempio:

    `https://contosoapim.azure-api.net/conference/speakers`

1. Aggiungere quindi le intestazioni seguenti:

    | Chiave | valore |
    | --- | ----- |
    | `Authorization` | Valore del token codificato registrato in precedenza, con prefisso `Bearer ` (includere lo spazio dopo "Bearer") |
    | `Ocp-Apim-Subscription-Key` | Chiave di sottoscrizione di Gestione API registrata in precedenza |

    **Intestazioni** e URL della richiesta **GET** vengono visualizzati nel modo seguente:

    ![Interfaccia utente di Postman in cui sono visualizzati le intestazioni e l'URL della richiesta GET](media/secure-apim-with-b2c-token/postman-01-headers.png)

1. Selezionare il pulsante **Send** (Invia) in Postman per eseguire la richiesta. Se tutti gli elementi sono stati configurati correttamente, viene visualizzata una risposta JSON con una raccolta di relatori (qui visualizzata parzialmente):

    ```json
    {
      "collection": {
        "version": "1.0",
        "href": "https://conferenceapi.azurewebsites.net:443/speakers",
        "links": [],
        "items": [
          {
            "href": "https://conferenceapi.azurewebsites.net/speaker/1",
            "data": [
              {
                "name": "Name",
                "value": "Scott Guthrie"
              }
            ],
            "links": [
              {
                "rel": "http://tavis.net/rels/sessions",
                "href": "https://conferenceapi.azurewebsites.net/speaker/1/sessions"
              }
            ]
          },
    [...]
    ```

### <a name="test-an-insecure-api-call"></a>Testare una chiamata API non sicura

Dopo che è stata eseguita una richiesta con esito positivo, testare il caso di errore per verificare che le chiamate all'API con un token *non valido* vengano rifiutate come previsto. Per eseguire il test, è ad esempio possibile aggiungere o modificare alcuni caratteri nel valore del token e quindi eseguire la stessa richiesta `GET` come in precedenza.

1. Aggiungere alcuni caratteri al valore del token per simulare un token non valido. Ad esempio, aggiungere "INVALID" al valore del token:

    ![Sezione Headers dell'interfaccia utente di Postman con la dicitura INVALID aggiunta al token](media/secure-apim-with-b2c-token/postman-02-invalid-token.png)

1. Selezionare il pulsante **Send** (Invia) per eseguire la richiesta. Con un token non valido, il risultato previsto è un codice di stato non autorizzato `401`:

    ```json
    {
        "statusCode": 401,
        "message": "Unauthorized. Access token is missing or invalid."
    }
    ```

Se viene visualizzato il codice di stato `401`, è stato verificato che solo i chiamanti con un token di accesso valido emesso da Azure AD B2C possono eseguire richieste con esito positivo all'API di Gestione API di Azure.

## <a name="support-multiple-applications-and-issuers"></a>Supportare più applicazioni e autorità emittenti

Diverse applicazioni interagiscono in genere con una sola API REST. Per consentire all'API di accettare token destinati a più applicazioni, aggiungere i relativi ID applicazione all'elemento `<audiences>` nel criterio in ingresso di Gestione API.

```xml
<!-- Accept tokens intended for these recipient applications -->
<audiences>
    <audience>44444444-0000-0000-0000-444444444444</audience>
    <audience>66666666-0000-0000-0000-666666666666</audience>
</audiences>
```

Analogamente, per supportare più autorità di emissione di token, aggiungere i relativi URI endpoint all'elemento `<issuers>` nel criterio in ingresso di Gestione API.

```xml
<!-- Accept tokens from multiple issuers -->
<issuers>
    <issuer>https://<tenant-name>.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
    <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
</issuers>
```

## <a name="migrate-to-b2clogincom"></a>Eseguire la migrazione a b2clogin.com

Se è presente un'API di Gestione API che convalida i token emessi dall'endpoint `login.microsoftonline.com` legacy, è necessario eseguire la migrazione dell'API e delle applicazioni che la chiamano per usare i token emessi da [b2clogin.com](b2clogin.md).

Per eseguire una migrazione a fasi, è possibile seguire questa procedura generale:

1. Nel criterio in ingresso di Gestione API aggiungere il supporto per i token emessi da b2clogin.com e login.microsoftonline.com.
1. Aggiornare le applicazioni una alla volta per ottenere i token dall'endpoint b2clogin.com.
1. Dopo che tutte le applicazioni hanno correttamente ottenuto i token da b2clogin.com, rimuovere dall'API il supporto per i token emessi da login.microsoftonline.com.

L'esempio seguente del criterio in ingresso di Gestione API illustra come accettare i token emessi da b2clogin.com e login.microsoftonline.com. Supporta inoltre le richieste API di due applicazioni.

```xml
<policies>
    <inbound>
        <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
            <openid-config url="https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1_signupsignin1/v2.0/.well-known/openid-configuration" />
            <audiences>
                <audience>44444444-0000-0000-0000-444444444444</audience>
                <audience>66666666-0000-0000-0000-666666666666</audience>
            </audiences>
            <issuers>
                <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
                <issuer>https://<tenant-name>.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
            </issuers>
        </validate-jwt>
        <base />
    </inbound>
    <backend> <base /> </backend>
    <outbound> <base /> </outbound>
    <on-error> <base /> </on-error>
</policies>
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui criteri di Gestione API di Azure, vedere l'[indice di riferimento dei criteri di Gestione API](../api-management/api-management-policies.md).

Per informazioni sulla migrazione delle API Web basate su OWIN e delle relative applicazioni a b2clogin.com, vedere [Eseguire la migrazione di un'API Web basata su OWIN a b2clogin.com](multiple-token-endpoints.md).
