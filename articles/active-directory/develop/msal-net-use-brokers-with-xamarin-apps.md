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
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 7fa13a328a55b0e9eaa546e70bf0711f4f011cf1
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96173431"
---
# <a name="use-microsoft-authenticator-or-intune-company-portal-on-xamarin-applications"></a>Usare Microsoft Authenticator o Portale aziendale Intune nelle applicazioni Novell

In Android e iOS, i broker come Microsoft Authenticator e Microsoft Intune specifici di Android Portale aziendale abilitano:

- **Single Sign-on (SSO)**: gli utenti non devono accedere a ogni applicazione.
- **Identificazione del dispositivo**: il broker accede al certificato del dispositivo. Questo certificato viene creato nel dispositivo quando viene aggiunto all'area di lavoro.
- **Verifica dell'identificazione dell'applicazione**: quando un'applicazione chiama il broker, passa l'URL di reindirizzamento. Il Broker verifica l'URL.

Per abilitare una di queste funzionalità, usare il `WithBroker()` parametro quando si chiama il `PublicClientApplicationBuilder.CreateApplication` metodo. Per `.WithBroker()` impostazione predefinita, il parametro è impostato su true.

La configurazione dell'autenticazione negoziata in Microsoft Authentication Library per .NET (MSAL.NET) varia a seconda della piattaforma:

