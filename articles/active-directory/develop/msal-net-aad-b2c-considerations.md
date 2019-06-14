---
title: Azure Active Directory B2C (Microsoft Authentication Library per .NET) | Azure
description: Informazioni sulle considerazioni specifiche quando si usa Azure AD B2C con Microsoft Authentication Library per .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8240a487bdb01cdbe9017ddc7cb95ce4fc0e1503
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052339"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>Usare MSAL.NET per consentire agli utenti con identità di social networking

È possibile usare MSAL.NET per consentire agli utenti con identità di social networking usando [Azure Active Directory B2C (Azure AD B2C)](https://aka.ms/aadb2c). Azure AD B2C si basa sulla nozione di criteri. In MSAL.NET, specificare un criterio si traduce in fornendo un'autorità.

- Quando si crea un'istanza dell'applicazione client pubblica, è necessario specificare il criterio dell'autorità.
- Quando si desidera applicare un criterio, è necessario chiamare un override di `AcquireTokenInteractive` contenente un `authority` parametro.

Questa pagina è per MSAL 3.x. Se è interessati a MSAL 2.x, vedi [specifiche di Azure AD B2C in MSAL 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x).

## <a name="authority-for-a-azure-ad-b2c-tenant-and-policy"></a>Autorità per un tenant di Azure AD B2C e i criteri

L'autorità da usare è `https://login.microsoftonline.com/tfp/{tenant}/{policyName}` dove:

- `tenant` è il nome del tenant di Azure AD B2C, 
- `policyName` il nome del criterio da applicare (ad esempio "b2c_1_susi" per sign-in/iscrizione).

Le indicazioni correnti di Azure AD B2C consiste nell'usare `b2clogin.com` come autorità. Ad esempio: `$"https://{your-tenant-name}.b2clogin.com/tfp/{your-tenant-ID}/{policyname}"`. Per altre informazioni, vedere questo [documentazione](/azure/active-directory-b2c/b2clogin).

## <a name="instantiating-the-application"></a>Creazione di un'istanza dell'applicazione

Quando si compila l'applicazione, è necessario fornire l'autorità.

```csharp
// Azure AD B2C Coordinates
public static string Tenant = "fabrikamb2c.onmicrosoft.com";
public static string ClientID = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
public static string PolicySignUpSignIn = "b2c_1_susi";
public static string PolicyEditProfile = "b2c_1_edit_profile";
public static string PolicyResetPassword = "b2c_1_reset";

public static string AuthorityBase = $"https://fabrikamb2c.b2clogin.com/tfp/{Tenant}/";
public static string Authority = $"{AuthorityBase}{PolicySignUpSignIn}";
public static string AuthorityEditProfile = $"{AuthorityBase}{PolicyEditProfile}";
public static string AuthorityPasswordReset = $"{AuthorityBase}{PolicyResetPassword}";

application = PublicClientApplicationBuilder.Create(ClientID)
               .WithB2CAuthority(Authority)
               .Build();
```

## <a name="acquire-a-token-to-apply-a-policy"></a>Acquisire un token per applicare un criterio

Acquisire un token per un B2C di Azure AD API protette in un'applicazione client pubblica richiede di usare gli override con un'autorità di:

```csharp
IEnumerable<IAccount> accounts = await application.GetAccountsAsync();
AuthenticationResult ar = await application .AcquireToken(scopes, parentWindow)
                                            .WithAccount(GetAccountByPolicy(accounts, policy))
                                            .ExecuteAsync();
```

con:

- `policy` in una delle stringhe precedenti (ad esempio `PolicySignUpSignIn`).
- `GetAccountByPolicy(IEnumerable<IAccount>, string)` è un metodo che consente di trovare un account per i criteri specificati. Ad esempio:

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

Applicazione di un criterio (ad esempio consentendo all'utente finale di modificare il proprio profilo o reimpostare la password) viene attualmente eseguito chiamando `AcquireTokenInteractive`. Nel caso di questi due criteri non si usa il token restituito / autenticazione risultato.

## <a name="special-case-of-editprofile-and-resetpassword-policies"></a>Caso speciale di criteri EditProfile e ResetPassword

Quando si desidera offrire un'esperienza in cui gli utenti finali accedono usando un'identità di social networking e quindi modificare il proprio profilo è necessario applicare i criteri di Azure AD B2C EditProfile. Consente di eseguire questa operazione, consiste nel chiamare `AcquireTokenInteractive` con l'autorità specifica per tale criterio e una richiesta impostata su `Prompt.NoPrompt` per evitare la finestra di dialogo Selezione account da visualizzare (come l'utente è già connesso)

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
## <a name="resource-owner-password-credentials-ropc-with-azure-ad-b2c"></a>Credenziali password proprietario di risorsa (ROPC) con Azure AD B2C
Per altre informazioni sul flusso ROPC, consultare questo [documentazione](v2-oauth-ropc.md).

Questo flusso verrà **sconsigliato** perché l'applicazione che richiede un utente la password non è sicura. Per altre informazioni su questo problema, vedere [questo articolo](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). 

Usando nome utente/password, si è offrendo remota una serie di operazioni:
- Core ai tenant dell'identità moderna: password Ottiene operava riprodotti. Poiché abbiamo questo concetto di un segreto di condivisione che può essere intercettato. Questa impostazione è incompatibile con senza password.
- Utenti che necessitano di eseguire l'autenticazione a più fattori non saranno in grado di accedere (perché non è disponibile alcuna interazione).
- Utenti non saranno in grado di eseguire single sign-on.

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>Configurare il flusso ROPC in Azure AD B2C
Nel tenant di Azure AD B2C, creare un nuovo flusso utente e selezionare **Accedi di mediante ROPC**. In questo modo il criterio ROPC per il tenant. Visualizzare [Configura le credenziali password proprietario risorsa flusso](/azure/active-directory-b2c/configure-ropc) per altri dettagli.

`IPublicClientApplication` contiene un metodo:
```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

Questo metodo accetta come parametri:
- Il *ambiti* per richiedere un token di accesso.
- Oggetto *username*.
- Una classe SecureString *password* per l'utente.

Ricordarsi di usare l'autorità che contiene i criteri ROPC.

### <a name="limitations-of-the-ropc-flow"></a>Limitazioni del flusso di ROPC
 - Il flusso ROPC **funziona solo per gli account locali** (in cui si registra con Azure AD B2C usando un indirizzo di posta elettronica o nome utente). Questo flusso non funziona se la federazione per uno qualsiasi dei provider di identità supportati da Azure AD B2C (Facebook, Google, ecc.).
 - Attualmente è disponibile **alcun token ID non restituito da Azure AD B2C** quando si implementa il flusso ROPC da MSAL. Ciò significa che non è possibile creare un oggetto Account, pertanto nella cache, vi sarà alcun Account e nessun utente. Il flusso AcquireTokenSilent non funzionerà in questo scenario. Tuttavia, ROPC non mostra un'interfaccia utente, pertanto non vi sarà alcun impatto sull'esperienza utente.

## <a name="google-auth-and-embedded-webview"></a>Autenticazione di Google e Webview incorporato

Se sei uno sviluppatore di Azure AD B2C Usa Google come provider di identità è prevista è usare il browser del sistema, come Google nepovoluje hodnotu [l'autenticazione da visualizzazioni Web incorporate](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html). Attualmente, `login.microsoftonline.com` è un'autorità attendibile con Google. Usando questa autorità funzionerà con webview incorporato. Tuttavia l'uso `b2clogin.com` non è un'autorità attendibile con Google, in modo che gli utenti non saranno in grado di eseguire l'autenticazione.

Microsoft fornirà un aggiornamento del wiki e ciò [problema](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688) se le cose cambiano.

## <a name="caching-with-azure-ad-b2c-in-msalnet"></a>La memorizzazione nella cache con Azure AD B2C in MSAL.Net 

### <a name="known-issue-with-azure-ad-b2c"></a>Problema noto con Azure AD B2C

MSAL.Net supporta un [cache dei token](/dotnet/api/microsoft.identity.client.tokencache?view=azure-dotnet). Il token di chiave di memorizzazione nella cache si basa sulle attestazioni restituite dal Provider di identità. Attualmente MSAL.Net necessita di due attestazioni per creare una chiave di cache di token:  
- `tid` l'ID Tenant di Azure AD, e 
- `preferred_username` 

Entrambe queste attestazioni sono presenti in molti degli scenari di Azure AD B2C. 

L'impatto sul cliente è che quando è stato effettuato un tentativo di visualizzare il campo nome utente, si ottiene "Mancante dalla risposta del token" come valore? In questo caso, infatti Azure AD B2C non restituisce un valore nel IdToken per il preferred_username a causa delle limitazioni con l'account di social networking e provider di identità esterni (IDP). Azure AD restituisce un valore per preferred_username poiché l'utente sa, ma per Azure AD B2C, perché l'utente può accedere con un account locale, Facebook, Google, GitHub, non esiste e così via non è un valore coerente per Azure AD B2C da usare per preferred_username. Per sbloccare MSAL dall'implementazione di compatibilità della cache con ADAL, abbiamo deciso di utilizzare "Mancante dalla risposta del token" sul lato quando si lavora con gli account Azure AD B2C quando il IdToken restituisce nothing per preferred_username. MSAL deve restituire un valore per preferred_username mantenere la compatibilità della cache in più raccolte.

### <a name="workarounds"></a>Soluzioni alternative

#### <a name="mitigation-for-the-missing-tenant-id"></a>Mitigazione dei rischi per l'ID tenant mancante

La soluzione alternativa suggerita consiste nell'usare il [dai criteri di memorizzazione nella cache](#acquire-a-token-to-apply-a-policy)

In alternativa, è possibile usare la `tid` attestazione, se si usa la [i criteri personalizzati B2C](https://aka.ms/ief), in quanto offre la possibilità di restituire attestazioni aggiuntive all'applicazione. Per altre informazioni su [trasformazione delle attestazioni](/azure/active-directory-b2c/claims-transformation-technical-profile)

#### <a name="mitigation-for-missing-from-the-token-response"></a>Mitigazione dei rischi per "Mancante dalla risposta del token"
Una possibilità consiste nell'usare l'attestazione "name" come nome utente preferito. Il processo è descritto in questo [B2C doc](../../active-directory-b2c/active-directory-b2c-reference-policies.md) -> "della colonna restituito attestazioni, scegliere le attestazioni che devono essere restituite nei token di autorizzazione inviati all'applicazione dopo la corretta esperienza di modifica del profilo. Ad esempio, selezionare nome visualizzato, codice postale".

## <a name="next-steps"></a>Passaggi successivi 

Nell'esempio seguente vengono forniti ulteriori dettagli sull'acquisizione dei token in modo interattivo con MSAL.NET per le applicazioni di Azure AD B2C.

| Esempio | Piattaforma | Descrizione|
|------ | -------- | -----------|
|[active-directory-b2c-xamarin-native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin iOS, Xamarin. Android, UWP | Una semplice app Xamarin. Forms che illustra come usare MSAL.NET per autenticare gli utenti tramite Azure AD B2C e accedere a un'API Web con i token risultanti.|
