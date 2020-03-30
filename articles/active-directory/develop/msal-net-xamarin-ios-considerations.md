---
title: Xamarin considerazioni su iOS (MSAL.NET) Azure
titleSuffix: Microsoft identity platform
description: Informazioni sulle considerazioni per l'utilizzo di Xamarin iOS con Microsoft Authentication Library per .NET (MSAL.NET).
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76e614b605cd07cd5dc454824dd204447f806907
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262711"
---
# <a name="considerations-for-using-xamarin-ios-with-msalnet"></a>Considerazioni sull'utilizzo di Xamarin iOS con MSAL.NET
Quando si utilizza Microsoft Authentication Library per .NET (MSAL.NET) in Xamarin iOS, è necessario:When you use Microsoft Authentication Library for .NET (MSAL.NET) on Xamarin iOS, you should: 

- Eseguire l'override e implementare la `OpenUrl` funzione in `AppDelegate`.
- Abilitare i gruppi di portachiavi.
- Abilitare la condivisione della cache dei token.
- Abilitare l'accesso al portachiavi.
- Comprendere i problemi noti relativi a iOS 12 e all'autenticazione.

## <a name="implement-openurl"></a>Implementare OpenUrlImplement OpenUrl

Eseguire `OpenUrl` l'override `FormsApplicationDelegate` del metodo `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs`della classe derivata e chiamare . Ad esempio:

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

Eseguire inoltre le attività seguenti: 
* Definire uno schema URL.
* Richiedere le autorizzazioni affinché l'app chiami un'altra app.
* Disporre di un modulo specifico per l'URL di reindirizzamento.
* Registrare l'URL di reindirizzamento nel portale di [Azure.](https://portal.azure.com)

### <a name="enable-keychain-access"></a>Abilitare l'accesso al portachiavi

Per abilitare l'accesso al portachiavi, assicurarsi che l'applicazione disponga di un gruppo di accesso al portachiavi. È possibile impostare il gruppo di accesso portachiavi quando si crea l'applicazione utilizzando l'API. `WithIosKeychainSecurityGroup()`

Per trarre vantaggio dalla cache e dal Single Sign-On (SSO), impostare il gruppo di accesso al portachiavi sullo stesso valore in tutte le applicazioni.

Questo esempio del programma di installazione utilizza MSAL 4.x:
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Abilitare anche l'accesso `Entitlements.plist` portachiavi nel file. Utilizzare il gruppo di accesso seguente o il proprio gruppo di accesso.

```xml
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
```

Quando si `WithIosKeychainSecurityGroup()` utilizza l'API, MSAL aggiunge automaticamente il gruppo di sicurezza`AppIdentifierPrefix`alla fine dell'ID *team* dell'applicazione ( ). MSAL aggiunge il gruppo di sicurezza perché quando si compila l'applicazione in Xcode, farà lo stesso. Ecco perché i permessi `Entitlements.plist` nel file `$(AppIdentifierPrefix)` devono essere inclusi prima del gruppo di accesso al portachiavi.

Per ulteriori informazioni, vedere la [documentazione sui permessi iOS](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps). 

### <a name="enable-token-cache-sharing-across-ios-applications"></a>Abilitare la condivisione della cache dei token tra le applicazioni iOSEnable token cache sharing across iOS applications

