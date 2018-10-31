---
title: Introduzione alla piattaforma UWP (Universal Windows Platform) per Azure AD v2.0 | Microsoft Docs
description: Informazioni su come le applicazioni della piattaforma UWP (Universal Windows Platform) possono chiamare API che richiedono token di accesso dall'endpoint di Azure Active Directory v2.0
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/24/2018
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 4ba4e844ed6bb01204b7a0adf5020aec255147dd
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2018
ms.locfileid: "49986543"
---
# <a name="call-microsoft-graph-api-from-a-universal-windows-platform-application-xaml"></a>Chiamare l'API Microsoft Graph da un'applicazione della piattaforma UWP (XAML)

> [!div renderon="docs"]
> [!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Questa guida illustra come un'applicazione nativa della piattaforma UWP (Universal Windows) può richiedere un token di accesso e quindi chiamare l'API Microsoft Graph. La guida è valida anche per altre API che richiedono i token di accesso dall'endpoint di Azure Active Directory v2.0.

Al termine di questa guida, l'applicazione chiama un'API protetta usando account personali. Esempi sono outlook.com, live.com e altri. L'applicazione userà anche account di lavoro e di formazione da qualsiasi società o organizzazione che usufruisce di Azure Active Directory.

>[!NOTE]
> Per seguire le indicazioni di questa guida è necessario che sia installato Visual Studio 2017 con la piattaforma di sviluppo UWP. Consultare [Per iniziare](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) per istruzioni su come scaricare e configurare Visual Studio per sviluppare app per piattaforma UWP.

## <a name="how-this-guide-works"></a>Come interpretare questa guida

![Grafico su come interpretare questa guida](./media/tutorial-v2-windows-uwp/uwp-intro.png)

L'applicazione di esempio creata in questa guida consente a un'applicazione UWP di eseguire query nell'API Microsoft Graph o in un'API Web che accetta token dall'endpoint di Azure Active Directory v2.0. Per questo scenario, viene aggiunto un token a richieste HTTP tramite l'intestazione di autorizzazione. Le acquisizioni e i rinnovi del token vengono gestiti da Microsoft Authentication Library (MSAL).

## <a name="nuget-packages"></a>Pacchetti NuGet

Questa guida usa i pacchetti NuGet seguenti:

|Libreria|DESCRIZIONE|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft Authentication Library|

## <a name="set-up-your-project"></a>Configurare il progetto

In questa sezione vengono fornite istruzioni dettagliate per integrare un'applicazione Windows Desktop .NET (XAML) con *Accedi con Microsoft*. Quindi è possibile eseguire una query con le API Web che richiedono un token, ad esempio API Microsoft Graph.

L'applicazione creata in questa guida consente di visualizzare un pulsante per eseguire query sull'API Graph, un pulsante di disconnessione e caselle di testo con i risultati delle chiamate.

> [!NOTE]
> Se invece si preferisce scaricare questo progetto Visual Studio di esempio, è possibile [scaricare il progetto](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/master.zip) e proseguire alla [registrazione dell'applicazione](#register-your-application "passaggio di registrazione dell'applicazione") per configurare il codice di esempio prima che venga eseguito.


### <a name="create-your-application"></a>Creare l'applicazione

1. In Visual Studio selezionare **File** > **Nuovo** > **Progetto**.
2. In **Modelli** selezionare **Visual C#**.
3. Selezionare **Blank App (Universal Windows)** (App vuota (Windows universale)).
4. Nominare l'app, quindi selezionare **OK**.
5. Se richiesto, è possibile selezionare qualsiasi versione per le versioni **Destinazione** e **Minima** e fare clic su **OK**.

    >![Versione minima e versione di destinazione](./media/tutorial-v2-windows-uwp/vs-minimum-target.png)

## <a name="add-microsoft-authentication-library-to-your-project"></a>Aggiungere Microsoft Authentication Library al progetto
1. In Visual Studio selezionare **Strumenti** > **Gestione pacchetti NuGet** > **Console di Gestione pacchetti**.
2. Nella finestra **Console di Gestione pacchetti** copiare e incollare il comando seguente:

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre -Version 1.1.4-preview0002
    ```

> [!NOTE]
> Questo comando installa [Microsoft Authentication Library](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet). MSAL acquisisce, memorizza nella cache e aggiorna i token utente che accedono alle API protette da Azure Active Directory v2.0.

> [!NOTE]
> Questa esercitazione non usa ancora la versione più recente di MSAL.NET, ma è in corso l'aggiornamento.

## <a name="initialize-msal"></a>Inizializzare MSAL
Questo passaggio consente di creare una classe per gestire l'interazione con la libreria MSAL, ad esempio per la gestione dei token.

1. Aprire il file **App.xaml.cs**, quindi aggiungere alla classe il riferimento relativo alla libreria MSAL:

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. Aggiungere le due righe seguenti alla classe dell'app, (all'interno del blocco <code>sealed partial class App : Application</code>):

    ```csharp
    // Below is the clientId of your app registration. 
    // You have to replace the below with the Application Id for your app registration
    private static string ClientId = "your_client_id_here";
    
    public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);
    ```

## <a name="create-your-applications-ui"></a>Creare l'interfaccia utente dell'applicazione

Nell'ambito del modello di progetto viene automaticamente creato un file **MainPage.xaml**. Aprire il file e seguire le istruzioni:

* Sostituire il nodo dell'applicazione **Griglia** con il codice seguente:

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
    
## <a name="use-msal-to-get-a-token-for-microsoft-graph-api"></a>Usare MSAL per ottenere un token per l'API Microsoft Graph

Questa sezione illustra come usare MSAL per ottenere un token per l'API Microsoft Graph.

1.  Nel file **MainPage.xaml.cs** aggiungere alla classe il riferimento relativo a MSAL:

    ```csharp
    using Microsoft.Identity.Client;
    ```
2. Sostituire il codice della classe <code>MainPage</code> con il codice seguente:

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

Se si chiama il metodo `AcquireTokenAsync`, viene visualizzata una finestra in cui viene chiesto agli utenti di eseguire l'accesso. Le applicazioni in genere richiedono agli utenti di eseguire l'accesso in modo interattivo quando devono accedere per la prima volta a una risorsa protetta. Potrebbe essere necessario eseguire l'accesso anche quando un'operazione invisibile all'utente per l'acquisizione di un token ha esito negativo. Ad esempio, quando scade la password dell'utente.

#### <a name="get-a-user-token-silently"></a>Ottenere un token utente in modo automatico

Il metodo `AcquireTokenSilentAsync` gestisce le acquisizioni e i rinnovi dei token senza alcuna interazione da parte dell'utente. Dopo che `AcquireTokenAsync` viene eseguita per la prima volta e all'utente vengono richieste le credenziali, il metodo `AcquireTokenSilentAsync` deve essere usato per richiedere token per le chiamate successive perché li acquisisce in modo automatico. MSAL gestirà la cache dei token e il rinnovo.

Il metodo `AcquireTokenSilentAsync` avrà infine esito negativo. Il motivo potrebbe essere la disconnessione da parte dell'utente o la modifica della password su un altro dispositivo. Se MSAL rileva che il problema può essere risolto richiedendo un'azione interattiva, viene attivata un'eccezione `MsalUiRequiredException`. L'applicazione può gestire questa eccezione in due modi:

* Può eseguire immediatamente una chiamata a `AcquireTokenAsync` e richiedere così all'utente di eseguire l'accesso. Questo modello viene usato in genere nelle applicazioni online in cui non è disponibile contenuto offline per l'utente. L'esempio generato da questa configurazione guidata segue il modello. Lo si può vedere in azione la prima volta che si esegue l'esempio. 
    * Dato che nessun utente ha usato l'applicazione, `PublicClientApp.Users.FirstOrDefault()` contiene un valore Null e viene generata un'eccezione `MsalUiRequiredException`.
    * Il codice nell'esempio gestisce quindi l'eccezione chiamando `AcquireTokenAsync`. e richiedere così all'utente di eseguire l'accesso.

* O al contrario, presenta un'indicazione visiva agli utenti per segnalare loro che è necessario un accesso interattivo. Possono quindi selezionare il momento adatto per eseguire l'accesso. In alternativa, l'applicazione può riprovare a eseguire `AcquireTokenSilentAsync` in un secondo momento. Questo modello viene spesso usato quando gli utenti possono usare altre funzionalità dell'applicazione senza interruzioni. Ad esempio, quando il contenuto offline è disponibile nell'applicazione. In questo caso, gli utenti possono decidere se vogliono eseguire l'accesso per accedere alla risorsa protetta oppure aggiornare le informazioni obsolete. In alternativa, l'applicazione può decidere di riprovare a eseguire `AcquireTokenSilentAsync` quando la rete viene ripristinata dopo essere stata temporaneamente non disponibile.

## <a name="call-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Chiamare l'API Microsoft Graph usando il token appena ottenuto

* Aggiungere il seguente nuovo metodo a **MainPage.xaml.cs**. Questo metodo consente di eseguire una richiesta `GET` all'API Graph usando un'intestazione [Authorize]:

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

In questa applicazione di esempio, viene usato il metodo `GetHttpContentWithToken` per eseguire una richiesta HTTP `GET` a una risorsa protetta che richiede un token. Il metodo restituisce quindi il contenuto al chiamante. Questo metodo aggiunge il token acquisito nell'intestazione di **autorizzazione HTTP**. Per questo esempio, la risorsa è l'endpoint **me** dell'API Microsoft Graph, che visualizza le informazioni del profilo dell'utente.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Aggiungere un metodo per disconnettere l'utente

* Per disconnettere l'utente, aggiungere il metodo seguente al file **MainPage.xaml.cs**:

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

### <a name="more-information-on-sign-out"></a>Altre informazioni sulla disconnessione

Il metodo `SignOutButton_Click` rimuove l'utente dalla cache utente MSAL. Questo metodo indica in modo efficace a MSAL di dimenticare l'utente corrente. Quindi, una richiesta futura per l’acquisizione di un token ha esito positivo solo se è creata in modo da essere interattiva.
L'applicazione in questo esempio supporta un singolo utente. Ma MSAL supporta scenari in cui più account possono eseguire l'accesso nello stesso momento. Ad esempio, un'applicazione di posta elettronica in cui un utente gestisce più account.

## <a name="display-basic-token-information"></a>Visualizzare informazioni di base sui token

* Aggiungere il metodo seguente al file **MainPage.xaml.cs** per visualizzare informazioni di base sul token:

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

I token ID acquisiti tramite **OpenID Connect** includono anche un piccolo subset di informazioni relative all'utente. `DisplayBasicTokenInfo` visualizza le informazioni di base contenute nel token. Esempi sono il nome dell'utente visualizzato e l'ID, la data di scadenza del token e la stringa che rappresenta il token di accesso. Premendo più volte il pulsante **Chiamare API Microsoft Graph**, è possibile vedere che lo stesso token è stato usato di nuovo per richieste successive. Se MSAL decide che è il momento di rinnovare il token, è possibile anche visualizzare la data di scadenza estesa.

## <a name="register-your-application"></a>Registrare l'applicazione

È ora necessario registrare l'applicazione nel portale di registrazione delle applicazioni Microsoft:
1. Passare al [portale di registrazione delle applicazioni Microsoft](https://apps.dev.microsoft.com/portal/register-app) per registrare un'applicazione.
2. Immettere un nome per l'applicazione.
3. Assicurarsi che l'opzione per **Installazione guidata** *non sia selezionata*.
4. Selezionare **Aggiungi piattaforme**, **Applicazione nativa** e quindi **Salva**.
5. Copiare il GUID nell'**ID applicazione**, tornare a Visual Studio, aprire **App.xaml.cs** e sostituire con `your_client_id_here`l'ID applicazione appena registrato:

    ```csharp
    private static string ClientId = "your_application_id_here";
    ```

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Abilitare l'autenticazione integrata in domini federati (facoltativo)

Per abilitare l'autenticazione integrata di Windows quando è in uso con un dominio federato di Azure Active Directory, il manifesto dell'applicazione deve abilitare funzionalità aggiuntive:

1. Fare doppio clic su **Package.appxmanifest**.
2. Selezionare la scheda **Funzionalità** e assicurarsi che le impostazioni seguenti siano abilitate:

    - Autenticazione aziendale
    - Reti private (client e server)
    - Certificati utente condivisi

3. Aprire **App.xaml.cs** e aggiungere la riga seguente nel costruttore dell'app:

    ```csharp
    App.PublicClientApp.UseCorporateNetwork = true;
    ```

> [!IMPORTANT]
> L'Autenticazione integrata di Windows non è configurata per impostazione predefinita per questo esempio. Le applicazioni che supportano funzionalità di *Autenticazione Enterprise* oppure *Certificati utente condivisi* richiedono un livello di verifica da Windows Store. Inoltre, gli sviluppatori desiderano eseguire un livello ulteriore di verifica. Abilitare questa impostazione solo se è necessario usare l'autenticazione integrata di Windows con un dominio federato di Azure Active Directory.

## <a name="test-your-code"></a>Testare il codice

Per testare l'applicazione, premere F5 per eseguire il progetto in Visual Studio. Verrà visualizzata la finestra principale:

![Interfaccia utente dell'applicazione](./media/tutorial-v2-windows-uwp/testapp-ui.png)

Quando si è pronti per eseguire test, selezionare **Call Microsoft Graph API** (Chiama API Microsoft Graph). Usare quindi un account Microsoft Azure Active Directory (account aziendale) o un account Microsoft (live.com, outlook.com) per accedere. Se è la prima volta, verrà visualizzata una finestra in cui si chiede all'utente di eseguire l'accesso:

![Pagina di accesso](./media/tutorial-v2-windows-uwp/sign-in-page.png)

### <a name="consent"></a>Consenso
La prima volta che si accede all'applicazione, viene visualizzata una schermata di consenso, simile alla seguente. Selezionare **Sì** per fornire il consenso esplicito all'accesso:

![Schermata di consenso per l'accesso](./media/tutorial-v2-windows-uwp/consentscreen.png)
### <a name="expected-results"></a>Risultati previsti
Nella schermata **Risultati della chiamata API** vengono visualizzate le informazioni sul profilo utente restituite dalla chiamata all'API Microsoft Graph:

![Schermata dei risultati della chiamata API](./media/tutorial-v2-windows-uwp/uwp-results-screen.PNG)

Nella casella **Informazioni sul token** vengono visualizzate anche informazioni di base sul token acquisite tramite `AcquireTokenAsync` o `AcquireTokenSilentAsync`:

|Proprietà  |Format  |DESCRIZIONE |
|---------|---------|---------|
|**Nome** |Nome e cognome dell'utente|Nome e cognome dell'utente.|
|**Nome utente** |<span>user@domain.com</span> |Il nome utente che identifica l'utente.|
|**Token Expires** (Scadenza token) |Datetime |Data e ora in cui scade il token. MSAL estende la data di scadenza rinnovando il token in base alla necessità.|
|**Token di accesso** |string |Stringa di token inviata alle richieste HTTP che necessitano di un'*Intestazione dell'autorizzazione*.|

#### <a name="see-whats-in-the-access-token-optional"></a>Visualizzare gli elementi nel token di accesso (facoltativo)
Facoltativamente, è possibile copiare il valore in **Token di accesso** e incollarlo in https://jwt.ms per decodificarlo e visualizzare l'elenco delle attestazioni.

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Altre informazioni sugli ambiti e sulle autorizzazioni delegate

L'API Microsoft Graph richiede l'ambito *user.read* per leggere il profilo dell'utente. Per impostazione predefinita, questo ambito viene aggiunto automaticamente in ogni applicazione registrata nel Portale di registrazione dell'applicazione. Altre API per Microsoft Graph e le API personalizzate per il server di back-end potrebbero richiedere anche altri ambiti. L'API Microsoft Graph richiede l'ambito *Calendars.Read* per elencare i calendari dell'utente.

Per accedere ai calendari dell'utente nel contesto di un'applicazione, aggiungere l'autorizzazione delegata *Calendars.Read* alle informazioni di registrazione dell'applicazione. Aggiungere quindi l'ambito *Calendars.Read* alla chiamata `acquireTokenSilent`.

> [!NOTE]
> Con l'aumentare del numero di ambiti, è possibile che agli utenti venga chiesto di esprimere anche altri tipi di consenso.

## <a name="known-issues"></a>Problemi noti

### <a name="issue-1"></a>Problema 1
Quando si accede all'applicazione in un dominio federato di Azure Active Directory, è possibile che venga visualizzato uno dei seguenti messaggi di errore:
 - Non sono stati trovati certificati client validi nella richiesta.
 - Non sono stati trovati certificati validi nell'archivio certificati dell'utente.
 - Riprovare scegliendo un metodo di autenticazione diverso.

**Causa:** le funzionalità Enterprise e certificati non sono abilitate.

**Soluzione:** seguire i passaggi riportati nella sezione relativa all'[autenticazione integrata in domini federati](#enable-integrated-authentication-on-federated-domains-optional).

### <a name="issue-2"></a>Problema 2
Abilitare[l'autenticazione integrata in domini federati](#enable-integrated-authentication-on-federated-domains-optional) e provare a usare Windows Hello in un computer Windows 10 per accedere in un ambiente configurato con autenticazione a più fattori. Viene riportato l'elenco dei certificati. Tuttavia, se si sceglie di usare il PIN, la finestra PIN non viene mai riportata.

**Causa:** questo problema è una limitazione nota del gestore delle autenticazioni Web nelle applicazioni della piattaforma UWP eseguite nel desktop di Windows 10. Funziona correttamente su Windows 10 Mobile.

**Soluzione alternativa:** selezionare **Accedi con altre opzioni**. Selezionare quindi **Accedi con nome utente e password**. Selezionare **Immettere la password**. Quindi eseguire il processo di autenticazione del telefono.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]