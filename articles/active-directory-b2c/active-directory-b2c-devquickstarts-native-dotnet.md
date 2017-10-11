---
title: Azure Active Directory B2C | Documentazione Microsoft
description: "Come creare un'applicazione desktop di Windows con funzionalità di gestione dell'iscrizione, dell'accesso e del profilo utente usando Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 9da14362-8216-4485-960e-af17cd5ba3bd
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
ms.openlocfilehash: 8e2b5c704230ee2ba1395dc76a1551aaa8e7af7f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="azure-ad-b2c-build-a-windows-desktop-app"></a>Azure AD B2C: creare un'app desktop di Windows
Azure Active Directory (Azure AD) B2C consente di aggiungere funzionalità avanzate di gestione delle identità self-service all'app desktop in pochi brevi passaggi. Questo articolo descrive come creare un'app Windows Presentation Foundation (WPF) .NET "To do list" con funzionalità di gestione dell'iscrizione, dell'accesso e del profilo utente. L'app includerà il supporto per l'iscrizione e l'accesso tramite un nome utente o un indirizzo di posta elettronica. L'app includerà anche il supporto per l'iscrizione e l'accesso tramite account di social networking quali Facebook e Google.

## <a name="get-an-azure-ad-b2c-directory"></a>Ottenere una directory di Azure AD B2C
Prima di poter usare Azure AD B2C, è necessario creare una directory, o tenant.  Una directory è un contenitore per utenti, app, gruppi e così via. Se non è già stato fatto, [creare una directory B2C](active-directory-b2c-get-started.md) prima di proseguire con questa guida.

## <a name="create-an-application"></a>Creare un'applicazione
Successivamente, è necessario creare un'app nella directory B2C. In questo modo Azure AD acquisisce le informazioni necessarie per comunicare in modo sicuro con l'app. Per creare un'app, [seguire questa procedura](active-directory-b2c-app-registration.md).  Assicurarsi di:

* Includere un **client nativo** nell'applicazione.
* Copiare l'**URI di reindirizzamento** `urn:ietf:wg:oauth:2.0:oob`. Si tratta dell'URL predefinito per questo esempio di codice.
* Copiare l' **ID applicazione** assegnato all'app. Sarà necessario più avanti.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Creare i criteri
In Azure AD B2C ogni esperienza utente è definita da [criteri](active-directory-b2c-reference-policies.md)specifici. Questo esempio di codice contiene tre esperienze di identità: iscrizione, accesso e modifica del profilo. È necessario creare i criteri per ogni tipo, come descritto nell' [articolo di riferimento sui criteri](active-directory-b2c-reference-policies.md#create-a-sign-up-policy). Durante la creazione dei tre criteri, assicurarsi di:

* Scegliere **Iscrizione ID utente** o **Iscrizione posta elettronica** nel pannello dei provider di identità.
* Scegliere **Nome visualizzato** e altri attributi nei criteri di iscrizione.
* Scegliere le attestazioni **Nome visualizzato** e **ID oggetto** come attestazioni dell'applicazione in tutti i criteri. È consentito scegliere anche altre attestazioni.
* Copiare il **Nome** di ogni criterio dopo averlo creato. Dovrebbero mostrare il prefisso `b2c_1_`.  I nomi dei criteri saranno necessari in un secondo momento.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Dopo aver creato i tre criteri, è possibile passare alla compilazione dell'app.

