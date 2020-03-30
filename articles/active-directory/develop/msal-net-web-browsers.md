---
title: Utilizzo dei browser Web (MSAL.NET) Azure
titleSuffix: Microsoft identity platform
description: Informazioni su considerazioni specifiche quando si usa Xamarin Android con la libreria di autenticazione Microsoft per .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: ed1f47ae99f6346a932d0fe94be7586dc25a672f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262737"
---
# <a name="using-web-browsers-msalnet"></a>Utilizzo dei browser Web (MSAL.NET)

I browser Web sono necessari per l'autenticazione interattiva. Per impostazione predefinita, MSAL.NET supporta il [browser web](#system-web-browser-on-xamarinios-xamarinandroid) di sistema su Xamarin.iOS e Xamarin.Android. Ma [è anche possibile abilitare il browser Web Incorporato](#enable-embedded-webviews-on-ios-and-android) a seconda delle esigenze (UX, necessità di Single Sign-On (SSO), sicurezza) in [Xamarin.iOS](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinios) e [Xamarin.Android](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) apps. E puoi anche [scegliere dinamicamente](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) quale browser web utilizzare in base alla presenza di Chrome o di un browser che supporta le schede personalizzate di Chrome in Android. MSAL.NET supporta solo il browser di sistema nelle applicazioni desktop .NET Core.

## <a name="web-browsers-in-msalnet"></a>Browser Web in MSAL.NET

### <a name="interaction-happens-in-a-web-browser"></a>L'interazione avviene in un browser Web

È importante comprendere che quando si acquisisce un token in modo interattivo, il contenuto della finestra di dialogo non viene fornito dalla libreria, ma dal servizio token di sicurezza (Security Token Service). L'endpoint di autenticazione restituisce codice HTML e JavaScript che controlla l'interazione, il cui rendering viene eseguito in un browser Web o in un controllo Web.The authentication endpoint sends back some HTML and JavaScript that controls the interaction, which is rendered in a web browser or web control. Consentire al servizio token di sicurezza di gestire l'interazione HTML presenta molti vantaggi:

- La password (se ne è stata digitata una) non viene mai memorizzata dall'applicazione, né dalla libreria di autenticazione.
- Abilita i reindirizzamenti ad altri provider di identità (ad esempio, l'accesso con un account dell'istituto di istruzione aziendale o un account personale con MSAL o con un account social con Azure AD B2C).
- Consente al servizio token di protezione di controllare l'accesso condizionale, ad esempio, facendo in modo che l'utente esedisca l'autenticazione a più fattori durante la fase di autenticazione (immettendo un pin di Windows Hello o chiamandosi sul telefono o su un'app di autenticazione sul telefono). Nei casi in cui l'autenticazione a più fattori richiesta non è ancora configurata, l'utente può configurarla appena in tempo nella stessa finestra di dialogo.  L'utente immette il proprio numero di cellulare ed è guidato a installare un'applicazione di autenticazione e la scansione di un tag QR per aggiungere il proprio account. Questa interazione guidata dal server è una grande esperienza!
- Consente all'utente di modificare la password in questa stessa finestra di dialogo quando la password è scaduta (fornendo campi aggiuntivi per la vecchia password e la nuova password).
- Abilita la personalizzazione del tenant o dell'applicazione (immagini) controllata dall'amministratore tenant di Azure AD/ proprietario dell'applicazione.
- Consente agli utenti di acconsentire a consentire all'applicazione di accedere alle risorse/ambiti nel proprio nome subito dopo l'autenticazione.

### <a name="embedded-vs-system-web-ui"></a>Interfaccia utente Web di confronto tra Embedded e sistema

MSAL.NET è una libreria con più framework e dispone di codice specifico del framework per ospitare un browser in un controllo dell'interfaccia utente (ad esempio, in .Net Classic utilizza WinForms, su Xamarin utilizza controlli mobili nativi e così via). Questo controllo `embedded` è denominato interfaccia utente Web.This control is called web UI. In alternativa, MSAL.NET è anche in grado di avviare il browser del sistema operativo.

In genere, si consiglia di utilizzare l'impostazione predefinita della piattaforma, e questo è in genere il browser di sistema. Il browser di sistema è meglio ricordare gli utenti che hanno effettuato l'accesso prima. Se è necessario modificare questo comportamento, utilizzare`WithUseEmbeddedWebView(bool)`

