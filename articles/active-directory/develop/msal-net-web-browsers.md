---
title: Web browser in Microsoft Authentication Library per .NET | Azure
description: Per informazioni su considerazioni specifiche, vedere l'articolo relativo all'uso di Novell Android con Microsoft Authentication Library per .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1285c5c61cee25e387ca5fb598f0e062088e549
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532492"
---
# <a name="using-web-browsers-in-msalnet"></a>Uso di Web browser in MSAL.NET
I Web browser sono necessari per l'autenticazione interattiva. Per impostazione predefinita, MSAL.NET supporta il [Web browser di sistema](#system-web-browser-on-xamarinios-xamarinandroid) in Novell. iOS e Novell. Android. Tuttavia, [è anche possibile abilitare la Web browser incorporata](#enable-embedded-webviews-on-ios-and-android) in base alle esigenze (esperienza utente, necessità di accesso Single Sign-on (SSO), sicurezza) nelle app [Novell. iOS](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinios) e [Novell. Android](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) . È anche possibile [scegliere dinamicamente](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) il Web browser da usare in base alla presenza di Chrome o un browser che supporta le schede personalizzate Chrome in Android. MSAL.NET supporta solo il browser di sistema nelle applicazioni desktop .NET Core.

## <a name="web-browsers-in-msalnet"></a>Web browser in MSAL.NET

### <a name="interaction-happens-in-a-web-browser"></a>L'interazione avviene in un Web browser

Quando si acquisisce un token in modo interattivo, è importante comprendere che il contenuto della finestra di dialogo non viene fornito dalla libreria, ma dal servizio token di sicurezza. L'endpoint di autenticazione restituisce codice HTML e JavaScript che controlla l'interazione, di cui viene eseguito il rendering in un Web browser o in un controllo Web. Consentire al servizio token di gestione di gestire l'interazione HTML presenta molti vantaggi:

