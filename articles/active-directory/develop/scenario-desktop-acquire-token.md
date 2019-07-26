---
title: App desktop che chiama le API Web (acquisizione di un token per l'app)-piattaforma di identità Microsoft
description: Informazioni su come creare un'app desktop che chiama le API Web (acquisendo un token per l'app |)
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
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 05596365dfa011675f38beda2435fdda1a53a5a3
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2019
ms.locfileid: "68488853"
---
# <a name="desktop-app-that-calls-web-apis---acquire-a-token"></a>App desktop che chiama le API Web-Acquisisci un token

Una volta compilato `IPublicClientApplication`, verrà usato per acquisire un token da usare per chiamare un'API Web.

## <a name="recommended-pattern"></a>Modello consigliato

L'API Web è definita da `scopes`. Indipendentemente dall'esperienza fornita nell'applicazione, il modello che si vuole usare è:

- Tentativo sistematico di ottenere un token dalla cache dei token chiamando`AcquireTokenSilent`
- Se la chiamata ha esito negativo `AcquireToken` , usare il flusso che si vuole usare (qui `AcquireTokenXX`rappresentato da)

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

Ecco i dettagli dei vari modi per acquisire i token in un'applicazione desktop

## <a name="acquiring-a-token-interactively"></a>Acquisizione di un token in modo interattivo

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

### <a name="mandatory-parameters"></a>Parametri obbligatori

`AcquireTokenInteractive`dispone di un solo parametro ``scopes``obbligatorio, che contiene un'enumerazione di stringhe che definiscono gli ambiti per i quali è richiesto un token. Se il token è per la Microsoft Graph, gli ambiti richiesti sono disponibili in riferimento API di ogni API Microsoft Graph nella sezione denominata "autorizzazioni". Per [elencare i contatti dell'utente](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts), ad esempio, sarà necessario usare l'ambito "User. Read", "Contacts. Read". Vedere anche [Microsoft Graph riferimento alle autorizzazioni](https://developer.microsoft.com/graph/docs/concepts/permissions_reference).

In Android è necessario specificare anche l'attività padre (usando `.WithParentActivityOrWindow`, vedere di seguito), in modo che il token torni a tale attività padre dopo l'interazione. Se non viene specificato, verrà generata un'eccezione durante la chiamata `.ExecuteAsync()`a.

### <a name="specific-optional-parameters"></a>Parametri facoltativi specifici

#### <a name="withparentactivityorwindow"></a>WithParentActivityOrWindow

Interactive, l'interfaccia utente è importante. `AcquireTokenInteractive`dispone di uno specifico parametro facoltativo che consente di specificare, per le piattaforme che lo supportano, l'interfaccia utente padre. Se usato in un'applicazione desktop, `.WithParentActivityOrWindow` ha un tipo diverso a seconda della piattaforma:

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

