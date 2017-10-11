---
title: Introduzione a Windows Desktop per Azure AD v2 - Uso | Microsoft Docs
description: Come applicazioni .NET per Windows Desktop (XAML) possono chiamare un'API che richiede token di accesso dall'endpoint di Azure Active Directory v2
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
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: ef74361c7a15b0eb7dad1f6ee03f8df707a7c05e
ms.openlocfilehash: 826ba0a00b26993d4f37f0a8ce587d7bb77e7eb4
ms.contentlocale: it-it

---

## <a name="use-the-microsoft-authentication-library-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Usare Microsoft Authentication Library (MSAL) per ottenere un token per l'API Microsoft Graph

Questa sezione illustra come usare MSAL per ottenere un token per l'API Microsoft Graph.

1.  In `MainWindow.xaml.cs` aggiungere alla classe il riferimento della libreria MSAL:

```csharp
using Microsoft.Identity.Client;
```
<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
Sostituire il codice della classe <code>MainWindow</code> con:
</li>
</ol>

```csharp
public partial class MainWindow : Window
{
    //Set the API Endpoint to Graph 'me' endpoint
    string _graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";

    //Set the scope for API call to user.read
    string[] _scopes = new string[] { "user.read" };


    public MainWindow()
    {
        InitializeComponent();
    }

    /// <summary>
    /// Call AcquireTokenAsync - to acquire a token requiring user to sign-in
    /// </summary>
    private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
    {
        AuthenticationResult authResult = null;

        try
        {
            authResult = await App.PublicClientApp.AcquireTokenSilentAsync(_scopes, App.PublicClientApp.Users.FirstOrDefault());
        }
        catch (MsalUiRequiredException ex)
        {
            // A MsalUiRequiredException happened on AcquireTokenSilentAsync. This indicates you need to call AcquireTokenAsync to acquire a token
            System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

            try
            {
                authResult = await App.PublicClientApp.AcquireTokenAsync(_scopes);
            }
            catch (MsalException msalex)
            {
                ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
            }
        }
        catch (Exception ex)
        {
            ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
            return;
        }

        if (authResult != null)
        {
            ResultText.Text = await GetHttpContentWithToken(_graphAPIEndpoint, authResult.AccessToken);
            DisplayBasicTokenInfo(authResult);
            this.SignOutButton.Visibility = Visibility.Visible;
        }
    }
}
```

<!--start-collapse-->
### <a name="more-information"></a>Altre informazioni
#### <a name="getting-a-user-token-interactive"></a>Acquisizione di un token utente in modo interattivo
Se si chiama il metodo `AcquireTokenAsync`, viene visualizzata una finestra in cui si chiede all'utente di eseguire l'accesso. In genere, le applicazioni chiedono agli utenti di accedere in modo interattivo la prima volta che devono accedere a una risorsa protetta o quando un'operazione invisibile di acquisizione di un token ha esito negativo (ad esempio, perché la password dell'utente è scaduta).

#### <a name="getting-a-user-token-silently"></a>Acquisizione di un token utente in modo invisibile
`AcquireTokenSilentAsync` gestisce le acquisizioni e i rinnovi dei token senza alcuna interazione da parte dell'utente. Dopo aver eseguito `AcquireTokenAsync` la prima volta, per le chiamate successive il metodo generalmente usato per ottenere token per accedere a risorse protette è `AcquireTokenSilentAsync`: le chiamate per richiedere o rinnovare token vengono effettuate in modo invisibile per l'utente.
Alla fine, tuttavia, `AcquireTokenSilentAsync` avrà esito negativo perché, ad esempio, l'utente si sarà disconnesso o avrà modificato la password in un altro dispositivo. Se MSAL rileva che il problema può essere risolto richiedendo un'azione interattiva, viene attivata una `MsalUiRequiredException`. L'applicazione può gestire questa eccezione in due modi:

1.  Effettuare subito una chiamata a `AcquireTokenAsync`, in modo da chiedere all'utente di eseguire l'accesso. Questo criterio viene usato in genere nelle applicazioni online in cui non sono disponibili contenuti offline per l'utente. L'esempio generato in questa installazione guidata usa questo criterio, che è possibile vedere in pratica la prima volta che si esegue l'esempio: poiché nessun utente ha mai usato l'applicazione, `PublicClientApp.Users.FirstOrDefault()` conterrà un valore null e verrà generata un'eccezione `MsalUiRequiredException`. Il codice dell'esempio gestirà quindi l'eccezione chiamando `AcquireTokenAsync`, ovvero chiedendo all'utente di eseguire l'eccesso.

