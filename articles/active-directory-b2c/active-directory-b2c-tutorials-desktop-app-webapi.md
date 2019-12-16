---
title: "Esercitazione: Concedere l'accesso a un'API Web Node.js da un'applicazione desktop"
description: Esercitazione su come usare Active Directory B2C per proteggere un'API Web Node.js e chiamarla da un'app desktop .NET.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 10/12/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: f271dd23501aa0514faff29f638f695c80c844f2
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74949773"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>Esercitazione: concedere l'accesso a un'API Web Node.js da un'app desktop usando Azure Active Directory B2C

Questa esercitazione illustra come chiamare un'API Web Node.js protetta da Azure Active Directory B2C (Azure AD B2C) da un'app desktop Windows Presentation Foundation (WPF), a sua volta protetta da Azure AD B2C.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Aggiungere un'applicazione API Web
> * Configurare gli ambiti per un'API Web
> * Concedere le autorizzazioni per l'API Web
> * Aggiornare l'esempio per l'uso dell'applicazione

## <a name="prerequisites"></a>Prerequisiti

Completare i passaggi e i prerequisiti riportati in [Esercitazione: Autenticare gli utenti in un client desktop nativo](active-directory-b2c-tutorials-desktop-app.md).

## <a name="add-a-web-api-application"></a>Aggiungere un'applicazione API Web

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Configurare gli ambiti

Gli ambiti consentono di regolamentare l'accesso alle risorse protette. Vengono usati dall'API Web per implementare il controllo degli accessi in base all'ambito. Alcuni utenti possono avere ad esempio accesso sia in lettura che in scrittura, mentre altri possono avere autorizzazioni di sola lettura. In questa esercitazione si definiscono autorizzazioni di lettura e scrittura per l'API Web.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Prendere nota del valore in **AMBITI** per l'ambito `demo.read` da usare in un passaggio successivo durante la configurazione dell'applicazione desktop. Il valore dell'ambito completo è simile a `https://contosob2c.onmicrosoft.com/api/demo.read`.

## <a name="grant-permissions"></a>Concedere le autorizzazioni

Per chiamare un'API Web protetta da un'applicazione client nativa, è necessario concedere le autorizzazioni per l'applicazione client nativa registrata all'API Web registrata in Azure AD B2C.

Nell'esercitazione sui prerequisiti è stata registrata un'applicazione client nativa denominata *nativeapp1*. I passaggi seguenti consentono di configurare la registrazione dell'applicazione nativa con gli ambiti dell'API esposti per *webapi1* nella sezione precedente. Ciò consente all'applicazione desktop di ottenere un token di accesso da Azure AD B2C che l'API Web può usare per verificare e fornire l'accesso con ambito alle relative risorse. Gli esempi di codice dell'applicazione desktop e dell'API Web vengono configurati ed eseguiti più avanti nell'esercitazione.

#### <a name="applicationstabapplications"></a>[Applicazioni](#tab/applications/)

