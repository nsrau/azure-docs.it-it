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
ms.openlocfilehash: 8c4b2d818549da07faf9ecd28f61b4ed5315f745
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679331"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>Esercitazione: concedere l'accesso a un'API Web Node.js da un'app desktop usando Azure Active Directory B2C

Questa esercitazione illustra come chiamare una risorsa API Web Node.js protetta da Azure Active Directory B2C (Azure AD B2C) da un'app desktop Windows Presentation Foundation (WPF).

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Aggiungere un'applicazione API Web
> * Configurare gli ambiti per un'API Web
> * Concedere le autorizzazioni per l'API Web
> * Aggiornare l'esempio per l'uso dell'applicazione

## <a name="prerequisites"></a>Prerequisiti

Completare i passaggi e i prerequisiti riportati in [Esercitazione: Abilitare l'autenticazione in un'app desktop con account usando Azure Active Directory B2C](active-directory-b2c-tutorials-desktop-app.md).

## <a name="add-a-web-api-application"></a>Aggiungere un'applicazione API Web

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Configurare gli ambiti

Gli ambiti consentono di regolamentare l'accesso alle risorse protette. Vengono usati dall'API Web per implementare il controllo degli accessi in base all'ambito. Alcuni utenti possono avere ad esempio accesso sia in lettura che in scrittura, mentre altri possono avere autorizzazioni di sola lettura. In questa esercitazione si definiscono autorizzazioni di lettura per l'API Web.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>Concedere le autorizzazioni

Per chiamare un'API Web protetta da un'applicazione, è necessario concedere all'applicazione le autorizzazioni per l'API. Nell'esercitazione preliminare è stata creata un'applicazione Web denominata *app1* in Azure AD B2C. Questa applicazione verrà usata per chiamare l'API Web.

1. Selezionare **Applicazioni** e quindi *nativeapp1*.
1. Selezionare **Accesso all'API** e quindi **Aggiungi**.
1. Nell'elenco a discesa **Seleziona API** selezionare *webapi1*.
1. Nell'elenco a discesa **Seleziona ambiti** selezionare gli ambiti definiti in precedenza. Ad esempio, *demo.read* e *demo.write*.
1. Selezionare **OK**.

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
