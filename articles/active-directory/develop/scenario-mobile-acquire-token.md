---
title: Acquisire un token per chiamare un'API Web (app per dispositivi mobili) Azure
titleSuffix: Microsoft identity platform
description: Scopri come creare un'app per dispositivi mobili che chiama le API Web. (Ottenere un token per l'app.)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: cf967525283f28d5829d80b75e40e263f7eaedef
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882744"
---
# <a name="get-a-token-for-a-mobile-app-that-calls-web-apis"></a>Ottenere un token per un'app per dispositivi mobili che chiama le API WebGet a token for a mobile app that calls web APIs

Prima che l'app possa chiamare API Web protette, è necessario un token di accesso. In questo articolo viene illustrato il processo per ottenere un token utilizzando Microsoft Authentication Library (MSAL).

## <a name="define-a-scope"></a>Definire un ambitoDefine a scope

Quando si richiede un token, è necessario definire un ambito. L'ambito determina i dati a cui l'app può accedere.  

Il modo più semplice per definire un ambito consiste `App ID URI` nel `.default`combinare le API Web desiderate con l'ambito . Questa definizione indica alla piattaforma di identità Microsoft che l'app richiede tutti gli ambiti impostati nel portale.

### <a name="android"></a>Android
```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
```

### <a name="ios"></a>iOS
```swift
let scopes = ["https://graph.microsoft.com/.default"]
```

### <a name="xamarin"></a>Xamarin
```csharp 
var scopes = new [] {"https://graph.microsoft.com/.default"};
```

## <a name="get-tokens"></a>Ottenere tokenGet tokens

### <a name="acquire-tokens-via-msal"></a>Acquisire token tramite MSAL

MSAL consente alle app di acquisire token in modo silenzioso e interattivo. Quando si `AcquireTokenSilent()` `AcquireTokenInteractive()`chiama o , MSAL restituisce un token di accesso per gli ambiti richiesti. Il modello corretto consiste nel fare una richiesta invisibile all'utente e quindi eseguire il rollback a una richiesta interattiva.

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
// TODO: Create an interactive callback to catch successful or failed requests.
sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());        
```

#### <a name="ios"></a>iOS

In primo luogo provare ad acquisire un token in silenzio:

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

Se MSAL `MSALErrorInteractionRequired`restituisce , provare ad acquisire i token in modo interattivo:

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

MSAL per iOS e macOS supporta vari modificatori per ottenere un token in modo interattivo o invisibile all'utente:
* [Parametri comuni per ottenere un tokenCommon parameters for getting a token](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALTokenParameters.html#/Configuration%20parameters)
* [Parametri per ottenere un token interattivo](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALInteractiveTokenParameters.html#/Configuring%20MSALInteractiveTokenParameters)
* [Parametri per ottenere un token invisibile all'utente](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALSilentTokenParameters.html)

#### <a name="xamarin"></a>Xamarin

Nell'esempio seguente viene illustrato il codice minimo per ottenere un token in modo interattivo. Nell'esempio viene utilizzato Microsoft Graph per leggere il profilo dell'utente.

```csharp
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

`AcquireTokenInteractive`dispone di un `scopes`solo parametro obbligatorio: . Il `scopes` parametro enumera le stringhe che definiscono gli ambiti per i quali è necessario un token. Se il token è per Microsoft Graph, è possibile trovare gli ambiti necessari nel riferimento API di ogni API di Microsoft Graph. Nel riferimento, vai alla sezione "Autorizzazioni". 

