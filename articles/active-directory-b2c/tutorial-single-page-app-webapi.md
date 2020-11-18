---
title: "Esercitazione: Proteggere un'API Web Node.js usando Azure AD B2C e concedere l'accesso a un'applicazione a pagina singola"
titleSuffix: Azure AD B2C
description: Questa esercitazione illustra come usare Active Directory B2C per proteggere un'API Web Node.js e chiamarla da un'applicazione a pagina singola.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/04/2020
ms.custom: mvc, devx-track-js
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 737810a7d07d0d97b2e42acffa17fdd32986c48b
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421091"
---
# <a name="tutorial-protect-and-grant-access-to-a-nodejs-web-api-from-a-single-page-application-with-azure-ad-b2c"></a>Esercitazione: Proteggere e concedere l'accesso a un'API Web Node.js da un'applicazione a pagina singola con Azure AD B2C

Questa esercitazione illustra come chiamare un'API Web Node.js protetta da Azure Active Directory B2C (Azure AD B2C) da un'applicazione a pagina singola.

In questa esercitazione, che è la seconda di una serie in due parti, verrà descritto come:

> [!div class="checklist"]
> * Creare una registrazione dell'applicazione API Web nel tenant di Azure AD B2C
> * Configurare gli ambiti per l'API Web
> * Concedere le autorizzazioni per l'API Web
> * Modificare un esempio di codice dell'API Web per usare il proprio tenant

Nella [prima esercitazione](tutorial-single-page-app.md) di questa serie è stato scaricato l'esempio di codice e lo si è modificato per consentire agli utenti di accedere con un flusso utente nel tenant di Azure AD B2C.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

