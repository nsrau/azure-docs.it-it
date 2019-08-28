---
title: "Esercitazione: Concedere l'accesso a un'API Web ASP.NET Core da un'applicazione a pagina singola - Azure Active Directory B2C"
description: Informazioni su come usare Active Directory B2C per proteggere un'API Web NET Core e chiamarla da un'applicazione Node.js a pagina singola.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 07/24/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: b53ce30f4c49580bcd8ad3e259adf0300d8bd4a6
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68369316"
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-application-using-azure-active-directory-b2c"></a>Esercitazione: Concedere l'accesso a un'API Web ASP.NET Core da un'applicazione a pagina singola con Azure Active Directory B2C

Questa esercitazione illustra come chiamare una risorsa API Web ASP.NET Core protetta da Azure Active Directory (Azure AD) B2C da un'applicazione a pagina singola.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Aggiungere un'applicazione API Web
> * Configurare gli ambiti per un'API Web
> * Concedere le autorizzazioni per l'API Web
> * Configurare l'esempio per l'uso dell'applicazione

## <a name="prerequisites"></a>Prerequisiti

* Completare i passaggi e i prerequisiti riportati in [Esercitazione: Abilitare l'autenticazione in un'applicazione a pagina singola con Azure Active Directory B2C](active-directory-b2c-tutorials-spa.md).
* Visual Studio 2019 o versione successiva oppure Visual Studio Code
* .NET Core 2.2 o versione successiva
* Node.js

## <a name="add-a-web-api-application"></a>Aggiungere un'applicazione API Web

Per poter accettare e rispondere a richieste di risorse protette da parte di applicazioni client che presentano un token di accesso, le risorse API Web devono prima essere registrate nel tenant.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul **filtro delle directory e delle sottoscrizioni** nel menu in alto e scegliere la directory che contiene il tenant.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **Applicazioni** e quindi **Aggiungi**.
1. Immettere un nome per l'applicazione. Ad esempio, *webapi1*.
1. Per **Includi app Web/API Web** e **Consenti il flusso implicito**, selezionare **Sì**.
1. Per **URL di risposta**, immettere un endpoint a cui Azure AD B2C deve restituire eventuali token richiesti dall'applicazione. In questa esercitazione, l'esempio viene eseguito in locale ed è in ascolto su `https://localhost:5000`.
1. Per **URI ID app** immettere un identificatore dell'endpoint API per l'URI visualizzato. Per l'esercitazione, immettere `api`, in modo che l'URI completo sia simile a `https://contosotenant.onmicrosoft.com/api`.
1. Fare clic su **Create**(Crea).
1. Selezionare l'applicazione *webapi1* per aprire la relativa pagina delle proprietà.
1. Prendere nota dell'**ID applicazione** visualizzato nella pagina delle proprietà. Questo ID sarà necessario in un passaggio successivo durante la configurazione dell'applicazione Web.

## <a name="configure-scopes"></a>Configurare gli ambiti

Gli ambiti consentono di regolamentare l'accesso alle risorse protette. Vengono usati dall'API Web per implementare il controllo degli accessi in base all'ambito. Alcuni utenti possono avere ad esempio accesso sia in lettura che in scrittura, mentre altri possono avere autorizzazioni di sola lettura. In questa esercitazione si definiscono autorizzazioni di lettura e scrittura per l'API Web.

1. Selezionare **Applicazioni**, quindi selezionare *webapi1* per aprire la relativa pagina delle proprietà, se non è già aperta.
1. Selezionare **Ambiti pubblicati**.
1. Immettere `Hello.Read` come **AMBITO** e `Read access to hello` come **DESCRIZIONE**.
1. Immettere `Hello.Write` come **AMBITO** e `Write access to hello` come **DESCRIZIONE**.
1. Selezionare **Salva**.
1. Prendere nota del **VALORE AMBITO COMPLETO** per l'ambito `Hello.Read` da usare in un passaggio successivo durante la configurazione dell'applicazione a pagina singola. Il valore dell'ambito completo è simile a `https://yourtenant.onmicrosoft.com/api/Hello.Read`.

Gli ambiti pubblicati possono essere usati per concedere a un'app client autorizzazioni per l'API Web.

## <a name="grant-permissions"></a>Concedere le autorizzazioni

Per chiamare un'API Web protetta da un'altra applicazione, è necessario concedere a tale applicazione le autorizzazioni per l'API Web.

