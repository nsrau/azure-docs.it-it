# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Chiamare l'API Microsoft Graph da un'app Windows Desktop

Questa guida viene illustrato come ottenere un token di accesso e chiamare l'API di Microsoft Graph o altre API che richiedono i token di accesso da un endpoint di Azure Active Directory v2 un'applicazione nativa di Windows Desktop .NET (XAML).

Dopo aver completato la Guida, l'applicazione sarà in grado di chiamare un'API protetta che utilizza account personali (inclusi outlook.com, live.com e altri). L'applicazione verrà inoltre usare e gli account da qualsiasi società o organizzazione che usa Azure Active Directory dell'istituto di istruzione.  

> [!NOTE] 
> La Guida richiede Visual Studio 2015 Update 3 o Visual Studio 2017.  Non si dispone di una di queste versioni? [Scaricare gratuitamente Visual Studio 2017](https://www.visualstudio.com/downloads/).

## <a name="how-this-guide-works"></a>Come interpretare questa guida

![Come interpretare questa guida](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.png)

L'applicazione di esempio creati in questa Guida consente a un'applicazione Desktop di Windows che esegue una query API Microsoft Graph o un'API Web che accetta i token da un endpoint di Azure Active Directory v2. Per questo scenario, aggiungere un token per le richieste HTTP tramite l'intestazione di autorizzazione. Libreria di autenticazione di Microsoft (MSAL) gestisce l'acquisizione del token e il rinnovo.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Gestione dell'acquisizione di token per l'accesso ad API Web protette

Dopo che l'utente è autenticato, l'applicazione di esempio riceve un token che può essere usato per eseguire query di Microsoft Graph API o un'API Web protetta da Azure Active Directory v2.

API, ad esempio Microsoft Graph richiedono un token per consentire l'accesso a risorse specifiche. Ad esempio, un token è necessario per leggere un profilo utente, del calendario dell'utente di accedere o inviare tramite posta elettronica. L'applicazione può richiedere un token di accesso tramite MSAL per accedere a tali risorse specificando gli ambiti di API. Questo token di accesso viene quindi aggiunto all'intestazione di autorizzazione HTTP di ogni chiamata viene effettuata alla risorsa protetta. 

MSAL gestisce la memorizzazione nella cache e aggiornando i token di accesso, in modo che l'applicazione non è necessario.

## <a name="nuget-packages"></a>Pacchetti NuGet

Questa guida usa i pacchetti NuGet seguenti:

|Libreria|DESCRIZIONE|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft Authentication Library (MSAL)|

