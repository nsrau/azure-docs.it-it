---
title: Introduzione all'uso di Microsoft Identity Platform con un'app Windows Desktop | Microsoft Docs
description: Informazioni su come un'applicazione .NET per Windows Desktop (XAML) può ottenere un token di accesso e chiamare un'API protetta da Microsoft Identity Platform.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f816091e3e8682069a950ff6f6eb839e285bb2f
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69512426"
---
# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Chiamare l'API Microsoft Graph da un'app Windows Desktop

Questa guida illustra come un'applicazione .NET per Windows Desktop (XAML) nativa usa un token di accesso per chiamare l'API Microsoft Graph. L'app può accedere anche ad altre API che richiedono token di accesso da un endpoint di Microsoft Identity Platform per sviluppatori v2.0, la piattaforma in precedenza denominata Azure AD.

Dopo aver completato la Guida, l'applicazione sarà in grado di chiamare un'API protetta che usa account personali (inclusi outlook.com, live.com e altri). L'applicazione userà anche account di lavoro e di formazione da qualsiasi società o organizzazione che usa Azure Active Directory.  

> [!NOTE]
> La guida richiede Visual Studio 2015 Update 3, Visual Studio 2017 o Visual Studio 2019. Non si dispone di nessuna di queste versioni? [Scaricare gratuitamente Visual Studio 2019](https://www.visualstudio.com/downloads/).

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Funzionamento dell'app di esempio generata da questa guida

![Illustra come funziona l'app di esempio generata da questa esercitazione](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.svg)

L'applicazione di esempio creata in questa guida consente a un'applicazione per Windows Desktop di eseguire query sull'API Microsoft Graph o su un'API Web che accetta token da un endpoint di Microsoft Identity Platform. Per questo scenario, viene aggiunto un token a richieste HTTP tramite l'intestazione di autorizzazione. L'acquisizione e il rinnovo del token vengono gestiti da Microsoft Authentication Library (MSAL).

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Gestione dell'acquisizione di token per l'accesso ad API Web protette

Dopo l'autenticazione dell'utente, l'applicazione di esempio riceve un token che può essere usato per eseguire query sull'API Microsoft Graph o su un'API Web protetta da Microsoft Identity Platform per sviluppatori.

Le API come Microsoft Graph richiedono un token per consentire l'accesso a specifiche risorse. È necessario un token, ad esempio, per leggere il profilo di un utente, accedere al calendario di un utente o inviare posta elettronica. L'applicazione può richiedere un token di accesso usando MSAL per accedere alle risorse tramite la specifica degli ambiti dell'API. Questo token di accesso viene quindi aggiunto all'intestazione di autorizzazione HTTP per ogni chiamata effettuata per la risorsa protetta.

La memorizzazione nella cache e l'aggiornamento dei token di accesso vengono gestiti da MSAL e non devono quindi essere eseguiti dall'applicazione.

## <a name="nuget-packages"></a>Pacchetti NuGet

Questa guida usa i pacchetti NuGet seguenti:

|Libreria|DESCRIZIONE|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft Authentication Library (MSAL.NET)|

## <a name="set-up-your-project"></a>Configurare il progetto

In questa sezione verrà creato un nuovo progetto per illustrare come integrare un'applicazione .NET per Windows Desktop (XAML) con *Accedi con Microsoft* in modo da poter eseguire query su API Web che richiedono un token.

L'applicazione creata in questa Guida consente di visualizzare un pulsante usato per chiamare un grafo, un'area per visualizzare i risultati sullo schermo e un pulsante di disconnessione.

> [!NOTE]
> Se invece si preferisce scaricare questo progetto Visual Studio di esempio, [scaricare un progetto](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/msal3x.zip) e andare direttamente al [passaggio della configurazione](#register-your-application) per configurare il codice di esempio prima di eseguirlo.
>

Per creare l'applicazione, eseguire le operazioni seguenti:

1. In Visual Studio selezionare **File** > **Nuovo** > **Progetto**.
2. In **Modelli** selezionare **Visual C#** .
3. Selezionare **App WPF (.NET Framework)** , a seconda della versione di Visual Studio in uso.

## <a name="add-msal-to-your-project"></a>Aggiungere MSAL al progetto

1. In Visual Studio selezionare **Strumenti** > **Gestione pacchetti NuGet**> **Console di Gestione pacchetti**.
2. Nella finestra Console di Gestione pacchetti incollare il seguente comando di Azure PowerShell:

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

    > [!NOTE] 
    > Questo comando installa Microsoft Authentication Library. MSAL gestisce l'acquisizione, la memorizzazione nella cache e l'aggiornamento dei token utente usati per accedere alle API protette da Azure Active Directory 2.0
    >

## <a name="register-your-application"></a>Registrare l'applicazione

È possibile registrare l'applicazione in uno dei due modi descritti di seguito.

### <a name="option-1-express-mode"></a>Opzione 1: modalità Rapida

È possibile registrare rapidamente l'applicazione seguendo questa procedura:
1. Passare alla [registrazione delle applicazioni nel portale di Azure](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs).
1. Immettere un nome per l'applicazione e fare clic su **Registra**.
1. Seguire le istruzioni per scaricare e configurare automaticamente la nuova applicazione con un clic.

### <a name="option-2-advanced-mode"></a>Opzione 2: Modalità Avanzata

Per registrare l'applicazione e aggiungere le relative informazioni di registrazione alla soluzione, seguire questa procedura:
1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Se l'account consente di accedere a più tenant, selezionare l'account nell'angolo in alto a destra e impostare la sessione del portale sul tenant di Azure Active Directory desiderato.
1. Passare alla pagina [Registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) di Microsoft Identity Platform per sviluppatori.
1. Selezionare **Nuova registrazione**.
   - Nella sezione **Nome** immettere un nome di applicazione significativo che verrà visualizzato agli utenti dell'app, ad esempio `Win-App-calling-MsGraph`.
   - Nella sezione **Tipi di account supportati** selezionare **Account in qualsiasi directory organizzativa e account Microsoft personali (ad esempio Skype, Xbox, Outlook.com)** .
   - Selezionare **Registra** per creare l'applicazione.
1. Nell'elenco delle pagine per l'app selezionare **Autenticazione**.
   1. Nella sezione **URI di reindirizzamento**, nell'elenco URI di reindirizzamento:
   1. Nella colonna **TIPO** selezionare **Client pubblico (per dispositivi mobili e desktop)** .
   1. Immettere `urn:ietf:wg:oauth:2.0:oob` nella colonna **URI DI REINDIRIZZAMENTO**
1. Selezionare **Salva**.
1. Passare a Visual Studio, aprire il file *App.xaml.cs* e quindi sostituire `Enter_the_Application_Id_here` nel frammento di codice seguente con l'ID applicazione appena registrato e copiato.

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    ```

## <a name="add-the-code-to-initialize-msal"></a>Aggiungere il codice per inizializzare MSAL

Questo passaggio consente di creare una classe per gestire l'interazione con la libreria MSAL, ad esempio per la gestione dei token.

1. Aprire il file *App.xaml.cs*, quindi aggiungere alla classe il riferimento relativo alla libreria MSAL:

    ```csharp
    using Microsoft.Identity.Client;
    ```
   <!-- Workaround for Docs conversion bug -->

2. Aggiornare la classe App con il codice seguente:

    ```csharp
    public partial class App : Application
    {
        static App()
        {
            _clientApp = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(AzureCloudInstance.AzurePublic, Tenant)
                .Build();
        }

        // Below are the clientId (Application Id) of your app registration and the tenant information. 
        // You have to replace:
        // - the content of ClientID with the Application Id for your app registration
        // - the content of Tenant by the information about the accounts allowed to sign-in in your application:
        //   - For Work or School account in your org, use your tenant ID, or domain
        //   - for any Work or School accounts, use `organizations`
        //   - for any Work or School accounts, or Microsoft personal account, use `common`
        //   - for Microsoft Personal account, use consumers
        private static string ClientId = "0b8b0665-bc13-4fdc-bd72-e0227b9fc011";

        private static string Tenant = "common";

        private static IPublicClientApplication _clientApp ;

        public static IPublicClientApplication PublicClientApp { get { return _clientApp; } }
    }
    ```

## <a name="create-the-application-ui"></a>Creare l'interfaccia utente dell'applicazione

Questa sezione illustra come un'applicazione può eseguire una query su un server back-end protetto come Microsoft Graph. 

Nell'ambito del modello di progetto viene automaticamente creato un file *MainWindow.xaml*. Aprire il file e sostituire il nodo dell'applicazione *\<Griglia>* con il codice seguente:

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <Label Content="API Call Results" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <Label Content="Token Info" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```

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
        string graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";

        //Set the scope for API call to user.read
        string[] scopes = new string[] { "user.read" };


        public MainWindow()
        {
            InitializeComponent();
        }

      /// <summary>
        /// Call AcquireToken - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
            AuthenticationResult authResult = null;
            var app = App.PublicClientApp;
            ResultText.Text = string.Empty;
            TokenInfoText.Text = string.Empty;

            var accounts = await app.GetAccountsAsync();
            var firstAccount = accounts.FirstOrDefault();

            try
            {
                authResult = await app.AcquireTokenSilent(scopes, firstAccount)
                    .ExecuteAsync();
            }
            catch (MsalUiRequiredException ex)
            {
                // A MsalUiRequiredException happened on AcquireTokenSilent.
                // This indicates you need to call AcquireTokenInteractive to acquire a token
                System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

                try
                {
                    authResult = await app.AcquireTokenInteractive(scopes)
                        .WithAccount(accounts.FirstOrDefault())
                        .WithPrompt(Prompt.SelectAccount)
                        .ExecuteAsync();
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
                ResultText.Text = await GetHttpContentWithToken(graphAPIEndpoint, authResult.AccessToken);
                DisplayBasicTokenInfo(authResult);
                this.SignOutButton.Visibility = Visibility.Visible;
            }
        }
    ```

<!--start-collapse-->
### <a name="more-information"></a>Altre informazioni

#### <a name="get-a-user-token-interactively"></a>Ottenere un token utente in modo interattivo

Se si chiama il metodo `AcquireTokenInteractive`, viene visualizzata una finestra in cui viene chiesto agli utenti di eseguire l'accesso. Le applicazioni in genere richiedono agli utenti di eseguire l'accesso in modo interattivo quando devono accedere per la prima volta a una risorsa protetta. Potrebbe essere necessario eseguire l'accesso anche quando un'operazione invisibile all'utente per l'acquisizione di un token ha esito negativo, ad esempio a causa della scadenza della password di un utente.

#### <a name="get-a-user-token-silently"></a>Ottenere un token utente in modo automatico

Il metodo `AcquireTokenSilent` gestisce le acquisizioni e i rinnovi dei token senza alcuna interazione da parte dell'utente. Dopo la prima esecuzione di `AcquireTokenInteractive`, per le chiamate successive il metodo generalmente usato per ottenere i token per accedere alle risorse protette è `AcquireTokenSilent`, perché le chiamate per richiedere o rinnovare i token vengono effettuate in modo invisibile all'utente.

Il metodo `AcquireTokenSilent` avrà infine esito negativo, ad esempio perché l'utente ha effettuato la disconnessione o ha modificato la propria password su un altro dispositivo. Se MSAL rileva che il problema può essere risolto richiedendo un'azione interattiva, viene attivata un'eccezione `MsalUiRequiredException`. L'applicazione può gestire questa eccezione in due modi:

* Può eseguire immediatamente una chiamata a `AcquireTokenInteractive` e richiedere così all'utente di eseguire l'accesso. Questo modello viene usato in genere nelle applicazioni online in cui non è disponibile contenuto offline per l'utente. L'esempio generato in questa configurazione guidata segue questo modello, il cui funzionamento può essere osservato la prima volta che si esegue l'esempio. 

* Dato che nessun utente ha usato l'applicazione, `PublicClientApp.Users.FirstOrDefault()` contiene un valore Null e viene generata un'eccezione `MsalUiRequiredException`. 

* Il codice dell'esempio gestisce quindi l'eccezione chiamando `AcquireTokenInteractive` e richiedendo così all'utente di eseguire l'eccesso.

* Può presentare un'indicazione visiva per informare gli utenti che è necessario un accesso interattivo e consentire così di scegliere il momento opportuno per accedere. In alternativa, l'applicazione può riprovare a eseguire `AcquireTokenSilent` in un secondo momento. Questo modello viene in genere adottato quando gli utenti possono usare altre funzionalità dell'applicazione senza interruzioni, ad esempio quando nell'applicazione è disponibile contenuto offline. In questo caso, gli utenti possono decidere se vogliono eseguire l'accesso per accedere alla risorsa protetta oppure aggiornare le informazioni obsolete. In alternativa, l'applicazione può decidere di riprovare a eseguire `AcquireTokenSilent` quando la rete viene ripristinata dopo essere stata temporaneamente non disponibile.
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
    }
}
```

<!--start-collapse-->
### <a name="more-information"></a>Altre informazioni

Oltre al token di accesso che viene usato per chiamare l'API Microsoft Graph, dopo l'accesso dell'utente, MSAL ottiene anche un token ID. Questo token contiene un piccolo subset di informazioni relative agli utenti. Il metodo `DisplayBasicTokenInfo` visualizza le informazioni di base contenute nel token. Ad esempio, visualizza l'ID e il nome visualizzato dell'utente, la data di scadenza del token e la stringa che rappresenta il token di accesso. Premendo più volte il pulsante *Call Microsoft Graph API* (Chiama API Microsoft Graph), è possibile vedere che lo stesso token è stato usato per più richieste successive. Se MSAL decide che è il momento di rinnovare il token, è possibile anche vedere la data di scadenza estesa.
<!--end-collapse-->

[!INCLUDE [5. Test and Validate](../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]

Contribuire al miglioramento di Microsoft Identity Platform. Completare un breve sondaggio di due domande per condividere la propria opinione.

> [!div class="nextstepaction"]
> [Sondaggio su Microsoft Identity Platform](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
