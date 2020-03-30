---
title: Utilizzare i broker con Xamarin iOS & Android Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come configurare applicazioni Xamarin iOS che possono usare Microsoft Authenticator e Microsoft Authentication Library per .NET (MSAL.NET). Informazioni su come eseguire la migrazione da Libreria di autenticazione di Azure AD per .NET (ADAL.NET) a Microsoft Authentication Library for .NET (MSAL.NET).
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/08/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1a57173311278c5e3e0304aeb12d4d6999379eb5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262789"
---
# <a name="use-microsoft-authenticator-or-intune-company-portal-on-xamarin-applications"></a>Usare Microsoft Authenticator o Intune Company Portal nelle applicazioni XamarinUse Microsoft Authenticator or Intune Company Portal on Xamarin applications

In Android e iOS, broker come Microsoft Authenticator e il portale aziendale di Microsoft Intune specifico di Android consentono di abilitare:On Android and iOS, brokers like Microsoft Authenticator and the Android-specific Microsoft Intune Company Portal enable:

- **Single Sign-On (SSO):** gli utenti non devono accedere a ogni applicazione.
- **Identificazione del dispositivo**: Il broker accede al certificato del dispositivo. Questo certificato viene creato nel dispositivo quando viene aggiunto all'area di lavoro.
- **Verifica dell'identificazione**dell'applicazione : Quando un'applicazione chiama il broker, passa il suo URL di reindirizzamento. Il broker verifica l'URL.

Per abilitare una di `WithBroker()` queste funzionalità, `PublicClientApplicationBuilder.CreateApplication` utilizzare il parametro quando si chiama il metodo . Il `.WithBroker()` parametro è impostato su true per impostazione predefinita. 

Usare anche le istruzioni nelle sezioni seguenti per configurare l'autenticazione negoziata per le applicazioni iOS o [Android.Also](#brokered-authentication-for-android) use the instructions in the following sections to set up brokered authentication for [iOS](#brokered-authentication-for-ios) applications or Android applications.

## <a name="brokered-authentication-for-ios"></a>Autenticazione negoziata per iOSBrokered authentication for iOS

Segui la procedura seguente per abilitare l'app Xamarin.iOS a comunicare con l'app [Microsoft Authenticator.](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458)

### <a name="step-1-enable-broker-support"></a>Passaggio 1: Abilitare il supporto del brokerStep 1: Enable broker support
È necessario abilitare il `PublicClientApplication`supporto broker per singole istanze di . Il supporto è disabilitato per impostazione predefinita. Quando si `PublicClientApplication` `PublicClientApplicationBuilder`crea tramite , utilizzare il `WithBroker()` parametro come illustrato nell'esempio seguente. Il `WithBroker()` parametro è impostato su true per impostazione predefinita.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>Passaggio 2: attiva l'accesso al portachiavi

