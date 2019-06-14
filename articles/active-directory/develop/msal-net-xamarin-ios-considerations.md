---
title: Considerazioni su iOS di Xamarin (Microsoft Authentication Library per .NET) | Azure
description: Informazioni sulle considerazioni specifiche per Xamarin iOS con Microsoft Authentication Library per .NET (MSAL.NET).
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
ms.openlocfilehash: bf236bff2300129ec97d3b8946c4c2a2748bca77
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65602140"
---
# <a name="xamarin-ios-specific-considerations-with-msalnet"></a>Considerazioni su iOS specifiche di Xamarin con MSAL.NET
In Xamarin iOS, esistono diverse considerazioni di cui è necessario tenere conto quando si usa MSAL.NET

- [Problemi noti relativi a iOS 12 e autenticazione](#known-issues-with-ios-12-and-authentication)
- [Eseguire l'override e implementare il `OpenUrl` funzionare nel `AppDelegate`](#implement-openurl)
- [Abilitare i gruppi di portachiavi](#enable-keychain-groups)
- [Abilitare la condivisione della cache dei token](#enable-token-cache-sharing-across-ios-applications)
- [Abilitare l'accesso Keychain](#enable-keychain-access)

## <a name="known-issues-with-ios-12-and-authentication"></a>Problemi noti relativi a iOS 12 e autenticazione
Microsoft ha rilasciato una [avviso di sicurezza](https://github.com/aspnet/AspNetCore/issues/4647) per fornire informazioni su un'incompatibilità tra iOS12 e alcuni tipi di autenticazione. L'incompatibilità delle interruzioni sociale, WSFed e OIDC gli account di accesso. Questo avviso offre anche informazioni aggiuntive sulle operazioni che gli sviluppatori possono eseguire per rimuovere le restrizioni di sicurezza corrente aggiunte da ASP.NET alle applicazioni di diventare compatibili con iOS12.  

Quando si sviluppano applicazioni MSAL.NET in Xamarin iOS, è probabile che un ciclo infinito quando si tenta di accedere a siti Web da iOS 12 (simile al seguente [problema ADAL](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329). 

È anche possibile visualizzare un'interruzione nell'autenticazione di OIDC di ASP.NET Core con iOS 12 Safari come descritto in questo [problema WebKit](https://bugs.webkit.org/show_bug.cgi?id=188165).

## <a name="implement-openurl"></a>Implementare OpenUrl

Prima di tutto è necessario eseguire l'override di `OpenUrl` metodo del `FormsApplicationDelegate` classe derivata e chiamata `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs`.

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

È anche necessario definire uno schema URL, richiedono le autorizzazioni per l'app a chiamare un'altra app, hanno un formato specifico per l'URL di reindirizzamento e registrare questo URL di reindirizzamento nel [portale di Azure](https://portal.azure.com).

## <a name="enable-keychain-groups"></a>Abilitare i gruppi di portachiavi

Per rendere il token nella cache di lavoro e avere la `AcquireTokenSilentAsync` lavoro metodo, è necessario eseguire più procedure:
1. Abilitare l'accesso a keychain nelle *`* Entitlements.plist* file e specificare il **gruppi di portachiavi** nell'identificatore del bundle.
2. Selezionare *`*Entitlements.plist*`* del file nei **Entitlement personalizzati** campo nella finestra di opzioni progetto iOS **visualizzazione di firma del Bundle**.
3. Quando un certificato di firma, assicurarsi che XCode Usa lo stesso ID di Apple.

## <a name="enable-token-cache-sharing-across-ios-applications"></a>Abilitare la cache dei token condivisione tra le applicazioni iOS

A partire da MSAL 2.x, è possibile specificare un gruppo di sicurezza di keychain da usare per rendere persistente la cache dei token in più applicazioni. In questo modo è possibile condividere tra diverse applicazioni con il gruppo di sicurezza di keychain stesso incluse quelle sviluppate con la cache dei token [ADAL.NET](https://aka.ms/adal-net), le applicazioni xamarin. IOS MSAL.NET e le applicazioni iOS native sviluppate con [ADAL.objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc) oppure [MSAL.objc](https://github.com/AzureAD/microsoft-authentication-library-for-objc)).

La cache dei token di condivisione consente accesso single sign-on (SSO) tra tutte le applicazioni che usino lo stesso gruppo di sicurezza di keychain.

Per abilitare l'accesso single sign-on, è necessario impostare il `PublicClientApplication.iOSKeychainSecurityGroup` proprietà sullo stesso valore in tutte le applicazioni.

Un esempio di questo con MSAL v3.x sarebbe:
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.msalrocks")
     .Build();
```

Un esempio di questo con MSAL v2.7.x sarebbe:

```csharp
PublicClientApplication.iOSKeychainSecurityGroup = "com.microsoft.msalrocks";
```

> [!NOTE]
> Il `KeychainSecurityGroup` proprietà è stata deprecata. In precedenza, in MSAL 2.x, gli sviluppatori dovevano includono il prefisso TeamId quando si usa il `KeychainSecurityGroup` proprietà. 
> 
> A questo punto, a partire da MSAL 2.7.x, MSAL risolverà il prefisso TeamId in fase di esecuzione quando si usa il `iOSKeychainSecurityGroup` proprietà. Quando si usa questa proprietà, il valore non deve contenere il prefisso TeamId. 
> 
> Usare il nuovo `iOSKeychainSecurityGroup` proprietà, che non richiede agli sviluppatori di fornire il TeamId. Il `KeychainSecurityGroup` proprietà è obsoleta. 

## <a name="enable-keychain-access"></a>Abilitare l'accesso keychain

In MSAL 2.x e ADAL 4.x, il TeamId viene usato per accedere al keychain, che consente le librerie di autenticazione offrire single sign-on (SSO) tra le applicazioni del server di pubblicazione stesso. 

Che cos'è il [TeamIdentifierPrefix](/xamarin/ios/deploy-test/provisioning/entitlements?tabs=vsmac) (TeamId)? È un identificatore univoco (aziendale o personale) in Store l'App. L'ID App sia univoco per un'app. Se si dispone di più app, il TeamId per tutte le app saranno gli stessi, ma l'ID App sarà diverso. Il gruppo di accesso keychain preceduto dal TeamId automaticamente per ogni gruppo dal sistema. È come il sistema operativo impone che le app dallo stesso editore possono accedere il portachiavi condiviso. 

Quando si inizializza il `PublicClientApplication`, se si riceve un `MsalClientException` con il messaggio: `TeamId returned null from the iOS keychain...`, dovrai eseguire le operazioni seguenti nell'app per Xamarin iOS:

1. In Visual Studio, nella scheda Debug, passare alla proprietà nameOfMyApp.iOS...
2. Passare quindi alla firma del Bundle iOS 
3. Entitlement personalizzati, scegliere il... Selezionare il file entitlements. plist dell'App
4. Nel file csproj dell'app per iOS, è necessario disporre di questa riga ora inclusa: `<CodesignEntitlements>Entitlements.plist</CodesignEntitlements>`
5. **Ricompilare** il progetto.

Si tratta *inoltre* abilitare l'accesso a keychain nel `Entitlements.plist` file, usando il sotto il gruppo di accesso o il proprio:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
</plist>
```

## <a name="next-steps"></a>Passaggi successivi

Altri dettagli sono disponibili nel [iOS considerazioni specifiche](https://github.com/azure-samples/active-directory-xamarin-native-v2#ios-specific-considerations) paragrafo del file readme.md dell'esempio seguente:

Esempio | Piattaforma | Descrizione 
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, piattaforma UWP | Una semplice app Xamarin. Forms che illustra come usare MSAL per autenticare l'account del servizio gestito e Azure AD tramite endpoint V2.0 di AAD e accedere a Microsoft Graph con il token risultante. <br>![Topologia](media/msal-net-xamarin-ios-considerations/topology.png)