## <a name="download-the-code"></a>Scaricare il codice
Il codice per questa esercitazione è [disponibile in GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet). Per compilare l'esempio passo dopo passo, è possibile [scaricare un progetto bozza come file ZIP](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/skeleton.zip). È anche possibile clonare la struttura:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git
```

L'app completata è anche [disponibile come file ZIP](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip) o nel ramo `complete` dello stesso repository.

Dopo aver scaricato il codice di esempio, aprire il file SLN di Visual Studio per iniziare. Il progetto `TaskClient` è l'applicazione desktop WPF con cui interagisce l'utente. Ai fini di questa esercitazione, chiama un'API Web dell'attività back-end, ospitata in Azure, che archivia l'elenco attività di ogni utente.  Non è necessario compilare l'API Web, perché ne è già in esecuzione una.

Per informazioni sull'autenticazione sicura delle richieste da parte di un'API Web con Azure AD B2C, vedere l' [articolo di introduzione all'API Web](active-directory-b2c-devquickstarts-api-dotnet.md).

## <a name="execute-policies"></a>Eseguire i criteri
L'app comunica con Azure AD B2C inviando messaggi di autenticazione che specificano i criteri che devono essere eseguiti come parte della richiesta HTTP. Per le applicazioni desktop .NET, è possibile usare l'anteprima di Microsoft Authentication Library (MSAL) per inviare messaggi di autenticazione OAuth 2.0, eseguire i criteri e ottenere token per chiamare le API Web.

### <a name="install-msal"></a>Installare MSAL
Aggiungere MSAL al progetto `TaskClient` usando la console di Gestione pacchetti di Visual Studio.

```
PM> Install-Package Microsoft.Identity.Client -IncludePrerelease
```

### <a name="enter-your-b2c-details"></a>Immettere le informazioni B2C
Aprire il file `Globals.cs` e sostituire i valori della proprietà con i propri. Questa classe viene usata in tutto il progetto `TaskClient` per fare riferimento ai valori usati comunemente.

```C#
public static class Globals
{
    ...

    // TODO: Replace these five default with your own configuration values
    public static string tenant = "fabrikamb2c.onmicrosoft.com";
    public static string clientId = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
    public static string signInPolicy = "b2c_1_sign_in";
    public static string signUpPolicy = "b2c_1_sign_up";
    public static string editProfilePolicy = "b2c_1_edit_profile";

    ...
}
```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

### <a name="create-the-publicclientapplication"></a>Creare PublicClientApplication
La classe primaria di MSAL è `PublicClientApplication`. Questa classe rappresenta l'applicazione nel sistema Azure Active Directory B2C. All'avvio dell'app, creare un'istanza di `PublicClientApplication` in `MainWindow.xaml.cs`. Questa istanza può essere usata in tutta la finestra.

```C#
protected async override void OnInitialized(EventArgs e)
{
    base.OnInitialized(e);

    pca = new PublicClientApplication(Globals.clientId)
    {
        // MSAL implements an in-memory cache by default.  Since we want tokens to persist when the user closes the app,
        // we've extended the MSAL TokenCache and created a simple FileCache in this app.
        UserTokenCache = new FileCache(),
    };

    ...
```

### <a name="initiate-a-sign-up-flow"></a>Avvia un flusso di registrazione
Quando l'utente sceglie di iscriversi, avviare un flusso di iscrizione che usa i criteri di iscrizione creati. Con MSAL è sufficiente chiamare `pca.AcquireTokenAsync(...)`. I parametri passati a `AcquireTokenAsync(...)` determinano quale token si riceve, i criteri usati nella richiesta di autenticazione e altro ancora.

```C#
private async void SignUp(object sender, RoutedEventArgs e)
{
    AuthenticationResult result = null;
    try
    {
        // Use the app's clientId here as the scope parameter, indicating that
        // you want a token to the your app's backend web API (represented by
        // the cloud hosted task API).  Use the UiOptions.ForceLogin flag to
        // indicate to MSAL that it should show a sign-up UI no matter what.
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                Globals.signUpPolicy);

        // Upon success, indicate in the app that the user is signed in.
        SignInButton.Visibility = Visibility.Collapsed;
        SignUpButton.Visibility = Visibility.Collapsed;
        EditProfileButton.Visibility = Visibility.Visible;
        SignOutButton.Visibility = Visibility.Visible;

        // When the request completes successfully, you can get user
        // information from the AuthenticationResult
        UsernameLabel.Content = result.User.Name;

        // After the sign up successfully completes, display the user's To-Do List
        GetTodoList();
    }

    // Handle any exeptions that occurred during execution of the policy.
    catch (MsalException ex)
    {
        if (ex.ErrorCode != "authentication_canceled")
        {
            // An unexpected error occurred.
            string message = ex.Message;
            if (ex.InnerException != null)
            {
                message += "Inner Exception : " + ex.InnerException.Message;
            }

            MessageBox.Show(message);
        }

        return;
    }
}
```

### <a name="initiate-a-sign-in-flow"></a>Avvia un flusso di accesso
È possibile avviare un flusso di accesso allo stesso modo in cui si avvia un flusso di iscrizione. Quando l'utente accede, eseguire la stessa chiamata a MSAL, questa volta usando i propri criteri di accesso:

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
    AuthenticationResult result = null;
    try
    {
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                    string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                    Globals.signInPolicy);
        ...
```

