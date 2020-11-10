---
title: Configurare app per dispositivi mobili che chiamano API Web | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come configurare il codice dell'app per dispositivi mobili per chiamare un'API Web
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/16/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: a18a36b8583f8534b2a2e643e5c155dc7a2d65e2
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94444062"
---
# <a name="configure-a-mobile-app-that-calls-web-apis"></a>Configurare un'app per dispositivi mobili che chiama API Web

Dopo aver creato l'applicazione, si apprenderà come configurare il codice usando i parametri di registrazione dell'app. Le applicazioni per dispositivi mobili presentano alcune complessità correlate all'inserimento nel Framework di creazione.

## <a name="find-msal-support-for-mobile-apps"></a>Trova il supporto MSAL per le app per dispositivi mobili

I seguenti tipi di Microsoft Authentication Library (MSAL) supportano le app per dispositivi mobili.

MSAL | Descrizione
------------ | ----------
![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Utilizzato per sviluppare applicazioni portabili. MSAL.NET supporta le piattaforme seguenti per la creazione di un'applicazione per dispositivi mobili: piattaforma UWP (Universal Windows Platform) (UWP), Novell. iOS e Novell. Android.
![MSAL.iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL.iOS | Usato per sviluppare applicazioni iOS native usando Objective-C o SWIFT.
![MSAL.Android](media/sample-v2-code/logo_android.png) <br/> MSAL.Android | Usato per sviluppare applicazioni Android native in Java per Android.

## <a name="instantiate-the-application"></a>Creare un'istanza dell'applicazione

### <a name="android"></a>Android

Le applicazioni per dispositivi mobili usano la `PublicClientApplication` classe. Ecco come crearne un'istanza:

```Java
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);
```

### <a name="ios"></a>iOS

Per le applicazioni per dispositivi mobili in iOS è necessario creare un'istanza della `MSALPublicClientApplication` classe. Per creare un'istanza della classe, usare il codice seguente.

```objc
NSError *msalError = nil;

MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

Le [Proprietà MSALPublicClientApplicationConfig aggiuntive](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplicationConfig.html#/Configuration%20options) possono eseguire l'override dell'autorità predefinita, specificare un URI di reindirizzamento o modificare il comportamento della memorizzazione nella cache del token MSAL.

### <a name="xamarin-or-uwp"></a>Novell o UWP

Questa sezione illustra come creare un'istanza dell'applicazione per le app Novell. iOS, Novell. Android e UWP.

#### <a name="instantiate-the-application"></a>Creare un'istanza dell'applicazione

In Novell o UWP il modo più semplice per creare un'istanza dell'applicazione consiste nell'usare il codice seguente. In questo codice `ClientId` è il GUID dell'app registrata.

```csharp
var app = PublicClientApplicationBuilder.Create(clientId)
                                        .Build();
```

`With<Parameter>`Metodi aggiuntivi impostare l'elemento padre dell'interfaccia utente, eseguire l'override dell'autorità predefinita, specificare un nome e una versione del client per la telemetria, specificare un URI di reindirizzamento e specificare la factory HTTP da usare. La factory HTTP può essere usata, ad esempio, per gestire i proxy e per specificare i dati di telemetria e la registrazione.

Nelle sezioni seguenti vengono fornite ulteriori informazioni sulla creazione di un'istanza dell'applicazione.

##### <a name="specify-the-parent-ui-window-or-activity"></a>Specificare l'interfaccia utente, la finestra o l'attività padre

In Android è necessario passare l'attività padre prima di eseguire l'autenticazione interattiva. In iOS, quando si usa un broker, è necessario passare `ViewController` . Allo stesso modo in UWP, potrebbe essere necessario passare la finestra padre. Passarlo quando si acquisisce il token. Tuttavia, quando si crea l'app, è anche possibile specificare un callback come delegato che restituisce `UIParent` .

```csharp
IPublicClientApplication application = PublicClientApplicationBuilder.Create(clientId)
  .ParentActivityOrWindowFunc(() => parentUi)
  .Build();
```

In Android è consigliabile usare [`CurrentActivityPlugin`](https://github.com/jamesmontemagno/CurrentActivityPlugin) . Il codice del generatore risultante è `PublicClientApplication` simile a questo esempio:

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

##### <a name="find-more-app-building-parameters"></a>Trova altri parametri per la compilazione di app

Per un elenco di tutti i metodi disponibili in `PublicClientApplicationBuilder` , vedere l' [elenco dei metodi](/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods).

Per una descrizione di tutte le opzioni esposte in `PublicClientApplicationOptions` , vedere la [documentazione di riferimento](/dotnet/api/microsoft.identity.client.publicclientapplicationoptions).

## <a name="tasks-for-xamarin-ios"></a>Attività per Novell iOS

Se si usa MSAL.NET in Novell iOS, eseguire le attività seguenti.

* [Eseguire l'override e implementare la `OpenUrl` funzione in `AppDelegate`](msal-net-xamarin-ios-considerations.md#implement-openurl)
* [Abilita gruppi Keychain](msal-net-xamarin-ios-considerations.md#enable-keychain-access)
* [Abilitare la condivisione della cache del token](msal-net-xamarin-ios-considerations.md#enable-token-cache-sharing-across-ios-applications)
* [Abilita accesso Keychain](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

Per altre informazioni, vedere [considerazioni su Novell iOS](msal-net-xamarin-ios-considerations.md).

## <a name="tasks-for-msal-for-ios-and-macos"></a>Attività per MSAL per iOS e macOS

Queste attività sono necessarie quando si usa MSAL per iOS e macOS:

* [Implementare il `openURL` callback](#brokered-authentication-for-msal-for-ios-and-macos)
* [Abilitare i gruppi di accesso a keychain](howto-v2-keychain-objc.md)
* [Personalizzare browser e visualizzazioni Web](customize-webviews.md)

## <a name="tasks-for-xamarinandroid"></a>Attività per Novell. Android

Se si usa Novell. Android, eseguire le attività seguenti:

- [Verificare che il controllo torni a MSAL al termine della parte interattiva del flusso di autenticazione](msal-net-xamarin-android-considerations.md#ensure-that-control-returns-to-msal)
- [Aggiornare il manifesto Android](msal-net-xamarin-android-considerations.md#update-the-android-manifest)
- [Usare la visualizzazione Web incorporata (facoltativo)](msal-net-xamarin-android-considerations.md#use-the-embedded-web-view-optional)
- [Risolvere i problemi in caso di necessità](msal-net-xamarin-android-considerations.md#troubleshooting)

Per altre informazioni, vedere [considerazioni su Novell. Android](msal-net-xamarin-android-considerations.md).

Per considerazioni sui browser in Android, vedere [considerazioni specifiche di Novell. Android con MSAL.NET](msal-net-system-browser-android-considerations.md).

#### <a name="tasks-for-uwp"></a>Attività per UWP

In UWP è possibile usare le reti aziendali. Nelle sezioni seguenti vengono descritte le attività che è necessario completare nello scenario aziendale.

Per altre informazioni, vedere [considerazioni specifiche di UWP con MSAL.NET](msal-net-uwp-considerations.md).

## <a name="configure-the-application-to-use-the-broker"></a>Configurare l'applicazione per l'uso del broker

In Android e iOS i broker abilitano:

- **Single Sign-on (SSO)** : è possibile usare SSO per i dispositivi registrati con Azure Active Directory (Azure ad). Quando si usa SSO, gli utenti non devono accedere a ogni applicazione.
- **Identificazione del dispositivo** : questa impostazione Abilita i criteri di accesso condizionale correlati ai dispositivi Azure ad. Il processo di autenticazione usa il certificato del dispositivo creato quando il dispositivo è stato aggiunto all'area di lavoro.
- **Verifica dell'identificazione dell'applicazione** : quando un'applicazione chiama il broker, passa l'URL di reindirizzamento. Quindi il broker la verifica.

### <a name="enable-the-broker-on-xamarin"></a>Abilitare Service Broker in Novell

Per abilitare il broker in Novell, usare il `WithBroker()` parametro quando si chiama il `PublicClientApplicationBuilder.CreateApplication` metodo. Per impostazione predefinita, `.WithBroker()` è impostato su true.

Per abilitare l'autenticazione negoziata per Novell. iOS, seguire la procedura descritta nella [sezione Novell. iOS](#enable-brokered-authentication-for-xamarin-ios) in questo articolo.

### <a name="enable-the-broker-for-msal-for-android"></a>Abilitare Service Broker per MSAL per Android

Per informazioni sull'abilitazione di un broker in Android, vedere [autenticazione negoziata in Android](msal-android-single-sign-on.md).

### <a name="enable-the-broker-for-msal-for-ios-and-macos"></a>Abilitare Service Broker per MSAL per iOS e macOS

L'autenticazione negoziata è abilitata per impostazione predefinita per gli scenari di Azure AD in MSAL per iOS e macOS.

Le sezioni seguenti forniscono le istruzioni per configurare l'applicazione per il supporto dell'autenticazione negoziata per MSAL per Novell. iOS o MSAL per iOS e macOS. Nei due set di istruzioni alcuni passaggi sono diversi.

### <a name="enable-brokered-authentication-for-xamarin-ios"></a>Abilitare l'autenticazione negoziata per Novell iOS

Eseguire la procedura descritta in questa sezione per abilitare l'app Novell. iOS per comunicare con l'app [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) .

#### <a name="step-1-enable-broker-support"></a>Passaggio 1: abilitare il supporto broker

Il supporto di Service Broker è disabilitato per impostazione predefinita. Viene abilitata per una singola `PublicClientApplication` classe. Usare il `WithBroker()` parametro quando si crea la `PublicClientApplication` classe tramite `PublicClientApplicationBuilder` . Per `WithBroker()` impostazione predefinita, il parametro è impostato su true.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

#### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Passaggio 2: aggiornare AppDelegate per gestire il callback

Quando MSAL.NET chiama il broker, il broker chiama di nuovo l'applicazione. Esegue una chiamata tramite il `AppDelegate.OpenUrl` metodo. Poiché MSAL attende la risposta dal broker, l'applicazione deve collaborare per chiamare MSAL.NET. Questo comportamento viene configurato aggiornando il `AppDelegate.cs` file per eseguire l'override del metodo, come illustrato nel codice seguente.

```csharp
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

Questo metodo viene richiamato ogni volta che l'applicazione viene avviata. Si tratta di un'opportunità per elaborare la risposta dal broker e per completare il processo di autenticazione avviato da MSAL.NET.

#### <a name="step-3-set-a-uiviewcontroller"></a>Passaggio 3: impostare un UIViewController ()

Per Novell iOS, non è in genere necessario impostare una finestra degli oggetti. In questo caso, tuttavia, è consigliabile impostarlo in modo che sia possibile inviare e ricevere risposte da un broker. Per impostare una finestra oggetto, in `AppDelegate.cs` è necessario impostare `ViewController` .

Per impostare la finestra oggetto, attenersi alla seguente procedura:

1. In `AppDelegate.cs` impostare `App.RootViewController` su un nuovo oggetto `UIViewController()` . Questa impostazione garantisce che la chiamata al broker includa `UIViewController` . Se non è impostato correttamente, è possibile che venga ricevuto questo errore:

    `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker."`

1. Nella `AcquireTokenInteractive` chiamata usare `.WithParentActivityOrWindow(App.RootViewController)` . Passare il riferimento alla finestra oggetto che verrà utilizzata. Ecco un esempio:

    In `App.cs`:
    ```csharp
       public static object RootViewController { get; set; }
    ```
    In `AppDelegate.cs`:
    ```csharp
       LoadApplication(new App());
       App.RootViewController = new UIViewController();
    ```
    Nella `AcquireToken` chiamata:
    ```csharp
    result = await app.AcquireTokenInteractive(scopes)
                 .WithParentActivityOrWindow(App.RootViewController)
                 .ExecuteAsync();
    ```

#### <a name="step-4-register-a-url-scheme"></a>Passaggio 4: registrare uno schema URL

MSAL.NET usa gli URL per richiamare il broker e quindi riportare la risposta di Service Broker all'app. Per completare la round trip, registrare lo schema dell'URL dell'app nel `Info.plist` file.

Per registrare lo schema dell'URL dell'app, seguire questa procedura:

1. Prefisso `CFBundleURLSchemes` con `msauth` .
1. Aggiungere `CFBundleURLName` alla fine. Seguire questo modello:

   `$"msauth.(BundleId)"`

   Qui, `BundleId` identifica in modo univoco il dispositivo. Ad esempio, se `BundleId` è `yourcompany.xforms` , lo schema dell'URL è `msauth.com.yourcompany.xforms` .

   > [!NOTE]
   > Questo schema URL diventerà parte dell'URI di reindirizzamento che identifica in modo univoco l'app quando riceve la risposta del broker.

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

#### <a name="step-5-add-to-the-lsapplicationqueriesschemes-section"></a>Passaggio 5: aggiungere alla sezione LSApplicationQueriesSchemes

MSAL USA `–canOpenURL:` per verificare se il broker è installato nel dispositivo. In iOS 9, Apple ha bloccato gli schemi per i quali un'applicazione può eseguire una query.

Aggiungere `msauthv2` alla `LSApplicationQueriesSchemes` sezione del `Info.plist` file, come nell'esempio di codice seguente:

```XML
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="brokered-authentication-for-msal-for-ios-and-macos"></a>Autenticazione negoziata per MSAL per iOS e macOS

L'autenticazione negoziata è abilitata per impostazione predefinita per gli scenari Azure AD.

#### <a name="step-1-update-appdelegate-to-handle-the-callback"></a>Passaggio 1: aggiornare AppDelegate per gestire il callback

Quando MSAL per iOS e macOS chiama il broker, il broker richiama l'applicazione usando il `openURL` metodo. Poiché MSAL attende la risposta dal broker, l'applicazione deve collaborare per chiamare MSAL. Configurare questa funzionalità aggiornando il `AppDelegate.m` file per eseguire l'override del metodo, come illustrato negli esempi di codice seguenti.

```objc
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
    return [MSALPublicClientApplication handleMSALResponse:url
                                         sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {

        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }

        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

> [!NOTE]
> Se è stata adottata `UISceneDelegate` in iOS 13 o versioni successive, inserire il callback MSAL in in `scene:openURLContexts:` `UISceneDelegate` alternativa. MSAL `handleMSALResponse:sourceApplication:` deve essere chiamato una sola volta per ogni URL.
>
> Per ulteriori informazioni, vedere la [documentazione di Apple](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc).

#### <a name="step-2-register-a-url-scheme"></a>Passaggio 2: registrare uno schema URL

MSAL per iOS e macOS usa gli URL per richiamare il broker e quindi restituire la risposta di Service Broker all'app. Per completare la round trip, registrare uno schema URL per l'app nel `Info.plist` file.

Per registrare uno schema per l'app:

1. Anteporre al prefisso lo schema URL personalizzato `msauth` .

1. Aggiungere l'identificatore del bundle alla fine dello schema. Seguire questo modello:

   `$"msauth.(BundleId)"`

   Qui, `BundleId` identifica in modo univoco il dispositivo. Ad esempio, se `BundleId` è `yourcompany.xforms` , lo schema dell'URL è `msauth.com.yourcompany.xforms` .

   > [!NOTE]
   > Questo schema URL diventerà parte dell'URI di reindirizzamento che identifica in modo univoco l'app quando riceve la risposta del broker. Verificare che l'URI di reindirizzamento nel formato `msauth.(BundleId)://auth` sia registrato per l'applicazione nel [portale di Azure](https://portal.azure.com).

   ```XML
   <key>CFBundleURLTypes</key>
   <array>
       <dict>
           <key>CFBundleURLSchemes</key>
           <array>
               <string>msauth.[BUNDLE_ID]</string>
           </array>
       </dict>
   </array>
   ```

#### <a name="step-3-add-lsapplicationqueriesschemes"></a>Passaggio 3: aggiungere LSApplicationQueriesSchemes

Aggiungere `LSApplicationQueriesSchemes` per consentire le chiamate all'app Microsoft Authenticator, se installata.

> [!NOTE]
> Lo `msauthv3` schema è necessario quando l'app viene compilata con Xcode 11 e versioni successive.

Di seguito è riportato un esempio di come aggiungere `LSApplicationQueriesSchemes` :

```XML
<key>LSApplicationQueriesSchemes</key>
<array>
  <string>msauthv2</string>
  <string>msauthv3</string>
</array>
```

### <a name="brokered-authentication-for-xamarinandroid"></a>Autenticazione negoziata per Novell. Android

Per informazioni sull'abilitazione di un broker in Android, vedere [autenticazione negoziata in Novell. Android](msal-net-use-brokers-with-xamarin-apps.md#brokered-authentication-for-android).

## <a name="next-steps"></a>Passaggi successivi

Passare all'articolo successivo in questo scenario, [acquisendo un token](scenario-mobile-acquire-token.md).