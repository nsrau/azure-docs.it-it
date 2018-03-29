---
title: "Esercitazione: Usare Azure Active Directory B2C per proteggere un'API Web ASP.NET"
description: Esercitazione su come usare Active Directory B2C per proteggere un'API Web ASP.NET e chiamarla da un'app Web ASP.NET.
services: active-directory-b2c
author: davidmu1
manager: mtillman
editor: ''
ms.author: davidmu
ms.date: 1/23/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory-b2c
ms.openlocfilehash: f4e1c18f151a9c815258f01ea198d3d173d0b44e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="tutorial-use-azure-active-directory-b2c-to-protect-an-aspnet-web-api"></a>Esercitazione: Usare Azure Active Directory B2C per proteggere un'API Web ASP.NET

Questa esercitazione illustra come chiamare da un'app Web ASP.NET una risorsa API Web protetta da Azure Active Directory (Azure AD) B2C.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Registrare un'API Web nel tenant di Azure AD B2C
> * Definire e configurare gli ambiti per un'API Web
> * Concedere le autorizzazioni per l'app all'API Web
> * Aggiornare il codice di esempio per l'uso di Azure AD B2C per proteggere un'API Web

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>prerequisiti

* Completare l'esercitazione [Usare Azure Active Directory B2C per l'autenticazione degli utenti in un'app Web ASP.NET](active-directory-b2c-tutorials-web-app.md).
* Installare [Visual Studio 2017](https://www.visualstudio.com/downloads/) con il carico di lavoro **Sviluppo ASP.NET e Web**.

## <a name="register-web-api"></a>Registrare l'API Web

Le risorse API Web devono essere registrate nel tenant prima che possano accettare e rispondere a [richieste di risorse protette](../active-directory/develop/active-directory-dev-glossary.md#resource-server) da parte di [applicazioni client](../active-directory/develop/active-directory-dev-glossary.md#client-application) che presentano un [token di accesso](../active-directory/develop/active-directory-dev-glossary.md#access-token) di Azure Active Directory. La registrazione stabilisce l'[oggetto applicazione ed entità servizio](../active-directory/develop/active-directory-dev-glossary.md#application-object) nel tenant. 

Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di Azure AD B2C.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Selezionare **Azure AD B2C** dall'elenco di servizi nel portale di Azure.

2. Nelle impostazioni di B2C fare clic su **Applicazioni** e quindi su **+ Aggiungi**.

    Per registrare l'API Web di esempio nel tenant, usare le impostazioni seguenti.
    
    ![Aggiungere una nuova API](media/active-directory-b2c-tutorials-web-api/web-api-registration.png)
    
    | Impostazione      | Valore consigliato  | Descrizione                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nome** | API Web di esempio | Immettere un **nome** che descriva l'API Web agli sviluppatori. |
    | **Includi app Web/API Web** | Sì | Selezionare **Sì** per un'API Web. |
    | **Consenti il flusso implicito** | Sì | Selezionare **Sì** perché l'API usa l'[accesso OpenID Connect](active-directory-b2c-reference-oidc.md). |
    | **URL di risposta** | `https://localhost:44332` | Gli URL di risposta sono gli endpoint a cui Azure AD B2C restituisce eventuali token richiesti dall'API. In questa esercitazione, l'API Web di esempio viene eseguita in locale (localhost) ed è in ascolto sulla porta 44332. |
    | **URI ID app** | myAPISample | L'URI identifica l'API in modo univoco nel tenant. In questo modo è possibile registrare più API per ogni tenant. Gli [ambiti](../active-directory/develop/active-directory-dev-glossary.md#scopes) regolano l'accesso alla risorsa API protetta e vengono definiti per ogni URI ID app. |
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
| **Ambito** | Hello.Read | Accesso in lettura a Hello |
| **Ambito** | Hello.Write | Accesso in scrittura a Hello |

Gli ambiti pubblicati possono essere usati per concedere a un'app client autorizzazioni per l'API Web.

### <a name="grant-app-permissions-to-web-api"></a>Concedere autorizzazioni all'app per l'API Web

Per chiamare un'API Web protetta da un'app, è necessario concedere all'app le autorizzazioni per l'API. 

1. Nel portale selezionare **Azure AD B2C** dall'elenco dei servizi e fare clic su **Applicazioni** per visualizzare l'elenco delle app registrate.

2. Selezionare **App Web di esempio** dall'elenco di app e fare clic su **Accesso all'API (anteprima)** e quindi su **Aggiungi**.

3. Nell'elenco a discesa **Seleziona API** selezionare l'API Web registrata, ovvero **API Web di esempio**.

4. Nell'elenco a discesa **Seleziona ambiti** selezionare gli ambiti definiti nella registrazione dell'API Web.

    ![Selezione degli ambiti per l'app](media/active-directory-b2c-tutorials-web-api/selecting-scopes-for-app.png)

5. Fare clic su **OK**.

L'**App Web di esempio** è registrata per chiamare l'**API Web di esempio** protetta. Un utente esegue l'[autenticazione](../active-directory/develop/active-directory-dev-glossary.md#authentication) con Azure AD B2C per usare l'app Web. L'app Web ottiene una [concessione di autorizzazione](../active-directory/develop/active-directory-dev-glossary.md#authorization-grant) da Azure AD B2C per l'accesso all'API Web protetta.

## <a name="update-web-api-code"></a>Aggiornare il codice dell'API Web

Ora che l'API Web è registrata e sono stati definiti gli ambiti, è necessario configurare il codice dell'API Web per l'uso del tenant di Azure AD B2C. In questa esercitazione si configura un'API Web di esempio. 

L'API Web di esempio è inclusa nel progetto scaricato nell'esercitazione preliminare [Usare Azure Active Directory B2C per l'autenticazione degli utenti in un'app Web ASP.NET](active-directory-b2c-tutorials-web-app.md). Se l'esercitazione preliminare non è stata completata, completarla prima di continuare.

La soluzione di esempio contiene due progetti:

**App Web di esempio (TaskWebApp):** app Web per la creazione e modifica di un elenco di attività. L'app Web usa il **criterio di iscrizione o accesso** per l'iscrizione o l'accesso degli utenti tramite un indirizzo e-mail.

**App di esempio dell'API Web (TaskService):** API Web che supporta la funzionalità di creazione, lettura, aggiornamento ed eliminazione dell'elenco di attività. L'API Web è protetta da Azure AD B2C e chiamata dall'app Web.

L'app Web e l'API Web di esempio definiscono i valori di configurazione come impostazioni dell'app nel file Web.config di ogni progetto.

Aprire la soluzione **B2C-WebAPI-DotNet** in Visual Studio.

### <a name="configure-the-web-app"></a>Configurare l'app Web

1. Aprire **Web.config** nel progetto **TaskWebApp**.

2. Per eseguire l'API in locale, usare l'impostazione localhost per **api:TaskServiceUrl**. Modificare il file Web.config come segue: 

    ```C#
    <add key="api:TaskServiceUrl" value="https://localhost:44332/"/>
    ```

3. Configurare l'URI dell'API. Si tratta dell'URI usato dall'app Web per eseguire la richiesta API. Configurare anche le autorizzazioni richieste.

```C#
<add key="api:ApiIdentifier" value="https://<Your tenant name>.onmicrosoft.com/myAPISample/" />
<add key="api:ReadScope" value="Hello.Read" />
<add key="api:WriteScope" value="Hello.Write" />
```

### <a name="configure-the-web-api"></a>Configurare l'API Web

1. Aprire **Web.config** nel progetto **TaskService**.

2. Configurare l'API per l'uso del tenant.

    ```C#
    <add key="ida:Tenant" value="<Your tenant name>.onmicrosoft.com" />
    ```

3. Per l'ID client usare l'ID dell'applicazione registrata per l'API.

    ```C#
    <add key="ida:ClientId" value="<The Application ID for your web API obtained from the Azure portal>"/>
    ```

4. Aggiornare l'impostazione dei criteri con il nome generato quando sono stati creati i criteri di iscrizione o accesso.

    ```C#
    <add key="ida:SignUpSignInPolicyId" value="b2c_1_SiUpIn" />
    ```

5. Configurare l'impostazione degli ambiti in modo che corrisponda a quella creata nel portale.

    ```C#
    <add key="api:ReadScope" value="Hello.Read" />
    <add key="api:WriteScope" value="Hello.Write" />
    ```

## <a name="run-the-sample-web-app-and-web-api"></a>Eseguire l'app Web e l'API Web di esempio

È necessario eseguire entrambi i progetti, ovvero **TaskWebApp** e **TaskService**. 

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione e selezionare **Imposta progetti di avvio...**. 
2. Selezionare il pulsante di opzione **Progetti di avvio multipli**.
3. Modificare il valore di **Azione** in entrambi i progetti in **Avvia**.
4. Fare clic su OK per salvare la configurazione.
5. Premere **F5** per eseguire entrambe le applicazioni. Ogni applicazione si apre in una scheda del browser separata. `https://localhost:44316/` è l'app Web.
    `https://localhost:44332/` è l'API Web.

6. Nell'app Web, fare clic sul collegamento di iscrizione/accesso nel banner dei menu per eseguire l'iscrizione all'applicazione Web. Usare l'account creato nell'[esercitazione relativa all'app Web](active-directory-b2c-tutorials-web-app.md). 
7. Una volta effettuato l'accesso, fare clic sul collegamento **Elenco attività** e creare un elemento per l'elenco.

Quando si crea un elemento dell'elenco attività, l'app Web invia una richiesta all'API Web per la generazione dell'elemento. L'app Web protetta chiama l'API Web protetta nel tenant di Azure AD B2C.

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile usare il tenant di Azure AD B2C se si prevede di provare altre esercitazioni relative ad Azure AD B2C. Quando non è più necessario, è possibile [eliminare il tenant di Azure AD B2C](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stata descritta la protezione di un'API Web ASP.NET tramite la registrazione e la definizione degli ambiti in Azure AD B2C. Per altre informazioni sullo sviluppo di questo scenario, incluse informazioni dettagliate sul codice, passare all'esercitazione successiva.

> [!div class="nextstepaction"]
> [Creare un'app Web ASP.NET con criteri di iscrizione, accesso, modifica del profilo e reimpostazione della password di Azure Active Directory B2C](active-directory-b2c-devquickstarts-web-dotnet-susi.md)