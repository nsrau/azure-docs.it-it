<properties
	pageTitle="Introduzione a Windows Phone per Azure AD | Microsoft Azure"
	description="Come compilare un'applicazione di Windows Phone che si integra con Azure AD per l'accesso e chiama le API protette di Azure AD usando OAuth."
	services="active-directory"
	documentationCenter="windows"
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-windows-phone"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="dastrock"/>



# Integrare Azure AD con un'app di Windows Phone

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Se si sta sviluppando un'app di Windows Phone, Azure AD semplifica e facilita l'autenticazione degli utenti con gli account Active Directory. Consente inoltre all'applicazione di usare in modo sicuro qualsiasi API Web protetta da Azure AD, ad esempio le API di Office 365 o l'API di Azure.

Per i client nativi .NET che devono accedere a risorse protette, Azure AD fornisce Active Directory Authentication Library (ADAL). La funzione di ADAL è di permettere all'app di ottenere facilmente i token di accesso. Per far capire quanto è semplice, verrà compilata un'app di Windows Phone 8.1, "Directory Searcher", che:

-	Ottiene i token di accesso per la chiamata all'API Graph di Azure AD con il [protocollo di autenticazione OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
-	Cerca in una directory gli utenti con un determinato UPN.
-	Disconnette gli utenti.

Per compilare l'applicazione funzionante completa, sarà necessario:

2. Registrare l'applicazione con Azure AD.
3. Installare e configurare ADAL.
5. Usare ADAL per ottenere i token da Azure AD.

