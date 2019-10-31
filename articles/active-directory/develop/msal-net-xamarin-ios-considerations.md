---
title: Considerazioni su Novell iOS (Microsoft Authentication Library per .NET)
titleSuffix: Microsoft identity platform
description: Informazioni su considerazioni specifiche quando si usa Novell iOS con Microsoft Authentication Library per .NET (MSAL.NET).
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
ms.date: 07/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cc4eb0084e0606251d3fbd80d08723701c6a260
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175627"
---
# <a name="xamarin-ios-specific-considerations-with-msalnet"></a>Considerazioni specifiche di Novell per iOS con MSAL.NET
In Novell iOS sono disponibili diverse considerazioni che è necessario tenere in considerazione quando si usa MSAL.NET

- [Problemi noti con iOS 12 e autenticazione](#known-issues-with-ios-12-and-authentication)
- [Eseguire l'override e implementare la funzione `OpenUrl` nell'`AppDelegate`](#implement-openurl)
- [Abilita gruppi Keychain](#enable-keychain-access)
- [Abilitare la condivisione della cache del token](#enable-token-cache-sharing-across-ios-applications)
- [Abilita accesso Keychain](#enable-keychain-access)

## <a name="known-issues-with-ios-12-and-authentication"></a>Problemi noti con iOS 12 e autenticazione
Microsoft ha rilasciato un [avviso di sicurezza](https://github.com/aspnet/AspNetCore/issues/4647) per fornire informazioni su un'incompatibilità tra iOS12 e alcuni tipi di autenticazione. L'incompatibilità interrompe gli accessi social, WSFed e OIDC. Questa consulenza fornisce anche indicazioni su cosa possono fare gli sviluppatori per rimuovere le attuali restrizioni di sicurezza aggiunte da ASP.NET alle applicazioni per essere compatibili con iOS12.  

Quando si sviluppano applicazioni MSAL.NET in Novell iOS, è possibile che venga visualizzato un ciclo infinito quando si tenta di accedere ai siti Web da iOS 12 (analogamente a questo [problema di adal](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329). 

È anche possibile che venga visualizzato un break in ASP.NET Core Autenticazione OIDC con iOS 12 Safari, come descritto in questo [problema di WebKit](https://bugs.webkit.org/show_bug.cgi?id=188165).

## <a name="implement-openurl"></a>Implementare OpenUrl

Prima di tutto è necessario eseguire l'override del metodo `OpenUrl` della classe derivata `FormsApplicationDelegate` e chiamare `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs`.

```CSharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

È anche necessario definire uno schema URL, richiedere le autorizzazioni per l'app per chiamare un'altra app, avere un modulo specifico per l'URL di reindirizzamento e registrare questo URL di reindirizzamento nella [portale di Azure](https://portal.azure.com)

### <a name="enable-keychain-access"></a>Abilita accesso Keychain

Per abilitare l'accesso keychain, l'applicazione deve avere un gruppo di accesso a keychain.
È possibile impostare il gruppo di accesso a keychain usando l'API `WithIosKeychainSecurityGroup()` durante la creazione dell'applicazione come illustrato di seguito:

Per abilitare Single Sign-On, è necessario impostare la proprietà `PublicClientApplication.iOSKeychainSecurityGroup` sullo stesso valore in tutte le applicazioni.

Un esempio di questo uso di MSAL V3. x è:
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.msalrocks")
     .Build();
```

Il file titles. plist deve essere aggiornato in modo analogo al seguente frammento XML:

Questa modifica è *aggiunta* all'abilitazione dell'accesso Keychain nel file di `Entitlements.plist`, usando il gruppo di accesso seguente o il proprio:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.msalrocks</string>
  </array>
</dict>
</plist>
```

Un esempio di questo uso di MSAL V4. x è:

```csharp
PublicClientApplication.iOSKeychainSecurityGroup = "com.microsoft.msalrocks";
```

Quando si usa l'API di `WithIosKeychainSecurityGroup()`, MSAL aggiunge automaticamente il gruppo di sicurezza alla fine dell'"ID team" dell'applicazione (AppIdentifierPrefix), perché quando si compila l'applicazione con Xcode, lo sarà. [Per altri dettagli, vedere la documentazione sui diritti iOS](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps). Per questo motivo è necessario aggiornare i diritti per includere $ (AppIdentifierPrefix) prima del gruppo di accesso a keychain nel file titles. plist.

### <a name="enable-token-cache-sharing-across-ios-applications"></a>Abilitare la condivisione della cache del token tra le applicazioni iOS

Da MSAL 2. x, è possibile specificare un gruppo di accesso a Keychain da usare per la memorizzazione nella cache dei token tra più applicazioni. Questa impostazione consente di condividere la cache dei token tra più applicazioni con lo stesso gruppo di accesso a keychain, inclusi quelli sviluppati con le applicazioni [adal.NET](https://aka.ms/adal-net), MSAL.NET Novell. iOS e le applicazioni iOS native sviluppate con [ ADAL. objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc) o [MSAL. objc](https://github.com/AzureAD/microsoft-authentication-library-for-objc)).

La condivisione della cache dei token consente Single Sign-On tra tutte le applicazioni che usano lo stesso gruppo di accesso a keychain.

Per abilitare questa condivisione della cache, è necessario impostare il metodo use the ' WithIosKeychainSecurityGroup ()' per impostare il gruppo di accesso a keychain sullo stesso valore in tutte le applicazioni che condividono la stessa cache, come illustrato nell'esempio precedente.

In precedenza è stato indicato che MSAL ha aggiunto $ (AppIdentifierPrefix) ogni volta che si usa l'API `WithIosKeychainSecurityGroup()`. Questo perché il AppIdentifierPrefix o "ID team" viene usato per garantire che solo le applicazioni eseguite dallo stesso server di pubblicazione possano condividere l'accesso keychain.

> [!NOTE]
> **La proprietà `KeychainSecurityGroup` è deprecata.**
> 
> In precedenza, da MSAL 2. x, gli sviluppatori dovevano includere il prefisso TeamId quando utilizzavano la proprietà `KeychainSecurityGroup`.
>
>  Da MSAL 2.7. x, quando si usa la nuova proprietà `iOSKeychainSecurityGroup`, MSAL risolverà il prefisso TeamId durante il Runtime. Quando si usa questa proprietà, il valore non deve contenere il prefisso TeamId.
>  Usare la nuova proprietà `iOSKeychainSecurityGroup`, che non richiede di specificare il TeamId, perché la proprietà `KeychainSecurityGroup` precedente è ora obsoleta.

### <a name="use-microsoft-authenticator"></a>USA Microsoft Authenticator

L'applicazione può usare Microsoft Authenticator (Broker) per abilitare:

- Single Sign-on (SSO). Gli utenti non dovranno accedere a ogni applicazione.
- Identificazione del dispositivo. Accedendo al certificato del dispositivo, creato sul dispositivo quando è stato aggiunto all'area di lavoro. L'applicazione sarà pronta se gli amministratori del tenant abilitano l'accesso condizionale correlato ai dispositivi.
- Verifica dell'identificazione dell'applicazione. Quando un'applicazione chiama il broker, passa l'URL di reindirizzamento e il broker lo verifica.

Per informazioni dettagliate su come abilitare il broker, vedere [usare Microsoft Authenticator o Microsoft Intune portale aziendale nelle applicazioni Novell iOS e Android](msal-net-use-brokers-with-xamarin-apps.md).

### <a name="sample-illustrating-xamarin-ios-specific-properties"></a>Esempio che illustra le proprietà specifiche di Novell iOS

Altre informazioni sono disponibili nel paragrafo [considerazioni specifiche iOS](https://github.com/azure-samples/active-directory-xamarin-native-v2#ios-specific-considerations) del file Readme.MD dell'esempio seguente:

Esempio | Piattaforma | Description
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, piattaforma UWP | Una semplice app Novell Forms che illustra come usare MSAL per autenticare MSA e Azure AD tramite l'endpoint Azure AD V 2.0 e accedere al Microsoft Graph con il token risultante.

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->
