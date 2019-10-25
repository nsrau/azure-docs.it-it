---
title: "App per dispositivi mobili che chiama API Web: ottenere un token per l'app"
titleSuffix: Microsoft identity platform
description: Informazioni su come creare un'app per dispositivi mobili che chiama le API Web (ricevendo un token per l'app)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b1d6a0e4bfffaaf4ed0fa836bcf62a7f1104e6a
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803761"
---
# <a name="mobile-app-that-calls-web-apis---get-a-token"></a>App per dispositivi mobili che chiama API Web-ottenere un token

Prima di iniziare a chiamare le API Web protette, l'app richiede un token di accesso. Questo articolo illustra il processo di recupero di un token tramite Microsoft Authentication Library (MSAL).

## <a name="scopes-to-request"></a>Ambiti da richiedere

Quando si richiede un token, è necessario definire un ambito. L'ambito determina a quali dati può accedere l'app.  

L'approccio più semplice consiste nel combinare la `App ID URI` dell'API Web desiderata con l'ambito `.default`. In questo modo si comunica alla piattaforma di identità Microsoft che l'app richiede che tutti gli ambiti siano impostati nel portale.

#### <a name="android"></a>Android
```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
```

#### <a name="ios"></a>iOS
```swift
let scopes = ["https://graph.microsoft.com/.default"]
```

#### <a name="xamarin"></a>Xamarin
```CSharp 
var scopes = new [] {"https://graph.microsoft.com/.default"};
```

## <a name="get-tokens"></a>Ottenere i token

### <a name="acquire-tokens-via-msal"></a>Acquisire token tramite MSAL

MSAL consente alle app di acquisire i token in modo invisibile all'utente e in modo interattivo. È sufficiente chiamare questi metodi e MSAL restituisce un token di accesso per gli ambiti richiesti. Il modello corretto prevede l'esecuzione di una richiesta invisibile all'utente e il fallback a una richiesta interattiva.

#### <a name="android"></a>Android

```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);

// Check if there are any accounts we can sign in silently.
// Result is in the silent callback (success or error).
sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
    @Override
    public void onAccountsLoaded(final List<IAccount> accounts) {

        if (accounts.isEmpty() && accounts.size() == 1) {
            // TODO: Create a silent callback to catch successful or failed request.
            sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
        } else {
            /* No accounts or > 1 account. */
        }
    }
});    

[...]

// No accounts found. Interactively request a token.
// TODO: Create an interactive callback to catch successful or failed request.
sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());        
```

#### <a name="ios"></a>iOS

**Provare prima ad acquisire un token in modo invisibile all'utente:**

Objective-C:

```objc

NSArray *scopes = @[@"https://graph.microsoft.com/.default"];
NSString *accountIdentifier = @"my.account.id";
    
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];
    
MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {
        
    if (!error)
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;
            
        // Access token to call the Web API
        NSString *accessToken = result.accessToken;
    }
        
    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
        return;
    }
}];
```
 
Swift:

```swift

let scopes = ["https://graph.microsoft.com/.default"]
let accountIdentifier = "my.account.id"
        
guard let account = try? application.account(forIdentifier: accountIdentifier) else { return }
let silentParameters = MSALSilentTokenParameters(scopes: scopes, account: account)
application.acquireTokenSilent(with: silentParameters) { (result, error) in
            
    guard let authResult = result, error == nil else {
                
    let nsError = error! as NSError
                
    if (nsError.domain == MSALErrorDomain &&
        nsError.code == MSALError.interactionRequired.rawValue) {
                    
            // Interactive auth will be required, call acquireToken()
            return
         }
         return
     }
            
    // You'll want to get the account identifier to retrieve and reuse the account
    // for later acquireToken calls
    let accountIdentifier = authResult.account.identifier
            
    // Access token to call the Web API
    let accessToken = authResult.accessToken
}
```

**Se MSAL restituisce `MSALErrorInteractionRequired`, provare ad acquisire i token in modo interattivo:**

Objective-C:

```objc
UIViewController *viewController = ...; // Pass a reference to the view controller that should be used when getting a token interactively
MSALWebviewParameters *webParameters = [[MSALWebviewParameters alloc] initWithParentViewController:viewController];
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:webParameters];
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) {
    if (!error) 
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;
            
        NSString *accessToken = result.accessToken;
    }
}];
```

Swift:

```swift
let viewController = ... // Pass a reference to the view controller that should be used when getting a token interactively
let webviewParameters = MSALWebviewParameters(parentViewController: viewController)
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: webviewParameters)
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in
                
    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }
                
    // Get access token from result
    let accessToken = authResult.accessToken
})
```

