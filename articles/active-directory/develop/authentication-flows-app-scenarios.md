---
title: Scenari di app e flussi di autenticazione per Microsoft Identity Platform | Azure
description: Informazioni sugli scenari delle applicazioni per la piattaforma di identità Microsoft, tra cui l'autenticazione delle identità, l'acquisizione di token e la chiamata di API protette.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/03/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: ff92f66d4d7b96660fe83b79273f65ca50eb22f9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89292895"
---
# <a name="authentication-flows-and-application-scenarios"></a>Flussi di autenticazione e scenari di applicazioni

L'endpoint di Microsoft Identity Platform (v2.0) supporta l'autenticazione per diversi tipi di moderne architetture di applicazioni. Tutte le architetture sono basate sui protocolli standard [OAuth 2.0 e OpenID Connect](active-directory-v2-protocols.md). Usando le [librerie di autenticazione per la piattaforma di identità Microsoft](reference-v2-libraries.md), le applicazioni autenticano le identità e acquisiscono i token per accedere alle API protette.

Questo articolo descrive i flussi di autenticazione e gli scenari di applicazione in cui sono usati.

## <a name="application-categories"></a>Categorie di applicazioni

I token possono essere acquisiti da diversi tipi di applicazioni, tra cui:

- App Web
- App per dispositivi mobili
- App desktop
- API Web

I token possono anche essere acquisiti dalle app in esecuzione su dispositivi che non dispongono di un browser o che sono in esecuzione nel Internet delle cose.

Nelle sezioni seguenti vengono descritte le categorie di applicazioni.

### <a name="protected-resources-vs-client-applications"></a>Risorse protette o applicazioni client

Gli scenari di autenticazione coinvolgono due attività:

- **Acquisizione dei token di sicurezza per un'API Web protetta**: è consigliabile usare le [librerie client supportate da Microsoft](reference-v2-libraries.md#microsoft-supported-client-libraries) per acquisire i token. In particolare, si consiglia la famiglia Microsoft Authentication Library (MSAL).
- **Protezione di un'API Web o di un'app Web**: una sfida per la protezione di queste risorse consiste nel convalidare il token di sicurezza. In alcune piattaforme, Microsoft offre [librerie middleware](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries).

### <a name="with-users-or-without-users"></a>Con utenti o senza utenti

La maggior parte degli scenari di autenticazione acquisisce token per conto degli utenti che hanno eseguito l'accesso.

![Scenari con utenti](media/scenarios/scenarios-with-users.svg)

Tuttavia, sono disponibili anche app daemon. In questi scenari, le applicazioni acquisiscono token per conto di se stessi senza utente.

![Scenari con app daemon](media/scenarios/daemon-app.svg)

### <a name="single-page-public-client-and-confidential-client-applications"></a>Applicazioni a pagina singola, applicazioni client pubbliche e applicazioni client riservate

I token di sicurezza possono essere acquisiti da più tipi di applicazioni. Queste applicazioni tendono a essere separate nelle tre categorie seguenti. Ognuna viene usata in librerie e oggetti diversi.

- **Applicazioni a singola pagina**: note anche come Spa, si tratta di app Web in cui i token vengono acquisiti da un'app JavaScript o typescript in esecuzione nel browser. Molte app moderne hanno un'applicazione a singola pagina sul front-end, scritta principalmente in JavaScript. L'applicazione usa spesso un framework come Angular, React o Vue. MSAL.js è l'unica libreria di autenticazione Microsoft che supporta le applicazioni a pagina singola.

- **Applicazioni client pubbliche**: le app in questa categoria, ad esempio i tipi seguenti, sempre l'accesso degli utenti:
  - App desktop che chiamano API Web per conto degli utenti connessi
  - App per dispositivi mobili
  - App in esecuzione su dispositivi che non hanno un browser, ad esempio quelli in esecuzione su Internet
  
