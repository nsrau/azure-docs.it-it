---
title: "Esercitazione: Abilitare l'autenticazione in un'applicazione a pagina singola - Azure Active Directory B2C | Microsoft Docs"
description: Esercitazione su come usare Azure Active Directory B2C per consentire l'accesso degli utenti a un'applicazione a pagina singola (JavaScript).
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 6e4be3a14a6cfba6b32bea8a77975e3e34ea012d
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66507820"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-using-azure-active-directory-b2c"></a>Esercitazione: Abilitare l'autenticazione in un'applicazione a pagina singola con Azure Active Directory B2C

Questa esercitazione illustra come usare Azure Active Directory (Azure AD) B2C per l'iscrizione e l'accesso degli utenti in un'applicazione a pagina singola. Azure AD B2C consente alle applicazioni di eseguire l'autenticazione ad account di social network, aziendali e di Azure Active Directory usando protocolli standard aperti.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Aggiornare l'applicazione in Azure AD B2C
> * Configurare l'esempio per l'uso dell'applicazione
> * Iscriversi usando il flusso utente

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

* [Creare flussi utente](tutorial-create-user-flows.md) per abilitare le esperienze utente nell'applicazione. 
* Installare [Visual Studio 2019](https://www.visualstudio.com/downloads/) con il carico di lavoro **Sviluppo ASP.NET e Web**.
* Installare [.NET Core SDK 2.0.0](https://www.microsoft.com/net/core) o versione successiva.
* Installare [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>Aggiornare l'applicazione

Nell'esercitazione completata come parte dei prerequisiti è stato aggiunta un'applicazione Web in Azure AD B2C. Per consentire la comunicazione con l'esempio nell'esercitazione, è necessario aggiungere un URI di reindirizzamento all'applicazione in Azure AD B2C.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul **filtro delle directory e delle sottoscrizioni** nel menu in alto e scegliere la directory che contiene il tenant.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
4. Selezionare **Applicazioni** e quindi l'applicazione *webapp1*.
5. In **URL di risposta** aggiungere `http://localhost:6420`.
6. Selezionare **Salva**.
7. Nella pagina delle proprietà prendere nota dell'ID applicazione, che verrà usato durante la configurazione dell'applicazione Web.
8. Selezionare **Chiavi**, **Genera chiave** e quindi **Salva**. Prendere nota della chiave, che verrà usata durante la configurazione dell'applicazione Web.

## <a name="configure-the-sample"></a>Configurare l'esempio

In questa esercitazione si configura un esempio che è possibile scaricare da GitHub. L'esempio dimostra come un'applicazione a pagina singola può usare Azure AD B2C per l'iscrizione e l'accesso degli utenti e per chiamare un'API Web protetta.

[Scaricare un file ZIP](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) o clonare l'esempio da GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

Per modificare le impostazioni:

1. Aprire il file `index.html` dell'esempio.
2. Configurare l'esempio con l'ID applicazione e la chiave annotati in precedenza. Modificare le righe di codice seguenti sostituendo i valori con i nomi della directory e delle API:

    ```javascript
    // The current application coordinates were pre-registered in a B2C directory.
    var applicationConfig = {
        clientID: '<Application ID>',
        authority: "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_signupsignin1",
        b2cScopes: ["https://contoso.onmicrosoft.com/demoapi/demo.read"],
        webApi: 'https://contosohello.azurewebsites.net/hello',
    };
    ```

    Il nome del flusso utente usato in questa esercitazione è **B2C_1_signupsignin1**. Se si usa un nome di flusso utente diverso, inserire il nome usato nel valore di `authority`.

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

4. Usare un browser per passare all'indirizzo `http://localhost:6420` e visualizzare l'applicazione.

L'esempio supporta l'iscrizione, l'accesso, la modifica del profilo e la reimpostazione della password. Questa esercitazione illustra l'iscrizione di un utente con un indirizzo di posta elettronica.

### <a name="sign-up-using-an-email-address"></a>Iscriversi usando un indirizzo di posta elettronica

1. Fare clic su **Login** per iscriversi come utente dell'applicazione. Verrà usato il flusso utente **B2C_1_signupsignin1** definito in un passaggio precedente.
2. Azure AD B2C presenta una pagina di accesso con un collegamento di iscrizione. Non avendo ancora un account, fare clic sul collegamento **Iscriversi adesso**. 
3. Il flusso di lavoro per l'iscrizione presenta una pagina per raccogliere e verificare l'identità dell'utente usando un indirizzo e-mail. Il flusso di lavoro per l'iscrizione raccoglie anche la password dell'utente e gli attributi richiesti definiti nel flusso utente.

    Usare un indirizzo e-mail valido ed eseguire la convalida usando un codice di verifica. Impostare una password. Immettere i valori per gli attributi richiesti. 

    ![Flusso di lavoro di iscrizione](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.png)

4. Fare clic su **Crea** per creare un account locale nella directory di Azure AD B2C.

Ora l'utente può usare un indirizzo di posta elettronica per accedere e usare l'app a pagina singola.

> [!NOTE]
> Dopo l'accesso, l'app visualizza l'errore "Autorizzazioni insufficienti". L'errore viene visualizzato perché si sta tentando di accedere a una risorsa dalla directory dimostrativa. Poiché il token di accesso è valido solo per la directory di Azure AD, la chiamata API non è autorizzata. Continuare con l'esercitazione successiva per la creazione di un'API Web protetta per la directory.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come:

> [!div class="checklist"]
> * Aggiornare l'applicazione in Azure AD B2C
> * Configurare l'esempio per l'uso dell'applicazione
> * Iscriversi usando il flusso utente

> [!div class="nextstepaction"]
> [Esercitazione: Concedere l'accesso a un'API Web ASP.NET Core da un'app a pagina singola con Azure Active Directory B2C](active-directory-b2c-tutorials-spa-webapi.md)
