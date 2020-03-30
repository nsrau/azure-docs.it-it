---
title: Proteggere un'API di gestione delle API di Azure tramite Azure Active Directory B2CSecure an Azure API Management API API by using Azure Active Directory B2C
description: Informazioni su come usare i token di accesso rilasciati da Azure Active Directory B2C per proteggere un endpoint API di gestione API di Azure.Learn how to use access tokens issued by Azure Active Directory B2C to secure an Azure API Management API endpoint.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 00938d831e70289b24acb599b81016aa6e564d78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186931"
---
# <a name="secure-an-azure-api-management-api-with-azure-ad-b2c"></a>Proteggere un'API di gestione delle API di Azure con Azure AD B2CSecure an Azure API Management API API with Azure AD B2C

Informazioni su come limitare l'accesso all'API di Gestione API di Azure ai client autenticati con Azure Active Directory B2C (Azure AD B2C). Seguire i passaggi descritti in questo articolo per creare e testare criteri in ingresso in Gestione API che limitano l'accesso solo alle richieste che includono un token di accesso rilasciato da Azure AD B2C valido.

## <a name="prerequisites"></a>Prerequisiti

Prima di continuare con i passaggi descritti in questo articolo, sono necessarie le risorse seguenti:You need the following resources in place before continue with the steps in this article:

