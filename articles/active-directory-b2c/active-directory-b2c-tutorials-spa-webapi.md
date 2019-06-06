---
title: "Esercitazione: Concedere l'accesso a un'API Web ASP.NET Core da un'applicazione a pagina singola usando Azure Active Directory B2C | Microsoft Docs"
description: Esercitazione su come usare Active Directory B2C per proteggere un'API Web NET Core e chiamarla da un'app a singola pagina.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 609e7bc4104a445c5d77109c9470fb487b2f1336
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66507739"
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-application-using-azure-active-directory-b2c"></a>Esercitazione: Concedere l'accesso a un'API Web ASP.NET Core da un'applicazione a pagina singola con Azure Active Directory B2C

Questa esercitazione illustra come chiamare una risorsa API Web ASP.NET Core protetta da Azure Active Directory (Azure AD) B2C da un'applicazione a pagina singola.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Aggiungere un'applicazione API Web
> * Configurare gli ambiti per un'API Web
> * Concedere le autorizzazioni per l'API Web
> * Configurare l'esempio per l'uso dell'applicazione

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Completare i passaggi e i prerequisiti riportati in [Esercitazione: Abilitare l'autenticazione per un'app a pagina singola con account usando Azure Active Directory B2C](active-directory-b2c-tutorials-spa.md).

## <a name="add-a-web-api-application"></a>Aggiungere un'applicazione API Web

Per poter accettare e rispondere a richieste di risorse protette da parte di applicazioni client che presentano un token di accesso, le risorse API Web devono prima essere registrate nel tenant.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul **filtro delle directory e delle sottoscrizioni** nel menu in alto e scegliere la directory che contiene il tenant.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
4. Selezionare **Applicazioni** e quindi **Aggiungi**.
5. Immettere un nome per l'applicazione. Ad esempio, *webapi1*.
6. Per **Includi app Web/API Web** e **Consenti il flusso implicito**, selezionare **Sì**.
7. Per **URL di risposta**, immettere un endpoint a cui Azure AD B2C deve restituire eventuali token richiesti dall'applicazione. In questa esercitazione, l'esempio viene eseguito in locale ed è in ascolto su `https://localhost:5000`.
8. Per **URI ID app** immettere l'identificatore usato per l'API Web. L'URI completo dell'identificatore, incluso il dominio, viene generato automaticamente. Ad esempio: `https://contosotenant.onmicrosoft.com/api`.
9. Fare clic su **Create**(Crea).
10. Nella pagina delle proprietà prendere nota dell'ID applicazione, che verrà usato durante la configurazione dell'applicazione Web.

## <a name="configure-scopes"></a>Configurare gli ambiti

Gli ambiti consentono di regolamentare l'accesso alle risorse protette. Vengono usati dall'API Web per implementare il controllo degli accessi in base all'ambito. Alcuni utenti possono avere ad esempio accesso sia in lettura che in scrittura, mentre altri possono avere autorizzazioni di sola lettura. In questa esercitazione si definiscono autorizzazioni di lettura per l'API Web.

1. Selezionare **Applicazioni** e quindi *webapi1*.
2. Selezionare **Ambiti pubblicati**.
3. Immettere `Hello.Read` come **ambito** e `Read access to hello` come descrizione.
4. Immettere `Hello.Write` come **ambito** e `Write access to hello` come descrizione.
5. Fare clic su **Save**.

Gli ambiti pubblicati possono essere usati per concedere a un'app client autorizzazioni per l'API Web.

## <a name="grant-permissions"></a>Concedere le autorizzazioni

Per chiamare un'API Web protetta da un'applicazione, è necessario concedere all'applicazione le autorizzazioni per l'API. Nell'esercitazione preliminare è stata creata un'applicazione Web denominata *webapp1* in Azure AD B2C. Questa applicazione client verrà usata per chiamare l'API Web.

