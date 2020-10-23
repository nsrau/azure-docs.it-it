---
title: Usare MSAL.js con Azure AD B2C
titleSuffix: Microsoft identity platform
description: Microsoft Authentication Library per JavaScript (MSAL.js) consente alle applicazioni di usare Azure AD B2C e acquisire token per chiamare le API Web protette. Queste API Web possono essere Microsoft Graph, altre API Microsoft, API Web di terze parti o API Web proprie.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/05/2020
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: ab072fa53d3ecc3f856b6765acfb8c19da3ff298
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92442253"
---
# <a name="use-microsoft-authentication-library-for-javascript-to-work-with-azure-ad-b2c"></a>Utilizzare Microsoft Authentication Library per JavaScript per utilizzare Azure AD B2C

[Microsoft Authentication Library per JavaScript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js) consente agli sviluppatori JavaScript di autenticare gli utenti con identità di social networking e locali tramite [Azure Active Directory B2C](../../active-directory-b2c/overview.md) (Azure ad B2C).

Utilizzando Azure AD B2C come servizio di gestione delle identità, è possibile personalizzare e controllare il modo in cui i clienti effettuano l'iscrizione, l'accesso e la gestione dei profili quando utilizzano le applicazioni. Azure AD B2C consente inoltre di personalizzare e personalizzare l'interfaccia utente visualizzata dall'applicazione durante il processo di autenticazione.

Le sezioni seguenti illustrano come:

- Proteggere un'API Web di Node.js
- Supportare l'accesso in un'applicazione a singola pagina (SPA) e chiamare *l'* API Web protetta
- Abilita supporto per la reimpostazione della password

## <a name="prerequisites"></a>Prerequisiti

Se non è già stato fatto, creare un [tenant Azure ad B2C](../../active-directory-b2c/tutorial-create-tenant.md).

## <a name="nodejs-web-api"></a>API Web Node. js

I passaggi seguenti illustrano come un' **API Web** può usare Azure ad B2C per proteggere se stessa ed esporre gli ambiti selezionati a un'applicazione client.

MSAL.js per node è attualmente in fase di sviluppo. Per ulteriori informazioni, vedere la Guida di [orientamento](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap) su GitHub. È attualmente consigliabile usare [Passport-Azure-ad](https://github.com/AzureAD/passport-azure-ad), una libreria di autenticazione per Node.js sviluppata e supportata da Microsoft.

### <a name="step-1-register-your-application"></a>Passaggio 1: Registrare l'applicazione

Per proteggere l'API Web con Azure AD B2C, prima di tutto è necessario registrarla. Per istruzioni dettagliate, vedere [Registrare l'applicazione](../../active-directory-b2c/add-web-api-application.md).

### <a name="step-2-download-the-sample-application"></a>Passaggio 2: scaricare l'applicazione di esempio

Scaricare l'esempio come file ZIP o clonarlo da GitHub:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="step-3-configure-authentication"></a>Passaggio 3: configurare l'autenticazione

1. Aprire il file `config.json` dell'esempio.

2. Configurare l'esempio con le credenziali dell'applicazione ottenute in precedenza durante la registrazione dell'applicazione. Modificare le righe di codice seguenti sostituendo i valori con il nome del tenant, l'ID client e il nome del criterio.

    ```json
         "credentials": {
             "tenantName": "<your-tenant-name>",
             "clientID": "<your-webapi-application-ID>"
         },
         "policies": {
             "policyName": "B2C_1_signupsignin1"
         },
         "resource": {
             "scope": ["demo.read"] 
         },
    ```

Per altre informazioni, vedere questo [ esempio di API webNode.js B2C](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi).

## <a name="javascript-spa"></a>JavaScript SPA

I passaggi seguenti illustrano il modo in cui un' **applicazione a pagina singola** può usare Azure ad B2C per iscriversi, effettuare l'accesso e chiamare un'API Web protetta.

### <a name="step-1-register-your-application"></a>Passaggio 1: Registrare l'applicazione

