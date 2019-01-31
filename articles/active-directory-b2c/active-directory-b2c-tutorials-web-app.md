---
title: "Esercitazione: abilitare un'applicazione Web per l'autenticazione con account che usano Azure Active Directory B2C | Microsoft Docs"
description: Esercitazione su come usare Azure Active Directory B2C per consentire l'accesso degli utenti a un'applicazione Web ASP.NET.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 11/30/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 714d733e765f28d1244f6ee1c7b1cb237c0c4b1f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55198351"
---
# <a name="tutorial-enable-a-web-application-to-authenticate-with-accounts-using-azure-active-directory-b2c"></a>Esercitazione: abilitare un'applicazione Web per l'autenticazione con account che usano Azure Active Directory B2C

Questa esercitazione illustra come usare Azure Active Directory (Azure AD) B2C per l'iscrizione e l'accesso degli utenti a un'app Web ASP.NET. Azure AD B2C consente alle app di eseguire l'autenticazione ad account di social network, account aziendali e account di Azure Active Directory usando protocolli standard aperti.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Registrare un'app Web ASP.NET di esempio nel tenant di Azure AD B2C.
> * Creare flussi utente per iscrizione, accesso, modifica del profilo e reimpostazione delle password.
> * Configurare l'app Web di esempio per l'uso del tenant di Azure AD B2C. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