MSAL per iOS e macOS supporta vari modificatori quando si recupera un token in modo interattivo o invisibile all'utente.
* [Parametri comuni quando si recupera un token](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALTokenParameters.html#/Configuration%20parameters)
* [Parametri per l'acquisizione di token interattivi](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALInteractiveTokenParameters.html#/Configuring%20MSALInteractiveTokenParameters)
* [Parametri per l'acquisizione di token invisibile all'utente](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALSilentTokenParameters.html)

#### <a name="xamarin"></a>Xamarin

Nell'esempio seguente viene illustrato il codice minimo per ottenere un token in modo interattivo per la lettura del profilo dell'utente con Microsoft Graph.

```CSharp
string[] scopes = new string[] {"user.read"};
var app = PublicClientApplicationBuilder.Create(clientId).Build();
var accounts = await app.GetAccountsAsync();
AuthenticationResult result;
try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
             .ExecuteAsync();
}
catch(MsalUiRequiredException)
{
 result = await app.AcquireTokenInteractive(scopes)
             .ExecuteAsync();
}
```

#### <a name="mandatory-parameters-in-msalnet"></a>Parametri obbligatori in MSAL.NET

`AcquireTokenInteractive` dispone di un solo parametro obbligatorio ``scopes``, che contiene un'enumerazione di stringhe che definiscono gli ambiti per i quali è richiesto un token. Se il token è per la Microsoft Graph, gli ambiti richiesti sono disponibili in riferimento API di ogni API Microsoft Graph nella sezione denominata "autorizzazioni". Per [elencare i contatti dell'utente](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts), ad esempio, sarà necessario usare l'ambito "User. Read", "Contacts. Read". Vedere anche [Microsoft Graph riferimento alle autorizzazioni](https://developer.microsoft.com/graph/docs/concepts/permissions_reference).

Se non è stato specificato durante la compilazione dell'app, in Android è necessario specificare anche l'attività padre (usando `.WithParentActivityOrWindow`, vedere più avanti) in modo che il token torni a tale attività padre dopo l'interazione. Se non viene specificato, viene generata un'eccezione quando si chiama `.ExecuteAsync()`.

#### <a name="specific-optional-parameters-in-msalnet"></a>Parametri facoltativi specifici in MSAL.NET

##### <a name="withprompt"></a>WithPrompt

`WithPrompt()` viene usato per controllare l'interattività con l'utente specificando una richiesta

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

La classe definisce le costanti seguenti:

- ``SelectAccount``: forza il STS a presentare la finestra di dialogo di selezione dell'account contenente gli account per i quali l'utente dispone di una sessione. Questa opzione è utile quando gli sviluppatori di applicazioni desiderano consentire all'utente di scegliere tra identità diverse. Questa opzione consente a MSAL di inviare ``prompt=select_account`` al provider di identità. Questa opzione è l'impostazione predefinita e consente di garantire la migliore esperienza possibile in base alle informazioni disponibili (account, presenza di una sessione per l'utente e così via). ...). Non modificarla a meno che non si disponga di un buon motivo per eseguire questa operazione.
- ``Consent``: consente allo sviluppatore di applicazioni di forzare la richiesta di consenso da parte dell'utente, anche se è stato concesso il consenso prima. In questo caso, MSAL Invia `prompt=consent` al provider di identità. Questa opzione può essere usata in alcune applicazioni con sicurezza mirata, in cui la governance dell'organizzazione richiede che all'utente venga visualizzata la finestra di dialogo di consenso ogni volta che viene usata l'applicazione.
- ``ForceLogin``: consente allo sviluppatore di applicazioni di chiedere all'utente le credenziali dal servizio anche se il prompt utente non è necessario. Questa opzione può essere utile se l'acquisizione di un token ha esito negativo, per consentire all'utente di eseguire di nuovo l'accesso. In questo caso, MSAL Invia `prompt=login` al provider di identità. Anche in questo caso abbiamo visto che è stato usato in alcune applicazioni mirate alla sicurezza in cui la governance dell'organizzazione richiede che l'utente si riconnetta ogni volta che accede a parti specifiche di un'applicazione.
- ``Never`` (solo per .NET 4,5 e WinRT) non richiede l'intervento dell'utente, ma tenterà invece di usare il cookie archiviato nella visualizzazione Web incorporata nascosta (vedere di seguito: visualizzazioni Web in MSAL.NET). L'uso di questa opzione potrebbe non riuscire e, in tal caso `AcquireTokenInteractive` genererà un'eccezione per notificare che è necessaria un'interazione dell'interfaccia utente e sarà necessario usare un altro parametro di `Prompt`.
- ``NoPrompt``: non verrà inviato alcun messaggio di richiesta al provider di identità. Questa opzione è utile solo per Azure AD B2C modificare i criteri del profilo (vedere le [specifiche B2C](https://aka.ms/msal-net-b2c-specificities)).

##### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Questo modificatore viene usato in uno scenario avanzato in cui si vuole che l'utente preacconsente a diverse risorse in anticipo (e non vuole usare il consenso incrementale, che viene in genere usato con MSAL.NET/Microsoft Identity Platform v 2.0). Per informazioni dettagliate [, vedere Procedura: ottenere il consenso dell'utente in anticipo per diverse risorse](scenario-desktop-production.md#how-to-have--the-user-consent-upfront-for-several-resources).

```CSharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

##### <a name="other-optional-parameters"></a>Altri parametri facoltativi

Altre informazioni su tutti gli altri parametri facoltativi per `AcquireTokenInteractive` dalla documentazione di riferimento per [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods)

### <a name="acquire-tokens-via-the-protocol"></a>Acquisire token tramite il protocollo

Non è consigliabile usare direttamente il protocollo. In tal caso, l'app non supporterà alcuni Single Sign-On (SSO), la gestione dei dispositivi e gli scenari di accesso condizionale.

Quando si usa il protocollo per ottenere i token per le app per dispositivi mobili, è necessario effettuare due richieste: ottenere un codice di autorizzazione e scambiarlo per un token.

#### <a name="get-authorization-code"></a>Ottenere il codice di autorizzazione

```Text
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=<CLIENT_ID>
&response_type=code
&redirect_uri=<ENCODED_REDIRECT_URI>
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2F.default
&state=12345
```

#### <a name="get-access-and-refresh-token"></a>Ottenere l'accesso e il token di aggiornamento

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=<CLIENT_ID>
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=<ENCODED_REDIRECT_URI>
&grant_type=authorization_code
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Chiamata di un'API Web](scenario-mobile-call-api.md)
