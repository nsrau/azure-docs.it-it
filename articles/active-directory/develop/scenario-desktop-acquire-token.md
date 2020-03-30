---
title: Acquisire un token per chiamare un'API Web (app desktop) Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come creare un'app desktop che chiama le API Web per acquisire un token per l'app
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 636c7c654b98ced5f93c3ace0e4a99bfc9bf7def
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262607"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token"></a>App desktop che chiama le API Web: acquisire un token

Dopo aver compilato un'istanza dell'applicazione client pubblica, la userai per acquisire un token che userai per chiamare un'API Web.

## <a name="recommended-pattern"></a>Modello consigliato

L'API Web è `scopes`definita dal relativo oggetto . Qualunque sia l'esperienza fornita nell'applicazione, il modello da usare è:

- Tentativo sistematico di ottenere un token `AcquireTokenSilent`dalla cache dei token chiamando .
- Se la chiamata ha `AcquireToken` esito negativo, utilizzare il flusso `AcquireTokenXX`che si desidera utilizzare, rappresentato qui da .

# <a name="net"></a>[.NET](#tab/dotnet)

### <a name="in-msalnet"></a>In MSAL.NET

```csharp
AuthenticationResult result;
var accounts = await app.GetAccountsAsync();
IAccount account = ChooseAccount(accounts); // for instance accounts.FirstOrDefault
                                            // if the app manages is at most one account  
try
{
 result = await app.AcquireTokenSilent(scopes, account)
                   .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
  result = await app.AcquireTokenXX(scopes, account)
                    .WithOptionalParameterXXX(parameter)
                    .ExecuteAsync();
}
```

# <a name="java"></a>[Java](#tab/java)

```java

Set<IAccount> accountsInCache = pca.getAccounts().join();
// Take first account in the cache. In a production application, you would filter
// accountsInCache to get the right account for the user authenticating.
IAccount account = accountsInCache.iterator().next();

IAuthenticationResult result;
try {
    SilentParameters silentParameters =
            SilentParameters
                    .builder(SCOPE, account)
                    .build();

    // try to acquire token silently. This call will fail since the token cache
    // does not have any data for the user you are trying to acquire a token for
    result = pca.acquireTokenSilently(silentParameters).join();
} catch (Exception ex) {
    if (ex.getCause() instanceof MsalException) {

        InteractiveRequestParameters parameters = InteractiveRequestParameters
                .builder(new URI("http://localhost"))
                .scopes(SCOPE)
                .build();

        // Try to acquire a token interactively with system browser. If successful, you should see
        // the token and account information printed out to console
        result = pca.acquireToken(parameters).join();
    } else {
        // Handle other exceptions accordingly
        throw ex;
    }
}
return result;

```

# <a name="python"></a>[Python](#tab/python)

```Python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_xxx(scopes=config["scope"])
```

# <a name="macos"></a>[Macos](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>In MSAL per iOS e macOS

Objective-C:

```objc
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];

MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {

    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
    }
}];
```
Swift:

```swift
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
}
```
---

Ecco i vari modi per acquisire token in un'applicazione desktop.

## <a name="acquire-a-token-interactively"></a>Acquisire un token in modo interattivoAcquire a token interactively

The following example shows minimal code to get a token interactively for reading the user's profile with Microsoft Graph.

# <a name="net"></a>[.NET](#tab/dotnet)
### <a name="in-msalnet"></a>In MSAL.NET

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

### <a name="mandatory-parameters"></a>Parametri obbligatori

`AcquireTokenInteractive`dispone di un ``scopes``solo parametro obbligatorio, , che contiene un'enumerazione di stringhe che definiscono gli ambiti per i quali è necessario un token. Se il token è per Microsoft Graph, gli ambiti necessari sono disponibili nel riferimento API di ogni API di Microsoft Graph nella sezione denominata "Autorizzazioni". Ad esempio, per [elencare i contatti dell'utente,](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts)è necessario utilizzare l'ambito "User.Read", "Contacts.Read". Per ulteriori informazioni, vedere Informazioni di [riferimento sulle autorizzazioni di Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/permissions_reference).

In Android, è inoltre necessario specificare `.WithParentActivityOrWindow`l'attività padre utilizzando , come illustrato, in modo che il token torni a tale attività padre dopo l'interazione. Se non viene specificato, viene generata `.ExecuteAsync()`un'eccezione quando si chiama .

### <a name="specific-optional-parameters-in-msalnet"></a>Parametri facoltativi specifici in MSAL.NET

#### <a name="withparentactivityorwindow"></a>WithParentActivityOrWindow

L'interfaccia utente è importante perché è interattiva. `AcquireTokenInteractive`dispone di un parametro facoltativo specifico che può specificare, per le piattaforme che lo supportano, l'interfaccia utente padre. Se utilizzato in un'applicazione desktop, `.WithParentActivityOrWindow` ha un tipo diverso, che dipende dalla piattaforma.

```csharp
// net45
WithParentActivityOrWindow(IntPtr windowPtr)
WithParentActivityOrWindow(IWin32Window window)

// Mac
WithParentActivityOrWindow(NSWindow window)

// .Net Standard (this will be on all platforms at runtime, but only on NetStandard at build time)
WithParentActivityOrWindow(object parent).
```

Osservazioni:

- In .NET Standard, `object` `Activity` il previsto `UIViewController` è su `NSWindow` Android, su `IWin32Window` `IntPr` iOS, su MAC e o su Windows.
- In Windows, è `AcquireTokenInteractive` necessario chiamare dal thread dell'interfaccia utente in modo che il browser incorporato ottenga il contesto di sincronizzazione dell'interfaccia utente appropriato. La non chiamata dal thread dell'interfaccia utente potrebbe causare il pump dei messaggi in modo corretto e gli scenari di deadlock con l'interfaccia utente. Un modo per chiamare le librerie di autenticazione Microsoft (MSAL) dal thread dell'interfaccia utente se non si è già nel thread dell'interfaccia utente consiste nell'utilizzare il `Dispatcher` in WPFWPF.
- Se si usa WPFWPF, per ottenere una finestra da `WindowInteropHelper.Handle` un controllo WPFWPF, è possibile usare la classe . Quindi la chiamata proviene`this`da un controllo WPF ( ):

  ```csharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>WithPrompt

`WithPrompt()`viene utilizzato per controllare l'interattività con l'utente specificando un prompt.

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

La classe definisce le costanti seguenti:The class defines the following constants:

- ``SelectAccount``forza il servizio token di protezione a presentare la finestra di dialogo di selezione dell'account contenente gli account per i quali l'utente dispone di una sessione. Questa opzione è utile quando gli sviluppatori di applicazioni desiderano consentire agli utenti di scegliere tra identità diverse. Questa opzione spinge MSAL a inviare ``prompt=select_account`` al provider di identità. Questa opzione è l'impostazione predefinita. Fa un buon lavoro di fornire la migliore esperienza possibile in base alle informazioni disponibili, come l'account e la presenza di una sessione per l'utente. Non cambiarlo a meno che tu non abbia buone ragioni per farlo.
- ``Consent``consente allo sviluppatore dell'applicazione di imporre all'utente di ricevere il consenso, anche se il consenso è stato concesso in precedenza. In questo caso, `prompt=consent` MSAL invia al provider di identità. Questa opzione può essere utilizzata in alcune applicazioni incentrate sulla sicurezza in cui la governance dell'organizzazione richiede che all'utente venga visualizzata la finestra di dialogo di consenso ogni volta che viene utilizzata l'applicazione.
- ``ForceLogin``consente allo sviluppatore dell'applicazione di richiedere le credenziali all'utente, anche se questa richiesta dell'utente potrebbe non essere necessaria. Questa opzione può essere utile per consentire all'utente di accedere di nuovo se l'acquisizione di un token non riesce. In questo caso, `prompt=login` MSAL invia al provider di identità. A volte viene utilizzato in applicazioni incentrate sulla sicurezza in cui la governance dell'organizzazione richiede che l'utente si rifirmitempo ogni volta che accedono a parti specifiche di un'applicazione.
- ``Never``(solo per .NET 4.5 e WinRT) non richiede all'utente, ma tenta di utilizzare il cookie memorizzato nella visualizzazione Web incorporata nascosta. Per ulteriori informazioni, vedere Visualizzazioni Web in MSAL.NET. L'utilizzo di questa opzione potrebbe non riuscire. In tal `AcquireTokenInteractive` caso, genera un'eccezione per notificare che è necessaria un'interazione dell'interfaccia utente. È necessario utilizzare un `Prompt` altro parametro.
- ``NoPrompt``non invierà alcuna richiesta al provider di identità. Questa opzione è utile solo per i criteri di modifica b2C di Azure Active Directory (Azure AD). Per altre informazioni, vedere Specifiche di Azure AD B2C .For more information, see [Azure AD B2C specifics](https://aka.ms/msal-net-b2c-specificities).

#### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Questo modificatore viene utilizzato in uno scenario avanzato in cui si desidera che l'utente pre-acconsenta a diverse risorse in anticipo e non si desidera utilizzare il consenso incrementale, che viene in genere utilizzato con MSAL.NET/the piattaforma di identità Microsoft. Per ulteriori informazioni, consultate [Richiedere all'utente il consenso in anticipo per diverse risorse.](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources)

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>WithCustomWebUi (informazioni in lingua inlinguata in lingua

Un'interfaccia utente Web è un meccanismo per richiamare un browser. Questo meccanismo può essere un controllo WebBrowser dell'interfaccia utente dedicato o un modo per delegare l'apertura del browser.
MSAL fornisce le implementazioni dell'interfaccia utente Web per la maggior parte delle piattaforme, ma ci sono casi in cui è possibile ospitare il browser manualmente:

- Piattaforme non esplicitamente coperte da MSAL, ad esempio Blazor, Unity e Mono sui desktop.
- Si vuole testare l'interfaccia utente per testare l'applicazione e usare un browser automatizzato che può essere usato con Selenium.You want to UI test your application and use an automated browser that can be used with Selenium.
- Il browser e l'app che esegue MSAL sono in processi separati.

##### <a name="at-a-glance"></a>Riepilogo

A tale scopo, si `start Url`assegna a MSAL , che deve essere visualizzato in un browser di scelta in modo che l'utente finale possa immettere elementi quali il nome utente.
Al termine dell'autenticazione, l'app deve `end Url`passare nuovamente a MSAL , che contiene un codice fornito da Azure AD.
L'host `end Url` di `redirectUri`è sempre . Per `end Url`intercettare , effettuare una delle seguenti operazioni:

- Monitorare i `redirect Url` reindirizzamenti del browser fino a quando non viene colpito.
- Fare in modo che il browser eseridi a un URL, che viene monitorato.

##### <a name="withcustomwebui-is-an-extensibility-point"></a>WithCustomWebUi è un punto di estendibilità

`WithCustomWebUi`è un punto di estendibilità che è possibile utilizzare per fornire la propria interfaccia utente nelle applicazioni client pubbliche. È inoltre possibile consentire all'utente di passare attraverso l'endpoint /Authorize del provider di identità e consentire loro di accedere e acconsentire. MSAL.NET quindi possibile riscattare il codice di autenticazione e ottenere un token. Ad esempio, viene utilizzato in Visual Studio per avere applicazioni elettroni (ad esempio, Visual Studio Feedback) forniscono l'interazione web, ma lasciare che MSAL.NET eseguire la maggior parte del lavoro. Puoi anche usarlo se vuoi fornire l'automazione dell'interfaccia utente. Nelle applicazioni client pubbliche, MSAL.NET utilizza lo standard PKCE (Proof Key for Code Exchange) per garantire il rispetto della sicurezza. Solo MSAL.NET possibile riscattare il codice. Per ulteriori informazioni, vedere [RFC 7636 - Proof Key for Code Exchange da parte di client pubblici OAuth](https://tools.ietf.org/html/rfc7636).

  ```csharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="use-withcustomwebui"></a>Utilizzare WithCustomWebUi