### <a name="at-a-glance"></a>Riepilogo

| Framework        | Origine dati | System | Predefinito |
| ------------- |-------------| -----| ----- |
| Classi e classi che si esima     | Sì | Sì, sì, | Origine dati |
| .NET Core     | No | Sì, sì, | System |
| .NET Standard | No | Sì, sì, | System |
| UWP | Sì | No | Origine dati |
| Xamarin.Android | Sì | Sì  | System |
| Xamarin.iOS | Sì | Sì  | System |
| Xamarin.Mac| Sì | No | Origine dati |

- Richiedehttp://localhostl'URI di reindirizzamento " "

## <a name="system-web-browser-on-xamarinios-xamarinandroid"></a>Browser web di sistema su Xamarin.iOS, Xamarin.Android

Per impostazione predefinita, MSAL.NET supporta il browser Web di sistema su Xamarin.iOS, Xamarin.Android e .NET Core.By default, MSAL.NET supports the system web browser on Xamarin.iOS, Xamarin.Android, and .NET Core. Per tutte le piattaforme che forniscono l'interfaccia utente, ovvero non .NET Core, viene fornita una finestra di dialogo fornita dalla libreria che incorpora un controllo Web browser. MSAL.NET inoltre utilizza una visualizzazione Web incorporata per .NET Desktop e WAB per la piattaforma UWP. Tuttavia, sfrutta per impostazione predefinita il **browser web** di sistema per le applicazioni Xamarin iOS e Xamarin Android. In iOS, sceglie anche la visualizzazione Web da utilizzare a seconda della versione del sistema operativo (iOS12, iOS11 e versioni precedenti).

L'utilizzo del browser di sistema ha il vantaggio significativo di condividere lo stato SSO con altre applicazioni e con applicazioni web senza bisogno di un broker (Portale aziendale / Authenticator). Il browser di sistema è stato utilizzato, per impostazione predefinita, in MSAL.NET per le piattaforme Xamarin iOS e Xamarin Android perché, su queste piattaforme, il browser web di sistema occupa l'intero schermo, e l'esperienza utente è migliore. La visualizzazione Web di sistema non è distinguibile da una finestra di dialogo. Su iOS, però, l'utente potrebbe essere necessario dare il consenso per il browser per richiamare l'applicazione, che può essere fastidioso.

## <a name="system-browser-experience-on-net-core"></a>Esperienza del browser di sistema in .NET Core

In .NET Core, MSAL.NET avvierà il browser di sistema come processo separato. MSAL.NET non ha il controllo su questo browser, ma una volta che l'utente termina l'autenticazione, la pagina web viene reindirizzata in modo tale che MSAL.NET possibile intercettare l'URI.

È inoltre possibile configurare le app scritte per .NET Classic per l'utilizzo di questo browser, specificando

```csharp
await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
```

MSAL.NET non è in grado di rilevare se l'utente si allontana o semplicemente chiude il browser. Le app che usano questa tecnica `CancellationToken`sono incoraggiate a definire un timeout (tramite ). È consigliabile un timeout di almeno alcuni minuti, per prendere in considerazione i casi in cui all'utente viene richiesto di modificare la password o eseguire l'autenticazione a più fattori.

### <a name="how-to-use-the-default-os-browser"></a>Come utilizzare il browser ostiosistemati predefinito

MSAL.NET deve restare in ascolto e intercettare il codice inviato da AAD al termine dell'autenticazione dell'utente (vedere il codice di autorizzazione per i dettagli)The user needs to listen on `http://localhost:port` and intercept the code that AAD sends when the user is done authenticating (See Authorization [code](v2-oauth2-auth-code-flow.md) for details)

Per attivare il browser di sistema:

1. Durante la registrazione `http://localhost` dell'app, configurare come uri di reindirizzamento (non attualmente supportato da B2C)
2. Quando si costruisce PublicClientApplication, specificare questo uri di reindirizzamento:When you construct your PublicClientApplication, specify this redirect uri:

```csharp
IPublicClientApplication pca = PublicClientApplicationBuilder
                            .Create("<CLIENT_ID>")
                             // or use a known port if you wish "http://localhost:1234"
                            .WithRedirectUri("http://localhost")  
                            .Build();
```

