---
title: Acquisire un token per chiamare un'API Web (app desktop) | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come creare un'app desktop che chiama le API Web per acquisire un token per l'app
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: jmprieur
ms.custom: aaddev, tracking-python
ms.openlocfilehash: 85fb188028e0e61378b1e47c1fb7b88eaaa4d7a0
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2020
ms.locfileid: "87541924"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token"></a>App desktop che chiama le API Web: Acquisire un token

Una volta creata un'istanza dell'applicazione client pubblica, la si userà per acquisire un token da usare per chiamare un'API Web.

## <a name="recommended-pattern"></a>Modello consigliato

L'API Web è definita dai relativi ambiti: `scopes`. Indipendentemente dall'esperienza fornita nell'applicazione, il modello da usare è:

- Tentare in modo sistematico di ottenere un token dalla cache dei token chiamando `AcquireTokenSilent`.
- Se la chiamata ha esito negativo, usare il flusso `AcquireToken` che si vuole usare, che qui è rappresentato da `AcquireTokenXX`.

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

# <a name="macos"></a>[macOS](#tab/macOS)

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

Ecco i vari modi per acquisire i token in un'applicazione desktop.

## <a name="acquire-a-token-interactively"></a>Acquisire un token in modo interattivo

Nell'esempio seguente viene illustrato il codice minimo per ottenere un token in modo interattivo per la lettura del profilo dell'utente con Microsoft Graph.

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