* [Applicazioni iOS](#brokered-authentication-for-ios)
* [Applicazioni Android](#brokered-authentication-for-android)

## <a name="brokered-authentication-for-ios"></a>Autenticazione negoziata per iOS

Usare la procedura seguente per abilitare l'app Novell. iOS per la comunicazione con l'app [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) . Se la destinazione è iOS 13, provare a leggere le informazioni sulle modifiche apportate all' [API di Apple](./msal-net-xamarin-ios-considerations.md).

### <a name="step-1-enable-broker-support"></a>Passaggio 1: abilitare il supporto broker

È necessario abilitare il supporto del broker per le singole istanze di `PublicClientApplication` . Il supporto è disabilitato per impostazione predefinita. Quando si crea `PublicClientApplication` tramite `PublicClientApplicationBuilder` , usare il `WithBroker()` parametro come illustrato nell'esempio riportato di seguito. Per `WithBroker()` impostazione predefinita, il parametro è impostato su true.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>Passaggio 2: abilitare l'accesso Keychain

Per abilitare l'accesso keychain, è necessario disporre di un gruppo di accesso a Keychain per l'applicazione. `WithIosKeychainSecurityGroup()`Quando si crea l'applicazione, è possibile usare l'API per impostare il gruppo di accesso a Keychain:

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Per altre informazioni, vedere [Enable Keychain Access](msal-net-xamarin-ios-considerations.md#enable-keychain-access).

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Passaggio 3: aggiornare AppDelegate per gestire il callback

Quando MSAL.NET chiama il broker, il broker richiama l'applicazione tramite il `OpenUrl` metodo della `AppDelegate` classe. Poiché MSAL attende la risposta del broker, l'applicazione deve collaborare per chiamare MSAL.NET. Per abilitare questa collaborazione, aggiornare il file *AppDelegate.cs* per eseguire l'override del metodo seguente.

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

Sempre nel file *AppDelegate.cs* , è necessario impostare una finestra degli oggetti. Non è in genere necessario impostare la finestra degli oggetti per Novell iOS, ma è necessaria una finestra oggetto per inviare e ricevere risposte dal broker.

Per configurare la finestra oggetto:

1. Nel file *AppDelegate.cs* impostare `App.RootViewController` su un nuovo `UIViewController()` . Questa assegnazione garantisce che la chiamata al broker includa `UIViewController` . Se questa impostazione viene assegnata in modo errato, è possibile che venga ricevuto questo errore:

      `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`

1. Nella `AcquireTokenInteractive` chiamata usare `.WithParentActivityOrWindow(App.RootViewController)` e quindi passare il riferimento alla finestra oggetto che verrà usata.

    In *app.cs*:

    ```csharp
       public static object RootViewController { get; set; }
    ```

    In *AppDelegate.cs*:

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

MSAL.NET usa gli URL per richiamare il broker e quindi restituisce la risposta del broker all'app. Per completare la round trip, registrare uno schema URL per l'app nel file *info. plist* .

Il `CFBundleURLSchemes` nome deve includere `msauth.` come prefisso. Seguire il prefisso con `CFBundleURLName` .

Nello schema URL, `BundleId` identifica in modo univoco l'app: `$"msauth.(BundleId)"` . Quindi `BundleId` , se è `com.yourcompany.xforms` , lo schema dell'URL è `msauth.com.yourcompany.xforms` .

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

Aggiungere `msauthv2` alla `LSApplicationQueriesSchemes` sezione del file *info. plist* , come nell'esempio seguente:

```XML
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
    </array>
```

### <a name="step-7-add-a-redirect-uri-to-your-app-registration"></a>Passaggio 7: aggiungere un URI di reindirizzamento alla registrazione dell'app

Quando si usa Service Broker, l'URI di reindirizzamento presenta un requisito aggiuntivo. L'URI di Reindirizzamento _deve_ avere il formato seguente:

```csharp
$"msauth.{BundleId}://auth"
```

Ecco un esempio:

```csharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth";
```

Si noti che l'URI di reindirizzamento corrisponde al `CFBundleURLSchemes` nome incluso nel file *info. plist* .

Aggiungere l'URI di reindirizzamento alla registrazione dell'app nel [portale di Azure](https://portal.azure.com). Per generare un URI di reindirizzamento formattato correttamente, usare **registrazioni app** nel portale di Azure per generare l'URI di reindirizzamento negoziato dall'ID bundle.

**Per generare l'URI di reindirizzamento:**

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare **Azure Active Directory**  >  **registrazioni app** > app registrata
1. Selezionare **autenticazione**  >  **aggiungere una piattaforma**  >  **iOS/MacOS**
1. Immettere l'ID bundle, quindi selezionare **Configura**.

    Copiare l'URI di reindirizzamento generato visualizzato nella casella di testo **URI di reindirizzamento** per l'inclusione nel codice:

    :::image type="content" source="media/msal-net-use-brokers-with-xamarin-apps/portal-01-ios-platform-settings.png" alt-text="impostazioni della piattaforma iOS con URI di reindirizzamento generato in portale di Azure":::
1. Selezionare **fine** per completare la generazione dell'URI di reindirizzamento.

## <a name="brokered-authentication-for-android"></a>Autenticazione negoziata per Android

### <a name="step-1-enable-broker-support"></a>Passaggio 1: abilitare il supporto broker

Il supporto di Service Broker è abilitato in `PublicClientApplication` base al criterio. È disabilitata per impostazione predefinita. Usare il `WithBroker()` parametro (impostato su true per impostazione predefinita) quando si crea l'oggetto `IPublicClientApplication` tramite l'oggetto `PublicClientApplicationBuilder` .

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid) // See step #4
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Passaggio 2: aggiornare AppDelegate per gestire il callback

Quando MSAL.NET chiama il broker, il broker chiamerà a sua volta l'applicazione con il `OnActivityResult()` metodo. Poiché MSAL attenderà la risposta dal broker, l'applicazione deve instradare il risultato a MSAL.NET.

Instradare il risultato al `SetAuthenticationContinuationEventArgs(int requestCode, Result resultCode, Intent data)` metodo eseguendo l'override del `OnActivityResult()` metodo come illustrato di seguito:

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
   base.OnActivityResult(requestCode, resultCode, data);
   AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

Questo metodo viene richiamato ogni volta che viene avviata l'applicazione broker e viene usato come opportunità per elaborare la risposta dal broker e completare il processo di autenticazione avviato da MSAL.NET.

### <a name="step-3-set-an-activity"></a>Passaggio 3: impostare un'attività

Per abilitare l'autenticazione negoziata, impostare un'attività in modo che MSAL possa inviare e ricevere la risposta da e verso il broker. A tale scopo, fornire l'attività (in genere `MainActivity` ) all' `WithParentActivityOrWindow(object parent)` oggetto padre.

Ad esempio, nella chiamata a `AcquireTokenInteractive()` :

```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow((Activity)context))
             .ExecuteAsync();
```

### <a name="step-4-add-a-redirect-uri-to-your-app-registration"></a>Passaggio 4: aggiungere un URI di reindirizzamento alla registrazione dell'app

MSAL usa gli URL per richiamare il broker e quindi tornare all'app. Per completare questa round trip, è necessario registrare un **URI di reindirizzamento** per l'app usando il [portale di Azure](https://portal.azure.com).

Il formato dell'URI di reindirizzamento per l'applicazione dipende dal certificato usato per firmare l'APK. Ad esempio:

```
msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=
```

L'ultima parte dell'URI, `hgbUYHVBYUTvuvT&Y6tr554365466=` , è la versione con codifica Base64 della firma con cui è firmato l'apk. Quando si sviluppa l'app in Visual Studio, se si esegue il debug del codice senza firmare l'APK con un certificato specifico, Visual Studio firma l'APK per l'utente a scopo di debug. Quando Visual Studio firma l'APK in questo modo, fornisce una firma univoca per il computer su cui è basata. Pertanto, ogni volta che si compila l'app in un computer diverso, è necessario aggiornare l'URI di reindirizzamento nel codice dell'applicazione e la registrazione dell'applicazione nella portale di Azure per eseguire l'autenticazione con MSAL.

Durante il debug, è possibile che si verifichi un'eccezione MSAL (o un messaggio di log) indicante che l'URI di reindirizzamento specificato non è corretto. **Il messaggio di eccezione o di log indica anche l'URI di Reindirizzamento da usare** con il computer corrente in cui si esegue il debug. È possibile usare l'URI di reindirizzamento fornito per continuare a sviluppare l'app, purché si aggiorni l'URI di reindirizzamento nel codice e si aggiunga l'URI di reindirizzamento fornito alla registrazione dell'app nel portale di Azure.

Quando si è pronti per finalizzare il codice, aggiornare l'URI di reindirizzamento nel codice e la registrazione dell'applicazione nella portale di Azure per usare la firma del certificato con cui si firma l'APK.

In pratica, questo significa che è consigliabile aggiungere un URI di reindirizzamento per ogni membro del team di sviluppo, *oltre* a un URI di reindirizzamento per la versione con firma di produzione dell'APK.

È possibile calcolare la firma autonomamente, in modo analogo a come MSAL:

```csharp
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

È anche possibile acquisire la firma per il pacchetto usando il comando **Tool** con i comandi seguenti:

* Windows:
    ```console
    keytool.exe -list -v -keystore "%LocalAppData%\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android
    ````
* macOS:
    ```console
    keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
    ````

### <a name="step-5-optional-fall-back-to-the-system-browser"></a>Passaggio 5 (facoltativo): eseguire il fallback al browser di sistema

Se MSAL è configurato per l'uso del broker, ma il broker non è installato, MSAL eseguirà il fallback utilizzando una visualizzazione Web (un browser). MSAL tenterà di eseguire l'autenticazione usando il browser di sistema predefinito sul dispositivo, operazione che ha esito negativo perché l'URI di reindirizzamento è configurato per il broker e il browser di sistema non è in grado di usarlo per tornare a MSAL. Per evitare l'errore, è possibile configurare un *filtro preventivo* con l'URI di reindirizzamento del broker usato nel passaggio 4.

Modificare il manifesto dell'applicazione per aggiungere il filtro preventivo:

```xml
<!-- NOTE the SLASH (required) that prefixes the signature value in the path attribute.
     The signature value is the Base64-encoded signature discussed above. -->
<intent-filter>
      <data android:scheme="msauth"
                    android:host="Package Name"
                    android:path="/Package Signature"/>
```

Se, ad esempio, si dispone di un URI di Reindirizzamento `msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=` , il manifesto dovrebbe essere simile al seguente frammento XML.

La barra () davanti `/` alla firma nel `android:path` valore è **obbligatoria**.

```xml
<!-- NOTE the SLASH (required) that prefixes the signature value in the path attribute.
     The signature value is the Base64-encoded signature discussed above. -->
<intent-filter>
      <data android:scheme="msauth"
                    android:host="com.microsoft.xforms.testApp"
                    android:path="/hgbUYHVBYUTvuvT&Y6tr554365466="/>
```

In alternativa, è possibile configurare MSAL per eseguire il fallback al browser incorporato, che non si basa su un URI di reindirizzamento:

```csharp
.WithUseEmbeddedWebUi(true)
```

## <a name="troubleshooting-tips-for-android-brokered-authentication"></a>Suggerimenti per la risoluzione dei problemi relativi all'autenticazione negoziata Android

Ecco alcuni suggerimenti per evitare problemi quando si implementa l'autenticazione negoziata in Android:

- **URI di reindirizzamento** : aggiungere un URI di reindirizzamento alla registrazione dell'applicazione nella [portale di Azure](https://portal.azure.com/). Un URI di reindirizzamento mancante o errato è un problema comune riscontrato dagli sviluppatori.
- **Versione broker** : installare la versione minima richiesta delle app Broker. Una di queste due app può essere usata per l'autenticazione negoziata in Android.
  - [Portale aziendale Intune](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal) (versione 5.0.4689.0 o successiva)
  - [Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator) (versione 6.2001.0140 o successiva).
- **Precedenza di Service Broker** : MSAL comunica con il *primo broker installato* sul dispositivo quando sono installati più broker.

    Esempio: se si installa per la prima volta Microsoft Authenticator e quindi si installa Portale aziendale Intune, l'autenticazione negoziata verrà eseguita *solo* sul Microsoft Authenticator.
- **Log** : se si verifica un problema con l'autenticazione negoziata, la visualizzazione dei log del broker può aiutare a diagnosticare la causa.
  - Visualizza log Microsoft Authenticator:

    1. Selezionare il pulsante di menu nell'angolo in alto a destra dell'app.
    1. Selezionare la **Guida**  >  **Invia log**  >  **Visualizza log**.
    1. Selezionare **copia tutto** per copiare i log del broker negli Appunti del dispositivo.

    Il modo migliore per eseguire il debug con questi log è inviare un messaggio di posta elettronica a se stessi e visualizzarli nel computer di sviluppo. Potrebbe risultare più semplice analizzare i log nel computer invece che nel dispositivo stesso. È anche possibile usare un editor di test in Android per salvare i log come file di testo e quindi usare un cavo USB per copiare il file in un computer.

  - Visualizza log Portale aziendale Intune:

    1. Selezionare il pulsante di menu nell'angolo in alto a sinistra dell'app
    1. Selezionare **Impostazioni**  >  **dati di diagnostica**
    1. Selezionare **copia log** per copiare i log del broker nella scheda SD del dispositivo.
    1. Connettere il dispositivo a un computer usando un cavo USB per visualizzare i log nel computer di sviluppo.

    Dopo aver creato i log, è possibile cercarli per i tentativi di autenticazione tramite l'ID di correlazione. L'ID di correlazione è associato a ogni richiesta di autenticazione. Per trovare gli errori restituiti dall'endpoint di autenticazione della piattaforma di identità Microsoft, cercare `AADSTS` .

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle [considerazioni relative all'uso di piattaforma UWP (Universal Windows Platform) con MSAL.NET](msal-net-uwp-considerations.md).