> [!Note]
> Se si `http://localhost`configura , internamente MSAL.NET troveranno una porta aperta casuale e la utilizzeranno.

### <a name="linux-and-mac"></a>Linux e MAC

Su Linux, MSAL.NET aprirà il browser del sistema operativo predefinito utilizzando lo strumento xdg-open. Per risolvere i problemi, eseguire lo strumento da un terminale, ad esempio,`xdg-open "https://www.bing.com"`  
Su Mac, il browser viene aperto richiamando`open <url>`

### <a name="customizing-the-experience"></a>Personalizzazione dell'esperienza

> [!NOTE]
> La personalizzazione è disponibile in MSAL.NET 4.1.0 o versioni successive.

MSAL.NET è in grado di rispondere con un messaggio HTTP quando viene ricevuto un token o in caso di errore. È possibile visualizzare un messaggio HTML o reindirizzare a un URL di propria scelta:

```csharp
var options = new SystemWebViewOptions() 
{
    HtmlMessageError = "<p> An error occured: {0}. Details {1}</p>",
    BrowserRedirectSuccess = new Uri("https://www.microsoft.com");
}

await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
         .WithSystemWebViewOptions(options)
         .ExecuteAsync();
```

### <a name="opening-a-specific-browser-experimental"></a>Apertura di un browser specifico (sperimentale)

È possibile personalizzare il modo in cui MSAL.NET apre il browser. Ad esempio, invece di utilizzare qualsiasi browser è quello predefinito, è possibile forzare l'apertura di un browser specifico:

```csharp
var options = new SystemWebViewOptions() 
{
    OpenBrowserAsync = SystemWebViewOptions.OpenWithEdgeBrowserAsync
}
```

### <a name="uwp-doesnt-use-the-system-webview"></a>La piattaforma UWP non utilizza la visualizzazione Web di sistema

