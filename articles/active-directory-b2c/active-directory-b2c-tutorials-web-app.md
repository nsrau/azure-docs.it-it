---
title: 'Esercitazione: Usare Azure Active Directory B2C per l''autenticazione degli utenti in un''app Web ASP.NET'
description: Esercitazione su come usare Azure Active Directory B2C per l'iscrizione e l'accesso degli utenti a un'app Web ASP.NET.
services: active-directory-b2c
author: PatAltimore
ms.author: patricka
ms.reviewer: saraford
ms.date: 1/23/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory-b2c
ms.openlocfilehash: ee006476f9e40e9d1a6e7213cb1881ca46ea75c2
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="tutorial-authenticate-users-with-azure-active-directory-b2c-in-an-aspnet-web-app"></a>Esercitazione: Eseguire l'autenticazione degli utenti con Azure Active Directory B2C in un'app Web ASP.NET

Questa esercitazione illustra come usare Azure Active Directory (Azure AD) B2C per l'iscrizione e l'accesso degli utenti a un'app Web ASP.NET. Azure AD B2C consente alle app di eseguire l'autenticazione ad account di social network, account aziendali e account di Azure Active Directory usando protocolli standard aperti.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Registrare un'app Web ASP.NET di esempio nel tenant di Azure AD B2C.
> * Creare criteri per iscrizione, accesso, modifica del profilo e reimpostazione delle password.
> * Configurare l'app Web di esempio per l'uso del tenant di Azure AD B2C. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>prerequisiti

