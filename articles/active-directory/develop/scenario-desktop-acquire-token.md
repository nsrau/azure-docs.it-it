---
title: "App desktop che chiama web API (acquisire un token per l'app): piattaforma delle identità Microsoft"
description: Informazioni su come creare un'app Desktop che chiama le API web (acquisire un token per l'app |)
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecf5b874345a94e8fd3d3a0783f8e48c7484377d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111266"
---
# <a name="desktop-app-that-calls-web-apis---acquire-a-token"></a>App desktop che chiama le API - web acquisire un token

Dopo aver creato è `IPublicClientApplication`, si userà per acquisire un token che userà quindi per chiamare un'API web.

## <a name="recommended-pattern"></a>Modello consigliato

L'API web è definito dal relativo `scopes`. Qualunque sia l'esperienza fornita nell'applicazione, il modello che è opportuno usare è:

- Sistematicamente il tentativo di ottenere un token dalla cache dei token mediante la chiamata `AcquireTokenSilent`
- Se questa chiamata non riesce, usare il `AcquireToken` flusso che si desidera utilizzare (in questo caso rappresentato da `AcquireTokenXX`)

```CSharp
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

Di seguito viene indicato il dettaglio dei vari modi per acquisire i token in un'applicazione desktop

## <a name="acquiring-a-token-interactively"></a>Acquisire un token in modo interattivo

Nell'esempio seguente mostra una quantità minima di codice per ottenere un token in modo interattivo per la lettura del profilo dell'utente con Microsoft Graph.

```CSharp
string[] scopes = new string["user.read"];
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