### <a name="initiate-an-edit-profile-flow"></a>Avviare un flusso di modifica del profilo
Anche in questo caso è possibile eseguire i criteri di modifica del profilo in modo analogo:

```C#
private async void EditProfile(object sender, RoutedEventArgs e)
{
    AuthenticationResult result = null;
    try
    {
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                    string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                    Globals.editProfilePolicy);
```

In tutti questi casi, MSAL restituisce un token in `AuthenticationResult` oppure genera un'eccezione. Ogni volta che si ottiene un token da MSAL, è possibile usare l'oggetto `AuthenticationResult.User` per aggiornare i dati utente nell'app, ad esempio l'interfaccia utente. ADAL inoltre memorizza nella cache il token per l'uso in altre parti dell'applicazione.

### <a name="check-for-tokens-on-app-start"></a>Verificare la presenza di token all'avvio dell'app
È possibile usare MSAL anche per tenere traccia dello stato di accesso dell'utente.  In questa app si vuole che l'utente rimanga connesso anche dopo che ha chiuso e riaperto l'app.  Nuovamente all'interno dell'override `OnInitialized`, usare il metodo `AcquireTokenSilent` di MSAL per verificare la disponibilità di token memorizzati nella cache:

```C#
AuthenticationResult result = null;
try
{
    // If the user has has a token cached with any policy, we'll display them as signed-in.
    TokenCacheItem tci = pca.UserTokenCache.ReadItems(Globals.clientId).Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
    string existingPolicy = tci == null ? null : tci.Policy;
    result = await pca.AcquireTokenSilentAsync(new string[] { Globals.clientId }, string.Empty, Globals.authority, existingPolicy, false);

    SignInButton.Visibility = Visibility.Collapsed;
    SignUpButton.Visibility = Visibility.Collapsed;
    EditProfileButton.Visibility = Visibility.Visible;
    SignOutButton.Visibility = Visibility.Visible;
    UsernameLabel.Content = result.User.Name;
    GetTodoList();
}
catch (MsalException ex)
{
    if (ex.ErrorCode == "failed_to_acquire_token_silently")
    {
        // There are no tokens in the cache.  Proceed without calling the To Do list service.
    }
    else
    {
        // An unexpected error occurred.
        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }
        MessageBox.Show(message);
    }
    return;
}
```

## <a name="call-the-task-api"></a>Chiamare l'API delle attività
Finora si è usato MSAL per eseguire i criteri e ottenere i token.  Quando si vuole usare uno di questi token per chiamare l'API dell'attività, è possibile usare di nuovo il metodo `AcquireTokenSilent` di MSAL per verificare la disponibilità di token memorizzati nella cache:

```C#
private async void GetTodoList()
{
    AuthenticationResult result = null;
    try
    {
        // Here we want to check for a cached token, independent of whatever policy was used to acquire it.
        TokenCacheItem tci = pca.UserTokenCache.ReadItems(Globals.clientId).Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
        string existingPolicy = tci == null ? null : tci.Policy;

        // Use AcquireTokenSilent to indicate that MSAL should throw an exception if a token cannot be acquired
        result = await pca.AcquireTokenSilentAsync(new string[] { Globals.clientId }, string.Empty, Globals.authority, existingPolicy, false);

    }
    // If a token could not be acquired silently, we'll catch the exception and show the user a message.
    catch (MsalException ex)
    {
        // There is no access token in the cache, so prompt the user to sign-in.
        if (ex.ErrorCode == "failed_to_acquire_token_silently")
        {
            MessageBox.Show("Please sign up or sign in first");
            SignInButton.Visibility = Visibility.Visible;
            SignUpButton.Visibility = Visibility.Visible;
            EditProfileButton.Visibility = Visibility.Collapsed;
            SignOutButton.Visibility = Visibility.Collapsed;
            UsernameLabel.Content = string.Empty;
        }
        else
        {
            // An unexpected error occurred.
            string message = ex.Message;
            if (ex.InnerException != null)
            {
                message += "Inner Exception : " + ex.InnerException.Message;
            }
            MessageBox.Show(message);
        }

        return;
    }
    ...
```