A partire da MSAL 2.x, è possibile specificare un gruppo di accesso portachiavi per rendere persistente la cache dei token in più applicazioni. Questa impostazione consente di condividere la cache dei token tra diverse applicazioni che dispongono dello stesso gruppo di accesso al portachiavi. È possibile condividere il token cash tra [applicazioni ADAL.NET,](https://aka.ms/adal-net) applicazioni MSAL.NET Xamarin.iOS e applicazioni iOS native sviluppate in [ADAL.objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc) o [MSAL.objc](https://github.com/AzureAD/microsoft-authentication-library-for-objc).

Condividendo la cache dei token, si consente l'accesso Single Sign-On (SSO) tra tutte le applicazioni che utilizzano lo stesso gruppo di accesso al portachiavi.

Per abilitare questa condivisione della cache, utilizzare il `WithIosKeychainSecurityGroup()` metodo per impostare il gruppo di accesso portachiavi sullo stesso valore in tutte le applicazioni che condividono la stessa cache. Nel primo esempio di codice in questo articolo viene illustrato come utilizzare il metodo .

In precedenza in questo articolo, `$(AppIdentifierPrefix)` si è `WithIosKeychainSecurityGroup()` appreso che MSAL aggiunge ogni volta che si utilizza l'API. MSAL aggiunge questo elemento `AppIdentifierPrefix` perché l'ID del team garantisce che solo le applicazioni effettuate dallo stesso editore possano condividere l'accesso al portachiavi.

> [!NOTE]
> La `KeychainSecurityGroup` proprietà è deprecata.
> 
> A partire da MSAL 2.x, `TeamId` gli sviluppatori sono `KeychainSecurityGroup` stati costretti a includere il prefisso quando hanno utilizzato la proprietà. Ma a partire da MSAL 2.7.x, quando si utilizza la nuova `iOSKeychainSecurityGroup` proprietà, MSAL risolve il prefisso durante il `TeamId` runtime. Quando si utilizza questa proprietà, `TeamId` non includere il prefisso nel valore. Il prefisso non è obbligatorio.
>
> Poiché `KeychainSecurityGroup` la proprietà è `iOSKeychainSecurityGroup` obsoleta, utilizzare la proprietà .

### <a name="use-microsoft-authenticator"></a>Usare Microsoft Authenticator

L'applicazione può usare Microsoft Authenticator come broker per abilitare:Your application can use Microsoft Authenticator as a broker to enable:

- **SSO**: Quando si abilita SSO, gli utenti non devono accedere a ogni applicazione.
- **Identificazione del dispositivo**: Utilizzare l'identificazione del dispositivo per l'autenticazione accedendo al certificato del dispositivo. Questo certificato viene creato nel dispositivo quando viene aggiunto all'area di lavoro. L'applicazione sarà pronta se gli amministratori tenant abilitano l'accesso condizionale correlato ai dispositivi.
- **Verifica dell'identificazione**dell'applicazione : Quando un'applicazione chiama il broker, passa il suo URL di reindirizzamento. Il broker verifica l'URL di reindirizzamento.

Per informazioni dettagliate su come abilitare un broker, vedere [Usare Microsoft Authenticator o Portale aziendale di Microsoft Intune nelle applicazioni Xamarin iOS e Android.](msal-net-use-brokers-with-xamarin-apps.md)

## <a name="known-issues-with-ios-12-and-authentication"></a>Problemi noti relativi a iOS 12 e all'autenticazione
Microsoft ha rilasciato un [advisory](https://github.com/aspnet/AspNetCore/issues/4647) sulla sicurezza di un'incompatibilità tra iOS 12 e alcuni tipi di autenticazione. L'incompatibilità interrompe gli invii social, WSFed e OIDC. L'advisory sulla sicurezza consente agli sviluppatori di comprendere come rimuovere le restrizioni di sicurezza ASP.NET dalle applicazioni per renderle compatibili con iOS 12.  

Quando sviluppi applicazioni MSAL.NET su Xamarin iOS, potresti vedere un ciclo infinito quando tenti di accedere ai siti web da iOS 12. Questo comportamento è simile a questo [problema ADAL](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329). 

Potresti anche notare un'interruzione nellASP.NETautenticazione OIDC di base con iOS 12 Safari. Per ulteriori informazioni, vedere questo [problema di WebKit](https://bugs.webkit.org/show_bug.cgi?id=188165).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sulle proprietà per Xamarin iOS, vedere il paragrafo considerazioni specifiche di iOS del file di README.md dell'esempio seguente:For information about properties for Xamarin iOS, see the [iOS-specific considerations](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/master/1-Basic#ios-specific-considerations) paragraph of the following sample's README.md file:

Esempio | Piattaforma | Descrizione
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, piattaforma Windows universale (UWP) | Una semplice app Xamarin Forms che mostra come usare MSAL per autenticare gli account personali Microsoft e Azure AD tramite l'endpoint di Azure AD 2.0. L'app mostra anche come usare il token risultante per accedere a Microsoft Graph.

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->