Per le applicazioni desktop, tuttavia, l'avvio di una visualizzazione Web di sistema porta a un'esperienza utente scadente, in quanto l'utente vede il browser, dove potrebbero già avere altre schede aperte. E quando l'autenticazione è avvenuta, gli utenti ottiene una pagina che chiede loro di chiudere questa finestra. Se l'utente non presta attenzione, può chiudere l'intero processo (incluse altre schede, che non sono correlate all'autenticazione). L'utilizzo del browser di sistema sul desktop richiederebbe anche l'apertura delle porte locali e l'ascolto su di esse, che potrebbero richiedere autorizzazioni avanzate per l'applicazione. L'utente, in qualità di sviluppatore, utente o amministratore, potrebbe essere riluttante a questo requisito.

## <a name="enable-embedded-webviews-on-ios-and-android"></a>Abilitare le visualizzazioni Web incorporate su iOS e Android

Puoi anche abilitare le visualizzazioni Web incorporate nelle app Xamarin.iOS e Xamarin.Android. A partire dallMSAL.NET 2.0.0-preview, MSAL.NET supporta anche l'utilizzo dell'opzione webview **incorporata.** Ad ADAL.NET, la visualizzazione Web incorporata è l'unica opzione supportata.

In qualità di sviluppatore che utilizza MSAL.NET targeting Xamarin, puoi scegliere di utilizzare visualizzazioni Web o browser di sistema incorporati. Questa è la tua scelta a seconda dell'esperienza utente e dei problemi di sicurezza che desideri indirizzare.

Attualmente, MSAL.NET non supporta ancora i broker Android e iOS. Pertanto, se è necessario fornire Single Sign-On (SSO), il browser di sistema potrebbe essere ancora un'opzione migliore. I broker di supporto con il browser web incorporato sono sul MSAL.NET backlog.

### <a name="differences-between-embedded-webview-and-system-browser"></a>Differenze tra visualizzazione Web incorporata e browser di sistema
Ci sono alcune differenze visive tra la visualizzazione Web incorporata e il browser di sistema in MSAL.NET.

**Accesso interattivo con MSAL.NET utilizzando la visualizzazione Web integrata:**

![incorporato](media/msal-net-web-browsers/embedded-webview.png)

**Accesso interattivo con MSAL.NET utilizzando il Browser di sistema:**

![Browser di sistema](media/msal-net-web-browsers/system-browser.png)

### <a name="developer-options"></a>Opzioni sviluppatore

Gli sviluppatori che usano MSAL.NET, sono disponibili diverse opzioni per la visualizzazione della finestra di dialogo interattiva dal servizio token di sicurezza:As a developer using MSAL.NET, you have several options for displaying the interactive dialog from STS:

- **Browser di sistema.** Il browser di sistema è impostato per impostazione predefinita nella libreria. Se si utilizza Android, leggere [i browser](msal-net-system-browser-android-considerations.md) di sistema per informazioni specifiche su quali browser sono supportati per l'autenticazione. Quando si utilizza il browser di sistema in Android, si consiglia il dispositivo dispone di un browser che supporta le schede personalizzate di Chrome.  In caso contrario, l'autenticazione potrebbe non riuscire.
- **Webview incorporato.** Per utilizzare solo la visualizzazione `AcquireTokenInteractively` Web incorporata `WithUseEmbeddedWebView()` in MSAL.NET, il generatore di parametri contiene un metodo.

    iOS

    ```csharp
    AuthenticationResult authResult;
    authResult = app.AcquireTokenInteractively(scopes)
                    .WithUseEmbeddedWebView(useEmbeddedWebview)
                    .ExecuteAsync();
    ```

    Android:

    ```csharp
    authResult = app.AcquireTokenInteractively(scopes)
                .WithParentActivityOrWindow(activity)
                .WithUseEmbeddedWebView(useEmbeddedWebview)
                .ExecuteAsync();
    ```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinios"></a>Scelta tra browser web incorporato o browser di sistema su Xamarin.iOS

Nell'app iOS, `AppDelegate.cs` in è `ParentWindow` `null`possibile inizializzare il su . Non viene utilizzato in iOS

```csharp
App.ParentWindow = null; // no UI parent on iOS
```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid"></a>Scelta tra browser web incorporato o browser di sistema su Xamarin.Android

Nella tua app `MainActivity.cs` Android, in puoi impostare l'attività padre, in modo che il risultato dell'autenticazione arrivi su di essa:

```csharp
 App.ParentWindow = this;
```

Poi nel `MainPage.xaml.cs`:

```csharp
authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(true)
                      .ExecuteAsync();
```

#### <a name="detecting-the-presence-of-custom-tabs-on-xamarinandroid"></a>Rilevamento della presenza di schede personalizzate su Xamarin.Android

Se si desidera utilizzare il browser web di sistema per abilitare SSO con le applicazioni in esecuzione nel browser, ma sono preoccupati per `IsSystemWebViewAvailable()` l'esperienza utente per i dispositivi Android non dispone di un browser con supporto scheda personalizzato, si ha la possibilità di decidere chiamando il metodo in `IPublicClientApplication`. Questo metodo `true` restituisce se il PackageManager rileva schede personalizzate e `false` se non vengono rilevate nel dispositivo.

In base al valore restituito da questo metodo e ai requisiti, è possibile prendere una decisione:Based on the value returned by this method, and your requirements, you can make a decision:

- È possibile restituire un messaggio di errore personalizzato all'utente. Ad esempio: "Installare Chrome per continuare con l'autenticazione" -OR-
- È possibile eseguire il rollback all'opzione di visualizzazione Web incorporata e avviare l'interfaccia utente come visualizzazione Web incorporata.

Il codice seguente mostra l'opzione webview incorporata:

```csharp
bool useSystemBrowser = app.IsSystemWebviewAvailable();

authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(!useSystemBrowser)
                      .ExecuteAsync();
```

#### <a name="net-core-doesnt-support-interactive-authentication-with-an-embedded-browser"></a>.NET Core non supporta l'autenticazione interattiva con un browser incorporato

Per .NET Core, l'acquisizione interattiva dei token è disponibile solo tramite il Web browser di sistema, non con le visualizzazioni Web incorporate. Infatti, .NET Core non fornisce ancora l'interfaccia utente.
Se si desidera personalizzare l'esperienza di navigazione con il browser web di sistema, è possibile implementare l'interfaccia [IWithCustomUI](scenario-desktop-acquire-token.md#withcustomwebui) e anche fornire il proprio browser.
