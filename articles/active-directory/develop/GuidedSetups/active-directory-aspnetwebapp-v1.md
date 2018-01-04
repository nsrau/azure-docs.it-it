---
title: Server Web di Azure AD v1 ASP.NET introduzione | Documenti Microsoft
description: Implementazione di accessi Microsoft in una soluzione ASP.NET con un'applicazione tradizionale basata su Web browser tramite lo standard OpenID Connect
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/08/2017
ms.author: andret
ms.openlocfilehash: b23afd26f7ac1828381a0410d2455206c8f43c88
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2018
---
<!--start-intro-->
# <a name="add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Aggiungere l'accesso con Microsoft a un'app Web ASP.NET

Questa guida illustra come implementare l'accesso con Microsoft usando una soluzione ASP.NET MVC con un'applicazione tradizionale basata su Web browser tramite OpenID Connect. 

Al termine di questa Guida, l'applicazione accetta accessi di account aziendale e dell'istituto di istruzione di organizzazioni che sono integrate con Azure Active Directory.

> [!NOTE]
> Il programma di installazione guidata consente di abilitare accessi da un account aziendale e dell'istituto di istruzione nell'applicazione ASP.NET. Se si desidera abilitare accessi per gli account personali oltre a account aziendale e dell'istituto di istruzione, è possibile utilizzare il [v2 endpoint](../active-directory-v2-compare.md). Vedere [questa applicazione ASP.NET interattiva il programma di installazione per l'endpoint v2](./active-directory-aspnetwebapp.md) nonché [questo documento](../active-directory-v2-limitations.md) spiegazione delle limitazioni correnti dell'endpoint v2.
<br/><br/>

<!--separator-->

