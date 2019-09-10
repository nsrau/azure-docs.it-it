---
title: Migrazione di applicazioni Novell iOS con Microsoft Authenticator da ADAL.NET a MSAL.NET | Azure
description: Informazioni su come eseguire la migrazione di applicazioni iOS Novell usando Microsoft Authenticator dalla libreria Autenticazione di Azure AD per .NET (ADAL.NET) a Microsoft Authentication Library per .NET (MSAL.NET)
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/08/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 609aeeada78985466cb7c254cab32864e5d84b0e
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70875653"
---
# <a name="migrating-ios-applications-using-microsoft-authenticator-from-adalnet-to-msalnet"></a>Migrazione di applicazioni iOS con Microsoft Authenticator da ADAL.NET a MSAL.NET

Si è scelto di usare ADAL.NET e il broker iOS ed è ora di eseguire la migrazione a MSAL.NET [Microsoft Authentication Library](msal-overview.md), che supporta il broker in iOS dalla versione 4,3 e successive. 

Dove iniziare? Questo articolo illustra come eseguire la migrazione dell'app Novell iOS da ADAL a MSAL.

## <a name="prerequisites"></a>Prerequisiti
Questo documento presuppone che sia già presente un'app Novell iOS integrata con il broker iOS. In caso contrario, è preferibile passare direttamente a MSAL.NET e iniziare l'implementazione del broker. Per informazioni dettagliate sulla chiamata del broker iOS in MSAL.NET con una nuova applicazione, vedere [questa documentazione](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS#why-use-brokers-on-xamarinios-and-xamarinandroid-applications) .

## <a name="background"></a>Sfondo

### <a name="what-are-brokers"></a>Che cosa sono i broker?

I broker sono applicazioni, fornite da Microsoft, in Android e iOS ([Microsoft Authenticator](https://www.microsoft.com/account/authenticator) in iOS e Android portale aziendale Intune in Android). 

Consentono di:

- Single Sign-on,
- Identificazione del dispositivo, richiesta da alcuni [criteri di accesso condizionale](../conditional-access/overview.md) (vedere [gestione dei dispositivi](../conditional-access/conditions.md#device-platforms))
- Verifica dell'identificazione dell'applicazione, necessaria anche in alcuni scenari aziendali (vedere ad esempio [gestione di applicazioni mobili di Intune o mam](https://docs.microsoft.com/intune/mam-faq))

## <a name="migrate-from-adal-to-msal"></a>Eseguire la migrazione da ADAL a MSAL

### <a name="step-1-enable-the-broker"></a>Passaggio 1: Abilitare Service Broker

<table>
<tr><td>Codice ADAL corrente:</td><td>Controparte MSAL:</td></tr>
<tr><td>
In ADAL.NET, il supporto del broker è stato abilitato in base al contesto di autenticazione, è disabilitato per impostazione predefinita. È necessario impostare un `useBroker` flag su true `PlatformParameters` nel costruttore per chiamare Broker:

```CSharp
public PlatformParameters(
        UIViewController callerViewController, 
        bool useBroker)
```
Inoltre, nel codice specifico della piattaforma, in questo esempio, nel renderer di pagina per iOS, impostare il`useBroker` 
flag su true:
```CSharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```

Quindi, includere i parametri nella chiamata al token di acquisizione:
```CSharp
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
In MSAL.NET il supporto del broker è abilitato per le singole applicazioni client. Questo protocollo è disabilitato per impostazione predefinita. Per abilitarla, usare: 

`WithBroker()`(per impostazione predefinita, impostare su true) per chiamare Broker:

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos)
                .Build();
```
Nella chiamata al token di acquisizione:
```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

### <a name="step-2-set-a-uiviewcontroller"></a>Passaggio 2: Imposta un UIViewController ()
In ADAL.NET è stato passato il UIViewController come parte di PlatformParameters (vedere l'esempio nel passaggio 1). Tuttavia, in MSAL.NET, per offrire agli sviluppatori maggiore flessibilità, viene usata una finestra oggetto, ma non è necessaria nell'utilizzo normale di iOS. Tuttavia, per poter utilizzare Service Broker, è necessario impostare la finestra degli oggetti per inviare e ricevere risposte da Service Broker. 
<table>
<tr><td>Codice ADAL corrente:</td><td>Controparte MSAL:</td></tr>
<tr><td>
Il UIViewController viene passato in PlatformParamters nella piattaforma specifica iOS.

```CSharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```
</td><td>
In MSAL.NET è necessario eseguire due operazioni per impostare la finestra oggetto per iOS:

1) In `AppDelegate.cs` `UIViewController()`impostare suunnuovooggetto.`App.RootViewController` Questa assegnazione garantirà la presenza di un UIViewController con la chiamata al broker. Se la configurazione non è corretta, è possibile che venga ricevuto questo errore:`"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
2) Nella chiamata AcquireTokenInteractive usare il`.WithParentActivityOrWindow(App.RootViewController)`
e passano il riferimento alla finestra degli oggetti you'will use.

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

</table>

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Passaggio 3: Aggiornare AppDelegate per gestire il callback
Sia adal che MSAL chiameranno il broker e il broker effettuerà a sua volta la chiamata all'applicazione tramite il `OpenUrl` metodo `AppDelegate` della classe. Ulteriori informazioni sono disponibili [qui](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS/_edit#step-two-update-appdelegate-to-handle-the-callback)

: heavy_check_mark: non**sono state apportate modifiche tra adal.NET e MSAL.NET**

### <a name="step-4-register-a-url-scheme"></a>Passaggio 4: Registrare uno schema URL
ADAL.NET e MSAL.NET usano gli URL per richiamare il broker e restituire la risposta di Service Broker all'app. Registrare lo schema URL nel `Info.plist` file per l'app come segue:

<table>
<tr><td>Codice ADAL corrente:</td><td>Controparte MSAL:</td></tr>
<tr><td>
Lo schema URL è univoco per l'app.
</td><td>
Alla classe 

`CFBundleURLSchemes`il nome deve includere 

`msauth.`

come prefisso, seguito dal`CFBundleURLName`

Ad esempio: `$"msauth.(BundleId")`

```CSharp
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
>  Questo schema URL diventerà parte del RedirectUri usato per identificare in modo univoco l'app quando riceve la risposta dal broker

</table>

### <a name="step-5-lsapplicationqueriesschemes"></a>Passaggio 5: LSApplicationQueriesSchemes

ADAL.NET e MSAL.NET usano `-canOpenURL:` entrambi per verificare se il broker è installato nel dispositivo. Aggiungere l'identificatore corretto per il broker iOS alla sezione LSApplicationQueriesSchemes del file INFO. plist come indicato di seguito: 
<table>
<tr><td>Codice ADAL corrente:</td><td>Controparte MSAL:</td></tr>
<tr><td>
Utilizzi 

`msauth`


```CSharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauth</string>
</array>
```
</td><td>
Utilizzi 

`msauthv2`


```CSharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
</array>
```
</table>

### <a name="step-6-register-you-redirecturi-in-the-portal"></a>Passaggio 6: Registrare RedirectUri nel portale

ADAL.NET e MSAL.NET aggiungono un requisito aggiuntivo in redirectUri quando la destinazione è Service Broker. Registrare l'URI di reindirizzamento con l'applicazione nel portale.
<table>
<tr><td>Codice ADAL corrente:</td><td>Controparte MSAL:</td></tr>
<tr><td>

`"<app-scheme>://<your.bundle.id>"`esempio`mytestiosapp://com.mycompany.myapp`
</td><td>

`$"msauth.{BundleId}://auth"`

esempio:

`public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; `

</table>

Per altre informazioni sulla registrazione del redirectUri nel portale, vedere uso [del broker nelle applicazioni Novell. iOS](msal-net-use-brokers-with-xamarin-apps.md#step-7-make-sure-the-redirect-uri-is-registered-with-your-app) .

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle [considerazioni specifiche di Novell per iOS con MSAL.NET](msal-net-xamarin-ios-considerations.md). 
