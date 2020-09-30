---
title: Plug-in Microsoft Enterprise SSO per dispositivi Apple
titleSuffix: Microsoft identity platform | Azure
description: Informazioni sul plug-in Azure Active Directory SSO di Microsoft per i dispositivi iOS e macOS.
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/15/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: ec0ab4601e15129ecd8917e0e750a3e1661dc558
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91530698"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Plug-in Microsoft Enterprise SSO per i dispositivi Apple (anteprima)

> [!NOTE]
> Questa funzionalità è disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Il *plug-in Microsoft Enterprise SSO per i dispositivi Apple* fornisce Single Sign-on (SSO) per gli account Azure Active Directory (Azure ad) in tutte le applicazioni che supportano la funzionalità di [Enterprise Single Sign-on](https://developer.apple.com/documentation/authenticationservices) di Apple. Microsoft ha collaborato con Apple per sviluppare questo plug-in per aumentare l'usabilità dell'applicazione garantendo al contempo la migliore protezione fornita da Apple e da Microsoft.

In questa versione di anteprima pubblica, il plug-in Enterprise SSO è disponibile solo per i dispositivi iOS ed è distribuito in determinate applicazioni Microsoft.

## <a name="features"></a>Funzionalità

Il plug-in Microsoft Enterprise SSO per i dispositivi Apple offre i vantaggi seguenti:

- Fornisce l'accesso SSO per gli account Azure AD in tutte le applicazioni che supportano la funzionalità Enterprise Single Sign-On di Apple.
- Viene recapitato automaticamente nel Microsoft Authenticator e può essere abilitato da qualsiasi soluzione di gestione di dispositivi mobili (MDM).

## <a name="requirements"></a>Requisiti

Per usare il plug-in Microsoft Enterprise SSO per i dispositivi Apple:

- iOS 13,0 o versione successiva deve essere installato nel dispositivo.
- Nel dispositivo deve essere installata un'applicazione Microsoft che fornisce il plug-in Microsoft Enterprise SSO per i dispositivi Apple. Per la versione di anteprima pubblica, queste applicazioni includono l' [app Microsoft Authenticator](../user-help/user-help-auth-app-overview.md).
- Il dispositivo deve essere registrato con MDM, ad esempio con Microsoft Intune.
- È necessario effettuare il push della configurazione nel dispositivo per abilitare il plug-in Microsoft Enterprise SSO per i dispositivi Apple nel dispositivo. Questo vincolo di sicurezza è richiesto da Apple.

## <a name="enable-the-sso-plug-in-with-mobile-device-management-mdm"></a>Abilitare il plug-in SSO con la gestione dei dispositivi mobili (MDM)

Per abilitare il plug-in Microsoft Enterprise SSO per i dispositivi Apple, i dispositivi devono essere inviati tramite un servizio MDM. Poiché Microsoft include il plug-in Enterprise SSO nell' [app Microsoft Authenticator](..//user-help/user-help-auth-app-overview.md), usare MDM per configurare l'app per abilitare il plug-in Microsoft Enterprise SSO.

Per configurare il plug-in Microsoft Enterprise SSO per i dispositivi Apple, usare i parametri seguenti:

- **Tipo**: Reindirizzamento
- **ID estensione**: `com.microsoft.azureauthenticator.ssoextension`
- **Team ID**: (questo campo non è necessario per iOS)
- **URL**:
  - `https://login.microsoftonline.com`
  - `https://login.microsoft.com`
  - `https://sts.windows.net`
  - `https://login.partner.microsoftonline.cn`
  - `https://login.chinacloudapi.cn`
  - `https://login.microsoftonline.de`
  - `https://login.microsoftonline.us`
  - `https://login.usgovcloudapi.net`
  - `https://login-us.microsoftonline.com`
  
### <a name="additional-configuration-options"></a>Opzioni di configurazione aggiuntive
È possibile aggiungere altre opzioni di configurazione per estendere la funzionalità SSO ad altre app.

#### <a name="enable-sso-for-apps-that-dont-use-msal"></a>Abilitare l'accesso SSO per le app che non usano MSAL

Il plug-in SSO consente a qualsiasi applicazione di partecipare Single Sign-On anche se non è stato sviluppato utilizzando un SDK Microsoft come Microsoft Authentication Library (MSAL).

Il plug-in SSO viene installato automaticamente dai dispositivi che hanno scaricato l'app Microsoft Authenticator e hanno registrato il dispositivo con l'organizzazione. È probabile che l'organizzazione usi attualmente l'app Authenticator per scenari come l'autenticazione a più fattori, l'autenticazione senza password e l'accesso condizionale. Può essere attivata per le applicazioni usando qualsiasi provider MDM, sebbene Microsoft abbia semplificato la configurazione all'interno di Microsoft Endpoint Manager di Intune. Viene usato un elenco Consenti per configurare queste applicazioni in modo da usare il plug-in SSO installato dall'app Authenticator.

Sono supportate solo le app che usano tecnologie di rete Apple native o WebView. Se un'applicazione fornisce un'implementazione del proprio livello di rete, il plug-in Microsoft Enterprise SSO non è supportato.  

Usare i parametri seguenti per configurare il plug-in di Microsoft Enterprise SSO per le app che non usano MSAL:

- **Chiave**: `AppAllowList`
- **Tipo**: `String`
- **Valore**: elenco delimitato da virgole di ID bundle applicazione per le applicazioni che possono partecipare all'accesso SSO
- **Esempio**: `com.contoso.workapp, com.contoso.travelapp`

Le [app](./application-consent-experience.md) consentite che sono consentite dall'amministratore MDM per partecipare all'accesso SSO possono ottenere automaticamente un token per l'utente finale. Pertanto, è importante aggiungere solo applicazioni attendibili all'elenco Consenti. 

Non è necessario aggiungere a questo elenco le applicazioni che usano MSAL o ASWebAuthenticationSession. Queste applicazioni sono abilitate per impostazione predefinita. 

#### <a name="allow-creating-sso-session-from-any-application"></a>Consenti creazione sessione SSO da qualsiasi applicazione

Per impostazione predefinita, il plug-in Microsoft Enterprise SSO fornisce SSO per le app autorizzate solo quando il plug-in SSO dispone già di credenziali condivise. Il plug-in Microsoft Enterprise SSO è in grado di acquisire credenziali condivise quando viene chiamato da un'altra applicazione basata su ADAL o MSAL durante l'acquisizione del token. La maggior parte delle app Microsoft usa il plug-in Microsoft Authenticator o SSO. Ciò significa che per impostazione predefinita SSO all'esterno dei flussi di app native è il lavoro migliore.  

L'abilitazione `browser_sso_interaction_enabled` del flag Abilita le app non MSAL e Safari browser per eseguire il bootstrap iniziale e ottenere le credenziali condivise. Se il plug-in Microsoft Enterprise SSO non dispone ancora di credenziali condivise, tenterà di ottenerne una ogni volta che viene richiesto un accesso da un URL Azure AD all'interno del browser Safari, ASWebAuthenticationSession, SafariViewController o un'altra applicazione nativa consentita.  

- **Chiave**: `browser_sso_interaction_enabled`
- **Tipo**: `Integer`
- **Valore**: 1 o 0

È consigliabile abilitare questo flag per ottenere un'esperienza più coerente in tutte le app. Questo protocollo è disabilitato per impostazione predefinita. 

#### <a name="disable-oauth2-application-prompts"></a>Disabilitare i prompt dell'applicazione OAuth2

Il plug-in Microsoft Enterprise SSO fornisce l'accesso SSO aggiungendo le credenziali condivise alle richieste di rete provenienti dalle applicazioni consentite. È possibile che alcune applicazioni OAuth2 applichino il prompt dell'utente finale sul livello del protocollo. Le credenziali condivise verranno ignorate per tali app.  

L'abilitazione `disable_explicit_app_prompt` del flag limita la capacità di applicazioni native e Web di forzare un prompt dell'utente finale sul livello del protocollo e di ignorare SSO.

- **Chiave**: `disable_explicit_app_prompt`
- **Tipo**: `Integer`
- **Valore**: 1 o 0

È consigliabile abilitare questo flag per ottenere un'esperienza più coerente in tutte le app. Questo protocollo è disabilitato per impostazione predefinita. 

#### <a name="use-intune-for-simplified-configuration"></a>Usare Intune per la configurazione semplificata

È possibile usare Microsoft Intune come servizio MDM per semplificare la configurazione del plug-in Microsoft Enterprise SSO. Per ulteriori informazioni, vedere la [documentazione di configurazione di Intune](/intune/configuration/ios-device-features-settings).

## <a name="using-the-sso-plug-in-in-your-application"></a>Uso del plug-in SSO nell'applicazione

[Microsoft Authentication Library (MSAL) per i dispositivi Apple](https://github.com/AzureAD/microsoft-authentication-library-for-objc) versione 1.1.0 e versioni successive supporta il plug-in Microsoft Enterprise SSO per i dispositivi Apple.

Se si sta creando un'applicazione per gli scenari di lavoro Frontline, vedere [modalità dispositivo condiviso per i dispositivi iOS](msal-ios-shared-devices.md) per ulteriori impostazioni della funzionalità.

## <a name="how-the-sso-plug-in-works"></a>Funzionamento del plug-in SSO

Il plug-in Microsoft Enterprise SSO si basa sul [Framework di Enterprise Single Sign-on di Apple](https://developer.apple.com/documentation/authenticationservices/asauthorizationsinglesignonprovider?language=objc). I provider di identità che vengono caricati nel Framework possono intercettare il traffico di rete per i domini e migliorare o modificare la modalità di gestione di tali richieste. Il plug-in SSO, ad esempio, può visualizzare un'interfaccia utente aggiuntiva per raccogliere le credenziali dell'utente finale in modo sicuro, richiedere l'autenticazione a più fattori o fornire i token all'applicazione in modo invisibile all'utente.

Le applicazioni native possono inoltre implementare operazioni personalizzate e comunicare direttamente con il plug-in SSO.
È possibile ottenere informazioni sul Framework Single Sign-on in questo [video 2019 WWDC di Apple](https://developer.apple.com/videos/play/tech-talks/301/)

### <a name="applications-that-use-msal"></a>Applicazioni che usano MSAL

[Microsoft Authentication Library (MSAL) per i dispositivi Apple](https://github.com/AzureAD/microsoft-authentication-library-for-objc) versione 1.1.0 e versioni successive supporta il plug-in Microsoft Enterprise SSO per i dispositivi Apple in modo nativo per gli account aziendali e dell'Istituto di istruzione. 

Se sono stati seguiti [tutti i passaggi consigliati](./quickstart-v2-ios.md) e si è usato il [formato URI di reindirizzamento](./redirect-uris-ios.md)predefinito, non è necessaria alcuna configurazione speciale. Quando è in esecuzione in un dispositivo in cui è presente il plug-in SSO, MSAL lo richiamerà automaticamente per tutte le richieste di token interattivo e invisibile, nonché per le operazioni di enumerazione degli account e di rimozione dell'account. Poiché MSAL implementa il protocollo plug-in nativo SSO che si basa sulle operazioni personalizzate, questa configurazione offre all'utente finale l'esperienza nativa più semplice. 

Se il plug-in SSO non è abilitato da MDM, ma l'app Microsoft Authenticator è presente nel dispositivo, MSAL utilizzerà invece l'app Microsoft Authenticator per tutte le richieste di token interattive. Il plug-in SSO condivide l'accesso SSO con l'app Microsoft Authenticator.

### <a name="applications-that-dont-use-msal"></a>Applicazioni che non usano MSAL

Le applicazioni che non usano MSAL possono comunque ottenere SSO se un amministratore le aggiunge all'elenco Consenti in modo esplicito. 

Non sono necessarie modifiche al codice in tali app purché siano soddisfatte le condizioni seguenti:

- L'applicazione usa i framework Apple per eseguire richieste di rete (ad esempio, [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview), [NSURLSession](https://developer.apple.com/documentation/foundation/nsurlsession)) 
- L'applicazione usa protocolli standard per comunicare con Azure AD (ad esempio, OAuth2, SAML, WS-Federation)
- L'applicazione non raccoglie il nome utente e la password in testo normale nell'interfaccia utente nativa

In questo caso, SSO viene fornito quando l'applicazione crea una richiesta di rete e apre un Web browser per l'accesso dell'utente. Quando un utente viene reindirizzato a un URL di accesso Azure AD, il plug-in SSO convalida l'URL e controlla se è disponibile una credenziale SSO per tale URL. Se ne esiste uno, il plug-in SSO passa le credenziali SSO a Azure AD, che autorizza l'applicazione a completare la richiesta di rete senza chiedere all'utente di immettere le proprie credenziali. Inoltre, se il dispositivo è noto come Azure AD, il plug-in SSO passa anche il certificato del dispositivo per soddisfare il controllo dell'accesso condizionale basato su dispositivo. 

Per supportare l'accesso SSO per le app non MSAL, il plug-in SSO implementa un protocollo simile al plug-in del browser Windows descritto in [che cos'è un token di aggiornamento primario?](../devices/concept-primary-refresh-token.md#browser-sso-using-prt). 

Rispetto alle app basate su MSAL, il plug-in SSO agisce in modo più trasparente per le app non MSAL integrando l'esperienza di accesso del browser esistente fornita dalle app. L'utente finale può vedere la propria esperienza familiare, con il vantaggio di non dover eseguire accessi aggiuntivi in ognuna delle applicazioni. Ad esempio, anziché visualizzare la selezione account nativa, il plug-in SSO aggiunge sessioni SSO all'esperienza di selezione account basata sul Web. 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla modalità dispositivo condiviso in iOS, vedere [modalità dispositivo condiviso per dispositivi iOS](msal-ios-shared-devices.md).