`AcquireTokenInteractive` presenta un solo parametro obbligatorio ``scopes``, che contiene un'enumerazione di stringhe che definisce gli ambiti per i quali è necessario un token. Se il token sta per Microsoft Graph, gli ambiti necessari sono reperibile nel riferimento all'api di ogni API Microsoft graph nella sezione denominata "Autorizzazioni". Ad esempio, per [un elenco di contatti dell'utente](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts), l'ambito "Read", "Contacts.Read" dovrà essere usato. Vedere anche [riferimento alle autorizzazioni Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/permissions_reference).

In Android, è necessario specificare anche l'attività padre (usando `.WithParentActivityOrWindow`, vedere di seguito) in modo che il token ottiene nuovamente a quell'attività padre dopo l'interazione. Se non specificato, verrà generata un'eccezione quando si chiama `.ExecuteAsync()`.

### <a name="specific-optional-parameters"></a>Parametri facoltativi specifici

#### <a name="withparentactivityorwindow"></a>WithParentActivityOrWindow

Essendo interattive, è importante dell'interfaccia utente. `AcquireTokenInteractive` ha un parametro facoltativo specificato l'abilitazione per specificare, per le piattaforme che supportano, l'elemento padre dell'interfaccia utente. Se utilizzato in un'applicazione desktop, `.WithParentActivityOrWindow` presenta un tipo diverso a seconda della piattaforma:

```CSharp
// net45
WithParentActivityOrWindow(IntPtr windowPtr)
WithParentActivityOrWindow(IWin32Window window)

// Mac
WithParentActivityOrWindow(NSWindow window)

// .Net Standard (this will be on all platforms at runtime, but only on NetStandard at build time)
WithParentActivityOrWindow(object parent).
```

Osservazioni:

- In .NET Standard previsto `object` è un `Activity` in Android, un `UIViewController` in iOS, un `NSWindow` su MAC e un `IWin32Window` o `IntPr` su Windows.
- In Windows, è necessario chiamare `AcquireTokenInteractive` dall'interfaccia utente del thread in modo che il browser incorporato Ottiene il contesto di sincronizzazione dell'interfaccia utente appropriato.  Non chiamare dal thread dell'interfaccia utente potrebbe essere messaggi pump correttamente e/o non un deadlock in scenari con l'interfaccia utente. Un modo per la chiamata a MSAL dal thread dell'interfaccia utente se non si è sul thread UI già consiste nell'usare il `Dispatcher` su WPF.
- Se si usa WPF, per visualizzare una finestra in un controllo WPF, è possibile usare `WindowInteropHelper.Handle` classe. Quindi, proviene da un controllo WPF (`this`):
  
  ```CSharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>WithPrompt

`WithPrompt()` viene usato per controllare l'interattività con l'utente specificando un prompt dei comandi

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

La classe definisce le costanti seguenti:

- ``SelectAccount``: forzerà il servizio token di sicurezza per presentare la finestra di dialogo Selezione account contenenti gli account per cui l'utente dispone di una sessione. Questa opzione è utile quando si desidera che gli sviluppatori di applicazioni consentire all'utente scegliere tra diverse identità usate. Questa opzione MSAL per inviare le unità ``prompt=select_account`` al provider di identità. Questa opzione è l'impostazione predefinita e delle buon risultato di offrire la migliore esperienza possibile basata sulle informazioni disponibili (account, presenza di una sessione per l'utente e così via. ...). Non modificarlo solo se si dispone di buon motivo per eseguire questa operazione.
- ``Consent``: consente all'applicazione per gli sviluppatori per obbligare l'utente richiesto il consenso anche se è stato fornito il consenso prima. In questo caso, MSAL Invia `prompt=consent` al provider di identità. Questa opzione può essere utilizzata in alcune applicazioni di sicurezza con stato attivato in cui la governance dell'organizzazione richiede che all'utente viene visualizzata la finestra di dialogo di consenso ogni volta che viene usata l'applicazione.
- ``ForceLogin``: consente allo sviluppatore dell'applicazione che l'utente richiesta le credenziali dal servizio, anche se non sarebbe necessario questo prompt utente. Questa opzione può essere utile se l'acquisizione di un token ha esito negativo, per consentire all'utente rinviati-accesso aggiuntivo. In questo caso, MSAL Invia `prompt=login` al provider di identità. Anche in questo caso, abbiamo visto viene utilizzato in alcune applicazioni di sicurezza con stato attivo in cui la governance dell'organizzazione richiede che l'utente relogs aggiuntivo ogni volta che accedono a parti specifiche di un'applicazione.
- ``Never`` (per .NET 4.5 e solo WinRT) non verrà chiesto all'utente, ma invece proverà a usare i cookie memorizzato nella visualizzazione web incorporati nascosti (vedere di seguito: Visualizzazioni Web nel MSAL.NET). Utilizzo di questa opzione potrebbe non riuscire e in tal caso `AcquireTokenInteractive` genererà un'eccezione per informare che è necessaria un'interazione dell'interfaccia utente, ed è necessario usare un altro `Prompt` parametro.
- ``NoPrompt``: Non invierà alcuna richiesta al provider di identità. Questa opzione è utile solo per i criteri di Azure AD B2C Modifica profilo (vedere [B2C specifiche](https://aka.ms/msal-net-b2c-specificities)).

#### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Questo modificatore viene usato in uno scenario avanzato in cui si desidera che l'utente di pre-autorizzare diverse risorse upfront (e non si desidera utilizzare il consenso incrementale, in genere usata con MSAL.NET / 2.0 Microsoft identity platform). Per informazioni dettagliate, vedere [procedura: richiedere all'utente di fornire il consenso anticipo per più risorse](scenario-desktop-production.md#how-to-have--the-user-consent-upfront-for-several-resources).

```CSharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>WithCustomWebUi

##### <a name="withcustomwebui-is-an-extensibility-point"></a>WithCustomWebUi è un punto di estensibilità

`WithCustomWebUi` è un punto di estendibilità che consente che fornire la propria interfaccia utente nelle applicazioni client pubblico e per consentire all'utente di passare attraverso l'endpoint /Authorize del provider di identità e ti permettono di accedere e fornire il consenso. MSAL.NET può quindi riscattare il codice di autenticazione e ottenere un token. Ad esempio utilizzato in Visual Studio affinché elettroni applicazioni (ad esempio Visual Studio Feedback) forniscono l'interazione di web, ma lasciare che sia MSAL.NET per eseguire la maggior parte del lavoro. È anche possibile usarlo se si desidera fornire l'automazione dell'interfaccia utente. Nelle applicazioni client pubblico, MSAL.NET usa lo standard PKCE ([RFC 7636 - chiave di prova per Code Exchange dal client di OAuth pubblici](https://tools.ietf.org/html/rfc7636)) per assicurarsi che sia rispettata sicurezza: Solo MSAL.NET possibile riscattare il codice.

  ```CSharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="how-to-use-withcustomwebui"></a>Come usare WithCustomWebUi

Per poter utilizzare `.WithCustomWebUI`, è necessario:
  
  1. Implementare il `ICustomWebUi` interfaccia (vedere [qui](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70). In sostanza è necessario implementare un metodo `AcquireAuthorizationCodeAsync` accettando l'URL di codice di autorizzazione (calcolato dal MSAL.NET), consentendo all'utente di passare attraverso l'interazione con il provider di identità e la restituzione di eseguire il l'URL dal quale sarebbe il provider di identità aver chiamato l'implementazione indietro (tra cui il codice di autorizzazione). Se si verificano problemi, l'implementazione deve generare un `MsalExtensionException` perfettamente cooperare con MSAL dell'eccezione.
  2. Nel `AcquireTokenInteractive` chiamata, è possibile usare `.WithCustomUI()` modificatore passando l'istanza di classe personalizzata interfaccia utente web

     ```CSharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation---seleniumwebui"></a>Esempi di implementazione di ICustomWebUi in automazione di test - SeleniumWebUI

Il team MSAL.NET è riscritte i test dell'interfaccia utente per sfruttare questo meccanismo di estendibilità. Nel caso in cui si è interessati è possibile avere un quadro il [SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160) classe nel codice sorgente MSAL.NET

#### <a name="other-optional-parameters"></a>Altri parametri facoltativi

Altre informazioni su tutti gli altri parametri facoltativi per `AcquireTokenInteractive` nella documentazione di riferimento per [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods)

## <a name="integrated-windows-authentication"></a>Autenticazione integrata di Windows

Se si desidera accedere un utente di dominio a un dominio o in Azure AD aggiunti a un computer, è necessario usare:

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

### <a name="constraints"></a>Vincoli

- AcquireTokenByIntegratedWindowsAuth (IWA) è disponibile solo per **federati** solo per utenti, ovvero, gli utenti creati in Active Directory e supportato da Azure Active Directory. Gli utenti creati direttamente in AAD, senza il backup di Active Directory - **gestito** utenti - non è possibile usare questo flusso di autenticazione. Questa limitazione non influenza il flusso di nome utente/Password.
- Autenticazione integrata di Windows sia per le app scritte per .NET Framework, .NET Core e le piattaforme UWP
- Autenticazione integrata di Windows non bypassano MFA (multi-factor authentication). Se è configurata l'autenticazione a più fattori, autenticazione integrata di Windows potrebbe non riuscire a se una richiesta di autenticazione a più fattori è necessaria, poiché l'autenticazione a più fattori richiede l'intervento dell'utente.
  > [!NOTE]
  > Questo è difficile. Autenticazione integrata di Windows non è interattivo, ma richiede 2FA l'interattività con gli utenti. Non si controlla quando il provider di identità richiede 2FA per essere eseguita, l'amministratore del tenant viene. Dal nostro osservazioni, 2FA è obbligatorio quando si accede da un altro paese, quando non è connesso tramite VPN a una rete aziendale e a volte anche quando si è connessi tramite VPN. Non prevede un set di regole deterministico, Azure Active Directory Usa l'intelligenza artificiale per conoscere in modo continuativo se 2FA è necessaria. È necessario il fallback a un prompt utente (interattivo l'autenticazione o un dispositivo flusso del codice) se si verifica un errore di autenticazione integrata di Windows.