> Questa guida richiede Visual Studio 2015 Update 3 o Visual Studio 2017.  Se non lo si ha, è possibile  [scaricare Visual Studio 2017 gratuitamente](https://www.visualstudio.com/downloads/)

## <a name="how-this-guide-works"></a>Come interpretare questa guida

![Come interpretare questa guida](media/active-directory-aspnetwebapp-v1/aspnet-intro.png)

Questa guida si basa su uno scenario in cui un browser accede a un sito Web ASP.NET e chiede agli utenti di eseguire l'autenticazione tramite un pulsante di accesso. In questo scenario, la maggior parte delle operazioni necessarie per il rendering della pagina Web viene eseguita sul lato server.

> [!NOTE]
> Il programma di installazione guidata di seguito viene illustrato come accedere gli utenti di un'applicazione Web ASP.NET a partire da un modello vuoto e includono operazioni come l'aggiunta di un segno di pulsante e ogni controller e metodi, mentre anche alcuni concetti. In alternativa, è anche possibile creare un progetto per l'accesso in Azure Active Directory gli utenti (account aziendale e dell'istituto di istruzione) usando il [modello web di Visual Studio](https://docs.microsoft.com/aspnet/visual-studio/overview/2013/creating-web-projects-in-visual-studio#organizational-account-authentication-options) e selezionando *account aziendali* e quindi una delle opzioni cloud - questa opzione utilizza un modello più dettagliato, con ulteriori controller, metodi e le visualizzazioni.

## <a name="libraries"></a>Librerie

Questa guida vengono usati i pacchetti seguenti:

|Libreria|DESCRIZIONE|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Middleware che consente a un'applicazione di usare OpenID Connect per l'autenticazione|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Middleware che consente a un'applicazione di mantenere la sessione utente usando i cookie|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Consente l'esecuzione in IIS di applicazioni basate su OWIN tramite la pipeline di richieste ASP.NET|


<!--end-intro-->

<!--start-setup-->

## <a name="set-up-your-project"></a>Configurare il progetto

Questa sezione illustra la procedura per l'installazione e la configurazione della pipeline di autenticazione tramite middleware OWIN in un progetto ASP.NET mediante OpenID Connect. 

> Se invece si preferisce scaricare questo progetto Visual Studio di esempio, [Scaricare un progetto](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/GuidedSetup.zip) e passare direttamente al [passaggio di configurazione](#configure-your-webconfig-and-register-an-application) per configurare il codice di esempio prima di eseguirlo.

## <a name="create-your-aspnet-project"></a>Creare un progetto ASP.NET
1. In Visual Studio: `File` > `New` > `Project`<br/>
2. In *Visual C#\Web* selezionare `ASP.NET Web Application (.NET Framework)`
3. Assegnare un nome all'applicazione e fare clic su *OK*
4. Selezionare `Empty` e quindi selezionare la casella di controllo per aggiungere `MVC` riferimenti

## <a name="add-authentication-components"></a>Aggiungere i componenti per l'autenticazione

1. In Visual Studio: `Tools` > `Nuget Package Manager` > `Package Manager Console`
2. Aggiungere *pacchetti NuGet del middleware OWIN* digitando quanto segue nella finestra della Console di Gestione pacchetti:

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

<!--start-collapse-->
> ### <a name="about-these-packages"></a>Su questi pacchetti
>Le librerie precedenti abilitano l'accesso Single Sign-On (SSO) usando OpenID Connect tramite l'autenticazione basata su cookie. Al termine dell'autenticazione e dopo l'invio del token che rappresenta l'utente all'applicazione, il middleware OWIN crea un cookie di sessione. Il browser utilizza quindi questo cookie nelle richieste successive in modo che l'utente non è necessario ripetere l'autenticazione e non verifica aggiuntiva è necessaria.
<!--end-collapse-->

## <a name="configure-the-authentication-pipeline"></a>Configurare la pipeline di autenticazione
I passaggi seguenti consentono di creare un middleware OWIN *avvio classe* per configurare l'autenticazione OpenID Connect. Questa classe viene eseguita automaticamente.

> [!TIP]
> Se il progetto non include alcun file `Startup.cs` nella cartella radice:<br/>
> 1. Fare clic su una cartella radice del progetto: >`Add` > `New Item...` > `OWIN Startup class`<br/>
> 2. Assegnare il nome `Startup.cs`<br/>
>
>> Assicurarsi che la classe selezionata sia una classe di avvio di OWIN e non una classe C# standard. Per confermarlo, verificare se `[assembly: OwinStartup(typeof({NameSpace}.Startup))]` viene visualizzato sopra lo spazio dei nomi.


1. Aggiungere *OWIN* e *Microsoft. IdentityModel* gli spazi dei nomi per `Startup.cs`:

    [!code-csharp[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Startup.cs?name=AddedNameSpaces "Startup.cs")]

2. Sostituire la classe di avvio con il codice seguente:

    [!code-csharp[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Startup.cs?name=Startup "Startup.cs")]
    
<!--start-collapse-->
> [!NOTE]
> I parametri forniti in *OpenIDConnectAuthenticationOptions* fungeranno da coordinate per consentire all'applicazione di comunicare con Azure AD. Poiché il middleware di OpenID Connect utilizza i cookie, è necessario anche configurare l'autenticazione dei cookie come illustrato nel codice precedente. Il valore *ValidateIssuer* segnala a OpenIdConnect di non limitare l'accesso a un'organizzazione specifica.
<!--end-collapse-->

<!--end-setup-->

<!--start-use-->

## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>Aggiungere un controller per gestire le richieste di accesso e disconnessione

Questo passaggio illustra come creare un nuovo controller per esporre i metodi di accesso e disconnessione.

1.  Fare doppio clic su di `Controllers` cartella e scegliere`Add` > `Controller`
2.  Selezionare `MVC (.NET version) Controller – Empty`.
3.  Fare clic su *Aggiungi*.
4.  Assegnare il nome `HomeController` e fare clic su *Aggiungi*.
5.  Aggiungere *OWIN* gli spazi dei nomi alla classe:

    [!code-csharp[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Controllers\HomeController.cs?name=AddedNameSpaces "HomeController.cs")]

6. Aggiungere i metodi seguenti per gestire l'accesso e disconnessione al controller di avvio di una richiesta di autenticazione tramite codice:

    [!code-csharp[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Controllers\HomeController.cs?name=SigInAndSignOut "HomeController.cs")]
    
## <a name="create-the-apps-home-page-to-sign-in-users-via-a-sign-in-button"></a>Creare la home page dell'app per l'accesso degli utenti tramite un pulsante di accesso

In Visual Studio creare una nuova visualizzazione per aggiungere il pulsante di accesso e mostrare le informazioni relative all'utente dopo l'autenticazione:

1.  Fare doppio clic su di `Views\Home` cartella e scegliere`Add View`
2.  Denominarlo `Index`.
3.  Aggiungere al file il codice HTML seguente, che include il pulsante di accesso:

    [!code-html[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet/Views/Home/Index.cshtml "Index.cshtml")]

<!--start-collapse-->
> [!NOTE]
> Questa pagina aggiunge un pulsante di accesso in formato SVG con sfondo nero:<br/>![Pulsante "Accedi con Microsoft"](media/active-directory-aspnetwebapp-v1/aspnetsigninbuttonsample.png)<br/> Per i pulsanti di segno più, passare a [questa pagina](https://docs.microsoft.com/azure/active-directory/develop/active-directory-branding-guidelines "linee guida del marchio").
<!--end-collapse-->

## <a name="display-users-claims-by-adding-a-controller"></a>Visualizzare le attestazioni dell'utente tramite l'aggiunta di un controller
Questo controller illustra gli usi dell'attributo `[Authorize]` per la protezione di un controller. Questo attributo limita l'accesso al controller ai soli utenti autenticati. Nell'esempio di codice rende utilizzare l'attributo per visualizzare le attestazioni utente che sono state recuperate come parte di accesso.

1.  Fare doppio clic su di `Controllers` cartella:`Add` > `Controller`
2.  Selezionare `MVC {version} Controller – Empty`.
3.  Fare clic su *Aggiungi*.
4.  Assegnare il nome `ClaimsController`
5.  Sostituire il codice della classe controller con il codice seguente, viene aggiunto il `[Authorize]` attributo alla classe:

    [!code-csharp[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet\Controllers\ClaimsController.cs?name=ClaimsController "ClaimsController.cs")]

<!--start-collapse-->
> [!NOTE]
> A causa dell'uso dell'attributo `[Authorize]`, tutti i metodi di questo controller possono essere eseguiti solo se l'utente è autenticato. Se l'utente non autenticato e tenta di accedere al controller, OWIN avvia una richiesta di autenticazione e forzare l'autenticazione dell'utente. Analizza il codice precedente la raccolta di attestazioni dell'utente per gli attributi specifici incluse nel token dell'utente. Tali attributi includono nome e cognome, nome utente e soggetto ID utente globale dell'utente. Contengono anche l'*ID tenant*, che rappresenta l'ID dell'organizzazione dell'utente. 
<!--end-collapse-->

## <a name="create-a-view-to-display-the-users-claims"></a>Creare una visualizzazione per mostrare le attestazioni dell'utente

In Visual Studio creare una nuova visualizzazione per mostrare le attestazioni dell'utente in una pagina Web:

1.  Fare doppio clic su di `Views\Claims` cartella e:`Add View`
2.  Denominarlo `Index`.
3.  Aggiungere il codice HTML seguente al file:

    [!code-html[main](../../../../WebApp-OpenIDConnect-DotNet/WebApp-OpenIDConnect-DotNet/Views/Claims/Index.cshtml "Index.cshtml")]
    
<!--end-use-->

<!--start-configure-->
## <a name="configure-your-webconfig-and-register-an-application"></a>Configurare il *Web. config* e registrare un'applicazione

1. In Visual Studio, aggiungere quanto segue a `web.config` (che si trova nella cartella radice) nella sezione `configuration\appSettings`:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="RedirectUrl" value="Enter_the_Redirect_Url_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}" /> 
    ```
2. In Esplora soluzioni selezionare il progetto ed esaminare il <i>proprietà</i> finestra (se non viene visualizzata una finestra delle proprietà, premere F4)
3. Impostare il valore di SSL abilitato su <code>True</code>
4. Copia URL SSL del progetto negli Appunti:<br/><br/>![Proprietà del progetto](media/active-directory-aspnetwebapp-v1/visual-studio-project-properties.png)<br />
5. In <code>web.config</code>, sostituire <code>Enter_the_Redirect_URL_here</code> con l'URL SSL del progetto 

### <a name="register-your-application-in-the-azure-portal-then-add-its-information-to-webconfig"></a>Registrare l'applicazione nel portale di Azure, quindi aggiungere le informazioni per *Web. config*

1. Passare al [portale Microsoft Azure - registrazioni di App](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) per registrare un'applicazione
2. Selezionare `New application registration`.
3. Immettere un nome per l'applicazione
4. Incollare il progetto di Visual Studio *URL SSL* in `Sign-on URL` (questo URL viene inoltre aggiunto automaticamente all'elenco di URL di risposta per l'applicazione si sta registrando)
5. Fare clic su `Create` per registrare l'applicazione. Questa azione consente di tornare all'elenco di applicazioni
6. A questo punto, eseguire la ricerca e/o selezionare l'applicazione appena creata per aprirne le relative proprietà
7. Copiare il guid in `Application ID` negli Appunti
8. Tornare a Visual Studio e, in `web.config`, sostituire `Enter_the_Application_Id_here` con l'ID dell'applicazione dall'applicazione appena registrato

> [!TIP]
> Se l'account è configurato per l'accesso a più directory, assicurarsi di avere selezionato la directory per l'organizzazione si desidera che l'applicazione deve essere registrato facendo clic sul nome di account in alto a destra nel portale di Azure e quindi verificare il directory selezionata come indicato:<br/>![Selezionare la directory](media/active-directory-aspnetwebapp-v1/tenantselector.png)

## <a name="configure-sign-in-options"></a>Configurare le opzioni di accesso

È possibile configurare l'applicazione per consentire solo agli utenti che appartengono all'istanza di Azure Active Directory dell'organizzazione di accedere, o accettare accessi da utenti che appartengono a qualsiasi organizzazione. Seguire le istruzioni di una delle opzioni seguenti:

### <a name="configure-your-application-to-allow-sign-ins-of-work-and-school-accounts-from-any-company-or-organization-multi-tenant"></a>Configurare l'applicazione per consentire accessi di account aziendale e dell'istituto di istruzione da qualsiasi società o organizzazione (multi-tenant)

Seguire la procedura seguente se si desidera accettare accessi di account aziendale e dell'istituto di istruzione da qualsiasi società o organizzazione che è integrato con Azure Active Directory. Si tratta di uno scenario comune per *applicazioni SaaS*:

1. Tornare al [portale Microsoft Azure - registrazioni di App](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) e individuare l'applicazione appena registrato
2. In `All Settings` selezionare`Properties`
3. Modifica `Multi-tenanted` proprietà `Yes` e fare clic su`Save`

Per ulteriori informazioni su questa impostazione e il concetto di applicazioni multi-tenant, vedere [questo articolo](../active-directory-devhowto-multi-tenant-overview.md "Panoramica multi-tenant").

### <a name="restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>Impedire agli utenti di istanza di Active Directory del sola organizzazione per accedere all'applicazione (single-tenant)

Questa opzione è uno scenario comune per *applicazioni LOB*: se si desidera che l'applicazione per accettare accessi solo da account appartenenti a una specifica istanza di Azure Active Directory (incluse *account guest*dell'istanza), sostituire il `Tenant` parametro *Web. config* da `Common` con il nome del tenant dell'organizzazione: esempio *contoso.onmicrosoft.com*. Successivamente, modificare il `ValidateIssuer` argomento in del [ *classe di avvio di OWIN* ](#configure-the-authentication-pipeline) a `true`.

Per consentire l'accesso solo agli utenti di un elenco di organizzazioni specifiche, impostare `ValidateIssuer` su true e usare il parametro `ValidIssuers` per specificare un elenco di organizzazioni.

Un'altra opzione consiste nell'implementare un metodo personalizzato per convalidare gli emittenti utilizzando *IssuerValidator* parametro. Per ulteriori informazioni su `TokenValidationParameters`, vedere [questo articolo MSDN](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx "articolo MSDN TokenValidationParameters").

<!--end-configure-->

<!--start-configure-arp-->
<!--
## Configure your ASP.NET Web App with the application's registration information

In this step, you will configure your project to use SSL, and then use the SSL URL to configure your application’s registration information. After this, add the application’ registration information to your solution via *web.config*.

1.  In Solution Explorer, select the project and look at the `Properties` window (if you don’t see a Properties window, press F4)
2.  Change `SSL Enabled` to `True`
3.  Copy the value from `SSL URL` above and paste it in the `Redirect URL` field on the top of this page, then click *Update*:<br/><br/>![Project properties](media/active-directory-aspnetwebapp-v1/vsprojectproperties.png)<br />
4.  Add the following in `web.config` file located in root’s folder, under section `configuration\appSettings`:

```xml
<add key="ClientId" value="[Enter the application Id here]" />
<add key="RedirectUri" value="[Enter the Redirect URL here]" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}" /> 
```
-->
<!--end-configure-arp-->
<!--start-test-->
## <a name="test-your-code"></a>Testare il codice

Premere `F5` per eseguire il progetto in Visual Studio. Il browser apre e indirizza di *http://localhost: {porta}* consente di visualizzare il *accedere con Microsoft* pulsante. Fare clic per eseguire l'accesso.

Quando si è pronti per testare, utilizzare un account aziendale (Azure Active Directory) per accedere. 

![Accedere con una finestra del browser Microsoft](media/active-directory-aspnetwebapp-v1/aspnetbrowsersignin.png)

![Accedere con una finestra del browser Microsoft](media/active-directory-aspnetwebapp-v1/aspnetbrowsersignin2.png)

#### <a name="expected-results"></a>Risultati previsti
Dopo l'accesso, l'utente viene reindirizzato alla home page del sito web, è l'URL HTTPS specificato nelle informazioni di registrazione dell'applicazione nel portale di registrazione dell'applicazione di Microsoft. In questa pagina vengono ora *Hello {User}* e un collegamento di disconnessione e un collegamento per visualizzare le attestazioni dell'utente, che è un collegamento al controller di Authorize creato in precedenza.

### <a name="see-users-claims"></a>Visualizzare le attestazioni dell'utente
Selezionare il collegamento ipertestuale per visualizzare le attestazioni dell'utente. Questa azione si comporta il controller e una vista in cui è disponibile solo per gli utenti autenticati.

#### <a name="expected-results"></a>Risultati previsti
 Verrà visualizzata una tabella contenente le proprietà di base dell'utente connesso:

| Proprietà | Valore | DESCRIZIONE|
|---|---|---|
| NOME | {Nome e cognome utente} | Nome e cognome dell'utente
|Username | <span>user@domain.com</span>| Nome utente usato per identificare l'utente connesso
| Oggetto| {Soggetto}|Stringa che identifica in modo univoco l'account di accesso dell'utente sul Web|
| ID tenant| {GUID}| *GUID* che rappresenta in modo univoco l'organizzazione di Azure Active Directory dell'utente.|

Inoltre, possibile visualizzare una tabella, incluse tutte le attestazioni utente incluse nella richiesta di autenticazione. Per un elenco di tutte le attestazioni in ID Token e la relativa spiegazione, vedere questo [articolo](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims "elenco di attestazioni nel ID Token").


### <a name="test-accessing-a-method-that-has-an-authorize-attribute-optional"></a>Testare l'accesso a un metodo con attributo *[Authorize]* (facoltativo)
In questo passaggio verificare l'accesso a controller di attestazioni come utente anonimo:<br/>
Selezionare il collegamento per la disconnessione dell'utente e completare il processo di disconnessione.<br/>
A questo punto, nel browser, digitare http://localhost: {porta} / attestazioni accedere il controller che è protetto con il `[Authorize]` attributo

#### <a name="expected-results"></a>Risultati previsti
Verrà visualizzata la richiesta di eseguire l'autenticazione per accedere alla visualizzazione.

## <a name="additional-information"></a>Informazioni aggiuntive

<!--start-collapse-->
### <a name="protect-your-entire-web-site"></a>Proteggere l'intero sito Web
Per proteggere l'intero sito Web, aggiungere `AuthorizeAttribute` a `GlobalFilters` nel metodo `Application_Start` di `Global.asax`:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

<div></div>
<br/>

<!--end-test-->