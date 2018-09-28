---
title: Accesso utenti e chiamata dell'API Microsoft Graph da un'app Desktop .NET (WPF) | Microsoft Docs
description: Informazioni su come compilare un'applicazione Windows Desktop .NET che si integra con Azure AD per l'accesso e chiama le API protette di Azure AD usando OAuth 2.0.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: ed33574f-6fa3-402c-b030-fae76fba84e1
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: f9389a7c0e80f075c01f2236fa1bdf9dc9544ac6
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46987442"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-a-net-desktop-wpf-app"></a>Guida introduttiva: Accesso utenti e chiamata dell'API Microsoft Graph da un'app Desktop .NET (WPF)

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Per i client nativi .NET che devono accedere a risorse protette, Azure Active Directory (Azure AD) include Active Directory Authentication Library (ADAL), che consente all'app di ottenere facilmente token di accesso. 

In questa guida introduttiva si apprenderà come creare un'applicazione .NET WPF To Do List che:

* Ottiene i token di accesso per chiamare l'API Graph di Azure AD con il protocollo di autenticazione OAuth 2.0.
* Cerca in una directory gli utenti con un determinato alias.
* Disconnette gli utenti.

Per compilare l'applicazione funzionante completa, sarà necessario:

1. Registrare l'applicazione con Azure AD.
2. Installare e configurare ADAL.
3. Usare ADAL per ottenere i token da Azure AD.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, completare questi prerequisiti:

