---
title: Configurare le app per dispositivi mobili che chiamano le API Web Azure
titleSuffix: Microsoft identity platform
description: Scopri come creare un'app per dispositivi mobili che chiama le API Web (configurazione del codice dell'app)
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
ms.openlocfilehash: fc25f13d0b0b8a264dcd47a5fdebb0533e93fb55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132493"
---
# <a name="configure-a-mobile-app-that-calls-web-apis"></a>Configurare un'app per dispositivi mobili che chiama le API Web

Dopo aver creato l'applicazione, si apprenderà come configurare il codice utilizzando i parametri di registrazione dell'app. Le applicazioni mobili presentano alcune complessità legate all'adattamento nel loro framework di creazione.

## <a name="find-msal-support-for-mobile-apps"></a>Trovare il supporto MSAL per le app per dispositivi mobili

I seguenti tipi Microsoft Authentication Library (MSAL) supportano le app per dispositivi mobili.

MSAL | Descrizione
------------ | ----------
![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Utilizzato per sviluppare applicazioni portatili. MSAL.NET supporta le seguenti piattaforme per la creazione di un'applicazione per dispositivi mobili: UWP (Universal Windows Platform), Xamarin.iOS e Xamarin.Android.
![MSAL.iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL.iOS | Utilizzato per sviluppare applicazioni iOS native utilizzando Objective-C o Swift.
![MSAL.Android](media/sample-v2-code/logo_android.png) <br/> MSAL.Android | Utilizzato per sviluppare applicazioni Android native in Java per Android.

## <a name="instantiate-the-application"></a>Creare un'istanza dell'applicazione

### <a name="android"></a>Android

Le applicazioni `PublicClientApplication` mobili utilizzano la classe . Ecco come crearne un'istanza:Here's how to instantiate it:

```Java
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);
```

### <a name="ios"></a>iOS

Le applicazioni mobili in iOS devono creare un'istanza della `MSALPublicClientApplication` classe. Per creare un'istanza della classe, utilizzare il codice seguente. 

```objc
NSError *msalError = nil;
     
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

[Ulteriori proprietà MSALPublicClientApplicationConfig](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplicationConfig.html#/Configuration%20options) possono eseguire l'override dell'autorità predefinita, specificare un URI di reindirizzamento o modificare il comportamento della memorizzazione nella cache dei token MSAL. 

### <a name="xamarin-or-uwp"></a>Xamarin o UWP

Questa sezione spiega come creare un'istanza dell'applicazione per le app Xamarin.iOS, Xamarin.Android e UWP.

#### <a name="instantiate-the-application"></a>Creare un'istanza dell'applicazione

In Xamarin o UWP, il modo più semplice per creare un'istanza dell'applicazione consiste nell'utilizzare il codice seguente. In questo `ClientId` codice, è il GUID dell'app registrata.

```csharp
var app = PublicClientApplicationBuilder.Create(clientId)
                                        .Build();
```

Metodi `With<Parameter>` aggiuntivi impostano l'elemento padre dell'interfaccia utente, eseguono l'override dell'autorità predefinita, specificano un nome client e una versione per i dati di telemetria, specificano un URI di reindirizzamento e specificano la factory HTTP da usare. La factory HTTP può essere usata, ad esempio, per gestire i proxy e per specificare dati di telemetria e registrazione. 

Nelle sezioni seguenti vengono fornite ulteriori informazioni sulla creazione di un'istanza dell'applicazione.

##### <a name="specify-the-parent-ui-window-or-activity"></a>Specificare l'interfaccia utente, la finestra o l'attività padre

In Android, è necessario passare l'attività padre prima di eseguire l'autenticazione interattiva. Su iOS, quando si utilizza un broker, `ViewController`è necessario pass-in . Allo stesso modo nella piattaforma UWP, è possibile passare nella finestra padre. Si passa quando si acquisisce il token. Tuttavia, quando si crea l'app, è anche possibile `UIParent`specificare un callback come delegato che restituisce .

```csharp
IPublicClientApplication application = PublicClientApplicationBuilder.Create(clientId)
  .ParentActivityOrWindowFunc(() => parentUi)
  .Build();
```

Su Android, si consiglia [`CurrentActivityPlugin`](https://github.com/jamesmontemagno/CurrentActivityPlugin)di utilizzare . Il `PublicClientApplication` codice del generatore risultante è simile all'esempio seguente:The resulting builder code looks like this example:

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

##### <a name="find-more-app-building-parameters"></a>Trova altri parametri per la creazione di app

Per un elenco di tutti `PublicClientApplicationBuilder`i metodi disponibili in , vedere l'elenco [Metodi](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods).

Per una descrizione di tutte `PublicClientApplicationOptions`le opzioni esposte in , vedere la [documentazione](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions)di riferimento .

## <a name="tasks-for-xamarin-ios"></a>Attività per Xamarin iOS

Se si utilizza MSAL.NET in Xamarin iOS, eseguire le attività seguenti.

* [Eseguire l'override e implementare la `OpenUrl` funzione in`AppDelegate`](msal-net-xamarin-ios-considerations.md#implement-openurl)
* [Abilitare i gruppi di portachiavi](msal-net-xamarin-ios-considerations.md#enable-keychain-access)
* [Abilitare la condivisione della cache dei tokenEnable token cache sharing](msal-net-xamarin-ios-considerations.md#enable-token-cache-sharing-across-ios-applications)
* [Abilitare l'accesso al portachiavi](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

Per altre informazioni, vedere [Considerazioni su Xamarin iOS](msal-net-xamarin-ios-considerations.md).

## <a name="tasks-for-msal-for-ios-and-macos"></a>Attività per MSAL per iOS e macOS

Queste attività sono necessarie quando si utilizza MSAL per iOS e macOS:

* [Implementare `openURL` il callback](#brokered-authentication-for-msal-for-ios-and-macos)
* [Abilitare i gruppi di accesso portachiavi](howto-v2-keychain-objc.md)
* [Personalizzare browser e visualizzazioni Web](customize-webviews.md)

## <a name="tasks-for-xamarinandroid"></a>Attività per Xamarin.Android

Se si utilizza Xamarin.Android, eseguire le attività seguenti:

- [Assicurarsi che il controllo torni a MSAL al termine della parte interattiva del flusso di autenticazioneEnsure control torna a MSAL dopo la fine della parte interattiva del flusso di autenticazioneEnsure control goes back to MSAL after the interactive portion of](msal-net-xamarin-android-considerations.md#ensure-that-control-returns-to-msal)
- [Aggiornare il manifesto AndroidUpdate the Android manifest](msal-net-xamarin-android-considerations.md#update-the-android-manifest)
- [Utilizzare la visualizzazione Web incorporata (facoltativo)](msal-net-xamarin-android-considerations.md#use-the-embedded-web-view-optional)
- [Risolvere i problemi in base alle esigenzeTroubleshoot as necessary](msal-net-xamarin-android-considerations.md#troubleshoot)

Per ulteriori informazioni, vedere [Considerazioni su Xamarin.Android](msal-net-xamarin-android-considerations.md).

Per considerazioni sui browser su Android, vedere Considerazioni specifiche di [Xamarin.Android con MSAL.NET](msal-net-system-browser-android-considerations.md).

#### <a name="tasks-for-uwp"></a>Attività per UWP

Nella piattaforma UWP è possibile utilizzare le reti aziendali. Nelle sezioni seguenti vengono illustrate le attività da completare nello scenario aziendale.

Per ulteriori informazioni, vedere [Considerazioni specifiche della piattaforma UWP con MSAL.NET](msal-net-uwp-considerations.md).

## <a name="configure-the-application-to-use-the-broker"></a>Configurare l'applicazione per l'utilizzo del broker

Su Android e iOS, i broker abilitano:

- **Single Sign-On (SSO):** è possibile usare SSO per i dispositivi registrati con Azure Active Directory (Azure AD). Quando si usa SSO, gli utenti non devono accedere a ogni applicazione.
- **Identificazione dei dispositivi:** questa impostazione abilita i criteri di accesso condizionale correlati ai dispositivi di Azure AD. Il processo di autenticazione usa il certificato del dispositivo creato quando il dispositivo è stato aggiunto all'area di lavoro.
- **Verifica dell'identificazione**dell'applicazione : Quando un'applicazione chiama il broker, passa il suo URL di reindirizzamento. Poi il broker lo verifica.

### <a name="enable-the-broker-on-xamarin"></a>Abilitare il broker su Xamarin

Per abilitare il broker su Xamarin, utilizzare il `WithBroker()` parametro quando si chiama il `PublicClientApplicationBuilder.CreateApplication` metodo . Per impostazione predefinita, `.WithBroker()` è impostato su true. 

Per abilitare l'autenticazione negoziata per Xamarin.iOS, seguire la procedura descritta nella [sezione Xamarin.iOS](#enable-brokered-authentication-for-xamarin-ios) in questo articolo.

### <a name="enable-the-broker-for-msal-for-android"></a>Abilitare il broker per MSAL per AndroidEnable the broker for MSAL for Android

Per informazioni sull'abilitazione di un broker su Android, vedere [Autenticazione negoziata su Android](brokered-auth.md). 

### <a name="enable-the-broker-for-msal-for-ios-and-macos"></a>Abilitare il broker per MSAL per iOS e macOS

L'autenticazione negoziata è abilitata per impostazione predefinita per gli scenari di Azure AD in MSAL per iOS e macOS.Brokered authentication is enabled by default for Azure AD scenarios in MSAL for iOS and macOS. 

Nelle sezioni seguenti vengono fornite istruzioni per configurare l'applicazione per il supporto dell'autenticazione negoziata per MSAL per Xamarin.iOS o MSAL per iOS e macOS. Nelle due serie di istruzioni, alcuni dei passaggi differiscono.

### <a name="enable-brokered-authentication-for-xamarin-ios"></a>Abilitare l'autenticazione negoziata per Xamarin iOSEnable brokered authentication for Xamarin iOS

Segui i passaggi in questa sezione per abilitare l'app Xamarin.iOS per parlare con l'app [Microsoft Authenticator.](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458)

#### <a name="step-1-enable-broker-support"></a>Passaggio 1: Abilitare il supporto del brokerStep 1: Enable broker support

Il supporto broker è disabilitato per impostazione predefinita. Attivarlo per una `PublicClientApplication` singola classe. Utilizzare `WithBroker()` il parametro `PublicClientApplication` quando `PublicClientApplicationBuilder`si crea la classe tramite . Il `WithBroker()` parametro è impostato su true per impostazione predefinita.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

#### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Passaggio 2: Aggiornare AppDelegate per gestire il callbackStep 2: Update AppDelegate to handle the callback

Quando MSAL.NET chiama il broker, il broker chiama quindi all'applicazione. Richiama utilizzando il `AppDelegate.OpenUrl` metodo . Poiché MSAL attende la risposta dal broker, l'applicazione deve cooperare per richiamare MSAL.NET. È possibile impostare questo `AppDelegate.cs` comportamento aggiornando il file per eseguire l'override del metodo, come illustrato nel codice seguente.

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

Questo metodo viene richiamato ogni volta che viene avviata l'applicazione. È un'opportunità per elaborare la risposta dal broker e completare il processo di autenticazione avviato MSAL.NET.

#### <a name="step-3-set-a-uiviewcontroller"></a>Passaggio 3: Impostare un UIViewController()

Per Xamarin iOS, in genere non è necessario impostare una finestra oggetto. Ma in questo caso è necessario impostarlo in modo da poter inviare e ricevere risposte da un broker. Per impostare una `AppDelegate.cs`finestra oggetto, `ViewController`in , impostare un oggetto .

Per impostare la finestra dell'oggetto, attenersi alla seguente procedura:

1. In `AppDelegate.cs`, `App.RootViewController` impostare `UIViewController()`la proprietà su un nuovo file . Questa impostazione assicura che la `UIViewController`chiamata al broker includa . Se non è impostato correttamente, è possibile che venga visualizzato questo errore:If it isn't set correctly, you might get this error:

    `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker."`

1. Alla `AcquireTokenInteractive` chiamata, `.WithParentActivityOrWindow(App.RootViewController)`utilizzare . Passare il riferimento alla finestra dell'oggetto che verrà utilizzata. Ad esempio:

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
    
#### <a name="step-4-register-a-url-scheme"></a>Passaggio 4: Registrare uno schema URLStep 4: Register a URL scheme

MSAL.NET utilizza gli URL per richiamare il broker e quindi restituire la risposta del broker all'app. Per completare il round trip, registra lo `Info.plist` schema URL dell'app nel file. 

Per registrare lo schema URL dell'app, procedi nel seguente modo:

1. `CFBundleURLSchemes` Prefisso `msauth`con . 
1. Aggiungere `CFBundleURLName` alla fine. Seguire questo modello:Follow this pattern: 

   `$"msauth.(BundleId)"`

   In `BundleId` questo caso, identifica in modo univoco il dispositivo. Ad esempio, `BundleId` `yourcompany.xforms`se è , `msauth.com.yourcompany.xforms`lo schema URL è .
    
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
    
#### <a name="step-5-add-to-the-lsapplicationqueriesschemes-section"></a>Passaggio 5: Aggiungere alla sezione LSApplicationQueriesSchemes

MSAL `–canOpenURL:` utilizza per verificare se il broker è installato nel dispositivo. In iOS 9, Apple ha bloccato gli schemi per cui un'applicazione può eseguire query.

Aggiungere `msauthv2` alla `LSApplicationQueriesSchemes` sezione `Info.plist` del file, come nell'esempio di codice seguente:

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="brokered-authentication-for-msal-for-ios-and-macos"></a>Autenticazione negoziata per MSAL per iOS e macOS

L'autenticazione negoziata è abilitata per impostazione predefinita per gli scenari di Azure AD.

#### <a name="step-1-update-appdelegate-to-handle-the-callback"></a>Passaggio 1: Aggiornare AppDelegate per gestire il callbackStep 1: Update AppDelegate to handle the callback

Quando MSAL per iOS e macOS chiama il broker, il `openURL` broker richiama l'applicazione utilizzando il metodo . Poiché MSAL attende la risposta dal broker, l'applicazione deve cooperare per richiamare MSAL. Impostare questa funzionalità aggiornando il `AppDelegate.m` file per eseguire l'override del metodo, come illustrato negli esempi di codice seguenti.

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
> Se è `UISceneDelegate` stato adottato su iOS 13 o versione `scene:openURLContexts:` `UISceneDelegate` successiva, inserire il callback MSAL in instead. MSAL `handleMSALResponse:sourceApplication:` deve essere chiamato una sola volta per ogni URL.
>
> Per ulteriori informazioni, vedere la [documentazione Apple](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc).

#### <a name="step-2-register-a-url-scheme"></a>Passaggio 2: Registrare uno schema URLStep 2: Register a URL scheme

MSAL per iOS e macOS usa gli URL per richiamare il broker e quindi restituire la risposta del broker all'app. Per completare il round trip, registra uno `Info.plist` schema URL per la tua app nel file.

Per registrare uno schema per la tua app: 

1. Anteporre a un `msauth`prefisso dello schema URL personalizzato con . 

1. Aggiungere l'identificatore del bundle alla fine dello schema. Seguire questo modello:Follow this pattern: 

   `$"msauth.(BundleId)"`

   In `BundleId` questo caso, identifica in modo univoco il dispositivo. Ad esempio, `BundleId` `yourcompany.xforms`se è , `msauth.com.yourcompany.xforms`lo schema URL è .
  
   > [!NOTE]
   > Questo schema URL diventerà parte dell'URI di reindirizzamento che identifica in modo univoco l'app quando riceve la risposta del broker. Assicurarsi che l'URI `msauth.(BundleId)://auth` di reindirizzamento nel formato sia registrato per l'applicazione nel portale di [Azure.](https://portal.azure.com)
  
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

#### <a name="step-3-add-lsapplicationqueriesschemes"></a>Passaggio 3: Aggiungere LSApplicationQueriesSchemesStep 3: Add LSApplicationQueriesSchemes

Aggiungi `LSApplicationQueriesSchemes` per consentire le chiamate all'app Microsoft Authenticator, se installata.

> [!NOTE]
> Lo `msauthv3` schema è necessario quando l'app viene compilata usando Xcode 11 e versioni successive. 

Ecco un esempio di come `LSApplicationQueriesSchemes`aggiungere:

```XML 
<key>LSApplicationQueriesSchemes</key>
<array>
  <string>msauthv2</string>
  <string>msauthv3</string>
</array>
```

### <a name="brokered-authentication-for-xamarinandroid"></a>Autenticazione negoziata per Xamarin.Android

MSAL.NET non supporta i broker per Android.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Acquisizione di un token](scenario-mobile-acquire-token.md)
