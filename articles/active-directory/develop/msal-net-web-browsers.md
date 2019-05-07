---
title: Web browser in Microsoft Authentication Library per .NET | Azure
description: Informazioni sulle considerazioni specifiche quando si usa Xamarin. Android con Microsoft Authentication Library per .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 350cb3fec4d325d6cf5848733c0bae18d5efacca
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076841"
---
# <a name="using-web-browsers-in-msalnet"></a>Uso di web browser in MSAL.NET
Web browser sono necessari per l'autenticazione interattiva. Per impostazione predefinita, MSAL.NET supporta il [browser web del sistema](#system-web-browser-on-xamarinios-and-xamarinandroid) in xamarin. IOS e [xamarin. Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/system-browser). Ma [è anche possibile abilitare il browser Web incorporato](#enable-embedded-webviews) a seconda dei requisiti (esperienza utente, è necessario per single sign-on (SSO), sicurezza) in [xamarin. IOS](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinios) e [xamarin. Android](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid) app. E può persino [scegliere in modo dinamico](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) il browser web da utilizzare in base alla presenza di Chrome o un browser che supportano le schede personalizzate di Chrome in Android.

## <a name="web-browsers-in-msalnet"></a>Web browser in MSAL.NET

È importante comprendere che, quando l'acquisizione di un token in modo interattivo, il contenuto della finestra di dialogo non viene fornito dalla libreria, ma il servizio STS (servizio Token di sicurezza). L'endpoint di autenticazione invia di nuovo alcune HTML e JavaScript che controlla l'interazione, che viene eseguito il rendering in un web browser o un controllo web. Consentendo il servizio token di sicurezza gestire l'interazione di HTML presenta numerosi vantaggi:

