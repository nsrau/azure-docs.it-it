<properties 
	pageTitle="Autenticare l'app con Live Connect (Windows Phone) | Mobile Dev Center" 
	description="Informazioni su come usare l'accesso Single Sign-On di Live Connect in Servizi mobili di Azure da un'applicazione per Windows Phone." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/09/2015" 
	ms.author="glenga"/>

# Autenticare l'app di Windows Phone mediante l'autenticazione gestita da client con account Microsoft

[AZURE.INCLUDE [mobile-services-selector-single-signon](../../includes/mobile-services-selector-single-signon.md)]
##Panoramica
Questo argomento illustra come ottenere un token di autenticazione per account Microsoft usando Live SDK da un'app per Windows Phone 8 o Windows Phone 8.1 Silverlight. È possibile usare questo token per autenticare gli utenti con Servizi mobili di Azure. In questa esercitazione si aggiungerà l'autenticazione con account Microsoft a un progetto esistente usando Live SDK. Dopo l'autenticazione e l'accesso, l'utente visualizza una schermata di benvenuto in cui sono indicati il nome utente e il relativo ID.

>[AZURE.NOTE]Questa esercitazione illustra i vantaggi offerti dall'uso dell'autenticazione gestita da client e di Live SDK. Ciò consente di semplificare l'autenticazione degli utenti che hanno già effettuato l'accesso al servizio mobile. È inoltre possibile richiedere ambiti aggiuntivi per consentire all'app di accedere anche a risorse come OneDrive. L'autenticazione gestita dal servizio offre un'esperienza più generalizzata e supporta più provider di autenticazione. Per altre informazioni sull'autenticazione gestita dal servizio, vedere l'argomento [Aggiungere l'autenticazione all'app](mobile-services-windows-phone-get-started-users.md).

Per completare questa esercitazione, è necessario disporre di:

+ [Live SDK]
+ Microsoft Visual Studio 2013 Update 3 o versione successiva.
+ È inoltre necessario completare l'esercitazione [Aggiungere Servizi mobili a un'app esistente].

##Registrare l'app per l'uso delle credenziali di un account Microsoft 

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

5. Aprire il file del progetto mainpage.xaml.cs e aggiungere le istruzioni using seguenti:

        using Microsoft.Live;      

6. Aggiungere il seguente frammento di codice alla classe MainPage:
	
        private LiveConnectSession session;
        private static string clientId = "<microsoft-account-client-id>";
        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            // Create the authentication client using the client ID of the registration.
            LiveAuthClient liveIdClient = new LiveAuthClient(clientId);

            while (session == null)
            {
                LiveLoginResult result = await liveIdClient.LoginAsync(new[] { "wl.basic" });
                if (result.Status == LiveConnectSessionStatus.Connected)
                {
                    session = result.Session;
                    LiveConnectClient client = new LiveConnectClient(result.Session);
                    LiveOperationResult meResult = await client.GetAsync("me");
                    MobileServiceUser loginResult = await App.MobileService
                        .LoginWithMicrosoftAccountAsync(result.Session.AuthenticationToken);

                    string title = string.Format("Welcome {0}!", meResult.Result["first_name"]);
                    var message = string.Format("You are now logged in - {0}", loginResult.UserId);
                    MessageBox.Show(message, title, MessageBoxButton.OK);
                }
                else
                {
                    session = null;
                    MessageBox.Show("You must log in.", "Login Required", MessageBoxButton.OK);
                }
            }
        }

    Verranno creati una variabile membro per archiviare la sessione Live Connect corrente e un metodo per gestire il processo di autenticazione. La classe LiveLoginResult contiene il token di autenticazione assegnato a Servizi mobili per l'autenticazione dell'utente.

7. Nel frammento di codice precedente, sostituire il segnaposto `<microsoft-account-client-id>` con l'ID client ottenuto dalla registrazione dell'account Microsoft per l'app.

5. Impostare come commento o eliminare la chiamata al metodo **RefreshTodoItems** nell'override del metodo **OnNavigatedTo** esistente.

	Ciò impedisce il caricamento dei dati prima dell'autenticazione dell'utente. Si aggiungerà quindi un pulsante per avviare la procedura di accesso.

6. Aggiungere il seguente frammento di codice alla classe MainPage:

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile service.
            await AuthenticateAsync();

            // Hide the login button and load items from the mobile service.
            this.ButtonLogin.Visibility = System.Windows.Visibility.Collapsed;
            RefreshTodoItems();
        }
		
7. Nel progetto dell'app aprire il file di progetto MainPage.xaml e aggiungere l'elemento **Button** seguente in **TitlePanel**, dopo l'elemento **TextBlock**:

		<Button Name="ButtonLogin" Click="ButtonLogin_Click" 
                        Visibility="Visible">Sign in</Button>
		
9. Premere F5 per eseguire l'app e accedere con l'account Microsoft.

   Dopo avere eseguito l'accesso, l'app dovrebbe funzionare senza errori e dovrebbe essere possibile eseguire query in Servizi mobili e aggiornare i dati.

A questo punto, qualsiasi utente autenticato da uno dei provider di identità attendibili può accedere alla tabella *TodoItem*. Per proteggere meglio i dati specifici dell'utente, è necessario implementare anche l'autorizzazione. A tale scopo si ottiene l'ID di un determinato utente, che può quindi essere usato per definire il livello di accesso che l'utente deve avere per una specifica risorsa.

## <a name="next-steps"> </a>Passaggi successivi

Nella prossima esercitazione, [Autorizzazione di utenti con script], il valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato verrà utilizzato per filtrare i dati restituiti da Servizi mobili. Per informazioni su come utilizzare altri provider di identità per l'autenticazione, vedere [Introduzione all'autenticazione].

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]: #next-steps

<!-- Images. -->


<!-- URLs. -->
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Aggiungere Servizi mobili a un'app esistente]: mobile-services-windows-phone-get-started-data.md
[Introduzione all'autenticazione]: mobile-services-windows-phone-get-started-users.md
[Autorizzazione di utenti con script]: ../mobile-services-windows-phone-authorize-users-in-scripts.md

[Azure Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=July15_HO4-->