1. Selezionare **Applicazioni** e quindi *webapp1*.
2. Selezionare **Accesso all'API** e quindi **Aggiungi**.
3. Nell'elenco a discesa **Seleziona API** selezionare *webapi1*.
4. Nell'elenco a discesa **Seleziona ambiti** selezionare gli ambiti **Hello.Read** e **Hello.Write** definiti in precedenza.
5. Fare clic su **OK**.

L'**App a pagina singola di esempio** è registrata per chiamare l'API protetta **Hello Core API**. Un utente esegue l'autenticazione con Azure AD B2C per usare l'app a pagina singola. L'app a pagina singola ottiene una concessione di autorizzazione da Azure AD B2C per l'accesso all'API Web protetta.

## <a name="configure-the-sample"></a>Configurare l'esempio

Dopo la registrazione dell'API Web e la definizione degli ambiti, configurare il codice dell'API Web per l'uso del tenant di Azure AD B2C. In questa esercitazione si configura un'applicazione Web .NET Core di esempio che è possibile scaricare da GitHub. [Scaricare un file ZIP](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip) o clonare l'app Web di esempio da GitHub.

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
        "ClientId": "<application-ID>",
        "Policy": "B2C_1_signupsignin1>",
        "ScopeRead": "Hello.Read"  
      },
    ```

#### <a name="enable-cors"></a>Abilitare CORS

Per consentire all'applicazione a pagina singola di chiamare l'API Web ASP.NET Core, è necessario abilitare [CORS](https://docs.microsoft.com/aspnet/core/security/cors).

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

3. Aprire il file **launchSettings.json** in **Proprietà**, individuare l'impostazione **iisSettings** *applicationURL* e quindi impostare il numero di porta sul numero registrato per l'URL di risposta API `http://localhost:5000`.

### <a name="configure-the-single-page-application"></a>Configurare l'applicazione a pagina singola

L'applicazione a pagina singola usa Azure Active Directory B2C per l'iscrizione e l'accesso degli utenti e chiama l'API Web protetta ASP.NET Core. Aggiornare l'applicazione a pagina singola per chiamare l'API Web .NET Core.

Per modificare le impostazioni dell'app:

1. Aprire il file `index.html` .
2. Configurare l'esempio con le informazioni di registrazione tenant di Azure Active Directory B2C. Nel codice seguente aggiungere il nome del tenant a **b2cScopes** e modificare il valore di **webApi** usando il valore di *applicationURL* registrato in precedenza:

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var applicationConfig = {
        clientID: '<application-ID>',
        authority: "https://<your-tenant-name>.b2clogin.com/tfp/<your-tenant-name>.onmicrosoft.com/B2C_1_signupsignin1",
        b2cScopes: ["https://<Your tenant name>.onmicrosoft.com/api/Hello.Read"],
        webApi: 'http://localhost:5000/api/values',
    };
    ```

## <a name="run-the-spa-application-and-web-api"></a>Eseguire l'applicazione a pagina singola e l'API Web

È necessario eseguire sia l'applicazione a pagina singola Node.js, sia l'API Web .NET Core.

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

    Nella finestra della console viene visualizzato il numero della porta in cui è ospitata l'applicazione.
    
    ```
    Listening on port 6420...
    ```

4. Usare un browser per passare all'indirizzo `http://localhost:6420` e visualizzare l'applicazione.
5. Accedere usando l'indirizzo e-mail e la password usati nell'esercitazione sull'[autenticazione degli utenti con Azure Active Directory B2C in un'applicazione a singola pagina (JavaScript)](active-directory-b2c-tutorials-spa.md).
6. Fare clic su **Call API** (Chiama API).

Dopo l'iscrizione o l'accesso con un account utente, l'esempio chiama l'API Web protetta e restituisce un risultato.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Aggiungere un'applicazione API Web
> * Configurare gli ambiti per un'API Web
> * Concedere le autorizzazioni per l'API Web
> * Configurare l'esempio per l'uso dell'applicazione

> [!div class="nextstepaction"]
> [Esercitazione: Aggiungere provider di identità alle applicazioni in Azure Active Directory B2C](tutorial-add-identity-providers.md)
