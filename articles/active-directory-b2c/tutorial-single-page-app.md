---
title: "Esercitazione: Abilitare l'autenticazione in un'app a pagina singola"
titleSuffix: Azure AD B2C
description: Questa esercitazione illustra come usare Azure Active Directory B2C per consentire l'accesso degli utenti a un'applicazione a pagina singola basata su JavaScript.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/04/2020
ms.custom: mvc, seo-javascript-september2019, devx-track-js
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 6a9f3b864bd8aba2140c7d32d4b5474ff7b95f88
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96171229"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-with-azure-ad-b2c"></a>Esercitazione: Abilitare l'autenticazione in un'applicazione a pagina singola con Azure AD B2C

Questa esercitazione illustra come usare Azure Active Directory B2C (Azure AD B2C) per l'iscrizione e l'accesso degli utenti in un'applicazione a pagina singola usando:
* [Flusso del codice di autorizzazione OAuth 2.0](./authorization-code-flow.md) (con [MSAL.js 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser))
* [Flusso di concessione implicita OAuth 2.0](./implicit-flow-single-page-application.md) (con [MSAL.js 1.x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core))

In questa esercitazione, che è la prima di una serie in due parti, verrà descritto come:

> [!div class="checklist"]
> * Aggiungere un URL di risposta a un'applicazione registrata nel tenant di Azure AD B2C
> * Scaricare il codice di esempio da GitHub
> * Modificare il codice dell'applicazione di esempio in modo che funzioni con il proprio tenant
> * Eseguire l'iscrizione usando il flusso utente di iscrizione/accesso

L'[esercitazione successiva](tutorial-single-page-app-webapi.md) della serie abilita la parte relativa all'API Web dell'esempio di codice.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Prima di continuare con i passaggi in questa esercitazione è necessario implementare le seguenti risorse di Azure AD B2C:

* [Tenant di Azure AD B2C](tutorial-create-tenant.md)
* [Applicazione registrata](tutorial-register-spa.md) nel tenant
* [Flussi utente creati](tutorial-create-user-flows.md) nel tenant

In più, nell'ambiente di sviluppo locale devono anche essere installati gli elementi seguenti:

