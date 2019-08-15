---
title: Introduzione all'uso di applicazioni UWP con Microsoft Identity Platform | Azure
description: Informazioni su come le applicazioni della piattaforma UWP (Universal Windows Platform) possono chiamare API che richiedono token di accesso generati dall'endpoint di Microsoft Identity Platform.
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
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 519a86bed6a3a09b476bce6435ae666d655dbe03
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852253"
---
# <a name="call-microsoft-graph-api-from-a-universal-windows-platform-application-xaml"></a>Chiamare l'API Microsoft Graph da un'applicazione della piattaforma UWP (XAML)

> [!div renderon="docs"]

Questa guida illustra come un'applicazione nativa della piattaforma UWP (Universal Windows) può richiedere un token di accesso e quindi chiamare l'API Microsoft Graph. La guida si applica anche ad altre API che richiedono token di accesso generati dall'endpoint di Microsoft Identity Platform.

Al termine di questa guida, l'applicazione chiama un'API protetta usando account personali. Esempi sono outlook.com, live.com e altri. L'applicazione chiama anche account aziendali o dell'istituto di istruzione di qualsiasi organizzazione o azienda in cui viene usato Azure Active Directory (Azure AD).

>[!NOTE]
> Per seguire le indicazioni di questa guida è necessario che sia installato Visual Studio 2017 con la piattaforma di sviluppo UWP. Consultare [Per iniziare](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) per istruzioni su come scaricare e configurare Visual Studio per sviluppare app per piattaforma UWP.

## <a name="how-this-guide-works"></a>Come interpretare questa guida

