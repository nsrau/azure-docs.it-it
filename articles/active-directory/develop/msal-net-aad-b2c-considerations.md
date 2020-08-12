---
title: Azure AD B2C e MSAL.NET
titleSuffix: Microsoft identity platform
description: Considerazioni sull'utilizzo di Azure AD B2C con Microsoft Authentication Library per .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2020
ms.author: jeferrie
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: ea5cc53d909ed090e152af84da49c8e87907f6bf
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88120610"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>Usare MSAL.NET per l'accesso degli utenti con identità di social networking

È possibile usare MSAL.NET per accedere agli utenti con identità di Social Networking usando [Azure Active Directory B2C (Azure ad B2C)](https://aka.ms/aadb2c). Azure AD B2C si basa sul concetto di criteri. In MSAL.NET, se si specifica un criterio, viene convertito in in modo da fornire un'autorità.

- Quando si crea un'istanza dell'applicazione client pubblica, è necessario specificare i criteri come parte dell'autorità.
- Quando si desidera applicare un criterio, chiamare un override di `AcquireTokenInteractive` che accetti il `authority` parametro.

Questo articolo si applica a MSAL.NET 3. x. Per MSAL.NET 2. x, vedere [Azure ad B2C specifiche in MSAL 2. x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x) nel Wiki di MSAL.NET su GitHub.

## <a name="authority-for-an-azure-ad-b2c-tenant-and-policy"></a>Autorità per un tenant e un criterio di Azure AD B2C

Il formato dell'autorità per Azure AD B2C è il seguente:`https://{azureADB2CHostname}/tfp/{tenant}/{policyName}`

- `azureADB2CHostname`: Nome del tenant Azure AD B2C più l'host. Ad esempio, *contosob2c.b2clogin.com*.
- `tenant`: Il nome di dominio o l'ID di directory (tenant) del tenant del Azure AD B2C. Ad esempio, *contosob2c.onmicrosoft.com* o GUID, rispettivamente.
- `policyName`: Nome del flusso utente o criterio personalizzato da applicare. Ad esempio, un criterio di iscrizione/accesso come *b2c_1_susi*.

Per ulteriori informazioni sulle autorità Azure AD B2C, vedere [impostare URL di reindirizzamento su b2clogin.com](../../active-directory-b2c/b2clogin.md).

## <a name="instantiating-the-application"></a>Creazione di un'istanza dell'applicazione

Fornire l'autorità chiamando `WithB2CAuthority()` quando si crea l'oggetto applicazione:

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

Per acquisire un token per un'API protetta da Azure AD B2C in un'applicazione client pubblica, è necessario usare le sostituzioni con un'autorità:

```csharp
IEnumerable<IAccount> accounts = await application.GetAccountsAsync();
AuthenticationResult ar = await application.AcquireTokenInteractive(scopes)
                                           .WithAccount(GetAccountByPolicy(accounts, policy))
                                           .WithParentActivityOrWindow(ParentActivityOrWindow)
                                           .ExecuteAsync();
```

Nel frammento di codice precedente:

- `policy`stringa che contiene il nome del flusso utente Azure AD B2C o dei criteri personalizzati (ad esempio, `PolicySignUpSignIn` ).
- `ParentActivityOrWindow`è necessario per Android (attività) ed è facoltativo per altre piattaforme che supportano un'interfaccia utente padre come Windows in Microsoft Windows e UIViewController in iOS. Per ulteriori informazioni sulla finestra di dialogo dell'interfaccia utente, vedere [WithParentActivityOrWindow](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively#withparentactivityorwindow) sul wiki di MSAL.
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

L'applicazione di un flusso utente o di un criterio personalizzato (ad esempio, per consentire all'utente di modificare il profilo o reimpostare la password) viene eseguita chiamando `AcquireTokenInteractive` . Per questi due criteri, non viene usato il risultato del token/autenticazione restituito.

## <a name="profile-edit-policies"></a>Criteri di modifica del profilo

Per consentire agli utenti di accedere con un'identità di social networking e quindi di modificare il profilo, applicare i criteri del profilo di modifica Azure AD B2C.

A tale scopo, chiamare `AcquireTokenInteractive` con l'autorità per quel criterio. Poiché l'utente ha già effettuato l'accesso e ha una sessione di cookie attiva, usare `Prompt.NoPrompt` per evitare la visualizzazione della finestra di dialogo di selezione dell'account.

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
    }
}
```

## <a name="resource-owner-password-credentials-ropc"></a>Credenziali password del proprietario della risorsa (ROPC)

Per altre informazioni sul flusso ROPC, vedere l' [accesso con la concessione delle credenziali password del proprietario della risorsa](v2-oauth-ropc.md).

Il flusso ROPC **non è consigliato** perché la richiesta della password dell'utente nell'applicazione non è sicura. Per ulteriori informazioni su questo problema, vedere [Qual è la soluzione per il problema crescente delle password?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/).

Con il nome utente e la password in un flusso ROPC si sacrificano diversi elementi:

- Principi fondamentali dell'identità moderna: è possibile eseguire il Fishing o la riproduzione della password perché il segreto condiviso può essere intercettato. Per definizione, ROPC è incompatibile con i flussi senza password.
- Gli utenti che devono eseguire l'autenticazione a più fattori non saranno in grado di eseguire l'accesso, perché non è presente alcuna interazione.
- Gli utenti non saranno in grado di utilizzare Single Sign-On (SSO).

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>Configurare il flusso ROPC in Azure AD B2C

Nel tenant di Azure AD B2C creare un nuovo flusso utente e selezionare **Accedi con ROPC** per abilitare ROPC per il flusso utente. Per altre informazioni, vedere [configurare il flusso di credenziali password del proprietario della risorsa](../../active-directory-b2c/configure-ropc.md).

`IPublicClientApplication`contiene il `AcquireTokenByUsernamePassword` Metodo:

```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

