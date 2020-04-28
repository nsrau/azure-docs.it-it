---
title: Usare i broker con Novell iOS & Android | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come configurare le applicazioni Novell iOS che possono usare Microsoft Authenticator e Microsoft Authentication Library per .NET (MSAL.NET). Viene inoltre illustrato come eseguire la migrazione da Autenticazione di Azure AD Library for .NET (ADAL.NET) a Microsoft Authentication Library for .NET (MSAL.NET).
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79262789"
---
# <a name="use-microsoft-authenticator-or-intune-company-portal-on-xamarin-applications"></a>Usare Microsoft Authenticator o Portale aziendale Intune nelle applicazioni Novell

In Android e iOS, i broker come Microsoft Authenticator e Microsoft Intune specifici di Android Portale aziendale abilitano:

- **Single Sign-on (SSO)**: gli utenti non devono accedere a ogni applicazione.
- **Identificazione del dispositivo**: il broker accede al certificato del dispositivo. Questo certificato viene creato nel dispositivo quando viene aggiunto all'area di lavoro.
- **Verifica dell'identificazione dell'applicazione**: quando un'applicazione chiama il broker, passa l'URL di reindirizzamento. Il Broker verifica l'URL.

Per abilitare una di queste funzionalità, usare il `WithBroker()` parametro quando si chiama il `PublicClientApplicationBuilder.CreateApplication` metodo. Per `.WithBroker()` impostazione predefinita, il parametro è impostato su true. 

Usare anche le istruzioni riportate nelle sezioni seguenti per configurare l'autenticazione negoziata per le applicazioni [iOS](#brokered-authentication-for-ios) o [Android](#brokered-authentication-for-android) .

## <a name="brokered-authentication-for-ios"></a>Autenticazione negoziata per iOS

Usare la procedura seguente per abilitare l'app Novell. iOS per comunicare con l'app [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) .

### <a name="step-1-enable-broker-support"></a>Passaggio 1: abilitare il supporto broker
È necessario abilitare il supporto del broker per le `PublicClientApplication`singole istanze di. Il supporto è disabilitato per impostazione predefinita. Quando si crea `PublicClientApplication` tramite `PublicClientApplicationBuilder`, usare il `WithBroker()` parametro come illustrato nell'esempio riportato di seguito. Per `WithBroker()` impostazione predefinita, il parametro è impostato su true.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>Passaggio 2: abilitare l'accesso Keychain

