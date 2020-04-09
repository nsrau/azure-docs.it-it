---
title: Usare MSAL con Azure Active Directory B2CLearn . Azure
titleSuffix: Microsoft identity platform
description: Microsoft Authentication Library per JavaScript (MSAL.js) consente alle applicazioni di utilizzare Azure AD B2C e acquisire token per chiamare API Web protette. Queste API Web possono essere Microsoft Graph, altre API Microsoft, API Web di terze parti o API Web proprie.
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
ms.openlocfilehash: dc8a330bc09f37f7941534ed7c17d1ffd14d08c5
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875963"
---
# <a name="use-microsoft-authentication-library-for-javascript-to-work-with-azure-active-directory-b2c"></a>Usare la libreria di autenticazione Microsoft per JavaScript per lavorare con Azure Active Directory B2CUse Microsoft Authentication Library for JavaScript to work with Azure Active Directory B2C

[Microsoft Authentication Library per JavaScript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js) consente agli sviluppatori JavaScript di autenticare gli utenti con identità social e locali utilizzando [Azure Active Directory B2C (Azure AD B2C).](https://docs.microsoft.com/azure/active-directory-b2c/) Usando Azure AD B2C come servizio di gestione delle identità, è possibile personalizzare e controllare il modo in cui i clienti si iscrivono, accedono e gestiscono i propri profili quando usano le applicazioni.

Azure AD B2C consente inoltre di personalizzare l'interfaccia utente delle applicazioni durante il processo di autenticazione per offrire ai clienti un'esperienza senza soluzione di continuità.

Questo articolo illustra come usare MSAL.js per usare Azure AD B2C e riepiloga i punti chiave di cui è necessario essere a conoscenza. Per una discussione completa e un'esercitazione, consultare la [documentazione B2C di Azure AD.](https://docs.microsoft.com/azure/active-directory-b2c/overview)

## <a name="prerequisites"></a>Prerequisiti

Se non è già stato creato il proprio [tenant B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)di Azure AD, iniziare con la creazione di uno ora (è anche possibile usare un tenant B2C di Azure AD esistente se ne è già disponibile uno).

Questa dimostrazione contiene due parti:This demonstration contains two parts:

- come proteggere un'API Web.
- come registrare un'applicazione a pagina singola per autenticarla e *chiamarla* API Web.

## <a name="nodejs-web-api"></a>Node.js Web API

> [!NOTE]
> In questo momento, MSAL.js per Node è ancora in fase di sviluppo (vedere la [roadmap](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)). Nel frattempo, si consiglia di utilizzare [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad), una libreria di autenticazione per Node.js sviluppata e supportata da Microsoft.

I passaggi seguenti illustrano come **un'API Web** può usare Azure AD B2C per proteggersi ed esporre gli ambiti selezionati a un'applicazione client.

### <a name="step-1-register-your-application"></a>Passaggio 1: Registrare l'applicazione

Per proteggere l'API Web con Azure AD B2C, è innanzitutto necessario registrarla. Per istruzioni dettagliate, vedere [Registrare l'applicazione](https://docs.microsoft.com/azure/active-directory-b2c/add-web-application?tabs=applications).

### <a name="step-2-download-the-sample-application"></a>Passaggio 2: Scaricare l'applicazione di esempioStep 2: Download the sample application

Scaricare l'esempio come file ZIP o clonarlo da GitHub:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="step-3-configure-authentication"></a>Passaggio 3: Configurare l'autenticazioneStep 3: Configure authentication

1. Aprire il file `config.js` dell'esempio.

2. Configurare l'esempio con le credenziali dell'applicazione ottenute in precedenza durante la registrazione dell'applicazione. Modificare le righe di codice seguenti sostituendo i valori con i nomi di clientID, host, tenantId e nome del criterio.

```JavaScript
const clientID = "<Application ID for your Node.js Web API - found on Properties page in Azure portal e.g. 93733604-cc77-4a3c-a604-87084dd55348>";
const b2cDomainHost = "<Domain of your B2C host eg. fabrikamb2c.b2clogin.com>";
const tenantId = "<your-tenant-ID>.onmicrosoft.com"; // Alternatively, you can use your Directory (tenant) ID (GUID)
const policyName = "<Name of your sign in / sign up policy, e.g. B2C_1_signupsignin1>";
```

Per altre informazioni, vedere questo esempio di [API Web B2C Node.js](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi).

---

## <a name="javascript-spa"></a>JavaScript SPA

I passaggi seguenti illustrano come un'applicazione a pagina singola può usare Azure AD B2C per iscriversi, accedere e chiamare **un'API** Web protetta.

### <a name="step-1-register-your-application"></a>Passaggio 1: Registrare l'applicazione

Per implementare l'autenticazione, è prima di tutto necessario registrare l'applicazione. Per istruzioni dettagliate, vedere [Registrare l'applicazione](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications).

### <a name="step-2-download-the-sample-application"></a>Passaggio 2: Scaricare l'applicazione di esempioStep 2: Download the sample application

Scaricare l'esempio come file ZIP o clonarlo da GitHub:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>Passaggio 3: Configurare l'autenticazioneStep 3: Configure authentication

La configurazione dell'applicazione può interessare due punti:

- Configurare l'endpoint API e gli ambiti espostiConfigure API endpoint and exposed scopes
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

4. Configurare l'esempio con i parametri ottenuti in precedenza durante la registrazione dell'applicazione a pagina singola. Modificare le righe di codice seguenti sostituendo i valori con clientId, metadati dell'autorità e ambiti di richiesta token.

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

Per ulteriori informazioni, consultare questo esempio di [applicazione JavaScript B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)a pagina singola .

---

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su:
- [Flussi utente](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows)
- [Criteri personalizzati](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)
- [Personalizzazione dell'esperienza utente](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-configure-user-input)
