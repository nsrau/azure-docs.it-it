---
title: "Guida introduttiva: Configurare l'accesso per un'app a pagina singola"
titleSuffix: Azure AD B2C
description: In questo Avvio rapido eseguire un'applicazione a singola pagina di esempio che usa Azure Active Directory B2C per consentire l'accesso all'account.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.date: 09/12/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ef6fd46ef51cff9823c93e2297a738f95494f577
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74948335"
---
# <a name="quickstart-set-up-sign-in-for-a-single-page-app-using-azure-active-directory-b2c"></a>Guida introduttiva: Configurare l'accesso per un'app a singola pagina tramite Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) consente la gestione delle identità del cloud per garantire la protezione costante dell'applicazione, delle attività aziendali e dei clienti. Azure AD B2C consente alle applicazioni di eseguire l'autenticazione per account di social networking e account aziendali usando protocolli standard aperti. In questa guida introduttiva si usa un'applicazione a pagina singola per eseguire l'accesso con un provider di identità basato su social network e chiamare un'API Web protetta da Azure AD B2C.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) con il carico di lavoro **Sviluppo ASP.NET e Web**
- [Node.js](https://nodejs.org/en/download/)
- Un account di social networking di Facebook, Google o Microsoft
- Esempio di codice di GitHub: [active-directory-b2c-javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)

    È possibile [scaricare l'archivio ZIP](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) o clonare il repository:

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
    ```

## <a name="run-the-application"></a>Eseguire l'applicazione

1. Avviare il server eseguendo questi comandi al prompt dei comandi di Node.js:

    ```
    cd active-directory-b2c-javascript-msal-singlepageapp
    npm install && npm update
    node server.js
    ```

    Server.js restituisce come output il numero di porta su cui è in ascolto in localhost.

    ```
    Listening on port 6420...
    ```

2. Passare all'URL dell'applicazione. Ad esempio: `http://localhost:6420`.

## <a name="sign-in-using-your-account"></a>Eseguire l'accesso con il proprio account

1. Fare clic su **Login** (Accedi) per avviare il flusso di lavoro.

    ![App di esempio dell'applicazione a pagina singola visualizzata nel browser](media/active-directory-b2c-quickstarts-spa/sample-app-spa.png)

    L'esempio supporta alcune opzioni di iscrizione, tra cui l'uso di un provider di identità di social networking o la creazione di un account locale tramite un indirizzo di posta elettronica. Per questo argomento di avvio rapido, usare un account di provider di identità basato su social network, ovvero un account di Facebook, Google o Microsoft.

2. Azure AD B2C presenta una pagina di accesso per un'azienda fittizia denominata Fabrikam per l'applicazione Web di esempio. Per iscriversi usando un provider di identità basato su social network, fare clic sul pulsante del provider di identità che si vuole usare.

    ![Pagina di accesso o di iscrizione che mostra i pulsanti del provider di identità](media/active-directory-b2c-quickstarts-spa/sign-in-or-sign-up-spa.png)

    È necessario eseguire l'autenticazione (accesso) tramite le credenziali dell'account di social networking e autorizzare l'applicazione a leggere informazioni da questo account. Dopo la concessione dell'accesso, l'applicazione può recuperare le informazioni sul profilo dall'account, ad esempio il nome e la città dell'utente.

3. Completare il processo di accesso per il provider di identità.

## <a name="access-a-protected-api-resource"></a>Accedere a una risorsa API protetta

Fare clic su **Call Web API** (Chiama API Web) affinché la chiamata all'API Web restituisca il nome visualizzato come oggetto JSON.

![Applicazione di esempio nel browser che mostra la risposta dell'API Web](media/active-directory-b2c-quickstarts-spa/call-api-spa.png)

L'applicazione a pagina singola include un token di accesso nella richiesta alla risorsa API Web protetta.

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile usare il tenant di Azure AD B2C se si prevede di provare altre guide introduttive o esercitazioni relative ad Azure AD B2C. Quando non è più necessario, è possibile [eliminare il tenant di Azure AD B2C](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stata usata un'applicazione a pagina singola di esempio per:

* Accedere con una pagina di accesso personalizzata
* Accedere con un provider di identità basato su social network
* Creare un account Azure AD B2C
* Chiamare un'API Web protetta da Azure AD B2C

Iniziare ora a creare un tenant di Azure AD B2C.

> [!div class="nextstepaction"]
> [Creare un tenant di Azure Active Directory B2C nel portale di Azure](tutorial-create-tenant.md)