* Scaricare la [struttura dell'app](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/skeleton.zip) oppure l'[esempio completato](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip).
* Predisporre un tenant di Azure AD in cui poter creare gli utenti e registrare un'applicazione. Se non si ha già un tenant, vedere le [informazioni su come ottenerne uno](quickstart-create-new-tenant.md).

## <a name="step-1-register-the-directorysearcher-application"></a>Passaggio 1: Registrare l'applicazione DirectorySearcher

Per consentire all'app di ottenere i token, registrare l'app nel tenant di Azure AD e concederle l'autorizzazione per accedere all'API Graph di Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nella barra in alto selezionare l'account e nell'elenco **Directory** scegliere il tenant di Active Directory in cui si vuole registrare l'applicazione.
3. Selezionare **Tutti i servizi** nella barra di spostamento a sinistra e scegliere **Azure Active Directory**.
4. In **Registrazioni app** scegliere **Aggiungi**.
5. Seguire le istruzioni e creare una nuova applicazione client **nativa**.
    * Il **Nome** dell'applicazione deve essere una descrizione per gli utenti finali.
    * L' **URI di reindirizzamento** è una combinazione dello schema e della stringa che Azure AD userà per restituire le risposte dei token. Immettere un valore specifico per l'applicazione in uso, ad esempio `http://DirectorySearcher`.

6. Dopo avere completato la registrazione, AAD assegnerà all'app un ID app univoco. Poiché questo valore sarà necessario nelle sezioni successive, copiarlo dalla pagina dell'applicazione.
7. Nella pagina **Impostazioni** scegliere **Autorizzazioni necessarie** e quindi scegliere **Aggiungi**. Selezionare **Microsoft Graph** come API e in **Autorizzazioni delegate** aggiungere l'autorizzazione **Lettura dati directory**. L'impostazione di questa autorizzazione consente all'applicazione di cercare utenti nell'API Graph.

## <a name="step-2-install-and-configure-adal"></a>Passaggio 2. Installare e configurare ADAL

Ora che si dispone di un'applicazione in Azure AD, è possibile installare ADAL e scrivere il codice relativo all'identità. Affinché ADAL possa comunicare con Azure AD, è necessario fornirle alcune informazioni sulla registrazione dell'app.

1. Iniziare aggiungendo ADAL al progetto `DirectorySearcher` tramite la console di Gestione pacchetti.

    ```
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

1. Nel progetto `DirectorySearcher` aprire `app.config`.
1. Sostituire i valori degli elementi nella sezione `<appSettings>` in modo che corrispondano ai valori inseriti nel portale di Azure. Il codice farà riferimento a questi valori ogni volta che userà ADAL.
  * `ida:Tenant` è il dominio del tenant di Azure AD, ad esempio contoso.onmicrosoft.com.
  * `ida:ClientId` è l'ID client dell'applicazione copiato dal portale.
  * `ida:RedirectUri` è l'URL di reindirizzamento registrato nel portale.

## <a name="step-3-use-adal-to-get-tokens-from-azure-ad"></a>Passaggio 3. Usare ADAL per ottenere i token da Azure AD

Il principio alla base di ADAL è che l'app, ogni volta che ha bisogno di un token di accesso, deve solo chiamare `authContext.AcquireTokenAsync(...)` e ADAL fa il resto.

1. Nel progetto `DirectorySearcher` aprire `MainWindow.xaml.cs`.
1. Individuare il metodo `MainWindow()`. 
1. Inizializzare l'oggetto `AuthenticationContext` dell'app, ovvero la classe primaria di ADAL. `AuthenticationContext` è l'ambiente in cui si passano ad ADAL le coordinate di cui ha bisogno per comunicare con Azure AD e gli si indica come memorizzare i token nella cache.

    ```csharp
    public MainWindow()
    {
        InitializeComponent();

        authContext = new AuthenticationContext(authority, new FileCache());

        CheckForCachedToken();
    }
    ```

1. Individuare il metodo `Search(...)`, che verrà chiamato quando l'utente seleziona il pulsante **Search** nell'interfaccia utente dell'app. Questo metodo invia una richiesta GET all'API Graph di Azure AD per eseguire una query sugli utenti il cui UPN inizia con il termine di ricerca specificato.
1. Per eseguire una query nell'API Graph, includere un oggetto access_token nell'intestazione `Authorization` della richiesta, dove entra in gioco ADAL.

    ```csharp
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

    Quando l'app richiede un token chiamando `AcquireTokenAsync(...)`, ADAL tenterà di restituire un token senza chiedere le credenziali all'utente.
    * Se ADAL determina che l'utente deve effettuare l'accesso per ottenere un token, visualizzerà una finestra di dialogo di accesso, raccoglierà le credenziali dell'utente e restituirà un token al termine dell'autenticazione. 
    * Se ADAL non può restituire un token per qualsiasi motivo, genera una `AdalException`.

1. Si noti che l'oggetto `AuthenticationResult` contiene un oggetto `UserInfo` che può essere usato per raccogliere informazioni che potrebbero essere richieste dall'app. In DirectorySearcher, `UserInfo` viene usato per personalizzare l'interfaccia utente dell'app con l'ID dell'utente.
1. È opportuno assicurarsi che, quando l'utente seleziona il pulsante **Sign Out**, la chiamata successiva a `AcquireTokenAsync(...)` chiederà all'utente di accedere. Con ADAL, è sufficiente cancellare la cache dei token:

    ```csharp
    private void SignOut(object sender = null, RoutedEventArgs args = null)
    {
        // Clear the token cache
        authContext.TokenCache.Clear();

        ...
    }
    ```

    Se l'utente non fa clic sul pulsante **Sign out**, è necessario mantenere la sessione dell'utente per la prossima esecuzione di DirectorySearcher. Quando viene avviata l'app, è possibile cercare nella cache dei token di ADAL se esiste un token e aggiornare di conseguenza l'interfaccia utente.

1. Nel metodo `CheckForCachedToken()`, eseguire un'altra chiamata a `AcquireTokenAsync(...)`, questa volta superando il parametro `PromptBehavior.Never`. `PromptBehavior.Never` comunicherà ad ADAL che non dovrà richiedere all'utente di accedere e dovrà invece generare un'eccezione se non è in grado di restituire un token.

    ```csharp
    public async void CheckForCachedToken() 
    {
        // As the application starts, try to get an access token without prompting the user. If one exists, show the user as signed in.
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

Congratulazioni! Si dispone ora di un'applicazione .NET WPF funzionante in grado di autenticare gli utenti, chiamare in modo sicuro le API Web usando OAuth 2.0 e ottenere informazioni di base sull'utente. Se non si è ancora popolato il tenant con alcuni utenti, ora è possibile farlo. Eseguire l'app DirectorySearcher e accedere con uno di tali utenti. Cercare altri utenti in base al relativo UPN. Chiudere l'app e rieseguirla. Si noti che la sessione dell'utente non è stata modificata. Disconnettersi e accedere nuovamente come un altro utente.

ADAL consente di incorporare facilmente nell'applicazione queste funzionalità comuni relative alle identità. Esegue automaticamente le attività più complesse, tra cui gestione della cache, supporto del protocollo OAuth, presentazione all'utente di un'interfaccia utente di accesso, aggiornamento dei token scaduti e altro. Tutto ciò che occorre conoscere è una sola chiamata all'API, `authContext.AcquireTokenAsync(...)`.

Come riferimento, consultare l'esempio completato (senza i valori di configurazione) disponibile [in GitHub](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip).

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come proteggere un'API Web usando token di connessione dell'accesso di OAuth 2.0.
> [!div class="nextstepaction"]
> [Proteggere un'API Web .NET con Azure AD >>](quickstart-v1-dotnet-webapi.md)
