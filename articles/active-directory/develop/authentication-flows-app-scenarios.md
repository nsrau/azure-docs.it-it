---
title: Scenari di autenticazione per Microsoft Identity Platform | Azure
description: Informazioni sui flussi di autenticazione e sugli scenari applicativi per Microsoft Identity Platform. Informazioni sui diversi tipi di applicazioni che possono autenticare identità, acquisire token e chiamare API protette.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/27/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71557a2776bae36508beec8d5af9e00923393163
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72324739"
---
# <a name="authentication-flows-and-application-scenarios"></a>Flussi di autenticazione e scenari di applicazioni

L'endpoint di Microsoft Identity Platform (v2.0) supporta l'autenticazione per diversi tipi di moderne architetture di applicazioni. Tutte le architetture sono basate sui protocolli standard [OAuth 2.0 e OpenID Connect](active-directory-v2-protocols.md).  Usando le [librerie di autenticazione](reference-v2-libraries.md) le applicazioni autenticano le identità e acquisiscono i token per accedere alle API protette.

Questo articolo descrive i diversi flussi di autenticazione e gli scenari applicativi in cui vengono usati. Include anche elenchi di:
- [Scenari applicativi e flussi di autenticazione supportati](#scenarios-and-supported-authentication-flows).
- [Scenari applicativi e piattaforme e linguaggi supportati](#scenarios-and-supported-platforms-and-languages).

## <a name="application-categories"></a>Categorie di applicazioni

I token possono essere acquisiti da diversi tipi di applicazioni, tra cui:

- App Web
- App per dispositivi mobili
- App desktop
- API Web

Possono anche essere acquisiti dalle app in esecuzione in dispositivi privi di browser o eseguiti in IoT.

Le applicazioni possono essere categorizzate in base all'elenco seguente:

- [Risorse protette o applicazioni client](#protected-resources-vs-client-applications): alcuni scenari riguardano la protezione di risorse come app Web o API Web. Altri scenari si riferiscono all'acquisizione di un token di sicurezza per chiamare un'API Web protetta.
- [Con o senza utenti](#with-users-or-without-users): alcuni scenari coinvolgono un utente che ha eseguito l'accesso, mentre altri scenari daemon no.
- [Applicazioni a pagina singola, applicazioni client pubbliche e applicazioni client riservate](#single-page-public-client-and-confidential-client-applications): Queste sono tre grandi categorie di tipi di applicazioni. Ognuna viene usata in librerie e oggetti diversi.
- [Destinatari per l'accesso](v2-supported-account-types.md#certain-authentication-flows-dont-support-all-the-account-types): i flussi di autenticazione disponibili variano in base ai destinatari per l'accesso. Alcuni flussi sono disponibili solo per gli account aziendali e dell'istituto di istruzione. Altri sono disponibili sia per questi account che per gli account Microsoft personali. I destinatari consentiti dipendono dai flussi di autenticazione.
- [Flussi OAuth 2.0 supportati](#scenarios-and-supported-authentication-flows):  i flussi di autenticazione vengono usati per implementare gli scenari applicativi che richiedono token. Non esiste un mapping uno-a-uno tra scenari di applicazioni e flussi di autenticazione.
- [Piattaforme supportate](#scenarios-and-supported-platforms-and-languages): Non tutti gli scenari di applicazioni sono disponibili per ogni piattaforma.

### <a name="protected-resources-vs-client-applications"></a>Risorse protette o applicazioni client

Gli scenari di autenticazione coinvolgono due attività:

- **Acquisizione dei token di sicurezza per un'API Web protetta**: Microsoft consiglia di usare le [librerie di autenticazione](reference-v2-libraries.md#microsoft-supported-client-libraries) per acquisire i token, in particolare la famiglia Microsoft Authentication Library (MSAL)
- **Protezione di un'API Web o di un'app Web**: uno dei problemi associati alla protezione di un'API Web o di un'app Web consiste nella convalida del token di sicurezza. In alcune piattaforme, Microsoft offre [librerie middleware](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries).

### <a name="with-users-or-without-users"></a>Con utenti o senza utenti

La maggior parte degli scenari di autenticazione acquisisce token per conto degli utenti che hanno eseguito l'accesso.

![Scenari con utenti](media/scenarios/scenarios-with-users.svg)

Esistono tuttavia anche scenari di app daemon in cui le applicazioni acquisiscono i token per conto di se stesse, senza utente.

![Scenari con app daemon](media/scenarios/daemon-app.svg)

### <a name="single-page-public-client-and-confidential-client-applications"></a>Applicazioni a pagina singola, applicazioni client pubbliche e applicazioni client riservate

I token di sicurezza possono essere acquisiti da più tipi di applicazioni. Queste applicazioni tendono a essere suddivise in tre categorie:

- **Applicazioni a pagina singola**: si tratta di app Web in cui i token vengono acquisiti da un'app JavaScript o TypeScript in esecuzione nel browser. Molte app moderne hanno un front-end di applicazione a pagina singola scritto principalmente in JavaScript. L'applicazione usa spesso un framework come Angular, React o Vue. MSAL.js è l'unica libreria di autenticazione Microsoft che supporta le applicazioni a pagina singola.

- **Applicazioni client pubbliche**: queste applicazioni gestiscono sempre l'accesso degli utenti:
  - App desktop che chiamano API Web per conto dell'utente che ha eseguito l'accesso
  - App per dispositivi mobili
  - App in esecuzione in dispositivi privi di browser, ad esempio quelli usati in IoT

  Questa app sono rappresentate dalla classe MSAL [PublicClientApplication](msal-client-applications.md).

- **Applicazioni client riservate**:
  - App Web che chiamano un'API Web
  - API Web che chiamano un'API Web
  - App daemon, anche se implementate come un servizio console, ad esempio un daemon Linux o un servizio Windows
 
  Questi tipi di app usano la classe [ConfidentialClientApplication](msal-client-applications.md).

## <a name="application-scenarios"></a>Scenari applicativi

L'endpoint di Microsoft Identity Platform supporta l'autenticazione per diversi tipi di architetture di app:

- App a pagina singola
- App Web
- API Web
- App per dispositivi mobili
- App native
- App daemon
- App lato server

Le applicazioni usano flussi di autenticazione diversi per concedere l'accesso agli utenti e ottenere i token per chiamare le API protette.

### <a name="a-single-page-application"></a>Applicazione a pagina singola

Molte app Web moderne vengono create come applicazioni a pagina singola lato client, scritte con JavaScript o con un apposito framework come Angular, Vue.js e React.js. Queste applicazioni vengono eseguite in un Web browser. Le relative caratteristiche di autenticazione sono diverse da quelle delle tradizionali app Web lato server. Usando Microsoft Identity Platform, le applicazioni a pagina singola possono concedere l'accesso agli utenti e ottenere i token per l'accesso a servizi back-end o API Web.

![Applicazione a pagina singola](media/scenarios/spa-app.svg)

Per altre informazioni, vedere [Applicazioni a pagina singola](scenario-spa-overview.md).

### <a name="a-web-app-that-is-signing-in-a-user"></a>App Web che concede l'accesso a un utente

![App Web che concede l'accesso a un utente](media/scenarios/scenario-webapp-signs-in-users.svg)

Per proteggere un'app Web che concede l'accesso a un utente:

- Se si distribuisce in .NET, si usa ASP.NET o ASP.NET Core con il middleware ASP.NET Open ID Connect. La protezione delle risorse comporta la convalida del token di sicurezza, operazione eseguita dalla libreria delle [estensioni IdentityModel per .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) e non dalle librerie MSAL.

- Se si sviluppa in Node.js, si usa Passport.js.

Per altre informazioni, vedere [App Web che concedono l'accesso degli utenti](scenario-web-app-sign-user-overview.md).

### <a name="a-web-app-that-signs-in-a-user-and-calling-a-web-api-on-behalf-of-the-user"></a>App Web che concede l'accesso a un utente e chiama un'API Web per conto dell'utente

![App Web che chiama le API Web](media/scenarios/web-app.svg)

Per chiamare un'API Web da un'app Web per conto di un utente, usare la classe MSAL **ConfidentialClientApplication**. Si userà il flusso del codice di autorizzazione, archiviando i token acquisiti nella cache dei token. Se necessario, MSAL aggiorna i token e il controller li acquisisce automaticamente dalla cache.

Per altre informazioni, vedere [App Web che chiama le API Web](scenario-web-app-call-api-overview.md).

### <a name="a-desktop-app-calling-a-web-api-on-behalf-of-a-signed-in-user"></a>App desktop che chiama un'API Web per conto dell'utente che ha eseguito l'accesso

Per chiamare un'API Web da un'applicazione desktop che concede l'accesso agli utenti, usare i metodi di acquisizione dei token interattivi della classe MSAL **PublicClientApplication**. Con questi metodi interattivi, è possibile controllare l'esperienza dell'interfaccia utente di accesso. MSAL usa un Web browser per questa interazione.

![App desktop che chiama un'API Web](media/scenarios/desktop-app.svg)

Esiste un'altra possibilità per le applicazioni ospitate in Windows in computer aggiunti a un dominio Windows o tramite Azure Active Directory (Azure AD). Queste applicazioni possono acquisire automaticamente un token tramite l'[autenticazione integrata di Windows](https://aka.ms/msal-net-iwa).

Le applicazioni in esecuzione in un dispositivo privo di browser potranno comunque chiamare un'API per conto di un utente. Per eseguire l'autenticazione, l'utente dovrà eseguire l'accesso in un altro dispositivo che include un Web browser. Per questo scenario, è necessario usare il [flusso di codice del dispositivo](https://aka.ms/msal-net-device-code-flow).

![Flusso di codice del dispositivo](media/scenarios/device-code-flow-app.svg)

Anche se non è consigliabile usarlo, il [flusso nome utente/password](https://aka.ms/msal-net-up) è disponibile nelle applicazioni client pubbliche. Questo flusso è ancora necessario in alcuni scenari, come DevOps.

Ma l'uso di questo flusso impone vincoli alle applicazioni. Ad esempio, le applicazioni che lo usano non saranno in grado di concedere l'accesso a un utente che deve eseguire l'autenticazione a più fattori o l'accesso condizionale. Inoltre, le applicazioni non usufruiscono dell'accesso Single Sign-On.

L'autenticazione con il flusso nome utente/password è anche contraria ai principi dell'autenticazione moderna e viene fornita solo per motivi di compatibilità con le versioni precedenti.

Nelle app desktop, se si vuole che la cache dei token sia persistente, è necessario [personalizzare la serializzazione della cache dei token](https://aka.ms/msal-net-token-cache-serialization). Implementando la [serializzazione doppia della cache dei token](https://aka.ms/msal-net-dual-cache-serialization), è anche possibile usare cache di token compatibili con le versioni precedenti e successive delle generazioni precedenti di librerie di autenticazione. Le specifiche librerie includono Azure AD Authentication Library per .NET (ADAL.NET) versioni 3 e 4.

Per altre informazioni, vedere [App desktop che chiama le API Web](scenario-desktop-overview.md).

### <a name="a-mobile-app-calling-a-web-api-on-behalf-of-an-interactive-user"></a>App per dispositivi mobili che chiama un'API Web per conto di un utente interattivo

In modo simile alle app desktop, per acquisire un token per chiamare un'API Web, un'app per dispositivi mobili chiama i metodi interattivi di acquisizione token della classe MSAL **PublicClientApplication**.

![App per dispositivi mobili che chiama un'API Web](media/scenarios/mobile-app.svg)

In iOS e Android, MSAL usa il Web browser di sistema per impostazione predefinita, ma in alternativa è anche possibile specificare l'uso della visualizzazione Web incorporata. Esistono alcune considerazioni specifiche a seconda della piattaforma per dispositivi mobili: UWP (Universal Windows Platform), iOS o Android.

Per alcuni scenari, come quelli che comportano l'accesso condizionale correlato all'ID dispositivo o la registrazione del dispositivo, è necessario installare un [broker](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/leveraging-brokers-on-Android-and-iOS) nel dispositivo. Gli esempi di broker includono il portale aziendale Microsoft in Android e Microsoft Authenticator in Android e iOS. Inoltre, MSAL può ora interagire con i broker.

> [!NOTE]
> Alle app per dispositivi mobili che usano MSAL.iOS, MSAL.Android o MSAL.NET in Xamarin possono essere applicati criteri di protezione. Ad esempio, i criteri potrebbero impedire all'utente di copiare testo protetto. L'app per dispositivi mobili [viene gestita da Intune](https://docs.microsoft.com/intune/app-sdk) ed è riconosciuta da Intune come app gestita. [Intune App SDK](https://docs.microsoft.com/intune/app-sdk-get-started) è separato dalle librerie MSAL e interagisce autonomamente con Azure AD.

Per altre informazioni, vedere [App per dispositivi mobili che chiama le API Web](scenario-mobile-overview.md).

### <a name="a-protected-web-api"></a>API Web protetta

È possibile usare l'endpoint di Microsoft Identity Platform per proteggere i servizi Web, ad esempio l'API Web RESTful dell'app. Un'API Web protetta viene chiamata con un token di accesso per proteggere i relativi dati e per autenticare le richieste in ingresso. Il chiamante di un'API Web aggiunge un token di accesso nell'intestazione dell'autorizzazione di una richiesta HTTP.

Se si vuole proteggere l'API Web ASP.NET o ASP.NET Core, è necessario convalidare il token di accesso. A questo scopo verrà usato il middleware JWT ASP.NET. La convalida viene eseguita dalla libreria delle [estensioni IdentityModel per .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) e non da MSAL.NET

Per altre informazioni, vedere [API Web protetta](scenario-protected-web-api-overview.md).

### <a name="a-web-api-calling-another-web-api-on-behalf-of-a-user"></a>API Web che chiama un'altra API Web per conto di un utente

Per fare in modo che l'API Web ASP.NET o ASP.NET Core protetta chiami un'altra API Web per conto di un utente, l'app deve acquisire un token per l'API Web downstream. A questo scopo chiama il metodo [AcquireTokenOnBehalfOf](https://aka.ms/msal-net-on-behalf-of) della classe **ConfidentialClientApplication**. Queste chiamate sono anche note come chiamate da servizio a servizio. Le API Web che chiamano altre API Web devono fornire una serializzazione della cache personalizzata.

  ![API Web che chiama un'altra API Web](media/scenarios/web-api.svg)

Per altre informazioni, vedere [API Web che chiama le API Web](scenario-web-api-call-api-overview.md).

### <a name="a-daemon-app-calling-a-web-api-in-the-daemons-name"></a>App daemon che chiama un'API Web nel nome del daemon

Anche le app che contengono processi a esecuzione prolungata o che non prevedono l'interazione con l'utente necessitano di un modo per accedere alle API Web protette. Queste app possono autenticarsi e ottenere i token usando l'identità dell'app, invece di un'identità delegata dell'utente. L'app dimostra la propria identità usando un segreto client o un certificato.

È possibile scrivere tali app daemon che acquisiscono un token per l'app chiamante usando i metodi di acquisizione di [credenziali client](https://aka.ms/msal-net-client-credentials) della classe MSAL **ConfidentialClientApplication**. Questi metodi richiedono che l'app chiamante abbia registrato un segreto in Azure AD. L'app quindi condivide il segreto con il daemon chiamato. Gli esempi di tali segreti includono le password delle applicazioni, l'asserzione di certificati o l'asserzione client.

![App daemon da altre app e API](media/scenarios/daemon-app.svg)

Per altre informazioni, vedere [Applicazione daemon che chiama le API Web](scenario-daemon-overview.md).

## <a name="scenarios-and-supported-authentication-flows"></a>Scenari e flussi di autenticazione supportati

Gli scenari che comportano l'acquisizione di token corrispondono anche ai flussi di autenticazione OAuth 2.0, come descritto in [Protocolli di Microsoft Identity Platform](active-directory-v2-protocols.md).

<table>
 <thead>
  <tr><th>Scenario</th> <th>Procedura dettagliata per lo scenario</th> <th>Flusso OAuth 2.0 e concessione</th> <th>Audience</th></tr>
 </thead>
 <tbody>
  <tr>
   <td><a href="scenario-spa-overview.md"><img alt="Single-Page App" src="media/scenarios/spa-app.svg"></a></td>
   <td><a href="scenario-spa-overview.md">App a singola pagina</a></td>
   <td><a href="v2-oauth2-implicit-grant-flow.md">Implicito</a></td>
   <td>Account aziendali o dell'istituto di istruzione, account personali e Microsoft Azure Active Directory B2C (Azure AD B2C)</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-sign-user-overview.md"><img alt="Web App that signs in users" src="media/scenarios/scenario-webapp-signs-in-users.svg"></a></td>
   <td><a href="scenario-web-app-sign-user-overview.md">App Web che concede l'accesso agli utenti</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Codice di autorizzazione</a></td>
   <td>Account aziendali o dell'istituto di istruzione, account personali, Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-call-api-overview.md"><img alt="Web App that signs in users" src="media/scenarios/web-app.svg"></a></td>
   <td><a href="scenario-web-app-call-api-overview.md">App Web che chiama le API Web</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Codice di autorizzazione</a></td>
   <td>Account aziendali o dell'istituto di istruzione, account personali, Azure AD B2C</td>
 </tr>

  <tr>
   <td rowspan="3"><a href="scenario-desktop-overview.md"><img alt=Desktop app that calls web APIs" src="media/scenarios/desktop-app.svg"></a></td>
   <td rowspan="4"><a href="scenario-desktop-overview.md">App desktop che chiama le API Web</a></td>
   <td>Interattivo tramite <a href="v2-oauth2-auth-code-flow.md">codice di autorizzazione</a> con PKCE</td>
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
   <td><a href="scenario-desktop-acquire-token.md#command-line-tool-without-web-browser"><img alt="Browserless application" src="media/scenarios/device-code-flow-app.svg"></a></td>
   <td><a href="v2-oauth2-device-code.md">Codice del dispositivo</a></td>
   <td>Account aziendali o dell'istituto di istruzione</td>
 </tr>

 <tr>
   <td rowspan="2"><a href="scenario-mobile-overview.md"><img alt="Mobile app that calls web APIs" src="media/scenarios/mobile-app.svg"></a></td>
   <td rowspan="2"><a href="scenario-mobile-overview.md">App per dispositivi mobili che chiama le API Web</a></td>
   <td>Interattivo tramite <a href="v2-oauth2-auth-code-flow.md">codice di autorizzazione</a> con PKCE</td>
   <td>Account aziendali o dell'istituto di istruzione, account personali, Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Password del proprietario della risorsa</a></td>
   <td>Account aziendali o dell'istituto di istruzione e Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-daemon-overview.md"><img alt="Daemon app that calls web APIs" src="media/scenarios/daemon-app.svg"></a></td>
   <td><a href=scenario-daemon-overview.md">App daemon che chiama le API Web</a></td>
   <td><a href="v2-oauth2-client-creds-grant-flow.md">Credenziali del client</a></td>
   <td>Autorizzazioni solo per app senza utente e usate solo nelle organizzazioni Azure AD</td>
 </tr>

  <tr>
   <td><a href=scenario-web-api-call-api-overview.md"><img alt="Web API that calls web APIs" src="media/scenarios/web-api.svg"></a></td>
   <td><a href=scenario-web-api-call-api-overview.md">API Web che chiama le API Web</a></td>
   <td><a href="v2-oauth2-on-behalf-of-flow.md">Per conto di</a></td>
   <td>Account aziendali o dell'istituto di istruzione e account personali</td>
 </tr>

 </tbody>
</table>

## <a name="scenarios-and-supported-platforms-and-languages"></a>Scenari e piattaforme e linguaggi supportati

Le librerie Microsoft Authentication Library supportano più piattaforme:

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

Per creare le applicazioni è anche possibile usare vari linguaggi. Alcuni tipi di applicazioni non sono disponibili in ogni piattaforma.

Nella colonna Windows della tabella seguente, ogni volta che viene menzionato .NET Core è anche possibile usare .NET Framework. Quest'ultima piattaforma viene omessa per evitare di creare confusione nella tabella.

|Scenario  | Windows | Linux | Mac | iOS | Android
|--|--|--|--|--|--|--|
| [App a singola pagina](scenario-spa-overview.md) <br/>[![App a pagina singola](media/scenarios/spa-app.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js
| [App Web che concede l'accesso agli utenti](scenario-web-app-sign-user-overview.md) <br/>[![App Web che concede l'accesso agli utenti](media/scenarios/scenario-webapp-signs-in-users.svg)](scenario-web-app-sign-user-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core
| [App Web che chiama le API Web](scenario-web-app-call-api-overview.md) <br/> <br/>[![App Web che chiama le API Web](media/scenarios/web-app.svg)](scenario-web-app-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png) <br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Flask + MSAL Python| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Flask + MSAL Python| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Flask + MSAL Python
| [App desktop che chiama le API Web](scenario-desktop-overview.md) <br/> <br/>[![App desktop che chiama le API Web](media/scenarios/desktop-app.svg)](scenario-desktop-overview.md) ![Flusso di codice del dispositivo](media/scenarios/device-code-flow-app.svg) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python <br/> MSAL.objc |
| [App per dispositivi mobili che chiama le API Web](scenario-mobile-overview.md) <br/> [![App per dispositivi mobili che chiama le API Web](media/scenarios/mobile-app.svg)](scenario-mobile-overview.md) | ![UWP](media/sample-v2-code/small_logo_windows.png) MSAL.NET ![Xamarin](media/sample-v2-code/small_logo_xamarin.png) MSAL.NET | | | ![iOS/Objective C o swift](media/sample-v2-code/small_logo_iOS.png) MSAL.objc | ![Android](media/sample-v2-code/small_logo_Android.png) MSAL.Android
| [App daemon](scenario-daemon-overview.md) <br/> [![App daemon](media/scenarios/daemon-app.svg)](scenario-daemon-overview.md) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png) MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python
| [App Web che chiama le API Web](scenario-web-api-call-api-overview.md) <br/><br/> [![App Web che chiama le API Web](media/scenarios/web-api.svg)](scenario-web-api-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python

Vedere anche [Librerie supportate da Microsoft per sistema operativo/linguaggio](reference-v2-libraries.md#microsoft-supported-libraries-by-os--language).

## <a name="next-steps"></a>Passaggi successivi
Vedere altre informazioni sui [concetti di base dell'autenticazione](authentication-scenarios.md) e i [token di accesso](access-tokens.md).
