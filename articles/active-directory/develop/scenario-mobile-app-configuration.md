---
title: App per dispositivi mobili che chiama le API Web (configurazione del codice)-piattaforma di identità Microsoft | Azure
description: Informazioni su come creare un'app per dispositivi mobili che chiama le API Web (configurazione del codice dell'app)
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
ms.date: 07/23/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ebf524d932322fa08729f229a451afe656900d5
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70061413"
---
# <a name="mobile-app-that-calls-web-apis---code-configuration"></a>App per dispositivi mobili che chiama API Web-configurazione del codice

Dopo aver creato l'applicazione, si apprenderà come configurare il codice usando i parametri di registrazione dell'app. Le applicazioni per dispositivi mobili hanno anche alcune specifiche complesse, che è necessario eseguire con l'adattamento al Framework usato per compilare queste app

## <a name="msal-libraries-supporting-mobile-apps"></a>Librerie MSAL che supportano app per dispositivi mobili

Le librerie Microsoft che supportano le app per dispositivi mobili sono:

  Libreria MSAL | DESCRIZIONE
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Per lo sviluppo di applicazioni portabili. Le piattaforme supportate da MSAL.NET per compilare un'applicazione per dispositivi mobili sono UWP, Novell. iOS e Novell. Android.
  ![MSAL.iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL.iOS | Per sviluppare applicazioni iOS native con Objective C o SWIFT
  ![MSAL.Android](media/sample-v2-code/logo_android.png) <br/> MSAL.Android | Per sviluppare applicazioni Android native in Java per Android

## <a name="configuring-the-application"></a>Configurazione dell'applicazione

Le applicazioni per dispositivi `PublicClientApplication` mobili usano la classe. Ecco come crearne un'istanza:

### <a name="android"></a>Android

```Java
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);
```

### <a name="ios"></a>iOS

```swift
// Initialize the app.
guard let authorityURL = URL(string: kAuthority) else {
    self.loggingText.text = "Unable to create authority URL"
    return
}
let authority = try MSALAADAuthority(url: authorityURL)
let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
}
```

### <a name="xamarin-or-uwp"></a>Novell o UWP

Il paragrafo seguente illustra come configurare il codice dell'applicazione per le app Novell. iOS, Novell. Android e UWP. Il primo passaggio consiste nell'creare un'istanza dell'applicazione. Un passaggio facoltativo consiste nel configurare il broker.

#### <a name="instantiating-the-application"></a>Creazione di un'istanza dell'applicazione

In Novell, o UWP, il modo più semplice per creare un'istanza dell'applicazione è il seguente, dove `ClientId` è il GUID dell'app registrata.

```CSharp
var app = PublicClientApplicationBuilder.Create(clientId)
                                        .Build();
```

Sono disponibili altri metodi con*parametri* che impostano l'elemento padre dell'interfaccia utente, eseguono l'override dell'autorità predefinita, specificano un nome e una versione del client (per la telemetria), specificano un URI di reindirizzamento, specificano la factory HTTP da usare (ad esempio per gestire i proxy, specificare telemetria e registrazione). Questo è l'argomento dei paragrafi seguenti.

##### <a name="specifying-the-parent-uiwindowactivity"></a>Specifica dell'interfaccia utente/finestra/attività padre

In Android è necessario passare l'attività padre prima di eseguire l'autenticazione interattiva. In iOS, quando si usa un broker, è necessario passare il ViewController. Allo stesso modo in UWP, potrebbe essere necessario passare la finestra padre. Questo è possibile quando si acquisisce il token, ma è anche possibile specificare un callback al momento della creazione dell'app un delegato che restituisce UIParent.

```CSharp
IPublicClientApplication application = PublicClientApplicationBuilder.Create(clientId)
  .ParentActivityOrWindowFunc(() => parentUi)
  .Build();
```

In Android è consigliabile usare `CurrentActivityPlugin` [qui](https://github.com/jamesmontemagno/CurrentActivityPlugin).  Quindi, `PublicClientApplication` il codice del generatore sarà simile al seguente:

```CSharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

##### <a name="more-app-building-parameters"></a>Altri parametri di compilazione dell'app

- Per l'elenco di tutti i modificatori disponibili `PublicClientApplicationBuilder`in, vedere la documentazione di riferimento [PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)
- Per la descrizione di tutte le opzioni esposte in `PublicClientApplicationOptions` vedere [PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions), nella documentazione di riferimento

#### <a name="xamarin-ios-specific-considerations"></a>Considerazioni specifiche su Novell iOS

In Novell iOS sono disponibili diverse considerazioni che è necessario tenere in considerazione quando si usa MSAL.NET:

1. [Eseguire l'override e `OpenUrl` implementare la funzione in`AppDelegate`](msal-net-xamarin-ios-considerations.md#implement-openurl)
1. [Abilita gruppi Keychain](msal-net-xamarin-ios-considerations.md#enable-keychain-access)
1. [Abilitare la condivisione della cache del token](msal-net-xamarin-ios-considerations.md#enable-token-cache-sharing-across-ios-applications)
1. [Abilita accesso Keychain](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

Informazioni dettagliate sono disponibili in [considerazioni su Novell iOS](msal-net-xamarin-ios-considerations.md)

#### <a name="other-xamarin-android-specific-considerations"></a>Altre considerazioni specifiche su Novell Android

Ecco le specifiche di Novell per Android:

- [Assicurando che il controllo torni a MSAL una volta terminata la parte interattiva del flusso di autenticazione](msal-net-xamarin-android-considerations.md#ensuring-control-goes-back-to-msal-once-the-interactive-portion-of-the-authentication-flow-ends)
- [Aggiornare il manifesto Android](msal-net-xamarin-android-considerations.md#update-the-android-manifest)
- [Usare la visualizzazione Web incorporata (facoltativo)](msal-net-xamarin-android-considerations.md#use-the-embedded-web-view-optional)
- [Risoluzione dei problemi](msal-net-xamarin-android-considerations.md#troubleshooting)

Informazioni dettagliate sono disponibili in [considerazioni su Novell Android](msal-net-xamarin-android-considerations.md)

Infine, esistono alcune particolarità da conoscere sui browser in Android. Sono descritte in [considerazioni specifiche di Novell per Android con MSAL.NET](msal-net-system-browser-android-considerations.md)

#### <a name="uwp-specific-considerations"></a>Considerazioni specifiche di UWP

In UWP è possibile usare le reti aziendali. Per altre informazioni sull'uso della libreria MSAL con UWP, vedere [considerazioni specifiche di piattaforma UWP (Universal Windows Platform) con MSAL.NET](msal-net-uwp-considerations.md).

## <a name="configuring-the-application-to-use-the-broker"></a>Configurazione dell'applicazione per l'utilizzo del broker

### <a name="why-use-brokers-on-xamarinios-and-xamarinandroid-applications"></a>Perché usare i broker nelle applicazioni Novell. iOS e Novell. Android?

In Android e iOS i broker abilitano:

- Single Sign-on (SSO). Gli utenti non dovranno accedere a ogni applicazione.
- Identificazione del dispositivo. Abilita Azure AD criteri di accesso condizionale correlati ai dispositivi, accedendo al certificato del dispositivo creato sul dispositivo quando è stato aggiunto all'area di lavoro.
- Verifica dell'identificazione dell'applicazione. Quando un'applicazione chiama il broker, passa l'URL di reindirizzamento e il broker lo verifica.

### <a name="enable-the-brokers-on-xamarin"></a>Abilitare i broker in Novell

Per abilitare una di queste funzionalità, usare il `WithBroker()` parametro quando si chiama `PublicClientApplicationBuilder.CreateApplication` il metodo. `.WithBroker()`per impostazione predefinita, è impostato su true. Attenersi alla procedura seguente per [iOS](#brokered-authentication-for-xamarinios).

### <a name="brokered-authentication-for-xamarinios"></a>Autenticazione negoziata per Novell. iOS

Seguire questa procedura per consentire all'app Novell. iOS di comunicare con l'app [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) .

#### <a name="step-1-enable-broker-support"></a>Passaggio 1: Abilita supporto broker

Il supporto di Service Broker è abilitato in`PublicClientApplication` base al criterio. È disabilitata per impostazione predefinita. È necessario utilizzare il `WithBroker()` parametro (impostato su true per impostazione predefinita) quando si `PublicClientApplication` crea l' `PublicClientApplicationBuilder`oggetto tramite l'oggetto.

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

#### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Passaggio 2: Aggiornare AppDelegate per gestire il callback

Quando MSAL.NET chiama il broker, il broker chiamerà a sua volta l'applicazione tramite il `AppDelegate.OpenUrl` metodo. Poiché MSAL attenderà la risposta dal broker, l'applicazione deve collaborare per chiamare MSAL.NET. A tale scopo, aggiornare il `AppDelegate.cs` file per eseguire l'override del metodo riportato di seguito.

```CSharp
public override bool OpenUrl(UIApplication app, NSUrl url,
                             string sourceApplication,
                             NSObject annotation)
{
 if (AuthenticationContinuationHelper.IsBrokerResponse(sourceApplication))
 {
  AuthenticationContinuationHelper.SetBrokerContinuationEventArgs(url);
  return true;
 }
 else if (!AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url))
 {
  return false;
 }
 return true;
}
```

Questo metodo viene richiamato ogni volta che viene avviata l'applicazione e viene usato come opportunità per elaborare la risposta dal broker e completare il processo di autenticazione avviato da MSAL.NET.

#### <a name="step-3-set-a-uiviewcontroller"></a>Passaggio 3: Imposta un UIViewController ()

Con Novell iOS, in genere non è necessario impostare una finestra degli oggetti, ma in questo caso si esegue per inviare e ricevere risposte da un broker. Sempre in `AppDelegate.cs`, impostare un ViewController.

Per impostare la finestra oggetto, eseguire le operazioni seguenti:

1) In `AppDelegate.cs` `UIViewController()`impostare suunnuovooggetto.`App.RootViewController` In questo modo si verifica un `UIViewController` con la chiamata al broker. Se la configurazione non è corretta, è possibile che venga ricevuto questo errore:`"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
2) Nella chiamata AcquireTokenInteractive usare `.WithParentActivityOrWindow(App.RootViewController)` e passare il riferimento alla finestra oggetto che verrà usata.