- La password (se una è stata digitata) non viene mai archiviata dall'applicazione, né dalla libreria di autenticazione.
- Consente il reindirizzamento ad altri provider di identità (ad esempio, l'accesso con un account aziendale o un account personale con MSAL o con un account di social networking con Azure AD B2C).
- Consente l'accesso condizionale del controllo STS, ad esempio, con l'autenticazione a più fattori (multi-factor authentication) durante la fase di autenticazione (immissione di un pin di Windows Hello o chiamata sul telefono o in un'app di autenticazione sul telefono). Nei casi in cui l'autenticazione a più fattori necessaria non è ancora stata configurata, l'utente può configurarla solo in tempo nella stessa finestra di dialogo.  L'utente immette il proprio numero di telefono cellulare ed è guidato a installare un'applicazione di autenticazione ed eseguire la scansione di un tag QR per aggiungere il proprio account. Questa interazione basata su server è un'ottima esperienza.
- Consente all'utente di modificare la password nella stessa finestra di dialogo alla scadenza della password, fornendo campi aggiuntivi per la vecchia password e la nuova password.
- Abilita la personalizzazione del tenant o l'applicazione (immagini) controllata dal proprietario dell'applicazione o dall'amministratore del tenant Azure AD.
- Consente agli utenti di consentire all'applicazione di accedere alle risorse o agli ambiti nel nome subito dopo l'autenticazione.

### <a name="embedded-vs-system-web-ui"></a>Interfaccia utente Web di Visual Studio System incorporato

MSAL.NET è una libreria multiframework e contiene codice specifico del Framework per ospitare un browser in un controllo dell'interfaccia utente (ad esempio, in .NET classico USA WinForms, in Novell usa controlli mobili nativi e così via). Questo controllo è denominato `embedded` interfaccia utente Web. In alternativa, MSAL.NET è anche in grado di avviare il browser sistema operativo.

In genere, è consigliabile usare il valore predefinito della piattaforma, che in genere è il browser del sistema. Il browser di sistema è migliore per ricordare gli utenti che hanno eseguito l'accesso in precedenza. Se è necessario modificare questo comportamento, usare`WithUseEmbeddedWebView(bool)`

### <a name="at-a-glance"></a>Riepilogo

| Framework        | Embedded | Sistema | Predefinito |
| ------------- |-------------| -----| ----- |
| .NET classico     | Yes | Sì ^ | Embedded |
| .NET Core     | No | Sì ^ | Sistema |
| .NET Standard | No | Sì ^ | Sistema |
| UWP | Sì | No | Embedded |
| Xamarin.Android | Yes | Sì  | Sistema |
| Xamarin.iOS | Yes | Yes  | Sistema |
| Xamarin.Mac| Sì | No | Embedded |

^ Richiede l'http://localhost URI di reindirizzamento ""

## <a name="system-web-browser-on-xamarinios-xamarinandroid"></a>Web browser di sistema in Novell. iOS, Novell. Android

Per impostazione predefinita, MSAL.NET supporta il Web browser di sistema in Novell. iOS, Novell. Android e .NET Core. Per tutte le piattaforme che forniscono l'interfaccia utente (ovvero, non .NET Core), una finestra di dialogo viene fornita dalla libreria che incorpora un controllo Web browser. MSAL.NET usa anche una visualizzazione Web incorporata per il desktop .NET e WAB per la piattaforma UWP. Tuttavia, utilizza per impostazione predefinita il **Web browser di sistema** per le applicazioni Novell iOS e Novell Android. In iOS, viene anche scelta la visualizzazione Web da usare a seconda della versione del sistema operativo (iOS12, iOS11 e versioni precedenti).

L'utilizzo del browser di sistema offre il vantaggio significativo della condivisione dello stato SSO con altre applicazioni e applicazioni Web senza la necessità di un broker (portale aziendale/autenticatore). Il browser di sistema è stato usato, per impostazione predefinita, in MSAL.NET per le piattaforme Android Novell iOS e Novell perché, su queste piattaforme, il Web browser di sistema occupa l'intero schermo e l'esperienza utente è migliore. La visualizzazione Web di sistema non è distinguibile da una finestra di dialogo. In iOS, tuttavia, l'utente potrebbe dover dare il consenso al browser per richiamare l'applicazione, il che può essere fastidioso.

## <a name="system-browser-experience-on-net-core"></a>Esperienza del browser di sistema in .NET Core

In .NET Core, MSAL.NET avvierà il browser di sistema come processo separato. MSAL.NET non ha il controllo su questo browser, ma una volta che l'utente ha completato l'autenticazione, la pagina Web viene reindirizzata in modo tale che MSAL.NET possa intercettare l'URI.

È anche possibile configurare le app scritte per .NET classico per l'uso di questo browser, specificando

```csharp
await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
```

MSAL.NET non è in grado di rilevare se l'utente si sposta o semplicemente chiude il browser. Le app che usano questa tecnica sono consigliate per definire un `CancellationToken`timeout (via). Si consiglia un timeout di almeno pochi minuti per tenere conto dei casi in cui all'utente viene richiesto di modificare la password o di eseguire l'autenticazione a più fattori.

### <a name="how-to-use-the-default-os-browser"></a>Come usare il browser del sistema operativo predefinito

MSAL.NET deve restare in ascolto `http://localhost:port` e intercettare il codice inviato da AAD quando l'utente esegue l'autenticazione (vedere il [codice di autorizzazione](v2-oauth2-auth-code-flow.md) per i dettagli)

Per abilitare il browser di sistema:

1. Durante la registrazione dell'app `http://localhost` , configurare come URI di reindirizzamento (attualmente non supportato da B2C)
2. Quando si crea la PublicClientApplication, specificare questo URI di reindirizzamento:

```csharp
IPublicClientApplication pca = PublicClientApplicationBuilder
                            .Create("<CLIENT_ID>")
                             // or use a known port if you wish "http://localhost:1234"
                            .WithRedirectUri("http://localhost")  
                            .Build();
```

> [!Note]
> Se si configura `http://localhost`, MSAL.NET internamente troverà una porta aperta casuale e la utilizzerà.

### <a name="linux-and-mac"></a>Linux e MAC

In Linux MSAL.NET aprirà il browser del sistema operativo predefinito usando lo strumento xdg-open. Per risolvere i problemi, eseguire lo strumento da un terminale, ad esempio`xdg-open "https://www.bing.com"`  
Sul Mac, il browser viene aperto richiamando`open <url>`

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

È possibile personalizzare il modo in cui MSAL.NET apre il browser. Ad esempio, invece di usare qualsiasi browser è l'impostazione predefinita, è possibile forzare l'apertura di un browser specifico:

```csharp
var options = new SystemWebViewOptions() 
{
    OpenBrowserAsync = SystemWebViewOptions.OpenWithEdgeBrowserAsync
}
```

### <a name="uwp-doesnt-use-the-system-webview"></a>UWP non usa la visualizzazione WebView di sistema

Per le applicazioni desktop, tuttavia, l'avvio di un sistema WebView conduce a un'esperienza utente comma, in quanto l'utente Visualizza il browser, dove potrebbero avere già altre schede aperte. Quando si verifica l'autenticazione, gli utenti ricevono una pagina che chiede di chiudere la finestra. Se l'utente non presta attenzione, può chiudere l'intero processo (incluse le altre schede, che non sono correlate all'autenticazione). Per sfruttare il browser di sistema sul desktop è inoltre necessario aprire le porte locali e restare in ascolto su di esse, che potrebbero richiedere autorizzazioni avanzate per l'applicazione. Gli sviluppatori, gli utenti o gli amministratori potrebbero essere riluttanti a questo requisito.

## <a name="enable-embedded-webviews-on-ios-and-android"></a>Abilitare WebView incorporati in iOS e Android

È anche possibile abilitare le visualizzazioni Web incorporate nelle app Novell. iOS e Novell. Android. A partire da MSAL.NET 2.0.0-Preview, MSAL.NET supporta anche l' uso dell'opzione Embedded WebView. Per ADAL.NET, embedded WebView è l'unica opzione supportata.

Gli sviluppatori che usano MSAL.NET con destinazione Novell possono scegliere di usare Webviews o browser di sistema incorporati. Questa è la scelta a seconda dell'esperienza utente e dei problemi di sicurezza che si vuole usare come destinazione.

Attualmente, MSAL.NET non supporta ancora i broker Android e iOS. Pertanto, se è necessario fornire l'accesso Single Sign-on (SSO), il browser di sistema potrebbe essere ancora un'opzione migliore. Il supporto di broker con il Web browser incorporato si trova nel backlog MSAL.NET.

### <a name="differences-between-embedded-webview-and-system-browser"></a>Differenze tra WebView incorporato e browser di sistema
Esistono alcune differenze visive tra WebView incorporato e browser di sistema in MSAL.NET.

**Accesso interattivo con MSAL.NET usando la WebView incorporata:**

![Embedded](media/msal-net-web-browsers/embedded-webview.png)

**Accesso interattivo con MSAL.NET tramite il browser di sistema:**

![Browser di sistema](media/msal-net-web-browsers/system-browser.png)

### <a name="developer-options"></a>Opzioni per gli sviluppatori

Gli sviluppatori che usano MSAL.NET sono disponibili diverse opzioni per la visualizzazione della finestra di dialogo interattiva da STS:

- **Browser del sistema.** Il browser di sistema è impostato per impostazione predefinita nella libreria. Se si usa Android, vedere [browser di sistema](msal-net-system-browser-android-considerations.md) per informazioni specifiche sui browser supportati per l'autenticazione. Quando si usa il browser di sistema in Android, è consigliabile che il dispositivo disponga di un browser che supporta le schede personalizzate Chrome.  In caso contrario, l'autenticazione potrebbe non riuscire.
- **Visualizzazione WebView incorporata.** Per usare solo WebView incorporato in MSAL.NET, il `AcquireTokenInteractively` generatore di parametri contiene `WithUseEmbeddedWebView()` un metodo.

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

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinios"></a>Scelta tra browser incorporato o browser di sistema in Novell. iOS

Nell'app iOS in `AppDelegate.cs` è possibile inizializzare il `ParentWindow` su `null`. Non viene usato in iOS

```csharp
App.ParentWindow = null; // no UI parent on iOS
```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid"></a>Scelta tra browser incorporato o browser di sistema in Novell. Android

Nell'app Android, in è `MainActivity.cs` possibile impostare l'attività padre, in modo che il risultato dell'autenticazione torni a esso:

```csharp
 App.ParentWindow = this;
```

Quindi nel `MainPage.xaml.cs`:

```csharp
authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(true)
                      .ExecuteAsync();
```

#### <a name="detecting-the-presence-of-custom-tabs-on-xamarinandroid"></a>Rilevamento della presenza di schede personalizzate in Novell. Android

Se si vuole usare il Web browser di sistema per abilitare l'accesso SSO con le app in esecuzione nel browser, ma si è preoccupati dell'esperienza utente per i dispositivi Android che non dispongono di un browser con supporto per le schede personalizzato, è possibile `IsSystemWebViewAvailable()` scegliere di decidere chiamando il metodo in < c 2 `IPublicClientApplication` . Questo metodo restituisce `true` se il PackageManager rileva schede personalizzate e `false` se non vengono rilevate nel dispositivo.

In base al valore restituito da questo metodo e ai requisiti, è possibile prendere una decisione:

- È possibile restituire all'utente un messaggio di errore personalizzato. Ad esempio:  "Per continuare l'autenticazione, installare Chrome"-o-
- È possibile eseguire il fallback all'opzione Embedded WebView e avviare l'interfaccia utente come WebView incorporata.

Il codice seguente mostra l'opzione incorporata WebView:

```csharp
bool useSystemBrowser = app.IsSystemWebviewAvailable();

authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(!useSystemBrowser)
                      .ExecuteAsync();
```

#### <a name="net-core-doesnt-support-interactive-authentication-with-an-embedded-browser"></a>.NET Core non supporta l'autenticazione interattiva con un browser incorporato

Per .NET Core, l'acquisizione di token in modo interattivo è disponibile solo tramite il Web browser di sistema, non con le visualizzazioni Web incorporate. In realtà, .NET Core non fornisce ancora l'interfaccia utente.
Per personalizzare l'esperienza di esplorazione con il Web browser di sistema, è possibile implementare l'interfaccia [IWithCustomUI](scenario-desktop-acquire-token.md#withcustomwebui) e persino fornire un browser personalizzato.
