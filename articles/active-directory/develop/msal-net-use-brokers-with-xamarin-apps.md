---
title: Usare Microsoft Authenticator o Microsoft Intune portale aziendale nelle applicazioni Novell iOS e Android | Azure
description: Informazioni su come eseguire la migrazione delle applicazioni Novell iOS possono usare Microsoft Authenticator dalla libreria Autenticazione di Azure AD per .NET (ADAL.NET) a Microsoft Authentication Library per .NET (MSAL.NET)
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
ms.openlocfilehash: e16ad801b3b691d942c2ba0dc723ba72e24cf4c0
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70875640"
---
# <a name="use-microsoft-authenticator-or-microsoft-intune-company-portal-on-xamarin-applications"></a>Usare Microsoft Authenticator o Microsoft Intune portale aziendale per le applicazioni Novell

In Android e iOS, broker come Microsoft Authenticator o Microsoft Intune portale aziendale Abilita (solo Android):

- Single Sign-on (SSO). Gli utenti non dovranno accedere a ogni applicazione.
- Identificazione del dispositivo. Accedendo al certificato del dispositivo, creato sul dispositivo quando è stato aggiunto all'area di lavoro.
- Verifica dell'identificazione dell'applicazione. Quando un'applicazione chiama il broker, passa l'URL di reindirizzamento e il broker lo verifica.

Per abilitare una di queste funzionalità, gli sviluppatori di applicazioni devono usare `WithBroker()` il parametro quando chiamano `PublicClientApplicationBuilder.CreateApplication` il metodo. `.WithBroker()`per impostazione predefinita, è impostato su true. Gli sviluppatori dovranno anche attenersi alla procedura seguente per le applicazioni [iOS](#brokered-authentication-for-ios) o [Android](#brokered-authentication-for-android) .

## <a name="brokered-authentication-for-ios"></a>Autenticazione negoziata per iOS

Seguire questa procedura per consentire all'app Novell. iOS di comunicare con l'app [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) .

### <a name="step-1-enable-broker-support"></a>Passaggio 1: Abilita supporto broker
Il supporto del broker è abilitato per ogni singolo PublicClientApplication. È disabilitata per impostazione predefinita. Usare il `WithBroker()` parametro (impostato su true per impostazione predefinita) quando si crea PublicClientApplication tramite PublicClientApplicationBuilder.

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Passaggio 2: Aggiornare AppDelegate per gestire il callback
Quando MSAL.NET chiama il broker, il broker chiamerà a sua volta l'applicazione tramite il `OpenUrl` metodo `AppDelegate` della classe. Poiché MSAL attenderà la risposta dal broker, l'applicazione deve collaborare per chiamare MSAL.NET. Per abilitare questa collaborazione, aggiornare il `AppDelegate.cs` file per eseguire l'override del metodo riportato di seguito.

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

Questo metodo viene richiamato ogni volta che l'applicazione viene avviata e viene usata come opportunità per elaborare la risposta dal broker e completare il processo di autenticazione avviato da MSAL.NET.

### <a name="step-3-set-a-uiviewcontroller"></a>Passaggio 3: Imposta un UIViewController ()
Sempre in `AppDelegate.cs`, è necessario impostare una finestra degli oggetti. In genere, con Novell iOS, non è necessario impostare la finestra degli oggetti, ma per inviare e ricevere risposte da Service Broker, è necessaria una finestra degli oggetti. 

A tale scopo, è necessario eseguire due operazioni. 
1) In `AppDelegate.cs` `UIViewController()`impostare suunnuovooggetto.`App.RootViewController` Questa assegnazione assicurerà la presenza di un UIViewController con la chiamata al broker. Se la configurazione non è corretta, è possibile che venga ricevuto questo errore:`"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
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

### <a name="step-4-register-a-url-scheme"></a>Passaggio 4: Registrare uno schema URL
MSAL.NET usa gli URL per richiamare il broker e quindi riportare la risposta di Service Broker all'app. Per completare la round trip, è necessario registrare uno schema URL per l'app nel `Info.plist` file.

Il `CFBundleURLSchemes` nome deve includere `msauth.` come `CFBundleURLName`prefisso, seguito da.

`$"msauth.(BundleId)"`

**Per esempio:** 
`msauth.com.yourcompany.xforms`

> [!NOTE]
> Questa funzione diventerà parte del RedirectUri usato per identificare in modo univoco l'app quando riceve la risposta dal broker.

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

### <a name="step-5-lsapplicationqueriesschemes"></a>Passaggio 5: LSApplicationQueriesSchemes
MSAL USA `–canOpenURL:` per verificare se il broker è installato nel dispositivo. In iOS 9, Apple ha bloccato gli schemi per cui un'applicazione può eseguire query. 

**Aggiungi** alla sezione del`Info.plist` file. `LSApplicationQueriesSchemes` **`msauthv2`**

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="step-6-register-your-redirecturi-in-the-application-portal"></a>Passaggio 6: Registrare la RedirectUri nel portale applicazioni
L'uso del Broker aggiunge un requisito aggiuntivo per la redirectUri. Il redirectUri _**deve**_ avere il formato seguente:
```CSharp
$"msauth.{BundleId}://auth"
```
**Ad esempio:**
```CSharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```
**Si noterà che RedirectUri corrisponde al `CFBundleURLSchemes` nome incluso `Info.plist` nel file.**

### <a name="step-7-make-sure-the-redirect-uri-is-registered-with-your-app"></a>Passaggio 7: assicurarsi che l'URI di reindirizzamento sia registrato con l'app

Questo URI di reindirizzamento deve essere registrato nel portale di registrazione delle app https://portal.azure.com) (come URI di reindirizzamento valido per l'applicazione. 

Il portale offre una nuova esperienza nel portale di registrazione delle app che consente di calcolare l'URI di risposta negoziata dall'ID bundle:

1. Nella registrazione dell'app scegliere **autenticazione** e quindi **provare la nuova esperienza**.
   ![Prova la nuova esperienza di registrazione delle app](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. Selezionare **Aggiungi piattaforma**.
   ![Aggiungere una piattaforma](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. Quando l'elenco delle piattaforme è supportato, selezionare **iOS**.
   ![Configurare iOS](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. Immettere l'ID bundle come richiesto e quindi premere **Register**.
   ![Immettere l'ID bundle](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

5. L'URI di reindirizzamento viene calcolato per l'utente.
   ![Copia URI di Reindirizzamento](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>Autenticazione negoziata per Android

Il supporto Broker non è disponibile per Android

## <a name="next-steps"></a>Passaggi successivi

[Considerazioni specifiche di piattaforma UWP (Universal Windows Platform) con MSAL.NET](msal-net-uwp-considerations.md)