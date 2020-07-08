---
title: Uso dei Web browser (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su considerazioni specifiche per l'uso di Xamarin Android con Microsoft Authentication Library per .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 4e62536b610595c7a53eb8333f06f147e628dec7
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83772047"
---
# <a name="using-web-browsers-msalnet"></a>Uso dei Web browser (MSAL.NET)

I Web browser sono necessari per l'autenticazione interattiva. Per impostazione predefinita, MSAL.NET supporta il [Web browser di sistema](#system-web-browser-on-xamarinios-xamarinandroid) in Xamarin.iOS e Xamarin.Android. Tuttavia, [è possibile abilitare il Web browser incorporato](#enable-embedded-webviews-on-ios-and-android) in base ai requisiti (esperienza utente, necessità di accesso Single Sign-On (SSO), sicurezza) nelle app [Xamarin.iOS](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinios) e [Xamarin.Android](#detecting-the-presence-of-custom-tabs-on-xamarinandroid). Ed è persino possibile [scegliere in modo dinamico](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) il Web browser da usare in base alla presenza di Chrome o di un browser che supporta le schede personalizzate di Chrome in Android. MSAL.NET supporta solo il browser di sistema nelle applicazioni desktop .NET Core.

## <a name="web-browsers-in-msalnet"></a>Web browser in MSAL.NET

### <a name="interaction-happens-in-a-web-browser"></a>L'interazione avviene in un Web browser

Quando si acquisisce un token in modo interattivo, è importante comprendere che il contenuto della finestra di dialogo non viene fornito dalla libreria, ma dal servizio token di sicurezza. L'endpoint di autenticazione restituisce il codice HTML e JavaScript che controlla l'interazione, il cui rendering viene eseguito in un Web browser o in un controllo Web. Consentire al servizio token di sicurezza di gestire l'interazione HTML presenta molti vantaggi:

- La password (se ne è stata digitata una) non viene mai archiviata dall'applicazione, né dalla libreria di autenticazione.
- Consente i reindirizzamenti ad altri provider di identità, ad esempio per l'accesso tramite account aziendale o dell'istituto di istruzione o account personale con MSAL oppure tramite un account di social network con Azure AD B2C.
- Consente al servizio token di sicurezza di controllare l'accesso condizionale, ad esempio, affinché l'utente esegua l'[autenticazione a più fattori (MFA)](../authentication/concept-mfa-howitworks.md) durante la fase di autenticazione mediante l'immissione di un codice PIN di Windows Hello, una chiamata sul telefono o un'app di autenticazione sul telefono. Nei casi in cui l'autenticazione a più fattori non è ancora stata configurata, l'utente può configurarla immediatamente nella stessa finestra di dialogo.  L'utente immette il proprio numero di telefono cellulare e viene guidato durante la procedura di installazione di un'applicazione di autenticazione di scansione di un tag a matrice per aggiungere il proprio account. Questa interazione guidata dal server è un'ottima esperienza.
- Consente all'utente di modificare la password nella stessa finestra di dialogo alla scadenza della password, fornendo campi aggiuntivi per la password precedente e quella nuova.
- Consente la personalizzazione del tenant o dell'applicazione (immagini) controllata dal proprietario dell'applicazione o dall'amministratore del tenant di Azure AD.
- Consente agli utenti di fornire il consenso per permettere all'applicazione di accedere alle risorse o agli ambiti per loro conto subito dopo l'autenticazione.

### <a name="embedded-vs-system-web-ui"></a>Interfaccia utente Web di sistema e incorporata

MSAL.NET è una libreria multiframework e contiene codice specifico del framework per ospitare un browser in un controllo dell'interfaccia utente (ad esempio, in .NET Classic usa WinForms, in Xamarin usa controlli per dispositivi mobili nativi e così via). Questo controllo viene chiamato interfaccia utente Web `embedded`. In alternativa, MSAL.NET può anche avviare il browser del sistema operativo.

In genere, è consigliabile usare l'impostazione predefinita della piattaforma, ovvero il browser del sistema. Il browser di sistema offre prestazioni migliori nel ricordare gli utenti che hanno eseguito l'accesso in precedenza. Se è necessario modificare questo comportamento, usare `WithUseEmbeddedWebView(bool)`

### <a name="at-a-glance"></a>Riepilogo

| Framework        | Origine dati | Sistema | Predefinito |
| ------------- |-------------| -----| ----- |
| .NET Classic     | Sì | Sì^ | Origine dati |
| .NET Core     | No | Sì^ | Sistema |
| .NET Standard | No | Sì^ | Sistema |
| UWP | Sì | No | Origine dati |
| Xamarin.Android | Sì | Sì  | Sistema |
| Xamarin.iOS | Sì | Sì  | Sistema |
| Xamarin.Mac| Sì | No | Origine dati |

^ richiede l'URI di reindirizzamento "http://localhost"

## <a name="system-web-browser-on-xamarinios-xamarinandroid"></a>Web browser di sistema in Xamarin.iOS, Xamarin.Android

Per impostazione predefinita, MSAL.NET supporta il Web browser di sistema in Xamarin.iOS, Xamarin.Android e .NET Core. Per tutte le piattaforme che forniscono l'interfaccia utente (ovvero, non .NET Core), viene fornita una finestra di dialogo dalla libreria che incorpora un controllo Web browser. MSAL.NET usa anche una visualizzazione Web incorporata per .NET Desktop e WAB per la piattaforma UWP. Tuttavia, per impostazione predefinita sfrutta il **Web browser di sistema** per le applicazioni Xamarin iOS e Xamarin Android. In iOS sceglie anche la visualizzazione Web da usare a seconda della versione del sistema operativo (iOS12, iOS11 e versioni precedenti).

L'utilizzo del browser di sistema offre il vantaggio significativo della condivisione dello stato dell'accesso SSO con altre applicazioni e le applicazioni Web senza la necessità di un broker (Portale aziendale/Authenticator). Il browser di sistema è stato usato, per impostazione predefinita, in MSAL.NET per le piattaforme Xamarin iOS e Xamarin Android perché, in queste piattaforme il Web browser di sistema occupa l'intero schermo e offre un'esperienza utente migliore. La visualizzazione Web di sistema non è si distingue da una finestra di dialogo. In iOS, tuttavia, l'utente potrebbe dover fornire il consenso affinché il browser richiami l'applicazione, rendendo così l'esperienza meno piacevole.

## <a name="system-browser-experience-on-net-core"></a>Esperienza del browser di sistema in .NET Core

In .NET Core MSAL.NET avvierà il browser di sistema come processo separato. MSAL.NET non ha il controllo su questo browser, ma non appena l'utente completa l'autenticazione, la pagina Web viene reindirizzata in modo tale che MSAL.NET possa intercettare l'URI.

È anche possibile configurare le app scritte per .NET Classic affinché usino questo browser, specificando

```csharp
await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
```

MSAL.NET non può rilevare se l'utente si sposta in un'altra pagina o semplicemente chiude il browser. Per le app che usano questa tecnica è consigliabile definire un timeout (tramite `CancellationToken`). Si consiglia di impostare un timeout di almeno pochi minuti per gestire i casi in cui all'utente viene richiesto di modificare la password o di eseguire l'autenticazione a più fattori.

### <a name="how-to-use-the-default-os-browser"></a>Come usare il browser del sistema operativo predefinito

MSAL.NET deve restare in ascolto su `http://localhost:port` e intercettare il codice inviato da AAD quando l'utente completa l'autenticazione. Per informazioni dettagliate, vedere [Codice di autorizzazione](v2-oauth2-auth-code-flow.md)

Per abilitare il browser di sistema:

1. Durante la registrazione dell'app, configurare `http://localhost` come URI di reindirizzamento (attualmente non supportato da B2C)
2. Quando si crea l'applicazione PublicClientApplication, specificare questo URI di reindirizzamento:

```csharp
IPublicClientApplication pca = PublicClientApplicationBuilder
                            .Create("<CLIENT_ID>")
                             // or use a known port if you wish "http://localhost:1234"
                            .WithRedirectUri("http://localhost")  
                            .Build();
```

> [!Note]
> Se si configura `http://localhost`, MSAL.NET internamente troverà una porta aperta casuale e la userà.

### <a name="linux-and-mac"></a>Linux e Mac

In Linux MSAL.NET aprirà il browser del sistema operativo predefinito usando lo strumento xdg-open. Per la risoluzione dei problemi, eseguire lo strumento da un terminale, ad esempio `xdg-open "https://www.bing.com"`. In Mac il browser viene aperto richiamando `open <url>`.

### <a name="customizing-the-experience"></a>Personalizzazione dell'esperienza

> [!NOTE]
> La personalizzazione è disponibile in MSAL.NET 4.1.0 o versioni successive.

MSAL.NET può rispondere con un messaggio HTTP quando viene ricevuto un token o in caso di errore. È possibile visualizzare un messaggio HTML o reindirizzare a un URL di propria scelta:

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

È possibile personalizzare il modo in cui MSAL.NET apre il browser. Ad esempio, anziché usare il browser predefinito, è possibile forzare l'apertura di un browser specifico:

```csharp
var options = new SystemWebViewOptions() 
{
    OpenBrowserAsync = SystemWebViewOptions.OpenWithEdgeBrowserAsync
}
```

### <a name="uwp-doesnt-use-the-system-webview"></a>La piattaforma UWP non usa la visualizzazione Web di sistema

Per le applicazioni desktop, tuttavia, l'avvio di una visualizzazione Web di sistema determina un'esperienza utente di scarsa qualità, perché quando viene visualizzato il browser potrebbero essere presenti altre schede aperte. Al termine dell'autenticazione, all'utente viene mostrata una pagina in cui viene richiesto di chiudere la finestra. Se l'utente non presta attenzione, rischia di chiudere l'intero processo (incluse le altre schede, che non sono correlate all'autenticazione). Per sfruttare il browser di sistema in un computer desktop è inoltre necessario aprire le porte locali e restare in ascolto su di esse e queste operazioni potrebbero richiedere autorizzazioni avanzate per l'applicazione. Gli sviluppatori, gli utenti o gli amministratori potrebbero essere riluttanti ad accettare questo requisito.

## <a name="enable-embedded-webviews-on-ios-and-android"></a>Abilitare le visualizzazioni Web incorporate in iOS e Android

È anche possibile abilitare le visualizzazioni Web incorporate nelle app Xamarin.iOS e Xamarin.Android. A partire da MSAL.NET 2.0.0-preview, MSAL.NET supporta anche l'uso dell'opzione per la visualizzazione Web **incorporata**. Per ADAL.NET, la visualizzazione Web incorporata è l'unica opzione supportata.

Gli sviluppatori che usano MSAL.NET per Xamarin possono scegliere di usare le visualizzazioni Web o i browser di sistema incorporati. Sarà necessario scegliere a seconda dell'esperienza utente che si vuole offrire e delle problematiche relative alla sicurezza.

Attualmente, MSAL.NET non supporta ancora i broker per Android e iOS. Se è quindi necessario fornire l'accesso Single Sign-On (SSO), il browser di sistema sarà la migliore opzione disponibile. Il supporto dei broker con il Web browser incorporato è incluso nel backlog di MSAL.NET.

### <a name="differences-between-embedded-webview-and-system-browser"></a>Differenze tra visualizzazioni Web incorporate e browser di sistema
Esistono alcune differenze visive tra la visualizzazione Web incorporata e il browser di sistema in MSAL.NET.

**Accesso interattivo con MSAL.NET tramite la visualizzazione Web incorporata:**

![incorporata](media/msal-net-web-browsers/embedded-webview.png)

**Accesso interattivo con MSAL.NET tramite il browser di sistema:**

![Browser di sistema](media/msal-net-web-browsers/system-browser.png)

### <a name="developer-options"></a>Opzioni per sviluppatori

Per gli sviluppatori che usano MSAL.NET sono disponibili numerose opzioni per la visualizzazione della finestra di dialogo interattiva dal servizio token di sicurezza:

- **Browser di sistema.** Il browser di sistema è definito per impostazione predefinita nella libreria. Se si usa Android, leggere [browser di sistema](msal-net-system-browser-android-considerations.md) per informazioni specifiche sui browser supportati per l'autenticazione. Quando si usa il browser di sistema in Android, è opportuno che nel dispositivo sia disponibile un browser che supporti le schede personalizzate di Chrome.  In caso contrario, l'autenticazione potrebbe non riuscire.
- **Visualizzazione Web incorporata.** Per usare solo la visualizzazione Web incorporata in MSAL.NET, il generatore di parametri `AcquireTokenInteractively` contiene un metodo `WithUseEmbeddedWebView()`.

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

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinios"></a>Scegliere tra browser Web incorporato o browser di sistema in Xamarin.iOS

Nell'app iOS in `AppDelegate.cs` è possibile inizializzare la finestra `ParentWindow` su `null`. Non viene usata in iOS

```csharp
App.ParentWindow = null; // no UI parent on iOS
```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid"></a>Scegliere tra browser Web incorporato o browser di sistema in Xamarin.Android

Nell'app Android in `MainActivity.cs` è possibile impostare l'attività padre, affinché le venga restituito il risultato dell'autenticazione:

```csharp
 App.ParentWindow = this;
```

Quindi in `MainPage.xaml.cs`:

```csharp
authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(true)
                      .ExecuteAsync();
```

#### <a name="detecting-the-presence-of-custom-tabs-on-xamarinandroid"></a>Rilevamento della presenza di schede personalizzate in Xamarin.Android

Se si vuole usare il Web browser di sistema per abilitare l'accesso SSO con le app in esecuzione nel browser, ma si è preoccupati dell'esperienza utente per i dispositivi Android che non dispongono di un browser con supporto per le schede personalizzate, è possibile decidere chiamando il metodo `IsSystemWebViewAvailable()` in `IPublicClientApplication`. Questo metodo restituisce `true` se PackageManager rileva le schede personalizzate e `false` se non vengono rilevate nel dispositivo.

In base al valore restituito da questo metodo e ai requisiti, è possibile prendere una decisione:

- È possibile restituire all'utente un messaggio di errore personalizzato. Ad esempio: "Per procedere con l'autenticazione, installare Chrome" oppure
- È possibile eseguire il fallback all'opzione visualizzazione Web incorporata e avviare l'interfaccia utente come visualizzazione Web incorporata.

Il codice seguente mostra l'opzione visualizzazione Web incorporata:

```csharp
bool useSystemBrowser = app.IsSystemWebviewAvailable();

authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(!useSystemBrowser)
                      .ExecuteAsync();
```

#### <a name="net-core-doesnt-support-interactive-authentication-with-an-embedded-browser"></a>.NET Core non supporta l'autenticazione interattiva con un browser incorporato

Per .NET Core, l'acquisizione di token in modo interattivo è disponibile solo tramite il Web browser di sistema, non con le visualizzazioni Web incorporate. In realtà, .NET Core non fornisce ancora l'interfaccia utente.
Per personalizzare l'esperienza di esplorazione con il Web browser di sistema, è possibile implementare l'interfaccia [IWithCustomUI](scenario-desktop-acquire-token.md#withcustomwebui) e anche specificare il browser.
