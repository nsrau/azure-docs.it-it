---
title: Azure AD B2C (MSAL.NET) Azure
titleSuffix: Microsoft identity platform
description: Informazioni su considerazioni specifiche sull'uso di Azure AD B2C con la libreria di autenticazione Microsoft per .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/29/2019
ms.author: jeferrie
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 697b4bc8e3a25085ac6f7d600ea2227dd30a6624
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262815"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>Usare MSAL.NET per accedere agli utenti con identità socialUse MSAL.NET

È possibile usare MSAL.NET per accedere agli utenti con identità di social networking usando [Azure Active Directory B2C (Azure AD B2C)](https://aka.ms/aadb2c). Azure AD B2C si basa sulla nozione di criteri. In MSAL.NET, la specifica di un criterio si traduce in fornire un'autorità.

- Quando si crea un'istanza dell'applicazione client pubblica, è necessario specificare i criteri in authority.
- Quando si desidera applicare un criterio, è `AcquireTokenInteractive` necessario `authority` chiamare un override di contenere un parametro.

Questa pagina è per MSAL 3.x. Se si è interessati a MSAL 2.x, vedere [Specifiche di Azure AD B2C in MSAL 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x).

## <a name="authority-for-a-azure-ad-b2c-tenant-and-policy"></a>Autorità per un tenant e criteri B2C di Azure ADAuthority for a Azure AD B2C tenant and policy

L'autorità da `https://{azureADB2CHostname}/tfp/{tenant}/{policyName}` utilizzare è dove:

- `azureADB2CHostname`è il nome del tenant B2C di Azure `{your-tenant-name}.b2clogin.com`AD più l'host (ad esempio ),
- `tenant`è il nome completo del tenant B2C `{your-tenant-name}.onmicrosoft.com`di Azure AD (ad esempio, ) o il GUID per il tenant, 
- `policyName`il nome del criterio o del flusso utente da applicare (ad esempio "b2c_1_susi" per l'iscrizione/accesso).

Per altre informazioni sulle autorità B2C di Azure AD, vedere questa [documentazione.](/azure/active-directory-b2c/b2clogin)

## <a name="instantiating-the-application"></a>Creazione di un'istanza dell'applicazione

Quando si compila l'applicazione, è necessario fornire l'autorità.

```csharp
// Azure AD B2C Coordinates
public static string Tenant = "fabrikamb2c.onmicrosoft.com";
public static string AzureADB2CHostname = "fabrikamb2c.b2clogin.com";
public static string ClientID = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
public static string PolicySignUpSignIn = "b2c_1_susi";
public static string PolicyEditProfile = "b2c_1_edit_profile";
public static string PolicyResetPassword = "b2c_1_reset";

public static string AuthorityBase = $"https://{AzureADB2CHostname}/tfp/{Tenant}/";
public static string Authority = $"{AuthorityBase}{PolicySignUpSignIn}";
public static string AuthorityEditProfile = $"{AuthorityBase}{PolicyEditProfile}";
public static string AuthorityPasswordReset = $"{AuthorityBase}{PolicyResetPassword}";

application = PublicClientApplicationBuilder.Create(ClientID)
               .WithB2CAuthority(Authority)
               .Build();
```

## <a name="acquire-a-token-to-apply-a-policy"></a>Acquisire un token per applicare un criterioAcquire a token to apply a policy

L'acquisizione di un token per un'API protetta B2C di Azure AD in un'applicazione client pubblica richiede l'uso delle sostituzioni con un'autorità:Acquiring a token for an Azure AD B2C protected API in a public client application requires you to use the overrides with an authority:

```csharp
IEnumerable<IAccount> accounts = await application.GetAccountsAsync();
AuthenticationResult ar = await application .AcquireTokenInteractive(scopes)
                                            .WithAccount(GetAccountByPolicy(accounts, policy))
                                            .WithParentActivityOrWindow(ParentActivityOrWindow)
                                            .ExecuteAsync();
```

con:

- `policy`essere una delle stringhe precedenti `PolicySignUpSignIn`(ad esempio ).
- `ParentActivityOrWindow`è necessario per Android (l'attività) e facoltativo per altre piattaforme che supportano l'interfaccia utente padre, ad esempio windows in Windows e UIViewController in iOS.Is required for Android (the Activity), and optional for other platforms which support the parent UI, such as windows in Windows and UIViewController in iOS. Per ulteriori [informazioni, vedere la finestra di dialogo dell'interfaccia utente](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively#withparentactivityorwindow).
- `GetAccountByPolicy(IEnumerable<IAccount>, string)`è un metodo che trova un account per un determinato criterio. Ad esempio:

  ```csharp
  private IAccount GetAccountByPolicy(IEnumerable<IAccount> accounts, string policy)
  {
   foreach (var account in accounts)
   {
    string userIdentifier = account.HomeAccountId.ObjectId.Split('.')[0];
    if (userIdentifier.EndsWith(policy.ToLower()))
     return account;
   }
   return null;
  }
  ```

L'applicazione di un criterio o di un flusso utente (ad esempio, consentire `AcquireTokenInteractive`all'utente finale di modificare il proprio profilo o reimpostare la password) è attualmente eseguita chiamando . Nel caso di questi due criteri, non si utilizza il token restituito / risultato di autenticazione.

## <a name="special-case-of-editprofile-and-resetpassword-policies"></a>Caso speciale dei criteri EditProfile e ResetPassword

Quando si vuole offrire un'esperienza in cui gli utenti finali accedono con un'identità di social networking e quindi modificano il proprio profilo, si vuole applicare il criterio Modifica profilo B2C di Azure AD. Il modo per eseguire `AcquireTokenInteractive` questa operazione consiste nel chiamare con l'autorità specifica per tale criterio e un prompt impostato per `Prompt.NoPrompt` impedire la visualizzazione della finestra di dialogo di selezione dell'account (poiché l'utente ha già eseguito l'accesso e dispone di una sessione cookie attiva).

```csharp
private async void EditProfileButton_Click(object sender, RoutedEventArgs e)
{
 IEnumerable<IAccount> accounts = await app.GetAccountsAsync();
 try
 {
  var authResult = await app.AcquireToken(scopes:App.ApiScopes)
                               .WithAccount(GetUserByPolicy(accounts, App.PolicyEditProfile)),
                               .WithPrompt(Prompt.NoPrompt),
                               .WithB2CAuthority(App.AuthorityEditProfile)
                               .ExecuteAsync();
  DisplayBasicTokenInfo(authResult);
 }
 catch
 {
  . . .
 }
}
```
## <a name="resource-owner-password-credentials-ropc-with-azure-ad-b2c"></a>Credenziali password del proprietario della risorsa (ROPC) con Azure AD B2C
Per ulteriori dettagli sul flusso ROPC, vedere questa [documentazione](v2-oauth-ropc.md).

Questo flusso non è **consigliato** perché l'applicazione che richiede la password a un utente non è sicura. Per ulteriori informazioni su questo problema, vedere [questo articolo](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). 

Utilizzando nome utente/password, si sta rinunciando a una serie di cose:
- Principi fondamentali dell'identità moderna: la password viene pescata, riprodotta. Perché abbiamo questo concetto di segreto azionario che può essere intercettato. Questo non è compatibile con password.
- Gli utenti che devono eseguire l'autenticazione a più fattori non saranno in grado di accedere (poiché non vi è alcuna interazione).
- Gli utenti non saranno in grado di eseguire l'accesso Single Sign-On.

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>Configurare il flusso ROPC in Azure AD B2CConfigure the ROPC flow in Azure AD B2C
Nel tenant B2C di Azure AD creare un nuovo flusso utente e selezionare **Accedi tramite ROPC**. In questo modo verranno abilitati i criteri ROPC per il tenant. Per ulteriori dettagli, vedere Configurare il flusso delle credenziali della password del [proprietario della risorsa.](/azure/active-directory-b2c/configure-ropc)

`IPublicClientApplication`contiene un metodo:
```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

Questo metodo accetta come parametri:This method takes as parameters:
- Ambiti per *cui* richiedere un token di accesso.
- Un *nome utente*.
- Una *password* SecureString per l'utente.

Ricordarsi di utilizzare l'autorità che contiene i criteri ROPC.

### <a name="limitations-of-the-ropc-flow"></a>Limitazioni del flusso ROPC
 - Il flusso ROPC **funziona solo per gli account locali** (in cui si registra con Azure AD B2C tramite un messaggio di posta elettronica o un nome utente). Questo flusso non funziona se la federazione in uno dei provider di identità supportati da Azure AD B2C (Facebook, Google e così via).

## <a name="google-auth-and-embedded-webview"></a>Google Auth e visualizzazione Web integrata

Se sei uno sviluppatore B2C di Azure AD che usa Google come provider di identità, ti ricomandiamo di utilizzare il browser di sistema, poiché Google non consente [l'autenticazione dalle visualizzazioni Web incorporate.](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html) Attualmente, `login.microsoftonline.com` è un'autorità attendibile con Google. L'utilizzo di questa autorità funzionerà con la visualizzazione Web incorporata. Tuttavia, l'utilizzo `b2clogin.com` non è un'autorità attendibile con Google, pertanto gli utenti non saranno in grado di eseguire l'autenticazione.

Se le cose cambiano, forniremo un aggiornamento a questo [problema.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688)

## <a name="caching-with-azure-ad-b2c-in-msalnet"></a>Memorizzazione nella cache con Azure AD B2C in MSAL.Net 

### <a name="known-issue-with-azure-ad-b2c"></a>Problema noto di Azure AD B2CKnown issue with Azure AD B2C

MSAL.Net supporta una [cache di token](/dotnet/api/microsoft.identity.client.tokencache?view=azure-dotnet). La chiave di memorizzazione nella cache dei token si basa sulle attestazioni restituite dal provider di identità. Attualmente MSAL.Net richiede due attestazioni per creare una chiave di cache di token:Currently MSAL.Net needs two claims to build a token cache key:  
- `tid`che è l'ID tenant di Azure AD e 
- `preferred_username` 

Entrambe queste attestazioni sono mancanti in molti degli scenari B2C di Azure AD. 

L'impatto del cliente è che quando si tenta di visualizzare il campo del nome utente, viene visualizzato "Mancante dalla risposta del token" come valore? In tal caso, ciò è dovuto al fatto che Azure AD B2C non restituisce un valore nel Token per il preferred_username a causa delle limitazioni con gli account social e i provider di identità esterni .NET. Azure AD restituisce un valore per preferred_username perché sa chi è l'utente, ma per Azure AD B2C, perché l'utente può accedere con un account locale, Facebook, Google, GitHub e così via, non esiste un valore coerente per Azure AD B2C da usare per preferred_username. Per sbloccare MSAL dall'implementazione della compatibilità della cache con ADAL, abbiamo deciso di usare "Missing from the token response" da parte nostra quando si gestiscono gli account B2C di Azure AD quando IdToken non restituisce nulla per preferred_username. MSAL deve restituire un valore per preferred_username per mantenere la compatibilità della cache tra le librerie.

### <a name="workarounds"></a>Soluzioni alternative

#### <a name="mitigation-for-the-missing-tenant-id"></a>Mitigazione per l'ID tenant mancanteMitigation for the missing tenant ID

La soluzione suggerita consiste nell'utilizzare la [memorizzazione nella cache](#acquire-a-token-to-apply-a-policy)

In alternativa, è `tid` possibile utilizzare l'attestazione, se si utilizzano i [criteri personalizzati B2C](https://aka.ms/ief), poiché fornisce la possibilità di restituire attestazioni aggiuntive all'applicazione. Per altre informazioni sulla [trasformazione delle attestazioniTo](/azure/active-directory-b2c/claims-transformation-technical-profile) learn more about Claims Transformation

#### <a name="mitigation-for-missing-from-the-token-response"></a>Mitigazione per "Missing from the token response"
Un'opzione consiste nell'utilizzare l'attestazione "nome" come nome utente preferito. Il processo è menzionato in questo [documento B2C](../../active-directory-b2c/user-flow-overview.md) -> "Nella colonna Attestazione di reso scegliere le attestazioni che si desidera vengano restituite nei token di autorizzazione inviati all'applicazione dopo un'esperienza di modifica del profilo corretta. Ad esempio, selezionare Nome visualizzato, Codice postale."

## <a name="next-steps"></a>Passaggi successivi 

Altre informazioni sull'acquisizione di token in modo interattivo con MSAL.NET per le applicazioni B2C di Azure AD sono disponibili nell'esempio seguente.

| Esempio | Piattaforma | Descrizione|
|------ | -------- | -----------|
|[active-directory-b2c-xamarin-native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin iOS, Xamarin Android, UWP | Una semplice app Xamarin Forms che illustra come usare MSAL.NET per autenticare gli utenti tramite Azure AD B2C e accedere a un'API Web con i token risultanti.|
