---
title: "Esercitazione: abilitare l'autenticazione a un'app a pagina singola con account che usano Azure Active Directory B2C | Microsoft Docs"
description: Esercitazione su come usare Azure Active Directory B2C per consentire l'accesso degli utenti a un'applicazione a pagina singola (JavaScript).
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 11/30/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 0632d97c85e4baf837329a2b573f4abe5c15bf26
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55195708"
---
# <a name="tutorial-enable-single-page-app-authentication-with-accounts-using-azure-active-directory-b2c"></a>Esercitazione: Abilitare l'autenticazione a un'app a pagina singola con account che usano Azure Active Directory B2C

Questa esercitazione illustra come usare Azure Active Directory (Azure AD) B2C per l'iscrizione e l'accesso degli utenti a un'app a pagina singola (SPA). Azure AD B2C consente alle app di eseguire l'autenticazione ad account di social network, account aziendali e account di Azure Active Directory usando protocolli standard aperti.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Registrare un'applicazione a singola pagina di esempio nella directory di Azure AD B2C.
> * Creare flussi utente per iscrizione, accesso, modifica del profilo e reimpostazione delle password.
> * Configurare l'applicazione di esempio per l'uso della directory di Azure AD B2C.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

* Creare la [directory di Azure AD B2C](active-directory-b2c-get-started.md)
* Installare [Visual Studio 2017](https://www.visualstudio.com/downloads/) con il carico di lavoro **Sviluppo ASP.NET e Web**.
* [.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) o versione successiva
* Installare [Node.js](https://nodejs.org/en/download/)

## <a name="register-single-page-app"></a>Registrare l'app a pagina singola

Le applicazioni devono essere [registrate](../active-directory/develop/developer-glossary.md#application-registration) nella directory prima di poter ricevere [token di accesso](../active-directory/develop/developer-glossary.md#access-token) da Azure Active Directory. La registrazione dell'app crea un [ID applicazione](../active-directory/develop/developer-glossary.md#application-id-client-id) per l'app nella directory. 

Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale della directory di Azure AD B2C.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Selezionare **Azure AD B2C** dall'elenco di servizi nel portale di Azure. 

2. Nelle impostazioni di B2C fare clic su **Applicazioni** e quindi su **Aggiungi**. 

    Per registrare l'app Web di esempio nella directory, usare le impostazioni seguenti:
    
    ![Aggiungere una nuova app](media/active-directory-b2c-tutorials-spa/spa-registration.png)
    
    | Impostazione      | Valore consigliato  | Descrizione                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nome** | App a pagina singola di esempio | Immettere un **nome** che descriva l'app agli utenti. | 
    | **Includi app Web/API Web** | Yes | Selezionare **Sì** per un'applicazione a pagina singola. |
    | **Consenti il flusso implicito** | Yes | Selezionare **Sì** perché l'app usa l'[accesso OpenID Connect](active-directory-b2c-reference-oidc.md). |
    | **URL di risposta** | `http://localhost:6420` | Gli URL di risposta sono gli endpoint a cui Azure AD B2C restituisce eventuali token richiesti dall'app. In questa esercitazione l'app di esempio viene eseguita in locale (localhost) ed è in ascolto sulla porta 6420. |
    | **Includi client nativo** | No  | Trattandosi di un'app a pagina singola e non di un client nativo, selezionare No. |
    
3. Fare clic su **Crea** per registrare l'app.

Le app registrate vengono visualizzate nell'elenco di applicazioni per la directory di Azure AD B2C. Selezionare l'app a pagina singola dall'elenco. Verrà visualizzato il riquadro delle proprietà dell'app a pagina singola registrata.

![Proprietà dell'app a pagina singola](./media/active-directory-b2c-tutorials-spa/b2c-spa-properties.png)

Annotare l'**ID client applicazione**. L'ID identifica l'app in modo univoco ed è necessario per configurare l'app più avanti nell'esercitazione.

## <a name="create-user-flows"></a>Creare i flussi utente

Un flusso utente di Azure AD B2C definisce l'esperienza utente per un'attività di identità. Ad esempio accesso, iscrizione, modifica delle password e modifica dei profili sono flussi utente comuni.

### <a name="create-a-sign-up-or-sign-in-user-flow"></a>Creare un flusso utente di iscrizione o accesso

Per configurare gli utenti per l'iscrizione e l'accesso all'app Web, creare un **flusso utente di iscrizione o accesso**.

1. Dalla pagina del portale di Azure AD B2C selezionare **Flussi utente** e fare clic su **Nuovo flusso utente**.
2. Nella scheda **Consigliati** fare clic su **Iscrizione e accesso**.

    Per configurare il flusso utente, usare le impostazioni seguenti:

    ![Aggiungere un flusso utente di iscrizione o accesso](media/active-directory-b2c-tutorials-spa/add-susi-user-flow.png)

    | Impostazione      | Valore consigliato  | Descrizione                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nome** | SiUpIn | Immettere un **nome** per il flusso utente. Il nome del flusso utente ha il prefisso **B2C_1_**. Usare il nome completo **B2C_1_SiUpIn** del flusso utente nel codice di esempio. | 
    | **Provider di identità** | Iscrizione tramite posta elettronica | Provider di identità usato per identificare l'utente in modo univoco. |

3. In **Attributi e attestazioni utente** fare clic su **Mostra più** e selezionare le impostazioni seguenti:

    ![Aggiungere un flusso utente di iscrizione o accesso](media/active-directory-b2c-tutorials-spa/add-attributes-and-claims.png)

    | Colonna      | Valore consigliato  | DESCRIZIONE                                        |
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
    | **Nome** | SiPe | Immettere un **nome** per il flusso utente. Il nome del flusso utente ha il prefisso **B2C_1_**. Usare il nome completo **B2C_1_SiPe** del flusso utente nel codice di esempio. | 
    | **Provider di identità** | Accesso all'account locale | Provider di identità usato per identificare l'utente in modo univoco. |

3.  In **Attributi utente** fare clic su **Mostra più** e selezionare le impostazioni seguenti:

    | Colonna      | Valore consigliato  | DESCRIZIONE                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Raccogli l'attributo** | Nome visualizzato e Codice postale | Selezionare gli attributi che un utente può modificare durante la modifica del profilo. |
    | **Restituisci l'attestazione** | Nome visualizzato, Codice postale, ID oggetto dell'utente | Selezionare le [attestazioni](../active-directory/develop/developer-glossary.md#claim) da includere nel [token di accesso](../active-directory/develop/developer-glossary.md#access-token) dopo aver completato la modifica del profilo. |

4. Fare clic su **OK**.
5. Fare clic su **Crea** per creare il flusso utente. 

### <a name="create-a-password-reset-user-flow"></a>Creare un flusso utente di reimpostazione delle password

Per abilitare la reimpostazione delle password nell'applicazione, è necessario creare un **flusso utente di reimpostazione delle password**. Questo flusso utente descrive l'esperienza utente durante la procedura di reimpostazione delle password e il contenuto dei token che l'applicazione riceverà al completamento della procedura.

1. Dalla pagina del portale di Azure AD B2C selezionare **Flussi utente** e fare clic su **Nuovo flusso utente**.
2. Nella scheda **Consigliati** fare clic su **Reimpostazione password**.

    Per configurare il flusso utente, usare le impostazioni seguenti.

    | Impostazione      | Valore consigliato  | Descrizione                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nome** | SSPR | Immettere un **nome** per il flusso utente. Il nome del flusso utente ha il prefisso **B2C_1_**. Usare il nome completo **B2C_1_SSPR** del flusso utente nel codice di esempio. | 
    | **Provider di identità** | Reimpostare la password usando l'indirizzo e-mail | Provider di identità usato per identificare l'utente in modo univoco. |

3. In **Attestazioni dell'applicazione** fare clic su **Mostra più** e selezionare l'impostazione seguente:

    | Colonna      | Valore consigliato  | DESCRIZIONE                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Restituisci l'attestazione** | ID oggetto dell'utente | Selezionare le [attestazioni](../active-directory/develop/developer-glossary.md#claim) da includere nel [token di accesso](../active-directory/develop/developer-glossary.md#access-token) dopo aver completato la reimpostazione delle password. |

4. Fare clic su **OK**.
5. Fare clic su **Crea** per creare il flusso utente. 

## <a name="update-single-page-app-code"></a>Aggiornare il codice dell'app a pagina singola

Ora che l'app è registrata e sono stati creati i flussi utente, è necessario configurare l'app per l'uso della directory di Azure AD B2C. In questa esercitazione si configura un'app JavaScript a pagina singola di esempio che è possibile scaricare da GitHub. 

[Scaricare un file ZIP](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) o clonare l'app Web di esempio da GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```
L'app di esempio dimostra come un'app a pagina singola può usare Azure Active Directory B2C per l'iscrizione e l'accesso degli utenti e per chiamare un'API Web protetta. È necessario modificare l'app per l'uso della registrazione dell'app nella directory e la configurazione dei flussi utente creati. 

Per modificare le impostazioni dell'app:

1. Aprire il file `index.html` nell'esempio di app a pagina singola Node.js.
2. Configurare l'esempio con le informazioni di registrazione della directory di Azure AD B2C. Modificare le righe di codice seguenti. Assicurarsi di sostituire i valori con i nomi della directory e delle API:

    ```javascript
    // The current application coordinates were pre-registered in a B2C directory.
    var applicationConfig = {
        clientID: '<Application ID for your SPA obtained from portal app registration>',
        authority: "https://fabrikamb2c.b2clogin.com/tfp/fabrikamb2c.onmicrosoft.com/B2C_1_<Sign-up or sign-in policy name>",
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/demoapi/demo.read"],
        webApi: 'https://fabrikamb2chello.azurewebsites.net/hello',
    };
    ```

    Il nome di flusso utente usato in questa esercitazione è **B2C_1_SiUpIn**. Se si usa un nome di flusso utente diverso, inserire il nome usato nel valore `authority`.

## <a name="run-the-sample"></a>Eseguire l'esempio

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

L'app di esempio supporta criteri di iscrizione, accesso, modifica del profilo e reimpostazione delle password. Questa esercitazione illustra l'iscrizione dell'utente per l'uso dell'app con un indirizzo e-mail. È possibile esplorare gli altri scenari in autonomia.

### <a name="sign-up-using-an-email-address"></a>Iscriversi usando un indirizzo di posta elettronica

1. Fare clic su **Accedi** per iscriversi come utente dell'app a pagina singola. Verrà usato il flusso utente **B2C_1_SiUpIn** definito in precedenza.

2. Azure AD B2C presenta una pagina di accesso con un collegamento di iscrizione. Non avendo ancora un account, fare clic sul collegamento **Iscriversi adesso**. 

3. Il flusso di lavoro per l'iscrizione presenta una pagina per raccogliere e verificare l'identità dell'utente usando un indirizzo e-mail. Il flusso di lavoro per l'iscrizione raccoglie anche la password dell'utente e gli attributi richiesti definiti nel flusso utente.

    Usare un indirizzo e-mail valido ed eseguire la convalida usando un codice di verifica. Impostare una password. Immettere i valori per gli attributi richiesti. 

    ![Flusso di lavoro di iscrizione](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.png)

4. Fare clic su **Crea** per creare un account locale nella directory di Azure AD B2C.

Ora l'utente può usare il proprio indirizzo e-mail per accedere e usare l'app a pagina singola.

> [!NOTE]
> Dopo l'accesso, l'app visualizza l'errore "Autorizzazioni insufficienti". L'errore viene visualizzato perché si sta tentando di accedere a una risorsa dalla directory dimostrativa. Poiché il token di accesso è valido solo per la directory di Azure AD, la chiamata API non è autorizzata. Continuare con l'esercitazione successiva per la creazione di un'API Web protetta per la directory. 

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile usare la directory di Azure AD B2C se si prevede di provare altre esercitazioni relative ad Azure AD B2C. Quando non è più necessario, è possibile [eliminare la directory di Azure AD B2C](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come creare una directory di Azure AD B2C, creare flussi utente e aggiornare l'app a singola pagina di esempio per l'uso della directory di Azure AD B2C. Proseguire con l'esercitazione successiva per informazioni su come registrare, configurare e chiamare un'API Web protetta da un'app desktop.

> [!div class="nextstepaction"]
> [Esempi di codice per Azure AD B2C](https://azure.microsoft.com/resources/samples/?service=active-directory-b2c&sort=0)