Per `.WithCustomWebUI`utilizzare , attenersi alla seguente procedura.

  1. Implementare l'interfaccia `ICustomWebUi`. Per ulteriori informazioni, vedere [questo sito Web](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70). Implementare `AcquireAuthorizationCodeAsync`un metodo e accettare l'URL del codice di autorizzazione calcolato da MSAL.NET. Quindi consentire all'utente di passare attraverso l'interazione con il provider di identità e restituire l'URL con cui il provider di identità avrebbe chiamato l'implementazione insieme al codice di autorizzazione. In caso di problemi, l'implementazione deve generare un'eccezione `MsalExtensionException` per cooperare bene con MSAL.
  2. Nella `AcquireTokenInteractive` chiamata usare `.WithCustomUI()` il modificatore che passa l'istanza dell'interfaccia utente Web personalizzata.

     ```csharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation-seleniumwebui"></a>Esempi di implementazione di ICustomWebUi nell'automazione di test: SeleniumWebUI

Il team MSAL.NET ha riscritto i test dell'interfaccia utente per usare questo meccanismo di estendibilità. Se sei interessato, guarda la classe [SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160) nel codice sorgente di MSAL.NET.

##### <a name="provide-a-great-experience-with-systemwebviewoptions"></a>Offrire un'esperienza eccezionale con SystemWebViewOptions

Da MSAL.NET 4.1 [`SystemWebViewOptions`](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.systemwebviewoptions?view=azure-dotnet)è possibile specificare:

- URI da utilizzare`BrowserRedirectError`per ( ) o`HtmlMessageError`il frammento HTML da visualizzare ( ) in caso di errori di accesso o consenso nel browser Web di sistema.
- URI da utilizzare`BrowserRedirectSuccess`per ( ) o`HtmlMessageSuccess`il frammento HTML da visualizzare ( ) in caso di accesso riuscito o consenso.
- Azione da eseguire per avviare il browser di sistema. È possibile fornire la propria `OpenBrowserAsync` implementazione impostando il delegato. La classe fornisce inoltre un'implementazione `OpenWithEdgeBrowserAsync` `OpenWithChromeEdgeBrowserAsync` predefinita per due browser: e rispettivamente per Microsoft Edge e [Microsoft Edge on Chromium.](https://www.windowscentral.com/faq-edge-chromium)

Per usare questa struttura, scrivere qualcosa di simile all'esempio seguente:To use this structure, write something like the following example:

```csharp
IPublicClientApplication app;
...

options = new SystemWebViewOptions
{
 HtmlMessageError = "<b>Sign-in failed. You can close this tab ...</b>",
 BrowserRedirectSuccess = "https://contoso.com/help-for-my-awesome-commandline-tool.html"
};

var result = app.AcquireTokenInteractive(scopes)
                .WithEmbeddedWebView(false)       // The default in .NET Core
                .WithSystemWebViewOptions(options)
                .Build();
```

#### <a name="other-optional-parameters"></a>Altri parametri facoltativi

Per ulteriori informazioni su tutti `AcquireTokenInteractive`gli altri parametri facoltativi per , vedere [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods).

# <a name="java"></a>[Java](#tab/java)

```java
private static IAuthenticationResult acquireTokenInteractive() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            InteractiveRequestParameters parameters = InteractiveRequestParameters
                    .builder(new URI("http://localhost"))
                    .scopes(SCOPE)
                    .build();

            // Try to acquire a token interactively with system browser. If successful, you should see
            // the token and account information printed out to console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

MSAL Python non fornisce direttamente un metodo di token di acquisizione interattivo. Al contrario, richiede all'applicazione di inviare una richiesta di autorizzazione nella relativa implementazione del flusso di interazione dell'utente per ottenere un codice di autorizzazione. Questo codice può quindi `acquire_token_by_authorization_code` essere passato al metodo per ottenere il token.

```Python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_authorization_code(
         request.args['code'],
         scopes=config["scope"])    

```

# <a name="macos"></a>[Macos](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>In MSAL per iOS e macOS

Objective-C:

```objc
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:[MSALWebviewParameters new]];
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
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: MSALWebviewParameters())
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in

    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }

    // Get access token from result
    let accessToken = authResult.accessToken
})
```
---

## <a name="integrated-windows-authentication"></a>Autenticazione integrata di Windows

Per accedere a un utente di dominio in un computer aggiunto a un dominio o ad Azure AD, usare l'autenticazione integrata di Windows (IWA).

### <a name="constraints"></a>Vincoli

- L'autenticazione integrata di Windows è utilizzabile solo per gli utenti *federati,* ovvero per gli utenti creati in Active Directory e supportati da Azure AD. Gli utenti creati direttamente in Azure AD senza il backup di Active Directory, noto come utenti *gestiti,* non possono usare questo flusso di autenticazione. Questa limitazione non influisce sul flusso di nome utente e password.
- IWA è per le app scritte per le piattaforme .NET Framework, .NET Core e UWP (Universal Windows Platform).
- IWA non ignora l'autenticazione a più fattori (MFA). Se è configurato l'autenticazione a più fattori, IWA potrebbe non riuscire se è necessaria una richiesta di verifica dell'autenticazione a più fattori, perché l'autenticazione a più fattori richiede l'interazione dell'utente.
  > [!NOTE]
  > Questo è difficile. IWA non è interattiva, ma l'autenticazione a più fattori richiede l'interattività dell'utente. Non si controlla quando il provider di identità richiede l'autenticazione a più fattori da eseguire, l'amministratore tenant. Dalle nostre osservazioni, l'autenticazione a più fattori è necessaria quando si accede da un paese diverso, quando non si è connessi tramite VPN a una rete aziendale e talvolta anche quando si è connessi tramite VPN. Non aspettatevi un insieme deterministico di regole. Azure AD usa l'ia per sapere continuamente se è necessaria l'autenticazione a più fattori. Eseguire il rollback a un prompt utente come l'autenticazione interattiva o il flusso del codice del dispositivo se IWA ha esito negativo.

- L'autorità `PublicClientApplicationBuilder` passata deve essere:
  - Il tenant nel `https://login.microsoftonline.com/{tenant}/`formato `tenant` , dove è il GUID che rappresenta l'ID tenant o un dominio associato al tenant.
  - Per tutti gli account `https://login.microsoftonline.com/organizations/`aziendali e dell'istituto di istruzione: .
  - Gli account personali Microsoft non sono supportati. Non è possibile usare i tenant /common o /consumers.

