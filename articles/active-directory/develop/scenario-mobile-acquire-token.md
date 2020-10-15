---
title: Acquisire un token per chiamare un'API Web (app per dispositivi mobili) | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come creare un'app per dispositivi mobili che chiama API Web Ottenere un token per l'app.
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
ms.openlocfilehash: dfccc274ef920c59d39c160055ab27a6900c839c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88141279"
---
# <a name="get-a-token-for-a-mobile-app-that-calls-web-apis"></a>Ottenere un token per un'app per dispositivi mobili che chiama API Web

Prima che l'app possa chiamare le API Web protette, è necessario un token di accesso. Questo articolo illustra il processo di ottenimento di un token tramite Microsoft Authentication Library (MSAL).

## <a name="define-a-scope"></a>Definire un ambito

Quando si richiede un token, è necessario definire un ambito. L'ambito determina a quali dati può accedere l'app.

Il modo più semplice per definire un ambito consiste nel combinare le API Web desiderate `App ID URI` con l'ambito `.default` . Questa definizione indica alla piattaforma di identità Microsoft che l'app richiede tutti gli ambiti impostati nel portale.

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

## <a name="get-tokens"></a>Ottenere i token

### <a name="acquire-tokens-via-msal"></a>Acquisire token tramite MSAL

MSAL consente alle app di acquisire i token in modo invisibile all'utente e in modo interattivo. Quando si chiama `AcquireTokenSilent()` o `AcquireTokenInteractive()` , MSAL restituisce un token di accesso per gli ambiti richiesti. Il modello corretto consiste nel creare una richiesta invisibile all'utente e quindi eseguire il fallback a una richiesta interattiva.

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

