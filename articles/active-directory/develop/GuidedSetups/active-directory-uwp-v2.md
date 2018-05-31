---
title: Introduzione a UWP per Azure AD v2 | Microsoft Docs
description: Informazioni su come le applicazioni della piattaforma UWP (Universal Windows Platform) (XAML) possono chiamare API che richiedono token di accesso dall'endpoint di Azure Active Directory v2
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/20/2018
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 390559922b3b8fb293d1c8b38f36dfd0a1df9ebd
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
ms.locfileid: "33763374"
---
# <a name="call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>Chiamare l'API Microsoft Graph da un'applicazione della piattaforma UWP (Universal Windows Platform)

Questa guida illustra come un'applicazione della piattaforma UWP (Universal Windows Platform) (XAML) nativa può ottenere un token di accesso e usarlo per chiamare l'API Microsoft Graph o altre API che richiedono token di accesso dall'endpoint di Azure Active Directory v2.

Al termine di questa guida, l'applicazione sarà in grado di chiamare un'API protetta usando sia account personali (ad esempio, outlook.com, live.com e altri) sia account aziendali o di istituti di istruzione di proprietà di aziende o organizzazioni con Azure Active Directory.  

> Per seguire le indicazioni di questa guida è necessario che sia installato Visual Studio 2017 con la piattaforma di sviluppo UWP. Vedere questo [articolo](https://docs.microsoft.com/windows/uwp/get-started/get-set-up "Configurare Visual Studio per la piattaforma UWP") per istruzioni su come scaricare e configurare Visual Studio per sviluppare app UWP.

### <a name="how-this-guide-works"></a>Come interpretare questa guida

![Come interpretare questa guida](media/active-directory-mobileanddesktopapp-windowsuniversalplatform-introduction/uwp-intro.png)

L'applicazione di esempio creata in questa guida consente a un'app UWP di eseguire query nell'API Microsoft Graph o in un'API Web che accetta token dall'endpoint di Azure Active Directory v2. Per questo scenario, viene aggiunto un token a richieste HTTP tramite l'intestazione di autorizzazione. Le acquisizioni e i rinnovi dei token vengono gestiti da Microsoft Authentication Library (MSAL).

### <a name="nuget-packages"></a>Pacchetti NuGet

Questa guida usa i pacchetti NuGet seguenti:

|Libreria|DESCRIZIONE|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft Authentication Library (MSAL)|


## <a name="set-up-your-project"></a>Configurare il progetto

Questa sezione include istruzioni dettagliate su come integrare un'applicazione .NET per Windows Desktop (XAML) con *Accedi a Microsoft* in modo da poter eseguire query su API Web che richiedono un token, come l'API Microsoft Graph.

L'applicazione creata in questa guida consente di visualizzare un pulsante usato per eseguire query sull'API Graph, un pulsante di disconnessione e caselle di testo con i risultati delle chiamate.

> Se invece si preferisce scaricare questo progetto Visual Studio di esempio, è possibile [scaricare il progetto](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/master.zip) e proseguire con il passaggio di [registrazione dell'applicazione](#register-your-application "passaggio di registrazione dell'applicazione") per configurare il codice di esempio prima di eseguirlo.


### <a name="create-your-application"></a>Creare l'applicazione
1. In Visual Studio: **File** > **Nuovo** > **Progetto**<br/>
2. In *Modelli* selezionare **Visual C#**
3. Selezionare **App vuota (Windows universale)**.
4. Assegnare un nome all'app e fare clic su OK.
5. Se richiesto, è possibile selezionare qualsiasi versione per i campi *Versione di destinazione* e *Versione minima* e fare clic su OK:<br/><br/>![Versione minima e versione di destinazione](media/active-directory-uwp-v2.md/vs-minimum-target.png)

## <a name="add-the-microsoft-authentication-library-msal-to-your-project"></a>Aggiungere Microsoft Authentication Library (MSAL) al progetto
1. In Visual Studio: **Strumenti** > **Gestione pacchetti NuGet** > **Console di Gestione pacchetti**
2. Nella finestra Console di Gestione pacchetti copiare e incollare il comando seguente:

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

> [!NOTE]
> Il pacchetto precedente consente di installare [Microsoft Authentication Library (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet), che gestisce l'acquisizione, la memorizzazione nella cache e l'aggiornamento dei token utente usati per accedere ad API protette da Azure Active Directory v2.

## <a name="initialize-msal"></a>Inizializzare MSAL
Questo passaggio consente di creare una classe per gestire l'interazione con la libreria MSAL, ad esempio la gestione dei token.

1. Aprire il file **App.xaml.cs** e aggiungere alla classe il riferimento relativo alla libreria MSAL:

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. Aggiungere le due righe seguenti alla classe dell'app, all'interno del blocco <code>sealed partial class App : Application</code>:

    ```csharp
    // Below is the clientId of your app registration. 
    // You have to replace the below with the Application Id for your app registration
    private static string ClientId = "your_client_id_here";
    
    public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);
    ```

## <a name="create-your-applications-ui"></a>Creare l'interfaccia utente dell'applicazione

Nell'ambito del modello di progetto viene automaticamente creato un file **MainPage.xaml**. Aprire il file e seguire le istruzioni:

1.  Sostituire il nodo **<Grid>** dell'applicazione con:

    ```xml
    <Grid>
        <StackPanel Background="Azure">
            <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
                <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
                <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
            </StackPanel>
            <TextBlock Text="API Call Results" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
            <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
            <TextBlock Text="Token Info" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
            <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
        </StackPanel>
    </Grid>
    ```
    
## <a name="use-the-microsoft-authentication-library-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Usare Microsoft Authentication Library (MSAL) per ottenere un token per l'API Microsoft Graph

Questa sezione illustra come usare MSAL per ottenere un token per l'API Microsoft Graph.

1.  Nel file **MainPage.xaml.cs** aggiungere alla classe il riferimento relativo alla libreria MSAL:

    ```csharp
    using Microsoft.Identity.Client;
    ```
2. Sostituire il codice della classe <code>MainPage</code> con:

    ```csharp
    public sealed partial class MainPage : Page
    {
        // Set the API Endpoint to Graph 'me' endpoint
        string graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";
    
        // Set the scope for API call to user.read
        string[] scopes = new string[] { "user.read" };
    
        public MainPage()
        {
            this.InitializeComponent();
        }
    
        /// <summary>
        /// Call AcquireTokenAsync - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
            AuthenticationResult authResult = null;
            ResultText.Text = string.Empty;
            TokenInfoText.Text = string.Empty;
    
            try
            {
                authResult = await App.PublicClientApp.AcquireTokenSilentAsync(scopes, App.PublicClientApp.Users.FirstOrDefault());
            }
            catch (MsalUiRequiredException ex)
            {
                // A MsalUiRequiredException happened on AcquireTokenSilentAsync. This indicates you need to call AcquireTokenAsync to acquire a token
                System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");
    
                try
                {
                    authResult = await App.PublicClientApp.AcquireTokenAsync(scopes);
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
    }
    ```

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

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>Chiamare l'API Microsoft Graph usando il token appena ottenuto

1. Aggiungere il nuovo metodo seguente al file **MainPage.xaml.cs**. Questo metodo consente di eseguire una richiesta `GET` all'API Graph usando un'intestazione di autorizzazione:

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
            // Add the token in Authorization header
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

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Altre informazioni sull'esecuzione di una chiamata REST a un'API protetta

In questa applicazione di esempio, viene usato il metodo `GetHttpContentWithToken` per eseguire una richiesta HTTP `GET` a una risorsa protetta che richiede un token e restituisce il contenuto al chiamante. Questo metodo aggiunge il token acquisito nell'*intestazione di autorizzazione HTTP*. Per questo esempio, la risorsa è l'endpoint *me* dell'API Microsoft Graph, che consente di visualizzare informazioni sul profilo dell'utente.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Aggiungere un metodo per disconnettere l'utente

1. Per disconnettere l'utente, aggiungere il metodo seguente al file **MainPage.xaml.cs**:

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

### <a name="more-info-on-sign-out"></a>Altre informazioni sulla disconnessione

Il metodo `SignOutButton_Click` consente di rimuovere l'utente dalla cache utente di MSAL. In questo modo, MSAL dimenticherà l'utente corrente e un'eventuale richiesta futura di acquisizione di un token riuscirà solo se eseguita in modo interattivo.
Anche se l'applicazione in questo esempio supporta un unico utente, MSAL supporta anche scenari in cui è possibile eseguire contemporaneamente l'accesso di più account, come nel caso di un'applicazione di posta elettronica in cui un utente dispone di più account.

## <a name="display-basic-token-information"></a>Visualizzare informazioni di base sul token

1. Aggiungere il metodo seguente al file **MainPage.xaml.cs** per visualizzare informazioni di base sul token:

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

### <a name="more-information"></a>Altre informazioni

I token ID acquisiti tramite *OpenID Connect* includono anche un piccolo subset di informazioni relative all'utente. `DisplayBasicTokenInfo` consente di visualizzare le informazioni di base contenute nel token, quali l'ID e il nome visualizzato dell'utente, la data di scadenza del token e la stringa che rappresenta il token di accesso. Queste informazioni possono essere visualizzate dall'utente. Premendo più volte il pulsante **Call Microsoft Graph API** (Chiama API Microsoft Graph), è possibile vedere che lo stesso token è stato usato per richieste successive. Se MSAL decide che è il momento di rinnovare il token, è possibile anche vedere la data di scadenza estesa.

## <a name="register-your-application"></a>Registrare l'applicazione

È ora necessario registrare l'applicazione nel *portale di registrazione delle applicazioni Microsoft*:
1. Passare al [portale di registrazione delle applicazioni Microsoft](https://apps.dev.microsoft.com/portal/register-app) per registrare un'applicazione
2. Immettere un nome per l'applicazione 
3. Assicurarsi che l'opzione per l'installazione guidata sia deselezionata
4. Fare clic su **Add Platforms** (Aggiungi piattaforme), selezionare **Native Application** (Applicazione nativa) e quindi fare clic su Save (Salva)
5. Copiare il GUID nell'ID applicazione, tornare a Visual Studio, aprire **App.xaml.cs** e sostituire `your_client_id_here` con l'ID applicazione appena registrato:

    ```csharp
    private static string ClientId = "your_application_id_here";
    ```

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Abilitare l'autenticazione integrata in domini federati (facoltativo)

Per abilitare l'autenticazione integrata di Windows per l'uso con un dominio federato di Azure Active Directory, il manifesto dell'applicazione deve abilitare funzionalità aggiuntive:

1. Fare doppio clic su **Package.appxmanifest**
2. Selezionare la scheda **Funzionalità** e assicurarsi che le impostazioni seguenti siano abilitate:

    - Autenticazione aziendale
    - Reti private (client e server)
    - Certificati utente condivisi 

3. Aprire quindi **App.xaml.cs** e aggiungere la riga seguente nel costruttore dell'app:

    ```csharp
    App.PublicClientApp.UseCorporateNetwork = true;
    ```

> [!IMPORTANT]
> L'autenticazione integrata di Windows non è configurata per impostazione predefinita per questo esempio, poiché per le applicazioni che richiedono le funzionalità *Autenticazione aziendale* o *Certificati utente condivisi* è necessario un livello più elevato di verifica da Windows Store che non tutti gli sviluppatori vogliono eseguire. Abilitare questa impostazione solo se è necessario usare l'autenticazione integrata di Windows con un dominio federato di Azure Active Directory.


## <a name="test-your-code"></a>Testare il codice

Per testare l'applicazione, premere `F5` per eseguire il progetto in Visual Studio. Verrà visualizzata la finestra principale:

![Interfaccia utente dell'applicazione](media/active-directory-uwp-v2.md/testapp-ui.png)

Quando si è pronti per eseguire il test, fare clic su *Call Microsoft Graph API* (Chiama API Microsoft Graph) e usare un account Microsoft Azure Active Directory (account aziendale) o un account Microsoft (live.com, outlook.com) per accedere. Se è la prima volta, verrà visualizzata una finestra in cui si chiede all'utente di eseguire l'accesso:

![Pagina di accesso](media/active-directory-uwp-v2.md/sign-in-page.png)

### <a name="consent"></a>Consenso
La prima volta che si accede all'applicazione viene visualizzata una schermata di consenso, simile alla seguente, in cui è necessario accettare in modo esplicito:

![Schermata di consenso](media/active-directory-uwp-v2.md/consentscreen.png)
### <a name="expected-results"></a>Risultati previsti
Nella schermata API Call Results (Risultati della chiamata API) vengono visualizzate le informazioni sul profilo utente restituite dalla chiamata all'API Microsoft Graph:

![Schermata dei risultati](media/active-directory-uwp-v2.md/uwp-results-screen.PNG)

Nella casella Token Info (Informazioni sul token) vengono visualizzate anche informazioni di base sul token acquisite tramite `AcquireTokenAsync` o `AcquireTokenSilentAsync`:

|Proprietà  |Formato  |Descrizione |
|---------|---------|---------|
|**Nome** |Nome e cognome dell'utente |Nome e cognome dell'utente.|
|**Nome utente** |<span>user@domain.com</span> |Nome utente usato per identificare l'utente.|
|**Token Expires** (Scadenza token) |Datetime |Ora in cui scadrà il token. MSAL estende la data di scadenza rinnovando il token in base alla necessità.|
|**Token di accesso** |string |Stringa di token inviata alle richieste HTTP che necessitano di un'*Intestazione dell'autorizzazione*.|

#### <a name="see-what-is-in-the-access-token-optional"></a>Visualizzare gli elementi nel token di accesso (facoltativo)
Facoltativamente, è possibile copiare il valore in "Token di accesso" e incollarlo in https://jwt.ms per decodificarlo e visualizzare l'elenco delle attestazioni.

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Altre informazioni sugli ambiti e sulle autorizzazioni delegate

L'API Microsoft Graph richiede l'ambito *user.read* per leggere il profilo dell'utente. Per impostazione predefinita, questo ambito viene aggiunto automaticamente in ogni applicazione registrata nel portale di registrazione dell'applicazione. Altre API per Microsoft Graph e le API personalizzate per il server di back-end potrebbero richiedere anche altri ambiti. L'API Microsoft Graph richiede l'ambito *Calendars.Read* per elencare i calendari dell'utente.

Per accedere ai calendari dell'utente nel contesto di un'applicazione, aggiungere l'autorizzazione delegata *Calendars.Read* alle informazioni di registrazione dell'applicazione. Aggiungere quindi l'ambito *Calendars.Read* alla chiamata `acquireTokenSilent`. 

> [!NOTE]
> Con l'aumentare del numero di ambiti, è possibile che all'utente venga chiesto di esprimere anche altri tipi di consenso.

## <a name="known-issues"></a>Problemi noti

### <a name="issue-1"></a>Problema 1:
Quando si accede all'applicazione in un dominio federato di Azure Active Directory, è possibile che venga visualizzato uno degli errori seguenti:
 - Non sono stati trovati certificati client validi nella richiesta.
 - Non sono stati trovati certificati validi nell'archivio certificati dell'utente.
 - Riprovare scegliendo un metodo di autenticazione diverso.

**Causa**: le funzionalità Autenticazione aziendale e Certificati utente condivisi non sono abilitate

**Soluzione**: seguire i passaggi riportati nella sezione relativa all'[autenticazione integrata in domini federati](#enable-integrated-authentication-on-federated-domains-optional)

### <a name="issue-2"></a>Problema 2:
Dopo che si è abilitata l'[autenticazione integrata in domini federati](#enable-integrated-authentication-on-federated-domains-optional) e si è provato a usare Windows Hello in un computer Windows 10 per accedere in un ambiente con Multi-Factor Authentication configurato, viene presentato l'elenco di certificati. Se tuttavia si sceglie di usare il PIN, la finestra per il PIN non viene mai visualizzata.

**Causa**: limitazione nota con il gestore delle autenticazioni nelle applicazioni UWP in esecuzione in Windows 10 Desktop (funziona correttamente in Windows 10 Mobile)

**Soluzione alternativa**: gli utenti devono eseguire l'accesso con altre opzioni e quindi scegliere *Sign-in with a username and password* (Accedi con nome utente e password), scegliere di specificare la password e quindi eseguire l'autenticazione tramite telefono.

