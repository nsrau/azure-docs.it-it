---
title: Proteggere un'API di gestione API di Azure usando Azure Active Directory B2C
description: Informazioni su come usare i token di accesso emessi da Azure Active Directory B2C per proteggere un endpoint API di gestione API di Azure.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 4c42959d46aa522042275456a87e590f9e009348
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70183075"
---
# <a name="secure-an-azure-api-management-api-with-azure-ad-b2c"></a>Proteggere un'API di gestione API di Azure con Azure AD B2C

Informazioni su come limitare l'accesso all'API di gestione API di Azure (gestione API) ai client che hanno eseguito l'autenticazione con Azure Active Directory B2C (Azure AD B2C). Eseguire la procedura descritta in questo articolo per creare e testare un criterio in ingresso in gestione API che limita l'accesso solo alle richieste che includono un token di accesso Azure AD B2C emesso valido.

## <a name="prerequisites"></a>Prerequisiti

Prima di continuare con i passaggi descritti in questo articolo, è necessario disporre delle risorse seguenti:

* [Tenant di Azure AD B2C](tutorial-create-tenant.md)
* [Applicazione registrata](tutorial-register-applications.md) nel tenant
* [Flussi utente creati](tutorial-create-user-flows.md) nel tenant
* [API pubblicata](../api-management/import-and-publish.md) in gestione API di Azure
* [Impostore](https://www.getpostman.com/) per testare l'accesso protetto (facoltativo)

## <a name="get-azure-ad-b2c-application-id"></a>Ottenere Azure AD B2C ID applicazione

Quando si protegge un'API in gestione API di Azure con Azure AD B2C, sono necessari diversi valori per i [criteri in ingresso](../api-management/api-management-howto-policies.md) creati in gestione API. Prima di tutto, registrare l'ID applicazione di un'applicazione creata in precedenza nel tenant del Azure AD B2C. Se si usa l'applicazione creata nei prerequisiti, usare l'ID applicazione per *webbapp1*.

1. Passare al tenant di Azure AD B2C nel [portale di Azure](https://portal.azure.com).
1. In **Gestisci**selezionare **applicazioni**.
1. Registrare il valore nell' **ID applicazione** per *app Web 1* o un'altra applicazione creata in precedenza.

  ![Posizione dell'ID applicazione di un'applicazione B2C nel portale di Azure](media/secure-apim-with-b2c-token/portal-02-app-id.png)

## <a name="get-token-issuer-endpoint"></a>Ottenere l'endpoint dell'emittente del token

Ottenere quindi l'URL di configurazione noto per uno dei flussi utente di Azure AD B2C. È necessario anche l'URI dell'endpoint dell'emittente del token che si vuole supportare in gestione API di Azure.

1. Passare al tenant di Azure AD B2C nel [portale di Azure](https://portal.azure.com).
1. In **criteri**selezionare **flussi utente (criteri)** .
1. Selezionare un criterio esistente, ad esempio *B2C_1_signupsignin1*, quindi selezionare **Esegui flusso utente**.
1. Registrare l'URL in collegamento ipertestuale visualizzato sotto l'intestazione **Esegui flusso utente** nella parte superiore della pagina. Questo URL è l'endpoint di individuazione noto di OpenID Connect per il flusso utente e viene usato nella sezione successiva quando si configura il criterio in ingresso in gestione API di Azure.

    ![Collegamento ipertestuale URI noto nella pagina Esegui ora della portale di Azure](media/secure-apim-with-b2c-token/portal-01-policy-link.png)

1. Selezionare il collegamento ipertestuale per passare alla pagina di configurazione nota di OpenID Connect.
1. Nella pagina visualizzata nel browser registrare il `issuer` valore, ad esempio:

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

    Questo valore viene usato nella sezione successiva quando si configura l'API in gestione API di Azure.

A questo punto dovrebbero essere registrati due URL da usare nella sezione successiva: l'URL dell'endpoint di configurazione noto di OpenID Connect e l'URI dell'autorità emittente. Ad esempio:

```
https://yourb2ctenant.b2clogin.com/yourb2ctenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_signupsignin1
https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/
```

## <a name="configure-inbound-policy-in-azure-api-management"></a>Configurare i criteri in ingresso in gestione API di Azure

A questo punto è possibile aggiungere i criteri in ingresso in gestione API di Azure per la convalida delle chiamate API. Aggiungendo un criterio di [convalida JWT](../api-management/api-management-access-restriction-policies.md#ValidateJWT) che verifica i destinatari e l'autorità emittente in un token di accesso, è possibile assicurarsi che vengano accettate solo le chiamate API con un token valido.

1. Selezionare l'istanza di Gestione API di Azure nel [portale di Azure](https://portal.azure.com).
1. Selezionare **API**.
1. Selezionare l'API che si vuole proteggere con Azure AD B2C.
1. Selezionare la scheda **Progettazione**.
1. In **elaborazione in ingresso** **\< / selezionare\>** per aprire l'editor del codice dei criteri.
1. Inserire il seguente `<validate-jwt>` tag all'interno `<inbound>` del criterio.

    1. Aggiornare il `url` valore `<openid-config>` nell'elemento con l'URL di configurazione noto del criterio.
    1. Aggiornare l' `<audience>` elemento con l'ID applicazione dell'applicazione creata in precedenza nel tenant B2C (ad esempio, *app Web 1*).
    1. Aggiornare l' `<issuer>` elemento con l'endpoint dell'emittente del token registrato in precedenza.

    ```xml
    <policies>
        <inbound>
            <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
                <openid-config url="https://yourb2ctenant.b2clogin.com/yourb2ctenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_signupsignin1" />
                <audiences>
                    <audience>44444444-0000-0000-0000-444444444444</audience>
                </audiences>
                <issuers>
                    <issuer>https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
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

Per garantire che solo i chiamanti autenticati possano accedere all'API, è possibile convalidare la configurazione di gestione API di Azure chiamando l'API con il [post](https://www.getpostman.com/).

Per chiamare l'API, è necessario un token di accesso emesso da Azure AD B2C e una chiave di sottoscrizione gestione API.

### <a name="get-an-access-token"></a>Ottenere un token di accesso

Per prima cosa, è necessario un token emesso da Azure ad B2C da `Authorization` usare nell'intestazione del post. È possibile ottenerne uno usando la funzionalità **Esegui ora** del flusso dell'utente di iscrizione/accesso che è stato creato come uno dei prerequisiti.

1. Passare al tenant di Azure AD B2C nel [portale di Azure](https://portal.azure.com).
1. In **criteri**selezionare **flussi utente (criteri)** .
1. Selezionare un flusso utente di iscrizione/accesso esistente, ad esempio *B2C_1_signupsignin1*.
1. Per **applicazione**selezionare *app Web 1*.
1. Per **URL di risposta**scegliere `https://jwt.ms`.
1. Selezionare **Esegui il flusso utente**.

    ![Pagina Esegui flusso utente per il flusso di accesso dell'utente in portale di Azure](media/secure-apim-with-b2c-token/portal-03-user-flow.png)

1. Completare il processo di accesso. Si dovrebbe essere reindirizzati a `https://jwt.ms`.
1. Registrare il valore del token codificato visualizzato nel browser. Usare questo valore del token per l'intestazione dell'autorizzazione in post.

    ![Valore del token codificato visualizzato in jwt.ms](media/secure-apim-with-b2c-token/jwt-ms-01-token.png)

### <a name="get-api-subscription-key"></a>Ottenere la chiave di sottoscrizione API

Un'applicazione client (in questo caso, poster) che chiama un'API pubblicata deve includere una chiave di sottoscrizione di gestione API valida nelle richieste HTTP all'API. Per ottenere una chiave di sottoscrizione da includere nella richiesta HTTP del post:

1. Passare all'istanza del servizio gestione API di Azure nel [portale di Azure](https://portal.azure.com).
1. Selezionare **Sottoscrizioni**.
1. Selezionare i puntini **di sospensione per il prodotto: Illimitata**, quindi selezionare **Mostra/Nascondi chiavi**.
1. Registrare la **chiave primaria** per il prodotto. Usare questa chiave per l' `Ocp-Apim-Subscription-Key` intestazione nella richiesta HTTP in postazione.

![Pagina chiave sottoscrizione con le chiavi Mostra/Nascondi selezionate in portale di Azure](media/secure-apim-with-b2c-token/portal-04-api-subscription-key.png)

### <a name="test-a-secure-api-call"></a>Testare una chiamata API protetta

Con il token di accesso e la chiave di sottoscrizione gestione API registrati, è ora possibile verificare se l'accesso sicuro all'API è stato configurato correttamente.

1. Creare una nuova `GET` richiesta in [post](https://www.getpostman.com/). Per l'URL della richiesta, specificare l'endpoint dell'elenco dei relatori dell'API pubblicata come uno dei prerequisiti. Esempio:

    `https://contosoapim.azure-api.net/conference/speakers`

1. Aggiungere quindi le intestazioni seguenti:

    | Chiave | Valore |
    | --- | ----- |
    | `Authorization` | Valore del token codificato registrato in precedenza, con `Bearer ` prefisso (includere lo spazio dopo "Bearer") |
    | `Ocp-Apim-Subscription-Key` | Chiave di sottoscrizione gestione API registrata in precedenza |

    L'URL e le **intestazioni** della richiesta **Get** verranno visualizzati in modo simile a:

    ![Interfaccia utente del post che mostra le intestazioni e l'URL della richiesta GET](media/secure-apim-with-b2c-token/postman-01-headers.png)

1. Per eseguire la richiesta, selezionare il pulsante **Send** in postin. Se tutti gli elementi sono stati configurati correttamente, viene visualizzata una risposta JSON con una raccolta di speaker della conferenza (qui troncata):

    ```JSON
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

Ora che è stata effettuata una richiesta con esito positivo, testare il caso di errore per assicurarsi che le chiamate all'API con un token *non valido* vengano rifiutate come previsto. Un modo per eseguire il test consiste nell'aggiungere o modificare alcuni caratteri nel valore del token, quindi eseguire la stessa `GET` richiesta che precede.

1. Aggiungere diversi caratteri al valore del token per simulare un token non valido. Ad esempio, aggiungere "non valido" al valore del token:

    ![Sezione intestazioni dell'interfaccia utente del post che mostra aggiunta non valida al token](media/secure-apim-with-b2c-token/postman-02-invalid-token.png)

1. Selezionare il pulsante **Send (Invia** ) per eseguire la richiesta. Con un token non valido, il risultato previsto è `401` un codice di stato non autorizzato:

    ```JSON
    {
        "statusCode": 401,
        "message": "Unauthorized. Access token is missing or invalid."
    }
    ```

Se viene visualizzato il `401` codice di stato, si è verificato che solo i chiamanti con un token di accesso valido rilasciato da Azure ad B2C possibile eseguire correttamente le richieste all'API di gestione API di Azure.

## <a name="support-multiple-applications-and-issuers"></a>Supportare più applicazioni e autorità emittenti

Diverse applicazioni in genere interagiscono con una singola API REST. Per consentire a più applicazioni di chiamare l'API, aggiungere gli ID applicazione all' `<audiences>` elemento nei criteri in ingresso gestione API.

```XML
<!-- Accept requests from multiple applications -->
<audiences>
    <audience>44444444-0000-0000-0000-444444444444</audience>
    <audience>66666666-0000-0000-0000-666666666666</audience>
</audiences>
```

Analogamente, per supportare più autorità di certificazione, aggiungere gli URI degli `<audiences>` endpoint all'elemento nei criteri in ingresso gestione API.

```XML
<!-- Accept tokens from multiple issuers -->
<issuers>
    <issuer>https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
    <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
</issuers>
```

## <a name="migrate-to-b2clogincom"></a>Eseguire la migrazione a b2clogin.com

Se si dispone di un'API Gestione API che convalida i token emessi dall'endpoint legacy `login.microsoftonline.com` , è necessario eseguire la migrazione dell'API e delle applicazioni che la chiamano per usare i token emessi da [b2clogin.com](b2clogin.md).

È possibile seguire questo processo generale per eseguire una migrazione di gestione temporanea:

1. Aggiungere il supporto nei criteri in ingresso gestione API per i token rilasciati da b2clogin.com e login.microsoftonline.com.
1. Aggiornare le applicazioni una alla volta per ottenere i token dall'endpoint b2clogin.com.
1. Una volta che tutte le applicazioni ottengono correttamente i token da b2clogin.com, rimuovere il supporto per i token rilasciati da login.microsoftonline.com dall'API.

L'esempio seguente gestione API i criteri in ingresso illustrano come accettare i token rilasciati da b2clogin.com e login.microsoftonline.com. Supporta inoltre le richieste API di due applicazioni.

```XML
<policies>
    <inbound>
        <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
            <openid-config url="https://yourb2ctenant.b2clogin.com/yourb2ctenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_signupsignin1" />
            <audiences>
                <audience>44444444-0000-0000-0000-444444444444</audience>
                <audience>66666666-0000-0000-0000-666666666666</audience>
            </audiences>
            <issuers>
                <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
                <issuer>https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
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

Per altre informazioni sui criteri di gestione API di Azure, vedere l' [indice di riferimento dei criteri di gestione API](../api-management/api-management-policies.md).

È possibile trovare informazioni sulla migrazione di API Web basate su OWIN e delle relative applicazioni a b2clogin.com in [eseguire la migrazione di un'API Web basata su OWIN a b2clogin.com](multiple-token-endpoints.md).
