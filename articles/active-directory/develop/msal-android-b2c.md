---
title: Azure AD B2C (MSAL Android) | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su considerazioni specifiche quando si usa Azure AD B2C con Microsoft Authentication Library per Android (MSAL. Android
services: active-directory
author: brianmel
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 9/18/2019
ms.author: brianmel
ms.reviewer: rapong
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 98a31ea2daffba19242e73362af5a44e3a392342
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74917115"
---
# <a name="use-msal-for-android-with-b2c"></a>Usare MSAL per Android con B2C

Microsoft Authentication Library (MSAL) permette agli sviluppatori di applicazioni di autenticare gli utenti con identità di social networking e locali usando [Azure Active Directory B2C (Azure AD B2C)](https://docs.microsoft.com/azure/active-directory-b2c/). Azure AD B2C è un servizio di gestione delle identità, È possibile usarlo per personalizzare e controllare la modalità di iscrizione, accesso e gestione dei profili dei clienti quando usano le applicazioni.

## <a name="configure-known-authorities-and-redirect-uri"></a>Configurare le autorità note e l'URI di Reindirizzamento

In MSAL per Android i criteri B2C (percorsi utente) sono configurati come singole autorità.

Data un'applicazione B2C con due criteri:
- Iscrizione/accesso
    * Chiamata `B2C_1_SISOPolicy`
- Modifica profilo
    * Chiamata `B2C_1_EditProfile`

Il file di configurazione per l'app dichiara due `authorities`. Uno per ogni criterio. Il `type` proprietà di ogni autorità è `B2C`.

### `app/src/main/res/raw/msal_config.json`
```json
{
    "client_id": "<your_client_id_here>",
    "redirect_uri": "<your_redirect_uri_here>",
    "authorities": [{
            "type": "B2C",
            "authority_url": "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/",
            "default": true
        },
        {
            "type": "B2C",
            "authority_url": "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_EditProfile/"
        }
    ]
}
```

Il `redirect_uri` deve essere registrato nella configurazione dell'app e anche in `AndroidManifest.xml` per supportare il reindirizzamento durante il flusso di [concessione del codice di autorizzazione](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-oauth-code).

## <a name="initialize-ipublicclientapplication"></a>Inizializzare IPublicClientApplication

`IPublicClientApplication` viene costruito da un metodo factory per consentire l'analisi asincrona della configurazione dell'applicazione.

```java
PublicClientApplication.createMultipleAccountPublicClientApplication(
    context, // Your application Context
    R.raw.msal_config, // Id of app JSON config
    new IPublicClientApplication.ApplicationCreatedListener() {
        @Override
        public void onCreated(IMultipleAccountPublicClientApplication pca) {
            // Application has been initialized.
        }

        @Override
        public void onError(MsalException exception) {
            // Application could not be created.
            // Check Exception message for details.
        }
    }
);
```

## <a name="interactively-acquire-a-token"></a>Acquisire un token in modo interattivo

Per acquisire un token in modo interattivo con MSAL, compilare un'istanza di `AcquireTokenParameters` e fornirla al metodo `acquireToken`. La richiesta di token seguente usa l'autorità di `default`.

```java
IMultipleAccountPublicClientApplication pca = ...; // Initialization not shown

AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .withPrompt(Prompt.LOGIN)
    .callback(new AuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            // Token request was successful, inspect the result
        }

        @Override
        public void onError(MsalException exception) {
            // Token request was unsuccessful, inspect the exception
        }

        @Override
        public void onCancel() {
            // The user cancelled the flow
        }
    }).build();

pca.acquireToken(parameters);
```

## <a name="silently-renew-a-token"></a>Rinnovare automaticamente un token

Per acquisire un token in modo invisibile all'utente con MSAL, compilare un'istanza di `AcquireTokenSilentParameters` e fornirla al metodo `acquireTokenSilentAsync`. A differenza del metodo `acquireToken`, è necessario specificare il `authority` per acquisire un token in modo invisibile all'utente.

```java
IMultilpeAccountPublicClientApplication pca = ...; // Initialization not shown
AcquireTokenSilentParameters parameters = new AcquireTokenSilentParameters.Builder()
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .forAccount(account)
    // Select a configured authority (policy), mandatory for silent auth requests
    .fromAuthority("https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/")
    .callback(new SilentAuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            // Token request was successful, inspect the result
        }

        @Override
        public void onError(MsalException exception) {
            // Token request was unsuccesful, inspect the exception
        }
    })
    .build();

pca.acquireTokenSilentAsync(parameters);
```

## <a name="specify-a-policy"></a>Specificare un criterio

Poiché i criteri in B2C sono rappresentati come autorità separate, il richiamo di un criterio diverso da quello predefinito viene ottenuto specificando una clausola `fromAuthority` quando si costruiscono parametri `acquireToken` o `acquireTokenSilent`.  ad esempio:

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .withPrompt(Prompt.LOGIN)
    .callback(...) // provide callback here
    .fromAuthority("<url_of_policy_defined_in_configuration_json>")
    .build();
```

## <a name="handle-password-change-policies"></a>Gestire i criteri di modifica della password

Il flusso dell'utente per l'iscrizione o l'accesso all'account locale Visualizza una**password dimenticata?** collegamento. Facendo clic su questo collegamento non viene attivato automaticamente un flusso utente di reimpostazione della password.

Al contrario, il codice di errore `AADB2C90118` viene restituito all'app. L'app deve gestire questo codice di errore eseguendo un flusso utente specifico che reimposta la password.

Per intercettare un codice di errore di reimpostazione della password, è possibile usare l'implementazione seguente all'interno del `AuthenticationCallback`:

```java
new AuthenticationCallback() {

    @Override
    public void onSuccess(IAuthenticationResult authenticationResult) {
        // ..
    }

    @Override
    public void onError(MsalException exception) {
        final String B2C_PASSWORD_CHANGE = "AADB2C90118";

        if (exception.getMessage().contains(B2C_PASSWORD_CHANGE)) {
            // invoke password reset flow
        }
    }

    @Override
    public void onCancel() {
        // ..
    }
}
```

## <a name="use-iauthenticationresult"></a>Usare IAuthenticationResult

Un'acquisizione di token riuscita restituisce un oggetto `IAuthenticationResult`. Contiene il token di accesso, le attestazioni utente e i metadati.

### <a name="get-the-access-token-and-related-properties"></a>Ottenere il token di accesso e le proprietà correlate

```java
// Get the raw bearer token
String accessToken = authenticationResult.getAccessToken();

// Get the scopes included in the access token
String[] accessTokenScopes = authenticationResult.getScope();

// Gets the access token's expiry
Date expiry = authenticationResult.getExpiresOn();

// Get the tenant for which this access token was issued
String tenantId = authenticationResult.getTenantId();
```

### <a name="get-the-authorized-account"></a>Ottenere l'account autorizzato

```java
// Get the account from the result
IAccount account = authenticationResult.getAccount();

// Get the id of this account - note for B2C, the policy name is a part of the id
String id = account.getId();

// Get the IdToken Claims
//
// For more information about B2C token claims, see reference documentation
// https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens
Map<String, ?> claims = account.getClaims();

// Get the 'preferred_username' claim through a convenience function
String username = account.getUsername();

// Get the tenant id (tid) claim through a convenience function
String tenantId = account.getTenantId();
```

### <a name="idtoken-claims"></a>Attestazioni IdToken

Le attestazioni restituite in IdToken vengono popolate dal servizio token di sicurezza (STS), non da MSAL. A seconda del provider di identità (IdP) usato, alcune attestazioni potrebbero essere assenti. Alcuni IDP attualmente non forniscono il `preferred_username` attestazione. Poiché questa attestazione viene utilizzata da MSAL per la memorizzazione nella cache, al suo posto viene utilizzato un valore segnaposto `MISSING FROM THE TOKEN RESPONSE`. Per altre informazioni sulle attestazioni IdToken B2C, vedere [Cenni preliminari sui token in Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens#claims).

## <a name="managing-accounts-and-policies"></a>Gestione di account e criteri

B2C considera ogni criterio come un'autorità separata. Pertanto, i token di accesso, i token di aggiornamento e i token ID restituiti da ogni criterio non sono intercambiabili. Questo significa che ogni criterio restituisce un oggetto `IAccount` separato i cui token non possono essere usati per richiamare altri criteri.

Ogni criterio consente di aggiungere una `IAccount` alla cache per ogni utente. Se un utente accede a un'applicazione e richiama due criteri, avranno due `IAccount`s. Per rimuovere l'utente dalla cache, è necessario chiamare `removeAccount()` per ogni criterio.

Quando si rinnovano i token per un criterio con `acquireTokenSilent`, fornire lo stesso `IAccount` restituito dalle chiamate precedenti del criterio al `AcquireTokenSilentParameters`. Se si specifica un account restituito da un altro criterio, verrà generato un errore.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle Azure Active Directory B2C (Azure AD B2C) in [che cosa sono Azure Active Directory B2C?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
