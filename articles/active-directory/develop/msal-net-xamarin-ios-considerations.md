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
ms.date: 09/09/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: f550cb4e9069055da6569492b35fc7fe75d70980
ms.sourcegitcommit: 1b320bc7863707a07e98644fbaed9faa0108da97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89594051"
---
# <a name="considerations-for-using-xamarin-ios-with-msalnet"></a>Considerazioni sull'uso di Novell iOS con MSAL.NET

Quando si usa Microsoft Authentication Library per .NET (MSAL.NET) in Novell iOS, è necessario:

- Eseguire l'override e implementare la `OpenUrl` funzione in `AppDelegate` .
- Abilitare i gruppi di portachiavi.
- Abilitare la condivisione della cache del token.
- Abilitare l'accesso keychain.
- Informazioni sui problemi noti di iOS 12 e iOS 13 e sull'autenticazione.

## <a name="implement-openurl"></a>Implementare OpenUrl

Eseguire l'override del `OpenUrl` metodo della `FormsApplicationDelegate` classe derivata e chiamare `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs` . Ecco un esempio:

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

Eseguire inoltre le attività seguenti:

* Definire uno schema URI di reindirizzamento.
* Richiedere le autorizzazioni per l'app per chiamare un'altra app.
* Hanno un formato specifico per l'URI di reindirizzamento.
* [Registrare un URI di reindirizzamento](quickstart-register-app.md#add-a-redirect-uri) nel portale di Azure.

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
> La `KeychainSecurityGroup` proprietà è deprecata. Utilizzare in alternativa la proprietà `iOSKeychainSecurityGroup`. Il `TeamId` prefisso non è obbligatorio quando si usa `iOSKeychainSecurityGroup` .

### <a name="use-microsoft-authenticator"></a>USA Microsoft Authenticator

L'applicazione può usare Microsoft Authenticator come broker per abilitare:

- **SSO**: quando si Abilita SSO, gli utenti non devono accedere a ogni applicazione.
- **Identificazione del dispositivo**: usare l'identificazione del dispositivo per l'autenticazione tramite l'accesso al certificato del dispositivo. Questo certificato viene creato nel dispositivo quando viene aggiunto all'area di lavoro. L'applicazione sarà pronta se gli amministratori tenant abilitano l'accesso condizionale correlato ai dispositivi.
- **Verifica dell'identificazione dell'applicazione**: quando un'applicazione chiama il broker, passa l'URL di reindirizzamento. Il Broker verifica l'URL di reindirizzamento.

Per informazioni dettagliate su come abilitare un broker, vedere [usare Microsoft Authenticator o Microsoft Intune portale aziendale nelle applicazioni Novell iOS e Android](msal-net-use-brokers-with-xamarin-apps.md).

## <a name="known-issues-with-ios-12-and-authentication"></a>Problemi noti con iOS 12 e autenticazione

Microsoft ha rilasciato un [avviso di sicurezza](https://github.com/aspnet/AspNetCore/issues/4647) relativo a un'incompatibilità tra iOS 12 e alcuni tipi di autenticazione. L'incompatibilità interrompe gli accessi social, WSFed e OIDC. L'avviso di sicurezza consente di comprendere come rimuovere le restrizioni di sicurezza ASP.NET dalle applicazioni per renderle compatibili con iOS 12.

Quando si sviluppano applicazioni MSAL.NET in Novell iOS, è possibile che venga visualizzato un ciclo infinito quando si tenta di accedere ai siti Web da iOS 12. Questo comportamento è simile a questo problema di ADAL su GitHub: [ciclo infinito quando si prova ad accedere al sito Web da iOS 12 #1329](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329).

È anche possibile che venga visualizzato un break in ASP.NET Core Autenticazione OIDC con iOS 12 Safari. Per altre informazioni, vedere questo [problema di WebKit](https://bugs.webkit.org/show_bug.cgi?id=188165).

## <a name="known-issues-with-ios-13-and-authentication"></a>Problemi noti di iOS 13 e autenticazione

Se l'app richiede l'accesso condizionale o il supporto per l'autenticazione dei certificati, consentire all'app di comunicare con l'app Microsoft Authenticator Broker. MSAL è quindi responsabile della gestione delle richieste e delle risposte tra l'applicazione e Microsoft Authenticator.

In iOS 13, Apple ha apportato una modifica all'API di rilievo rimuovendo la capacità dell'applicazione di leggere l'applicazione di origine quando riceve una risposta da un'applicazione esterna tramite schemi URL personalizzati.

Documentazione di Apple per gli stati [UIApplicationOpenURLOptionsSourceApplicationKey](https://developer.apple.com/documentation/uikit/uiapplicationopenurloptionssourceapplicationkey?language=objc) :

> *Se la richiesta proviene da un'altra app appartenente al team, UIKit imposta il valore di questa chiave sull'ID dell'app. Se l'identificatore del team dell'app di origine è diverso dall'identificatore del team dell'app corrente, il valore della chiave è Nil.*

Questa modifica si interrompe per MSAL perché si è basata su `UIApplication.SharedApplication.OpenUrl` per verificare la comunicazione tra MSAL e l'app Microsoft Authenticator.

In iOS 13 è inoltre necessario che lo sviluppatore fornisca un controller di presentazione quando usa `ASWebAuthenticationSession` .

Se si esegue la compilazione con Xcode 11 e si usa il broker iOS o, l'app ha un effetto `ASWebAuthenticationSession` .

In questi casi, usare [MSAL.NET 4.4.0 +](https://www.nuget.org/packages/Microsoft.Identity.Client/) per consentire l'autenticazione riuscita.

### <a name="additional-requirements"></a>Requisiti aggiuntivi

- Quando si usano le librerie MSAL più recenti, verificare che nel dispositivo sia installato **Microsoft Authenticator versione 6.3.19 +** .
- Quando si esegue l'aggiornamento a MSAL.NET 4.4.0 +, aggiornare il `LSApplicationQueriesSchemes` nel file *info. plist* e aggiungere `msauthv3` :

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

    L'aggiunta `msauthv3` a *info. plist* è necessaria per rilevare la presenza dell'app Microsoft Authenticator più recente sul dispositivo che supporta iOS 13.

## <a name="report-an-issue"></a>Segnalare un problema

Se si hanno domande o si vuole segnalare un problema riscontrato in MSAL.NET, aprire un problema nel repository [AzureAD/Microsoft-Authentication-Library-for-DotNet](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues) su GitHub.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sulle proprietà per Novell iOS, vedere il paragrafo [considerazioni specifiche per iOS](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/master/1-Basic#ios-specific-considerations) del file Readme.MD dell'esempio seguente:

Esempio | Piattaforma | Descrizione
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Novell iOS, Android, piattaforma UWP (Universal Windows Platform) (UWP) | Una semplice app Novell Forms che illustra come usare MSAL per autenticare gli account personali Microsoft e Azure AD tramite l'endpoint Azure AD 2,0. L'app mostra anche come usare il token risultante per accedere Microsoft Graph.

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->