Per iniziare, [scaricare un progetto struttura](https://github.com/AzureADQuickStarts/NativeClient-WindowsPhone/archive/skeleton.zip) o [scaricare l'esempio completato](https://github.com/AzureADQuickStarts/NativeClient-WindowsPhone/archive/complete.zip). Ognuno è una soluzione di Visual Studio 2013. Sarà necessario anche un tenant di Azure AD in cui poter creare gli utenti e registrare un'applicazione. Se non si ha già un tenant, vedere le [informazioni su come ottenerne uno](active-directory-howto-tenant.md).

## *1. Registrare l'applicazione Directory Searcher*
Per consentire all'applicazione di ottenere i token, sarà innanzitutto necessario registrarla nel tenant di Azure AD e concederle l'autorizzazione per accedere all'API Graph di Azure AD:

-	Accedere al [portale di gestione di Azure](https://manage.windowsazure.com).
-	Nel pannello di navigazione a sinistra fare clic su **Active Directory**.
-	Selezionare un tenant in cui registrare l'applicazione.
-	Fare clic sulla scheda **Applicazioni**, quindi fare clic su **Aggiungi** nel pannello in basso.
-	Seguire le istruzioni e creare una nuova **Applicazione client nativa**.
    -	Il **Nome** dell'applicazione deve essere una descrizione per gli utenti finali.
    -	L'**URI di reindirizzamento** è una combinazione dello schema e della stringa che Azure AD userà per restituire le risposte dei token. Per ora immettere un valore segnaposto, ad esempio `http://DirectorySearcher`. Questo valore verrà sostituito in un secondo momento.
-	Dopo avere completato la registrazione, AAD assegnerà all'app un identificatore client univoco. Poiché questo valore sarà necessario nelle sezioni successive, copiarlo dalla scheda **Configura**.
- Sempre nella scheda **Configura** individuare la sezione "Autorizzazioni per altre applicazioni". Per l'applicazione "Azure Active Directory", aggiungere l'autorizzazione **Accedere alla directory dell'organizzazione** in **Autorizzazioni delegate**. In questo modo l'applicazione potrà cercare gli utenti nell'API Graph.

## *2. Installare e configurare ADAL*
Ora che si dispone di un'applicazione in Azure AD, è possibile installare ADAL e scrivere il codice relativo all'identità. Per consentire ad ADAL di comunicare con Azure AD, è necessario fornirgli alcune informazioni sulla registrazione dell'app. Iniziare aggiungendo ADAL al progetto DirectorySearcher con la Console di Gestione pacchetti.

```
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

-	Nel progetto DirectorySearcher aprire `MainPage.xaml.cs`. Sostituire i valori dell'area `Config Values` in modo che corrispondano ai valori inseriti nel portale di Azure. Il codice farà riferimento a questi valori ogni volta che userà ADAL.
    -	`tenant` è il dominio del tenant di Azure AD, ad esempio, contoso.onmicrosoft.com.
    -	`clientId` è l'ID client dell'applicazione copiato dal portale.
-	Ora è necessario individuare l'URI di callback per l'app di Windows Phone. Impostare un punto di interruzione in questa riga del metodo `MainPage`:

```
redirectURI = Windows.Security.Authentication.Web.WebAuthenticationBroker.GetCurrentApplicationCallbackUri();
```
- Eseguire l'app e prendere nota del valore di `redirectUri` quando viene raggiunto il punto di interruzione. Dovrebbe essere simile a

```
ms-app://s-1-15-2-1352796503-54529114-405753024-3540103335-3203256200-511895534-1429095407/
```

- Sempre nella scheda **Configura** dell'applicazione nel portale di gestione di Azure sostituire il valore di **RedirectUri** con questo valore.  

## *3. Usare ADAL per ottenere i token da AAD*
Il principio alla base di ADAL è che l'app, ogni volta che ha bisogno di un token di accesso, deve solo chiamare `authContext.AcquireToken(…)` e ADAL fa il resto.

-	Il primo passaggio consiste nell'inizializzare l'oggetto `AuthenticationContext` dell'app, ovvero la classe primaria di ADAL, dove si passano ad ADAL le coordinate di cui ha bisogno per comunicare con Azure AD e gli si indica come memorizzare i token nella cache.

```C#
public MainPage()
{
    ...

    // ADAL for Windows Phone 8.1 builds AuthenticationContext instances through a factory
    authContext = AuthenticationContext.CreateAsync(authority).GetResults();
}
```

- Individuare ora il metodo `Search(...)`, che verrà richiamato quando l'utente fa clic sul pulsante "Search" nell'interfaccia utente dell'app. Questo metodo invia una richiesta GET all'API Graph di Azure AD per eseguire una query sugli utenti il cui UPN inizia con il termine di ricerca specificato. Per eseguire una query nell'API Graph, è però necessario includere un oggetto access\_token nell'intestazione `Authorization` della richiesta, dove entra in gioco ADAL.

```C#
private async void Search(object sender, RoutedEventArgs e)
{
    ...

    // Try to get a token without triggering any user prompt.
    // ADAL will check whether the requested token is in ADAL's token cache or can otherwise be obtained without user interaction.
    AuthenticationResult result = await authContext.AcquireTokenSilentAsync(graphResourceId, clientId);
    if (result != null && result.Status == AuthenticationStatus.Success)
    {
        // A token was successfully retrieved.
        QueryGraph(result);
    }
    else
    {
        // Acquiring a token without user interaction was not possible.
        // Trigger an authentication experience and specify that once a token has been obtained the QueryGraph method should be called
        authContext.AcquireTokenAndContinue(graphResourceId, clientId, redirectURI, QueryGraph);
    }
}
```
- Se è necessaria l'autenticazione interattiva, ADAL userà WAB (Web Authentication Broker) e il [modello di continuazione](http://www.cloudidentity.com/blog/2014/06/16/adal-for-windows-phone-8-1-deep-dive/) di Windows Phone per visualizzare la pagina di accesso di Azure AD. Quando l'utente effettua l'accesso, l'app deve passare ad ADAL i risultati dell'interazione WAB. È semplice come implementare l'interfaccia `ContinueWebAuthentication`:

```C#
// This method is automatically invoked when the application
// is reactivated after an authentication interaction through WebAuthenticationBroker.
public async void ContinueWebAuthentication(WebAuthenticationBrokerContinuationEventArgs args)
{
    // pass the authentication interaction results to ADAL, which will
    // conclude the token acquisition operation and invoke the callback specified in AcquireTokenAndContinue.
    await authContext.ContinueAcquireTokenAsync(args);
}
```

- Ora è possibile usare l'oggetto `AuthenticationResult` restituito da ADAL all'app. Nel callback `QueryGraph(...)` associare l'oggetto access\_token acquisito alla richiesta GET nell'intestazione dell'autorizzazione:

```C#
private async void QueryGraph(AuthenticationResult result)
{
    if (result.Status != AuthenticationStatus.Success)
    {
        MessageDialog dialog = new MessageDialog(string.Format("If the error continues, please contact your administrator.\n\nError: {0}\n\nError Description:\n\n{1}", result.Error, result.ErrorDescription), "Sorry, an error occurred while signing you in.");
        await dialog.ShowAsync();
    }

    // Add the access token to the Authorization Header of the call to the Graph API, and call the Graph API.
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

    ...
}
```
- È possibile usare l'oggetto `AuthenticationResult` anche per visualizzare informazioni sull'utente nell'app. Nel metodo `QueryGraph(...)` usare il risultato per visualizzare l'ID dell'utente nella pagina:

```C#
// Update the Page UI to represent the signed in user
ActiveUser.Text = result.UserInfo.DisplayableId;
```
- Infine è possibile usare ADAL anche per disconnettere l'utente dall'applicazione. È opportuno assicurarsi che, quando l'utente fa clic sul pulsante "Sign Out", la chiamata successiva a `AcquireTokenSilentAsync(...)` abbia esito negativo. Con ADAL, basta cancellare la cache dei token:

```C#
private void SignOut()
{
    // Clear session state from the token cache.
    authContext.TokenCache.Clear();

    ...
}
```

Congratulazioni. È stata compilata un'app di Windows Phone in grado di autenticare gli utenti, di chiamare in modo sicuro le API Web usando OAuth 2.0 e di ottenere informazioni di base sull'utente. Se non si è ancora popolato il tenant con alcuni utenti, ora è possibile farlo. Eseguire l'app DirectorySearcher e accedere con uno di tali utenti. Cercare altri utenti in base al relativo UPN. Chiudere l'app e rieseguirla. Si noti che la sessione dell'utente non è stata modificata. Disconnettersi e accedere nuovamente come un altro utente.

ADAL consente di incorporare facilmente nell'applicazione tutte queste funzionalità comuni relative alle identità. Esegue automaticamente le attività più complesse: gestione della cache, supporto del protocollo OAuth, presentazione all'utente di un'interfaccia utente di accesso, aggiornamento dei token scaduti e altro. Tutto ciò che occorre conoscere è una sola chiamata all'API, `authContext.AcquireToken*(…)`.

Come riferimento, viene fornito l'esempio completato (senza i valori di configurazione) [qui](https://github.com/AzureADQuickStarts/NativeClient-WindowsPhone/archive/complete.zip). Ora è possibile passare ad altri scenari relativi alle identità. È possibile:

[Proteggere un'API Web .NET con Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=AcomDC_0128_2016-->