Per implementare l'autenticazione, è prima di tutto necessario registrare l'applicazione. Per istruzioni dettagliate, vedere [Registrare l'applicazione](../../active-directory-b2c/tutorial-register-applications.md).

### <a name="step-2-download-the-sample-application"></a>Passaggio 2: scaricare l'applicazione di esempio

Scaricare l'esempio di codice [. Archiviare](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) o clonare il repository GitHub:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>Passaggio 3: configurare l'autenticazione

Per la configurazione dell'applicazione sono disponibili due punti di interesse:

- Configurare l'endpoint API e gli ambiti esposti
- Configurare i parametri di autenticazione e gli ambiti dei token

1. Aprire il file *apiConfig.js* nell'esempio.

2. Configurare l'esempio con i parametri ottenuti in precedenza durante la registrazione dell'API Web. Modificare le righe di codice seguenti sostituendo i valori con l'indirizzo dell'API Web e gli ambiti esposti.

   ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"], //API scopes you exposed during api registration
        webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
    };
   ```

1. Aprire il file *authConfig.js* nell'esempio.

1. Configurare l'esempio con i parametri ottenuti in precedenza durante la registrazione dell'applicazione a singola pagina. Modificare le righe di codice seguenti sostituendo i valori con i clienti, i metadati dell'autorità e gli ambiti delle richieste di token.

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

## <a name="support-password-reset"></a>Supporto per la reimpostazione della password

In questa sezione si estenderà l'applicazione a singola pagina per usare il flusso utente per la reimpostazione della password Azure AD B2C. Sebbene MSAL.js attualmente non supporti più flussi utente o criteri personalizzati in modo nativo, è possibile usare la libreria per gestire casi d'uso comuni come la reimpostazione della password.

Nei passaggi seguenti si presuppone che siano già stati seguiti i passaggi descritti nella sezione precedente relativa a [JavaScript Spa](#javascript-spa) .

### <a name="step-1-define-the-authority-string-for-password-reset-user-flow"></a>Passaggio 1: definire la stringa dell'autorità per il flusso utente per la reimpostazione della password

1. Per prima cosa, creare un oggetto in cui archiviare gli URI dell'autorità:

    ```javascript
        const b2cPolicies = {
            names: {
                signUpSignIn: "b2c_1_susi",
                forgotPassword: "b2c_1_reset"
            },
            authorities: {
                signUpSignIn: {
                    authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi",
                },
                forgotPassword: {
                    authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_reset",
                },
            },
        }
    ```

1. Successivamente, inizializzare l'oggetto MSAL con il `signInSignUp` criterio come predefinito (vedere il frammento di codice precedente). Quando un utente tenta di eseguire l'accesso, viene visualizzata la schermata seguente:

    :::image type="content" source="media/msal-b2c-overview/user-journey-01-signin.png" alt-text="Schermata di accesso visualizzata da Azure AD B2C":::

### <a name="step-2-catch-and-handle-authentication-errors-in-your-login-method"></a>Passaggio 2: intercettare e gestire gli errori di autenticazione nel metodo di accesso

Quando un utente seleziona **password dimenticata**, l'applicazione genera un errore che dovrebbe essere intercettato nel codice e quindi gestito presentando il flusso utente appropriato. In questo caso, il `b2c_1_reset` flusso di reimpostazione della password.

1. Estendere il metodo di accesso come segue:

    ```javascript
    function signIn() {
      myMSALObj.loginPopup(loginRequest)
        .then(loginResponse => {
            console.log("id_token acquired at: " + new Date().toString());

            if (myMSALObj.getAccount()) {
              updateUI();
            }

        }).catch(function (error) {
          console.log(error);

          // error handling
          if (error.errorMessage) {
            // check for forgot password error
            if (error.errorMessage.indexOf("AADB2C90118") > -1) {

              //call login method again with the password reset user flow
              myMSALObj.loginPopup(b2cPolicies.authorities.forgotPassword)
                .then(loginResponse => {
                  console.log(loginResponse);
                  window.alert("Password has been reset successfully. \nPlease sign-in with your new password.");
                })
            }
          }
        });
    }
    ```

1. Il frammento di codice precedente Mostra come visualizzare la schermata di reimpostazione della password dopo avere intercettato l'errore con il codice `AADB2C90118` .

    Dopo aver reimpostato la password, l'utente torna all'applicazione per accedere di nuovo.

    :::image type="content" source="media/msal-b2c-overview/user-journey-02-password-reset.png" alt-text="Schermata di accesso visualizzata da Azure AD B2C" border="false":::

    Per ulteriori informazioni sui codici di errore e sulla gestione delle eccezioni, vedere [codici di errore e di eccezione MSAL](msal-handling-exceptions.md).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su questi Azure AD B2C concetti:

- [Flussi utente](../../active-directory-b2c/tutorial-create-user-flows.md)
- [Criteri personalizzati](../../active-directory-b2c/custom-policy-get-started.md)
- [Personalizzazione dell'esperienza utente](../../active-directory-b2c/custom-policy-configure-user-input.md)
