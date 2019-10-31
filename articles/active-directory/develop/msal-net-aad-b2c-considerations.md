---
title: Azure AD B2C (Microsoft Authentication Library per .NET)
titleSuffix: Microsoft identity platform
description: Per informazioni su considerazioni specifiche, vedere l'articolo relativo all'uso di Azure AD B2C con Microsoft Authentication Library per .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/29/2019
ms.author: jeferrie
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0996c5635223800a981497256654b7e418bf4163
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175611"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>Usare MSAL.NET per l'accesso degli utenti con identità di social networking

È possibile usare MSAL.NET per accedere agli utenti con identità di Social Networking usando [Azure Active Directory B2C (Azure ad B2C)](https://aka.ms/aadb2c). Azure AD B2C si basa sul concetto di criteri. In MSAL.NET, se si specifica un criterio, viene convertito in in modo da fornire un'autorità.

- Quando si crea un'istanza dell'applicazione client pubblica, è necessario specificare i criteri in autorità.
- Quando si desidera applicare un criterio, è necessario chiamare un override di `AcquireTokenInteractive` contenente un parametro di `authority`.

Questa pagina è per MSAL 3. x. Se si è interessati a MSAL 2. x, vedere [Azure ad B2C specifiche in MSAL 2. x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x).

## <a name="authority-for-a-azure-ad-b2c-tenant-and-policy"></a>Autorità per un tenant e un criterio di Azure AD B2C

L'autorità da usare è `https://{azureADB2CHostname}/tfp/{tenant}/{policyName}` in cui:

- `azureADB2CHostname` è il nome del tenant Azure AD B2C più l'host, ad esempio `{your-tenant-name}.b2clogin.com`
- `tenant` è il nome completo del tenant di Azure AD B2C, ad esempio `{your-tenant-name}.onmicrosoft.com`, oppure il GUID per il tenant. 
- `policyName` il nome del flusso utente o del criterio da applicare (ad esempio "b2c_1_susi" per l'iscrizione/accesso).

Per ulteriori informazioni sulle autorità Azure AD B2C, vedere questa [documentazione](/azure/active-directory-b2c/b2clogin).

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

## <a name="acquire-a-token-to-apply-a-policy"></a>Acquisire un token per applicare un criterio

Per acquisire un token per un'API protetta Azure AD B2C in un'applicazione client pubblica, è necessario usare le sostituzioni con un'autorità:

```csharp
IEnumerable<IAccount> accounts = await application.GetAccountsAsync();
AuthenticationResult ar = await application .AcquireTokenInteractive(scopes)
                                            .WithAccount(GetAccountByPolicy(accounts, policy))
                                            .WithParentActivityOrWindow(ParentActivityOrWindow)
                                            .ExecuteAsync();
```

con:

- `policy` essere una delle stringhe precedenti, ad esempio `PolicySignUpSignIn`.
- `ParentActivityOrWindow` è necessario per Android (attività) e facoltativa per altre piattaforme che supportano l'interfaccia utente padre, ad esempio Windows in Windows e UIViewController in iOS. Per ulteriori informazioni, vedere [la finestra di dialogo dell'interfaccia utente](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively#withparentactivityorwindow).
- `GetAccountByPolicy(IEnumerable<IAccount>, string)` è un metodo che trova un account per un determinato criterio. ad esempio:

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

L'applicazione di un criterio o di un flusso utente (ad esempio, per consentire all'utente finale di modificare il profilo o reimpostare la password) viene eseguita chiamando `AcquireTokenInteractive`. Nel caso di questi due criteri, non si usa il risultato del token/autenticazione restituito.

## <a name="special-case-of-editprofile-and-resetpassword-policies"></a>Caso speciale dei criteri EditProfile e ResetPassword

Quando si vuole offrire un'esperienza in cui gli utenti finali possono accedere con un'identità di social networking e quindi modificare il profilo, si vuole applicare il Azure AD B2C criteri del profilo di modifica. Per eseguire questa operazione, chiamare `AcquireTokenInteractive` con l'autorità specifica per tale criterio e un prompt impostato su `Prompt.NoPrompt` per impedire che venga visualizzata la finestra di dialogo di selezione dell'account (poiché l'utente ha già eseguito l'accesso e ha una sessione di cookie attiva).

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
Per informazioni dettagliate sul flusso ROPC, vedere la [documentazione](v2-oauth-ropc.md).

Questo flusso **non è consigliato** perché l'applicazione che richiede un utente per la password non è sicura. Per ulteriori informazioni su questo problema, vedere [questo articolo](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). 

Utilizzando nome utente/password, vengono riportate alcune operazioni:
- principi fondamentali dell'identità moderna: la password viene pescata, rieseguita. Poiché questo è il concetto di segreto di condivisione che può essere intercettato. Questa operazione non è compatibile con le password.
- Gli utenti che devono eseguire l'autenticazione a più fattori non saranno in grado di eseguire l'accesso, perché non è presente alcuna interazione.
- Gli utenti non saranno in grado di eseguire Single Sign-On.

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>Configurare il flusso ROPC in Azure AD B2C
Nel tenant di Azure AD B2C creare un nuovo flusso utente e selezionare **Accedi con ROPC**. In questo modo verrà abilitato il criterio ROPC per il tenant. Per altri dettagli, vedere [configurare il flusso di credenziali password del proprietario della risorsa](/azure/active-directory-b2c/configure-ropc) .

`IPublicClientApplication` contiene un metodo:
```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

Questo metodo accetta come parametri:
- *Ambiti* per i quali richiedere un token di accesso.
- *Nome utente*.
- *Password* di SecureString per l'utente.

Ricordarsi di usare l'autorità che contiene i criteri ROPC.

### <a name="limitations-of-the-ropc-flow"></a>Limitazioni del flusso ROPC
 - Il flusso ROPC **funziona solo per gli account locali** (in cui si esegue la registrazione con Azure ad B2C usando un indirizzo di posta elettronica o un nome utente). Questo flusso non funziona se viene eseguita la Federazione a uno dei provider di identità supportati da Azure AD B2C (Facebook, Google e così via).

## <a name="google-auth-and-embedded-webview"></a>Google auth e WebView incorporato

Gli sviluppatori Azure AD B2C che usano Google come provider di identità si rimandano a usare il browser di sistema, perché Google non consente [l'autenticazione da visualizzazioni Web incorporate](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html). Attualmente, `login.microsoftonline.com` è un'autorità attendibile con Google. L'uso di questa autorità funzionerà con WebView incorporato. Tuttavia, l'uso di `b2clogin.com` non è un'autorità attendibile con Google, quindi gli utenti non saranno in grado di eseguire l'autenticazione.

Se gli elementi cambiano, verrà fornito un aggiornamento a questo [problema](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688) .

## <a name="caching-with-azure-ad-b2c-in-msalnet"></a>Caching con Azure AD B2C in MSAL.Net 

### <a name="known-issue-with-azure-ad-b2c"></a>Problema noto con Azure AD B2C

MSAL.Net supporta una [cache di token](/dotnet/api/microsoft.identity.client.tokencache?view=azure-dotnet). La chiave di caching del token è basata sulle attestazioni restituite dal provider di identità. Attualmente MSAL.Net richiede due attestazioni per compilare una chiave di cache del token:  
- `tid` che rappresenta l'ID tenant Azure AD e 
- `preferred_username` 

Entrambe le attestazioni non sono presenti in molti scenari Azure AD B2C. 

L'effetto del cliente è che, quando si tenta di visualizzare il campo username, si sta ricevendo "missing from the token Response" come valore? In caso affermativo, questo è dovuto al fatto che Azure AD B2C non restituisce un valore in IdToken per il preferred_username a causa di limitazioni con gli account di social networking e i provider di identità esterni (IDP). Azure AD restituisce un valore per preferred_username perché conosce l'utente, ma per Azure AD B2C, poiché l'utente può accedere con un account locale, Facebook, Google, GitHub e così via, non esiste un valore coerente per Azure AD B2C da usare per preferred_username. Per sbloccare MSAL dalla distribuzione della compatibilità della cache con ADAL, si è deciso di usare "missing from the token Response" alla fine quando si gestiscono gli account di Azure AD B2C quando IdToken non restituisce nulla per preferred_username. MSAL deve restituire un valore per preferred_username per mantenere la compatibilità della cache tra le librerie.

### <a name="workarounds"></a>Soluzioni alternative

#### <a name="mitigation-for-the-missing-tenant-id"></a>Attenuazione per l'ID tenant mancante

La soluzione alternativa consigliata consiste nell'usare la [memorizzazione nella cache per criterio](#acquire-a-token-to-apply-a-policy)

In alternativa, è possibile usare l'attestazione `tid`, se si usano i [criteri personalizzati B2C](https://aka.ms/ief), perché fornisce la possibilità di restituire attestazioni aggiuntive all'applicazione. Per ulteriori informazioni sulla [trasformazione delle attestazioni](/azure/active-directory-b2c/claims-transformation-technical-profile)

#### <a name="mitigation-for-missing-from-the-token-response"></a>Mitigazione per "missing from the token Response"
Un'opzione consiste nell'usare l'attestazione "Name" come nome utente preferito. Il processo è indicato in questa > [doc B2C](../../active-directory-b2c/active-directory-b2c-reference-policies.md) "nella colonna Restituisci attestazione scegliere le attestazioni che si desidera vengano restituite nei token di autorizzazione inviati all'applicazione dopo un'esperienza di modifica del profilo riuscita. Ad esempio, selezionare nome visualizzato, Cap. "

## <a name="next-steps"></a>Passaggi successivi 

Nell'esempio seguente vengono fornite ulteriori informazioni sull'acquisizione di token in modo interattivo con MSAL.NET per Azure AD B2C applicazioni.

| Esempio | Piattaforma | Description|
|------ | -------- | -----------|
|[Active-Directory-B2C-Novell-Native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Novell iOS, Novell Android, UWP | Una semplice app Novell Forms che illustra come usare MSAL.NET per autenticare gli utenti tramite Azure AD B2C e accedere a un'API Web con i token risultanti.|