Ad esempio, per [elencare i contatti dell'utente,](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts)utilizzare l'ambito "Utente.Lettura", "Contatti.Lettura". Per ulteriori informazioni, vedere Informazioni di [riferimento sulle autorizzazioni di Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/permissions_reference).

In Android, è possibile specificare l'attività `PublicClientApplicationBuilder`padre quando si crea l'app utilizzando . Se non si specifica l'attività padre in quel momento, `.WithParentActivityOrWindow` è possibile specificarla in un secondo momento utilizzando come nella sezione seguente. Se si specifica l'attività padre, il token torna all'attività padre dopo l'interazione. Se non viene specificato, la `.ExecuteAsync()` chiamata genera un'eccezione.

#### <a name="specific-optional-parameters-in-msalnet"></a>Parametri facoltativi specifici in MSAL.NET

Nelle sezioni seguenti vengono illustrati i parametri facoltativi di MSAL.NET. 

##### <a name="withprompt"></a>WithPrompt

Il `WithPrompt()` parametro controlla l'interattività con l'utente specificando un prompt.

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

La classe definisce le costanti seguenti:The class defines the following constants:

- `SelectAccount`forza il servizio token di sicurezza (STS) per presentare la finestra di dialogo di selezione dell'account. La finestra di dialogo contiene gli account per i quali l'utente dispone di una sessione. È possibile utilizzare questa opzione quando si desidera consentire all'utente di scegliere tra identità diverse. Questa opzione spinge MSAL a inviare `prompt=select_account` al provider di identità. 
    
    La `SelectAccount` costante è l'impostazione predefinita e fornisce in modo efficace la migliore esperienza possibile in base alle informazioni disponibili. Le informazioni disponibili possono includere l'account, la presenza di una sessione per l'utente e così via. Non modificare questa impostazione predefinita a meno che non si abbia un buon motivo per farlo.
- `Consent`consente di richiedere all'utente il consenso anche se il consenso è stato concesso in precedenza. In questo caso, `prompt=consent` MSAL invia al provider di identità. 

    È possibile utilizzare `Consent` la costante nelle applicazioni incentrate sulla sicurezza in cui la governance dell'organizzazione richiede agli utenti di visualizzare la finestra di dialogo di consenso ogni volta che utilizzano l'applicazione.
- `ForceLogin`consente al servizio di richiedere all'utente le credenziali anche se il prompt non è necessario. 

    Questa opzione può essere utile se l'acquisizione del token non riesce e si desidera consentire all'utente di accedere di nuovo. In questo caso, `prompt=login` MSAL invia al provider di identità. È possibile utilizzare questa opzione in applicazioni incentrate sulla sicurezza in cui la governance dell'organizzazione richiede all'utente di accedere ogni volta che accedono a parti specifiche dell'applicazione.
- `Never`è solo per .NET 4.5 e Windows Runtime (WinRT). Questa costante non richiede all'utente, ma tenterà di utilizzare il cookie memorizzato nella visualizzazione Web incorporata nascosta. Per ulteriori informazioni, consultate [Utilizzo dei browser Web con MSAL.NET.](https://docs.microsoft.com/azure/active-directory/develop/msal-net-web-browsers)

    Se questa opzione `AcquireTokenInteractive` ha esito negativo, viene generata un'eccezione per notificare che è necessaria un'interazione dell'interfaccia utente. Quindi è necessario `Prompt` utilizzare un altro parametro.
- `NoPrompt`non invia una richiesta al provider di identità. 

    Questa opzione è utile solo per i criteri di modifica-profilo in Azure Active Directory B2C. Per ulteriori informazioni, vedere [Specifiche B2C](https://aka.ms/msal-net-b2c-specificities).

##### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Utilizzare `WithExtraScopeToConsent` il modificatore in uno scenario avanzato in cui si desidera che l'utente fornisca il consenso anticipato a diverse risorse. È possibile utilizzare questo modificatore quando non si desidera utilizzare il consenso incrementale, che viene normalmente utilizzato con MSAL.NET o Microsoft Identity Platform 2.0.You can use this modifier when you don't want to use incremental consent, which is normally used with MSAL.NET or Microsoft identity platform 2.0. Per ulteriori informazioni, consultate [Richiedere all'utente il consenso in anticipo per diverse risorse.](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources)

Ecco un esempio di codice: 

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

##### <a name="other-optional-parameters"></a>Altri parametri facoltativi

Per informazioni sugli altri `AcquireTokenInteractive`parametri facoltativi per , vedere la [documentazione di riferimento per AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods).

### <a name="acquire-tokens-via-the-protocol"></a>Acquisire token tramite il protocollo

Non è consigliabile usare direttamente il protocollo per ottenere i token. In tal caso, l'app non supporterà alcuni scenari che implicano L'accesso Single Sign-On (SSO), la gestione dei dispositivi e l'accesso condizionale.

Quando si usa il protocollo per ottenere i token per le app per dispositivi mobili, effettuare due richieste:When you use the protocol to get tokens for mobile apps, make two requests: 

* Ottenere un codice di autorizzazione.
* Scambiare il codice per un token.

#### <a name="get-an-authorization-code"></a>Ottenere un codice di autorizzazione

```Text
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=<CLIENT_ID>
&response_type=code
&redirect_uri=<ENCODED_REDIRECT_URI>
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2F.default
&state=12345
```

#### <a name="get-access-and-refresh-the-token"></a>Ottenere l'accesso e aggiornare il tokenGet access and refresh the token

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
> [Chiamata a un'API Web](scenario-mobile-call-api.md)