Per abilitare l'accesso portachiavi, è necessario disporre di un gruppo di accesso portachiavi per l'applicazione. È possibile `WithIosKeychainSecurityGroup()` utilizzare l'API per impostare il gruppo di accesso portachiavi quando si crea l'applicazione:You can use the API to set your keychain access group when you create your application:

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
      
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Per ulteriori informazioni, consultate [Abilitare l'accesso al portachiavi.](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Passaggio 3: Aggiornare AppDelegate per gestire il callbackStep 3: Update AppDelegate to handle the callback
Quando Microsoft Authentication Library for .NET (MSAL.NET) chiama il broker, `OpenUrl` il `AppDelegate` broker richiama l'applicazione tramite il metodo della classe. Poiché MSAL attende la risposta del broker, l'applicazione deve cooperare per richiamare MSAL.NET. Per abilitare questa `AppDelegate.cs` cooperazione, aggiornare il file per eseguire l'override del metodo seguente.

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

Questo metodo viene richiamato ogni volta che viene avviata l'applicazione. Viene utilizzato come opportunità per elaborare la risposta dal broker e completare il processo di autenticazione avviato MSAL.NET.

### <a name="step-4-set-uiviewcontroller"></a>Passaggio 4: Impostare UIViewController()
Sempre nel `AppDelegate.cs` file, è necessario impostare una finestra oggetto. In genere, per Xamarin iOS non è necessario impostare la finestra dell'oggetto. Ma è necessaria una finestra oggetto per inviare e ricevere risposte dal broker. 

Per impostare la finestra dell'oggetto: 
1. Nel `AppDelegate.cs` file, `App.RootViewController` impostare `UIViewController()`su un nuovo file . Questa assegnazione garantisce che la `UIViewController`chiamata al broker includa . Se questa impostazione viene assegnata in modo non corretto, è possibile che venga visualizzato questo errore:If this setting is assigned incorrectly, you might get this error:

      `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`

1. Nella `AcquireTokenInteractive` chiamata, `.WithParentActivityOrWindow(App.RootViewController)` utilizzare e quindi passare il riferimento alla finestra dell'oggetto che verrà utilizzata.

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

### <a name="step-5-register-a-url-scheme"></a>Passaggio 5: Registrare uno schema URLStep 5: Register a URL scheme
MSAL.NET utilizza gli URL per richiamare il broker e quindi restituire la risposta del broker all'app. Per completare il round trip, registra uno `Info.plist` schema URL per la tua app nel file.

Il `CFBundleURLSchemes` nome `msauth.` deve essere incluso come prefisso. Seguire il `CFBundleURLName`prefisso con . 

Nello schema URL, `BundleId` identifica in `$"msauth.(BundleId)"`modo univoco l'app: . Quindi, `BundleId` `com.yourcompany.xforms`se è , `msauth.com.yourcompany.xforms`allora lo schema URL è .

> [!NOTE]
> Questo schema URL diventa parte dell'URI di reindirizzamento che identifica in modo univoco l'app quando riceve la risposta dal broker.

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

### <a name="step-6-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>Passaggio 6: Aggiungere l'identificatore del broker alla sezione LSApplicationQueriesSchemes

MSAL `–canOpenURL:` utilizza per verificare se il broker è installato nel dispositivo. In iOS 9, Apple ha bloccato gli schemi per cui un'applicazione può eseguire query. 

Aggiungere `msauthv2` alla `LSApplicationQueriesSchemes` sezione `Info.plist` del file, come nell'esempio seguente:

```XML
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
    </array>
```

### <a name="step-7-register-your-redirect-uri-in-the-application-portal"></a>Passaggio 7: Registrare l'URI di reindirizzamento nel portale dell'applicazioneStep 7: Register your redirect URI in the application portal

Quando si utilizza il broker, l'URI di reindirizzamento ha un requisito aggiuntivo. L'URI di reindirizzamento deve avere il formato seguente:The redirect URI _must_ have the following format:

```csharp
$"msauth.{BundleId}://auth"
```

Ad esempio:

```csharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```

Si noti che `CFBundleURLSchemes` l'URI di `Info.plist` reindirizzamento corrisponde al nome incluso nel file.

### <a name="step-8-make-sure-the-redirect-uri-is-registered-with-your-app"></a>Passaggio 8: assicurati che l'URI di reindirizzamento sia registrato nell'app

L'URI di reindirizzamento deve essere registrato nel portale di [registrazione dell'app](https://portal.azure.com) come URI di reindirizzamento valido per l'applicazione. 

Il portale di registrazione app offre una nuova esperienza che consente di calcolare l'URI di risposta negoziato dall'ID del bundle. 

Per calcolare l'URI di reindirizzamento:

1. Nel portale di registrazione app scegliere **Authentication** > **Try out the new experience**.

   ![Prova la nuova esperienza di registrazione dell'app](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

1. Selezionare **Aggiungi una piattaforma**.

   ![Aggiungere una piattaformaAdd a platform](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

1. Quando l'elenco delle piattaforme è supportato, selezionare **iOS**.

   ![Configurare iOS](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

1. Immettere l'ID del bundle come richiesto e quindi **selezionare Configura**.

   ![Immettere l'ID del pacchetto](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

Al termine dei passaggi, l'URI di reindirizzamento viene calcolato automaticamente.

![Copia URI di reindirizzamento](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>Autenticazione negoziata per Android

### <a name="step-1-enable-broker-support"></a>Passaggio 1: Abilitare il supporto del brokerStep 1: Enable broker support

Il supporto del gestore di broker è abilitato in base a PublicClientApplication.Broker support is enabled on a per-PublicClientApplication. È disabilitata per impostazione predefinita. Utilizzare `WithBroker()` il parametro (impostato su true `IPublicClientApplication` per `PublicClientApplicationBuilder`impostazione predefinita) durante la creazione dell'oggetto tramite il parametro .

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid) //(see step 4 below)
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Passaggio 2: Aggiornare AppDelegate per gestire il callbackStep 2: Update AppDelegate to handle the callback

Quando MSAL.NET chiama il broker, il broker, a sua volta, richiama l'applicazione con il metodo OnActivityResult(). Poiché MSAL attenderà la risposta dal broker, l'applicazione deve instradare il risultato a MSAL.NET.
Ciò può essere ottenuto `SetAuthenticationContinuationEventArgs(int requestCode, Result resultCode, Intent data)` instradando il risultato al eseguendo l'override del metodo OnActivityResult(), come illustrato di seguito

```CSharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
   base.OnActivityResult(requestCode, resultCode, data);
   AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

Questo metodo viene richiamato ogni volta che l'applicazione broker viene avviata e viene utilizzato come un'opportunità per elaborare la risposta dal broker e completare il processo di autenticazione avviato da MSAL.NET.

### <a name="step-3-set-an-activity"></a>Passaggio 3: Impostare un'attivitàStep 3: Set an Activity

Affinché l'autenticazione negoziata funzioni, è necessario impostare un'attività in modo che MSAL possa inviare e ricevere la risposta dal broker.

A tale scopo, è necessario fornire l'attività (in `WithParentActivityOrWindow(object parent)` genere il MainActivity) per il come l'oggetto padre. 

**Per esempio:**

Nella chiamata Acquisisci token:

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow((Activity)context))
             .ExecuteAsync();
```

### <a name="step-4-register-your-redirecturi-in-the-application-portal"></a>Passaggio 4: Registrare il RedirectUri nel portale dell'applicazioneStep 4: Register your RedirectUri in the application portal

MSAL usa gli URL per richiamare il broker e quindi tornare all'app. Per completare il round trip, devi registrare uno schema URL per la tua app. Questo URI di reindirizzamento deve essere registrato nel portale di registrazione dell'app Azure AD come URI di reindirizzamento valido per l'applicazione.


L'URI di reindirizzamento necessario per l'applicazione dipende dal certificato utilizzato per firmare l'APK.

```
Example: msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=
```

L'ultima parte dell'URI, `hgbUYHVBYUTvuvT&Y6tr554365466=`, è la firma con cui l'APK è firmato con codifica in base64.
Tuttavia, durante la fase di sviluppo dell'applicazione utilizzando Visual Studio, se si esegue il debug del codice senza firmare l'apk con un certificato specifico, Visual Studio firmerà l'apk per il debug, assegnando all'APK una firma univoca per macchina su cui è costruito. Pertanto, ogni volta che si compila l'app in un computer diverso, è necessario aggiornare l'URI di reindirizzamento nel codice dell'applicazione e la registrazione dell'applicazione nel portale di Azure per eseguire l'autenticazione con MSAL. 

Durante il debug, è possibile riscontrare un'eccezione MSAL (o un messaggio di log) che indica che l'URI di reindirizzamento fornito non è corretto. **Questa eccezione fornirà anche l'URI** di reindirizzamento che è necessario utilizzare con il computer corrente su cui si sta eseguendo il debug. È possibile utilizzare questo URI di reindirizzamento per continuare lo sviluppo per il momento.

Quando si è pronti a finalizzare il codice, assicurarsi di aggiornare l'URI di reindirizzamento nel codice e nella registrazione dell'applicazione nel portale di Azure per usare la firma del certificato con cui firmare l'APK.

In pratica, ciò significa che è necessario registrare un URI di reindirizzamento per ogni membro del team, oltre a un URI di reindirizzamento per la versione firmata di produzione dell'APK.

È anche possibile calcolare questa firma manualmente, in modo simile a come MSAL esegue questa operazione:You can also compute this signature yourself, similar to how MSAL does it: 

```CSharp
   private string GetRedirectUriForBroker()
   {
      string packageName = Application.Context.PackageName;
      string signatureDigest = this.GetCurrentSignatureForPackage(packageName);
      if (!string.IsNullOrEmpty(signatureDigest))
      {
            return string.Format(CultureInfo.InvariantCulture, "{0}://{1}/{2}", RedirectUriScheme,
               packageName.ToLowerInvariant(), signatureDigest);
      }

      return string.Empty;
   }

   private string GetCurrentSignatureForPackage(string packageName)
   {
            PackageInfo info = Application.Context.PackageManager.GetPackageInfo(packageName,
               PackageInfoFlags.Signatures);
            if (info != null && info.Signatures != null && info.Signatures.Count > 0)
            {
               // First available signature. Applications can be signed with multiple signatures.
               // The order of Signatures is not guaranteed.
               Signature signature = info.Signatures[0];
               MessageDigest md = MessageDigest.GetInstance("SHA");
               md.Update(signature.ToByteArray());
               return Convert.ToBase64String(md.Digest(), Base64FormattingOptions.None);
               // Server side needs to register all other tags. ADAL will
               // send one of them.
            }
   }
```

Hai anche la possibilità di acquisire la firma per il tuo pacchetto utilizzando il keytool con i seguenti comandi:

Per Windows: `keytool.exe -list -v -keystore "%LocalAppData%\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android`

Per Mac:`keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64`

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle [considerazioni sull'utilizzo](msal-net-uwp-considerations.md)della piattaforma Windows universale con MSAL.NET .