- La password (se uno è stato digitato) non viene mai archiviata dall'applicazione, né la libreria di autenticazione.
- Abilita reindirizzamenti da altri provider di identità (ad esempio account di accesso aggiuntivo con un account dell'istituto di istruzione di lavoro o un account personale con MSAL o con un account di social networking con Azure AD B2C).
- Consente il servizio token di sicurezza controllo dell'accesso condizionale, ad esempio, facendo in modo che l'autenticazione utente a più fattori (MFA) durante la fase di autenticazione (immettere un pin di Windows Hello o chiamata sul telefono, o in un'app di autenticazione sul telefono). Nei casi in cui i necessari multi-factor authentication non è configurato, ancora, l'utente può configurarlo JIT nella finestra di dialogo stessa.  L'utente immette il numero di telefono cellulare e viene guidato per installare un'applicazione di autenticazione e analisi di un tag di codici a matrice per aggiungere il proprio account. Basato su interazione tra il server è un'esperienza ottimale.
- Consente all'utente di cambiare la password in questa finestra di dialogo stessa quando la password è scaduta (purché campi aggiuntivi per la vecchia password e la nuova password).
- Abilita personalizzazione dei tenant o l'applicazione (immagini) controllata dall'amministratore del tenant di Azure AD / proprietario dell'applicazione.
- Consente agli utenti di fornire il consenso per permettere all'applicazione di accedere alle risorse / ambiti nel loro nome subito dopo l'autenticazione.

## <a name="system-web-browser-on-xamarinios-and-xamarinandroid"></a>Sistema web browser in xamarin. IOS e xamarin. Android

Per impostazione predefinita, MSAL.NET supporta il web browser del sistema in xamarin. IOS e xamarin. Android. Per ospitare l'interazione con il servizio token di sicurezza, ADAL.NET Usa solo il **embedded** browser web. Per tutte le piattaforme che forniscono l'interfaccia utente (ovvero, non .NET Core), una finestra di dialogo viene fornito dalla libreria di incorporamento di un controllo Web browser. MSAL.NET usa anche una visualizzazione web incorporati per il Desktop di .NET e della Rubrica di Windows per la piattaforma UWP. Per impostazione predefinita, tuttavia, sfrutta il **browser web del sistema** per Xamarin iOS e le applicazioni Xamarin. Android. In iOS, che sceglie anche la visualizzazione web da utilizzare a seconda della versione del sistema operativo (iOS12, iOS11 e versioni precedenti).

Usando il browser del sistema ha il significativo vantaggio di condividere lo stato SSO con altre applicazioni e con le applicazioni web senza la necessità di un broker (portale aziendale / Authenticator). Il browser del sistema è stato usato, per impostazione predefinita, nella finestra di MSAL.NET per le piattaforme di Xamarin. Android e Xamarin iOS perché, in queste piattaforme, il web browser del sistema occupa l'intero schermo e l'esperienza utente è migliore. La visualizzazione web di sistema non è distinguibile da una finestra di dialogo. In iOS, tuttavia, l'utente potrebbe essere necessario dare il consenso per il browser richiamare l'applicazione, che può risultare fastidiosa.

### <a name="uwp-does-not-use-the-system-webview"></a>Piattaforma UWP non utilizzato System Webview

Per le applicazioni desktop, tuttavia, l'avvio di un System Webview comporta un'esperienza utente restituendo, come l'utente vede il browser, in cui si dispone già di altre schede aperti. E quando è stato eseguito l'autenticazione, gli utenti viene visualizzata una pagina in cui viene chiesto di chiudere questa finestra. Se l'utente non prestare attenzione, è possibile chiudere l'intero processo (incluse altre schede, che non sono correlate all'autenticazione). Sfruttando il browser del sistema sul desktop anche richiederebbe l'apertura di porte locali e in ascolto su di essi, che potrebbe richiedere autorizzazioni avanzate per l'applicazione. Come uno sviluppatore, un utente o un amministratore, potrebbe essere riluttanti su questo requisito.

## <a name="enable-embedded-webviews"></a>Abilita visualizzazioni Web incorporate 
È anche possibile abilitare visualizzazioni Web incorporate nelle App xamarin. IOS e xamarin. Android. A partire da MSAL.NET 2.0.0-preview, MSAL.NET supporta anche l'utilizzo di **embedded** opzione webview. Per ADAL.NET, webview incorporato è l'unica opzione supportata.

Gli sviluppatori tramite MSAL.NET destinati a Xamarin, è possibile scegliere di usare visualizzazioni Web incorporate o browser del sistema. Questa è la scelta in base i problemi di sicurezza e l'esperienza utente di destinazione.

Attualmente, MSAL.NET non supporta ancora il broker di Android e iOS. Pertanto se è necessario fornire accesso single sign-on (SSO), il browser del sistema potrebbe essere ancora un'opzione migliore. È nel backlog MSAL.NET supportare Broker con i browser web incorporato.

### <a name="differences-between-embedded-webview-and-system-browser"></a>Differenze tra webview incorporato e browser del sistema 
Esistono alcune differenze visual tra webview incorporato e browser del sistema in MSAL.NET.

**Interactive sign aggiuntivo con MSAL.NET usando Webview incorporato:**

![Embedded](media/msal-net-web-browsers/embedded-webview.png)

**Interactive sign-in con MSAL.NET usando il Browser del sistema:**

![Browser del sistema](media/msal-net-web-browsers/system-browser.png)

### <a name="developer-options"></a>Opzioni per gli sviluppatori

Gli sviluppatori tramite MSAL.NET sono disponibili diverse opzioni per visualizzare la finestra di dialogo interattivo dal servizio token di sicurezza:

- **Browser del sistema.** Il browser del sistema è impostato per impostazione predefinita nella libreria. Se si usa Android, leggere [browser sistema](msal-net-system-browser-android-considerations.md) per informazioni specifiche sui browser supportati per l'autenticazione. Quando si usa il browser del sistema in Android, è consigliabile che il dispositivo dispone di un browser che supporta le schede personalizzate di Chrome.  In caso contrario, l'autenticazione può non riuscire. 
- **Webview incorporato.** Per usare solo incorporato webview in MSAL.NET, esistono overload del `UIParent()` costruttore disponibili per Android e iOS.

    iOS:

    ```csharp
    public UIParent(bool useEmbeddedWebview)
    ```

    Android:

    ```csharp
    public UIParent(Activity activity, bool useEmbeddedWebview)
    ```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinios"></a>Scelta tra browser web incorporato o browser del sistema in xamarin. IOS

Nell'app iOS in `AppDelegate.cs` è possibile utilizzare browser del sistema o webview incorporato.

```csharp
// Use only embedded webview
App.UIParent = new UIParent(true);

// Use only system browser
App.UIParent = new UIParent();
```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid"></a>Scelta tra browser web incorporato o browser del sistema in xamarin. Android

Nell'app per Android, in `MainActivity.cs` è possibile decidere come implementare le opzioni di visualizzazione Web.

```csharp
// Use only embedded webview
App.UIParent = new UIParent(Xamarin.Forms.Forms.Context as Activity, true);

// or
// Use only system browser
App.UIParent = new UIParent(Xamarin.Forms.Forms.Context as Activity);
```

#### <a name="detecting-the-presence-of-custom-tabs-on-xamarinandroid"></a>Rilevare la presenza di schede personalizzate in xamarin. Android

Se si vuole usare il web browser del sistema per abilitare l'accesso SSO con le App in esecuzione nel browser, ma si preoccupa sull'esperienza dell'utente per i dispositivi Android senza che sia un browser con il supporto di scheda personalizzata, è possibile decidere chiamando il `IsSystemWebViewAvailable()` metodo < c 2 > `UIParent` . Questo metodo restituisce `true` se il PackageManager rileva le schede personalizzate e `false` se non vengono rilevate nel dispositivo.

In base al valore restituito da questo metodo e ai requisiti, è possibile prendere una decisione:

- È possibile restituire un messaggio di errore personalizzato per l'utente. Ad esempio:  "Installare Chrome per continuare con l'autenticazione" - OR-
- È possibile eseguire il fallback per l'opzione incorporata webview e avviare l'interfaccia utente come un webview incorporato.

Il codice seguente mostra l'opzione webview incorporato:

```csharp
bool useSystemBrowser = UIParent.IsSystemWebviewAvailable();
if (useSystemBrowser)
{
    // A browser with custom tabs is present on device, use system browser
    App.UIParent = new UIParent(Xamarin.Forms.Forms.Context as Activity);
}
else
{
    // A browser with custom tabs is not present on device, use embedded webview
    App.UIParent = new UIParent(Xamarin.Forms.Forms.Context as Activity, true);
}

// Alternative:
App.UIParent = new UIParent(Xamarin.Forms.Forms.Context as Activity, !useSystemBrowser);

```

## <a name="net-core-does-not-support-interactive-authentication"></a>.NET core non supporta l'autenticazione interattiva

Per .NET Core, acquisizione di token in modo interattivo non è disponibile. In effetti, .NET Core non offre ancora dell'interfaccia utente. Se si desidera fornire interactive sign-in per un'applicazione .NET Core, è possibile consentire all'applicazione presentare all'utente un codice e un URL a cui accedere per accedere in modo interattivo (vedere [flusso del codice di dispositivo](msal-authentication-flows.md#device-code)).