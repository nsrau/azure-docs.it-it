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
ms.openlocfilehash: 3a3eb77315953c3791e09c4326af7cc3e3231a69
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92670043"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-with-azure-ad-b2c"></a>Esercitazione: Abilitare l'autenticazione in un'applicazione a pagina singola con Azure AD B2C

Questa esercitazione illustra come usare Azure Active Directory B2C (Azure AD B2C) per l'iscrizione e l'accesso degli utenti in un'applicazione a pagina singola mediante il flusso di concessione implicita OAuth 2.0.

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
* [Applicazione registrata](tutorial-register-spa.md) nel tenant (usare le opzioni del flusso implicito)
* [Flussi utente creati](tutorial-create-user-flows.md) nel tenant

In più, nell'ambiente di sviluppo locale devono anche essere installati gli elementi seguenti:

* [Visual Studio Code](https://code.visualstudio.com/) o un altro editor di codice
* [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>Aggiornare l'applicazione

Nella seconda esercitazione completata come parte dei prerequisiti è stata registrata un'applicazione Web in Azure AD B2C. Per consentire la comunicazione con l'esempio in questa esercitazione, aggiungere un URL di risposta (anche noto come URI di reindirizzamento) alla registrazione dell'applicazione.

Per aggiornare un'applicazione nel tenant di Azure AD B2C, è possibile usare la nuova esperienza unificata **Registrazioni app** oppure l'esperienza legacy **Applicazioni (legacy)** . [Altre informazioni sulla nuova esperienza](https://aka.ms/b2cappregtraining).

#### <a name="app-registrations"></a>[Registrazioni per l'app](#tab/app-reg-ga/)

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel menu a sinistra selezionare **Azure AD B2C**. In alternativa, selezionare **Tutti i servizi** e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **Registrazioni app** , la scheda **Applicazioni di cui si è proprietari** e quindi l'applicazione *webapp1*.
1. In **Web** selezionare il collegamento **Aggiungi un URI** , immettere `http://localhost:6420`.
1. In **Concessione implicita** selezionare le caselle di controllo **Token di accesso** e **Token ID** , se non sono già selezionate, quindi selezionare **Salva**.
1. Selezionare **Panoramica**.
1. Prendere nota del valore di **ID applicazione (client)** che verrà usato in un passaggio successivo quando si aggiorna il codice nell'applicazione Web a pagina singola.

#### <a name="applications-legacy"></a>[Applicazioni (legacy)](#tab/applications-legacy/)

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant.
1. Selezionare **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **Applicazioni (legacy)** e quindi l'applicazione *webapp1*.
1. In **URL di risposta** aggiungere `http://localhost:6420`.
1. Selezionare **Salva**.
1. Nella pagina delle proprietà, registrare l' **ID applicazione**. Si userà l'ID dell'app in un passaggio successivo quando si aggiorna il codice nell'applicazione Web a pagina singola.

* * *

## <a name="get-the-sample-code"></a>Scaricare il codice di esempio

In questa esercitazione verrà configurato un esempio di codice che si scarica da GitHub per usare il proprio tenant B2C. L'esempio dimostra come un'applicazione a pagina singola può usare Azure AD B2C per l'iscrizione e l'accesso degli utenti e per chiamare un'API Web protetta (l'API Web verrà abilitata nell'esercitazione successiva della serie).

[Scaricare un file ZIP](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) o clonare l'esempio da GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

## <a name="update-the-sample"></a>Aggiornare l'esempio

Ora che è stato ottenuto l'esempio, aggiornare il codice con il nome del tenant di Azure AD B2C e l'ID dell'applicazione registrato in un passaggio precedente.

1. Aprire il file *authConfig.js* nella cartella *JavaScriptSPA*.
1. Nell'oggetto `msalConfig` aggiornare:
    * `clientId` con il valore del campo **ID dell'applicazione (client)** annotato in un passaggio precedente
    * L'URI `authority` con il nome del tenant di Azure AD B2C e il nome del flusso utente di accesso/iscrizione creato come parte dei prerequisiti (ad esempio, *B2C_1_signupsignin1* )

    ```javascript
    const msalConfig = {
        auth: {
          clientId: "00000000-0000-0000-0000-000000000000", // Replace this value with your Application (client) ID
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

1. Aprire il file *authConfig.js* nella cartella *JavaScriptSPA*.
1. Nell'oggetto `msalConfig` aggiornare:
    * `clientId` con il valore del campo **ID dell'applicazione (client)** annotato in un passaggio precedente
    * L'URI `authority` con il nome del tenant di Azure AD B2C e il nome del flusso utente di accesso/iscrizione creato come parte dei prerequisiti (ad esempio, *B2C_1_signupsignin1* )
1. Aprire il file *Policies.js*.
1. Individuare le voci `names` e `authorities` e sostituirle come appropriato con i nomi dei criteri creati nel passaggio 2. Sostituire `fabrikamb2c.onmicrosoft.com` con il nome del tenant di Azure AD B2C, ad esempio `https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>`.
1. Aprire il file *apiConfig.js*.
1. Individuare l'assegnazione per gli ambiti `b2cScopes` e sostituire l'URL con l'URL dell'ambito creato per l'API Web, ad esempio `b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/helloapi/demo.read"]`.
1. Individuare l'assegnazione per l'URL dell'API `webApi` e sostituire l'URL corrente con l'URL in cui è stata distribuita l'API Web nel passaggio 4, ad esempio `webApi: http://localhost:5000/hello`.

Il codice risultante dovrà essere simile al seguente:

### <a name="authconfigjs"></a>authConfig.js

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
### <a name="policiesjs"></a>policies.js

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
### <a name="apiconfigjs"></a>apiConfig.js

```javascript
const apiConfig = {
  b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"],
  webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
};
```

## <a name="run-the-sample"></a>Eseguire l'esempio

1. Aprire una finestra della console e passare alla directory contenente il codice di esempio. Ad esempio:

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

Quando si seleziona **Crea** , l'applicazione visualizza il nome dell'utente che ha eseguito l'accesso.

:::image type="content" source="media/tutorial-single-page-app/web-app-spa-02-logged-in.png" alt-text="Web browser che mostra un'applicazione a pagina singola con l'utente connesso":::

Se si vuole testare l'accesso, selezionare il pulsante **Disconnetti** , quindi selezionare **Accedi** e accedere con l'indirizzo di posta elettronica e la password immessi al momento dell'iscrizione.

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