* Creare il proprio [tenant di Azure AD B2C](active-directory-b2c-get-started.md)
* Installare [Visual Studio 2017](https://www.visualstudio.com/downloads/) con il carico di lavoro **Sviluppo ASP.NET e Web**.

## <a name="register-web-app"></a>Registrare l'app Web

Le applicazioni devono essere [registrate](../active-directory/develop/active-directory-dev-glossary.md#application-registration) nel tenant perché possano ricevere [token di accesso](../active-directory/develop/active-directory-dev-glossary.md#access-token) da Azure Active Directory. La registrazione dell'app crea un [ID applicazione](../active-directory/develop/active-directory-dev-glossary.md#application-id-client-id) per l'app nel tenant. 

Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di Azure AD B2C.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Selezionare **Azure AD B2C** dall'elenco di servizi nel portale di Azure.

2. Nelle impostazioni di B2C fare clic su **Applicazioni** e quindi su **Aggiungi**.

    Per registrare l'app Web di esempio nel tenant, usare le impostazioni seguenti.

    ![Aggiungere una nuova app](media/active-directory-b2c-tutorials-web-app/web-app-registration.png)

    | Impostazione      | Valore consigliato  | Descrizione                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nome** | App Web di esempio | Immettere un **nome** che descriva l'app agli utenti. | 
    | **Includi app Web/API Web** | Sì | Selezionare **Sì** per un'app Web. |
    | **Consenti il flusso implicito** | Sì | Selezionare **Sì** perché l'app usa l'[accesso OpenID Connect](active-directory-b2c-reference-oidc.md). |
    | **URL di risposta** | `https://localhost:44316` | Gli URL di risposta sono gli endpoint a cui Azure AD B2C restituisce eventuali token richiesti dall'app. In questa esercitazione, l'app di esempio viene eseguita in locale (localhost) ed è in ascolto sulla porta 44316. |
    | **Client nativo** | No  | Trattandosi di un'app Web e non un client nativo, selezionare No. |

3. Fare clic su **Crea** per registrare l'app.

Le app registrate vengono visualizzate nell'elenco di applicazioni per il tenant di Azure AD B2C. Selezionare l'app Web dall'elenco. Viene visualizzato il riquadro delle proprietà dell'app Web.

![Proprietà dell'app Web](./media/active-directory-b2c-tutorials-web-app/b2c-web-app-properties.png)

Annotare l'**ID client applicazione**. L'ID identifica l'app in modo univoco ed è necessario per configurare l'app più avanti nell'esercitazione.

### <a name="create-a-client-password"></a>Creare una password client

Azure AD B2C usa l'autorizzazione OAuth2 per le [applicazioni client](../active-directory/develop/active-directory-dev-glossary.md#client-application). Le app Web sono [client riservati](../active-directory/develop/active-directory-dev-glossary.md#web-client) e richiedono un segreto client (password). L'ID client dell'applicazione e il segreto client vengono usati quando l'app Web esegue l'autenticazione con Azure Active Directory. 

1. Selezionare la pagina Chiavi per l'app Web registrata e fare clic su **Genera chiave**.

2. Fare clic su **Salva** per visualizzare la chiave.

    ![Pagina Chiavi generale dell'app](media/active-directory-b2c-tutorials-web-app/app-general-keys-page.png)

La chiave viene visualizzata una sola volta nel portale. È importante copiare e salvare il valore della chiave perché servirà per la configurazione dell'app. Tenere la chiave riservata. Non condividerla pubblicamente.

## <a name="create-policies"></a>Creare criteri

Un criterio di Azure AD B2C definisce i flussi di lavoro degli utenti. Ad esempio accesso, iscrizione, modifica delle password e modifica dei profili sono flussi di lavoro comuni.

### <a name="create-a-sign-up-or-sign-in-policy"></a>Creare un criterio di iscrizione o accesso

Per configurare gli utenti per l'iscrizione e l'accesso all'app Web, creare **criteri di iscrizione o accesso**.

1. Dalla pagina del portale di Azure AD B2C selezionare **Criteri di iscrizione o di accesso** e fare clic su **Aggiungi**.

    Per configurare i criteri, usare le impostazioni seguenti:

    ![Aggiungere criteri di iscrizione o di accesso](media/active-directory-b2c-tutorials-web-app/add-susi-policy.png)

    | Impostazione      | Valore consigliato  | Descrizione                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nome** | SiUpIn | Immettere un **nome** per il criterio. Il nome del criterio ha il prefisso **b2c_1_**. Usare il nome completo **b2c_1_SiUpIn** del criterio nel codice di esempio. | 
    | **Provider di identità** | Iscrizione tramite posta elettronica | Provider di identità usato per identificare l'utente in modo univoco. |
    | **Attributi di iscrizione** | Nome visualizzato e Codice postale | Selezionare gli attributi che devono essere raccolti dall'utente durante l'iscrizione. |
    | **Attestazioni dell'applicazione** | Nome visualizzato, Codice postale, Nuovo utente, ID oggetto dell'utente | Selezionare le [attestazioni](../active-directory/develop/active-directory-dev-glossary.md#claim) da includere nel [token di accesso](../active-directory/develop/active-directory-dev-glossary.md#access-token). |

2. Fare clic su **Crea** per creare i criteri. 

### <a name="create-a-profile-editing-policy"></a>Creare i criteri di modifica del profilo

Per consentire agli utenti di reimpostare autonomamente le informazioni del profilo, creare **criteri di modifica del profilo**.

1. Dalla pagina del portale di Azure AD B2C selezionare **Criteri di modifica del profilo** e fare clic su **Aggiungi**.

    Per configurare i criteri, usare le impostazioni seguenti:

    | Impostazione      | Valore consigliato  | Descrizione                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nome** | SiPe | Immettere un **nome** per il criterio. Il nome del criterio ha il prefisso **b2c_1_**. Usare il nome completo **b2c_1_SiPe** del criterio nel codice di esempio. | 
    | **Provider di identità** | Accesso all'account locale | Provider di identità usato per identificare l'utente in modo univoco. |
    | **Attributi del profilo** | Nome visualizzato e Codice postale | Selezionare gli attributi che un utente può modificare durante la modifica del profilo. |
    | **Attestazioni dell'applicazione** | Nome visualizzato, Codice postale, Nuovo utente, ID oggetto dell'utente | Selezionare le [attestazioni](../active-directory/develop/active-directory-dev-glossary.md#claim) da includere nel [token di accesso](../active-directory/develop/active-directory-dev-glossary.md#access-token) dopo aver completato la modifica del profilo. |

2. Fare clic su **Crea** per creare i criteri. 

### <a name="create-a-password-reset-policy"></a>Creare i criteri di reimpostazione delle password

Per abilitare la reimpostazione delle password nell'applicazione, è necessario creare **criteri di reimpostazione delle password**. Questi criteri descrivono l'esperienza utente durante la procedura di reimpostazione delle password e il contenuto dei token che l'applicazione riceverà al completamento della procedura.

1. Dalla pagina del portale di Azure AD B2C selezionare **Criteri di reimpostazione password** e fare clic su **Aggiungi**.

    Per configurare i criteri, usare le impostazioni seguenti.

    | Impostazione      | Valore consigliato  | Descrizione                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nome** | SSPR | Immettere un **nome** per il criterio. Il nome del criterio ha il prefisso **b2c_1_**. Usare il nome completo **b2c_1_SSPR** del criterio nel codice di esempio. | 
    | **Provider di identità** | Reimpostare la password usando l'indirizzo e-mail | Provider di identità usato per identificare l'utente in modo univoco. |
    | **Attestazioni dell'applicazione** | ID oggetto dell'utente | Selezionare le [attestazioni](../active-directory/develop/active-directory-dev-glossary.md#claim) da includere nel [token di accesso](../active-directory/develop/active-directory-dev-glossary.md#access-token) dopo aver completato la reimpostazione delle password. |

2. Fare clic su **Crea** per creare i criteri. 

## <a name="update-web-app-code"></a>Aggiornare il codice dell'app Web

Ora che l'app Web è registrata e sono stati creati i criteri, è necessario configurare l'app per l'uso del tenant di Azure AD B2C. In questa esercitazione si configura un'app Web di esempio. 

[Scaricare un file ZIP](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip) o clonare l'app Web di esempio da GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

L'app Web ASP.NET di esempio è una semplice app per la creazione e l'aggiornamento di un elenco attività. L'app usa [componenti middleware di Microsoft OWIN](https://docs.microsoft.com/en-us/aspnet/aspnet/overview/owin-and-katana/) per consentire agli utenti di effettuare l'iscrizione per usare l'app nel tenant di Azure AD B2C. Tramite la creazione di criteri di Azure AD B2C, gli utenti possono usare un account di social network o creare un account da usare come identità per accedere all'app. 

La soluzione di esempio contiene due progetti:

**App Web di esempio (TaskWebApp):** app Web per la creazione e modifica di un elenco di attività. L'app Web usa il **criterio di iscrizione o accesso** per l'iscrizione o l'accesso degli utenti tramite un indirizzo e-mail.

**App di esempio dell'API Web (TaskService):** API Web che supporta la funzionalità di creazione, lettura, aggiornamento ed eliminazione dell'elenco di attività. L'API Web è protetta da Azure AD B2C e chiamata dall'app Web.

È necessario modificare l'app per l'uso della registrazione dell'app nel tenant. È anche necessario configurare i criteri creati. L'app Web di esempio definisce i valori di configurazione come impostazioni dell'app nel file Web.config. Per modificare le impostazioni dell'app:

1. Aprire la soluzione **B2C-WebAPI-DotNet** in Visual Studio.

2. Nel progetto **TaskWebApp** dell'app Web aprire il file **Web.config** e apportare le modifiche seguenti:

    ```C#
    <add key="ida:Tenant" value="<Your tenant name>.onmicrosoft.com" />
    
    <add key="ida:ClientId" value="The Application ID for your web app registered in your tenant" />
    
    <add key="ida:ClientSecret" value="Client password (client secret)" />
    ```
3. Aggiornare le impostazioni dei criteri con il nome generato quando sono stati creati i criteri.

    ```C#
    <add key="ida:SignUpSignInPolicyId" value="b2c_1_SiUpIn" />
    <add key="ida:EditProfilePolicyId" value="b2c_1_SiPe" />
    <add key="ida:ResetPasswordPolicyId" value="b2c_1_SSPR" />
    ```

## <a name="run-the-sample-web-app"></a>Eseguire l'app Web di esempio

In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **TaskWebApp** e scegliere **Imposta come progetto di avvio**

Premere **F5** per avviare l'app Web. Il browser predefinito si apre con l'indirizzo del sito Web locale `https://localhost:44316/`. 

L'app di esempio supporta criteri di iscrizione, accesso, modifica del profilo e reimpostazione delle password. Di seguito è illustrata l'iscrizione dell'utente per l'uso dell'app con un indirizzo e-mail. È possibile provare gli altri scenari in autonomia.

### <a name="sign-up-using-an-email-address"></a>Iscriversi usando un indirizzo di posta elettronica

1. Fare clic sul collegamento di **iscrizione/accesso** nel banner in alto per eseguire l'iscrizione come utente dell'app Web. Vengono usati i criteri **b2c_1_SiUpIn** definiti in un passaggio precedente.

2. Azure AD B2C presenta una pagina di accesso con un collegamento di iscrizione. Non avendo ancora un account, fare clic sul collegamento **Iscriversi adesso**. 

3. Il flusso di lavoro per l'iscrizione presenta una pagina per raccogliere e verificare l'identità dell'utente usando un indirizzo e-mail. Il flusso di lavoro per l'iscrizione raccoglie anche la password dell'utente e gli attributi richiesti definiti nei criteri.

    Usare un indirizzo e-mail valido ed eseguire la convalida usando un codice di verifica. Impostare una password. Immettere i valori per gli attributi richiesti. 

    ![Flusso di lavoro di iscrizione](media/active-directory-b2c-tutorials-web-app/sign-up-workflow.png)

4. Fare clic su **Crea** per creare un account locale nel tenant di Azure AD B2C.

Ora l'utente può usare il proprio indirizzo e-mail per accedere e usare l'app Web.

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile usare il tenant di Azure AD B2C se si prevede di provare altre esercitazioni relative ad Azure AD B2C. Quando non è più necessario, è possibile [eliminare il tenant di Azure AD B2C](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come creare un tenant di Azure AD B2C, creare criteri e aggiornare l'app Web di esempio per l'uso del tenant di Azure AD B2C. Proseguire con l'esercitazione successiva per informazioni su come registrare, configurare e chiamare un'API Web ASP.NET protetta dal tenant di Azure AD B2C.

> [!div class="nextstepaction"]
> [Usare Azure Active Directory B2C per proteggere un'API Web ASP.NET](active-directory-b2c-tutorials-web-api.md)