Nell'esercitazione preliminare è stata creata un'applicazione Web denominata *webapp1*. In questa esercitazione si configurerà l'applicazione in modo che chiami l'API Web creata in una sezione precedente, *webapi1*.

1. Passare al tenant B2C nel portale di Azure
1. Selezionare **Applicazioni** e quindi *webapp1*.
1. Selezionare **Accesso all'API** e quindi **Aggiungi**.
1. Nell'elenco a discesa **Seleziona API** selezionare *webapi1*.
1. Nell'elenco a discesa **Seleziona ambiti** selezionare gli ambiti **Hello.Read** e **Hello.Write** definiti in precedenza.
1. Fare clic su **OK**.

L'applicazione a pagina singola viene registrata per la chiamata dell'API Web protetta. Un utente esegue l'autenticazione con Azure AD B2C per usare l'applicazione a pagina singola. L'app a pagina singola ottiene una concessione di autorizzazione da Azure AD B2C per l'accesso all'API Web protetta.

## <a name="configure-the-sample"></a>Configurare l'esempio

Dopo la registrazione dell'API Web e la definizione degli ambiti, configurare il codice dell'API Web per l'uso del tenant di Azure AD B2C. In questa esercitazione si configura un'applicazione Web .NET Core di esempio che è possibile scaricare da GitHub.

[Scaricare un file \*.zip](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip) o clonare il progetto di API Web di esempio di GitHub.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi.git
```

### <a name="configure-the-web-api"></a>Configurare l'API Web

1. Aprire il file *B2C-WebApi/**appsettings.json***  in Visual Studio o Visual Studio Code.
1. Modificare il blocco `AzureAdB2C` in base al nome del tenant, l'ID dell'applicazione API Web, il nome dei criteri di iscrizione/accesso e gli ambiti definiti in precedenza. Il blocco dovrebbe essere simile all'esempio seguente (con i valori `Tenant` e `ClientId` appropriati):

    ```json
    "AzureAdB2C": {
      "Tenant": "<your-tenant-name>.onmicrosoft.com",
      "ClientId": "<webapi-application-ID>",
      "Policy": "B2C_1_signupsignin1",

      "ScopeRead": "Hello.Read",
      "ScopeWrite": "Hello.Write"
    },
    ```

#### <a name="enable-cors"></a>Abilitare CORS

Per consentire all'applicazione a pagina singola di chiamare l'API Web ASP.NET Core, è necessario abilitare [CORS](https://docs.microsoft.com/aspnet/core/security/cors)nell'API Web.

1. In *Startup.cs* aggiungere CORS al metodo `ConfigureServices()`.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddCors();
    ```

1. Inoltre, all'interno del metodo `ConfigureServices()` impostare il valore `jwtOptions.Authority` sull'URI dell'autorità emittente del token seguente.

    Sostituire `<your-tenant-name>` con il nome del tenant B2C.

    ```csharp
    jwtOptions.Authority = $"https://<your-tenant-name>.b2clogin.com/{Configuration["AzureAdB2C:Tenant"]}/{Configuration["AzureAdB2C:Policy"]}/v2.0";
    ```