1. Selezionare **Applicazioni** e quindi *nativeapp1*.
1. Selezionare **Accesso all'API** e quindi **Aggiungi**.
1. Nell'elenco a discesa **Seleziona API** selezionare *webapi1*.
1. Nell'elenco a discesa **Seleziona ambiti** selezionare gli ambiti definiti in precedenza. Ad esempio, *demo.read* e *demo.write*.
1. Selezionare **OK**.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Registrazioni app (anteprima)](#tab/app-reg-preview/)

1. Selezionare **Registrazioni app (anteprima)** e quindi l'applicazione client nativa che deve avere accesso all'API. Ad esempio, *nativeapp1*.
1. In **Gestisci** selezionare **Autorizzazioni API**.
1. In **Autorizzazioni configurate** selezionare **Aggiungi un'autorizzazione**.
1. Selezionare la scheda **Le mie API**.
1. Selezionare l'API per cui è necessario concedere l'accesso all'applicazione client nativa. Ad esempio, *webapi1*.
1. In **Autorizzazione** espandere **demo** e quindi selezionare gli ambiti definiti in precedenza. Ad esempio, *demo.read* e *demo.write*.
1. Selezionare **Aggiungi autorizzazioni**. Come indicato, attendere alcuni minuti prima di procedere con il passaggio successivo.
1. Selezionare **Concedi consenso amministratore per (nome del tenant)** .
1. Selezionare l'account amministratore attualmente connesso oppure accedere con un account nel tenant di Azure AD B2C a cui sia stato assegnato almeno il ruolo di *amministratore applicazione cloud*.
1. Selezionare **Accetto**.
1. Selezionare **Aggiorna** e quindi verificare che il testo "Concesso per" sia visualizzato in **Stato** per entrambi gli ambiti. La propagazione delle autorizzazioni potrebbe richiedere alcuni minuti.

* * *

Gli utenti eseguono l'autenticazione con Azure AD B2C per usare l'applicazione desktop WPF. L'applicazione desktop ottiene una concessione di autorizzazione da Azure AD B2C per l'accesso all'API Web protetta.

## <a name="configure-the-samples"></a>Configurare gli esempi

Ora che l'API Web è stata registrata e sono stati configurati gli ambiti e le autorizzazioni, configurare l'applicazione desktop e gli esempi dell'API Web per l'uso del tenant di Azure AD B2C.

### <a name="update-the-desktop-application"></a>Aggiornare l'applicazione desktop

In un prerequisito per questo articolo è stata modificata un'[applicazione desktop WPF](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) per abilitare l'accesso con un flusso utente nel tenant di Azure AD B2C. In questa sezione si aggiorna la stessa applicazione per fare riferimento all'API Web registrata in precedenza, *webapi1*.

1. Aprire la soluzione **active-directory-b2c-wpf** (`active-directory-b2c-wpf.sln`) in Visual Studio.
1. Nel progetto **active-directory-b2c-wpf** aprire il file *App.xaml.cs* e trovare le definizioni delle variabili seguenti.
    1. Sostituire il valore della variabile `ApiScopes` con il valore registrato in precedenza quando è stato definito l'ambito **demo.read**.
    1. Sostituire il valore della variabile `ApiEndpoint` con **l'URI di reindirizzamento** registrato in precedenza quando è stata registrata l'API Web (ad esempio *webapi1*) nel tenant.

    Ad esempio:

    ```csharp
    public static string[] ApiScopes = { "https://contosob2c.onmicrosoft.com/api/demo.read" };
    public static string ApiEndpoint = "http://localhost:5000";
    ```

### <a name="get-and-update-the-nodejs-api-sample"></a>Ottenere e aggiornare l'esempio dell'API Node.js

Quindi, ottenere l'esempio di codice dell'API Web Node.js da GitHub e configurarlo per l'uso dell'API Web registrata nel tenant di Azure AD B2C.

[Scaricare un file ZIP](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) o clonare l'app Web di esempio da GitHub.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

L'API Web Node.js di esempio usa la libreria Passport.js per consentire ad Azure Active Directory B2C di proteggere le chiamate all'API.

1. Aprire il file `index.js` .
1. Aggiornare le definizioni delle variabili con i valori seguenti. Modificare `<web-API-application-ID>` con l'**ID dell'applicazione (client)** dell'API Web registrata in precedenza (*webapi1*). Modificare `<your-b2c-tenant>` con il nome del tenant di Azure AD B2C.

    ```nodejs
    var clientID = "<web-API-application-ID>";
    var b2cDomainHost = "<your-b2c-tenant>.b2clogin.com";
    var tenantIdGuid = "<your-b2c-tenant>.onmicrosoft.com";
    var policyName = "B2C_1_signupsignin1";
    ```
1. Poiché l'API viene eseguita in locale, aggiornare il percorso nella route per il metodo GET in `/` anziché il percorso dell'app demo di `/hello`:

    ```nodejs
    app.get("/",
    ```

## <a name="run-the-samples"></a>Eseguire gli esempi

### <a name="run-the-nodejs-web-api"></a>Eseguire l'API Web Node.js:

1. Avviare un prompt dei comandi di Node.js.
2. Passare alla directory contenente l'esempio di Node.js. Ad esempio `cd c:\active-directory-b2c-javascript-nodejs-webapi`
3. Eseguire i comandi seguenti:
    ```console
    npm install && npm update
    ```
    ```console
    node index.js
    ```

### <a name="run-the-desktop-application"></a>Eseguire l'applicazione desktop

1. Aprire la soluzione **active-directory-b2c-wpf** in Visual Studio.
2. Premere **F5** per eseguire l'app desktop.
3. Accedere usando l'indirizzo e-mail e la password usati nell'esercitazione sull'[autenticazione degli utenti con Azure Active Directory B2C in un'applicazione desktop](active-directory-b2c-tutorials-desktop-app.md).
4. Selezionare il pulsante **Call API** (Chiama API).

L'applicazione desktop effettua una richiesta all'API Web in esecuzione localmente e, alla verifica di un token di accesso valido, mostra il nome visualizzato dell'utente connesso.

![Nome visualizzato mostrato nel riquadro superiore dell'applicazione desktop WPF](media/active-directory-b2c-tutorials-desktop-app-webapi/desktop-app-01-post-api-call.png)

L'applicazione desktop, protetta da Azure AD B2C, chiama l'API Web in esecuzione localmente che è a sua volta protetta da Azure AD B2C.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Aggiungere un'applicazione API Web
> * Configurare gli ambiti per un'API Web
> * Concedere le autorizzazioni per l'API Web
> * Aggiornare l'esempio per l'uso dell'applicazione

> [!div class="nextstepaction"]
> [Esercitazione: Aggiungere provider di identità alle applicazioni in Azure Active Directory B2C](tutorial-add-identity-providers.md)