- L'autorità passato il `PublicClientApplicationBuilder` deve essere:
  - tenant-ed (nel formato `https://login.microsoftonline.com/{tenant}/` in cui `tenant` è il guid che rappresenta l'ID tenant o in un dominio associato al tenant.
  - per qualsiasi lavoro e gli account dell'istituto di istruzione (`https://login.microsoftonline.com/organizations/`)

  > Non sono supportati gli account personali Microsoft (non è possibile usare endpoint /common o /consumers tenant)

- Poiché l'autenticazione integrata di Windows è un flusso invisibile all'utente:
  - l'utente dell'applicazione deve avere precedentemente ottenuto il consenso a usare l'applicazione
  - o l'amministratore del tenant deve avere precedentemente fornito il consenso a tutti gli utenti nel tenant di usare l'applicazione.
  - In altre parole:
    - gli sviluppatori hanno sia premuto la **Concedi** pulsante nel portale di Azure per l'utente
    - o un amministratore del tenant ha premuto il **concedere/revocare il consenso dell'amministratore per {dominio del tenant}** pulsante la **autorizzazioni delle API** scheda della finestra di registrazione per l'applicazione (vedere [aggiungere le autorizzazioni per accedere ad API web](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis#add-permissions-to-access-web-apis))
    - o che vengono rese disponibili agli utenti fornire il consenso all'applicazione (vedere [richiesta di consenso utente singoli](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-individual-user-consent))
    - o aver fornito un modo per l'amministratore del tenant di concedere il consenso per l'applicazione (vedere [consenso dell'amministratore](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-consent-for-an-entire-tenant))

- Questo flusso è abilitato per desktop .net, .net core e Windows universale (UWP) app. In .NET core solo l'overload richiede il nome utente è disponibile, come la piattaforma .NET Core non è possibile richiedere il nome utente per il sistema operativo.
  
Per altre informazioni sul consenso dell'utente, vedere [v2.0 autorizzazioni e consenso](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)

### <a name="how-to-use-it"></a>Come usarlo

In genere è necessario un solo parametro (`scopes`). Tuttavia in base al modo in cui l'amministratore di Windows ha il programma di installazione dei criteri, può essere possibile che le applicazioni nel computer windows non sono autorizzate a cercare l'utente ha eseguito l'accesso. In tal caso, usare un secondo metodo `.WithUsername()` e passare il nome utente dell'utente connesso come formato di UPN - `joe@contoso.com`.

L'esempio seguente presenta il caso più recente, con spiegazioni del tipo di eccezioni che è possibile ottenere e delle relative attenuazioni

```CSharp
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

Per l'elenco dei possibili modificatori in AcquireTokenByIntegratedWindowsAuthentication, vedere [AcquireTokenByIntegratedWindowsAuthParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder?view=azure-dotnet-preview#methods)

## <a name="username--password"></a>Nome utente/password

È anche possibile acquisire un token, fornendo il nome utente e password. Questo flusso è limitato e non è consigliato, ma sono comunque usare casi in cui è necessario.

### <a name="this-flow-isnt-recommended"></a>Questo flusso non è consigliato

Questo flusso è **sconsigliato** perché l'applicazione che richiede un utente la password non è protetta. Per altre informazioni su questo problema, vedere [questo articolo](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). Il flusso preferito prima di acquisire un token in modo invisibile in computer appartenenti al dominio di Windows viene [l'autenticazione integrata di Windows](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication). In caso contrario, è anche possibile usare [flusso del codice di dispositivo](https://aka.ms/msal-net-device-code-flow)

> Anche se ciò è utile in alcuni casi (scenari DevOps), se si vuole usare nome utente/password negli scenari interattivi in cui vengono forniti i onw dell'interfaccia utente, è consigliabile pensare davvero sullo spostamento dal controllo. Tramite nome utente/password si è offrendo remota una serie di operazioni:

> - Core ai tenant dell'identità moderna: password Ottiene operava riprodotti. Poiché abbiamo questo concetto di un segreto di condivisione che può essere intercettato.
> Questa impostazione è incompatibile con senza password.
> - gli utenti che devono eseguire MFA non sarà in grado di effettuare l'accesso (come non vi è alcuna interazione)
> - Utenti non saranno in grado di eseguire single sign-on

### <a name="constraints"></a>Vincoli

Inoltre, si applicano i vincoli seguenti:

- Il flusso di nome utente/Password non è compatibile con l'accesso condizionale e multi-factor authentication: Di conseguenza, se l'app viene eseguita in un tenant di Azure AD in cui l'amministratore del tenant richiede l'autenticazione a più fattori, è possibile usare questo flusso. Molte organizzazioni di farlo.
- Funziona solo per lavoro e gli account dell'istituto di istruzione (non MSA)
- Il flusso è disponibile per desktop .net e .net core, ma non in UWP

### <a name="b2c-specifics"></a>Specifiche di B2C

[Altre informazioni sull'uso di ROPC con B2C](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c).

### <a name="how-to-use-it"></a>Viene illustrato come utilizzarlo?

`IPublicClientApplication`contiene il metodo `AcquireTokenByUsernamePassword`

L'esempio seguente viene presentato un caso semplificato

```CSharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuild.Create(clientId)
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

L'esempio seguente presenta il caso più recente, con spiegazioni del tipo di eccezioni che è possibile ottenere e delle relative attenuazioni

```CSharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuild.Create(clientId)
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

Per informazioni dettagliate su tutti i modificatori che possono essere applicati a `AcquireTokenByUsernamePassword`, vedere [AcquireTokenByUsernamePasswordParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyusernamepasswordparameterbuilder?view=azure-dotnet-preview#methods)

## <a name="command-line-tool-without-web-browser"></a>Strumento da riga di comando (senza un web browser)

### <a name="device-code-flow-why-and-how"></a>Flusso del codice di dispositivo per questo motivo? E come?

Se si sta scrivendo uno strumento da riga di comando (che non dispone di controlli Web) e non possono o non si desidera utilizzare i flussi precedenti, dovrai usare `AcquireTokenWithDeviceCode`.

Autenticazione interattiva con Azure AD richiede un web browser (per informazioni dettagliate, vedere [sull'utilizzo dei web browser](https://aka.ms/msal-net-uses-web-browser)). Tuttavia, per autenticare gli utenti nei dispositivi o i sistemi operativi che non forniscono un Web browser, flusso di codice del dispositivo consente all'utente di usare un altro dispositivo (ad esempio un altro computer o un telefono cellulare) per accedere modo interattivo. Tramite il flusso del codice di dispositivo, l'applicazione ottiene i token mediante un processo in due passaggi appositamente progettato per questi dispositivi/sistemi operativi. Esempi di tali applicazioni sono applicazioni in esecuzione in iOT o strumenti da riga di comando (CLI). L'idea è che:

1. Ogni volta che è necessaria l'autenticazione utente, l'app fornisce un codice e chiede all'utente di usare un altro dispositivo (ad esempio un smartphone connesso a internet) per passare a un URL (ad esempio, `https://microsoft.com/devicelogin`), in cui l'utente verrà richiesto di immettere il codice. Di fatto, la pagina web guiderà l'utente tramite un'esperienza di autenticazione normali, incluse le richieste di consenso e l'autenticazione a più fattori se necessario.

2. Al termine dell'autenticazione, l'app della riga di comando riceverà il token richiesto tramite un canale di supporto e verrà usato per eseguire le chiamate all'API web che è necessario.

### <a name="code"></a>Codice

`IPublicClientApplication`contiene un metodo denominato `AcquireTokenWithDeviceCode`

```CSharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

Questo metodo accetta come parametri:

- Il `scopes` per richiedere un token di accesso
- Un callback che riceverà il `DeviceCodeResult`

  ![image](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

Esempio di codice seguente presenta il caso più recente, con spiegazioni di eccezioni che è possibile ottenere e limitarne gli effetti del tipo.

```CSharp
static async Task<AuthenticationResult> GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication pca = PublicClientApplicationBuilder
      .Create(clientId)
      .WithAuthority(authority)
      .Build();

 AuthenticationResult result = null;
 var accounts = await app.GetAccountsAsync();

 // All AcquireToken* methods store the tokens in the cache, so check the cache first
 try
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
       .ExecuteAsync();
 }
 catch (MsalUiRequiredException ex)
 {
  // A MsalUiRequiredException happened on AcquireTokenSilent.
  // This indicates you need to call AcquireTokenInteractive to acquire a token
  System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");
 }

 try
 {
  result = await app.AcquireTokenWithDeviceCode(scopes,
      deviceCodeCallback =>
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
  // If you use a CancellationToken, and call the Cancel() method on it, then this may be triggered
  // to indicate that the operation was cancelled.
  // See https://docs.microsoft.com/dotnet/standard/threading/cancellation-in-managed-threads
  // for more detailed information on how C# supports cancellation in managed threads.
 }
 catch (MsalClientException ex)
 {
  // Verification code expired before contacting the server
  // This exception will occur if the user does not manage to sign-in before a time out (15 mins) and the
  // call to `AcquireTokenWithDeviceCode` is not cancelled in between
 }
}
```

## <a name="file-based-token-cache"></a>Cache dei token basati su file

In MSAL.NET viene fornita per impostazione predefinita una cache dei token in memoria.

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-appsweb-apis"></a>La serializzazione è personalizzabile nelle App desktop Windows e App web/API web

Nel caso di .NET Framework e .NET core, se si non esegue alcuna operazione aggiuntiva, la cache dei token in memoria ha una durata per la durata dell'applicazione. Per comprendere il motivo per cui la serializzazione non è fornita per impostazione predefinita, memorizza MSAL .NET le applicazioni desktop/core possono essere applicazioni di Windows (che hanno accesso al file system), o console **ma anche** applicazioni Web o API web. Queste App Web e API web potrebbe usare alcuni meccanismi specifici della cache, ad esempio database, cache distribuite, cache redis, e così via. Affinché un'applicazione di cache dei token persistente in .NET Desktop o Core, è necessario personalizzare la serializzazione.

Classi e interfacce coinvolti nella serializzazione della cache dei token sono i seguenti tipi:

- ``ITokenCache``, che definisce gli eventi per sottoscrivere le richieste di serializzazione della cache dei token, nonché i metodi per serializzare o deserializzare la cache in diversi formati (v3.0 ADAL, MSAL 2.x e MSAL 3.x = ADAL v5.0)
- ``TokenCacheCallback`` è un callback passato agli eventi in modo da consentire di gestire la serializzazione. verrà chiamati con argomenti di tipo ``TokenCacheNotificationArgs``.
- ``TokenCacheNotificationArgs`` fornisce solo la ``ClientId`` dell'applicazione e un riferimento all'utente per cui il token è disponibile

  ![image](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> MSAL.NET crea token memorizzati nella cache e fornisce la cache `IToken` quando si chiamano i metodi `GetUserTokenCache` e `GetAppTokenCache` di un'applicazione. Non si dovrebbe implementare l'interfaccia. Quando si implementa una serializzazione della cache dei token personalizzata, occorre:
>
> - Reagire agli "eventi" `BeforeAccess` e `AfterAccess`. Il`BeforeAccess` delegato ha la responsabilità di deserializzare la cache, mentre il `AfterAccess` uno è responsabile per la serializzazione della cache.
> - Alcuni di questi eventi archiviano o caricano BLOB, che vengono passati tramite l'argomento dell'evento al tipo di archiviazione desiderato.

Le strategie sono diverse a seconda se si sta scrivendo una serializzazione della cache dei token per un'applicazione client pubblica (Desktop) o un'applicazione client riservata (web/API web app, app daemon).

Poiché V2.x MSAL sono disponibili diverse opzioni, a seconda se si vuole serializzare la cache solo per il formato MSAL.NET (cache formato unificato che è comune con MSAL, ma anche tra le piattaforme) o se si desidera supportare le [legacy](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) Serializzazione della cache dei token di ADAL V3.

La personalizzazione della serializzazione di cache di Token di condividere lo stato SSO tra ADAL.NET 3.x, ADAL.NET 5.x e MSAL.NET sono illustrati nella parte dell'esempio seguente: [active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2)

### <a name="simple-token-cache-serialization-msal-only"></a>Serializzazione semplice della cache dei token (solo MSAL)

Di seguito è riportato un esempio di implementazione semplice della serializzazione personalizzata di una cache dei token per le applicazioni desktop. Qui la cache dei token utente in un file nella stessa cartella dell'applicazione.

Dopo aver compilato l'applicazione, si abilita la serializzazione tramite la chiamata ``TokenCacheHelper.EnableSerialization()`` passando all'applicazione `UserTokenCache`

```CSharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

Questa classe helper è simile al frammento di codice seguente:

```CSharp
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

Un'anteprima di una cache dei token qualità del prodotto serializzatore basati su file per le applicazioni client pubblico (per le applicazioni desktop in esecuzione su Windows, Mac e linux) è disponibile il [Microsoft.Identity.Client.Extensions.Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) libreria open source. È possibile includerla nelle applicazioni dal pacchetto NuGet seguente: [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

> Dichiarazione di non responsabilità. La libreria Microsoft.Identity.Client.Extensions.Msal è un'estensione tramite MSAL.NET. Le classi in queste librerie potrebbero arrivino in MSAL.NET in futuro, così come sono oppure con le modifiche di rilievo.

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>Serializzazione della cache dei token dual (cache MSAL unificata + ADAL V3)

Se si desidera implementare la serializzazione della cache dei token entrambi con la Unified memorizzare nella cache di formato (comuni a ADAL.NET 4.x e MSAL.NET 2.x e con altri MSALs la stessa generazione o meno recenti, sulla stessa piattaforma), è possibile ottenere ispirazione dal codice seguente :

```CSharp
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

Questa volta la classe helper è simile al codice seguente:

```CSharp
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
   usertokenCache = cache;
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   usertokenCache.SetBeforeAccess(BeforeAccessNotification);
   usertokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Token cache
  /// </summary>
  static ITokenCache usertokenCache;

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
> [Chiamare un'API web dall'app desktop](scenario-desktop-call-api.md)