- Poiché l'autenticazione integrata di Windows è un flusso invisibile all'utente:Because Integrated Windows Authentication is a silent flow:
  - L'utente dell'applicazione deve aver precedentemente acconsentito all'utilizzo dell'applicazione.
  - In alternativa, l'amministratore tenant deve aver acconsentito in precedenza a tutti gli utenti nel tenant per utilizzare l'applicazione.
  - In altre parole:
    - Uno sviluppatore ha selezionato il pulsante **Concedi** nel portale di Azure per se stessi.
    - In alternativa, un amministratore tenant ha selezionato il **pulsante Concedi/revoca il consenso dell'amministratore per** il dominio tenant nella scheda **Autorizzazioni API** della registrazione per l'applicazione. Per ulteriori informazioni, consultate [Aggiungere autorizzazioni per accedere alle API Web.](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis#add-permissions-to-access-web-apis)
    - In alternativa, è stato fornito un modo per consentire agli utenti di acconsentire all'applicazione. Per ulteriori informazioni, vedere [Richiesta del consenso dei singoli utenti](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-individual-user-consent).
    - In alternativa, è stato fornito un modo per l'amministratore tenant di acconsentire all'applicazione. Per ulteriori informazioni, vedere [Consenso dell'amministratore](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-consent-for-an-entire-tenant).

- Questo flusso è abilitato per le app desktop .NET, .NET Core e UWP.

Per ulteriori informazioni sul consenso, vedere Autorizzazioni e consenso della piattaforma di [identità Microsoft](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent).

### <a name="learn-how-to-use-it"></a>Informazioni su come usare l'SDK

# <a name="net"></a>[.NET](#tab/dotnet)

In MSAL.NET, è necessario utilizzare:

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

In genere è necessario`scopes`un solo parametro ( ). A seconda del modo in cui l'amministratore di Windows ha configurato i criteri, le applicazioni nel computer Windows potrebbero non essere autorizzate a cercare l'utente connesso. In tal caso, utilizzare `.WithUsername()`un secondo metodo, e passare il nome utente dell'utente `joe@contoso.com`connesso come formato UPN, ad esempio . In .NET Core, solo l'overload che prende il nome utente è disponibile perché la piattaforma .NET Core non può chiedere il nome utente al sistema operativo.

L'esempio seguente presenta il caso più recente, con spiegazioni sul tipo di eccezioni che è possibile ottenere e le relative attenuazioni.

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app = PublicClientApplicationBuilder
      .Create(clientId)
      .WithAuthority(authority)
      .Build();

 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
      .ExecuteAsync();
 }
 else
 {
  try
  {
   result = await app.AcquireTokenByIntegratedWindowsAuth(scopes)
      .ExecuteAsync(CancellationToken.None);
  }
  catch (MsalUiRequiredException ex)
  {
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'.Send an interactive authorization request for this user and resource.

   // you need to get user consent first. This can be done, if you are not using .NET Core (which does not have any Web UI)
   // by doing (once only) an AcquireToken interactive.

   // If you are using .NET core or don't want to do an AcquireTokenInteractive, you might want to suggest the user to navigate
   // to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read

   // AADSTS50079: The user is required to use multi-factor authentication.
   // There is no mitigation - if MFA is configured for your tenant and AAD decides to enforce it,
   // you need to fallback to an interactive flows such as AcquireTokenInteractive or AcquireTokenByDeviceCode
   }
   catch (MsalServiceException ex)
   {
    // Kind of errors you could have (in ex.Message)

    // MsalServiceException: AADSTS90010: The grant type is not supported over the /common or /consumers endpoints. Please use the /organizations or tenant-specific endpoint.
    // you used common.
    // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted or otherwise organizations

    // MsalServiceException: AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
    // Explanation: this can happen if your application was not registered as a public client application in Azure AD
    // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   }
   catch (MsalClientException ex)
   {
      // Error Code: unknown_user Message: Could not identify logged in user
      // Explanation: the library was unable to query the current Windows logged-in user or this user is not AD or AAD
      // joined (work-place joined users are not supported).

      // Mitigation 1: on UWP, check that the application has the following capabilities: Enterprise Authentication,
      // Private Networks (Client and Server), User Account Information

      // Mitigation 2: Implement your own logic to fetch the username (e.g. john@contoso.com) and use the
      // AcquireTokenByIntegratedWindowsAuth form that takes in the username

      // Error Code: integrated_windows_auth_not_supported_managed_user
      // Explanation: This method relies on an a protocol exposed by Active Directory (AD). If a user was created in Azure
      // Active Directory without AD backing ("managed" user), this method will fail. Users created in AD and backed by
      // AAD ("federated" users) can benefit from this non-interactive method of authentication.
      // Mitigation: Use interactive authentication
   }
 }


 Console.WriteLine(result.Account.Username);
}
```

Per l'elenco dei possibili modificatori su AcquireTokenByIntegratedWindowsAuthentication, vedere [AcquireTokenByIntegratedWindowsAuthParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder?view=azure-dotnet-preview#methods).

# <a name="java"></a>[Java](#tab/java)

Questo estratto proviene dagli [esempi di sviluppo Java MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

```Java
private static IAuthenticationResult acquireTokenIwa() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            IntegratedWindowsAuthenticationParameters parameters =
                    IntegratedWindowsAuthenticationParameters
                            .builder(SCOPE, USER_NAME)
                            .build();

            // Try to acquire a IWA. You will need to generate a Kerberos ticket.
            // If successful, you should see the token and account information printed out to
            // console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

Questo flusso non è ancora supportato in MSAL Python.

# <a name="macos"></a>[Macos](#tab/macOS)

Questo flusso non si applica a MacOS.

---

## <a name="username-and-password"></a>Nome utente e password

È inoltre possibile acquisire un token fornendo il nome utente e la password. Questo flusso è limitato e non consigliato, ma ci sono ancora casi d'uso in cui è necessario.

### <a name="this-flow-isnt-recommended"></a>Questo flusso non è consigliato

Questo flusso non è *consigliato* perché il fatto che l'applicazione chieda conferma a un utente per la password non è sicuro. Per ulteriori informazioni, vedere [Qual è la soluzione al problema crescente delle password?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). Il flusso preferito per l'acquisizione di un token in modo invisibile all'utente nei computer aggiunti a un dominio Windows è [Autenticazione integrata](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication)di Windows . È anche possibile usare il [flusso di codice](https://aka.ms/msal-net-device-code-flow)del dispositivo .

> [!NOTE]
> L'uso di un nome utente e di una password è utile in alcuni casi, ad esempio gli scenari DevOps.Using a username and password is useful in some cases, such as DevOps scenarios. Ma se vuoi usare un nome utente e una password in scenari interattivi in cui fornisci la tua interfaccia utente, pensa a come allontanarti da essa. Utilizzando un nome utente e una password, stai rinunciando a una serie di cose:
>
> - Principi fondamentali dell'identità moderna. Una password può essere sottoposta a phished e riprodotta perché un segreto condiviso può essere intercettato. È incompatibile con il senza password.
> - Gli utenti che devono eseguire l'autenticazione a più fattori non possono accedere perché non è presente alcuna interazione.
> - Gli utenti non possono eseguire L'accesso Single Sign-On (SSO).

### <a name="constraints"></a>Vincoli

Si applicano anche i seguenti vincoli:

- Il flusso di nome utente e password non è compatibile con l'accesso condizionale e l'autenticazione a più fattori. Di conseguenza, se l'app viene eseguita in un tenant di Azure AD in cui l'amministratore tenant richiede l'autenticazione a più fattori, non è possibile usare questo flusso. Molte organizzazioni lo fanno.
- Funziona solo per gli account di lavoro e dell'istituto di istruzione (non MSA).
- Il flusso è disponibile in .NET desktop e .NET Core, ma non in UWP.

### <a name="b2c-specifics"></a>Specifiche B2C

Per ulteriori informazioni, vedere [Resource Owner Password Credentials (ROPC) with B2C](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c).

### <a name="use-it"></a>Usalo

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication`contiene il `AcquireTokenByUsernamePassword`metodo .

L'esempio seguente presenta un caso semplificato.

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
       .WithAuthority(authority)
       .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAsync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password
    securePassword.AppendChar(c);  // keystroke by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                     securePassword)
                      .ExecuteAsync();
  }
  catch(MsalException)
  {
   // See details below
  }
 }
 Console.WriteLine(result.Account.Username);
}
```

L'esempio seguente presenta il caso più recente, con spiegazioni sul tipo di eccezioni che è possibile ottenere e le relative attenuazioni.

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
                                   .WithAuthority(authority)
                                   .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password keystroke
    securePassword.AppendChar(c);  // by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                    securePassword)
                    .ExecuteAsync();
  }
  catch (MsalUiRequiredException ex) when (ex.Message.Contains("AADSTS65001"))
  {
   // Here are the kind of error messages you could have, and possible mitigations

   // ------------------------------------------------------------------------
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'. Send an interactive authorization request for this user and resource.

   // Mitigation: you need to get user consent first. This can be done either statically (through the portal),
   /// or dynamically (but this requires an interaction with Azure AD, which is not possible with
   // the username/password flow)
   // Statically: in the portal by doing the following in the "API permissions" tab of the application registration:
   // 1. Click "Add a permission" and add all the delegated permissions corresponding to the scopes you want (for instance
   // User.Read and User.ReadBasic.All)
   // 2. Click "Grant/revoke admin consent for <tenant>") and click "yes".
   // Dynamically, if you are not using .NET Core (which does not have any Web UI) by
   // calling (once only) AcquireTokenInteractive.
   // remember that Username/password is for public client applications that is desktop/mobile applications.
   // If you are using .NET core or don't want to call AcquireTokenInteractive, you might want to:
   // - use device code flow (See https://aka.ms/msal-net-device-code-flow)
   // - or suggest the user to navigate to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ErrorCode: invalid_grant
   // SubError: basic_action
   // MsalUiRequiredException: AADSTS50079: The user is required to use multi-factor authentication.
   // The tenant admin for your organization has chosen to oblige users to perform multi-factor authentication.
   // Mitigation: none for this flow
   // Your application cannot use the Username/Password grant.
   // Like in the previous case, you might want to use an interactive flow (AcquireTokenInteractive()),
   // or Device Code Flow instead.
   // Note this is one of the reason why using username/password is not recommended;
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // Message = "AADSTS70002: Error validating credentials.
   // AADSTS50126: Invalid username or password
   // In the case of a managed user (user from an Azure AD tenant opposed to a
   // federated user, which would be owned
   // in another IdP through ADFS), the user has entered the wrong password
   // Mitigation: ask the user to re-enter the password
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // MsalServiceException: ADSTS50034: To sign into this application the account must be added to
   // the {domainName} directory.
   // or The user account does not exist in the {domainName} directory. To sign into this application,
   // the account must be added to the directory.
   // The user was not found in the directory
   // Explanation: wrong username
   // Mitigation: ask the user to re-enter the username.
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_request")
  {
   // ------------------------------------------------------------------------
   // AADSTS90010: The grant type is not supported over the /common or /consumers endpoints.
   // Please use the /organizations or tenant-specific endpoint.
   // you used common.
   // Mitigation: as explained in the message from Azure AD, the authority you use in the application needs
   // to be tenanted or otherwise "organizations". change the
   // "Tenant": property in the appsettings.json to be a GUID (tenant Id), or domain name (contoso.com)
   // if such a domain is registered with your tenant
   // or "organizations", if you want this application to sign-in users in any Work and School accounts.
   // ------------------------------------------------------------------------

  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "unauthorized_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS700016: Application with identifier '{clientId}' was not found in the directory '{domain}'.
   // This can happen if the application has not been installed by the administrator of the tenant or consented
   // to by any user in the tenant.
   // You may have sent your authentication request to the wrong tenant
   // Cause: The clientId in the appsettings.json might be wrong
   // Mitigation: check the clientId and the app registration
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
   // Explanation: this can happen if your application was not registered as a public client application in Azure AD
   // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException)
  {
   throw;
  }

  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user_type")
  {
   // Message = "Unsupported User Type 'Unknown'. Please see https://aka.ms/msal-net-up"
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "user_realm_discovery_failed")
  {
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user. That's for instance the case
   // if you use a phone number
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user")
  {
   // the username was probably empty
   // ex.Message = "Could not identify the user logged into the OS. See https://aka.ms/msal-net-iwa for details."
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "parsing_wstrust_response_failed")
  {
   // ------------------------------------------------------------------------
   // In the case of a Federated user (that is owned by a federated IdP, as opposed to a managed user owned in an Azure AD tenant)
   // ID3242: The security token could not be authenticated or authorized.
   // The user does not exist or has entered the wrong password
   // ------------------------------------------------------------------------
  }
 }

 Console.WriteLine(result.Account.Username);
}
```

Per ulteriori informazioni su tutti i modificatori che possono essere applicati a `AcquireTokenByUsernamePassword`, vedere [AcquireTokenByUsernamePasswordParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyusernamepasswordparameterbuilder?view=azure-dotnet-preview#methods).

# <a name="java"></a>[Java](#tab/java)

L'estratto seguente proviene dagli esempi di [sviluppo Java MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

```Java
private static IAuthenticationResult acquireTokenUsernamePassword() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();
        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            UserNamePasswordParameters parameters =
                    UserNamePasswordParameters
                            .builder(SCOPE, USER_NAME, USER_PASSWORD.toCharArray())
                            .build();
            // Try to acquire a token via username/password. If successful, you should see
            // the token and account information printed out to console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

Questo estratto proviene dagli [esempi di sviluppo MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/).

```Python
# Create a preferably long-lived app instance which maintains a token cache.
app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# The pattern to acquire a token looks like this.
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    logging.info("Account(s) exists in cache, probably with token too. Let's try.")
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    # See this page for constraints of Username Password Flow.
    # https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Username-Password-Authentication
    result = app.acquire_token_by_username_password(
        config["username"], config["password"], scopes=config["scope"])
```

# <a name="macos"></a>[Macos](#tab/macOS)

Questo flusso non è supportato in MSAL per macOS.

---

## <a name="command-line-tool-without-a-web-browser"></a>Strumento da riga di comando senza browser Web

### <a name="device-code-flow"></a>Flusso del codice del dispositivo

Se stai scrivendo uno strumento da riga di comando che non dispone di controlli Web e non puoi o non vuoi usare i flussi precedenti, devi usare il flusso di codice del dispositivo.

L'autenticazione interattiva con Azure AD richiede un Web browser. Per ulteriori informazioni, consultate [Utilizzo dei browser Web.](https://aka.ms/msal-net-uses-web-browser) Per autenticare gli utenti su dispositivi o sistemi operativi che non forniscono un Web browser, il flusso di codice del dispositivo consente all'utente di utilizzare un altro dispositivo, ad esempio un computer o un telefono cellulare, per accedere in modo interattivo. Utilizzando il flusso del codice del dispositivo, l'applicazione ottiene i token tramite un processo in due passaggi progettato per questi dispositivi o osi numerici. Esempi di tali applicazioni sono le applicazioni eseguite su iOT o strumenti della riga di comando (CLI). L'idea è che:

1. Ogni volta che è richiesta l'autenticazione dell'utente, l'app fornisce un codice per l'utente. All'utente viene chiesto di utilizzare un altro dispositivo, ad esempio uno smartphone `https://microsoft.com/devicelogin`connesso a Internet, per accedere a un URL, ad esempio . Quindi all'utente viene richiesto di immettere il codice. Fatto questo, la pagina web conduce l'utente attraverso una normale esperienza di autenticazione, che include le richieste di consenso e l'autenticazione a più fattori, se necessario.

2. Al termine dell'autenticazione, l'app della riga di comando riceve i token necessari tramite un canale di back e li usa per eseguire le chiamate API Web necessarie.

### <a name="use-it"></a>Usalo

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication`contiene un `AcquireTokenWithDeviceCode`metodo denominato .

```csharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

Questo metodo accetta come parametri:This method takes as parameters:

- Oggetto `scopes` per cui richiedere un token di accesso.
- Callback che riceve `DeviceCodeResult`il file .

  ![DeviceCodeResult proprietà](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

Il codice di esempio seguente presenta il caso più recente, con spiegazioni del tipo di eccezioni che è possibile ottenere e la relativa attenuazione.

```csharp
private const string ClientId = "<client_guid>";
private const string Authority = "https://login.microsoftonline.com/contoso.com";
private readonly string[] Scopes = new string[] { "user.read" };

static async Task<AuthenticationResult> GetATokenForGraph()
{
    IPublicClientApplication pca = PublicClientApplicationBuilder
            .Create(ClientId)
            .WithAuthority(Authority)
            .WithDefaultRedirectUri()
            .Build();

    var accounts = await pca.GetAccountsAsync();

    // All AcquireToken* methods store the tokens in the cache, so check the cache first
    try
    {
        return await pca.AcquireTokenSilent(Scopes, accounts.FirstOrDefault())
            .ExecuteAsync();
    }
    catch (MsalUiRequiredException ex)
    {
        // No token found in the cache or AAD insists that a form interactive auth is required (e.g. the tenant admin turned on MFA)
        // If you want to provide a more complex user experience, check out ex.Classification

        return await AcquireByDeviceCodeAsync(pca);
    }         
}

private async Task<AuthenticationResult> AcquireByDeviceCodeAsync(IPublicClientApplication pca)
{
    try
    {
        var result = await pca.AcquireTokenWithDeviceCode(scopes,
            deviceCodeResult =>
            {
                    // This will print the message on the console which tells the user where to go sign-in using
                    // a separate browser and the code to enter once they sign in.
                    // The AcquireTokenWithDeviceCode() method will poll the server after firing this
                    // device code callback to look for the successful login of the user via that browser.
                    // This background polling (whose interval and timeout data is also provided as fields in the
                    // deviceCodeCallback class) will occur until:
                    // * The user has successfully logged in via browser and entered the proper code
                    // * The timeout specified by the server for the lifetime of this code (typically ~15 minutes) has been reached
                    // * The developing application calls the Cancel() method on a CancellationToken sent into the method.
                    //   If this occurs, an OperationCanceledException will be thrown (see catch below for more details).
                    Console.WriteLine(deviceCodeResult.Message);
                return Task.FromResult(0);
            }).ExecuteAsync();

        Console.WriteLine(result.Account.Username);
        return result;
    }
    // TODO: handle or throw all these exceptions depending on your app
    catch (MsalServiceException ex)
    {
        // Kind of errors you could have (in ex.Message)

        // AADSTS50059: No tenant-identifying information found in either the request or implied by any provided credentials.
        // Mitigation: as explained in the message from Azure AD, the authoriy needs to be tenanted. you have probably created
        // your public client application with the following authorities:
        // https://login.microsoftonline.com/common or https://login.microsoftonline.com/organizations

        // AADSTS90133: Device Code flow is not supported under /common or /consumers endpoint.
        // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted

        // AADSTS90002: Tenant <tenantId or domain you used in the authority> not found. This may happen if there are
        // no active subscriptions for the tenant. Check with your subscription administrator.
        // Mitigation: if you have an active subscription for the tenant this might be that you have a typo in the
        // tenantId (GUID) or tenant domain name.
    }
    catch (OperationCanceledException ex)
    {
        // If you use a CancellationToken, and call the Cancel() method on it, then this *may* be triggered
        // to indicate that the operation was cancelled.
        // See https://docs.microsoft.com/dotnet/standard/threading/cancellation-in-managed-threads
        // for more detailed information on how C# supports cancellation in managed threads.
    }
    catch (MsalClientException ex)
    {
        // Possible cause - verification code expired before contacting the server
        // This exception will occur if the user does not manage to sign-in before a time out (15 mins) and the
        // call to `AcquireTokenWithDeviceCode` is not cancelled in between
    }
}
```
# <a name="java"></a>[Java](#tab/java)

Questo estratto proviene dagli [esempi di sviluppo Java MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

```java
private static IAuthenticationResult acquireTokenDeviceCode() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            Consumer<DeviceCode> deviceCodeConsumer = (DeviceCode deviceCode) ->
                    System.out.println(deviceCode.message());

            DeviceCodeFlowParameters parameters =
                    DeviceCodeFlowParameters
                            .builder(SCOPE, deviceCodeConsumer)
                            .build();

            // Try to acquire a token via device code flow. If successful, you should see
            // the token and account information printed out to console, and the sample_cache.json
            // file should have been updated with the latest tokens.
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

Questo estratto proviene dagli [esempi di sviluppo MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/).

```Python
# Create a preferably long-lived app instance which maintains a token cache.
app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# The pattern to acquire a token looks like this.
result = None

# Note: If your device-flow app does not have any interactive ability, you can
#   completely skip the following cache part. But here we demonstrate it anyway.
# We now check the cache to see if we have some end users signed in before.
accounts = app.get_accounts()
if accounts:
    logging.info("Account(s) exists in cache, probably with token too. Let's try.")
    print("Pick the account you want to use to proceed:")
    for a in accounts:
        print(a["username"])
    # Assuming the end user chose this one
    chosen = accounts[0]
    # Now let's try to find a token in cache for this account
    result = app.acquire_token_silent(config["scope"], account=chosen)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")

    flow = app.initiate_device_flow(scopes=config["scope"])
    if "user_code" not in flow:
        raise ValueError(
            "Fail to create device flow. Err: %s" % json.dumps(flow, indent=4))

    print(flow["message"])
    sys.stdout.flush()  # Some terminal needs this to ensure the message is shown

    # Ideally you should wait here, in order to save some unnecessary polling
    # input("Press Enter after signing in from another device to proceed, CTRL+C to abort.")

    result = app.acquire_token_by_device_flow(flow)  # By default it will block
        # You can follow this instruction to shorten the block time
        #    https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow
        # or you may even turn off the blocking behavior,
        # and then keep calling acquire_token_by_device_flow(flow) in your own customized loop
```

# <a name="macos"></a>[Macos](#tab/macOS)

Questo flusso non si applica a MacOS.

---

## <a name="file-based-token-cache"></a>Cache dei token basata su file

In MSAL.NET viene fornita per impostazione predefinita una cache dei token in memoria.

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-apps-or-web-apis"></a>La serializzazione è personalizzabile nelle app desktop di Windows e nelle app Web o nelle API Web

Nel caso di .NET Framework e .NET Core, se non si fa nulla di più, la cache dei token in memoria dura per la durata dell'applicazione. Per comprendere perché la serializzazione non viene fornita out-of-the-box, tenere presente che le applicazioni DESKTOP MSAL .NET o .NET Core possono essere applicazioni console o Windows (che avrebbero accesso al file system), *ma anche* applicazioni Web o API Web. Queste app Web e le API Web potrebbero usare alcuni meccanismi di cache specifici, ad esempio database, cache distribuite e cache Redis. Per avere un'applicazione cache di token persistente in .NET desktop o .NET Core, è necessario personalizzare la serializzazione.

Le classi e le interfacce coinvolte nella serializzazione della cache dei token sono i tipi seguenti:Classes and interfaces involved in token cache serialization are the following types:

- ``ITokenCache``, che definisce gli eventi per sottoscrivere le richieste di serializzazione della cache dei token e i metodi per serializzare o deserializzare la cache in vari formati (ADAL v3.0, MSAL 2.x e MSAL 3.x - ADAL v5.0).
- ``TokenCacheCallback`` è un callback passato agli eventi in modo da consentire di gestire la serializzazione. Verranno chiamati con argomenti di tipo ``TokenCacheNotificationArgs``.
- ``TokenCacheNotificationArgs``fornisce solo ``ClientId`` l'applicazione e un riferimento all'utente per il quale il token è disponibile.

  ![Diagramma di serializzazione della cache dei token](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> MSAL.NET crea token memorizzati nella cache e fornisce la cache `IToken` quando si chiamano le proprietà `UserTokenCache` e `AppTokenCache` di un'applicazione. Non dovresti implementare l'interfaccia da solo. Quando si implementa una serializzazione della cache dei token personalizzata, occorre:
>
> - Reagire `BeforeAccess` agli `AfterAccess` eventi e agli eventi o alla loro controparte *Async.React* to and events, or their Async counterpart. Il`BeforeAccess` delegato è responsabile della deserializzazione della cache. Il `AfterAccess` delegato è responsabile della serializzazione della cache.
> - Tenere presenti che parte di questi eventi archivia o carica BLOB, che vengono passati attraverso l'argomento dell'evento a qualsiasi archiviazione desiderata.

Le strategie sono diverse a seconda che si stia scrivendo una serializzazione della cache di token per un'applicazione client pubblica, ad esempio un desktop, o un'applicazione client riservata, ad esempio un'app Web o un'API Web o un'app daemon.

A partire da MSAL v2.x, sono disponibili diverse opzioni. La scelta dipende dal fatto che si desidera serializzare la cache solo nel formato MSAL.NET, ovvero una cache di formato unificato che è comune con MSAL ma anche tra le piattaforme. In alternativa, è anche possibile supportare la serializzazione della cache token [legacy](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) di ADAL v3.

La personalizzazione della serializzazione della cache dei token per condividere lo stato SSO tra ADAL.NET 3.x, ADAL.NET 5.x e MSAL.NET viene illustrata in parte dell'esempio [active-directory-dotnet-v1-to-v2.](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2)

### <a name="simple-token-cache-serialization-msal-only"></a>Serializzazione semplice della cache dei token (solo MSAL)

L'esempio seguente è un'implementazione ingenua della serializzazione personalizzata di una cache di token per le applicazioni desktop. In questo caso, la cache dei token utente si trova in un file nella stessa cartella dell'applicazione.

Dopo aver compilato l'applicazione, abilitare la serializzazione chiamando ``TokenCacheHelper.EnableSerialization()`` e passando l'applicazione `UserTokenCache`.

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

Questa classe helper è simile al frammento di codice seguente:This helper class looks like the following code snippet:

```csharp
static class TokenCacheHelper
 {
  public static void EnableSerialization(ITokenCache tokenCache)
  {
   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Path to the token cache
  /// </summary>
  public static readonly string CacheFilePath = System.Reflection.Assembly.GetExecutingAssembly().Location + ".msalcache.bin3";

  private static readonly object FileLock = new object();


  private static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeMsalV3(File.Exists(CacheFilePath)
            ? ProtectedData.Unprotect(File.ReadAllBytes(CacheFilePath),
                                      null,
                                      DataProtectionScope.CurrentUser)
            : null);
   }
  }

  private static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     // reflect changesgs in the persistent store
     File.WriteAllBytes(CacheFilePath,
                         ProtectedData.Protect(args.TokenCache.SerializeMsalV3(),
                                                 null,
                                                 DataProtectionScope.CurrentUser)
                         );
    }
   }
  }
 }
