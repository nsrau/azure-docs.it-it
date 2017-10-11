---
title: Introduzione a .NET per Azure AD | Documentazione Microsoft
description: Come compilare un'applicazione desktop di Windows .NET che si integra con Azure AD per l'accesso e chiama le API protette di Azure AD usando OAuth.
services: active-directory
documentationcenter: .net
author: jmprieur
manager: mbaldwin
editor: 
ms.assetid: ed33574f-6fa3-402c-b030-fae76fba84e1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 7a252e0e5243c7b7489373845531cb913ca1f6aa
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="integrate-azure-ad-into-a-windows-desktop-wpf-app"></a>Integrare Azure AD in un'app WPF desktop di Windows
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Se si sta sviluppando un'applicazione desktop, Azure AD semplifica e facilita l'autenticazione degli utenti con gli account Active Directory.  Consente inoltre all'applicazione di usare in modo sicuro qualsiasi API Web protetta da Azure AD, ad esempio le API di Office 365 o l'API di Azure.

Per i client nativi .NET che devono accedere a risorse protette, Azure AD fornisce Active Directory Authentication Library (ADAL).  La funzione di ADAL è di permettere all'app di ottenere facilmente i token di accesso.  Per far capire quanto è semplice, verrà compilata un'applicazione WPF .NET To-Do List che:

* Ottiene i token di accesso per la chiamata all'API Graph di Azure AD con il [protocollo di autenticazione OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* Cerca in una directory gli utenti con un determinato alias.
* Disconnette gli utenti.

Per compilare l'applicazione funzionante completa, sarà necessario:

1. Registrare l'applicazione con Azure AD.
2. Installare e configurare ADAL.
3. Usare ADAL per ottenere i token da Azure AD.

Per iniziare, [scaricare la struttura dell'app](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/skeleton.zip) o [scaricare l'esempio completato](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip).  Sarà necessario anche un tenant di Azure AD in cui poter creare gli utenti e registrare un'applicazione.  Se non si ha già un tenant, vedere le [informazioni su come ottenerne uno](active-directory-howto-tenant.md).

## <a name="1-register-the-directorysearcher-application"></a>1. Registrare l'applicazione DirectorySearcher
Per consentire all'applicazione di ottenere i token, sarà innanzitutto necessario registrarla nel tenant di Azure AD e concederle l'autorizzazione per accedere all'API Graph di Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nella barra in alto fare clic sull'account e nell'elenco **Directory** scegliere il tenant di Active Directory in cui si vuole registrare l'applicazione.
3. Fare clic su **Altri servizi** nella barra di spostamento a sinistra e scegliere **Azure Active Directory**.
4. Fare clic su **App registrations (Registrazioni app)** e scegliere **Aggiungi**.
5. Seguire le istruzioni e creare una nuova **Applicazione client nativa**.
  * Il **Nome** dell'applicazione deve essere una descrizione per gli utenti finali.
  * L' **URI di reindirizzamento** è una combinazione dello schema e della stringa che Azure AD userà per restituire le risposte dei token.  Immettere un valore specifico per l'applicazione, ad esempio `http://DirectorySearcher`.
6. Dopo avere completato la registrazione, AAD assegnerà all'app un'ID app univoca.  Poiché questo valore sarà necessario nelle sezioni successive, copiarlo dalla pagina dell'applicazione.
7. Nella pagina **Impostazioni** scegliere **Autorizzazioni necessarie** e quindi scegliere **Aggiungi**. Selezionare **Microsoft Graph** come API e aggiungere l'autorizzazione **Lettura dati directory** in **Autorizzazioni delegate**.  In questo modo l'applicazione potrà cercare gli utenti nell'API Graph.

## <a name="2-install--configure-adal"></a>2. Installare e configurare ADAL
Ora che si dispone di un'applicazione in Azure AD, è possibile installare ADAL e scrivere il codice relativo all'identità.  Affinché la libreria ADAL possa comunicare con Azure AD, è necessario fornire alcune informazioni relative alla registrazione dell'app.

* Per prima cosa aggiungere ADAL al progetto DirectorySearcher usando la console di Gestione pacchetti.