Questo `AcquireTokenByUsernamePassword` metodo accetta i parametri seguenti:

- *Ambiti* per i quali ottenere un token di accesso.
- *Nome utente*.
- *Password* di SecureString per l'utente.

### <a name="limitations-of-the-ropc-flow"></a>Limitazioni del flusso ROPC

Il flusso ROPC **funziona solo per gli account locali**, in cui gli utenti hanno eseguito la registrazione con Azure ad B2C usando un indirizzo di posta elettronica o un nome utente. Questo flusso non funziona quando si esegue la Federazione a un provider di identità esterno supportato da Azure AD B2C (Facebook, Google e così via).

## <a name="google-auth-and-embedded-webview"></a>Google auth e WebView incorporato

Se si usa Google come provider di identità, è consigliabile usare il browser di sistema perché Google non consente [l'autenticazione da visualizzazioni Web incorporate](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html). Attualmente, `login.microsoftonline.com` è un'autorità attendibile con Google e funzionerà con WebView incorporato. Tuttavia, `b2clogin.com` non è un'autorità attendibile con Google, quindi gli utenti non saranno in grado di eseguire l'autenticazione.

Se gli elementi cambiano, verrà fornito un aggiornamento a questo [problema](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688) .

## <a name="token-caching-in-msalnet"></a>Memorizzazione di token nella cache in MSAL.NET

### <a name="known-issue-with-azure-ad-b2c"></a>Problema noto con Azure AD B2C

MSAL.NET supporta una [cache di token](/dotnet/api/microsoft.identity.client.tokencache?view=azure-dotnet). La chiave di caching del token è basata sulle attestazioni restituite dal provider di identità (IdP).

Attualmente, MSAL.NET richiede due attestazioni per compilare una chiave di cache del token:

- `tid`(ID tenant Azure AD)
- `preferred_username`

Entrambe le attestazioni potrebbero mancare negli scenari Azure AD B2C perché non tutti i provider di identità basati su social network (Facebook, Google e altri) li restituiscono nei token che restituiscono Azure AD B2C.

Un sintomo di tale scenario è che MSAL.NET restituisce `Missing from the token response` quando si accede al `preferred_username` valore dell'attestazione nei token emessi da Azure ad B2C. MSAL usa il `Missing from the token response` valore per per `preferred_username` mantenere la compatibilità incrociata della cache tra le librerie.

### <a name="workarounds"></a>Soluzioni alternative

#### <a name="mitigation-for-missing-tenant-id"></a>Attenuazione per l'ID tenant mancante

La soluzione alternativa suggerita consiste nell'usare la [memorizzazione nella cache in base ai criteri](#acquire-a-token-to-apply-a-policy) descritti in precedenza.

In alternativa, è possibile usare l' `tid` attestazione se si usano [criteri personalizzati](../../active-directory-b2c/custom-policy-get-started.md) in Azure ad B2C. I criteri personalizzati possono restituire attestazioni aggiuntive all'applicazione tramite la [trasformazione delle attestazioni](../../active-directory-b2c/claims-transformation-technical-profile.md).

#### <a name="mitigation-for-missing-from-the-token-response"></a>Mitigazione per "missing from the token Response"

Un'opzione consiste nell'usare l' `name` attestazione anziché `preferred_username` . Per includere l' `name` attestazione nei token ID emessi da Azure ad B2C, selezionare **nome visualizzato** quando si configura il flusso utente.

Per altre informazioni su come specificare quali attestazioni vengono restituite dai flussi utente, vedere [esercitazione: creare flussi utente in Azure ad B2C](../../active-directory-b2c/tutorial-create-user-flows.md).

## <a name="next-steps"></a>Passaggi successivi

Nell'esempio seguente vengono fornite ulteriori informazioni sull'acquisizione di token in modo interattivo con MSAL.NET per Azure AD B2C applicazioni.

| Esempio | Piattaforma | Descrizione|
|------ | -------- | -----------|
|[Active-Directory-B2C-Novell-Native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Novell iOS, Novell Android, UWP | Un'app Novell Forms che usa MSAL.NET per autenticare gli utenti tramite Azure AD B2C e quindi accedere a un'API Web con i token restituiti.|