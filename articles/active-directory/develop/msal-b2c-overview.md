---
title: Usare MSAL con Azure Active Directory B2CLearn | Azure
titleSuffix: Microsoft identity platform
description: Microsoft Authentication Library per JavaScript (MSAL. js) consente alle applicazioni di usare Azure AD B2C e acquisire token per chiamare le API Web protette. Queste API Web possono essere Microsoft Graph, altre API Microsoft, API Web di terze parti o API Web proprie.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/16/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 8e076dfd6670265d458eb35d8e1b3e4500009a12
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81534483"
---
# <a name="use-microsoft-authentication-library-for-javascript-to-work-with-azure-active-directory-b2c"></a>Utilizzare Microsoft Authentication Library per JavaScript per utilizzare Azure Active Directory B2C

[Microsoft Authentication Library per JavaScript (MSAL. js)](https://github.com/AzureAD/microsoft-authentication-library-for-js) consente agli sviluppatori JavaScript di autenticare gli utenti con identità di social networking e locali usando [Azure Active Directory B2C (Azure ad B2C)](https://docs.microsoft.com/azure/active-directory-b2c/). Utilizzando Azure AD B2C come servizio di gestione delle identità, è possibile personalizzare e controllare il modo in cui i clienti effettuano l'iscrizione, l'accesso e la gestione dei profili quando utilizzano le applicazioni.

Azure AD B2C consente inoltre di personalizzare e personalizzare l'interfaccia utente delle applicazioni durante il processo di autenticazione per offrire un'esperienza uniforme ai clienti.

Questo articolo illustra come usare MSAL. js per lavorare con Azure AD B2C e riepiloga i punti chiave che è necessario conoscere. Per un'esercitazione e una discussione completa, consultare [Azure ad B2C documentazione](https://docs.microsoft.com/azure/active-directory-b2c/overview).

## <a name="prerequisites"></a>Prerequisiti

Se non è già stato creato un [tenant Azure ad B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant), iniziare subito a crearne uno (è anche possibile usare un tenant di Azure ad B2C esistente, se ne è già presente uno).

Questa dimostrazione contiene due parti:

- come proteggere un'API Web.
- come registrare un'applicazione a singola pagina per autenticare e chiamare *l'* API Web.

## <a name="nodejs-web-api"></a>API Web Node. js

> [!NOTE]
> A questo punto, MSAL. js per node è ancora in fase di sviluppo (vedere la [Roadmap](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)). Nel frattempo, è consigliabile usare [Passport-Azure-ad](https://github.com/AzureAD/passport-azure-ad), una libreria di autenticazione per node. js sviluppata e supportata da Microsoft.

I passaggi seguenti illustrano come un' **API Web** può usare Azure ad B2C per proteggere se stessa ed esporre gli ambiti selezionati a un'applicazione client.

### <a name="step-1-register-your-application"></a>Passaggio 1: Registrare l'applicazione

Per proteggere l'API Web con Azure AD B2C, prima di tutto è necessario registrarla. Per istruzioni dettagliate, vedere [Registrare l'applicazione](https://docs.microsoft.com/azure/active-directory-b2c/add-web-application?tabs=applications).

### <a name="step-2-download-the-sample-application"></a>Passaggio 2: scaricare l'applicazione di esempio

Scaricare l'esempio come file ZIP o clonarlo da GitHub:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="step-3-configure-authentication"></a>Passaggio 3: configurare l'autenticazione

1. Aprire il file `config.js` dell'esempio.

2. Configurare l'esempio con le credenziali dell'applicazione ottenute in precedenza durante la registrazione dell'applicazione. Modificare le righe di codice seguenti sostituendo i valori con i nomi di ClientID, host, tenantId e nome del criterio.

```JavaScript
const clientID = "<Application ID for your Node.js web API - found on Properties page in Azure portal e.g. 93733604-cc77-4a3c-a604-87084dd55348>";
const b2cDomainHost = "<Domain of your B2C host eg. fabrikamb2c.b2clogin.com>";
const tenantId = "<your-tenant-ID>.onmicrosoft.com"; // Alternatively, you can use your Directory (tenant) ID (GUID)
const policyName = "<Name of your sign in / sign up policy, e.g. B2C_1_signupsignin1>";
```

Per altre informazioni, vedere l' [esempio di API Web Node. js B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi).

---

## <a name="javascript-spa"></a>JavaScript SPA

I passaggi seguenti illustrano il modo in cui un' **applicazione a pagina singola** può usare Azure ad B2C per iscriversi, effettuare l'accesso e chiamare un'API Web protetta.

### <a name="step-1-register-your-application"></a>Passaggio 1: Registrare l'applicazione

Per implementare l'autenticazione, è prima di tutto necessario registrare l'applicazione. Per istruzioni dettagliate, vedere [Registrare l'applicazione](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications).

### <a name="step-2-download-the-sample-application"></a>Passaggio 2: scaricare l'applicazione di esempio

Scaricare l'esempio come file ZIP o clonarlo da GitHub:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>Passaggio 3: configurare l'autenticazione

Per la configurazione dell'applicazione sono disponibili due punti di interesse:

- Configurare l'endpoint API e gli ambiti esposti
- Configurare i parametri di autenticazione e gli ambiti dei token

1. Aprire il file `apiConfig.js` dell'esempio.

2. Configurare l'esempio con i parametri ottenuti in precedenza durante la registrazione dell'API Web. Modificare le righe di codice seguenti sostituendo i valori con l'indirizzo dell'API Web e gli ambiti esposti.

   ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"], //API scopes you exposed during api registration
        webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
    };
   ```

3. Aprire il file `authConfig.js` dell'esempio.

4. Configurare l'esempio con i parametri ottenuti in precedenza durante la registrazione dell'applicazione a singola pagina. Modificare le righe di codice seguenti sostituendo i valori con i clienti, i metadati dell'autorità e gli ambiti delle richieste di token.

   ```javascript
    // Config object to be passed to Msal on creation.
    const msalConfig = {
        auth: {
            clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902",
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/B2C_1_signupsignin1",
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage", // This configures where your cache will be stored
            storeAuthStateInCookie: false // Set this to "true" to save cache in cookies
        }
    };

    // Add here scopes for id token to be used at the MS Identity Platform endpoint
    const loginRequest = {
        scopes: ["openid", "profile"],
    };
   ```

Per altre informazioni, vedere l' [esempio di applicazione a pagina singola di JavaScript B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp).

---

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su:
- [Flussi utente](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows)
- [Criteri personalizzati](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)
- [Personalizzazione dell'esperienza utente](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-configure-user-input)
