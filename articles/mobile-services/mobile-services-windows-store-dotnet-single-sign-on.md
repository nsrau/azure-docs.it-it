<properties 
	pageTitle="Autenticare l'app di Windows Store con Live Connect" 
	description="Informazioni su come usare l'accesso Single Sign-On di Live Connect in Servizi mobili di Azure da un'applicazione di Windows Store." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/08/2015" 
	ms.author="glenga"/>

# Autenticare l'app di Windows Store mediante l'autenticazione gestita da client con account Microsoft

[AZURE.INCLUDE [mobile-services-selector-single-signon](../../includes/mobile-services-selector-single-signon.md)]

##Panoramica
Questo argomento illustra come ottenere un token di autenticazione per account Microsoft usando Live SDK da un'app di Windows universale. È possibile usare questo token per autenticare gli utenti con Servizi mobili di Azure. In questa esercitazione si aggiungerà l'autenticazione con account Microsoft a un progetto esistente usando Live SDK. Dopo l'autenticazione e l'accesso, l'utente visualizza una schermata di benvenuto in cui sono indicati il nome utente e il relativo ID.

>[AZURE.NOTE]Questa esercitazione illustra i vantaggi offerti dall'uso dell'autenticazione diretta da client e di Live SDK. Ciò consente di semplificare l'autenticazione degli utenti che hanno già effettuato l'accesso al servizio mobile. È inoltre possibile richiedere ambiti aggiuntivi per consentire all'app di accedere anche a risorse come OneDrive. L'autenticazione diretta dal servizio offre un'esperienza più generalizzata e supporta più provider di autenticazione. Per altre informazioni sull'autenticazione diretta dal servizio, vedere l'argomento [Aggiungere l'autenticazione all'app](mobile-services-javascript-backend-windows-universal-dotnet-get-started-users.md).

Per completare questa esercitazione, è necessario disporre di:

+ [Live SDK]
+ Microsoft Visual Studio 2013 Update 3 o versione successiva.
+ È inoltre necessario completare prima l'esercitazione [Introduzione a Servizi mobili](mobile-services-javascript-backend-windows-store-dotnet-get-started.md) o [Aggiungere Servizi mobili a un'app esistente].

##Registrare l'app per l'uso dell'account Microsoft per l'autenticazione

Per poter autenticare gli utenti, è necessario registrare l'app presso il centro per sviluppatori degli account Microsoft. È quindi necessario connettere questa registrazione al servizio mobile. Completare i passaggi nell'argomento seguente per creare una registrazione di account Microsoft e connettersi al servizio mobile.

+ [Registrare l'app per l'uso delle credenziali di accesso di un account Microsoft](mobile-services-how-to-register-microsoft-authentication.md)

##<a name="permissions"></a>Limitare le autorizzazioni agli utenti autenticati

È quindi necessario limitare l'accesso a una risorsa, in questo caso la tabella *TodoItems*, per assicurarsi che sia accessibile solo da un utente connesso.

[AZURE.INCLUDE [mobile-services-restrict-permissions-windows](../../includes/mobile-services-restrict-permissions-windows.md)]

##<a name="add-authentication"></a>Aggiungere l'autenticazione all'app

Infine, aggiungere Live SDK e usarlo per autenticare gli utenti nell'app.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla soluzione e quindi scegliere **Gestisci pacchetti NuGet**.

2. Nel riquadro a sinistra selezionare la categoria **Online**, cercare **LiveSDK**, fare clic su **Installa** nel pacchetto di **Live SDK**, selezionare tutti i progetti e quindi accettare i contratti di licenza.

  	Live SDK verrà aggiunto alla soluzione.

3. Aprire il file del progetto MainPage.cs condiviso e aggiungere l'istruzione using seguente:

        using Microsoft.Live;        