* Creare il proprio [tenant di Azure AD B2C](active-directory-b2c-get-started.md)
* Installare [Visual Studio 2017](https://www.visualstudio.com/downloads/) con il carico di lavoro **Sviluppo ASP.NET e Web**.

## <a name="register-web-app"></a>Registrare l'app Web

Le applicazioni devono essere [registrate](../active-directory/develop/developer-glossary.md#application-registration) nel tenant perché possano ricevere [token di accesso](../active-directory/develop/developer-glossary.md#access-token) da Azure Active Directory. La registrazione dell'app crea un [ID applicazione](../active-directory/develop/developer-glossary.md#application-id-client-id) per l'app nel tenant. 

Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di Azure AD B2C.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo. A questo punto, dovrebbe essere in uso il tenant creato nell'esercitazione precedente. 

2. Nelle impostazioni di B2C fare clic su **Applicazioni** e quindi su **Aggiungi**. 

    Per registrare l'app Web di esempio nel tenant, usare le impostazioni seguenti:

    ![Aggiungere una nuova app](media/active-directory-b2c-tutorials-web-app/web-app-registration.png)
    
    | Impostazione      | Valore consigliato  | Descrizione                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nome** | App Web di esempio | Immettere un **nome** che descriva l'app agli utenti. | 
    | **Includi app Web/API Web** | Yes | Selezionare **Sì** per un'app Web. |
    | **Consenti il flusso implicito** | Yes | Selezionare **Sì** perché l'app usa l'[accesso OpenID Connect](active-directory-b2c-reference-oidc.md). |
    | **URL di risposta** | `https://localhost:44316` | Gli URL di risposta sono gli endpoint a cui Azure AD B2C restituisce eventuali token richiesti dall'app. In questa esercitazione, l'app di esempio viene eseguita in locale (localhost) ed è in ascolto sulla porta 44316. |
    | **Includi client nativo** | No  | Trattandosi di un'app Web e non un client nativo, selezionare No. |
    
3. Fare clic su **Crea** per registrare l'app.

Le app registrate vengono visualizzate nell'elenco di applicazioni per il tenant di Azure AD B2C. Selezionare l'app Web dall'elenco. Viene visualizzato il riquadro delle proprietà dell'app Web.

![Proprietà dell'app Web](./media/active-directory-b2c-tutorials-web-app/b2c-web-app-properties.png)

Annotare l'**ID applicazione**. L'ID identifica l'app in modo univoco ed è necessario per configurare l'app più avanti nell'esercitazione.

### <a name="create-a-client-password"></a>Creare una password client

Azure AD B2C usa l'autorizzazione OAuth2 per le [applicazioni client](../active-directory/develop/developer-glossary.md#client-application). Le app Web sono [client riservati](../active-directory/develop/developer-glossary.md#web-client) e richiedono un ID client o un ID applicazione e un segreto client, una password client o una chiave applicazione.

1. Selezionare la pagina Chiavi per l'app Web registrata e fare clic su **Genera chiave**.

2. Fare clic su **Salva** per visualizzare la chiave dell'app.

    ![Pagina Chiavi generale dell'app](media/active-directory-b2c-tutorials-web-app/app-general-keys-page.png)

La chiave viene visualizzata una sola volta nel portale. È importante copiare e salvare il valore della chiave perché servirà per la configurazione dell'app. Tenere la chiave riservata. Non condividerla pubblicamente.

## <a name="create-user-flows"></a>Creare i flussi utente

Un flusso utente di Azure AD B2C definisce l'esperienza utente per un'attività di identità. Ad esempio accesso, iscrizione, modifica delle password e modifica dei profili sono flussi utente comuni.

### <a name="create-a-sign-up-or-sign-in-user-flow"></a>Creare un flusso utente di iscrizione o accesso

Per configurare gli utenti per l'iscrizione e l'accesso all'app Web, creare un **flusso utente di iscrizione o accesso**.

1. Dalla pagina del portale di Azure AD B2C selezionare **Flussi utente** e fare clic su **Nuovo flusso utente**.
2. Nella scheda **Consigliati** fare clic su **Iscrizione e accesso**.

    Per configurare il flusso utente, usare le impostazioni seguenti:

    ![Aggiungere un flusso utente di iscrizione o accesso](media/active-directory-b2c-tutorials-web-app/add-susi-user-flow.png)

    | Impostazione      | Valore consigliato  | Descrizione                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nome** | SiUpIn | Immettere un **nome** per il flusso utente. Il nome del flusso utente ha il prefisso **b2c_1_**. Usare il nome completo **b2c_1_SiUpIn** del flusso utente nel codice di esempio. | 
    | **Provider di identità** | Iscrizione tramite posta elettronica | Provider di identità usato per identificare l'utente in modo univoco. |

3. In **Attributi e attestazioni utente** fare clic su **Mostra più** e selezionare le impostazioni seguenti:

    ![Aggiungere un flusso utente di iscrizione o accesso](media/active-directory-b2c-tutorials-web-app/add-attributes-and-claims.png)

    | Colonna      | Valori suggeriti  | DESCRIZIONE                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Raccogli l'attributo** | Nome visualizzato e Codice postale | Selezionare gli attributi che devono essere raccolti dall'utente durante l'iscrizione. |
    | **Restituisci l'attestazione** | Nome visualizzato, Codice postale, Nuovo utente, ID oggetto dell'utente | Selezionare le [attestazioni](../active-directory/develop/developer-glossary.md#claim) da includere nel [token di accesso](../active-directory/develop/developer-glossary.md#access-token). |

4. Fare clic su **OK**.
5. Fare clic su **Crea** per creare il flusso utente. 

### <a name="create-a-profile-editing-user-flow"></a>Creare un flusso utente di modifica del profilo

Per consentire agli utenti di reimpostare autonomamente le informazioni del profilo, creare un **flusso utente di modifica del profilo**.

1. Dalla pagina del portale di Azure AD B2C selezionare **Flussi utente** e fare clic su **Nuovo flusso utente**.
2. Nella scheda **Consigliati** fare clic su **Modifica del profilo**.

    Per configurare il flusso utente, usare le impostazioni seguenti:

    | Impostazione      | Valore consigliato  | Descrizione                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nome** | SiPe | Immettere un **nome** per il flusso utente. Il nome del flusso utente ha il prefisso **b2c_1_**. Usare il nome completo **b2c_1_SiPe** del flusso utente nel codice di esempio. | 
    | **Provider di identità** | Accesso all'account locale | Provider di identità usato per identificare l'utente in modo univoco. |

3. In **Attributi utente** fare clic su **Mostra più** e selezionare le impostazioni seguenti:

    | Colonna      | Valori suggeriti  | DESCRIZIONE                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Raccogli l'attributo** | Nome visualizzato e Codice postale | Selezionare gli attributi che un utente può modificare durante la modifica del profilo. |
    | **Restituisci l'attestazione** | Nome visualizzato, Codice postale, ID oggetto dell'utente | Selezionare le [attestazioni](../active-directory/develop/developer-glossary.md#claim) da includere nel [token di accesso](../active-directory/develop/developer-glossary.md#access-token) dopo aver completato la modifica del profilo. |

4. Fare clic su **OK**.
5. Fare clic su **Crea** per creare il flusso utente. 

### <a name="create-a-password-reset-user-flow"></a>Creare un flusso utente di reimpostazione delle password

Per abilitare la reimpostazione delle password nell'applicazione, è necessario creare un **flusso utente di reimpostazione delle password**. Questo flusso utente descrive l'esperienza utente durante la procedura di reimpostazione delle password e il contenuto dei token che l'applicazione riceverà al completamento della procedura.

1. Dalla pagina del portale di Azure AD B2C selezionare **Criteri di reimpostazione password** e fare clic su **Aggiungi**.
2. Nella scheda **Consigliati** fare clic su **Reimpostazione password**.

    Per configurare il flusso utente, usare le impostazioni seguenti.

    | Impostazione      | Valore consigliato  | Descrizione                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nome** | SSPR | Immettere un **nome** per il flusso utente. Il nome del flusso utente ha il prefisso **b2c_1_**. Usare il nome completo **b2c_1_SSPR** del flusso utente nel codice di esempio. | 
    | **Provider di identità** | Reimpostare la password usando l'indirizzo e-mail | Provider di identità usato per identificare l'utente in modo univoco. |

3. In **Attestazioni dell'applicazione** fare clic su **Mostra più** e selezionare l'impostazione seguente:
    | Colonna      | Valore consigliato  | DESCRIZIONE                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Restituisci l'attestazione** | ID oggetto dell'utente | Selezionare le [attestazioni](../active-directory/develop/developer-glossary.md#claim) da includere nel [token di accesso](../active-directory/develop/developer-glossary.md#access-token) dopo aver completato la reimpostazione delle password. |

4. Fare clic su **OK**.
5. Fare clic su **Crea** per creare il flusso utente. 

## <a name="update-web-app-code"></a>Aggiornare il codice dell'app Web

Ora che l'app Web è registrata e sono stati creati i flussi utente, è necessario configurare l'app per l'uso del tenant di Azure AD B2C. In questa esercitazione si configura un'app Web di esempio che è possibile scaricare da GitHub. 

[Scaricare un file ZIP](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip) o clonare l'app Web di esempio da GitHub. Assicurarsi di estrarre il file di esempio in una cartella il cui percorso sia costituito da un numero totale di caratteri minore di 260.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

L'app Web ASP.NET di esempio è una semplice app per la creazione e l'aggiornamento di un elenco attività. L'app usa [componenti middleware di Microsoft OWIN](https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/) per consentire agli utenti di effettuare l'iscrizione per usare l'app nel tenant di Azure AD B2C. Creando un flusso utente di Azure AD B2C, gli utenti possono usare un account di social network o creare un account da usare come identità per accedere all'app. 

La soluzione di esempio contiene due progetti:

**App Web di esempio (TaskWebApp):** app Web per la creazione e modifica di un elenco di attività. L'app Web usa il flusso utente di **iscrizione o accesso** per l'iscrizione o l'accesso degli utenti.

**App di esempio dell'API Web (TaskService):** API Web che supporta le funzionalità di creazione, lettura, aggiornamento ed eliminazione dell'elenco attività. L'API Web è protetta da Azure AD B2C e chiamata dall'app Web.

È necessario modificare l'app per l'uso della registrazione dell'app nel tenant, che include l'ID applicazione e la chiave registrati in precedenza. È anche necessario configurare i flussi utente creati. L'app Web di esempio definisce i valori di configurazione come impostazioni dell'app nel file Web.config. Per modificare le impostazioni dell'app:

1. Aprire la soluzione **B2C-WebAPI-DotNet** in Visual Studio.

2. Nel progetto di app Web **TaskWebApp** aprire il file **Web.config**. Sostituire il valore di `ida:Tenant` con il nome del tenant creato. Sostituire il valore di `ida:ClientId` con l'ID applicazione registrato. Sostituire il valore di `ida:ClientSecret` con la chiave registrata.

3. Nel file **Web.config** sostituire il valore di `ida:SignUpSignInPolicyId` con `b2c_1_SiUpIn`. Sostituire il valore di `ida:EditProfilePolicyId` con `b2c_1_SiPe`. Sostituire il valore di `ida:ResetPasswordPolicyId` con `b2c_1_SSPR`.

## <a name="run-the-sample-web-app"></a>Eseguire l'app Web di esempio

In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **TaskWebApp** e scegliere **Imposta come progetto di avvio**

Premere **F5** per avviare l'app Web. Il browser predefinito si apre con l'indirizzo del sito Web locale `https://localhost:44316/`. 

L'app di esempio supporta criteri di iscrizione, accesso, modifica del profilo e reimpostazione delle password. Questa esercitazione illustra l'iscrizione dell'utente per l'uso dell'app con un indirizzo e-mail. È possibile esplorare gli altri scenari in autonomia.

### <a name="sign-up-using-an-email-address"></a>Iscriversi usando un indirizzo di posta elettronica

1. Fare clic sul collegamento di **iscrizione/accesso** nel banner in alto per eseguire l'iscrizione come utente dell'app Web. Verrà usato il flusso utente **b2c_1_SiUpIn** definito in precedenza.

2. Azure AD B2C presenta una pagina di accesso con un collegamento di iscrizione. Non avendo ancora un account, fare clic sul collegamento **Iscriversi adesso**. 

3. Il flusso di lavoro per l'iscrizione presenta una pagina per raccogliere e verificare l'identità dell'utente usando un indirizzo e-mail. Il flusso di lavoro per l'iscrizione raccoglie anche la password dell'utente e gli attributi richiesti definiti nel flusso utente.

    Usare un indirizzo e-mail valido ed eseguire la convalida usando un codice di verifica. Impostare una password. Immettere i valori per gli attributi richiesti. 

    ![Flusso di lavoro di iscrizione](media/active-directory-b2c-tutorials-web-app/sign-up-workflow.png)

4. Fare clic su **Crea** per creare un account locale nel tenant di Azure AD B2C.

Ora l'utente può usare il proprio indirizzo e-mail per accedere e usare l'app Web.

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile usare il tenant di Azure AD B2C se si prevede di provare altre esercitazioni relative ad Azure AD B2C. Quando non è più necessario, è possibile [eliminare il tenant di Azure AD B2C](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come creare un tenant di Azure AD B2C, creare flussi utente e aggiornare l'app Web di esempio per l'uso del tenant di Azure AD B2C. Proseguire con l'esercitazione successiva per informazioni su come registrare, configurare e chiamare un'API Web ASP.NET protetta dal tenant di Azure AD B2C.

> [!div class="nextstepaction"]
> [Esercitazione: Usare Azure Active Directory B2C per proteggere un'API Web ASP.NET](active-directory-b2c-tutorials-web-api.md)