* [Visual Studio Code](https://code.visualstudio.com/) o un altro editor di codice
* [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>Aggiornare l'applicazione

Nella [seconda esercitazione](./tutorial-register-spa.md) completata come parte dei prerequisiti è stata registrata un'applicazione a pagina singola in Azure AD B2C. Per consentire la comunicazione con l'esempio in questa esercitazione, aggiungere un URL di risposta (anche noto come URI di reindirizzamento) alla registrazione dell'applicazione.

Per aggiornare un'applicazione nel tenant di Azure AD B2C, è possibile usare la nuova esperienza unificata **Registrazioni app** oppure l'esperienza legacy **Applicazioni (legacy)** . [Altre informazioni sulla nuova esperienza](./app-registrations-training-guide.md).

#### <a name="app-registrations-auth-code-flow"></a>[Registrazioni app (flusso del codice di autorizzazione)](#tab/app-reg-auth/)

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel menu a sinistra selezionare **Azure AD B2C**. In alternativa, selezionare **Tutti i servizi** e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **Registrazioni app**, la scheda **Applicazioni di cui si è proprietari** e quindi l'applicazione *spaapp1*.
1. In **Applicazione a pagina singola** selezionare il collegamento **Aggiungi URI**, quindi immettere `http://localhost:6420`.
1. Selezionare **Salva**.
1. Selezionare **Panoramica**.
1. Prendere nota del valore di **ID applicazione (client)** che verrà usato in un passaggio successivo quando si aggiorna il codice nell'applicazione Web a pagina singola.

#### <a name="app-registrations-implicit-flow"></a>[Registrazioni app (flusso implicito)](#tab/app-reg-implicit/)

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel menu a sinistra selezionare **Azure AD B2C**. In alternativa, selezionare **Tutti i servizi** e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **Registrazioni app**, la scheda **Applicazioni di cui si è proprietari** e quindi l'applicazione *spaapp1*.
1. In **Applicazione a pagina singola** selezionare il collegamento **Aggiungi URI**, quindi immettere `http://localhost:6420`.
1. In **Concessione implicita** selezionare le caselle di controllo **Token di accesso** e **Token ID**, se non sono già selezionate, quindi selezionare **Salva**.
1. Selezionare **Panoramica**.
1. Prendere nota del valore di **ID applicazione (client)** che verrà usato in un passaggio successivo quando si aggiorna il codice nell'applicazione Web a pagina singola.

#### <a name="applications-legacy"></a>[Applicazioni (legacy)](#tab/applications-legacy/)

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant.
1. Selezionare **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **Applicazioni (legacy)** e quindi l'applicazione *spaapp1*.
1. In **URL di risposta** aggiungere `http://localhost:6420`.
1. Selezionare **Salva**.
1. Nella pagina delle proprietà, registrare l' **ID applicazione**. Si userà l'ID dell'app in un passaggio successivo quando si aggiorna il codice nell'applicazione Web a pagina singola.

* * *

## <a name="get-the-sample-code"></a>Scaricare il codice di esempio

In questa esercitazione verrà configurato un esempio di codice che si scarica da GitHub per usare il proprio tenant B2C. L'esempio dimostra come un'applicazione a pagina singola può usare Azure AD B2C per l'iscrizione e l'accesso degli utenti e per chiamare un'API Web protetta (l'API Web verrà abilitata nell'esercitazione successiva della serie).

* Esempio per il flusso di codice di autorizzazione MSAL.js 2.x:

    [Scaricare un file ZIP](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/archive/main.zip) o clonare l'esempio da GitHub:

    ```
    git clone https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa.git
    ```
* Esempio per il flusso implicito MSAL.js 1.x:

    [Scaricare un file ZIP](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) o clonare l'esempio da GitHub:

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
    ```

## <a name="update-the-sample"></a>Aggiornare l'esempio

Ora che è stato ottenuto l'esempio, aggiornare il codice con il nome del tenant di Azure AD B2C e l'ID dell'applicazione registrato in un passaggio precedente.

#### <a name="auth-code-flow-sample"></a>[Esempio per il flusso del codice di autorizzazione](#tab/config-auth/)

1. Aprire il file *authConfig.js* nella cartella *App*.
1. Nell'oggetto `msalConfig` individuare l'assegnazione per `clientId` e sostituirla con l'**ID applicazione (client)** registrato in un passaggio precedente.
1. Aprire il file `policies.js`.
1. Individuare le voci in `names` e sostituire le assegnazioni corrispondenti con il nome dei flussi utente creati in un passaggio precedente, ad esempio `B2C_1_signupsignin1`.
1. Individuare le voci in `authorities` e sostituirle in base alle esigenze con i nomi dei flussi utente creati in un passaggio precedente, ad esempio `https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>`.
1. Individuare l'assegnazione per `authorityDomain` e sostituirla con `<your-tenant-name>.b2clogin.com`.
1. Aprire il file `apiConfig.js`.
1. Individuare l'assegnazione per `b2cScopes` e sostituire l'URL con l'URL dell'ambito creato per l'API Web, ad esempio `b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/helloapi/demo.read"]`.
1. Individuare l'assegnazione per `webApi` e sostituire l'URL corrente con l'URL in cui è stata distribuita l'API Web nel passaggio 4, ad esempio `webApi: http://localhost:5000/hello`.

#### <a name="implicit-flow-sample"></a>[Esempio per il flusso implicito](#tab/config-implicit/)

1. Aprire il file *authConfig.js* nella cartella *JavaScriptSPA*.
1. Nell'oggetto `msalConfig` individuare l'assegnazione per `clientId` e sostituirla con l'**ID applicazione (client)** registrato in un passaggio precedente.
1. Aprire il file `policies.js`.
1. Individuare le voci in `names` e sostituire le assegnazioni corrispondenti con il nome dei flussi utente creati in un passaggio precedente, ad esempio `B2C_1_signupsignin1`.
1. Individuare le voci in `authorities` e sostituirle in base alle esigenze con i nomi dei flussi utente creati in un passaggio precedente, ad esempio `https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>`.
1. Aprire il file `apiConfig.js`.
1. Individuare l'assegnazione per `b2cScopes` e sostituire l'URL con l'URL dell'ambito creato per l'API Web, ad esempio `b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/helloapi/demo.read"]`.
1. Individuare l'assegnazione per `webApi` e sostituire l'URL corrente con l'URL in cui è stata distribuita l'API Web nel passaggio 4, ad esempio `webApi: http://localhost:5000/hello`.

* * *

Il codice risultante avrà un aspetto simile al seguente:

#### <a name="auth-code-flow-sample"></a>[Esempio per il flusso del codice di autorizzazione](#tab/review-auth/)

*authConfig.js*:

```javascript
const msalConfig = {
  auth: {
    clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902",
    authority: b2cPolicies.authorities.signUpSignIn.authority,
    knownAuthorities: [b2cPolicies.authorityDomain],
  },
  cache: {
    cacheLocation: "localStorage",
    storeAuthStateInCookie: true
  }
};

const loginRequest = {
  scopes: ["openid", "profile"],
};

const tokenRequest = {
  scopes: apiConfig.b2cScopes // i.e. ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"]
};
```

*policies.js*:

```javascript
const b2cPolicies = {
    names: {
        signUpSignIn: "b2c_1_susi",
        forgotPassword: "b2c_1_reset",
        editProfile: "b2c_1_edit_profile"
    },
    authorities: {
        signUpSignIn: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi",
        },
        forgotPassword: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_reset",
        },
        editProfile: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_edit_profile"
        }
    },
    authorityDomain: "fabrikamb2c.b2clogin.com"
}
```

*apiConfig.js*:

```javascript
const apiConfig = {
  b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"],
  webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
};
```

#### <a name="implicit-flow-sample"></a>[Esempio per il flusso implicito](#tab/review-implicit/)

*authConfig.js*:

```javascript
const msalConfig = {
  auth: {
    clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902",
    authority: b2cPolicies.authorities.signUpSignIn.authority,
    validateAuthority: false
  },
  cache: {
    cacheLocation: "localStorage",
    storeAuthStateInCookie: true
  }
};

const loginRequest = {
  scopes: ["openid", "profile"],
};

const tokenRequest = {
  scopes: apiConfig.b2cScopes // i.e. ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"]
};
```

*policies.js*:

```javascript
const b2cPolicies = {
    names: {
        signUpSignIn: "b2c_1_susi",
        forgotPassword: "b2c_1_reset",
        editProfile: "b2c_1_edit_profile"
    },
    authorities: {
        signUpSignIn: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi",
        },
        forgotPassword: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_reset",
        },
        editProfile: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_edit_profile"
        }
    },
}
```

*apiConfig.js*:

```javascript
const apiConfig = {
  b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"],
  webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
};
```

* * *


## <a name="run-the-sample"></a>Eseguire l'esempio

1. Aprire una finestra della console e passare alla directory contenente il codice di esempio. 

    - Per l'esempio del flusso del codice di autorizzazione MSAL.js 2.x:

        ```console
        cd ms-identity-b2c-javascript-spa
        ```
    - Per l'esempio del flusso implicito MSAL.js 1.x: 

        ```console
        cd active-directory-b2c-javascript-msal-singlepageapp
        ```

1. Eseguire i comandi seguenti:

    ```console
    npm install && npm update
    npm start
    ```

    La finestra della console visualizza il numero di porta del server Node.js in esecuzione in locale:

    ```console
    Listening on port 6420...
    ```
1. Passare a `http://localhost:6420` per visualizzare l'applicazione Web in esecuzione nel computer locale.

    :::image type="content" source="media/tutorial-single-page-app/web-app-spa-01-not-logged-in.png" alt-text="Web browser che mostra un'applicazione a pagina singola eseguita in locale":::

### <a name="sign-up-using-an-email-address"></a>Iscriversi usando un indirizzo di posta elettronica

Questa applicazione di esempio supporta l'iscrizione, l'accesso e la reimpostazione della password. In questa esercitazione si esegue l'iscrizione con un indirizzo di posta elettronica.

1. Selezionare **Accedi** per avviare il flusso utente *B2C_1_signupsignin1* specificato in un passaggio precedente.
1. Azure AD B2C presenta una pagina di accesso con un collegamento di iscrizione. Non avendo ancora un account, selezionare il collegamento **Iscriversi adesso**.
1. Il flusso di lavoro per l'iscrizione presenta una pagina per raccogliere e verificare l'identità dell'utente usando un indirizzo di posta elettronica. Il flusso di lavoro per l'iscrizione raccoglie anche la password dell'utente e gli attributi richiesti definiti nel flusso utente.

    Usare un indirizzo e-mail valido ed eseguire la convalida usando un codice di verifica. Impostare una password. Immettere i valori per gli attributi richiesti.

    :::image type="content" source="media/tutorial-single-page-app/user-flow-sign-up-workflow-01.png" alt-text="Pagina di iscrizione visualizzata dal flusso utente di Azure AD B2C":::

1. Selezionare **Crea** per creare un account locale nella directory di Azure AD B2C.

Quando si seleziona **Crea**, l'applicazione visualizza il nome dell'utente che ha eseguito l'accesso.

:::image type="content" source="media/tutorial-single-page-app/web-app-spa-02-logged-in.png" alt-text="Web browser che mostra un'applicazione a pagina singola con l'utente connesso":::

Se si vuole testare l'accesso, selezionare il pulsante **Disconnetti**, quindi selezionare **Accedi** e accedere con l'indirizzo di posta elettronica e la password immessi al momento dell'iscrizione.

### <a name="what-about-calling-the-api"></a>Chiamare l'API

Se si seleziona il pulsante **Chiamata di API** dopo l'accesso, viene visualizzata la pagina del flusso utente di iscrizione/accesso anziché i risultati della chiamata API. Si tratta di un comportamento previsto perché non è stata ancora configurata la parte API dell'applicazione per comunicare con un'applicazione API Web registrata nel *proprio* tenant di Azure AD B2C.

A questo punto, l'applicazione sta ancora tentando di comunicare con l'API registrata nel tenant demo (fabrikamb2c.onmicrosoft.com) e poiché non è stata eseguita l'autenticazione con il tenant, viene visualizzata la pagina di iscrizione/accesso.

Passare all'esercitazione successiva della serie per abilitare l'API protetta (vedere la sezione [Passaggi successivi](#next-steps)).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata configurata un'applicazione a pagina singola per l'uso di un flusso utente nel tenant di Azure AD B2C per fornire le funzionalità di iscrizione e accesso. Sono stati completati i passaggi seguenti:

> [!div class="checklist"]
> * È stato aggiunto un URL di risposta a un'applicazione registrata nel tenant di Azure AD B2C
> * È stato scaricato il codice di esempio da GitHub
> * È stato modificato il codice dell'applicazione di esempio in modo che funzioni con il proprio tenant
> * È stata eseguita l'iscrizione usando il flusso utente di iscrizione/accesso

Passare ora all'esercitazione successiva nella serie per concedere l'accesso a un'API Web protetta dall'applicazione a pagina singola:

> [!div class="nextstepaction"]
> [Esercitazione: Proteggere e concedere l'accesso a un'API Web da un'applicazione a pagina singola >](tutorial-single-page-app-webapi.md)