`AcquireTokenInteractive` prevede un solo parametro obbligatorio, ``scopes``, che contiene un'enumerazione di stringhe che definiscono gli ambiti per i quali è richiesto un token. Se il token è per Microsoft Graph, gli ambiti necessari sono reperibili nelle informazioni di riferimento dell'API per ogni API Microsoft Graph nella sezione denominata "Autorizzazioni". Ad esempio, per [elencare i contatti dell'utente](https://docs.microsoft.com/graph/api/user-list-contacts), è necessario usare l'ambito "User. Read", "Contacts. Read". Per altre informazioni, vedere le [Informazioni di riferimento per le autorizzazioni dell'API Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/permissions_reference).

In Android è inoltre necessario specificare l'attività padre usando `.WithParentActivityOrWindow`, come illustrato, in modo che il token torni a tale attività padre dopo l'interazione. Se non viene specificato, viene generata un'eccezione quando si chiama `.ExecuteAsync()`.

### <a name="specific-optional-parameters-in-msalnet"></a>Parametri facoltativi specifici in MSAL.NET

#### <a name="withparentactivityorwindow"></a>WithParentActivityOrWindow

L'interfaccia utente è importante perché è interattiva. `AcquireTokenInteractive` prevede un parametro facoltativo specifico che può specificare, per le piattaforme che lo supportano, l'interfaccia utente padre. Quando viene usato in un'applicazione desktop, `.WithParentActivityOrWindow` ha un tipo diverso, che dipende dalla piattaforma.

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

- In .NET Standard l'elemento `object` previsto è `Activity` in Android, `UIViewController` in iOS, `NSWindow` in MAC e `IWin32Window` o `IntPr` in Windows.
- In Windows è necessario chiamare `AcquireTokenInteractive` dal thread di interfaccia utente in modo che il browser incorporato ottenga il contesto di sincronizzazione dell'interfaccia utente appropriato. Se la chiamata non viene effettuata dal thread di interfaccia utente potrebbero verificarsi errori durante la distribuzione dei messaggi con conseguenti scenari di inserimento nella coda di messaggi non recapitabili nell'interfaccia utente. Un modo per chiamare le librerie di autenticazione Microsoft (MSAL) dal thread di interfaccia utente, se non ci si trova già nel thread di interfaccia utente, consiste nell'usare `Dispatcher` in Windows Presentation Foundation (WPF).
- Se si usa WPF, per ottenere una finestra da un controllo WPF, è possibile usare la classe `WindowInteropHelper.Handle`. La chiamata proviene quindi da un controllo WPF (`this`):

  ```csharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>WithPrompt

`WithPrompt()` viene usato per controllare l'interattività con l'utente specificando una richiesta.

![Immagine che mostra i campi nella struttura dei messaggi di richiesta. Questi valori costanti controllano l'interattività con l'utente definendo il tipo di messaggio di richiesta visualizzato dal metodo WithPrompt ().](https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png)

La classe definisce le costanti seguenti:

- ``SelectAccount`` impone al servizio token di sicurezza di presentare la finestra di dialogo di selezione dell'account che contiene gli account per i quali l'utente dispone di una sessione. Questa opzione è utile quando gli sviluppatori di applicazioni desiderano consentire agli utenti di scegliere tra identità diverse. Questa opzione consente a MSAL di inviare ``prompt=select_account`` al provider di identità. Questa opzione è l'impostazione predefinita. Permette di fornire la migliore esperienza possibile in base alle informazioni disponibili, ad esempio l'account e la presenza di una sessione per l'utente. Non modificarla se non si ha un buon motivo per farlo.
- ``Consent`` consente agli sviluppatori di applicazioni di forzare la richiesta di consenso da parte dell'utente, anche se il consenso è già stato concesso in precedenza. In questo caso, MSAL invia `prompt=consent` al provider di identità. Questa opzione può essere usata in alcune applicazioni incentrate sulla sicurezza, in cui la governance dell'organizzazione richiede che all'utente venga presentata la finestra di dialogo di consenso ogni volta che viene usata l'applicazione.
- ``ForceLogin`` consente agli sviluppatori di applicazioni di richiedere all'utente le credenziali per il servizio, anche se questa richiesta utente potrebbe non essere necessaria. Questa opzione è utile per consentire all'utente di accedere di nuovo se l'acquisizione di un token ha esito negativo. In questo caso, MSAL invia `prompt=login` al provider di identità. Talvolta viene usata in applicazioni incentrate sulla sicurezza in cui la governance dell'organizzazione richiede all'utente di eseguire di nuovo l'accesso quando si passa a parti specifiche di un'applicazione.
- ``Never`` (solo per .NET 4.5 e WinRT) non presenterà alcuna richiesta all'utente, ma tenterà di usare il cookie archiviato nella visualizzazione Web incorporata nascosta. Per altre informazioni, vedere le visualizzazioni Web in MSAL.NET. L'uso di questa opzione potrebbe avere esito negativo. In tal caso, `AcquireTokenInteractive` genera un'eccezione per notificare che è necessaria un'interazione con l'interfaccia utente. Sarà necessario usare un altro parametro `Prompt`.
- ``NoPrompt`` non invierà alcuna richiesta al provider di identità. Questa opzione è utile solo per i criteri di modifica del profilo di Azure Active Directory (Azure AD) B2C. Per altre informazioni, vedere [Specifiche di Azure AD B2C](https://aka.ms/msal-net-b2c-specificities).

#### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Questo modificatore viene usato in uno scenario avanzato in cui si vuole che l'utente fornisca preventivamente il consenso per numerose risorse in anticipo e non si vuole usare il consenso incrementale, che viene in genere usato con MSAL.NET/Microsoft Identity Platform. Per altre informazioni, vedere [Ricevere il consenso utente in anticipo per numerose risorse](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources).

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>WithCustomWebUi

Un'interfaccia utente Web è un meccanismo per richiamare un browser. Questo meccanismo può essere un controllo WebBrowser dell'interfaccia utente dedicato o un modo per delegare l'apertura del browser.
MSAL fornisce implementazioni dell'interfaccia utente Web per la maggior parte delle piattaforme, ma è possibile che in alcuni casi l'utente preferisca ospitare il browser in autonomia, ad esempio:

- Per piattaforme non coperte in modo esplicito da MSAL, ad esempio Blazor, Unity e Mono nei computer desktop.
- Quando si vuole testare l'interfaccia utente dell'applicazione e usare un browser automatizzato che può essere utilizzato con Selenium.
- Quando il browser e l'app che eseguono MSAL risiedono in processi separati.

##### <a name="at-a-glance"></a>Riepilogo

A tale scopo, si passa a MSAL il controllo `start Url`, che deve essere visualizzato nel browser scelto affinché l'utente finale possa specificare elementi quali il nome utente.
Al termine dell'autenticazione, l'app deve passare di nuovo `end Url` a MSAL, che contiene un codice fornito da Azure AD.
L'host di `end Url` è sempre `redirectUri`. Per intercettare `end Url`, eseguire una di queste operazioni:

- Monitorare i reindirizzamenti del browser fino a quando non viene raggiunto `redirect Url`.
- Impostare il reindirizzamento del browser a un URL monitorato dall'utente.

##### <a name="withcustomwebui-is-an-extensibility-point"></a>WithCustomWebUi è un punto di estendibilità

`WithCustomWebUi` è un punto di estendibilità che è possibile usare per fornire una propria interfaccia utente in applicazioni client pubbliche. È anche possibile consentire all'utente di usare l'endpoint /Authorize del provider di identità per concedere l'accesso e il consenso. MSAL.NET può quindi riscattare il codice di autenticazione e ottenere un token. Viene usato ad esempio in Visual Studio per consentire alle applicazioni Electron, come Commenti e suggerimenti di Visual Studio, di fornire l'interazione Web, lasciando a MSAL.NET la maggior parte delle operazioni. È anche possibile usarlo per fornire l'automazione dell'interfaccia utente. Nelle applicazioni client pubbliche MSAL.NET usa lo standard PKCE (Proof Key for Code Exchange) per garantire il rispetto della sicurezza. Solo MSAL.NET può riscattare il codice. Per altre informazioni, vedere il memo [RFC 7636 - Proof Key for Code Exchange by OAuth Public Clients](https://tools.ietf.org/html/rfc7636) (RFC 7636 - Chiave di prova per lo scambio di codice dei client pubblici OAuth).

  ```csharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="use-withcustomwebui"></a>Usare WithCustomWebUi

Per usare `.WithCustomWebUI`, seguire questa procedura.

  1. Implementare l'interfaccia `ICustomWebUi`. Per altre informazioni, vedere [questo sito Web](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70). Implementare un metodo `AcquireAuthorizationCodeAsync` e accettare l'ULR del codice di autorizzazione calcolato da MSAL.NET. Consentire quindi all'utente di interagire con il provider di identità e restituire l'URL in base al quale il provider di identità avrebbe chiamato l'implementazione insieme al codice di autorizzazione. In caso di problemi, l'implementazione genererà un'eccezione `MsalExtensionException` per collaborare con MSAL.
  2. Nella chiamata a `AcquireTokenInteractive` usare il modificatore `.WithCustomUI()` passando l'istanza dell'interfaccia utente Web personalizzata.

     ```csharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation-seleniumwebui"></a>Esempi di implementazione di ICustomWebUi nell'automazione dei test: SeleniumWebUI

Il team di MSAL.NET ha riscritto i test dell'interfaccia utente per usare questo meccanismo di estendibilità. Se si è interessati, esaminare la classe [SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160) nel codice sorgente di MSAL.NET.

##### <a name="provide-a-great-experience-with-systemwebviewoptions"></a>Offrire un'esperienza ottimale con SystemWebViewOptions

Da [`SystemWebViewOptions`](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.systemwebviewoptions?view=azure-dotnet) in MSAL.NET 4.1 è possibile specificare:

- L'URI a cui passare (`BrowserRedirectError`) o il frammento HTML da visualizzare (`HtmlMessageError`) in caso di errori di accesso o di consenso nel Web browser del sistema.
- L'URI a cui passare (`BrowserRedirectSuccess`) o il frammento HTML da visualizzare (`HtmlMessageSuccess`) in caso di accesso o consenso riuscito.
- Azione da eseguire per avviare il browser del sistema. È possibile fornire un'implementazione personalizzata impostando il delegato `OpenBrowserAsync`. La classe fornisce anche un'implementazione predefinita per due browser: `OpenWithEdgeBrowserAsync` e `OpenWithChromeEdgeBrowserAsync` per Microsoft Edge e [Microsoft Edge basato su Chromium](https://www.windowscentral.com/faq-edge-chromium), rispettivamente.

Per usare questa struttura, scrivere un codice simile all'esempio seguente:

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

Per altre informazioni su tutti gli altri parametri facoltativi per `AcquireTokenInteractive`, vedere [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods).

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

MSAL per Python non fornisce direttamente un metodo di acquisizione token interattivo. Richiede invece all'applicazione di inviare una richiesta di autorizzazione nell'implementazione del flusso di interazione utente per ottenere un codice di autorizzazione. Questo codice può quindi essere passato al metodo `acquire_token_by_authorization_code` per ottenere il token.

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

# <a name="macos"></a>[macOS](#tab/macOS)

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

Per l'accesso di un utente di dominio a un dominio o a un computer aggiunto ad Azure AD, usare l'autenticazione integrata di Windows.

### <a name="constraints"></a>Vincoli

- L'autenticazione integrata di Windows è utilizzabile solo per gli utenti *federati*, ovvero gli utenti creati in Active Directory e supportati da Azure AD. Gli utenti creati direttamente in Azure AD senza supporto per Active Directory, noti anche come utenti *gestiti* non possono usare questo flusso di autenticazione. Questa limitazione non influisce sul flusso di nome utente e password.
- L'autenticazione integrata di Windows è destinata alle app scritte per .NET Framework, .NET Core e piattaforme UWP (Universal Windows Platform).
- L'autenticazione integrata di Windows non ignora l'[autenticazione a più fattori (MFA)](../authentication/concept-mfa-howitworks.md). Se è configurata l'autenticazione a più fattori, l'autenticazione integrata di Windows potrebbe avere esito negativo se è necessaria una richiesta di verifica di autenticazione a più fattori, in quanto richiede l'interazione dell'utente.
  > [!NOTE]
  > Si tratta di un problema complesso. L'autenticazione integrata di Windows è non interattiva, ma l'autenticazione a più fattori richiede l'interattività dell'utente. L'utente non controlla quando il provider di identità richiede l'esecuzione dell'autenticazione a più fattori. Questa operazione viene gestita dall'amministratore del tenant. In base a quanto osservato, l'autenticazione a più fattori è necessaria quando si accede da un paese o un'area geografica diversa, quando non si è connessi tramite VPN a una rete aziendale e talvolta anche quando si è connessi tramite VPN. Non è previsto un set di regole deterministico. Azure AD usa l'intelligenza artificiale per apprendere continuamente se è necessaria l'autenticazione a più fattori. Eseguire il fallback a una richiesta utente come l'autenticazione interattiva o il flusso del codice del dispositivo in caso di errore dell'autenticazione integrata di Windows.

- L'autorità passata in `PublicClientApplicationBuilder` deve essere:
  - Con tenant nel formato `https://login.microsoftonline.com/{tenant}/`, dove `tenant` è il GUID che rappresenta l'ID tenant o un dominio associato al tenant.
  - Per gli account aziendali e dell'istituto di istruzione: `https://login.microsoftonline.com/organizations/`.
  - Gli account Microsoft personali non sono supportati. Non è possibile usare i tenant /common o /consumers.

- Poiché l'autenticazione integrata di Windows è un flusso invisibile all'utente:
  - L'utente dell'applicazione deve avere precedentemente fornito il consenso per l'uso dell'applicazione.
  - In alternativa, l'amministratore del tenant deve avere precedentemente fornito il consenso per l'uso dell'applicazione per tutti gli utenti del tenant.
  - In altre parole:
    - Lo sviluppatore ha selezionato il pulsante **Concedi** nel portale di Azure per se stesso.
    - In alternativa, un amministratore del tenant ha selezionato **Fornisci/Revoca il consenso amministratore per {dominio tenant}** nella scheda **Autorizzazioni API** della registrazione per l'applicazione. Per altre informazioni, vedere [Aggiungere le autorizzazioni per accedere alle API Web](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis#add-permissions-to-access-web-apis).
    - In alternativa, è stato fornito un modo per consentire agli utenti di fornire il consenso per l'applicazione. Per altre informazioni, vedere [Richiesta di consenso per un singolo utente](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-individual-user-consent).
    - In alternativa, è stato fornito un modo per consentire all'amministratore del tenant di fornire il consenso per l'applicazione. Per altre informazioni, vedere [Consenso amministratore](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-consent-for-an-entire-tenant).

- Questo flusso è abilitato per le app .NET Desktop, .NET Core e UWP.

Per altre informazioni sul consenso, vedere [Autorizzazioni e consenso in Microsoft Identity Platform](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent).

### <a name="learn-how-to-use-it"></a>Informazioni su come usare l'SDK

# <a name="net"></a>[.NET](#tab/dotnet)

In MSAL.NET è necessario usare:

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

In genere è necessario un solo parametro (`scopes`). A seconda del modo in cui l'amministratore di Windows ha configurato i criteri, è possibile che le applicazioni nel computer Windows non siano autorizzate a cercare l'utente che ha eseguito l'accesso. In tal caso, usare un secondo metodo, `.WithUsername()`e passare il nome utente dell'utente che ha eseguito l'accesso come formato UPN, ad esempio `joe@contoso.com`. In .NET Core è disponibile solo l'overload che accetta il nome utente perché la piattaforma .NET Core non può richiedere il nome utente al sistema operativo.

Nell'esempio seguente viene presentato il caso più recente, con spiegazioni sulle possibili tipologie di eccezioni restituite e sulle relative mitigazioni.

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

Per l'elenco dei modificatori possibili in AcquireTokenByIntegratedWindowsAuthentication, vedere [AcquireTokenByIntegratedWindowsAuthParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder?view=azure-dotnet-preview#methods).

# <a name="java"></a>[Java](#tab/java)

Questo estratto è tratto dagli [esempi di sviluppo di MSAL per Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

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

Questo flusso non è ancora supportato in MSAL per Python.

# <a name="macos"></a>[macOS](#tab/macOS)

Questo flusso non si applica a macOS.

---

## <a name="username-and-password"></a>Nome utente e password

È anche possibile acquisire un token specificando il nome utente e la password. Questo flusso è limitato ed è sconsigliato, ma esistono alcuni casi di utilizzo in cui è necessario.

### <a name="this-flow-isnt-recommended"></a>Questo flusso non è consigliato

Questo flusso *non è consigliato* perché non è sicuro che l'applicazione richieda la password a un utente. Per altre informazioni, vedere [Qual è la soluzione per il problema crescente delle password?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). Il flusso preferito per acquisire un token in modo invisibile all'utente nei computer Windows aggiunti a un dominio è l'[autenticazione integrata di Windows](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication). È anche possibile usare il [flusso del codice del dispositivo](https://aka.ms/msal-net-device-code-flow).

> [!NOTE]
> L'uso di un nome utente e di una password è utile in alcuni casi, ad esempio in scenari DevOps. Tuttavia, se si vuole usare un nome utente e una password in scenari interattivi in cui si fornisce un'interfaccia utente personalizzata, è necessario valutare soluzioni alternative. Usando un nome utente e una password, è necessario rinunciare a numerosi aspetti:
>
> - Principi fondamentali dell'identità moderna. Una password può essere oggetto di phishing e riprodotta perché un segreto condiviso può essere intercettato. Non è compatibile con l'accesso senza password.
> - Gli utenti che devono eseguire l'autenticazione a più fattori non possono accedere perché non esiste alcuna interazione.
> - Gli utenti possono usufruire dell'accesso Single Sign-On (SSO).

### <a name="constraints"></a>Vincoli

Si applicano inoltre i vincoli seguenti:

- Il flusso di nome utente e password non è compatibile con l'accesso condizionale e l'autenticazione a più fattori. Di conseguenza, se l'app viene eseguita in un tenant di Azure AD in cui l'amministratore del tenant richiede l'autenticazione a più fattori, non è possibile usare questo flusso. Molte organizzazioni lo fanno.
- Funziona solo per gli account aziendali degli istituti di istruzione (non per gli account del servizio gestito).
- Il flusso è disponibile in .NET Desktop e .NET Core, ma non nella piattaforma UWP (Universal Windows Platform).

### <a name="b2c-specifics"></a>Specifiche per B2C

Per altre informazioni, vedere [Credenziali password del proprietario della risorsa con B2C](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c).

### <a name="use-it"></a>Utilizzo

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication`contiene il metodo `AcquireTokenByUsernamePassword`.

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

Nell'esempio seguente viene presentato il caso più recente, con spiegazioni sulle possibili tipologie di eccezioni restituite e sulle relative mitigazioni.

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

Per altre informazioni su tutti i modificatori che possono essere applicati a `AcquireTokenByUsernamePassword`, vedere [AcquireTokenByUsernamePasswordParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyusernamepasswordparameterbuilder?view=azure-dotnet-preview#methods).

# <a name="java"></a>[Java](#tab/java)

L'estratto seguente è tratto dagli [esempi di sviluppo di MSAL per Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

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

Questo estratto è tratto dagli [esempi di sviluppo di MSAL per Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/).

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

# <a name="macos"></a>[macOS](#tab/macOS)

Questo flusso non è supportato in MSAL per macOS.

---

## <a name="command-line-tool-without-a-web-browser"></a>Strumento da riga di comando senza un Web browser

### <a name="device-code-flow"></a>Flusso del codice del dispositivo

Se si sta scrivendo uno strumento da riga di comando che non dispone di controlli Web e non è possibile o non si vuole usare i flussi precedenti, è necessario usare il flusso del codice del dispositivo.

L'autenticazione interattiva con Azure AD richiede un Web browser. Per altre informazioni, vedere [Utilizzo dei Web browser](https://aka.ms/msal-net-uses-web-browser). Per autenticare gli utenti nei dispositivi o nei sistemi operativi che non forniscono un Web browser, il flusso del codice del dispositivo consente all'utente di usare un altro dispositivo, ad esempio un computer o un telefono cellulare, per eseguire l'accesso in modo interattivo. Usando il flusso del codice del dispositivo, l'applicazione ottiene i token tramite un processo in due passaggi appositamente progettato per questi dispositivi o sistemi operativi. Esempi di applicazioni di questo tipo sono le applicazioni in esecuzione in ambienti IoT o gli strumenti da riga di comando (interfaccia della riga di comando). L'idea è la seguente:

1. Ogni volta che è necessaria l'autenticazione utente, l'app fornisce un codice per l'utente. All'utente viene richiesto di usare un altro dispositivo, ad esempio uno smartphone connesso a Internet, per passare a un URL, ad esempio `https://microsoft.com/devicelogin`. All'utente viene quindi richiesto di immettere il codice. Una volta immesso, la pagina Web guida l'utente attraverso una normale esperienza di autenticazione, che include le richieste di consenso e l'autenticazione a più fattori, se necessario.

2. Una volta completata correttamente l'autenticazione, l'app da riga di comando riceve i token necessari tramite un backchannel e li usa per eseguire le chiamate all'API Web necessarie.

### <a name="use-it"></a>Utilizzo

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication`contiene un metodo denominato `AcquireTokenWithDeviceCode`.

```csharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

Questo metodo accetta come parametri:

- `scopes` per cui richiedere un token di accesso.
- Un callback che riceve `DeviceCodeResult`.

  ![Proprietà per DeviceCodeResult](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

Il codice di esempio seguente presenta il caso più recente, con spiegazioni sulle possibili tipologie di eccezioni restituite e sulle relative mitigazioni.

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

Questo estratto è tratto dagli [esempi di sviluppo di MSAL per Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

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

Questo estratto è tratto dagli [esempi di sviluppo di MSAL per Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/).

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

# <a name="macos"></a>[macOS](#tab/macOS)

Questo flusso non si applica a macOS.

---

## <a name="file-based-token-cache"></a>Cache di token basata su file

In MSAL.NET viene fornita per impostazione predefinita una cache dei token in memoria.

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-apps-or-web-apis"></a>La serializzazione è personalizzabile per app desktop di Windows e app Web o API Web

Nel caso di .NET Framework e .NET Core, se non si esegue alcuna operazione aggiuntiva, la cache dei token in memoria dura per la durata dell'applicazione. Per informazioni sul motivo per cui la serializzazione non è fornita in modo predefinito, tenere presente che le applicazioni .NET Desktop o .NET Core MSAL possono essere applicazioni console o Windows (che avrebbero accesso al file system) *ma anche* applicazioni Web o API Web. Queste app Web e API Web potrebbero usare alcuni meccanismi di cache specifici, ad esempio database, cache distribuite e cache Redis. Per disporre di un'applicazione cache dei token persistente in .NET Desktop o .NET Core, è necessario personalizzare la serializzazione.

Le classi e le interfacce coinvolte nella serializzazione della cache dei token sono dei tipi seguenti:

- ``ITokenCache``, che definisce gli eventi per la sottoscrizione delle richieste di serializzazione della cache dei token, nonché i metodi per serializzare o deserializzare la cache in diversi formati (ADAL v3.0, MSAL 2.x e MSAL 3.x = ADAL v5.0).
- ``TokenCacheCallback`` è un callback passato agli eventi in modo da consentire di gestire la serializzazione. Verranno chiamati con argomenti di tipo ``TokenCacheNotificationArgs``.
- ``TokenCacheNotificationArgs`` fornisce solo il ``ClientId`` dell'applicazione e un riferimento all'utente per cui è disponibile il token.

  ![Diagramma della serializzazione della cache dei token](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> MSAL.NET crea token memorizzati nella cache e fornisce la cache `IToken` quando si chiamano le proprietà `UserTokenCache` e `AppTokenCache` di un'applicazione. Non è previsto che l'utente implementi l'interfaccia autonomamente. Quando si implementa una serializzazione della cache dei token personalizzata, occorre:
>
> - Reagire a eventi `BeforeAccess` e `AfterAccess` o le relative versioni *asincrone*. Il delegato `BeforeAccess` è responsabile della deserializzazione della cache. Il delegato `AfterAccess` è responsabile della serializzazione della cache.
> - Alcuni di questi eventi archiviano o caricano BLOB, che vengono passati tramite l'argomento dell'evento al tipo di archiviazione desiderato.

Le strategie sono diverse a seconda del fatto che si stia scrivendo una serializzazione della cache dei token per un'applicazione client pubblica (desktop) o un'applicazione client riservata (app Web, API Web o app daemon).

A partire da MSAL v2.x sono disponibili numerose opzioni. La scelta varia a seconda che si voglia serializzare la cache solo nel formato MSAL.NET, ovvero una cache con formato unificato comune con MSAL ma anche con tutte le piattaforme. Oppure, potrebbe anche essere necessario supportare la serializzazione della cache dei token [legacy](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) di ADAL v3.

La personalizzazione della serializzazione della cache dei token per condividere lo stato Single Sign-On tra ADAL.NET 3.x, ADAL.NET 5.x e MSAL.NET è illustrata in parte dell'esempio: [active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2).

### <a name="simple-token-cache-serialization-msal-only"></a>Serializzazione semplice della cache dei token (solo MSAL)

Di seguito è riportato un esempio di implementazione semplice della serializzazione personalizzata di una cache dei token per le applicazioni desktop. In questo caso, la cache dei token dell'utente è un file nella stessa cartella dell'applicazione.

Dopo aver creato l'applicazione, si abilita la serializzazione chiamando il metodo ``TokenCacheHelper.EnableSerialization()`` e passando `UserTokenCache` all'applicazione.

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

Questa classe helper ha un aspetto simile al frammento di codice seguente:

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

Un'anteprima di un serializzatore basato su file della cache dei token per la qualità del prodotto per applicazioni client pubbliche (per le applicazioni desktop in esecuzione in Windows, Mac e Linux) è disponibile dalla libreria open source [Microsoft.Identity.Client.Extensions.Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal). È possibile includerla nelle applicazioni dal pacchetto NuGet seguente: [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

> [!NOTE]
> Dichiarazione di non responsabilità: La libreria Microsoft.Identity.Client.Extensions.Msal è un'estensione di MSAL.NET. In futuro, le classi in queste librerie potrebbero essere incluse in MSAL.NET, così come sono o con modifiche che causano interruzioni.

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>Serializzazione doppia della cache dei token (cache unificata MSAL e ADAL v3)

Potrebbe essere necessario implementare la serializzazione della cache del token con il formato di cache unificato. Questo formato è comune ad ADAL.NET 4.x e MSAL.NET 2.x e con altre librerie MSAL della stessa generazione o di una versione precedente nella stessa piattaforma. È possibile lasciarsi ispirare dal codice seguente:

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

Questa volta la classe helper ha un aspetto simile al codice seguente:

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
> [Chiamare un'API Web dall'app desktop](scenario-desktop-call-api.md)
