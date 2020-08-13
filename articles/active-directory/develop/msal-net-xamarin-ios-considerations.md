---
title: Considerazioni su Novell iOS (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Informazioni sulle considerazioni sull'uso di Novell iOS con Microsoft Authentication Library per .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 73303d64d7ebeb94290819226e5e5944f95f6e42
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88165703"
---
# <a name="considerations-for-using-xamarin-ios-with-msalnet"></a>Considerazioni sull'uso di Novell iOS con MSAL.NET
Quando si usa Microsoft Authentication Library per .NET (MSAL.NET) in Novell iOS, è necessario: 

- Eseguire l'override e implementare la `OpenUrl` funzione in `AppDelegate` .
- Abilitare i gruppi di portachiavi.
- Abilitare la condivisione della cache del token.
- Abilitare l'accesso keychain.
- Informazioni sui problemi noti di iOS 12 e sull'autenticazione.

## <a name="implement-openurl"></a>Implementare OpenUrl

Eseguire l'override del `OpenUrl` metodo della `FormsApplicationDelegate` classe derivata e chiamare `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs` . Ecco un esempio:

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

Eseguire anche le attività seguenti: 
* Definire uno schema URL.
* Richiedere le autorizzazioni per l'app per chiamare un'altra app.
* Hanno un modulo specifico per l'URL di reindirizzamento.
* Registrare l'URL di reindirizzamento nella [portale di Azure](https://portal.azure.com).

### <a name="enable-keychain-access"></a>Abilita accesso Keychain

Per abilitare l'accesso keychain, assicurarsi che l'applicazione disponga di un gruppo di accesso a keychain. È possibile impostare il gruppo di accesso a keychain quando si crea l'applicazione tramite l' `WithIosKeychainSecurityGroup()` API.

Per sfruttare i vantaggi della cache e della Single Sign-On (SSO), impostare il gruppo di accesso a keychain sullo stesso valore in tutte le applicazioni.

Questo esempio di installazione usa MSAL 4. x:
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Abilitare anche l'accesso a keychain nel `Entitlements.plist` file. Usare il gruppo di accesso seguente o il gruppo di accesso.

```xml
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
```

Quando si usa l' `WithIosKeychainSecurityGroup()` API, MSAL aggiunge automaticamente il gruppo di sicurezza alla fine dell' *ID team* dell'applicazione ( `AppIdentifierPrefix` ). MSAL aggiunge il gruppo di sicurezza perché, quando si compila l'applicazione in Xcode, eseguirà la stessa operazione. Questo è il motivo per cui i diritti nel `Entitlements.plist` file devono includere `$(AppIdentifierPrefix)` prima del gruppo di accesso a keychain.

Per ulteriori informazioni, vedere la [documentazione relativa ai diritti iOS](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps). 

### <a name="enable-token-cache-sharing-across-ios-applications"></a>Abilitare la condivisione della cache del token tra le applicazioni iOS

A partire da MSAL 2. x, è possibile specificare un gruppo di accesso a Keychain per salvare in modo permanente la cache dei token tra più applicazioni. Questa impostazione consente di condividere la cache dei token tra più applicazioni che hanno lo stesso gruppo di accesso a keychain. È possibile condividere il token in denaro tra le applicazioni [adal.NET](https://aka.ms/adal-net) , le applicazioni MSAL.NET Novell. iOS e le applicazioni iOS native sviluppate in [adal. objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc) o [MSAL. objc](https://github.com/AzureAD/microsoft-authentication-library-for-objc).

Condividendo la cache dei token si consente la Single Sign-On (SSO) tra tutte le applicazioni che usano lo stesso gruppo di accesso a keychain.

Per abilitare questa condivisione della cache, usare il `WithIosKeychainSecurityGroup()` metodo per impostare il gruppo di accesso a keychain sullo stesso valore in tutte le applicazioni che condividono la stessa cache. Il primo esempio di codice in questo articolo illustra come usare il metodo.

In precedenza in questo articolo si è appreso che MSAL aggiunge `$(AppIdentifierPrefix)` ogni volta che si usa l' `WithIosKeychainSecurityGroup()` API. MSAL aggiunge questo elemento perché l'ID Team `AppIdentifierPrefix` garantisce che solo le applicazioni eseguite dallo stesso server di pubblicazione possano condividere l'accesso keychain.

> [!NOTE]
> La `KeychainSecurityGroup` proprietà è deprecata.
> 
> A partire da MSAL 2. x, gli sviluppatori erano costretti a includere il `TeamId` prefisso quando usavano la `KeychainSecurityGroup` Proprietà. Tuttavia, a partire da MSAL 2.7. x, quando si usa la nuova `iOSKeychainSecurityGroup` proprietà, MSAL risolve il `TeamId` prefisso durante il Runtime. Quando si usa questa proprietà, non includere il `TeamId` prefisso nel valore. Il prefisso non è obbligatorio.
>
> Poiché la `KeychainSecurityGroup` proprietà è obsoleta, utilizzare la `iOSKeychainSecurityGroup` Proprietà.

### <a name="use-microsoft-authenticator"></a>USA Microsoft Authenticator

L'applicazione può usare Microsoft Authenticator come broker per abilitare:

- **SSO**: quando si Abilita SSO, gli utenti non devono accedere a ogni applicazione.
- **Identificazione del dispositivo**: usare l'identificazione del dispositivo per l'autenticazione tramite l'accesso al certificato del dispositivo. Questo certificato viene creato nel dispositivo quando viene aggiunto all'area di lavoro. L'applicazione sarà pronta se gli amministratori tenant abilitano l'accesso condizionale correlato ai dispositivi.
- **Verifica dell'identificazione dell'applicazione**: quando un'applicazione chiama il broker, passa l'URL di reindirizzamento. Il Broker verifica l'URL di reindirizzamento.

Per informazioni dettagliate su come abilitare un broker, vedere [usare Microsoft Authenticator o Microsoft Intune portale aziendale nelle applicazioni Novell iOS e Android](msal-net-use-brokers-with-xamarin-apps.md).

## <a name="known-issues-with-ios-12-and-authentication"></a>Problemi noti con iOS 12 e autenticazione
Microsoft ha rilasciato un [avviso di sicurezza](https://github.com/aspnet/AspNetCore/issues/4647) relativo a un'incompatibilità tra iOS 12 e alcuni tipi di autenticazione. L'incompatibilità interrompe gli accessi social, WSFed e OIDC. Gli avvisi di sicurezza aiutano gli sviluppatori a comprendere come rimuovere le restrizioni di sicurezza ASP.NET dalle applicazioni per renderle compatibili con iOS 12.  

Quando si sviluppano applicazioni MSAL.NET in Novell iOS, è possibile che venga visualizzato un ciclo infinito quando si tenta di accedere ai siti Web da iOS 12. Questo comportamento è simile a questo [problema adal](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329). 

È anche possibile che venga visualizzato un break in ASP.NET Core Autenticazione OIDC con iOS 12 Safari. Per altre informazioni, vedere questo [problema di WebKit](https://bugs.webkit.org/show_bug.cgi?id=188165).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sulle proprietà per Novell iOS, vedere il paragrafo [considerazioni specifiche per iOS](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/master/1-Basic#ios-specific-considerations) del file Readme.MD dell'esempio seguente:

Esempio | Piattaforma | Descrizione
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Novell iOS, Android, piattaforma UWP (Universal Windows Platform) (UWP) | Una semplice app Novell Forms che illustra come usare MSAL per autenticare gli account personali Microsoft e Azure AD tramite l'endpoint Azure AD 2,0. L'app mostra anche come usare il token risultante per accedere Microsoft Graph.

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->