---
title: "Esercitazione: Concedere l'accesso a un'API Web Node.js da un'applicazione desktop - Azure Active Directory B2C | Microsoft Docs"
description: Esercitazione su come usare Active Directory B2C per proteggere un'API Web Node.js e chiamarla da un'app desktop .NET.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 75469d4522cea2914e0f69d5aa1850e468cb0d50
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064848"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>Esercitazione: concedere l'accesso a un'API Web Node.js da un'app desktop usando Azure Active Directory B2C

Questa esercitazione illustra come chiamare una risorsa API Web Node.js protetta da Azure Active Directory B2C (Azure AD B2C) da un'app desktop Windows Presentation Foundation (WPF).

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Aggiungere un'applicazione API Web
> * Configurare gli ambiti per un'API Web
> * Concedere le autorizzazioni per l'API Web
> * Aggiornare l'esempio per l'uso dell'applicazione

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Completare i passaggi e i prerequisiti riportati in [Esercitazione: Abilitare l'autenticazione in un'app desktop con account usando Azure Active Directory B2C](active-directory-b2c-tutorials-desktop-app.md).

## <a name="add-a-web-api-application"></a>Aggiungere un'applicazione API Web

Per poter accettare e rispondere a richieste di risorse protette da parte di applicazioni client che presentano un token di accesso, le risorse API Web devono prima essere registrate nel tenant.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant.
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

Per chiamare un'API Web protetta da un'applicazione, è necessario concedere all'applicazione le autorizzazioni per l'API. Nell'esercitazione preliminare è stata creata un'applicazione Web denominata *app1* in Azure AD B2C. Questa applicazione verrà usata per chiamare l'API Web.

1. Selezionare **Applicazioni** e quindi *nativeapp1*.
2. Selezionare **Accesso all'API** e quindi **Aggiungi**.
3. Nell'elenco a discesa **Seleziona API** selezionare *webapi1*.
4. Nell'elenco a discesa **Seleziona ambiti** selezionare gli ambiti **Hello.Read** e **Hello.Write** definiti in precedenza.
5. Fare clic su **OK**.

Gli utenti eseguono l'autenticazione con Azure AD B2C per usare l'applicazione desktop WPF. L'applicazione desktop ottiene una concessione di autorizzazione da Azure AD B2C per l'accesso all'API Web protetta.

## <a name="configure-the-sample"></a>Configurare l'esempio

Dopo la registrazione dell'API Web e la definizione degli ambiti, configurare il codice dell'API Web per l'uso del tenant di Azure AD B2C. In questa esercitazione si configura un'applicazione Web Node.js di esempio che è possibile scaricare da GitHub.

[Scaricare un file ZIP](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) o clonare l'app Web di esempio da GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```
L'API Web Node.js di esempio usa la libreria Passport.js per consentire ad Azure Active Directory B2C di proteggere le chiamate all'API.

1. Aprire il file `index.js` .
2. Configurare l'esempio con le informazioni di registrazione tenant di Azure Active Directory B2C. Modificare le righe di codice seguenti:

    ```nodejs
    var tenantID = "<your-tenant-name>.onmicrosoft.com";
    var clientID = "<application-ID>";
    var policyName = "B2C_1_signupsignin1";
    ```

## <a name="run-the-sample"></a>Eseguire l'esempio

1. Avviare un prompt dei comandi di Node.js.
2. Passare alla directory contenente l'esempio di Node.js. Ad esempio `cd c:\active-directory-b2c-javascript-nodejs-webapi`
3. Eseguire i comandi seguenti:
    ```
    npm install && npm update
    ```
    ```
    node index.js
    ```

### <a name="run-the-desktop-application"></a>Eseguire l'applicazione desktop

1. Aprire la soluzione **active-directory-b2c-wpf** in Visual Studio.
2. Premere **F5** per eseguire l'app desktop.
3. Accedere usando l'indirizzo e-mail e la password usati nell'esercitazione sull'[autenticazione degli utenti con Azure Active Directory B2C in un'applicazione desktop](active-directory-b2c-tutorials-desktop-app.md).
4. Fare clic sul pulsante **Call API** (Chiama API).

L'applicazione desktop effettua una richiesta all'API Web e ottiene una risposta con il nome visualizzato dell'utente connesso. L'applicazione desktop protetta chiama l'API Web protetta nel tenant di Azure AD B2C.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Aggiungere un'applicazione API Web
> * Configurare gli ambiti per un'API Web
> * Concedere le autorizzazioni per l'API Web
> * Aggiornare l'esempio per l'uso dell'applicazione

> [!div class="nextstepaction"]
> [Esercitazione: Aggiungere provider di identità alle applicazioni in Azure Active Directory B2C](tutorial-add-identity-providers.md)
