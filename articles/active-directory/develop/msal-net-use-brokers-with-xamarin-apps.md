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
ms.openlocfilehash: 25b8aa9b5e80720e9543dafce7970404a62b7d1f
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132647"
---
# <a name="use-microsoft-authenticator-or-intune-company-portal-on-xamarin-applications"></a>Usare Microsoft Authenticator o Portale aziendale Intune nelle applicazioni Novell

In Android e iOS, i broker come Microsoft Authenticator e Microsoft Intune specifici di Android Portale aziendale abilitano:

- **Single Sign-on (SSO)** : gli utenti non devono accedere a ogni applicazione.
- **Identificazione del dispositivo**: il broker accede al certificato del dispositivo. Questo certificato viene creato nel dispositivo quando viene aggiunto all'area di lavoro.
- **Verifica dell'identificazione dell'applicazione**: quando un'applicazione chiama il broker, passa l'URL di reindirizzamento. Il Broker verifica l'URL.

Per abilitare una di queste funzionalità, usare il parametro `WithBroker()` quando si chiama il metodo `PublicClientApplicationBuilder.CreateApplication`. Per impostazione predefinita, il parametro `.WithBroker()` è impostato su true. 

Usare anche le istruzioni riportate nelle sezioni seguenti per configurare l'autenticazione negoziata per le applicazioni [iOS](#brokered-authentication-for-ios) o [Android](#brokered-authentication-for-android) .

## <a name="brokered-authentication-for-ios"></a>Autenticazione negoziata per iOS

Usare la procedura seguente per abilitare l'app Novell. iOS per comunicare con l'app [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) .

### <a name="step-1-enable-broker-support"></a>Passaggio 1: abilitare il supporto broker
È necessario abilitare il supporto del broker per le singole istanze di `PublicClientApplication`. Il supporto è disabilitato per impostazione predefinita. Quando si crea `PublicClientApplication` tramite `PublicClientApplicationBuilder`, usare il parametro `WithBroker()` come illustrato nell'esempio riportato di seguito. Per impostazione predefinita, il parametro `WithBroker()` è impostato su true.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>Passaggio 2: abilitare l'accesso Keychain

Per abilitare l'accesso keychain, è necessario disporre di un gruppo di accesso a Keychain per l'applicazione. Quando si crea l'applicazione, è possibile usare l'API `WithIosKeychainSecurityGroup()` per impostare il gruppo di accesso a Keychain:

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
      
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Per altre informazioni, vedere [Enable Keychain Access](msal-net-xamarin-ios-considerations.md#enable-keychain-access).

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Passaggio 3: aggiornare AppDelegate per gestire il callback
Quando Microsoft Authentication Library per .NET (MSAL.NET) chiama il broker, il broker richiama l'applicazione tramite il metodo `OpenUrl` della classe `AppDelegate`. Poiché MSAL attende la risposta del broker, l'applicazione deve collaborare per chiamare MSAL.NET. Per abilitare questa collaborazione, aggiornare il file di `AppDelegate.cs` per eseguire l'override del metodo riportato di seguito.

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
Sempre nel file `AppDelegate.cs` è necessario impostare una finestra degli oggetti. In genere, per Novell iOS non è necessario impostare la finestra degli oggetti. Tuttavia è necessaria una finestra oggetto per inviare e ricevere risposte dal broker. 

Per configurare la finestra oggetto: 
1. Nel file di `AppDelegate.cs` impostare `App.RootViewController` su un nuovo `UIViewController()`. Questa assegnazione garantisce che la chiamata al broker includa `UIViewController`. Se questa impostazione viene assegnata in modo errato, è possibile che venga ricevuto questo errore:

      `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`

1. Nella chiamata `AcquireTokenInteractive` usare `.WithParentActivityOrWindow(App.RootViewController)`, quindi passare il riferimento alla finestra oggetto che verrà usata.

    In `App.cs`:
    
    ```csharp
       public static object RootViewController { get; set; }
    ```
    
    In `AppDelegate.cs`:
    
    ```csharp
       LoadApplication(new App());
       App.RootViewController = new UIViewController();
    ```
    
    Nella chiamata `AcquireToken`:
    
    ```csharp
    result = await app.AcquireTokenInteractive(scopes)
                 .WithParentActivityOrWindow(App.RootViewController)
                 .ExecuteAsync();
    ```

### <a name="step-5-register-a-url-scheme"></a>Passaggio 5: registrare uno schema URL
MSAL.NET usa gli URL per richiamare il broker e quindi restituisce la risposta del broker all'app. Per completare la round trip, registrare uno schema URL per l'app nel file di `Info.plist`.

Il nome del `CFBundleURLSchemes` deve includere `msauth.` come prefisso. Seguire il prefisso con `CFBundleURLName`. 

Nello schema URL `BundleId` identifica in modo univoco l'app: `$"msauth.(BundleId)"`. Quindi, se `BundleId` è `com.yourcompany.xforms`, lo schema URL viene `msauth.com.yourcompany.xforms`.

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

Aggiungere `msauthv2` alla sezione `LSApplicationQueriesSchemes` del file di `Info.plist`, come nell'esempio seguente:

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
Si noti che l'URI di reindirizzamento corrisponde al nome del `CFBundleURLSchemes` incluso nel file di `Info.plist`.

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

MSAL.NET supporta solo la piattaforma Novell. iOS. Non supporta ancora i broker per la piattaforma Novell. Android.

La libreria nativa di MSAL Android supporta già l'autenticazione negoziata. Per altre informazioni, vedere [autenticazione negoziata in Android](brokered-auth.md).

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle [considerazioni relative all'uso di piattaforma UWP (Universal Windows Platform) con MSAL.NET](msal-net-uwp-considerations.md).