* Completare i passaggi e i prerequisiti riportati in [Esercitazione: Abilitare l'autenticazione in un'applicazione a pagina singola con Azure AD B2C](tutorial-single-page-app.md)
* [Visual Studio Code](https://code.visualstudio.com/) o un altro editor di codice
* [Node.js](https://nodejs.org/en/download/)

## <a name="add-a-web-api-application"></a>Aggiungere un'applicazione API Web

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Configurare gli ambiti

Gli ambiti consentono di regolamentare l'accesso alle risorse protette. Vengono usati dall'API Web per implementare il controllo degli accessi in base all'ambito. Alcuni utenti possono avere ad esempio accesso sia in lettura che in scrittura, mentre altri possono avere autorizzazioni di sola lettura. In questa esercitazione si definiscono autorizzazioni di lettura e scrittura per l'API Web.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Prendere nota del valore visualizzato **Ambiti** per l'ambito `demo.read` da usare in un passaggio successivo durante la configurazione dell'applicazione a pagina singola. Il valore dell'ambito completo è simile a `https://contosob2c.onmicrosoft.com/api/demo.read`.

## <a name="grant-permissions"></a>Concedere le autorizzazioni

Per chiamare un'API Web protetta da un'altra applicazione, è necessario concedere a tale applicazione le autorizzazioni per l'API Web.

Nell'esercitazione preliminare è stata creata un'applicazione a pagina singola denominata *spaapp1*. In questa esercitazione si configurerà l'applicazione in modo che chiami l'API Web creata in una sezione precedente, *spaapp1*.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

All'applicazione Web a pagina singola sono ora state concesse le autorizzazioni per l'API Web protetta per gli ambiti specificati. Un utente esegue l'autenticazione con Azure AD B2C per usare l'applicazione a pagina singola. L'app a pagina singola ottiene un token di accesso da Azure AD B2C per l'accesso all'API Web protetta.

## <a name="configure-the-sample"></a>Configurare l'esempio

Dopo la registrazione dell'API Web e la definizione degli ambiti, configurare il codice dell'API Web per l'uso del tenant di Azure AD B2C. In questa esercitazione si configura un'API Web Node.js di esempio che è possibile scaricare da GitHub.

[Scaricare un file \*.zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) o clonare il progetto di API Web di esempio di GitHub. È anche possibile passare direttamente al progetto [Azure-Samples/active-directory-b2c-javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) in GitHub.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="configure-the-web-api"></a>Configurare l'API Web

1. Aprire il file *config.json* nell'editor di codice.
1. Modificare i valori delle variabili in modo che corrispondano a quelli della registrazione dell'applicazione creata in precedenza. Aggiornare anche `policyName` con il flusso utente creato come parte dei prerequisiti. Ad esempio, *B2C_1_signupsignin1*.
    
    ```json
    "credentials": {
        "tenantName": "<your-tenant-name>",
        "clientID": "<your-webapi-application-ID>"
    },
    "policies": {
        "policyName": "B2C_1_signupsignin1"
    },
    "resource": {
        "scope": ["demo.read"] 
    },
    ```

#### <a name="enable-cors"></a>Abilitare CORS

Per consentire all'applicazione a pagina singola di chiamare l'API Web Node.js, è necessario abilitare [CORS](https://expressjs.com/en/resources/middleware/cors.html) nell'API Web. In un'applicazione di produzione occorre prestare attenzione al dominio che sta effettuando la richiesta, ma per questa esercitazione consentire le richieste provenienti da qualsiasi dominio.

Per abilitare CORS, usare il middleware seguente. Nell'esempio di codice dell'API Web Node.js in questa esercitazione è già stato aggiunto al file *index.js*.

```javascript
app.use((req, res, next) => {
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "Authorization, Origin, X-Requested-With, Content-Type, Accept");
    next();
});
```

### <a name="configure-the-single-page-application"></a>Configurare l'applicazione a pagina singola

L'applicazione a pagina singola dell'[esercitazione precedente](tutorial-single-page-app.md) della serie usa Azure AD B2C per l'iscrizione e l'accesso degli utenti e, per impostazione predefinita, chiama l'API Web Node.js protetta dal tenant demo *fabrikamb2c*.

In questa sezione l'applicazione Web a pagina singola viene aggiornata per chiamare l'API Web Node.js protetta dal *proprio* tenant di Azure AD B2C in esecuzione nel computer locale.

Per cambiare le impostazioni nell'applicazione a pagina singola:

1. Aprire il file [apiConfig.js][github-js-spa] all'interno della cartella *JavaScriptSPA* nel progetto *active-directory-b2c-javascript-msal-singlepageapp* scaricato o clonato nell'esercitazione precedente.
1. Configurare l'esempio con l'URI dell'ambito *demo.read* creato in precedenza e l'URL dell'API Web.
    1. Nella definizione `apiConfig` sostituire il valore `b2cScopes` con l'URI completo per l'ambito *demo.read* (il valore di **Ambito** annotato in precedenza).
    1. Modificare il dominio nel valore `webApi` impostandolo sull'URI di reindirizzamento aggiunto durante la registrazione dell'applicazione API Web in un passaggio precedente.

    Poiché l'API è accessibile dall'endpoint `/hello`, lasciare */hello* nell'URI.

    La definizione `apiConfig` dovrebbe essere simile al blocco di codice seguente (con il nome del proprio tenant B2C al posto di `<your-tenant-name>`):

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
      b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/api/demo.read"],
      webApi: "http://localhost:5000/hello" // '/hello' should remain in the URI
    };
    ```

## <a name="run-the-spa-and-web-api"></a>Eseguire l'applicazione a pagina singola e l'API Web

A questo punto si è pronti per testare l'accesso con ambito dell'applicazione a pagina singola all'API. Eseguire sia l'API Web Node.js che l'applicazione a pagina singola JavaScript nel computer locale. Accedere quindi all'applicazione a pagina singola e fare clic sul pulsante per **Chiamata di API** per avviare una richiesta all'API protetta.

Anche se entrambe le applicazioni vengono eseguite in locale in questa esercitazione, sono state configurate per usare Azure AD B2C per l'iscrizione e l'accesso sicuri e per concedere l'accesso all'API Web protetta.

### <a name="run-the-nodejs-web-api"></a>Eseguire l'API Web Node.js:

1. Aprire una finestra della console e passare alla directory contenente l'esempio di API Web Node.js. Ad esempio:

    ```console
    cd active-directory-b2c-javascript-nodejs-webapi
    ```

1. Eseguire i comandi seguenti:

    ```console
    npm install && npm update
    node index.js
    ```

    Nella finestra della console viene visualizzato il numero della porta in cui è ospitata l'applicazione.

    ```console
    Listening on port 5000...
    ```

### <a name="run-the-single-page-app"></a>Eseguire l'app a pagina singola

1. Aprire un'altra finestra della console e passare alla directory contenente l'esempio di applicazione a pagina singola JavaScript. Ad esempio:

    ```console
    cd ms-identity-b2c-javascript-spa
    ```

1. Eseguire i comandi seguenti:

    ```console
    npm install && npm update
    npm start
    ```

    Nella finestra della console viene visualizzato il numero della porta in cui è ospitata l'applicazione.

    ```console
    Listening on port 6420...
    ```

1. Passare a `http://localhost:6420` nel browser per visualizzare l'applicazione.

    ![App di esempio dell'applicazione a pagina singola visualizzata nel browser](./media/tutorial-single-page-app-webapi/tutorial-01-sample-app-browser.png)

1. Accedere con l'indirizzo di posta elettronica e la password usati nell'[esercitazione precedente](tutorial-single-page-app.md). Al completamento dell'accesso, verrà visualizzato il messaggio `User 'Your Username' logged-in`.
1. Selezionare il pulsante **Call API** (Chiama API). L'applicazione a pagina singola ottiene una concessione di autorizzazione da Azure AD B2C, quindi accede all'API Web protetta per visualizzare il nome dell'utente che ha eseguito l'accesso:

    ![Applicazione a pagina singola nel browser che mostra il risultato JSON del nome utente restituito dall'API](./media/tutorial-single-page-app-webapi/tutorial-02-call-api.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione:

> [!div class="checklist"]
> * È stata creata una registrazione dell'applicazione API Web nel tenant di Azure AD B2C
> * Sono stati configurati gli ambiti per l'API Web
> * Sono state concesse le autorizzazioni per l'API Web
> * È stato modificato un esempio di codice dell'API Web per usare il proprio tenant

Dopo aver visualizzato una richiesta di risorsa effettuata dall'applicazione a pagina singola all'API Web protetta, è possibile ottenere una conoscenza più approfondita dell'interazione tra questi tipi di applicazione e Azure AD B2C.

> [!div class="nextstepaction"]
> [Tipi di applicazioni che possono essere usate in Active Directory B2C >](application-types.md)

<!-- Links - EXTERNAL -->
[github-js-spa]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