```

Un'anteprima di un serializzatore basato su file della cache di token di qualità del prodotto per le applicazioni client pubbliche per le applicazioni desktop in esecuzione su Windows, Mac e Linux è disponibile nella libreria open source [Microsoft.Identity.Client.Extensions.Msal.](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) È possibile includerlo nelle applicazioni dal seguente pacchetto NuGet: [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

> [!NOTE]
> Dichiarazione di non responsabilità: la libreria Microsoft.Identity.Client.Extensions.Msal è un'estensione MSAL.NET. Le classi in queste librerie potrebbero farsi strada in MSAL.NET in futuro, così come sono o con modifiche di rilievo.

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>Serializzazione della cache a doppio token (cache unificata MSAL - ADAL v3)

È possibile implementare la serializzazione della cache dei token con il formato della cache unificata. Questo formato è comune a ADAL.NET 4.x e MSAL.NET 2.x e con altri file MSAL della stessa generazione o meno recenti, sulla stessa piattaforma. Lasciati ispirare dal codice seguente:

```csharp
string appLocation = Path.GetDirectoryName(Assembly.GetEntryAssembly().Location;
string cacheFolder = Path.GetFullPath(appLocation) + @"..\..\..\..");
string adalV3cacheFileName = Path.Combine(cacheFolder, "cacheAdalV3.bin");
string unifiedCacheFileName = Path.Combine(cacheFolder, "unifiedCache.bin");

IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
                                    .Build();
FilesBasedTokenCacheHelper.EnableSerialization(app.UserTokenCache,
                                               unifiedCacheFileName,
                                               adalV3cacheFileName);

```

Questa volta la classe helper è simile al codice seguente:This time the helper class looks like the following code:

```csharp
using System;
using System.IO;
using System.Security.Cryptography;
using Microsoft.Identity.Client;

namespace CommonCacheMsalV3
{
 /// <summary>
 /// Simple persistent cache implementation of the dual cache serialization (ADAL V3 legacy
 /// and unified cache format) for a desktop applications (from MSAL 2.x)
 /// </summary>
 static class FilesBasedTokenCacheHelper
 {
  /// <summary>
  /// Get the user token cache
  /// </summary>
  /// <param name="adalV3CacheFileName">File name where the cache is serialized with the
  /// ADAL V3 token cache format. Can
  /// be <c>null</c> if you don't want to implement the legacy ADAL V3 token cache
  /// serialization in your MSAL 2.x+ application</param>
  /// <param name="unifiedCacheFileName">File name where the cache is serialized
  /// with the Unified cache format, common to
  /// ADAL V4 and MSAL V2 and above, and also across ADAL/MSAL on the same platform.
  ///  Should not be <c>null</c></param>
  /// <returns></returns>
  public static void EnableSerialization(ITokenCache cache, string unifiedCacheFileName, string adalV3CacheFileName)
  {
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   cache.SetBeforeAccess(BeforeAccessNotification);
   cache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// File path where the token cache is serialized with the unified cache format
  /// (ADAL.NET V4, MSAL.NET V3)
  /// </summary>
  public static string UnifiedCacheFileName { get; private set; }

  /// <summary>
  /// File path where the token cache is serialized with the legacy ADAL V3 format
  /// </summary>
  public static string AdalV3CacheFileName { get; private set; }

  private static readonly object FileLock = new object();

  public static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeAdalV3(ReadFromFileIfExists(AdalV3CacheFileName));
    try
    {
     args.TokenCache.DeserializeMsalV3(ReadFromFileIfExists(UnifiedCacheFileName));
    }
    catch(Exception ex)
    {
     // Compatibility with the MSAL v2 cache if you used one
     args.TokenCache.DeserializeMsalV2(ReadFromFileIfExists(UnifiedCacheFileName));
    }
   }
  }

