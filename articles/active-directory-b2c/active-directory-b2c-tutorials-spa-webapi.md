---
title: "Esercitazione: concedere l'accesso a un'API Web ASP.NET Core da un'app Web a singola pagina usando Azure Active Directory B2C | Microsoft Docs"
description: Esercitazione su come usare Active Directory B2C per proteggere un'API Web NET Core e chiamarla da un'app a singola pagina.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.author: davidmu
ms.date: 3/02/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.component: B2C
ms.openlocfilehash: 54ddafbf0e4fe02bfc1445aad23ac3e20b42acb0
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2018
ms.locfileid: "43339387"
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-app-using-azure-active-directory-b2c"></a>Esercitazione: concedere l'accesso a un'API Web ASP.NET Core da un'app Web a singola pagina usando Azure Active Directory B2C

Questa esercitazione illustra come chiamare da un'app a singola pagina una risorsa API Web ASP.NET Core protetta da Azure Active Directory (Azure AD) B2C.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Registrare un'API Web nel tenant di Azure AD B2C
> * Definire e configurare gli ambiti per un'API Web
> * Concedere le autorizzazioni per l'app all'API Web
> * Aggiornare il codice di esempio per l'uso di Azure AD B2C per proteggere un'API Web

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

* Completare l'esercitazione sull'[autenticazione degli utenti con Azure Active Directory B2C in un'applicazione a singola pagina](active-directory-b2c-tutorials-spa.md).
* Installare [Visual Studio 2017](https://www.visualstudio.com/downloads/) con il carico di lavoro **Sviluppo ASP.NET e Web**.
* [.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) o versione successiva
* Installare [Node.js](https://nodejs.org/en/download/)

## <a name="register-web-api"></a>Registrare l'API Web

Le risorse API Web devono essere registrate nel tenant prima che possano accettare e rispondere a [richieste di risorse protette](../active-directory/develop/developer-glossary.md#resource-server) da parte di [applicazioni client](../active-directory/develop/developer-glossary.md#client-application) che presentano un [token di accesso](../active-directory/develop/developer-glossary.md#access-token) di Azure Active Directory. La registrazione stabilisce l'[oggetto applicazione ed entità servizio](../active-directory/develop/developer-glossary.md#application-object) nel tenant. 

Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di Azure AD B2C.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Selezionare **Azure AD B2C** dall'elenco di servizi nel portale di Azure.

2. Nelle impostazioni di B2C fare clic su **Applicazioni** e quindi su **Aggiungi**.

    Per registrare l'API Web di esempio nel tenant, usare le impostazioni seguenti.
    
    ![Aggiungere una nuova API](media/active-directory-b2c-tutorials-spa-webapi/web-api-registration.png)
    
    | Impostazione      | Valore consigliato  | Descrizione                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nome** | Hello Core API | Immettere un **nome** che descriva l'API Web agli sviluppatori. |
    | **Includi app Web/API Web** | Yes | Selezionare **Sì** per un'API Web. |
    | **Consenti il flusso implicito** | Yes | Selezionare **Sì** perché l'API usa l'[accesso OpenID Connect](active-directory-b2c-reference-oidc.md). |
    | **URL di risposta** | `http://localhost:44332` | Gli URL di risposta sono gli endpoint a cui Azure AD B2C restituisce eventuali token richiesti dall'API. In questa esercitazione l'API Web di esempio viene eseguita in locale (localhost) ed è in ascolto sulla porta 5000. |
    | **URI ID app** | HelloCoreAPI | L'URI identifica l'API in modo univoco nel tenant. In questo modo è possibile registrare più API per ogni tenant. Gli [ambiti](../active-directory/develop/developer-glossary.md#scopes) regolano l'accesso alla risorsa API protetta e vengono definiti per ogni URI ID app. |
    | **Client nativo** | No  | Trattandosi di un'API Web e non un client nativo, selezionare No. |
    
3. Fare clic su **Crea** per registrare l'API.

Le API registrate vengono visualizzate nell'elenco di applicazioni per il tenant Azure AD B2C. Selezionare l'API Web dall'elenco. Viene visualizzato il riquadro delle proprietà dell'API Web.

![Proprietà dell'API Web](./media/active-directory-b2c-tutorials-spa-webapi/b2c-web-api-properties.png)

Annotare l'**ID client applicazione**. L'ID identifica l'API in modo univoco ed è necessario per configurare l'API più avanti nell'esercitazione.

La registrazione dell'API Web con Azure AD B2C definisce una relazione di trust. L'API è registrata con B2C, quindi può ora considerare attendibili i token di accesso B2C ricevuti da altre applicazioni.

## <a name="define-and-configure-scopes"></a>Definire e configurare gli ambiti

Gli [ambiti](../active-directory/develop/developer-glossary.md#scopes) consentono di regolare l'accesso alle risorse protette. Vengono usati dall'API Web per implementare il controllo degli accessi in base all'ambito. Alcuni utenti possono avere ad esempio accesso sia in lettura che in scrittura, mentre altri possono avere autorizzazioni di sola lettura. In questa esercitazione si definiscono autorizzazioni di lettura per l'API Web.

### <a name="define-scopes-for-the-web-api"></a>Definire gli ambiti per l'API Web

Le API registrate vengono visualizzate nell'elenco di applicazioni per il tenant Azure AD B2C. Selezionare l'API Web dall'elenco. Viene visualizzato il riquadro delle proprietà dell'API Web.

Fare clic su **Ambiti pubblicati (anteprima)**.

Per configurare gli ambiti per l'API, aggiungere le voci seguenti. 

![Ambiti definiti nell'API Web](media/active-directory-b2c-tutorials-spa-webapi/scopes-web-api.png)

| Impostazione      | Valore consigliato  | DESCRIZIONE                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Ambito** | demo.read | Accesso in lettura a all'API demo |

Fare clic su **Save**.

Gli ambiti pubblicati possono essere usati per concedere a un'app client autorizzazioni per l'API Web.

### <a name="grant-app-permissions-to-web-api"></a>Concedere autorizzazioni all'app per l'API Web

Per chiamare un'API Web protetta da un'app, è necessario concedere all'app le autorizzazioni per l'API. In questa esercitazione usare l'app a singola pagina creata nell'esercitazione sull'[autenticazione degli utenti con Azure Active Directory B2C in un'applicazione a singola pagina (JavaScript)](active-directory-b2c-tutorials-spa.md).

1. Nel portale selezionare **Azure AD B2C** dall'elenco dei servizi e fare clic su **Applicazioni** per visualizzare l'elenco delle app registrate.

2. Selezionare **App a pagina singola di esempio** dall'elenco di app, fare clic su **Accesso all'API (anteprima)** e quindi su **Aggiungi**.

3. Nell'elenco a discesa **Seleziona API** selezionare l'API Web registrata, ovvero **Hello Core API**.

4. Nell'elenco a discesa **Seleziona ambiti** selezionare gli ambiti definiti nella registrazione dell'API Web.

    ![Selezione degli ambiti per l'app](media/active-directory-b2c-tutorials-spa-webapi/selecting-scopes-for-app.png)

5. Fare clic su **OK**.

L'**App a pagina singola di esempio** è registrata per chiamare l'API protetta **Hello Core API**. Gli utenti [eseguono l'autenticazione](../active-directory/develop/developer-glossary.md#authentication) con Azure AD B2C per usare l'app desktop WPF. L'app desktop ottiene una [concessione di autorizzazione](../active-directory/develop/developer-glossary.md#authorization-grant) da Azure AD B2C per l'accesso all'API Web protetta.

## <a name="update-code"></a>Aggiornare il codice

Ora che l'API Web è registrata e sono stati definiti gli ambiti, è necessario configurare il codice dell'API Web per l'uso del tenant di Azure AD B2C. In questa esercitazione si configura un'app Web .NET Core di esempio che è possibile scaricare da GitHub. 

[Scaricare un file ZIP](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip) o clonare l'app Web di esempio da GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi.git
```

### <a name="configure-the-web-api"></a>Configurare l'API Web

1. Aprire la soluzione **B2C-WebAPI.sln** in Visual Studio.

2. Aprire il file **appsettings.json**. Aggiornare i valori seguenti per configurare l'API Web per l'uso del tenant:

    ```javascript
    "AzureAdB2C": 
      {
        "Tenant": "<your tenant name>.onmicrosoft.com", 
        "ClientId": "<The Application ID for your web API obtained from the Azure portal>",
        "Policy": "<Your sign up sign in policy e.g. B2C_1_SiUpIn>",
        "ScopeRead": "demo.read"  
      },
    ```

#### <a name="enable-cors"></a>Abilitare CORS

Per consentire all'app a pagina singola di chiamare l'API Web ASP.NET Core, è necessario abilitare [CORS](https://docs.microsoft.com/aspnet/core/security/cors).

1. In **Startup.cs** aggiungere CORS al metodo `ConfigureServices()`.

    ```C#
    public void ConfigureServices(IServiceCollection services) {
      services.AddCors();
    ```

2. In **Startup.cs** configurare CORS nel metodo `Configure()`.

    ```C#
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory) {
      app.UseCors(builder =>
        builder.WithOrigins("http://localhost:6420").AllowAnyHeader().AllowAnyMethod());
    ```

3. Aprire il file **launchSettings.json** in **Proprietà**, individuare l'impostazione *applicationURL* e registrare il valore per usarlo nella sezione successiva.

### <a name="configure-the-single-page-app"></a>Configurare l'app a pagina singola

L'app a pagina singola usa Azure Active Directory B2C per l'iscrizione e l'accesso degli utenti e chiama l'API Web protetta ASP.NET Core. È necessario aggiornare l'app a pagina singola per la chiamata dell'API Web .NET Core.
Per modificare le impostazioni dell'app:

1. Aprire il file `index.html` nell'esempio di app a pagina singola Node.js.
2. Configurare l'esempio con le informazioni di registrazione tenant di Azure Active Directory B2C. Nel codice seguente aggiungere il nome del tenant a **b2cScopes** e modificare il valore di **webApi** usando il valore di *applicationURL* registrato in precedenza:

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var applicationConfig = {
        clientID: '<Application ID for your SPA obtained from portal app registration>',
        authority: "https://<your-tenant-name>.b2clogin.com/tfp/<your-tenant-name>.onmicrosoft.com/B2C_1_SiUpIn",
        b2cScopes: ["https://<Your tenant name>.onmicrosoft.com/HelloCoreAPI/demo.read"],
        webApi: 'http://localhost:64791/api/values',
    };
    ```

## <a name="run-the-spa-app-and-web-api"></a>Eseguire l'app a pagina singola e l'API Web

È necessario eseguire sia l'app a pagina singola Node.js, sia l'API Web .NET Core.

### <a name="run-the-aspnet-core-web-api"></a>Eseguire l'API Web ASP.NET Core 

Premere **F5** per eseguire il debug della soluzione **B2C-WebAPI.sln** in Visual Studio.

Quando si avvia il progetto, nel browser predefinito viene visualizzata una pagina Web che annuncia che l'API Web è disponibile per le richieste.

### <a name="run-the-single-page-app"></a>Eseguire l'app a pagina singola

1. Avviare un prompt dei comandi di Node.js.
2. Passare alla directory contenente l'esempio di Node.js. Ad esempio `cd c:\active-directory-b2c-javascript-msal-singlepageapp`
3. Eseguire i comandi seguenti:
    ```
    npm install && npm update
    node server.js
    ```

    Nella finestra della console viene visualizzato il numero della porta dove è ospitata l'app.
    
    ```
    Listening on port 6420...
    ```

4. Usare un browser per passare all'indirizzo `http://localhost:6420` per visualizzare l'app.
5. Accedere usando l'indirizzo e-mail e la password usati nell'esercitazione sull'[autenticazione degli utenti con Azure Active Directory B2C in un'applicazione a singola pagina (JavaScript)](active-directory-b2c-tutorials-spa.md).
6. Fare clic sul pulsante **Call API** (Chiama API).

Dopo l'iscrizione o l'accesso con un account utente, l'esempio chiama l'API Web protetta e restituisce un risultato.

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile usare il tenant di Azure AD B2C se si prevede di provare altre esercitazioni relative ad Azure AD B2C. Quando non è più necessario, è possibile [eliminare il tenant di Azure AD B2C](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stata descritta la protezione di un'API Web tramite la registrazione e la definizione degli ambiti in Azure AD B2C. Per altre informazioni, esplorare gli esempi di codice disponibili per Azure Active Directory B2C.

> [!div class="nextstepaction"]
> [Esempi di codice per Azure AD B2C](https://azure.microsoft.com/resources/samples/?service=active-directory-b2c&sort=0)