```
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

* Nel progetto DirectorySearcher aprire `app.config`.  Sostituire i valori degli elementi nella sezione `<appSettings>` in modo che corrispondano ai valori inseriti nel portale di Azure.  Il codice farà riferimento a questi valori ogni volta che userà ADAL.
  * `ida:Tenant` è il dominio del tenant di Azure AD, ad esempio, contoso.onmicrosoft.com.
  * `ida:ClientId` è l'ID client dell'applicazione copiato dal portale.
  * `ida:RedirectUri` è l'URL di reindirizzamento registrato nel portale.

## <a name="3----use-adal-to-get-tokens-from-aad"></a>3.    Usare ADAL per ottenere i token da AAD
Il principio alla base di ADAL è che l'app, ogni volta che ha bisogno di un token di accesso, deve solo chiamare `authContext.AcquireTokenAsync(...)` e ADAL fa il resto.  

* Nel progetto `DirectorySearcher` aprire `MainWindow.xaml.cs` e individuare il metodo `MainWindow()`.  Il primo passaggio consiste nell'inizializzare l'oggetto `AuthenticationContext` dell'app, ovvero la classe primaria di ADAL,  dove si passano ad ADAL le coordinate di cui ha bisogno per comunicare con Azure AD e gli si indica come memorizzare i token nella cache.

```C#
public MainWindow()
{
    InitializeComponent();

    authContext = new AuthenticationContext(authority, new FileCache());

    CheckForCachedToken();
}
```

* Individuare ora il metodo `Search(...)`, che verrà richiamato quando l'utente fa clic sul pulsante "Search" nell'interfaccia utente dell'app.  Questo metodo invia una richiesta GET all'API Graph di Azure AD per eseguire una query sugli utenti il cui UPN inizia con il termine di ricerca specificato.  Per eseguire una query nell'API Graph, è però necessario includere un oggetto access_token nell'intestazione `Authorization` della richiesta, dove entra in gioco ADAL.

```C#
private async void Search(object sender, RoutedEventArgs e)
{
    // Validate the Input String
    if (string.IsNullOrEmpty(SearchText.Text))
    {
        MessageBox.Show("Please enter a value for the To Do item name");
        return;
    }

    // Get an Access Token for the Graph API
    AuthenticationResult result = null;
    try
    {
        result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Auto));
        UserNameLabel.Content = result.UserInfo.DisplayableId;
        SignOutButton.Visibility = Visibility.Visible;
    }
    catch (AdalException ex)
    {
        // An unexpected error occurred, or user canceled the sign in.
        if (ex.ErrorCode != "access_denied")
            MessageBox.Show(ex.Message);

        return;
    }

    ...
}
```
* Quando l'app richiede un token chiamando `AcquireTokenAsync(...)`, ADAL tenterà di restituire un token senza chiedere le credenziali all'utente.  Se ADAL determina che l'utente deve effettuare l'accesso per ottenere un token, visualizzerà una finestra di dialogo di accesso, raccoglierà le credenziali dell'utente e restituirà un token al termine dell'autenticazione.  Se ADAL non può restituire un token per qualsiasi motivo, genera una `AdalException`.
* Si noti che l'oggetto `AuthenticationResult` contiene un oggetto `UserInfo` che può essere usato per raccogliere informazioni che potrebbero essere richieste dall'app.  In DirectorySearcher `UserInfo` viene usato per personalizzare l'interfaccia utente dell'app con l'ID dell'utente.
* È opportuno assicurarsi che, quando l'utente fa clic sul pulsante "Sign Out", la chiamata successiva a `AcquireTokenAsync(...)` chiederà all'utente di accedere.  Con ADAL, basta cancellare la cache dei token:

```C#
private void SignOut(object sender = null, RoutedEventArgs args = null)
{
    // Clear the token cache
    authContext.TokenCache.Clear();

    ...
}
```

* Se tuttavia l'utente non fa clic sul pulsante di "disconnessione", è consigliabile mantenere la sessione dell'utente per la prossima esecuzione di DirectorySearcher.  Quando viene avviata l'app, è possibile cercare nella cache dei token di ADAL se esiste un token e aggiornare di conseguenza l'interfaccia utente.  Nel metodo `CheckForCachedToken()`, eseguire un'altra chiamata a `AcquireTokenAsync(...)`, questa volta superando il parametro `PromptBehavior.Never`.  `PromptBehavior.Never` comunicherà ad ADAL che non dovrà richiedere all'utente di accedere e dovrà invece generare un'eccezione se non è in grado di restituire un token.

```C#
public async void CheckForCachedToken() 
{
    // As the application starts, try to get an access token without prompting the user.  If one exists, show the user as signed in.
    AuthenticationResult result = null;
    try
    {
        result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Never));
    }
    catch (AdalException ex)
    {
        if (ex.ErrorCode != "user_interaction_required")
        {
            // An unexpected error occurred.
            MessageBox.Show(ex.Message);
        }

        // If user interaction is required, proceed to main page without singing the user in.
        return;
    }

    // A valid token is in the cache
    SignOutButton.Visibility = Visibility.Visible;
    UserNameLabel.Content = result.UserInfo.DisplayableId;
}
```

Congratulazioni. È stata compilata un'applicazione WPF .NET in grado di autenticare gli utenti, di chiamare in modo sicuro le API Web usando OAuth 2.0 e di ottenere informazioni di base sull'utente.  Se non si è ancora popolato il tenant con alcuni utenti, ora è possibile farlo.  Eseguire l'app DirectorySearcher e accedere con uno di tali utenti.  Cercare altri utenti in base al relativo UPN.  Chiudere l'app e rieseguirla.  Si noti che la sessione dell'utente non è stata modificata.  Disconnettersi e accedere nuovamente come un altro utente.

ADAL consente di incorporare facilmente nell'applicazione tutte queste funzionalità comuni relative alle identità.  Esegue automaticamente le attività più complesse: gestione della cache, supporto del protocollo OAuth, presentazione all'utente di un'interfaccia utente di accesso, aggiornamento dei token scaduti e altro.  Tutto ciò che occorre conoscere è una sola chiamata all'API, `authContext.AcquireTokenAsync(...)`.

Come riferimento, viene fornito l'esempio completato (senza i valori di configurazione) [qui](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip).  Ora è possibile passare ad altri scenari.  È possibile:

[Proteggere un'API Web .NET con Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]

