---
title: "Esercitazione: concedere l'accesso a un'API Web Node.js da un'app desktop usando Azure Active Directory B2C | Microsoft Docs"
description: Esercitazione su come usare Active Directory B2C per proteggere un'API Web Node.js e chiamarla da un'app desktop .NET.
services: active-directory-b2c
author: PatAltimore
ms.author: patricka
ms.reviewer: parja
ms.date: 3/01/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory-b2c
ms.openlocfilehash: da3b8c42fc98f0957f2fa1a1ac95e12737528863
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2018
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>Esercitazione: concedere l'accesso a un'API Web Node.js da un'app desktop usando Azure Active Directory B2C

Questa esercitazione illustra come chiamare una risorsa API Web Node.js protetta da Azure Active Directory (Azure AD) B2C da un'app desktop Windows Presentation Foundation (WPF).

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Registrare un'API Web nel tenant di Azure AD B2C
> * Definire e configurare gli ambiti per un'API Web
> * Concedere le autorizzazioni per l'app all'API Web
> * Aggiornare il codice di esempio per l'uso di Azure AD B2C per proteggere un'API Web

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>prerequisiti

* Completare l'esercitazione sull'[autenticazione degli utenti con Azure Active Directory B2C in un'applicazione desktop](active-directory-b2c-tutorials-desktop-app.md).
* Installare [Visual Studio 2017](https://www.visualstudio.com/downloads/) con i carichi di lavoro **Sviluppo per desktop .NET** e **Sviluppo ASP.NET e Web**.
* Installare [Node.js](https://nodejs.org/en/download/)

## <a name="register-web-api"></a>Registrare l'API Web

Le risorse API Web devono essere registrate nel tenant prima che possano accettare e rispondere a [richieste di risorse protette](../active-directory/develop/active-directory-dev-glossary.md#resource-server) da parte di [applicazioni client](../active-directory/develop/active-directory-dev-glossary.md#client-application) che presentano un [token di accesso](../active-directory/develop/active-directory-dev-glossary.md#access-token) di Azure Active Directory. La registrazione stabilisce l'[oggetto applicazione ed entità servizio](../active-directory/develop/active-directory-dev-glossary.md#application-object) nel tenant. 

Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di Azure AD B2C.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Selezionare **Azure AD B2C** dall'elenco di servizi nel portale di Azure.

2. Nelle impostazioni di B2C fare clic su **Applicazioni** e quindi su **Aggiungi**.

    Per registrare l'API Web di esempio nel tenant, usare le impostazioni seguenti.
    
    ![Aggiungere una nuova API](media/active-directory-b2c-tutorials-desktop-app-webapi/web-api-registration.png)
    
    | Impostazione      | Valore consigliato  | Descrizione                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nome** | API Web Node.js di esempio | Immettere un **nome** che descriva l'API Web agli sviluppatori. |
    | **Includi app Web/API Web** | Sì | Selezionare **Sì** per un'API Web. |
    | **Consenti il flusso implicito** | Sì | Selezionare **Sì** perché l'API usa l'[accesso OpenID Connect](active-directory-b2c-reference-oidc.md). |
    | **URL di risposta** | `http://localhost:5000` | Gli URL di risposta sono gli endpoint a cui Azure AD B2C restituisce eventuali token richiesti dall'API. In questa esercitazione l'API Web di esempio viene eseguita in locale (localhost) ed è in ascolto sulla porta 5000. |
    | **URI ID app** | demoapi | L'URI identifica l'API in modo univoco nel tenant. In questo modo è possibile registrare più API per ogni tenant. Gli [ambiti](../active-directory/develop/active-directory-dev-glossary.md#scopes) regolano l'accesso alla risorsa API protetta e vengono definiti per ogni URI ID app. |
    | **Client nativo** | No  | Trattandosi di un'API Web e non un client nativo, selezionare No. |
    
3. Fare clic su **Crea** per registrare l'API.

Le API registrate vengono visualizzate nell'elenco di applicazioni per il tenant Azure AD B2C. Selezionare l'API Web dall'elenco. Viene visualizzato il riquadro delle proprietà dell'API Web.

![Proprietà dell'API Web](./media/active-directory-b2c-tutorials-web-api/b2c-web-api-properties.png)

Annotare l'**ID client applicazione**. L'ID identifica l'API in modo univoco ed è necessario per configurare l'API più avanti nell'esercitazione.

La registrazione dell'API Web con Azure AD B2C definisce una relazione di trust. L'API è registrata con B2C, quindi può ora considerare attendibili i token di accesso B2C ricevuti da altre applicazioni.

## <a name="define-and-configure-scopes"></a>Definire e configurare gli ambiti

Gli [ambiti](../active-directory/develop/active-directory-dev-glossary.md#scopes) consentono di regolare l'accesso alle risorse protette. Vengono usati dall'API Web per implementare il controllo degli accessi in base all'ambito. Alcuni utenti possono avere ad esempio accesso sia in lettura che in scrittura, mentre altri possono avere autorizzazioni di sola lettura. In questa esercitazione si definiscono autorizzazioni di lettura e scrittura per l'API Web.

### <a name="define-scopes-for-the-web-api"></a>Definire gli ambiti per l'API Web

Le API registrate vengono visualizzate nell'elenco di applicazioni per il tenant Azure AD B2C. Selezionare l'API Web dall'elenco. Viene visualizzato il riquadro delle proprietà dell'API Web.

Fare clic su **Ambiti pubblicati (anteprima)**.

Per configurare gli ambiti per l'API, aggiungere le voci seguenti. 

![Ambiti definiti nell'API Web](media/active-directory-b2c-tutorials-web-api/scopes-defined-in-web-api.png)

| Impostazione      | Valore consigliato  | DESCRIZIONE                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Ambito** | demo.read | Accesso in lettura a all'API demo|

Fare clic su **Save**.

Gli ambiti pubblicati possono essere usati per concedere a un'app client autorizzazioni per l'API Web.

### <a name="grant-app-permissions-to-web-api"></a>Concedere autorizzazioni all'app per l'API Web

Per chiamare un'API Web protetta da un'app, è necessario concedere all'app le autorizzazioni per l'API. In questa esercitazione usare l'app desktop creata nell'esercitazione sull'[autenticazione degli utenti con Azure Active Directory B2C in un'applicazione desktop](active-directory-b2c-tutorials-desktop-app.md).

1. Nel portale selezionare **Azure AD B2C** dall'elenco dei servizi e fare clic su **Applicazioni** per visualizzare l'elenco delle app registrate.

2. Selezionare **App WPF di esempio** dall'elenco di app, fare clic su **Accesso all'API (anteprima)** e quindi su **Aggiungi**.

3. Nell'elenco a discesa **Seleziona API** selezionare l'API Web registrata, ovvero **API Web Node.js di esempio**.

4. Nell'elenco a discesa **Seleziona ambiti** selezionare gli ambiti definiti nella registrazione dell'API Web.

    ![Selezione degli ambiti per l'app](media/active-directory-b2c-tutorials-web-api/selecting-scopes-for-app.png)

5. Fare clic su **OK**.

L'**App WPF di esempio** è registrata per chiamare l'**API Web Node.js di esempio** protetta. Gli utenti [eseguono l'autenticazione](../active-directory/develop/active-directory-dev-glossary.md#authentication) con Azure AD B2C per usare l'app desktop WPF. L'app desktop ottiene una [concessione di autorizzazione](../active-directory/develop/active-directory-dev-glossary.md#authorization-grant) da Azure AD B2C per l'accesso all'API Web protetta.

## <a name="update-web-api-code"></a>Aggiornare il codice dell'API Web

Ora che l'API Web è registrata e sono stati definiti gli ambiti, è necessario configurare il codice dell'API Web per l'uso del tenant di Azure AD B2C. In questa esercitazione si configura un'app Web Node.js di esempio che è possibile scaricare da GitHub. 

[Scaricare un file ZIP](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) o clonare l'app Web di esempio da GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```
L'API Web Node.js di esempio usa la libreria Passport.js per consentire ad Azure Active Directory B2C di proteggere le chiamate all'API. 

### <a name="configure-the-web-api"></a>Configurare l'API Web

1. Aprire il file `index.html` nell'API Web Node.js di esempio.
2. Configurare l'esempio con le informazioni di registrazione tenant di Azure Active Directory B2C. Modificare le righe di codice seguenti:

```nodejs
var tenantID = "<your-tenant-name>.onmicrosoft.com";
var clientID = "<Application ID for your Node.js Web API>";
var policyName = "B2C_1_SiUpIn";  // Sign-in / sign-up policy name
```

### <a name="configure-the-desktop-app"></a>Configurare l'app desktop

1. Aprire la soluzione `active-directory-b2c-wpf` dall'esercitazione sull'[autenticazione degli utenti con Azure Active Directory B2C in un'applicazione desktop](active-directory-b2c-tutorials-desktop-app.md) in Visual Studio.

## <a name="run-the-sample"></a>Eseguire l'esempio

Eseguire l'API Web Node.js:

1. Avviare un prompt dei comandi di Node.js.
2. Passare alla directory contenente l'esempio di Node.js. Ad esempio `cd c:\active-directory-b2c-javascript-nodejs-webapi`
3. Eseguire i comandi seguenti:
    ```
    npm install && npm update
    node index.js
    ```

Eseguire l'app desktop:

1. Premere **F5** per eseguire l'app desktop.
2. Accedere usando l'indirizzo e-mail e la password usati nell'esercitazione sull'[autenticazione degli utenti con Azure Active Directory B2C in un'applicazione desktop](active-directory-b2c-tutorials-desktop-app.md).
3. Fare clic sul pulsante **Call API** (Chiama API). 

L'app desktop effettua una richiesta all'API Web e ottiene una risposta con il nome visualizzato dell'utente connesso. L'app desktop protetta chiama l'API Web protetta nel tenant di Azure AD B2C.

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile usare il tenant di Azure AD B2C se si prevede di provare altre esercitazioni relative ad Azure AD B2C. Quando non è più necessario, è possibile [eliminare il tenant di Azure AD B2C](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stata descritta la protezione di un'API Web ASP.NET tramite la registrazione e la definizione degli ambiti in Azure AD B2C. Per altre informazioni, esplorare gli esempi di codice disponibili per Azure Active Directory B2C.

> [!div class="nextstepaction"]
> [Esempi di codice per Azure AD B2C](https://azure.microsoft.com/resources/samples/?service=active-directory-b2c&sort=0)