  public static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     WriteToFileIfNotNull(UnifiedCacheFileName, args.TokenCache.SerializeMsalV3());
     if (!string.IsNullOrWhiteSpace(AdalV3CacheFileName))
     {
      WriteToFileIfNotNull(AdalV3CacheFileName, args.TokenCache.SerializeAdalV3());
     }
    }
   }
  }

  /// <summary>
  /// Read the content of a file if it exists
  /// </summary>
  /// <param name="path">File path</param>
  /// <returns>Content of the file (in bytes)</returns>
  private static byte[] ReadFromFileIfExists(string path)
  {
   byte[] protectedBytes = (!string.IsNullOrEmpty(path) && File.Exists(path))
       ? File.ReadAllBytes(path) : null;
   byte[] unprotectedBytes = encrypt ?
       ((protectedBytes != null) ? ProtectedData.Unprotect(protectedBytes, null, DataProtectionScope.CurrentUser) : null)
       : protectedBytes;
   return unprotectedBytes;
  }

  /// <summary>
  /// Writes a blob of bytes to a file. If the blob is <c>null</c>, deletes the file
  /// </summary>
  /// <param name="path">path to the file to write</param>
  /// <param name="blob">Blob of bytes to write</param>
  private static void WriteToFileIfNotNull(string path, byte[] blob)
  {
   if (blob != null)
   {
    byte[] protectedBytes = encrypt
      ? ProtectedData.Protect(blob, null, DataProtectionScope.CurrentUser)
      : blob;
    File.WriteAllBytes(path, protectedBytes);
   }
   else
   {
    File.Delete(path);
   }
  }

  // Change if you want to test with an un-encrypted blob (this is a json format)
  private static bool encrypt = true;
 }
}
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Chiamare un'API Web dall'app desktopCall a web API from the desktop app](scenario-desktop-call-api.md)