Quando la chiamata a `AcquireTokenSilentAsync(...)` riesce e viene trovato un token nella cache, è possibile aggiungerlo all'intestazione `Authorization` della richiesta HTTP. L'API Web dell'attività userà questa intestazione per autenticare la richiesta di lettura dell'elenco attività dell'utente:

```C#
    ...
    // Once the token has been returned by MSAL, add it to the http authorization header, before making the call to access the To Do list service.
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);

    // Call the To Do list service.
    HttpResponseMessage response = await httpClient.GetAsync(Globals.taskServiceUrl + "/api/tasks");
    ...
```

## <a name="sign-the-user-out"></a>Disconnettere l'utente dall'app
Infine, è possibile usare la libreria MSAL per terminare la sessione dell'utente nell'app quando l'utente seleziona **Esci**.  Quando si usa MSAL, questa operazione viene eseguita cancellando tutti i token dalla relativa cache:

```C#
private void SignOut(object sender, RoutedEventArgs e)
{
    // Clear any remnants of the user's session.
    pca.UserTokenCache.Clear(Globals.clientId);

    // This is a helper method that clears browser cookies in the browser control that MSAL uses, it is not part of MSAL.
    ClearCookies();

    // Update the UI to show the user as signed out.
    TaskList.ItemsSource = string.Empty;
    SignInButton.Visibility = Visibility.Visible;
    SignUpButton.Visibility = Visibility.Visible;
    EditProfileButton.Visibility = Visibility.Collapsed;
    SignOutButton.Visibility = Visibility.Collapsed;
    return;
}
```

## <a name="run-the-sample-app"></a>Eseguire l'app di esempio
Infine, compilare ed eseguire l'esempio.  Effettuare l'iscrizione all'app usando un indirizzo di posta elettronica o un nome utente. Disconnettersi e accedere nuovamente con lo stesso account utente. Modificare il profilo dell'utente. Disconnettersi ed eseguire l'iscrizione usando un account utente diverso.

## <a name="add-social-idps"></a>Aggiungere i provider di identità per i social network
L'app supporta attualmente solo l'iscrizione e l'accesso dell'utente con **account locali**. Si tratta di account archiviati nella directory B2C che usano un nome utente e una password. Usando Azure AD B2C, è possibile aggiungere il supporto per altri provider di identità (IDP) senza modificare il codice.

Per aggiungere provider di identità per i social media all'applicazione, seguire le istruzioni dettagliate fornite in questi articoli. Per ogni provider di identità che si vuole supportare, è necessario registrare un'applicazione nel relativo sistema e ottenere un ID client.

* [Configurare Facebook come provider di identità](active-directory-b2c-setup-fb-app.md)
* [Configurare Google come provider di identità](active-directory-b2c-setup-goog-app.md)
* [Configurare Amazon come provider di identità](active-directory-b2c-setup-amzn-app.md)
* [Configurare LinkedIn come provider di identità](active-directory-b2c-setup-li-app.md)

Dopo aver aggiunto i provider di identità alla directory B2C, è necessario modificare ognuno dei tre criteri per includere i nuovi provider di identità, come descritto nell' [articolo di riferimento sui criteri](active-directory-b2c-reference-policies.md). Dopo aver salvato i criteri, eseguire nuovamente l'app. I nuovi provider di identità dovrebbero essere stati aggiunti tra le opzioni di accesso e iscrizione in ognuna delle esperienze per l'identità.

Provare a usare i criteri e osservare gli effetti sull'app di esempio. Aggiungere o rimuovere provider di identità, manipolare le attestazioni dell'applicazione o modificare gli attributi per l'iscrizione. Fare alcune prove fino a quando non risulta chiaro in che modo sono collegati i criteri, le richieste di autenticazione e MSAL.

Come riferimento, l'esempio completo [è disponibile come file con estensione zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip). È anche possibile clonarlo da GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git```