- **Applicazioni client riservate: le**app in questa categoria includono:
  - App Web che chiamano un'API Web
  - API Web che chiamano un'API Web
  - App daemon, anche se implementate come un servizio console, ad esempio un daemon Linux o un servizio Windows

### <a name="sign-in-audience"></a>Tipi di account supportati

i flussi di autenticazione disponibili variano in base ai destinatari per l'accesso. Alcuni flussi sono disponibili solo per gli account aziendali e dell'istituto di istruzione. Altri sono disponibili per gli account aziendali o dell'Istituto di istruzione e per gli account Microsoft personali.

Per ulteriori informazioni, vedere [tipi di account supportati](v2-supported-account-types.md#account-type-support-in-authentication-flows).

## <a name="application-scenarios"></a>Scenari applicativi

L'endpoint della piattaforma di identità Microsoft supporta l'autenticazione per queste architetture di app:

- App a pagina singola
- App Web
- API Web
- App per dispositivi mobili
- App native
- App daemon
- App lato server

Le applicazioni usano flussi di autenticazione diversi per concedere l'accesso agli utenti e ottenere i token per chiamare le API protette.

### <a name="single-page-application"></a>Applicazione a pagina singola

Molte app Web moderne sono compilate come applicazioni a singola pagina sul lato client. Queste applicazioni usano JavaScript o un Framework come angolare, VME e React. Queste applicazioni vengono eseguite in un Web browser.

Le applicazioni a singola pagina sono diverse dalle app Web tradizionali sul lato server in termini di caratteristiche di autenticazione. Usando Microsoft Identity Platform, le applicazioni a pagina singola possono concedere l'accesso agli utenti e ottenere i token per l'accesso a servizi back-end o API Web. Microsoft Identity Platform offre due tipi di concessione per le applicazioni JavaScript: 

| MSAL.js (2. x) | MSAL.js (1. x) |
|---|---|
| ![Autenticazione di un'applicazione a pagina singola](media/scenarios/spa-app-auth.svg) | ![Applicazione A pagina singola implicita](media/scenarios/spa-app.svg) |

### <a name="web-app-that-signs-in-a-user"></a>App Web che firma un utente

![App Web che concede l'accesso a un utente](media/scenarios/scenario-webapp-signs-in-users.svg)

Per proteggere un'app Web che accede a un utente:

- Se si sviluppa in .NET, usare ASP.NET o ASP.NET Core con il middleware ASP.NET OpenID Connect. Per la protezione di una risorsa è necessario convalidare il token di sicurezza, che viene eseguito dalle [estensioni IdentityModel per .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) e non dalle librerie MSAL.

- Se si sviluppa in Node.js, utilizzare [Passport.js](https://github.com/AzureAD/passport-azure-ad).

Per altre informazioni, vedere [App Web che concedono l'accesso degli utenti](scenario-web-app-sign-user-overview.md).

### <a name="web-app-that-signs-in-a-user-and-calls-a-web-api-on-behalf-of-the-user"></a>App Web che accede a un utente e chiama un'API Web per conto dell'utente

![App Web che chiama le API Web](media/scenarios/web-app.svg)

Per chiamare un'API Web da un'app Web per conto di un utente, usare il flusso del codice di autorizzazione e archiviare i token acquisiti nella cache dei token. Se necessario, MSAL aggiorna i token e il controller li acquisisce automaticamente dalla cache.

Per altre informazioni, vedere [app Web che chiama le API Web](scenario-web-app-call-api-overview.md).

### <a name="desktop-app-that-calls-a-web-api-on-behalf-of-a-signed-in-user"></a>App desktop che chiama un'API Web per conto di un utente che ha eseguito l'accesso

Per consentire a un'app desktop di chiamare un'API Web che esegue l'accesso agli utenti, usare i metodi di acquisizione dei token interattivi di MSAL. Con questi metodi interattivi, è possibile controllare l'esperienza dell'interfaccia utente di accesso. MSAL usa un Web browser per questa interazione.

![App desktop che chiama un'API Web](media/scenarios/desktop-app.svg)

Esiste un'altra possibilità per le applicazioni ospitate in Windows in computer aggiunti a un dominio Windows o tramite Azure Active Directory (Azure AD). Queste applicazioni possono acquisire automaticamente un token tramite l'[autenticazione integrata di Windows](https://aka.ms/msal-net-iwa).

Le applicazioni in esecuzione in un dispositivo privo di browser potranno comunque chiamare un'API per conto di un utente. Per eseguire l'autenticazione, l'utente dovrà eseguire l'accesso in un altro dispositivo che include un Web browser. Per questo scenario è necessario usare il [flusso del codice del dispositivo](https://aka.ms/msal-net-device-code-flow).

![Flusso di codice del dispositivo](media/scenarios/device-code-flow-app.svg)

Sebbene non sia consigliabile usarlo, il flusso di [nome utente/password](scenario-desktop-acquire-token.md#username-and-password) è disponibile nelle applicazioni client pubbliche. Questo flusso è ancora necessario in alcuni scenari, come DevOps.

L'uso del flusso di nome utente/password vincola le applicazioni. Ad esempio, le applicazioni non possono accedere a un utente che deve usare l'autenticazione a più fattori o lo strumento di accesso condizionale in Azure AD. Inoltre, le applicazioni non usufruiscono dell'accesso Single Sign-On. L'autenticazione con il flusso di nome utente/password viene eseguita rispetto ai principi dell'autenticazione moderna e viene fornita solo per motivi legacy.

Nelle app desktop, se si vuole che la cache dei token venga mantenute, è possibile personalizzare la [serializzazione della cache del token](scenario-desktop-acquire-token.md#file-based-token-cache). Implementando la [serializzazione a doppio token della cache](scenario-desktop-acquire-token.md#dual-token-cache-serialization-msal-unified-cache--adal-v3), è possibile usare le cache di token compatibili con le versioni precedenti e successive. Questi token supportano le generazioni precedenti di librerie di autenticazione. Le specifiche librerie includono Azure AD Authentication Library per .NET (ADAL.NET) versioni 3 e 4.

Per altre informazioni, vedere [App desktop che chiama le API Web](scenario-desktop-overview.md).

### <a name="mobile-app-that-calls-a-web-api-on-behalf-of-an-interactive-user"></a>App per dispositivi mobili che chiama un'API Web per conto di un utente interattivo

Analogamente a un'app desktop, un'app per dispositivi mobili chiama i metodi interattivi di acquisizione di token di MSAL per acquisire un token per chiamare un'API Web.

![App per dispositivi mobili che chiama un'API Web](media/scenarios/mobile-app.svg)

In iOS e Android, MSAL usa il Web browser di sistema per impostazione predefinita, Tuttavia, è possibile indirizzarli in modo da utilizzare la visualizzazione Web incorporata. Esistono specifiche che dipendono dalla piattaforma mobile: piattaforma UWP (Universal Windows Platform) (UWP), iOS o Android.

Per alcuni scenari, come quelli che comportano l'accesso condizionale correlato all'ID dispositivo o alla registrazione del dispositivo, è necessario installare un broker nel dispositivo. Gli esempi di broker includono il portale aziendale Microsoft in Android e Microsoft Authenticator in Android e iOS. MSAL è ora in grado di interagire con i broker. Per altre informazioni sui broker, vedere uso di [broker in Android e iOS](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/leveraging-brokers-on-Android-and-iOS).

Per altre informazioni, vedere [App per dispositivi mobili che chiama le API Web](scenario-mobile-overview.md).

> [!NOTE]
> App per dispositivi mobili che usa MSAL. iOS, MSAL. È possibile applicare i criteri di protezione delle app a Android o MSAL.NET in Novell. Ad esempio, i criteri potrebbero impedire all'utente di copiare testo protetto. L'app per dispositivi mobili viene gestita da Intune ed è riconosciuta da Intune come app gestita. Per altre informazioni, vedere [Panoramica di Microsoft Intune App SDK](/intune/app-sdk).
>
> [Intune App SDK](/intune/app-sdk-get-started) è separato dalle librerie MSAL e interagisce autonomamente con Azure AD.

### <a name="protected-web-api"></a>API Web protetta

È possibile usare l'endpoint della piattaforma Microsoft Identity per proteggere i servizi Web, ad esempio l'API Web RESTful dell'app. Un'API Web protetta viene chiamata tramite un token di accesso. Il token consente di proteggere i dati dell'API e di autenticare le richieste in ingresso. Il chiamante di un'API Web aggiunge un token di accesso nell'intestazione dell'autorizzazione di una richiesta HTTP.

Se si vuole proteggere l'API Web ASP.NET o ASP.NET Core, è necessario convalidare il token di accesso. A questo scopo verrà usato il middleware JWT ASP.NET. La convalida viene eseguita dalla libreria delle [estensioni IdentityModel per .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) e non da MSAL.NET

Per altre informazioni, vedere [API Web protetta](scenario-protected-web-api-overview.md).

### <a name="web-api-that-calls-another-web-api-on-behalf-of-a-user"></a>API Web che chiama un'altra API Web per conto di un utente

Per l'API Web protetta per chiamare un'altra API Web per conto di un utente, l'app deve acquisire un token per l'API Web downstream. Tali chiamate vengono talvolta definite chiamate *da servizio a servizio* . Le API Web che chiamano altre API Web devono fornire la serializzazione della cache personalizzata.

![API Web che chiama un'altra API Web](media/scenarios/web-api.svg)

Per altre informazioni, vedere [API Web che chiama le API Web](scenario-web-api-call-api-overview.md).

### <a name="daemon-app-that-calls-a-web-api-in-the-daemons-name"></a>App daemon che chiama un'API Web nel nome del daemon

Anche le app che contengono processi a esecuzione prolungata o che non prevedono l'interazione con l'utente necessitano di un modo per accedere alle API Web protette. Tale app può autenticarsi e ottenere i token usando l'identità dell'app. L'app dimostra la propria identità usando un segreto client o un certificato.

È possibile scrivere queste app daemon per acquisire un token per l'app chiamante usando i metodi di acquisizione delle [credenziali client](scenario-daemon-acquire-token.md#acquiretokenforclient-api) in MSAL. Questi metodi richiedono un segreto client aggiunto alla registrazione dell'app in Azure AD. L'app quindi condivide il segreto con il daemon chiamato. Esempi di questi segreti includono le password dell'applicazione, l'asserzione del certificato e l'asserzione client.

![App daemon da altre app e API](media/scenarios/daemon-app.svg)

Per altre informazioni, vedere [Applicazione daemon che chiama le API Web](scenario-daemon-overview.md).

## <a name="scenarios-and-supported-authentication-flows"></a>Scenari e flussi di autenticazione supportati

Usare i flussi di autenticazione per implementare gli scenari di applicazione che richiedono token. Non esiste un mapping uno-a-uno tra scenari di applicazioni e flussi di autenticazione.

Gli scenari che prevedono l'acquisizione di token vengono inoltre mappati ai flussi di autenticazione OAuth 2,0. Per ulteriori informazioni, vedere [la pagina relativa ai protocolli OAuth 2,0 e OpenID Connect nella piattaforma di identità Microsoft](active-directory-v2-protocols.md).

<table>
 <thead>
  <tr><th>Scenario</th> <th>Procedura dettagliata per lo scenario</th> <th>Flusso OAuth 2.0 e concessione</th> <th>Destinatari</th></tr>
 </thead>
 <tbody>
  <tr>
   <td><a href="scenario-spa-overview.md"><img alt="Single-Page App with Auth code" src="media/scenarios/spa-app-auth.svg"></a></td>
   <td><a href="scenario-spa-overview.md">App a singola pagina</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Codice di autorizzazione</a> con PKCE</td>
   <td>Account aziendali o dell'Istituto di istruzione, account personali e Azure Active Directory B2C (Azure AD B2C)</td>
 </tr>

  <tr>
   <td><a href="scenario-spa-overview.md"><img alt="Single-Page App with Implicit" src="media/scenarios/spa-app.svg"></a></td>
   <td><a href="scenario-spa-overview.md">App a singola pagina</a></td>
   <td><a href="v2-oauth2-implicit-grant-flow.md">Implicito</a></td>
   <td>Account aziendali o dell'Istituto di istruzione, account personali e Azure Active Directory B2C (Azure AD B2C)</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-sign-user-overview.md"><img alt="Web app that signs in users" src="media/scenarios/scenario-webapp-signs-in-users.svg"></a></td>
   <td><a href="scenario-web-app-sign-user-overview.md">App Web che offre l'accesso agli utenti</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Codice di autorizzazione</a></td>
   <td>Account aziendali o dell'istituto di istruzione, account personali, Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-call-api-overview.md"><img alt="Web app that signs in users" src="media/scenarios/web-app.svg"></a></td>
   <td><a href="scenario-web-app-call-api-overview.md">App Web che chiama le API Web</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Codice di autorizzazione</a></td>
   <td>Account aziendali o dell'istituto di istruzione, account personali, Azure AD B2C</td>
 </tr>

  <tr>
   <td rowspan="3"><a href="scenario-desktop-overview.md"><img alt=Desktop app that calls web APIs" src="media/scenarios/desktop-app.svg"></a></td>
   <td rowspan="4"><a href="scenario-desktop-overview.md">App desktop che chiama le API Web</a></td>
   <td>Interattivo usando il <a href="v2-oauth2-auth-code-flow.md">codice di autorizzazione</a> con PKCE</td>
   <td>Account aziendali o dell'istituto di istruzione, account personali, Azure AD B2C</td>
 </tr>

  <tr>
   <td>Autenticazione integrata di Windows</td>
   <td>Account aziendali o dell'istituto di istruzione</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Password del proprietario della risorsa</a></td>
   <td>Account aziendali o dell'istituto di istruzione e Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser"><img alt="Browserless application" src="media/scenarios/device-code-flow-app.svg"></a></td>
   <td><a href="v2-oauth2-device-code.md">Codice del dispositivo</a></td>
   <td>Account aziendali o dell'istituto di istruzione</td>
 </tr>

 <tr>
   <td rowspan="2"><a href="scenario-mobile-overview.md"><img alt="Mobile app that calls web APIs" src="media/scenarios/mobile-app.svg"></a></td>
   <td rowspan="2"><a href="scenario-mobile-overview.md">App per dispositivi mobili che chiama le API Web</a></td>
   <td>Interattivo usando il <a href="v2-oauth2-auth-code-flow.md">codice di autorizzazione</a> con PKCE</td>
   <td>Account aziendali o dell'istituto di istruzione, account personali, Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Password del proprietario della risorsa</a></td>
   <td>Account aziendali o dell'istituto di istruzione e Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-daemon-overview.md"><img alt="Daemon app that calls web APIs" src="media/scenarios/daemon-app.svg"></a></td>
   <td><a href="scenario-daemon-overview.md">App daemon che chiama le API Web</a></td>
   <td><a href="v2-oauth2-client-creds-grant-flow.md">Credenziali del client</a></td>
   <td>Autorizzazioni solo app senza utente e usate solo nelle organizzazioni Azure AD</td>
 </tr>

  <tr>
   <td><a href="scenario-web-api-call-api-overview.md"><img alt="Web API that calls web APIs" src="media/scenarios/web-api.svg"></a></td>
   <td><a href="scenario-web-api-call-api-overview.md">App Web che chiama le API Web</a></td>
   <td><a href="v2-oauth2-on-behalf-of-flow.md">On-behalf-of</a></td>
   <td>Account aziendali o dell'istituto di istruzione e account personali</td>
 </tr>

 </tbody>
</table>

## <a name="scenarios-and-supported-platforms-and-languages"></a>Scenari e piattaforme e linguaggi supportati

Le librerie di autenticazione Microsoft supportano più piattaforme:

- JavaScript
- .NET Framework
- .NET Core
- Windows 10/UWP
- Xamarin.iOS
- Xamarin.Android
- iOS nativo
- macOS
- Android nativo
- Java
- Python

Per creare le applicazioni è anche possibile usare vari linguaggi.

> [!NOTE]
> Alcuni tipi di applicazioni non sono disponibili in tutte le piattaforme.

Nella colonna Windows della tabella seguente, ogni volta che viene menzionato .NET Core è anche possibile usare .NET Framework. Quest'ultima piattaforma viene omessa per evitare di creare confusione nella tabella.

|Scenario  | Windows | Linux | Mac | iOS | Android
|--|--|--|--|--|--|--|
| [App a singola pagina](scenario-spa-overview.md) <br/>[![Autenticazione dell'app a pagina singola](media/scenarios/spa-app-auth.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js
| [App a singola pagina](scenario-spa-overview.md) <br/>[![App a singola pagina implicita](media/scenarios/spa-app.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js
| [App Web che offre l'accesso agli utenti](scenario-web-app-sign-user-overview.md) <br/>[![App Web per l'accesso degli utenti](media/scenarios/scenario-webapp-signs-in-users.svg)](scenario-web-app-sign-user-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core
| [App Web che chiama le API Web](scenario-web-app-call-api-overview.md) <br/> <br/>[![App Web che chiama le API Web](media/scenarios/web-app.svg)](scenario-web-app-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png) <br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Flask + MSAL Python| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Flask + MSAL Python| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Flask + MSAL Python
| [App desktop che chiama le API Web](scenario-desktop-overview.md) <br/> <br/>[ ![ App desktop che chiama le API Web](media/scenarios/desktop-app.svg)](scenario-desktop-overview.md) ![ Flusso del codice del dispositivo](media/scenarios/device-code-flow-app.svg) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python <br/> ![iOS/Objective C o swift](media/sample-v2-code/small_logo_iOS.png) MSAL.objc |
| [App per dispositivi mobili che chiama le API Web](scenario-mobile-overview.md) <br/> [![App per dispositivi mobili che chiama le API Web](media/scenarios/mobile-app.svg)](scenario-mobile-overview.md) | ![UWP](media/sample-v2-code/small_logo_windows.png) MSAL.NET ![Xamarin](media/sample-v2-code/small_logo_xamarin.png) MSAL.NET | | | ![iOS/Objective C o swift](media/sample-v2-code/small_logo_iOS.png) MSAL.objc | ![Android](media/sample-v2-code/small_logo_Android.png) MSAL.Android
| [App daemon](scenario-daemon-overview.md) <br/> [![App daemon](media/scenarios/daemon-app.svg)](scenario-daemon-overview.md) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png) MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python
| [App Web che chiama le API Web](scenario-web-api-call-api-overview.md) <br/><br/> [![App Web che chiama le API Web](media/scenarios/web-api.svg)](scenario-web-api-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python

Per ulteriori informazioni, vedere la pagina relativa alle [librerie supportate da Microsoft per sistema operativo/linguaggio](reference-v2-libraries.md#microsoft-supported-libraries-by-os--language).

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulle [nozioni di base sull'autenticazione](./authentication-vs-authorization.md) e sui [token di accesso sono disponibili nella piattaforma Microsoft Identity](access-tokens.md).
* Altre informazioni sulla [protezione dell'accesso alle app per](/azure/architecture/example-scenario/iot-aad/iot-aad)le cose.