1. Nel metodo `Configure()` configurare CORS.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
    {
        app.UseCors(builder =>
            builder.WithOrigins("http://localhost:6420").AllowAnyHeader().AllowAnyMethod());
    ```

1. (Solo Visual Studio) In **Proprietà** di Esplora soluzioni aprire il file *launchSettings.json*, quindi trovare il blocco `iisExpress`.
1. (Solo Visual Studio) Aggiornare il valore `applicationURL` con il numero di porta specificato quando è stata registrata l'applicazione *webapi1* in un passaggio precedente. Ad esempio:

    ```json
    "iisExpress": {
      "applicationUrl": "http://localhost:5000/",
      "sslPort": 0
    }
    ```

### <a name="configure-the-single-page-application"></a>Configurare l'applicazione a pagina singola

L'applicazione a pagina singola dell'[esercitazione precedente](active-directory-b2c-tutorials-spa.md) della serie usa Azure AD B2C per l'iscrizione e l'accesso degli utenti e chiama l'API Web ASP.NET Core protetta dal tenant dimostrativo *frabrikamb2c*.

In questa sezione l'applicazione a pagina singola viene aggiornata per chiamare l'API Web ASP.NET Core protetta dal *proprio* tenant di Azure AD B2C e che viene eseguita nel computer locale.

Per cambiare le impostazioni nell'applicazione a pagina singola:

1. Aprire il file *index.html* nel progetto [active-directory-b2c-javascript-msal-singlepageapp][github-js-spa] scaricato o clonato dall'esercitazione precedente.
1. Configurare l'esempio con l'URI dell'ambito *Hello.Read* creato in precedenza e l'URL dell'API Web.
    1. Nella definizione `appConfig` sostituire il valore `b2cScopes` con l'URI completo dell'ambito (il **VALORE AMBITO COMPLETO** annotato in precedenza).
    1. Sostituire il valore `webApi` con il valore `applicationURL` specificato nella sezione precedente.

    La definizione `appConfig` dovrebbe essere simile al blocco di codice seguente (con il nome del proprio tenant al posto di `<your-tenant-name>`):

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var appConfig = {
      b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/api/Hello.Read"],
      webApi: "http://localhost:5000/"
    };
    ```

## <a name="run-the-spa-and-web-api"></a>Eseguire l'applicazione a pagina singola e l'API Web

Infine, eseguire sia l'API Web ASP.NET Core che l'applicazione Node.js a pagina singola nel computer locale. Quindi, accedere all'applicazione a pagina singola e premere un pulsante per avviare una richiesta all'API protetta.

Anche se vengono eseguite in locale in questa esercitazione, entrambe usano Azure AD B2C per proteggere l'iscrizione/accesso e per concedere l'accesso all'API Web protetta.

### <a name="run-the-aspnet-core-web-api"></a>Eseguire l'API Web ASP.NET Core

In Visual Studio premere **F5** per eseguire il debug della soluzione *B2C-WebAPI.sln*. Quando si avvia il progetto, nel browser predefinito viene visualizzata una pagina Web che annuncia che l'API Web è disponibile per le richieste.

Se si preferisce usare l'interfaccia della riga di comando `dotnet` invece di Visual Studio:

1. Aprire una finestra della console e passare alla directory contenente il file *\*.csproj*. Ad esempio:

    `cd active-directory-b2c-dotnetcore-webapi/B2C-WebApi`

1. Creare ed eseguire l'API Web eseguendo `dotnet run`.

    Quando l'API è attiva e in esecuzione, verrà visualizzato un output simile al seguente (per l'esercitazione, è possibile ignorare tutti gli avvisi `NETSDK1059`):

    ```console
    $ dotnet run
    Hosting environment: Production
    Content root path: /home/user/active-directory-b2c-dotnetcore-webapi/B2C-WebApi
    Now listening on: http://localhost:5000
    Application started. Press Ctrl+C to shut down.
    ```

### <a name="run-the-single-page-app"></a>Eseguire l'app a pagina singola

1. Aprire una finestra della console e passare alla directory contenente l'esempio Node.js. Ad esempio:

    `cd active-directory-b2c-javascript-msal-singlepageapp`

1. Eseguire i comandi seguenti:

    ```console
    npm install && npm update
    node server.js
    ```

    Nella finestra della console viene visualizzato il numero della porta in cui è ospitata l'applicazione.

    ```console
    Listening on port 6420...
    ```

1. Passare a `http://localhost:6420` nel browser per visualizzare l'applicazione.
1. Accedere con l'indirizzo di posta elettronica e la password usati nell'[esercitazione precedente](active-directory-b2c-tutorials-spa.md). Al completamento dell'accesso, verrà visualizzato il messaggio `User 'Your Username' logged-in`.
1. Selezionare il pulsante **Call Web API**. L'applicazione a pagina singola ottiene una concessione di autorizzazione da Azure AD B2C, quindi accede all'API Web protetta per visualizzare il contenuto della relativa pagina di indice:

    ```Output
    Web APi returned:
    "<html>\r\n<head>\r\n  <title>Azure AD B2C API Sample</title>\r\n ...
    ```

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Aggiungere un'applicazione API Web
> * Configurare gli ambiti per un'API Web
> * Concedere le autorizzazioni per l'API Web
> * Configurare l'esempio per l'uso dell'applicazione

Dopo aver visualizzato una richiesta di risorsa effettuata dall'applicazione a pagina singola all'API Web protetta, è possibile ottenere una conoscenza più approfondita dell'interazione tra questi tipi di applicazione e Azure AD B2C.

> [!div class="nextstepaction"]
> [Tipi di applicazioni che possono essere usate in Active Directory B2C >](active-directory-b2c-apps.md)

<!-- Links - EXTERNAL -->
[github-js-spa]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