**Ad esempio:**

In `App.cs`:
```CSharp
   public static object RootViewController { get; set; }
```
In `AppDelegate.cs`:
```CSharp
   LoadApplication(new App());
   App.RootViewController = new UIViewController();
```
Nella chiamata al token di acquisizione:
```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

#### <a name="step-4-register-a-url-scheme"></a>Passaggio 4: Registrare uno schema URL

MSAL.NET usa gli URL per richiamare il broker e quindi riportare la risposta di Service Broker all'app. Per completare la round trip, è necessario registrare uno schema URL per l'app nel `Info.plist` file.

`CFBundleURLSchemes` Anteporre a .`msauth` Aggiungere `CFBundleURLName` quindi alla fine.

`$"msauth.(BundleId)"`

**Per esempio:** 
`msauth.com.yourcompany.xforms`

> [!NOTE]
> Questo schema URL diventerà parte del RedirectUri usato per identificare in modo univoco l'app quando riceve la risposta dal broker.

```XML
 <key>CFBundleURLTypes</key>
    <array>
      <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.yourcompany.xforms</string>
        <key>CFBundleURLSchemes</key>
        <array>
          <string>msauth.com.yourcompany.xforms</string>
        </array>
      </dict>
    </array>
```

#### <a name="step-5-lsapplicationqueriesschemes"></a>Passaggio 5: LSApplicationQueriesSchemes

MSAL USA `–canOpenURL:` per verificare se il broker è installato nel dispositivo. In iOS 9, Apple ha bloccato gli schemi per cui un'applicazione può eseguire query.

**Aggiungi** alla sezione del`Info.plist` file. `LSApplicationQueriesSchemes` **`msauthv2`**

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="brokered-authentication-for-xamarinandroid"></a>Autenticazione negoziata per Novell. Android

MSAL.NET non supporta ancora i broker per Android.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Acquisizione di un token](scenario-mobile-acquire-token.md)