- In .NET standard, il previsto `object` è un `Activity` in Android, un `UIViewController` in iOS, un `NSWindow` su Mac e un `IWin32Window` o `IntPr` Windows.
- In Windows è necessario chiamare `AcquireTokenInteractive` dal thread dell'interfaccia utente in modo che il browser incorporato ottenga il contesto di sincronizzazione dell'interfaccia utente appropriato.  Se non si chiama dal thread UI, i messaggi non vengono pompati correttamente e/o scenari di deadlock con l'interfaccia utente. Un modo per chiamare MSAL dal thread UI se non si è già nel thread dell'interfaccia utente consiste nell'usare `Dispatcher` in WPF.
- Se si usa WPF, per ottenere una finestra da un controllo WPF, è possibile usare `WindowInteropHelper.Handle` la classe. La chiamata è quindi da un controllo WPF (`this`):
  
  ```CSharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>WithPrompt

`WithPrompt()`viene usato per controllare l'interattività con l'utente specificando una richiesta

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

La classe definisce le costanti seguenti:

- ``SelectAccount``: forza il STS a presentare la finestra di dialogo di selezione dell'account contenente gli account per i quali l'utente dispone di una sessione. Questa opzione è utile quando gli sviluppatori di applicazioni desiderano consentire all'utente di scegliere tra identità diverse. Questa opzione consente a MSAL di ``prompt=select_account`` inviare al provider di identità. Questa opzione è l'impostazione predefinita e consente di garantire la migliore esperienza possibile in base alle informazioni disponibili (account, presenza di una sessione per l'utente e così via). ...). Non modificarla a meno che non si disponga di un buon motivo per eseguire questa operazione.
- ``Consent``: consente allo sviluppatore di applicazioni di forzare la richiesta di consenso dell'utente anche se il consenso è stato concesso prima. In questo caso, MSAL Invia `prompt=consent` al provider di identità. Questa opzione può essere usata in alcune applicazioni con sicurezza mirata, in cui la governance dell'organizzazione richiede che all'utente venga visualizzata la finestra di dialogo di consenso ogni volta che viene usata l'applicazione.
- ``ForceLogin``: consente allo sviluppatore di applicazioni di chiedere all'utente le credenziali dal servizio anche se il prompt utente non è necessario. Questa opzione può essere utile se l'acquisizione di un token ha esito negativo, per consentire all'utente di eseguire di nuovo l'accesso. In questo caso, MSAL Invia `prompt=login` al provider di identità. Anche in questo caso abbiamo visto che è stato usato in alcune applicazioni mirate alla sicurezza in cui la governance dell'organizzazione richiede che l'utente si riconnetta ogni volta che accede a parti specifiche di un'applicazione.
- ``Never``(solo per .NET 4,5 e WinRT) non richiede l'intervento dell'utente, ma tenterà invece di usare il cookie archiviato nella visualizzazione Web incorporata nascosta (vedere di seguito: Visualizzazioni Web in MSAL.NET). L'uso di questa opzione potrebbe avere esito negativo `AcquireTokenInteractive` e in questo caso genererà un'eccezione per notificare che è necessaria un'interazione dell'interfaccia utente ed `Prompt` è necessario usare un altro parametro.
- ``NoPrompt``: Non invierà alcun messaggio di richiesta al provider di identità. Questa opzione è utile solo per Azure AD B2C modificare i criteri del profilo (vedere le [specifiche B2C](https://aka.ms/msal-net-b2c-specificities)).

#### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Questo modificatore viene usato in uno scenario avanzato in cui si vuole che l'utente preacconsente a diverse risorse in anticipo (e non vuole usare il consenso incrementale, che viene in genere usato con MSAL.NET/Microsoft Identity Platform v 2.0). Per informazioni dettagliate [, vedere Procedura: ottenere il consenso dell'utente in anticipo per diverse risorse](scenario-desktop-production.md#how-to-have--the-user-consent-upfront-for-several-resources).

```CSharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>WithCustomWebUi

##### <a name="withcustomwebui-is-an-extensibility-point"></a>WithCustomWebUi è un punto di estendibilità