* [Tenant di Azure AD B2C](tutorial-create-tenant.md)
* [Applicazione registrata](tutorial-register-applications.md) nel tenant
* [Flussi utente creati](tutorial-create-user-flows.md) nel tenant
* [API pubblicata](../api-management/import-and-publish.md) in Gestione API di AzurePublished API in Azure API Management
* [Postino](https://www.getpostman.com/) per testare l'accesso protetto (opzionale)

## <a name="get-azure-ad-b2c-application-id"></a>Ottenere l'ID applicazione B2C di Azure ADGet Azure AD B2C application ID

Quando si protegge un'API in Gestione API di Azure con Azure AD B2C, sono necessari diversi valori per i [criteri in ingresso](../api-management/api-management-howto-policies.md) creati in Gestione API. Registrare innanzitutto l'ID applicazione di un'applicazione creata in precedenza nel tenant B2C di Azure AD. Se si utilizza l'applicazione creata nei prerequisiti, utilizzare l'ID applicazione per *webbapp1*.

Puoi utilizzare l'esperienza corrente di **Applicazioni** o la nostra nuova esperienza di registrazione unificata delle **app (anteprima)** per ottenere l'ID applicazione. [Altre informazioni sulla nuova esperienza](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Applicazioni](#tab/applications/)

1. Accedere al [portale](https://portal.azure.com)di Azure .
1. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel menu a sinistra selezionare **Azure AD B2C**. In alternativa, selezionare **Tutti i servizi** e quindi cercare e selezionare **Azure AD B2C**.
1. In **Gestisci**selezionare **Applicazioni**.
1. Registrare il valore nella colonna **ID APPLICATION** per *webapp1* o un'altra applicazione creata in precedenza.

#### <a name="app-registrations-preview"></a>[Registrazioni app (anteprima)](#tab/app-reg-preview/)

1. Accedere al [portale](https://portal.azure.com)di Azure .
1. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel menu a sinistra selezionare **Azure AD B2C**. In alternativa, selezionare **Tutti i servizi** e quindi cercare e selezionare **Azure AD B2C**.
1. Seleziona **Registrazioni app (anteprima)**, quindi seleziona la scheda **Applicazioni di** proprietà.
1. Registrare il valore nella colonna **ID applicazione (client)** per *webapp1* o un'altra applicazione creata in precedenza.

* * *

## <a name="get-token-issuer-endpoint"></a>Ottenere l'endpoint dell'autorità emittente di tokenGet token issuer endpoint

Ottenere quindi l'URL di configurazione noto per uno dei flussi utente B2C di Azure AD. You also need the token issuer endpoint URI you want to support in Azure API Management.

1. Passare al tenant B2C di Azure AD nel portale di [Azure.](https://portal.azure.com)
1. In **Criteri**selezionare **Flussi utente (criteri)**.
1. Selezionare un criterio esistente, ad esempio *B2C_1_signupsignin1*, quindi selezionare **Esegui flusso utente**.
1. Registrare l'URL nel collegamento ipertestuale visualizzato sotto l'intestazione **Esegui flusso utente** nella parte superiore della pagina. Questo URL è l'endpoint di individuazione noto di OpenID Connect per il flusso utente e lo si usa nella sezione successiva quando si configurano i criteri in ingresso in Gestione API di Azure.This URL is the OpenID Connect well-known discovery endpoint for the user flow, and you use it in the next section when you configure the inbound policy in Azure API Management.

    ![Collegamento ipertestuale URI noto nella pagina Esegui ora del portale di Azure](media/secure-apim-with-b2c-token/portal-01-policy-link.png)

1. Selezionare il collegamento ipertestuale per passare alla pagina di configurazione nota di OpenID Connect.
1. Nella pagina visualizzata nel browser, `issuer` registrare il valore, ad esempio:

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

    You use this value in the next section when you configure your API in Azure API Management.

Nella sezione successiva dovrebbero essere registrati due URL da utilizzare: l'URL dell'endpoint di configurazione noto OpenID Connect e l'URI dell'autorità emittente. Ad esempio:

```
https://yourb2ctenant.b2clogin.com/yourb2ctenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_signupsignin1
https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/
```

## <a name="configure-inbound-policy-in-azure-api-management"></a>Configurare i criteri in ingresso in Gestione API di AzureConfigure inbound policy in Azure API Management

È ora possibile aggiungere i criteri in ingresso in Gestione API di Azure che convalidano le chiamate API. Aggiungendo un criterio di [convalida JWT](../api-management/api-management-access-restriction-policies.md#ValidateJWT) che verifica il gruppo di destinatari e l'autorità di certificazione in un token di accesso, è possibile assicurarsi che vengano accettate solo le chiamate API con un token valido.

1. Selezionare l'istanza di Gestione API di Azure nel [portale di Azure](https://portal.azure.com).
1. Selezionare **API**.
1. Selezionare l'API che si vuole proteggere con Azure AD B2C.
1. Selezionare la scheda **Progettazione**.
1. In **Elaborazione in ingresso**selezionare ** \< / ** per aprire l'editor di codice dei criteri.
1. Inserire il `<validate-jwt>` tag `<inbound>` seguente all'interno del criterio.

    1. Aggiornare `url` il `<openid-config>` valore nell'elemento con l'URL di configurazione noto del criterio.
    1. Aggiornare `<audience>` l'elemento con ID applicazione dell'applicazione creata in precedenza nel tenant B2C, ad esempio *webapp1*.
    1. Aggiornare `<issuer>` l'elemento con l'endpoint dell'autorità emittente di token registrato in precedenza.

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

## <a name="validate-secure-api-access"></a>Convalidare l'accesso sicuro alle APIValidate secure API access

Per garantire che solo i chiamanti autenticati possano accedere all'API, è possibile convalidare la configurazione di Gestione API di Azure chiamando l'API con [Postman](https://www.getpostman.com/).

Per chiamare l'API, sono necessari sia un token di accesso rilasciato da Azure AD B2C che una chiave di sottoscrizione APIM.

### <a name="get-an-access-token"></a>Ottenere un token di accesso

È innanzitutto necessario un token rilasciato da Azure `Authorization` AD B2C da usare nell'intestazione in Postman.You first need a token issued by Azure AD B2C to use in the header in Postman. È possibile ottenerlo utilizzando la funzionalità **Esegui ora** del flusso utente di iscrizione/accesso che è necessario aver creato come uno dei prerequisiti.

1. Passare al tenant B2C di Azure AD nel portale di [Azure.](https://portal.azure.com)
1. In **Criteri**selezionare **Flussi utente (criteri)**.
1. Selezionare un flusso utente di iscrizione/accesso esistente, ad esempio *B2C_1_signupsignin1*.
1. Per **Applicazione**, selezionare *webapp1*.
1. Per **URL**risposta `https://jwt.ms`, scegliere .
1. Selezionare **Esegui flusso utente**.

    ![Eseguire la pagina Del flusso utente per l'accesso al flusso utente nel portale di AzureRun user flow page for sign up sign up user flow in Azure portal](media/secure-apim-with-b2c-token/portal-03-user-flow.png)

1. Completare il processo di accesso. Si dovrebbe essere `https://jwt.ms`reindirizzati a .
1. Registrare il valore del token codificato visualizzato nel browser. Utilizzare questo valore di token per l'intestazione Authorization in Postman.

    ![Valore del token codificato visualizzato in jwt.ms](media/secure-apim-with-b2c-token/jwt-ms-01-token.png)

### <a name="get-api-subscription-key"></a>Ottenere la chiave di sottoscrizione dell'APIGet API subscription key

Un'applicazione client (in questo caso Postman) che chiama un'API pubblicata deve includere una chiave di sottoscrizione di Gestione API valida nelle richieste HTTP all'API. Per ottenere un codice di abbonamento da includere nella richiesta HTTP Postman:

1. Passare all'istanza del servizio Gestione API di Azure nel portale di Azure.Browse to your Azure API Management service instance in the [Azure portal](https://portal.azure.com).
1. Selezionare **Abbonamenti**.
1. Selezionare i lipsiani per **Prodotto: Illimitato**, quindi selezionare **Mostra/Nascondi chiavi**.
1. Registrare la **PRIMARY KEY** per il prodotto. Utilizzare questa chiave `Ocp-Apim-Subscription-Key` per l'intestazione nella richiesta HTTP in Postman.

![Pagina della chiave di sottoscrizione con Mostra/Nascondi chiavi selezionate nel portale di AzureSubscription key page with Show/hide keys selected in Azure portal](media/secure-apim-with-b2c-token/portal-04-api-subscription-key.png)

### <a name="test-a-secure-api-call"></a>Testare una chiamata API sicuraTest a secure API call

Dopo aver registrato il token di accesso e la chiave di sottoscrizione APIM, è ora possibile verificare se l'accesso sicuro all'API è stato configurato correttamente.

1. Creare una `GET` nuova richiesta in [Postman](https://www.getpostman.com/). Per l'URL della richiesta, specificare l'endpoint dell'elenco degli altoparlanti dell'API pubblicata come uno dei prerequisiti. Ad esempio:

    `https://contosoapim.azure-api.net/conference/speakers`

1. Successivamente, aggiungere le intestazioni seguenti:Next, add the following headers:

    | Chiave | valore |
    | --- | ----- |
    | `Authorization` | Valore del token codificato registrato `Bearer ` in precedenza, preceduto da (includere lo spazio dopo "Bearer") |
    | `Ocp-Apim-Subscription-Key` | Chiave di sottoscrizione APIM registrata in precedenza |

    L'URL e le **intestazioni** della richiesta **GET** dovrebbero essere simili ai:

    ![Interfaccia utente di Postman che mostra l'URL e le intestazioni della richiesta GET](media/secure-apim-with-b2c-token/postman-01-headers.png)

1. Selezionare il pulsante **Invia** in Postman per eseguire la richiesta. Se tutto è stato configurato correttamente, si dovrebbe essere presentato con una risposta JSON con una raccolta di relatori della conferenza (mostrato qui troncato):

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

### <a name="test-an-insecure-api-call"></a>Testare una chiamata API non sicuraTest an unsecure API call

Dopo aver effettuato una richiesta corretta, verificare il caso di errore per assicurarsi che le chiamate all'API con un token *non valido* vengano rifiutate come previsto. Un modo per eseguire il test consiste nell'aggiungere o modificare alcuni `GET` caratteri nel valore del token, quindi eseguire la stessa richiesta di prima.

1. Aggiungere diversi caratteri al valore del token per simulare un token non valido. Ad esempio, aggiungere "INVALID" al valore del token:

    ![Sezione Headers dell'interfaccia utente postman che mostra INVALID aggiunto al token](media/secure-apim-with-b2c-token/postman-02-invalid-token.png)

1. Selezionare il pulsante **Invia** per eseguire la richiesta. Con un token non valido, `401` il risultato previsto è un codice di stato non autorizzato:With an invalid token, the expected result is a unauthorized status code:

    ```JSON
    {
        "statusCode": 401,
        "message": "Unauthorized. Access token is missing or invalid."
    }
    ```

Se viene `401` visualizzato il codice di stato, è stato verificato che solo i chiamanti con un token di accesso valido emesso da Azure AD B2C possono effettuare richieste riuscite all'API di gestione delle API di Azure.If you see the status code, you've verified that only callers with a valid access token issued by Azure AD B2C can make successful requests to your Azure API Management API API.

## <a name="support-multiple-applications-and-issuers"></a>Supporta più applicazioni ed emittenti

Diverse applicazioni interagiscono in genere con una singola API REST. Per consentire all'API di accettare i token destinati a più `<audiences>` applicazioni, aggiungere gli ID applicazione all'elemento nei criteri in ingresso di GESTIONE APIM.

```XML
<!-- Accept tokens intended for these recipient applications -->
<audiences>
    <audience>44444444-0000-0000-0000-444444444444</audience>
    <audience>66666666-0000-0000-0000-666666666666</audience>
</audiences>
```

Analogamente, per supportare più autorità di certificazione di token, aggiungere i relativi URI di endpoint all'elemento `<issuers>` nei criteri in ingresso di GESTIONE APIM.

```XML
<!-- Accept tokens from multiple issuers -->
<issuers>
    <issuer>https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
    <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
</issuers>
```

## <a name="migrate-to-b2clogincom"></a>Eseguire la migrazione a b2clogin.com

Se si dispone di un'API di gestione `login.microsoftonline.com` API che convalida i token rilasciati dall'endpoint legacy, è necessario eseguire la migrazione dell'API e delle applicazioni che la chiamano per utilizzare i token emessi da [b2clogin.com](b2clogin.md).

È possibile seguire questo processo generale per eseguire una migrazione in fasi:You can follow this general process to perform a staged migration:

1. Aggiungere il supporto nei criteri in ingresso di Gestione API Per i token emessi sia da b2clogin.com che da login.microsoftonline.com.
1. Aggiornare le applicazioni una alla volta per ottenere token dall'endpoint b2clogin.com.
1. Una volta che tutte le applicazioni ottengono correttamente i token da b2clogin.com, rimuovere il supporto per i token emessi da login.microsoftonline.com dall'API.

I criteri in ingresso di esempio seguenti illustrano come accettare i token emessi sia da b2clogin.com che da login.microsoftonline.com. Inoltre, supporta le richieste API da due applicazioni.

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

Per altri dettagli sui criteri di Gestione API di Azure, vedere l'indice di [riferimento dei criteri APIM.](../api-management/api-management-policies.md)

Per informazioni sulla migrazione delle API Web basate su OWIN e delle relative applicazioni, b2clogin.com in Eseguire la migrazione di [un'API Web basata](multiple-token-endpoints.md)su OWIN in b2clogin.com .