Per abilitare l'accesso keychain, è necessario disporre di un gruppo di accesso a Keychain per l'applicazione. Quando si crea l' `WithIosKeychainSecurityGroup()` applicazione, è possibile usare l'API per impostare il gruppo di accesso a Keychain:

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
      
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Per altre informazioni, vedere [Enable Keychain Access](msal-net-xamarin-ios-considerations.md#enable-keychain-access).

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Passaggio 3: aggiornare AppDelegate per gestire il callback
Quando Microsoft Authentication Library per .NET (MSAL.NET) chiama il broker, il broker richiama l'applicazione tramite il `OpenUrl` metodo della `AppDelegate` classe. Poiché MSAL attende la risposta del broker, l'applicazione deve collaborare per chiamare MSAL.NET. Per abilitare questa collaborazione, aggiornare il `AppDelegate.cs` file in modo che esegua l'override del metodo riportato di seguito.

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

Questo metodo viene richiamato ogni volta che l'applicazione viene avviata. Viene usato come opportunità per elaborare la risposta dal broker e completare il processo di autenticazione avviato da MSAL.NET.

### <a name="step-4-set-uiviewcontroller"></a>Passaggio 4: impostare UIViewController ()
Sempre nel `AppDelegate.cs` file è necessario impostare una finestra degli oggetti. In genere, per Novell iOS non è necessario impostare la finestra degli oggetti. Tuttavia è necessaria una finestra oggetto per inviare e ricevere risposte dal broker. 

Per configurare la finestra oggetto: 
1. Nel `AppDelegate.cs` file impostare `App.RootViewController` su un nuovo. `UIViewController()` Questa assegnazione garantisce che la chiamata al broker includa `UIViewController`. Se questa impostazione viene assegnata in modo errato, è possibile che venga ricevuto questo errore:

      `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`

1. Nella `AcquireTokenInteractive` chiamata usare `.WithParentActivityOrWindow(App.RootViewController)` e quindi passare il riferimento alla finestra oggetto che verrà usata.

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

### <a name="step-5-register-a-url-scheme"></a>Passaggio 5: registrare uno schema URL
MSAL.NET usa gli URL per richiamare il broker e quindi restituisce la risposta del broker all'app. Per completare la round trip, registrare uno schema URL per l'app nel `Info.plist` file.

Il `CFBundleURLSchemes` nome deve includere `msauth.` come prefisso. Seguire il prefisso con `CFBundleURLName`. 

Nello schema URL, `BundleId` identifica in modo univoco l'app: `$"msauth.(BundleId)"`. Quindi, `BundleId` se `com.yourcompany.xforms`è, lo schema dell'URL `msauth.com.yourcompany.xforms`è.

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

### <a name="step-6-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>Passaggio 6: aggiungere l'identificatore di Service Broker alla sezione LSApplicationQueriesSchemes

MSAL USA `–canOpenURL:` per verificare se il broker è installato nel dispositivo. In iOS 9, Apple ha bloccato gli schemi per i quali un'applicazione può eseguire una query. 

Aggiungere `msauthv2` alla `LSApplicationQueriesSchemes` sezione del `Info.plist` file, come nell'esempio seguente:

```XML
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
    </array>
```

### <a name="step-7-register-your-redirect-uri-in-the-application-portal"></a>Passaggio 7: registrare l'URI di reindirizzamento nel portale applicazioni

Quando si usa Service Broker, l'URI di reindirizzamento presenta un requisito aggiuntivo. L'URI di Reindirizzamento _deve_ avere il formato seguente:

```csharp
$"msauth.{BundleId}://auth"
```

Ad esempio:

```csharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```

Si noti che l'URI di Reindirizzamento `CFBundleURLSchemes` corrisponde al nome incluso nel `Info.plist` file.

### <a name="step-8-make-sure-the-redirect-uri-is-registered-with-your-app"></a>Passaggio 8: assicurarsi che l'URI di reindirizzamento sia registrato con l'app

L'URI di reindirizzamento deve essere registrato nel [portale di registrazione delle app](https://portal.azure.com) come URI di reindirizzamento valido per l'applicazione. 

Il portale di registrazione delle app offre una nuova esperienza che consente di calcolare l'URI di risposta negoziata dall'ID bundle. 

Per calcolare l'URI di reindirizzamento:

1. Nel portale di registrazione delle app scegliere **autenticazione** > **provare la nuova esperienza**.

   ![Prova la nuova esperienza di registrazione delle app](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

1. Selezionare **Aggiungi una piattaforma**.

   ![Aggiungere una piattaforma](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

1. Quando l'elenco delle piattaforme è supportato, selezionare **iOS**.

   ![Configurare iOS](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

1. Immettere l'ID bundle come richiesto e quindi selezionare **Configure (Configura**).

   ![Immettere l'ID bundle](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

Al termine della procedura, l'URI di reindirizzamento viene calcolato per l'utente.

![Copia URI di Reindirizzamento](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>Autenticazione negoziata per Android

### <a name="step-1-enable-broker-support"></a>Passaggio 1: abilitare il supporto broker

Il supporto del broker è abilitato per ogni singolo PublicClientApplication. È disabilitata per impostazione predefinita. Usare il `WithBroker()` parametro (impostato su true per impostazione predefinita) quando si `IPublicClientApplication` crea l' `PublicClientApplicationBuilder`oggetto tramite l'oggetto.

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid) //(see step 4 below)
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Passaggio 2: aggiornare AppDelegate per gestire il callback

Quando MSAL.NET chiama il broker, il broker chiamerà a sua volta l'applicazione con il metodo OnActivityResult (). Poiché MSAL attenderà la risposta dal broker, l'applicazione deve instradare il risultato a MSAL.NET.
Questa operazione può essere eseguita instradando il risultato `SetAuthenticationContinuationEventArgs(int requestCode, Result resultCode, Intent data)` a eseguendo l'override del metodo OnActivityResult () come illustrato di seguito.

```CSharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
   base.OnActivityResult(requestCode, resultCode, data);
   AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

Questo metodo viene richiamato ogni volta che l'applicazione Broker viene avviata e viene usata come opportunità per elaborare la risposta dal broker e completare il processo di autenticazione avviato da MSAL.NET.

### <a name="step-3-set-an-activity"></a>Passaggio 3: impostare un'attività

Per il funzionamento dell'autenticazione negoziata è necessario impostare un'attività in modo che MSAL possa inviare e ricevere la risposta da Service Broker.

A tale scopo, è necessario fornire l'attività (in genere MainActivity) a `WithParentActivityOrWindow(object parent)` come oggetto padre. 

**Ad esempio:**

Nella chiamata al token di acquisizione:

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow((Activity)context))
             .ExecuteAsync();
```

### <a name="step-4-register-your-redirecturi-in-the-application-portal"></a>Passaggio 4: registrare i RedirectUri nel portale applicazioni

MSAL usa gli URL per richiamare il broker e quindi tornare all'app. Per completare questa round trip, è necessario registrare uno schema URL per l'app. Questo URI di reindirizzamento deve essere registrato nel portale di registrazione delle app Azure AD come URI di reindirizzamento valido per l'applicazione.


L'URI di reindirizzamento necessario per l'applicazione dipende dal certificato usato per firmare l'APK.

```
Example: msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=
```

L'ultima parte dell'URI, `hgbUYHVBYUTvuvT&Y6tr554365466=`, è la firma con cui l'apk è firmato, con codifica Base64.
Tuttavia, durante la fase di sviluppo dell'applicazione con Visual Studio, se si esegue il debug del codice senza firmare l'apk con un certificato specifico, Visual Studio firmerà il file apk per l'utente a scopo di debug, assegnando all'APK una firma univoca per il computer su cui è basata. Pertanto, ogni volta che si compila l'app in un computer diverso, è necessario aggiornare l'URI di reindirizzamento nel codice dell'applicazione e la registrazione dell'applicazione nella portale di Azure per eseguire l'autenticazione con MSAL. 

Durante il debug, è possibile che si verifichi un'eccezione MSAL (o un messaggio di log) indicante che l'URI di reindirizzamento specificato non è corretto. **Questa eccezione fornirà anche l'URI di reindirizzamento che è necessario usare** con il computer corrente in cui si esegue il debug. È possibile utilizzare questo URI di reindirizzamento per continuare a sviluppare per il tempo.

Quando si è pronti a finalizzare il codice, assicurarsi di aggiornare l'URI di reindirizzamento nel codice e la registrazione dell'applicazione nella portale di Azure per usare la firma del certificato con cui verrà firmato l'APK.

In pratica, ciò significa che è necessario registrare un URI di reindirizzamento per ogni membro del team, oltre a un URI di reindirizzamento per la versione con firma di produzione dell'APK.

È anche possibile calcolare la firma autonomamente, in modo analogo a come MSAL: 

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

Si ha anche la possibilità di acquisire la firma per il pacchetto usando lo strumento di tastiera con i comandi seguenti:

Per Windows: `keytool.exe -list -v -keystore "%LocalAppData%\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android`

Per Mac:`keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64`

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle [considerazioni relative all'uso di piattaforma UWP (Universal Windows Platform) con MSAL.NET](msal-net-uwp-considerations.md).