![Illustra come funziona l'app di esempio generata da questa esercitazione](./media/tutorial-v2-windows-uwp/uwp-intro.svg)

Questa guida consente di creare un'applicazione UWP di esempio da cui vengono inoltrate query all'API Microsoft Graph o a un'API Web che accetta token generati dall'endpoint di Microsoft Identity Platform. Per questo scenario, viene aggiunto un token a richieste HTTP tramite l'intestazione di autorizzazione. Le acquisizioni e i rinnovi del token vengono gestiti da Microsoft Authentication Library (MSAL).

## <a name="nuget-packages"></a>Pacchetti NuGet

Questa guida usa i pacchetti NuGet seguenti:

|Libreria|DESCRIZIONE|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft Authentication Library|

## <a name="set-up-your-project"></a>Configurare il progetto

In questa sezione vengono fornite istruzioni dettagliate per integrare un'applicazione Windows Desktop .NET (XAML) con *Accedi con Microsoft*. Quindi è possibile eseguire una query con le API Web che richiedono un token, ad esempio API Microsoft Graph.

L'applicazione creata in questa guida consente di visualizzare un pulsante per eseguire query sull'API Graph, un pulsante di disconnessione e caselle di testo con i risultati delle chiamate.

> [!NOTE]
> Se invece si preferisce scaricare questo progetto Visual Studio di esempio, è possibile [scaricare il progetto](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip) e proseguire alla [registrazione dell'applicazione](#register-your-application "passaggio di registrazione dell'applicazione") per configurare il codice di esempio prima che venga eseguito.

### <a name="create-your-application"></a>Creare l'applicazione

1. In Visual Studio selezionare **File** > **Nuovo** > **Progetto**.
2. In **Modelli** selezionare **Visual C#** .
3. Selezionare **Blank App (Universal Windows)** (App vuota (Windows universale)).
4. Nominare l'app, quindi selezionare **OK**.
5. Se richiesto, è possibile selezionare qualsiasi versione per le versioni **Destinazione** e **Minima** e fare clic su **OK**.

    >![Versione minima e versione di destinazione](./media/tutorial-v2-windows-uwp/vs-minimum-target.png)

## <a name="add-microsoft-authentication-library-to-your-project"></a>Aggiungere Microsoft Authentication Library al progetto
1. In Visual Studio selezionare **Strumenti** > **Gestione pacchetti NuGet** > **Console di Gestione pacchetti**.
2. Nella finestra **Console di Gestione pacchetti** copiare e incollare il comando seguente:

    ```powershell
    Install-Package Microsoft.Identity.Client -IncludePrerelease
    ```

> [!NOTE]
> Questo comando installa [Microsoft Authentication Library](https://aka.ms/msal-net). MSAL acquisisce, memorizza nella cache e aggiorna i token utente che accedono alle API protette da Microsoft Identity Platform.

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
        //Set the API Endpoint to Graph 'me' endpoint
        string graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";

        //Set the scope for API call to user.read
        string[] scopes = new string[] { "user.read" };

        // Below are the clientId (Application Id) of your app registration and the tenant information. 
        // You have to replace:
        // - the content of ClientID with the Application Id for your app registration
        // - Te content of Tenant by the information about the accounts allowed to sign-in in your application:
        //   - For Work or School account in your org, use your tenant ID, or domain
        //   - for any Work or School accounts, use organizations
        //   - for any Work or School accounts, or Microsoft personal account, use common
        //   - for Microsoft Personal account, use consumers
        private const string ClientId = "0b8b0665-bc13-4fdc-bd72-e0227b9fc011";        

        public IPublicClientApplication PublicClientApp { get; } 

        public MainPage()
        {
          this.InitializeComponent();

          PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(AadAuthorityAudience.AzureAdAndPersonalMicrosoftAccount)
                .WithLogging((level, message, containsPii) =>
                {
                    Debug.WriteLine($"MSAL: {level} {message} ");
                }, LogLevel.Warning, enablePiiLogging:false,enableDefaultPlatformLogging:true)
                .WithUseCorporateNetwork(true)
                .Build();
        }

        /// <summary>
        /// Call AcquireTokenInteractive - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
         AuthenticationResult authResult = null;
         ResultText.Text = string.Empty;
         TokenInfoText.Text = string.Empty;

         // It's good practice to not do work on the UI thread, so use ConfigureAwait(false) whenever possible.            
         IEnumerable<IAccount> accounts = await PublicClientApp.GetAccountsAsync().ConfigureAwait(false); 
         IAccount firstAccount = accounts.FirstOrDefault();

         try
         {
          authResult = await PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                                  .ExecuteAsync();
         }
         catch (MsalUiRequiredException ex)
         {
          // A MsalUiRequiredException happened on AcquireTokenSilent.
          // This indicates you need to call AcquireTokenInteractive to acquire a token
          System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

          try
          {
           authResult = await PublicClientApp.AcquireTokenInteractive(scopes)
                                                      .ExecuteAsync()
                                                      .ConfigureAwait(false);
           }
           catch (MsalException msalex)
           {
            await DisplayMessageAsync($"Error Acquiring Token:{System.Environment.NewLine}{msalex}");
           }
          }
          catch (Exception ex)
          {
           await DisplayMessageAsync($"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}");
           return;
          }

          if (authResult != null)
          {
           var content = await GetHttpContentWithToken(graphAPIEndpoint,
                                                       authResult.AccessToken).ConfigureAwait(false);

           // Go back to the UI thread to make changes to the UI
           await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
           {
            ResultText.Text = content;
            DisplayBasicTokenInfo(authResult);
            this.SignOutButton.Visibility = Visibility.Visible;
           });
          }
        }
    ```

### <a name="more-information"></a>Altre informazioni

#### <a name="get-a-user-token-interactively"></a>Ottenere un token utente in modo interattivo

Se si chiama il metodo `AcquireTokenInteractive`, viene visualizzata una finestra in cui viene chiesto agli utenti di eseguire l'accesso. Le applicazioni in genere richiedono agli utenti di eseguire l'accesso in modo interattivo quando devono accedere per la prima volta a una risorsa protetta. Potrebbe essere necessario eseguire l'accesso anche quando un'operazione invisibile all'utente per l'acquisizione di un token ha esito negativo. Ad esempio, quando scade la password dell'utente.

#### <a name="get-a-user-token-silently"></a>Ottenere un token utente in modo automatico

Il metodo `AcquireTokenSilent` gestisce le acquisizioni e i rinnovi dei token senza alcuna interazione da parte dell'utente. Dopo che `AcquireTokenInteractive` è stato eseguito per la prima volta e sono state richieste le credenziali all'utente, il metodo `AcquireTokenSilent` deve essere usato per richiedere i token per le chiamate successive perché l'acquisizione viene eseguita in modo automatico. MSAL gestirà la cache dei token e il rinnovo.

Il metodo `AcquireTokenSilent` avrà infine esito negativo. Il motivo potrebbe essere la disconnessione da parte dell'utente o la modifica della password su un altro dispositivo. Se MSAL rileva che il problema può essere risolto richiedendo un'azione interattiva, viene attivata un'eccezione `MsalUiRequiredException`. L'applicazione può gestire questa eccezione in due modi:

* Può eseguire immediatamente una chiamata a `AcquireTokenInteractive` e richiedere così all'utente di eseguire l'accesso. Questo modello viene usato in genere nelle applicazioni online in cui non è disponibile contenuto offline per l'utente. L'esempio generato da questa configurazione guidata segue il modello. Lo si può vedere in azione la prima volta che si esegue l'esempio.
  * Dato che nessun utente ha usato l'applicazione, `accounts.FirstOrDefault()` contiene un valore Null e viene generata un'eccezione `MsalUiRequiredException`.
  * Il codice nell'esempio gestisce quindi l'eccezione chiamando `AcquireTokenInteractive`. e richiedere così all'utente di eseguire l'accesso.

* O al contrario, presenta un'indicazione visiva agli utenti per segnalare loro che è necessario un accesso interattivo. Possono quindi selezionare il momento adatto per eseguire l'accesso. In alternativa, l'applicazione può riprovare a eseguire `AcquireTokenSilent` in un secondo momento. Questo modello viene spesso usato quando gli utenti possono usare altre funzionalità dell'applicazione senza interruzioni. Ad esempio, quando il contenuto offline è disponibile nell'applicazione. In questo caso, gli utenti possono decidere se vogliono eseguire l'accesso per accedere alla risorsa protetta oppure aggiornare le informazioni obsolete. In alternativa, l'applicazione può decidere di riprovare a eseguire `AcquireTokenSilent` quando la rete viene ripristinata dopo essere stata temporaneamente non disponibile.

## <a name="call-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Chiamare l'API Microsoft Graph usando il token appena ottenuto

* Aggiungere il seguente nuovo metodo a **MainPage.xaml.cs**. Questo metodo consente di eseguire una richiesta `GET` all'API Graph usando un'intestazione `Authorization`:

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
            request.Headers.Authorization = 
              new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
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
    private async void SignOutButton_Click(object sender, RoutedEventArgs e)
    {
        IEnumerable<IAccount> accounts = await PublicClientApp.GetAccountsAsync
                                                              .ConfigureAwait(false);
        IAccount firstAccount = accounts.FirstOrDefault();

        try
        {
            await PublicClientApp.RemoveAsync(firstAccount).ConfigureAwait(false);
            await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
            {
                ResultText.Text = "User has signed-out";
                this.CallGraphButton.Visibility = Visibility.Visible;
                    this.SignOutButton.Visibility = Visibility.Collapsed;
                });
            }
            catch (MsalException ex)
            {
                ResultText.Text = $"Error signing-out user: {ex.Message}";
            }
        }
    ```

> [!NOTE]
> MSAL.NET usa metodi asincroni per acquisire i token o modificare gli account. È quindi necessario occuparsi di eseguire le azioni basate sull'interfaccia utente nel thread UI, e pertanto includere `Dispatcher.RunAsync`, oltre a chiamare per precauzione `ConfigureAwait(false)`

### <a name="more-information-on-sign-out"></a>Altre informazioni sulla disconnessione

Il metodo `SignOutButton_Click` rimuove l'utente dalla cache utente MSAL. Questo metodo indica in modo efficace a MSAL di dimenticare l'utente corrente. Quindi, una richiesta futura per l’acquisizione di un token ha esito positivo solo se è creata in modo da essere interattiva.
L'applicazione in questo esempio supporta un singolo utente. Ma MSAL supporta scenari in cui più account possono eseguire l'accesso nello stesso momento. Ad esempio, un'applicazione di posta elettronica in cui un utente gestisce più account.

## <a name="display-basic-token-information"></a>Visualizzare informazioni di base sui token

* Aggiungere il metodo seguente al file **MainPage.xaml.cs** per visualizzare informazioni di base sul token:

    ```csharp
    /// <summary>
    /// Display basic information contained in the token. Needs to be called from the UI thead.
    /// </summary>
    private void DisplayBasicTokenInfo(AuthenticationResult authResult)
    {
        TokenInfoText.Text = "";
        if (authResult != null)
        {
            TokenInfoText.Text += $"User Name: {authResult.Account.Username}" + Environment.NewLine;
            TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
        }
    }
    ```

### <a name="more-information"></a>Altre informazioni

I token ID acquisiti tramite **OpenID Connect** includono anche un piccolo subset di informazioni relative all'utente. `DisplayBasicTokenInfo` visualizza le informazioni di base contenute nel token. Esempi sono il nome dell'utente visualizzato e l'ID, la data di scadenza del token e la stringa che rappresenta il token di accesso. Premendo più volte il pulsante **Chiamare API Microsoft Graph**, è possibile vedere che lo stesso token è stato usato di nuovo per richieste successive. Se MSAL decide che è il momento di rinnovare il token, è possibile anche visualizzare la data di scadenza estesa.

## <a name="register-your-application"></a>Registrare l'applicazione

È ora necessario registrare l'applicazione nel portale di registrazione delle applicazioni Microsoft:

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Se l'account è presente in più tenant di Azure AD, selezionare `Directory + Subscription` nell'angolo in alto a destra nel menu disponibile nella parte superiore di pagina e passare al tenant di Azure AD desiderato nella sessione del portale.
1. Passare alla pagina [Registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) di Microsoft Identity Platform per sviluppatori.
1. Selezionare **Nuova registrazione**.
   - Nella sezione **Nome** immettere un nome di applicazione significativo che verrà visualizzato agli utenti dell'app, ad esempio `UWP-App-calling-MSGraph`.
   - Nella sezione **Tipi di account supportati** selezionare **Account in qualsiasi directory organizzativa e account Microsoft personali (ad esempio Skype, Xbox, Outlook.com)** .
   - Selezionare **Registra** per creare l'applicazione.
1. Nella pagina **Panoramica**  dell'app trovare il valore del campo **ID applicazione (client)** e prenderne nota. Tornare a Visual Studio, aprire **MainPage.xaml.cs** e sostituire il valore di ClientId con l'ID applicazione annotato nel passaggio precedente:
1. Nell'elenco delle pagine per l'app selezionare **Autenticazione**.
   1. Nella sezione **URI di reindirizzamento**, nell'elenco URI di reindirizzamento:
   1. Nella colonna **TIPO** selezionare **Client pubblico (per dispositivi mobili e desktop)** .
   1. Immettere `urn:ietf:wg:oauth:2.0:oob` nella colonna **URI DI REINDIRIZZAMENTO**.
1. Selezionare **Salva**.
1. Nell'elenco delle pagine per l'app selezionare **Autorizzazioni API**
   - Fare clic sul pulsante **Aggiungi un'autorizzazione** e quindi
   - Verificare che la scheda **API Microsoft** sia selezionata
   - Nella sezione *API Microsoft più usate* fare clic su **Microsoft Graph**
   - Nella sezione **Autorizzazioni delegate** verificare che siano selezionate le autorizzazioni appropriate: **User.Read**. Se necessario, usare la casella di ricerca.
   - Selezionare il pulsante **Aggiungi autorizzazioni**

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Abilitare l'autenticazione integrata in domini federati (facoltativo)

Per abilitare l'autenticazione integrata di Windows quando è in uso con un dominio federato di Azure AD, il manifesto dell'applicazione deve abilitare funzionalità aggiuntive:

1. Fare doppio clic su **Package.appxmanifest**.
2. Selezionare la scheda **Funzionalità** e assicurarsi che le impostazioni seguenti siano abilitate:

    - Autenticazione aziendale
    - Reti private (client e server)
    - Certificati utente condivisi

> [!IMPORTANT]
> L'[Autenticazione integrata di Windows](https://aka.ms/msal-net-iwa) non è configurata per impostazione predefinita per questo esempio. Le applicazioni che supportano funzionalità di *Autenticazione Enterprise* oppure *Certificati utente condivisi* richiedono un livello di verifica da Windows Store. Inoltre, gli sviluppatori desiderano eseguire un livello ulteriore di verifica. Abilitare questa impostazione solo se è necessario usare l'autenticazione integrata di Windows con un dominio federato di Azure AD.

## <a name="test-your-code"></a>Testare il codice

Per testare l'applicazione, premere F5 per eseguire il progetto in Visual Studio. Verrà visualizzata la finestra principale:

![Interfaccia utente dell'applicazione](./media/tutorial-v2-windows-uwp/testapp-ui.png)

Quando si è pronti per eseguire test, selezionare **Call Microsoft Graph API** (Chiama API Microsoft Graph). Usare quindi un account aziendale Azure AD o un account Microsoft, ad esempio live.com o outlook.com, per accedere. Se è la prima volta, verrà visualizzata una finestra in cui si chiede all'utente di eseguire l'accesso:

![Pagina di accesso](./media/tutorial-v2-windows-uwp/sign-in-page.png)

### <a name="consent"></a>Consenso

La prima volta che si accede all'applicazione, viene visualizzata una schermata di consenso, simile alla seguente. Selezionare **Sì** per fornire il consenso esplicito all'accesso:

![Schermata di consenso per l'accesso](./media/tutorial-v2-windows-uwp/consentscreen.png)

### <a name="expected-results"></a>Risultati previsti

Nella schermata **Risultati della chiamata API** vengono visualizzate le informazioni sul profilo utente restituite dalla chiamata all'API Microsoft Graph:

![Schermata dei risultati della chiamata API](./media/tutorial-v2-windows-uwp/uwp-results-screen.PNG)

Nella casella **Informazioni sul token** vengono visualizzate anche informazioni di base sul token acquisite tramite `AcquireTokenInteractive` o `AcquireTokenSilent`:

|Proprietà  |Format  |DESCRIZIONE |
|---------|---------|---------|
|**Nome utente** |<span>user@domain.com</span> |Il nome utente che identifica l'utente.|
|**Token Expires** (Scadenza token) |Datetime |Data e ora in cui scade il token. MSAL estende la data di scadenza rinnovando il token in base alla necessità.|

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Altre informazioni sugli ambiti e sulle autorizzazioni delegate

L'API Microsoft Graph richiede l'ambito *user.read* per leggere il profilo dell'utente. Per impostazione predefinita, questo ambito viene aggiunto automaticamente in ogni applicazione registrata nel Portale di registrazione dell'applicazione. Altre API per Microsoft Graph e le API personalizzate per il server di back-end potrebbero richiedere anche altri ambiti. L'API Microsoft Graph richiede l'ambito *Calendars.Read* per elencare i calendari dell'utente.

Per accedere ai calendari dell'utente nel contesto di un'applicazione, aggiungere l'autorizzazione delegata *Calendars.Read* alle informazioni di registrazione dell'applicazione. Aggiungere quindi l'ambito *Calendars.Read* alla chiamata `acquireTokenSilent`.

> [!NOTE]
> Con l'aumentare del numero di ambiti, è possibile che agli utenti venga chiesto di esprimere anche altri tipi di consenso.

## <a name="known-issues"></a>Problemi noti

### <a name="issue-1"></a>Problema 1

Quando si accede all'applicazione in un dominio federato di Azure AD, è possibile che venga visualizzato uno dei messaggi di errore seguenti:

* Non sono stati trovati certificati client validi nella richiesta.
* Non sono stati trovati certificati validi nell'archivio certificati dell'utente.
* Riprovare scegliendo un metodo di autenticazione diverso.

**Causa:** le funzionalità Enterprise e certificati non sono abilitate.

**Soluzione:** seguire i passaggi riportati nella sezione relativa all'[autenticazione integrata in domini federati](#enable-integrated-authentication-on-federated-domains-optional).

### <a name="issue-2"></a>Problema 2

Abilitare[l'autenticazione integrata in domini federati](#enable-integrated-authentication-on-federated-domains-optional) e provare a usare Windows Hello in un computer Windows 10 per accedere in un ambiente configurato con autenticazione a più fattori. Viene riportato l'elenco dei certificati. Tuttavia, se si sceglie di usare il PIN, la finestra PIN non viene mai riportata.

**Causa:** questo problema è una limitazione nota del gestore delle autenticazioni Web nelle applicazioni della piattaforma UWP eseguite nel desktop di Windows 10. Funziona correttamente su Windows 10 Mobile.

**Soluzione alternativa:** selezionare **Accedi con altre opzioni**. Selezionare quindi **Accedi con nome utente e password**. Selezionare **Immettere la password**. Quindi eseguire il processo di autenticazione del telefono.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Contribuire al miglioramento di Microsoft Identity Platform. Completare un breve sondaggio di due domande per condividere la propria opinione.

> [!div class="nextstepaction"]
> [Sondaggio su Microsoft Identity Platform](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