2.  Le applicazioni possono anche generare un'indicazione visiva per informare l'utente che è necessario un accesso interattivo, in modo da consentire di scegliere il momento più opportuno per accedere. In alternativa, l'applicazione riproverà a eseguire `AcquireTokenSilentAsync` in un secondo momento. Questo metodo viene usato in genere quando l'utente può accedere ad altre funzionalità dell'applicazione senza essere interrotto, ad esempio quando nell'applicazione sono disponibili contenuti offline. In questo caso, l'utente può decidere quando eseguire l'accesso per accedere alla risorsa protetta o per aggiornare informazioni obsolete. In alternativa, l'applicazione può decidere di riprovare a eseguire `AcquireTokenSilentAsync` se la rete viene ripristinata dopo essere stata temporaneamente non disponibile.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>Chiamare l'API Microsoft Graph usando il token appena ottenuto

1. Aggiungere il nuovo metodo seguente al file `MainWindow.xaml.cs`. Questo metodo consente di eseguire una richiesta `GET` all'API Graph usando un'intestazione di autorizzazione:

```csharp
/// <summary>
/// Perform an HTTP GET request to a URL using an HTTP Authorization header
/// </summary>
/// <param name="url">The URL</param>
/// <param name="token">The token</param>
/// <returns>String containing the results of the GET operation</returns>
public async Task<string> GetHttpContentWithToken(string url, string token)
{
    var httpClient = new System.Net.Http.HttpClient();
    System.Net.Http.HttpResponseMessage response;
    try
    {
        var request = new System.Net.Http.HttpRequestMessage(System.Net.Http.HttpMethod.Get, url);
        //Add the token in Authorization header
        request.Headers.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
        response = await httpClient.SendAsync(request);
        var content = await response.Content.ReadAsStringAsync();
        return content;
    }
    catch (Exception ex)
    {
        return ex.ToString();
    }
}
```
<!--start-collapse-->
### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Altre informazioni sull'esecuzione di una chiamata REST a un'API protetta

In questa applicazione di esempio, viene usato il metodo `GetHttpContentWithToken` per eseguire una richiesta HTTP `GET` a una risorsa protetta che richiede un token e restituisce il contenuto al chiamante. Questo metodo aggiunge il token acquisito nell'*intestazione di autorizzazione HTTP*. Per questo esempio, la risorsa è l'endpoint *me* dell'API Microsoft Graph, che consente di visualizzare informazioni sul profilo dell'utente.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Aggiungere un metodo per disconnettere l'utente

1. Aggiungere il metodo seguente al file `MainWindow.xaml.cs` per disconnettere l'utente:

```csharp
/// <summary>
/// Sign out the current user
/// </summary>
private void SignOutButton_Click(object sender, RoutedEventArgs e)
{
    if (App.PublicClientApp.Users.Any())
    {
        try
        {
            App.PublicClientApp.Remove(App.PublicClientApp.Users.FirstOrDefault());
            this.ResultText.Text = "User has signed-out";
            this.CallGraphButton.Visibility = Visibility.Visible;
            this.SignOutButton.Visibility = Visibility.Collapsed;
        }
        catch (MsalException ex)
        {
            ResultText.Text = $"Error signing-out user: {ex.Message}";
        }
    }
}
```
<!--start-collapse-->
### <a name="more-info-on-sign-out"></a>Altre informazioni sulla disconnessione

L'oggetto `SignOutButton_Click` rimuove l'utente dalla cache utente di MSAL: in questo modo, MSAL dimenticherà l'utente corrente e un'eventuale richiesta futura di acquisizione di un token riuscirà solo effettuata in modo interattivo.
Anche se l'applicazione in questo esempio supporta un unico utente, MSAL supporta anche scenari in cui è possibile eseguire contemporaneamente l'accesso di più account, come nel caso di un'applicazione di posta elettronica in cui un utente dispone di più account.
<!--end-collapse-->

## <a name="display-basic-token-information"></a>Visualizzare informazioni di base sul token

1. Aggiungere il metodo seguente al file `MainWindow.xaml.cs` per visualizzare informazioni di base sul token:

```csharp
/// <summary>
/// Display basic information contained in the token
/// </summary>
private void DisplayBasicTokenInfo(AuthenticationResult authResult)
{
    TokenInfoText.Text = "";
    if (authResult != null)
    {
        TokenInfoText.Text += $"Name: {authResult.User.Name}" + Environment.NewLine;
        TokenInfoText.Text += $"Username: {authResult.User.DisplayableId}" + Environment.NewLine;
        TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
        TokenInfoText.Text += $"Access Token: {authResult.AccessToken}" + Environment.NewLine;
    }
}
```
<!--start-collapse-->
### <a name="more-information"></a>Altre informazioni

I token acquisiti tramite *OpenID Connect* contengono anche un piccolo subset di informazioni relative all'utente. `DisplayBasicTokenInfo` consente di visualizzare le informazioni di base contenute nel token, quali l'ID e il nome visualizzato dell'utente, la data di scadenza del token e la stringa che rappresenta il token di accesso. Queste informazioni possono essere visualizzate dall'utente. Premendo più volte il pulsante *Call Microsoft Graph API* (Chiama API Microsoft Graph), è possibile vedere che lo stesso token è stato usato per richieste successive. Se MSAL decide che è il momento di rinnovare il token, è possibile anche vedere la data di scadenza estesa.
<!--end-collapse-->


