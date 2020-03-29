---
title: Azure AD B2C (MSAL Android) Azure
titleSuffix: Microsoft identity platform
description: Informazioni su considerazioni specifiche sull'uso di Azure AD B2C con la libreria di autenticazione Microsoft per Android (MSAL. Android)
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
ms.openlocfilehash: 0998bb04b0dfc69db4696f2e390cfe259eba6718
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696522"
---
# <a name="use-msal-for-android-with-b2c"></a>Usare MSAL per Android con B2CUse MSAL for Android with B2C

Microsoft Authentication Library (MSAL) permette agli sviluppatori di applicazioni di autenticare gli utenti con identità di social networking e locali usando [Azure Active Directory B2C (Azure AD B2C)](https://docs.microsoft.com/azure/active-directory-b2c/). Azure AD B2C è un servizio di gestione delle identità, Utilizzarlo per personalizzare e controllare il modo in cui i clienti si iscrivono, accedono e gestiscono i propri profili quando utilizzano le applicazioni.

## <a name="configure-known-authorities-and-redirect-uri"></a>Configurare le autorità note e l'URI di reindirizzamento

In MSAL per Android, i criteri B2C (percorsi utente) sono configurati come singole autorità.

Dato un'applicazione B2C che ha due criteri:
- Iscrizione / Accesso
    * Chiamato`B2C_1_SISOPolicy`
- Modifica profilo
    * Chiamato`B2C_1_EditProfile`

Il file di configurazione per `authorities`l'app dichiarerebbe due file . Uno per ogni politica. La `type` proprietà di `B2C`ciascuna autorità è .

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

Deve `redirect_uri` essere registrato nella configurazione dell'app e anche per `AndroidManifest.xml` supportare il reindirizzamento durante il flusso di concessione del codice di [autorizzazione.](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-oauth-code)

## <a name="initialize-ipublicclientapplication"></a>Inizializzare IPublicClientApplication

`IPublicClientApplication`viene costruito da un metodo factory per consentire l'analisi asincrona della configurazione dell'applicazione.

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

## <a name="interactively-acquire-a-token"></a>Acquisire in modo interattivo un token

Per acquisire un token in modo `AcquireTokenParameters` interattivo con MSAL, compilare un'istanza e fornirla al `acquireToken` metodo. La richiesta di `default` token riportata di seguito utilizza l'autorità.

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

## <a name="silently-renew-a-token"></a>Rinnovare un token in modo invisibile all'utente

Per acquisire un token in modo `AcquireTokenSilentParameters` invisibile all'utente `acquireTokenSilentAsync` con MSAL, compilare un'istanza e fornirla al metodo. A `acquireToken` differenza del `authority` metodo, l'oggetto deve essere specificato per acquisire un token in modo invisibile all'utente.

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

Poiché i criteri in B2C sono rappresentati come autorità separate, la `fromAuthority` chiamata di `acquireToken` `acquireTokenSilent` un criterio diverso da quello predefinito viene ottenuta specificando una clausola durante la costruzione o i parametri.  Ad esempio:

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .withPrompt(Prompt.LOGIN)
    .callback(...) // provide callback here
    .fromAuthority("<url_of_policy_defined_in_configuration_json>")
    .build();
```

## <a name="handle-password-change-policies"></a>Gestire i criteri di modifica delle password

Il flusso utente di iscrizione o accesso dell'account locale mostra una '**password dimenticata?**' collegamento. Facendo clic su questo collegamento non viene attivato automaticamente un flusso utente di reimpostazione della password.

Viene invece restituito il codice di errore `AADB2C90118` all'app. L'app deve gestire questo codice di errore eseguendo un flusso utente specifico che reimposta la password.

Per intercettare un codice di errore di reimpostazione della password, è possibile utilizzare la seguente implementazione all'interno `AuthenticationCallback`di :

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

## <a name="use-iauthenticationresult"></a>Usare IAuthenticationResultUse IAuthenticationResult

Un'acquisizione di `IAuthenticationResult` token riuscita genera un oggetto. Contiene il token di accesso, le attestazioni utente e i metadati.

### <a name="get-the-access-token-and-related-properties"></a>Ottenere il token di accesso e le proprietà correlateGet the access token and related properties

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

### <a name="get-the-authorized-account"></a>Ottenere l'account autorizzatoGet the authorized account

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

Le attestazioni restituite nell'IdToken vengono popolate dal servizio token di sicurezza, non da MSAL. A seconda del provider di identità (IdP) utilizzato, alcune attestazioni potrebbero essere assenti. Alcuni IdP attualmente non `preferred_username` forniscono l'attestazione. Poiché questa attestazione viene utilizzata da MSAL `MISSING FROM THE TOKEN RESPONSE`per la memorizzazione nella cache, viene utilizzato un valore segnaposto, , , , al suo posto. Per altre informazioni sulle attestazioni IdToken B2C, vedere [Panoramica dei token in Azure Active Directory B2C.](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens#claims)

## <a name="managing-accounts-and-policies"></a>Gestione di account e criteri

B2C considera ogni politica come un'autorità separata. Pertanto, i token di accesso, i token di aggiornamento e i token ID restituiti da ogni criterio non sono intercambiabili. Ciò significa che `IAccount` ogni criterio restituisce un oggetto separato i cui token non possono essere utilizzati per richiamare altri criteri.

Ogni criterio `IAccount` aggiunge un alla cache per ogni utente. Se un utente accede a un'applicazione e richiama due `IAccount`criteri, avrà due s. Per rimuovere questo utente dalla cache, è necessario chiamare `removeAccount()` per ogni criterio.

Quando si rinnovano i `acquireTokenSilent`token per `IAccount` un criterio con , fornire lo `AcquireTokenSilentParameters`stesso risultato dalle precedenti chiamate del criterio a . Se si è verificato un errore, se si otdurrà un account restituito da un altro criterio.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure Active Directory B2C (Azure AD B2C) [, che cos'è Azure Active Directory B2C?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
