
## <a name="use-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Utilizzare MSAL per ottenere un token per l'API di Microsoft Graph

In questa sezione, utilizzare MSAL per ottenere un token per l'API di Microsoft Graph.

1.  Nel *MainWindow.xaml.cs* file, aggiungere il riferimento per MSAL alla classe:

    ```csharp
    using Microsoft.Identity.Client;
    ```
<!-- Workaround for Docs conversion bug -->

2. Sostituire il `MainWindow` classe codice con quanto segue:

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
#### <a name="get-a-user-token-interactively"></a>Ottenere token un utente in modo interattivo
La chiamata di `AcquireTokenAsync` i metodi restituiscono in una finestra che richiede agli utenti di accedere. In genere, le applicazioni richiedono agli utenti di accedere in modo interattivo la prima volta, che è necessario che per una risorsa protetta. Sono inoltre potrebbe essere necessario eseguire l'accesso quando un'operazione invisibile all'utente di acquisire un token ha esito negativo (ad esempio, quando scade la password dell'utente).

#### <a name="get-a-user-token-silently"></a>Ottenere token un utente senza avvisare
Il `AcquireTokenSilentAsync` metodo gestisce le acquisizioni di token e rinnovo senza alcuna interazione dell'utente. Dopo aver `AcquireTokenAsync` viene eseguita per la prima volta, `AcquireTokenSilentAsync` è il metodo normale da utilizzare per ottenere i token di accesso alle risorse protette per le chiamate successive, in quanto le chiamate a richiedere o rinnovare token vengono eseguite automaticamente.

Infine, il `AcquireTokenSilentAsync` metodo avrà esito negativo. Le cause di errore potrebbero essere che l'utente è disconnesso o modificare la password in un altro dispositivo. Se MSAL rileva che il problema può essere risolto richiedendo un'azione interattiva, viene attivata un'eccezione `MsalUiRequiredException`. L'applicazione può gestire questa eccezione in due modi:

* È possibile effettuare una chiamata su `AcquireTokenAsync` immediatamente. Questa chiamata comporta chiedere conferma all'utente di accedere. Questo modello è in genere utilizzato nelle applicazioni in linea in cui è presente alcun contenuto non in linea disponibile per l'utente. Il codice di esempio generato da questa installazione guidata segue questo modello, che è possibile visualizzare in tempo azione prima che eseguire l'esempio. 
    * Poiché nessun utente ha usato l'applicazione, `PublicClientApp.Users.FirstOrDefault()` contiene un valore null e un `MsalUiRequiredException` viene generata un'eccezione. 
    * Il codice nell'esempio viene quindi gestita l'eccezione chiamando `AcquireTokenAsync`, dando luogo a chiedere conferma all'utente di accedere.

* Invece può presentare un'indicazione visiva agli utenti un accesso aggiuntivo interattivo è necessario, in modo che possono selezionare giunto il momento di eseguire l'accesso. O l'applicazione può ripetere `AcquireTokenSilentAsync` in un secondo momento. Questo modello viene spesso utilizzato quando gli utenti possono usare altre funzionalità delle applicazioni senza interruzioni, ad esempio, quando il contenuto non in linea è disponibile nell'applicazione. In questo caso, gli utenti possono decidere quando desiderano accedere per accedere alla risorsa protetta o aggiornare le informazioni obsolete. In alternativa, l'applicazione può decidere di ripetere `AcquireTokenSilentAsync` quando la rete viene ripristinata dopo essere state temporaneamente non disponibile.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Chiamare l'API Graph Microsoft utilizzando il token che è stata ottenuta

Aggiungere il seguente nuovo metodo per il `MainWindow.xaml.cs`. Il metodo viene utilizzato per rendere un `GET` richiesta sull'API Graph con un'intestazione di autorizzazione:

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

In questa applicazione di esempio, utilizzare il `GetHttpContentWithToken` metodo HTTP per `GET` richiesta su una risorsa protetta che richiede un token e quindi restituire il contenuto al chiamante. Questo metodo aggiunge il token acquisito nell'intestazione di autorizzazione HTTP. Per questo esempio, la risorsa è l'API di Microsoft Graph *me* endpoint, che visualizza informazioni sul profilo dell'utente.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-a-user"></a>Aggiungere un metodo per disconnettersi da un utente

Per disconnettersi da un utente, aggiungere il metodo seguente per il `MainWindow.xaml.cs` file:

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
### <a name="more-information-about-user-sign-out"></a>Ulteriori informazioni sulla disconnessione utente

Il `SignOutButton_Click` metodo rimuove gli utenti dalla cache utente MSAL, che indica in modo efficace MSAL dimenticare l'utente corrente in modo che una richiesta futura per acquisire un token avrà esito positivo solo se viene resa a essere interattive.

Anche se l'applicazione in questo esempio supporta utenti singoli, MSAL supporta scenari in cui può essere firmato in più account nello stesso momento. Un esempio è un'applicazione di posta elettronica in cui un utente dispone di più account.
<!--end-collapse-->

## <a name="display-basic-token-information"></a>Visualizzare informazioni sul token di base

Per visualizzare le informazioni di base del token, aggiungere il metodo seguente al *MainWindow.xaml.cs* file:

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

Oltre al token di accesso utilizzato per chiamare l'API di Microsoft Graph, dopo l'utente esegue l'accesso in, MSAL ottiene anche un ID token. Questo token contiene un piccolo subset delle informazioni relative agli utenti. Il `DisplayBasicTokenInfo` metodo visualizza le informazioni di base contenuta nel token. Ad esempio, Visualizza nome visualizzato dell'utente e ID, nonché la data di scadenza del token e la stringa che rappresenta il token di accesso stesso. È possibile selezionare il *chiamare API di Microsoft Graph* pulsante più volte e vedere che è stato riutilizzato lo stesso token per le richieste successive. Se MSAL decide che è il momento di rinnovare il token, è possibile anche vedere la data di scadenza estesa.
<!--end-collapse-->

