---
title: File di inclusione
description: File di inclusione
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 0b00597deff5a498d54ffcfd9978a68e5b60c5f8
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203223"
---
## <a name="use-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Usare MSAL per ottenere un token per l'API Microsoft Graph

In questa sezione si usa MSAL per ottenere un token per l'API Microsoft Graph.

1. Nel file *MainWindow.xaml.cs* aggiungere alla classe il riferimento relativo a MSAL:

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. Sostituire il codice della classe `MainWindow` con il seguente:

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

            var app = App.PublicClientApp;
            ResultText.Text = string.Empty;
            TokenInfoText.Text = string.Empty;

            var accounts = await app.GetAccountsAsync();

            try
            {
                authResult = await app.AcquireTokenSilentAsync(_scopes, accounts.FirstOrDefault());
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

#### <a name="get-a-user-token-interactively"></a>Ottenere un token utente in modo interattivo

Se si chiama il metodo `AcquireTokenAsync`, viene visualizzata una finestra in cui viene chiesto agli utenti di eseguire l'accesso. Le applicazioni in genere richiedono agli utenti di eseguire l'accesso in modo interattivo quando devono accedere per la prima volta a una risorsa protetta. Potrebbe essere necessario eseguire l'accesso anche quando un'operazione invisibile all'utente per l'acquisizione di un token ha esito negativo, ad esempio a causa della scadenza della password di un utente.

#### <a name="get-a-user-token-silently"></a>Ottenere un token utente in modo automatico

Il metodo `AcquireTokenSilentAsync` gestisce le acquisizioni e i rinnovi dei token senza alcuna interazione da parte dell'utente. Dopo la prima esecuzione di `AcquireTokenAsync`, per le chiamate successive il metodo generalmente usato per ottenere i token per accedere alle risorse protette è `AcquireTokenSilentAsync`, perché le chiamate per richiedere o rinnovare i token vengono effettuate in modo invisibile all'utente.

Il metodo `AcquireTokenSilentAsync` avrà infine esito negativo, ad esempio perché l'utente ha effettuato la disconnessione o ha modificato la propria password su un altro dispositivo. Se MSAL rileva che il problema può essere risolto richiedendo un'azione interattiva, viene attivata un'eccezione `MsalUiRequiredException`. L'applicazione può gestire questa eccezione in due modi:

* Può eseguire immediatamente una chiamata a `AcquireTokenAsync` e richiedere così all'utente di eseguire l'accesso. Questo modello viene usato in genere nelle applicazioni online in cui non è disponibile contenuto offline per l'utente. L'esempio generato in questa configurazione guidata segue questo modello, il cui funzionamento può essere osservato la prima volta che si esegue l'esempio. 

* Dato che nessun utente ha usato l'applicazione, `PublicClientApp.Users.FirstOrDefault()` contiene un valore Null e viene generata un'eccezione `MsalUiRequiredException`. 

* Il codice dell'esempio gestisce quindi l'eccezione chiamando `AcquireTokenAsync` e richiedendo così all'utente di eseguire l'eccesso.

* Può presentare un'indicazione visiva per informare gli utenti che è necessario un accesso interattivo e consentire così di scegliere il momento opportuno per accedere. In alternativa, l'applicazione può riprovare a eseguire `AcquireTokenSilentAsync` in un secondo momento. Questo modello viene in genere adottato quando gli utenti possono usare altre funzionalità dell'applicazione senza interruzioni, ad esempio quando nell'applicazione è disponibile contenuto offline. In questo caso, gli utenti possono decidere se vogliono eseguire l'accesso per accedere alla risorsa protetta oppure aggiornare le informazioni obsolete. In alternativa, l'applicazione può decidere di riprovare a eseguire `AcquireTokenSilentAsync` quando la rete viene ripristinata dopo essere stata temporaneamente non disponibile.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Chiamare l'API Microsoft Graph usando il token appena ottenuto

Aggiungere il nuovo metodo seguente al file `MainWindow.xaml.cs`. Questo metodo consente di eseguire una richiesta `GET` all'API Graph usando un'intestazione Authorize:

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
### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Altre informazioni sull'esecuzione di una chiamata REST a un'API protetta

In questa applicazione di esempio viene usato il metodo `GetHttpContentWithToken` per eseguire una richiesta HTTP `GET` a una risorsa protetta che richiede un token e restituire il contenuto al chiamante. Questo metodo aggiunge il token acquisito nell'intestazione di autorizzazione HTTP. Per questo esempio, la risorsa è l'endpoint *me* dell'API Microsoft Graph, che visualizza le informazioni del profilo dell'utente.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-a-user"></a>Aggiungere un metodo per disconnettere un utente

Per disconnettere un utente, aggiungere il metodo seguente al file `MainWindow.xaml.cs`:

```csharp
/// <summary>
/// Sign out the current user
/// </summary>
private async void SignOutButton_Click(object sender, RoutedEventArgs e)
{
    var accounts = await App.PublicClientApp.GetAccountsAsync(); 

    if (accounts.Any())
    {
        try
        {
            await App.PublicClientApp.RemoveAsync(accounts.FirstOrDefault()); 
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
### <a name="more-information-about-user-sign-out"></a>Altre informazioni sulla disconnessione degli utenti

Il metodo `SignOutButton_Click` rimuove gli utenti dalla cache utente di MSAL. In questo modo, MSAL dimentica l'utente corrente e una futura richiesta di acquisizione di un token avrà esito positivo solo se eseguita in modo interattivo.

Nonostante l'applicazione in questo esempio supporti singoli utenti, MSAL supporta anche scenari con accesso contemporaneo di più account, ad esempio un'applicazione di posta elettronica in cui un utente ha più account.
<!--end-collapse-->

## <a name="display-basic-token-information"></a>Visualizzare informazioni di base sui token

Per visualizzare informazioni di base sui token, aggiungere il metodo seguente al file *MainWindow.xaml.cs*:

```csharp
/// <summary>
/// Display basic information contained in the token
/// </summary>
private void DisplayBasicTokenInfo(AuthenticationResult authResult)
{
    TokenInfoText.Text = "";
    if (authResult != null)
    {
        TokenInfoText.Text += $"Username: {authResult.Account.Username}" + Environment.NewLine;
        TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
        TokenInfoText.Text += $"Access Token: {authResult.AccessToken}" + Environment.NewLine;
    }
}
```

<!--start-collapse-->
### <a name="more-information"></a>Altre informazioni

Oltre al token di accesso che viene usato per chiamare l'API Microsoft Graph, dopo l'accesso dell'utente, MSAL ottiene anche un token ID. Questo token contiene un piccolo subset di informazioni relative agli utenti. Il metodo `DisplayBasicTokenInfo` visualizza le informazioni di base contenute nel token. Ad esempio, visualizza l'ID e il nome visualizzato dell'utente, la data di scadenza del token e la stringa che rappresenta il token di accesso. Premendo più volte il pulsante *Call Microsoft Graph API* (Chiama API Microsoft Graph), è possibile vedere che lo stesso token è stato usato per più richieste successive. Se MSAL decide che è il momento di rinnovare il token, è possibile anche vedere la data di scadenza estesa.
<!--end-collapse-->
