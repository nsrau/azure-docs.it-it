---
title: Eseguire la migrazione di app Xamarin tramite broker a MSAL.NET
titleSuffix: Microsoft identity platform
description: Informazioni su come eseguire la migrazione di app Xamarin iOS che usano Microsoft Authenticator da ADAL.NET a MSAL.NET.
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
ms.openlocfilehash: de259daa7fd27cc4f138c294a7f347502ca482a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77185835"
---
# <a name="migrate-ios-applications-that-use-microsoft-authenticator-from-adalnet-to-msalnet"></a>Eseguire la migrazione di applicazioni iOS che usano Microsoft Authenticator da ADAL.NET a MSAL.NET

Hai usato la libreria di autenticazione di Azure Active Directory per .NET (ADAL.NET) e il broker iOS. Ora è il momento di eseguire la migrazione alla libreria di autenticazione [Microsoft](msal-overview.md) per .NET (MSAL.NET), che supporta il broker su iOS dalla versione 4.3 in poi. 

Da dove dovresti cominciare? Questo articolo consente di eseguire la migrazione dell'app Xamarin iOS da ADAL a MSAL.

## <a name="prerequisites"></a>Prerequisiti
Questo articolo presuppone che tu abbia già un'app Xamarin iOS integrata con il broker iOS. Se non lo fai, passare direttamente a MSAL.NET e iniziare l'implementazione broker lì. Per informazioni su come richiamare il broker iOS in MSAL.NET con una nuova applicazione, vedere [questa documentazione](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS#why-use-brokers-on-xamarinios-and-xamarinandroid-applications).

## <a name="background"></a>Background

### <a name="what-are-brokers"></a>Cosa sono i broker?

I broker sono applicazioni fornite da Microsoft su Android e iOS. Vedere l'app [Microsoft Authenticator](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6) in iOS e Android e l'app Portale aziendale di Intune in Android. 

Essi consentono:

- Single Sign-On.
- Identificazione del dispositivo, richiesta da alcuni [criteri di accesso condizionale](../conditional-access/overview.md). Per ulteriori informazioni, vedere [Gestione dei dispositivi](../conditional-access/concept-conditional-access-conditions.md#device-platforms).
- Verifica dell'identificazione dell'applicazione, necessaria anche in alcuni scenari aziendali. Per altre informazioni, vedere [Gestione delle applicazioni mobili di Intune (MAM)](https://docs.microsoft.com/intune/mam-faq).

## <a name="migrate-from-adal-to-msal"></a>Eseguire la migrazione da ADAL a MSALMigrate from ADAL to MSAL

### <a name="step-1-enable-the-broker"></a>Passaggio 1: Abilitare il brokerStep 1: Enable the broker

<table>
<tr><td>Codice ADAL corrente:</td><td>Controparte MSAL:</td></tr>
<tr><td>
In ADAL.NET, il supporto del broker è stato abilitato in base al contesto di autenticazione. È disabilitata per impostazione predefinita. Dovevi impostare un 

`useBroker`flag su true `PlatformParameters` nel costruttore per chiamare il broker:

```csharp
public PlatformParameters(
        UIViewController callerViewController, 
        bool useBroker)
```
Inoltre, nel codice specifico della piattaforma, in questo esempio, nel renderer di pagina per iOS, impostare il`useBroker` 
flag su true:
```csharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```

Quindi, includere i parametri nella chiamata del token di acquisizione:Then, include the parameters in the acquire token call:
```csharp
 AuthenticationResult result =
                    await
                        AuthContext.AcquireTokenAsync(
                              Resource, 
                              ClientId, 
                              new Uri(RedirectURI), 
                              platformParameters)
                              .ConfigureAwait(false);
```

</td><td>
In MSAL.NET, il supporto del broker è abilitato in base a PublicClientApplication.In MSAL.NET, broker support is enabled on a per-PublicClientApplication. È disabilitata per impostazione predefinita. Per attivarlo, utilizzare il 

`WithBroker()`parametro (impostato su true per impostazione predefinita) per chiamare il broker:

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos)
                .Build();
```
Nella chiamata del token di acquisizione:
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

### <a name="step-2-set-a-uiviewcontroller"></a>Passaggio 2: Impostare un UIViewController()
In ADAL.NET, è stato passato in `PlatformParameters`un UIViewController come parte di . (Vedere l'esempio nel passaggio 1.) In MSAL.NET, per offrire agli sviluppatori maggiore flessibilità, viene usata una finestra oggetto, ma non è necessaria nell'utilizzo normale di iOS.In MSAL.NET, to give developers more flexibility, an object window is used, but it's not required in regular iOS usage. Per utilizzare il broker, impostare la finestra dell'oggetto per inviare e ricevere risposte dal broker. 
<table>
<tr><td>Codice ADAL corrente:</td><td>Controparte MSAL:</td></tr>
<tr><td>
Un UIViewController viene passato in 

`PlatformParameters`nella piattaforma specifica di iOS.

```csharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```
</td><td>
In MSAL.NET, è necessario eseguire due operazioni per impostare la finestra dell'oggetto per iOS:In MSAL.NET, you do two things to set the object window for iOS:

1. In `AppDelegate.cs`, `App.RootViewController` impostare `UIViewController()`su un nuovo file . Questa assegnazione assicura che c'è un UIViewController con la chiamata al broker. Se non è impostato correttamente, è possibile che venga visualizzato questo errore:If it isn't set correctly, you might get this error:`"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
1. Nella chiamata AcquireTokenInteractive, `.WithParentActivityOrWindow(App.RootViewController)`utilizzare e passare il riferimento alla finestra dell'oggetto che verrà utilizzata.

**Per esempio:**

In `App.cs`:
```csharp
   public static object RootViewController { get; set; }
```
In `AppDelegate.cs`:
```csharp
   LoadApplication(new App());
   App.RootViewController = new UIViewController();
```
Nella chiamata del token di acquisizione:
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

</table>

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Passaggio 3: Aggiornare AppDelegate per gestire il callbackStep 3: Update AppDelegate to handle the callback
Sia ADAL che MSAL chiamano il broker e il broker `OpenUrl` a sua `AppDelegate` volta richiama all'applicazione tramite il metodo della classe. Per ulteriori informazioni, vedere [questa documentazione](msal-net-use-brokers-with-xamarin-apps.md#step-3-update-appdelegate-to-handle-the-callback).

Non ci sono cambiamenti tra ADAL.NET e MSAL.NET.

### <a name="step-4-register-a-url-scheme"></a>Passaggio 4: Registrare uno schema URLStep 4: Register a URL scheme
ADAL.NET e MSAL.NET utilizzano gli URL per richiamare il broker e restituire la risposta del broker all'app. Registra lo schema `Info.plist` URL nel file per la tua app come segue:

<table>
<tr><td>Codice ADAL corrente:</td><td>Controparte MSAL:</td></tr>
<tr><td>
Lo schema URL è univoco per la tua app.
</td><td>
Il valore di  

`CFBundleURLSchemes`nome deve includere 

`msauth.`

come prefisso, seguito dal`CFBundleURLName`

Ad esempio: `$"msauth.(BundleId")`

```csharp
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

> [!NOTE]
> Questo schema URL diventa parte dell'URI di reindirizzamento utilizzato per identificare in modo univoco l'app quando riceve la risposta dal broker.

</table>

### <a name="step-5-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>Passaggio 5: Aggiungere l'identificatore del broker alla sezione LSApplicationQueriesSchemes

ADAL.NET e MSAL.NET `-canOpenURL:` entrambi utilizzare per verificare se il broker è installato sul dispositivo. Aggiungere l'identificatore corretto per il broker iOS alla sezione LSApplicationQueriesSchemes del file info.plist come segue:

<table>
<tr><td>Codice ADAL corrente:</td><td>Controparte MSAL:</td></tr>
<tr><td>
Utilizzi 

`msauth`


```csharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauth</string>
</array>
```
</td><td>
Utilizzi 

`msauthv2`


```csharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
     <string>msauthv3</string>
</array>
```
</table>

### <a name="step-6-register-your-redirect-uri-in-the-portal"></a>Passaggio 6: Registrare l'URI di reindirizzamento nel portaleStep 6: Register your redirect URI in the portal

ADAL.NET e MSAL.NET entrambi aggiungono un requisito aggiuntivo all'URI di reindirizzamento quando è destinato al broker. Registrare l'URI di reindirizzamento con l'applicazione nel portale.
<table>
<tr><td>Codice ADAL corrente:</td><td>Controparte MSAL:</td></tr>
<tr><td>

`"<app-scheme>://<your.bundle.id>"`

Esempio: 

`mytestiosapp://com.mycompany.myapp`
</td><td>

`$"msauth.{BundleId}://auth"`

Esempio:

`public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; `

</table>

Per ulteriori informazioni su come registrare l'URI di reindirizzamento nel portale, vedere [Sfruttare il broker nelle applicazioni Xamarin.iOS](msal-net-use-brokers-with-xamarin-apps.md#step-8-make-sure-the-redirect-uri-is-registered-with-your-app).

## <a name="next-steps"></a>Passaggi successivi

Scopri le considerazioni specifiche di [Xamarin per iOS con MSAL.NET](msal-net-xamarin-ios-considerations.md). 