Provare prima ad acquisire un token in modo invisibile all'utente:

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

        // Access token to call the web API
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

    // Access token to call the web API
    let accessToken = authResult.accessToken
}
```

Se MSAL restituisce `MSALErrorInteractionRequired` , provare ad acquisire i token in modo interattivo:

```objc
UIViewController *viewController = ...; // Pass a reference to the view controller that should be used when getting a token interactively
MSALWebviewParameters *webParameters = [[MSALWebviewParameters alloc] initWithAuthPresentationViewController:viewController];
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
let webviewParameters = MSALWebviewParameters(authPresentationViewController: viewController)
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
* [Parametri comuni per ottenere un token](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALTokenParameters.html#/Configuration%20parameters)
* [Parametri per ottenere un token interattivo](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALInteractiveTokenParameters.html#/Configuring%20MSALInteractiveTokenParameters)
* [Parametri per ottenere un token invisibile all'utente](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALSilentTokenParameters.html)

#### <a name="xamarin"></a>Xamarin

Nell'esempio seguente viene illustrato il codice minimo per ottenere un token in modo interattivo. Nell'esempio viene usato Microsoft Graph per leggere il profilo dell'utente.

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

`AcquireTokenInteractive` ha un solo parametro obbligatorio: `scopes` . Il `scopes` parametro enumera le stringhe che definiscono gli ambiti per i quali è richiesto un token. Se il token è per Microsoft Graph, è possibile trovare gli ambiti necessari nel riferimento all'API di ogni API Microsoft Graph. Nel riferimento passare alla sezione "autorizzazioni".

Per [elencare i contatti dell'utente](/graph/api/user-list-contacts), ad esempio, usare l'ambito "User. Read", "Contacts. Read". Per altre informazioni, vedere le [Informazioni di riferimento per le autorizzazioni dell'API Microsoft Graph](/graph/permissions-reference).

In Android è possibile specificare l'attività padre quando si crea l'app usando `PublicClientApplicationBuilder` . Se non si specifica l'attività padre in quel momento, è possibile specificarla in un secondo momento usando `.WithParentActivityOrWindow` come nella sezione seguente. Se si specifica l'attività padre, il token torna a tale attività padre dopo l'interazione. Se non viene specificato, la `.ExecuteAsync()` chiamata genera un'eccezione.

#### <a name="specific-optional-parameters-in-msalnet"></a>Parametri facoltativi specifici in MSAL.NET

Le sezioni seguenti illustrano i parametri facoltativi in MSAL.NET.

##### <a name="withprompt"></a>WithPrompt

Il `WithPrompt()` parametro controlla l'interattività con l'utente specificando una richiesta.

![Immagine che mostra i campi nella struttura dei messaggi di richiesta. Questi valori costanti controllano l'interattività con l'utente definendo il tipo di messaggio di richiesta visualizzato dal parametro WithPrompt ().](https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png)

La classe definisce le costanti seguenti:

- `SelectAccount` impone al servizio token di sicurezza (STS) di presentare la finestra di dialogo di selezione dell'account. Nella finestra di dialogo sono contenuti gli account per i quali l'utente dispone di una sessione. È possibile utilizzare questa opzione quando si desidera consentire all'utente di scegliere tra identità diverse. Questa opzione consente a MSAL di inviare `prompt=select_account` al provider di identità.

    La `SelectAccount` costante è l'impostazione predefinita e fornisce efficacemente la migliore esperienza possibile in base alle informazioni disponibili. Le informazioni disponibili possono includere l'account, la presenza di una sessione per l'utente e così via. Non modificare questa impostazione predefinita a meno che non si disponga di un buon motivo per eseguire questa operazione.
- `Consent` consente di richiedere il consenso dell'utente anche se è stato concesso il consenso prima. In questo caso, MSAL invia `prompt=consent` al provider di identità.

    Potrebbe essere necessario utilizzare la `Consent` costante nelle applicazioni con stato attivo per la sicurezza, in cui la governance dell'organizzazione richiede agli utenti di visualizzare la finestra di dialogo di consenso ogni volta che viene utilizzata l'applicazione.
- `ForceLogin` consente al servizio di richiedere all'utente le credenziali anche se il messaggio non è necessario.

    Questa opzione può essere utile se l'acquisizione del token ha esito negativo e si vuole consentire all'utente di accedere di nuovo. In questo caso, MSAL invia `prompt=login` al provider di identità. Questa opzione può essere utilizzata nelle applicazioni con stato attivo per la sicurezza, in cui la governance dell'organizzazione richiede che l'utente acceda ogni volta che accede a parti specifiche dell'applicazione.
- `Never` è solo per .NET 4,5 e Windows Runtime (WinRT). Questa costante non richiede l'intervento dell'utente, ma tenterà di usare il cookie archiviato nella visualizzazione Web incorporata nascosta. Per altre informazioni, vedere [uso di Web browser con MSAL.NET](./msal-net-web-browsers.md).

    Se questa opzione ha esito negativo, `AcquireTokenInteractive` genera un'eccezione per notificare che è necessaria un'interazione dell'interfaccia utente. Quindi è necessario usare un altro `Prompt` parametro.
- `NoPrompt` non invia una richiesta al provider di identità.

    Questa opzione è utile solo per i criteri di modifica del profilo in Azure Active Directory B2C. Per altre informazioni, vedere [specifiche B2C](https://aka.ms/msal-net-b2c-specificities).

##### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Usare il `WithExtraScopeToConsent` modificatore in uno scenario avanzato in cui si vuole che l'utente fornisca il consenso iniziale a diverse risorse. È possibile usare questo modificatore quando non si vuole usare il consenso incrementale, che in genere viene usato con MSAL.NET o Microsoft Identity Platform 2,0. Per altre informazioni, vedere [Ricevere il consenso utente in anticipo per numerose risorse](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources).

Ecco un esempio di codice:

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

##### <a name="other-optional-parameters"></a>Altri parametri facoltativi

Per informazioni sugli altri parametri facoltativi per `AcquireTokenInteractive` , vedere la [documentazione di riferimento per AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods).

### <a name="acquire-tokens-via-the-protocol"></a>Acquisire token tramite il protocollo

Non è consigliabile usare direttamente il protocollo per ottenere i token. In tal caso, l'app non supporterà alcuni scenari che coinvolgono Single Sign-On (SSO), la gestione dei dispositivi e l'accesso condizionale.

Quando si usa il protocollo per ottenere i token per le app per dispositivi mobili, effettuare due richieste:

* Ottenere un codice di autorizzazione.
* Scambia il codice per un token.

#### <a name="get-an-authorization-code"></a>Ottenere un codice di autorizzazione

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=<CLIENT_ID>
&response_type=code
&redirect_uri=<ENCODED_REDIRECT_URI>
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2F.default
&state=12345
```

#### <a name="get-access-and-refresh-the-token"></a>Ottenere l'accesso e aggiornare il token

```HTTP
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