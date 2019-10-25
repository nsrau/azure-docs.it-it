---
title: Usare Microsoft Authenticator o Microsoft Intune Portale aziendale nelle applicazioni Novell iOS e Android
titleSuffix: Microsoft identity platform
description: Informazioni su come eseguire la migrazione di applicazioni Novell iOS che possono usare Microsoft Authenticator dalla libreria Autenticazione di Azure AD per .NET (ADAL.NET) a Microsoft Authentication Library per .NET (MSAL.NET)
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
ms.openlocfilehash: e71e9ef72e7b6caaa3894bb30c6e7e9cf762232a
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802721"
---
# <a name="use-microsoft-authenticator-or-microsoft-intune-company-portal-on-xamarin-applications"></a>Usare Microsoft Authenticator o Microsoft Intune Portale aziendale nelle applicazioni Novell

In Android e iOS, broker come Microsoft Authenticator o Microsoft Intune Portale aziendale Abilita (solo Android):

- Single Sign-on (SSO). Gli utenti non dovranno accedere a ogni applicazione.
- Identificazione del dispositivo. Il broker accede al certificato del dispositivo, che è stato creato nel dispositivo quando è stato aggiunto all'area di lavoro.
- Verifica dell'identificazione dell'applicazione. Quando un'applicazione chiama il broker, passa l'URL di reindirizzamento e il broker lo verifica.

Per abilitare una di queste funzionalità, gli sviluppatori di applicazioni devono usare il parametro `WithBroker()` quando chiamano il metodo `PublicClientApplicationBuilder.CreateApplication`. per impostazione predefinita, `.WithBroker()` è impostato su true. Gli sviluppatori devono anche seguire questa procedura per le applicazioni [iOS](#brokered-authentication-for-ios) o [Android](#brokered-authentication-for-android) .

## <a name="brokered-authentication-for-ios"></a>Autenticazione negoziata per iOS

Seguire questa procedura per abilitare l'app Novell. iOS per comunicare con l'app [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) .

### <a name="step-1-enable-broker-support"></a>Passaggio 1: abilitare il supporto broker
Il supporto del broker è abilitato per ogni singolo PublicClientApplication. È disabilitata per impostazione predefinita. Usare il parametro `WithBroker()` (impostato su true per impostazione predefinita) quando si crea il PublicClientApplication tramite PublicClientApplicationBuilder.

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Passaggio 2: aggiornare AppDelegate per gestire il callback
Quando Microsoft Authentication Library per .NET (MSAL.NET) chiama il broker, il broker a sua volta esegue una chiamata all'applicazione tramite il metodo `OpenUrl` della classe `AppDelegate`. Poiché MSAL attende la risposta dal broker, l'applicazione deve collaborare per chiamare MSAL.NET. Per abilitare questa collaborazione, aggiornare il file di `AppDelegate.cs` per eseguire l'override del metodo riportato di seguito.

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

Questo metodo viene richiamato ogni volta che l'applicazione viene avviata. Viene usato come opportunità per elaborare la risposta dal broker e completare il processo di autenticazione avviato da MSAL.NET.

### <a name="step-3-set-a-uiviewcontroller"></a>Passaggio 3: impostare un UIViewController ()
Sempre in `AppDelegate.cs`, è necessario impostare una finestra degli oggetti. In genere, con Novell iOS, non è necessario impostare la finestra degli oggetti. Per inviare e ricevere risposte dal broker, è necessaria una finestra degli oggetti. 

A tale scopo, è necessario eseguire due operazioni. 
1. In `AppDelegate.cs`impostare il `App.RootViewController` su un nuovo `UIViewController()`. Questa assegnazione garantisce la presenza di un UIViewController con la chiamata al broker. Se non è impostato correttamente, è possibile che venga ricevuto questo errore: `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
1. Nella chiamata AcquireTokenInteractive usare il `.WithParentActivityOrWindow(App.RootViewController)` e passare il riferimento alla finestra oggetto che verrà usata.

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

### <a name="step-4-register-a-url-scheme"></a>Passaggio 4: registrare uno schema URL
MSAL.NET usa gli URL per richiamare il broker e quindi riportare la risposta di Service Broker all'app. Per completare la round trip, registrare uno schema URL per l'app nel file di `Info.plist`.

Il nome del `CFBundleURLSchemes` deve includere `msauth.` come prefisso, seguito dal `CFBundleURLName`.

`$"msauth.(BundleId)"`

**Ad esempio:**

`msauth.com.yourcompany.xforms`

> [!NOTE]
> Questo schema URL diventa parte dell'URI di reindirizzamento usato per identificare in modo univoco l'app quando riceve la risposta dal broker.

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

### <a name="step-5-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>Passaggio 5: aggiungere l'identificatore di Service Broker alla sezione LSApplicationQueriesSchemes
MSAL USA `–canOpenURL:` per verificare se il broker è installato nel dispositivo. In iOS 9, Apple ha bloccato gli schemi per cui un'applicazione può eseguire query. 

Aggiungere `msauthv2` alla sezione `LSApplicationQueriesSchemes` del file di `Info.plist`.

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="step-6-register-your-redirect-uri-in-the-application-portal"></a>Passaggio 6: registrare l'URI di reindirizzamento nel portale applicazioni
L'uso di Service Broker aggiunge un requisito aggiuntivo all'URI di reindirizzamento. L'URI di Reindirizzamento _deve_ avere il formato seguente:
```CSharp
$"msauth.{BundleId}://auth"
```
**Ad esempio:**
```CSharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```
Si noti che l'URI di reindirizzamento corrisponde al nome del `CFBundleURLSchemes` incluso nel file di `Info.plist`.

### <a name="step-7-make-sure-the-redirect-uri-is-registered-with-your-app"></a>Passaggio 7: assicurarsi che l'URI di reindirizzamento sia registrato con l'app

Questo URI di reindirizzamento deve essere registrato nel portale di registrazione delle app (https://portal.azure.com) come URI di reindirizzamento valido per l'applicazione. 

Il portale offre una nuova esperienza nel portale di registrazione delle app che consente di calcolare l'URI di risposta negoziata dall'ID bundle.

1. Nella registrazione dell'app scegliere **autenticazione** e quindi **provare la nuova esperienza**.

   ![Prova la nuova esperienza di registrazione delle app](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

1. Selezionare **Aggiungi una piattaforma**.

   ![Aggiungere una piattaforma](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

1. Quando l'elenco delle piattaforme è supportato, selezionare **iOS**.

   ![Configurare iOS](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

1. Immettere l'ID bundle come richiesto e quindi selezionare **Configure (Configura**).

   ![Immettere l'ID bundle](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

1. L'URI di reindirizzamento viene calcolato per l'utente.

   ![Copia URI di Reindirizzamento](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>Autenticazione negoziata per Android

Il supporto Broker non è disponibile per Android.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle [considerazioni specifiche di piattaforma UWP (Universal Windows Platform) con MSAL.NET](msal-net-uwp-considerations.md).