4. Aggiungere il seguente frammento di codice alla classe **MainPage**:
	
        private LiveConnectSession session;
        private async System.Threading.Tasks.Task AuthenticateAsync()
        {

            // Get the URL the mobile service.
            var serviceUrl = App.MobileService.ApplicationUri.AbsoluteUri;

            // Create the authentication client using the mobile service URL.
            LiveAuthClient liveIdClient = new LiveAuthClient(serviceUrl);

            while (session == null)
            {
                // Request the authentication token from the Live authentication service.
				// The wl.basic scope is requested.
                LiveLoginResult result = await liveIdClient.LoginAsync(new string[] { "wl.basic" });
                if (result.Status == LiveConnectSessionStatus.Connected)
                {
                    session = result.Session;

                    // Get information about the logged-in user.
                    LiveConnectClient client = new LiveConnectClient(session);
                    LiveOperationResult meResult = await client.GetAsync("me");

                    // Use the Microsoft account auth token to sign in to Mobile Services.
                    MobileServiceUser loginResult = await App.MobileService
                        .LoginWithMicrosoftAccountAsync(result.Session.AuthenticationToken);

                    // Display a personalized sign-in greeting.
                    string title = string.Format("Welcome {0}!", meResult.Result["first_name"]);
                    var message = string.Format("You are now logged in - {0}", loginResult.UserId);
                    var dialog = new MessageDialog(message, title);
                    dialog.Commands.Add(new UICommand("OK"));
                    await dialog.ShowAsync();
                }
                else
                {
                    session = null;
                    var dialog = new MessageDialog("You must log in.", "Login Required");
                    dialog.Commands.Add(new UICommand("OK"));
                    await dialog.ShowAsync();
                }
            }
        }
    
    Verranno creati una variabile membro per archiviare la sessione Live Connect corrente e un metodo per gestire il processo di autenticazione.

	>[AZURE.NOTE]È consigliabile provare a usare i token di autorizzazione di Servizi mobili o i token di autenticazione Live memorizzati nella cache prima di richiedere nuovi token ai servizi. Se non si effettua questa operazione, si possono riscontrare problemi di utilizzo nel caso in cui molti clienti provino ad avviare simultaneamente l'app. Per un esempio di come memorizzare nella cache questo token, vedere [Introduzione all'autenticazione](../mobile-services-windows-store-dotnet-get-started-users.md#tokens).

5. Impostare come commento o eliminare la chiamata al metodo **RefreshTodoItems** nell'override del metodo **OnNavigatedTo** esistente.

	Ciò impedisce il caricamento dei dati prima dell'autenticazione dell'utente. Si aggiungerà quindi un pulsante per avviare la procedura di accesso.

6. Aggiungere il seguente frammento di codice alla classe MainPage:

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile service.
            await AuthenticateAsync();

            // Hide the login button and load items from the mobile service.
            this.ButtonLogin.Visibility = Windows.UI.Xaml.Visibility.Collapsed;
            await RefreshTodoItems();
        }
		
7. Nel progetto dell'app di Windows Store, aprire il file di progetto MainPage.xaml e aggiungere il seguente elemento **Button** giusto prima dell'elemento che definisce il pulsante **Save**:

		<Button Name="ButtonLogin" Click="ButtonLogin_Click" 
                        Visibility="Visible">Sign in</Button>

8. Ripetere il passaggio precedente per il progetto dell'app di Windows Phone Store, questa volta aggiungendo l'elemento **Button** in **TitlePanel**, dopo l'elemento **TextBlock**.
		
9. Premere F5 per eseguire l'app e accedere a Live Connect con l'account Microsoft.

	Dopo avere eseguito l'accesso, l'app dovrebbe funzionare senza errori e dovrebbe essere possibile eseguire query in Servizi mobili e aggiornare i dati.

10. Fare clic con il pulsante destro del mouse sul progetto dell'app di Windows Phone Store, selezionare **Imposta come progetto di avvio**, quindi ripetere il passaggio precedente per verificare la corretta esecuzione dell'app di Windows Phone Store.

A questo punto, qualsiasi utente autenticato da uno dei provider di identità attendibili può accedere alla tabella *TodoItem*. Per proteggere meglio i dati specifici dell'utente, è necessario implementare anche l'autorizzazione. A tale scopo si ottiene l'ID di un determinato utente, che può quindi essere usato per definire il livello di accesso che l'utente deve avere per una specifica risorsa.

## <a name="next-steps"> </a>Passaggi successivi

Nella prossima esercitazione, [Autorizzazione di utenti con script], il valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato verrà utilizzato per filtrare i dati restituiti da Servizi mobili. Per informazioni su come utilizzare altri provider di identità per l'autenticazione, vedere [Introduzione all'autenticazione]. Per ulteriori informazioni su come utilizzare Servizi mobili con .NET, vedere [Riferimento per i concetti e le procedure di .NET per Servizi mobili]

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039

[Aggiungere Servizi mobili a un'app esistente]: ../mobile-services-windows-store-dotnet-get-started-data.md
[Introduzione all'autenticazione]: ../mobile-services-windows-store-dotnet-get-started-users.md
[Autorizzazione di utenti con script]: mobile-services-javascript-backend-service-side-authorization.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Riferimento per i concetti e le procedure di .NET per Servizi mobili]: mobile-services-windows-dotnet-how-to-use-client-library.md
[Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
 

<!---HONumber=July15_HO4-->