`WithCustomWebUi`è un punto di estendibilità che consente di fornire la propria interfaccia utente nelle applicazioni client pubbliche e di consentire all'utente di passare attraverso l'endpoint/Authorize del provider di identità e di consentire l'accesso e il consenso. MSAL.NET può, quindi, riscattare il codice di autenticazione e ottenere un token. È ad esempio possibile usare in Visual Studio le applicazioni degli elettroni, ad esempio il feedback di VS, per fornire l'interazione Web, ma lasciarla MSAL.NET per eseguire la maggior parte delle operazioni. È anche possibile usarlo se si vuole fornire l'automazione dell'interfaccia utente. Nelle applicazioni client pubbliche, MSAL.NET usa la chiave PKCE standard ([RFC 7636-Proof per lo scambio di codice dai client OAuth pubblici](https://tools.ietf.org/html/rfc7636)) per garantire che venga rispettata la sicurezza: Solo MSAL.NET può riscattare il codice.

  ```CSharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="how-to-use-withcustomwebui"></a>Come usare WithCustomWebUi

Per utilizzare `.WithCustomWebUI`, è necessario:
  
  1. Implementare l' `ICustomWebUi` interfaccia (vedere [qui](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70). In pratica, è necessario implementare un metodo `AcquireAuthorizationCodeAsync` che accetti l'URL del codice di autorizzazione (calcolato da MSAL.NET), consentendo all'utente di eseguire l'interazione con il provider di identità e restituendo quindi l'URL con cui il provider di identità ha chiamato di nuovo l'implementazione (incluso il codice di autorizzazione). In caso di problemi, l'implementazione deve generare un' `MsalExtensionException` eccezione per cooperare con MSAL.
  2. Nella chiamata, è possibile usare `.WithCustomUI()` il modificatore passando l'istanza dell'interfaccia utente Web personalizzata `AcquireTokenInteractive`

     ```CSharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation---seleniumwebui"></a>Esempi di implementazione di ICustomWebUi nell'automazione di test-SeleniumWebUI

Il team di MSAL.NET ha riscritto i test dell'interfaccia utente per sfruttare questo meccanismo di estendibilità. Se si è interessati, è possibile esaminare la classe [SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160) nel codice sorgente MSAL.NET

#### <a name="other-optional-parameters"></a>Altri parametri facoltativi

Altre informazioni su tutti gli altri parametri facoltativi `AcquireTokenInteractive` per sono disponibili nella documentazione di riferimento per [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods)

## <a name="integrated-windows-authentication"></a>Autenticazione integrata di Windows

Se si vuole accedere a un utente di dominio in un dominio o in un computer Azure AD aggiunto, è necessario usare:

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

### <a name="constraints"></a>Vincoli

- AcquireTokenByIntegratedWindowsAuth (IWA) può essere utilizzato solo per gli utenti **federati** , ovvero per gli utenti creati in un Active Directory e supportati da Azure Active Directory. Utenti creati direttamente in AAD, senza utenti **gestiti** dal supporto di Active Directory, non possono usare questo flusso di autenticazione. Questa limitazione non influisce sul flusso di nome utente/password.
- AUTENTICAZIONE integrata per le app scritte per le piattaforme .NET Framework, .NET Core e UWP
- La funzionalità di autenticazione a più fattori non viene ignorata. Se è configurata l'autenticazione a più fattori, è possibile che l'autenticazione a più fattori non riesca se è necessaria una richiesta di autenticazione a più fattori
  > [!NOTE]
  > Questo è un problema complesso. IWA è non interattivo, ma l'autenticazione a più fattori richiede l'interattività dell'utente. Non si controlla quando il provider di identità richiede l'esecuzione dell'autenticazione a più fattori, l'amministratore del tenant. Dalle osservazioni, l'autenticazione a più fattori è necessaria quando si effettua l'accesso da un paese diverso, quando non si è connessi tramite VPN a una rete aziendale e talvolta anche quando si è connessi tramite VPN. Non è previsto un set di regole deterministico, Azure Active Directory usa l'intelligenza artificiale per apprendere continuamente se è necessaria l'autenticazione a più fattori. È consigliabile eseguire il fallback a un prompt utente (autenticazione interattiva o flusso del codice del dispositivo) se non è possibile eseguire l'autenticazione integrata.

- L'autorità passata `PublicClientApplicationBuilder` deve essere:
  - tenant-ed (nel formato `https://login.microsoftonline.com/{tenant}/` in cui `tenant` è il GUID che rappresenta l'ID tenant o un dominio associato al tenant.
  - per tutti gli account aziendali e dell'`https://login.microsoftonline.com/organizations/`Istituto di istruzione ()

  > Gli account personali Microsoft non sono supportati (non è possibile usare tenant/Common o/consumers)

- Poiché l'autenticazione integrata di Windows è un flusso invisibile all'utente:
  - l'utente dell'applicazione deve avere precedentemente acconsentito all'uso dell'applicazione
  - in alternativa, l'amministratore del tenant deve avere acconsentito in precedenza a tutti gli utenti del tenant per usare l'applicazione.
  - In altre parole:
    - uno sviluppatore ha premuto il pulsante **Concedi** sul portale di Azure per se stessi,
    - in alternativa, un amministratore del tenant ha premuto il pulsante **Concedi/revoca il consenso dell'amministratore per il dominio del tenant}** nella scheda **autorizzazioni API** della registrazione per l'applicazione (vedere [aggiungere autorizzazioni per accedere alle API Web](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis#add-permissions-to-access-web-apis)).
    - in alternativa, è stato fornito un modo per consentire agli utenti di concedere il consenso all'applicazione (vedere [richiesta di consenso utente singolo](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-individual-user-consent))
    - oppure è stato fornito un modo per consentire all'amministratore del tenant di acconsentire all'applicazione (vedere il [consenso dell'amministratore](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-consent-for-an-entire-tenant))

- Questo flusso è abilitato per le app .NET desktop, .NET Core e Windows Universal (UWP). In .NET Core è disponibile solo l'overload che accetta il nome utente, perché la piattaforma .NET Core non può richiedere il nome utente al sistema operativo.
  
Per altre informazioni sul consenso, vedere [autorizzazioni e consenso per la versione 2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)

### <a name="how-to-use-it"></a>Come usarlo

In genere è necessario un solo parametro`scopes`(). Tuttavia, a seconda del modo in cui l'amministratore di Windows ha configurato i criteri, è possibile che le applicazioni nel computer Windows non siano autorizzate a cercare l'utente che ha eseguito l'accesso. In tal caso, usare un secondo metodo `.WithUsername()` e passare il nome utente dell'utente connesso come formato UPN-. `joe@contoso.com`

Nell'esempio seguente viene presentato il caso più recente, con spiegazioni del tipo di eccezioni che è possibile ottenere e delle relative attenuazioni.

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

Per l'elenco dei modificatori possibili in AcquireTokenByIntegratedWindowsAuthentication, vedere [AcquireTokenByIntegratedWindowsAuthParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder?view=azure-dotnet-preview#methods)

## <a name="username--password"></a>Nome utente/password

È anche possibile acquisire un token fornendo il nome utente e la password. Questo flusso è limitato e non è consigliabile, ma sono ancora presenti casi di utilizzo in cui è necessario.

### <a name="this-flow-isnt-recommended"></a>Questo flusso non è consigliato

Questo flusso **non è consigliato** perché l'applicazione che richiede un utente per la password non è protetta. Per ulteriori informazioni su questo problema, vedere [questo articolo](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). Il flusso preferito per acquisire un token in modo invisibile all'utente nei computer Windows aggiunti a un dominio è [l'autenticazione integrata di Windows](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication). In caso contrario, è anche possibile usare il [flusso del codice del dispositivo](https://aka.ms/msal-net-device-code-flow)

> [!NOTE] 
> Sebbene questo sia utile in alcuni casi (scenari DevOps), se si vuole usare il nome utente e la password in scenari interattivi in cui si fornisce l'interfaccia utente di onw, è opportuno pensare a come allontanarsi. Utilizzando il nome utente e la password vengono configurati alcuni elementi:
>
> - tenant principali dell'identità moderna: la password viene pescata, rieseguita. Poiché questo è il concetto di segreto di condivisione che può essere intercettato.
> Questa operazione non è compatibile con le password.
> - Gli utenti che devono eseguire l'autenticazione a più fattori non saranno in grado di accedere (poiché non esiste alcuna interazione)
> - Gli utenti non saranno in grado di eseguire l'accesso Single Sign-on

### <a name="constraints"></a>Vincoli

Si applicano anche i vincoli seguenti:

- Il flusso di nome utente/password non è compatibile con l'accesso condizionale e l'autenticazione a più fattori: Di conseguenza, se l'app viene eseguita in un tenant di Azure AD in cui l'amministratore tenant richiede multi-factor authentication, non è possibile usare questo flusso. Molte organizzazioni lo eseguono.
- Funziona solo per gli account aziendali e dell'Istituto di istruzione (non MSA)
- Il flusso è disponibile su .NET desktop e .NET Core, ma non su UWP

### <a name="b2c-specifics"></a>Specifiche B2C

[Altre informazioni sull'uso di ROPC con B2C](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c).

### <a name="how-to-use-it"></a>Come usarlo?

`IPublicClientApplication`contiene il metodo`AcquireTokenByUsernamePassword`

L'esempio seguente presenta un caso semplificato

```CSharp
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

Nell'esempio seguente viene presentato il caso più recente, con spiegazioni del tipo di eccezioni che è possibile ottenere e delle relative attenuazioni.

```CSharp
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

Per informazioni dettagliate su tutti i modificatori che possono essere applicati `AcquireTokenByUsernamePassword`a, vedere [AcquireTokenByUsernamePasswordParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyusernamepasswordparameterbuilder?view=azure-dotnet-preview#methods)

## <a name="command-line-tool-without-web-browser"></a>Strumento da riga di comando (senza Web browser)

### <a name="device-code-flow-why-and-how"></a>Perché il flusso di codice del dispositivo? E come?

Se si sta scrivendo uno strumento da riga di comando (che non contiene controlli Web) e non è possibile o non si vuole usare i flussi precedenti, è necessario usare `AcquireTokenWithDeviceCode`.

L'autenticazione interattiva con Azure AD richiede un Web browser. per informazioni dettagliate, vedere [utilizzo dei Web browser](https://aka.ms/msal-net-uses-web-browser). Tuttavia, per autenticare gli utenti su dispositivi o sistemi operativi che non forniscono una Web browser, il flusso del codice del dispositivo consente all'utente di usare un altro dispositivo, ad esempio un altro computer o un telefono cellulare, per accedere in modo interattivo. Usando il flusso del codice del dispositivo, l'applicazione ottiene i token tramite un processo in due passaggi appositamente progettato per questi dispositivi/sistema operativo. Esempi di tali applicazioni sono le applicazioni in esecuzione su Internet o gli strumenti da riga di comando (CLI). L'idea è che:

1. Ogni volta che è necessaria l'autenticazione utente, l'app fornisce un codice e chiede all'utente di usare un altro dispositivo, ad esempio uno smartphone connesso a Internet, per passare a un URL `https://microsoft.com/devicelogin`(ad esempio,), in cui all'utente verrà richiesto di immettere il codice. In tal caso, la pagina Web consentirà all'utente di eseguire una normale esperienza di autenticazione, incluse le richieste di consenso e la funzionalità di autenticazione a più fattori, se necessario.

2. Una volta completata l'autenticazione, l'app della riga di comando riceverà i token necessari tramite un canale di back-through e li userà per eseguire le chiamate API Web necessarie.

### <a name="code"></a>Codice

`IPublicClientApplication`contiene un metodo denominato`AcquireTokenWithDeviceCode`

```CSharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

Questo metodo accetta come parametri:

- Oggetto `scopes` per cui richiedere un token di accesso
- Callback che riceverà il`DeviceCodeResult`

  ![image](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

Il codice di esempio seguente presenta il caso più recente, con spiegazioni del tipo di eccezioni che è possibile ottenere e la relativa mitigazione.

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

## <a name="file-based-token-cache"></a>Cache di token basata su file

In MSAL.NET viene fornita per impostazione predefinita una cache dei token in memoria.

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-appsweb-apis"></a>La serializzazione è personalizzabile in app desktop di Windows e app Web/API Web

Nel caso di .NET Framework e .NET Core, se non si esegue alcuna operazione aggiuntiva, la cache dei token in memoria dura per la durata dell'applicazione. Per comprendere il motivo per cui la serializzazione non è fornita in modo predefinito, ricordarsi che le applicazioni desktop/Core .NET MSAL possono essere applicazioni console o Windows (che potrebbero accedere al file system), **ma anche** applicazioni Web o API Web. Queste app Web e API Web possono usare alcuni meccanismi di cache specifici, ad esempio database, cache distribuite, cache Redis e così via. Per avere un'applicazione cache dei token persistente in .NET desktop o Core, è necessario personalizzare la serializzazione.

Le classi e le interfacce necessarie per la serializzazione della cache dei token sono i tipi seguenti:

- ``ITokenCache``, che definisce gli eventi per la sottoscrizione delle richieste di serializzazione della cache dei token, nonché i metodi per serializzare o deserializzare la cache in vari formati (ADAL v 3.0, MSAL 2. x e MSAL 3. x = ADAL v 5.0)
- ``TokenCacheCallback`` è un callback passato agli eventi in modo da consentire di gestire la serializzazione. verranno chiamati con argomenti di tipo ``TokenCacheNotificationArgs``.
- ``TokenCacheNotificationArgs``fornisce solo l' ``ClientId`` oggetto dell'applicazione e un riferimento all'utente per il quale è disponibile il token

  ![image](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> MSAL.NET crea token memorizzati nella cache e fornisce la cache `IToken` quando si chiamano le proprietà `UserTokenCache` e `AppTokenCache` di un'applicazione. Non è necessario implementare l'interfaccia manualmente. Quando si implementa una serializzazione della cache dei token personalizzata, occorre:
>
> - Reagire `BeforeAccess` a `AfterAccess` e "Events" (oppure a una controparte *Async* ). Il`BeforeAccess` delegato è responsabile della deserializzazione della cache, mentre quella `AfterAccess` è responsabile della serializzazione della cache.
> - Alcuni di questi eventi archiviano o caricano BLOB, che vengono passati tramite l'argomento dell'evento al tipo di archiviazione desiderato.

Le strategie sono diverse a seconda che si stia scrivendo una serializzazione della cache dei token per un'applicazione client pubblica (desktop) o un'applicazione client riservata (app Web/API Web, app daemon).

Dal momento che MSAL V2. x sono disponibili diverse opzioni, a seconda che si voglia serializzare la cache solo nel formato MSAL.NET (cache di formato unificata comune a MSAL, ma anche attraverso le piattaforme) o se si desidera supportare anche la cache dei token [legacy](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) serializzazione di ADAL V3.

La personalizzazione della serializzazione della cache dei token per condividere lo stato SSO tra ADAL.NET 3. x, ADAL.NET 5. x e MSAL.NET è illustrata nell'esempio seguente: [Active-Directory-DotNet-V1-to-V2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2)

### <a name="simple-token-cache-serialization-msal-only"></a>Serializzazione semplice della cache dei token (solo MSAL)

Di seguito è riportato un esempio di implementazione semplice della serializzazione personalizzata di una cache dei token per le applicazioni desktop. Qui la cache del token utente in un file nella stessa cartella dell'applicazione.

Dopo aver compilato l'applicazione, è possibile abilitare la serializzazione ``TokenCacheHelper.EnableSerialization()`` chiamando il passaggio dell'applicazione`UserTokenCache`

```CSharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

Questa classe helper ha un aspetto simile al frammento di codice seguente:

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

Un'anteprima del serializzatore basato su file della cache dei token di qualità del prodotto per le applicazioni client pubbliche (per le applicazioni desktop eseguite in Windows, Mac e Linux) è disponibile dalla libreria open source [Microsoft. Identity. client. Extensions. MSAL](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) . È possibile includerla nelle applicazioni dal pacchetto NuGet seguente: [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

> [!NOTE]
> Non responsabilità. La libreria Microsoft. Identity. client. Extensions. MSAL è un'estensione di MSAL.NET. In futuro, le classi in queste librerie potrebbero entrare in MSAL.NET, così come sono o con modifiche di rilievo.

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>Serializzazione Dual token cache (MSAL Unified cache + ADAL V3)

Se si vuole implementare la serializzazione della cache del token con il formato di cache unificata (comune a ADAL.NET 4. x e MSAL.NET 2. x e con altri MSALs della stessa generazione o meno, sulla stessa piattaforma), è possibile ottenere l'ispirazione dal codice seguente. :

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
> [Chiamata di un'API Web dall'app desktop](scenario-desktop-call-api.md)
