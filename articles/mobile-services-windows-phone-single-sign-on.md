<properties  linkid="develop-mobile-tutorials-single-sign-on-windows-8-wp8" urlDisplayName="Authenticate with single sign-on" pageTitle="Authenticate your app with Live Connect (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to use Live Connect single sign-on in Azure Mobile Services from a Windows Phone application." metaCanonical="" services="" documentationCenter="Mobile" title="Authenticate your Windows Phone 8 app with Live Connect single sign-on" authors="glenga" solutions="" manager="" editor="" />

# Autenticazione dell'app per Windows Phone 8 con l'accesso Single Sign-On di Live Connect
<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/en-us/develop/mobile/tutorials/single-sign-on-windows-8-dotnet" title="Windows Store C#">Windows Store C#</a><a href="/en-us/develop/mobile/tutorials/single-sign-on-windows-8-js" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/en-us/develop/mobile/tutorials/single-sign-on-wp8/" title="Windows Phone" class="current">Windows Phone</a>
</div>	

In questo argomento viene illustrato come utilizzare l'accesso Single Sign-On di Live Connect per autenticare gli utenti in Servizi mobili di Azure da un'app per Windows Phone 8. Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite Live Connect. Dopo essere stato autenticato da Live Connect e avere eseguito l'accesso, l'utente visualizza una schermata di benvenuto in cui sono indicati il nome utente e il relativo ID.


**Nota**
In questa esercitazione vengono descritti i vantaggi offerti dall'ambiente di accesso Single Sign-On per le app per Windows Phone, che semplifica l'autenticazione degli utenti che hanno già effettuato l'accesso al servizio mobile. Per un ambiente di autenticazione più generalizzato che supporta più provider di autenticazione, vedere l'argomento [Introduzione all'autenticazione](/en-us/develop/mobile/tutorials/get-started-with-users-wp8/).




In questa esercitazione vengono descritte le operazioni di base per abilitare l'autenticazione Live Connect:

1.  [Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili](#register)
2.  [Limitazione delle autorizzazioni per la tabella agli utenti autenticati](#permissions)
3.  [Aggiunta dell'autenticazione all'app](#add-authentication)

Per completare questa esercitazione, è necessario disporre di:

* [Live SDK per Windows e Windows Phone][1]
* Microsoft Visual Studio 2012 Express per Windows Phone

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. È inoltre necessario completare prima l'esercitazione [Introduzione a Servizi mobili](/en-us/develop/mobile/tutorials/get-started-wp8).

<h2><a name="register"></a><span  class="short-header">Registrazione dell'app</span>Registrazione dell'app con Live Connect</h2>


Per poter autenticare gli utenti, è necessario registrare l'app presso il Centro sviluppatori Live Connect. È quindi necessario registrare il segreto client per l'integrazione di Live Connect con Servizi mobili.

1.  Accedere al [portale di gestione di Azure][2], fare clic su **Mobile Services** e quindi sul servizio mobile.
    
	![][4]

2.  Fare clic sulla scheda **Dashboard** e prendere nota del valore in **Site URL**.
    
	![][5]
    
	Questo valore verrà utilizzato per definire il dominio di reindirizzamento.

3.  Passare alla pagina [Applicazioni personali][3] nel Centro sviluppatori Live  Connect e, se necessario, accedere con il proprio account Microsoft.

4.  Fare clic su **Crea applicazione**, quindi digitare un nome in **Nome applicazione** e fare clic su **Accetto**.
    
	![][1] 

	L'applicazione verrà registrata con Live Connect.

5.  Fare clic sulla pagina **Impostazioni applicazione**, quindi su
    **Impostazioni API** e prendere nota dei valori di **ID client** e
    **Chiave privata client**.
    
	![][2]
    


	**Nota sulla sicurezza**
	Il segreto client è un'importante credenziale di sicurezza. Non condividere questo valore con altri né distribuirlo con l'app.





6.  In **Dominio di reindirizzamento** immettere l'URL del servizio  mobile indicato nel passaggio 2, fare clic su **Sì** in **App client  mobile** e quindi fare clic su **Salva**.

7.  Tornare al portale di gestione, fare clic sulla scheda **Identity**, immettere il segreto client ottenuto da Live Connect in **Client secret** e fare clic su **Save**.
    
	![][13]

Il servizio mobile e l'app sono ora configurati per funzionare con Live Connect.

<h2><a name="permissions"></a><span  class="short-header">Limitazione delle autorizzazioni</span>Limitazione delle autorizzazioni agli utenti autenticati</h2>


1.  Nel portale di gestione fare clic sulla scheda **Data** e quindi sulla tabella **TodoItem**.
    
	![][14]

2.  Fare clic sulla scheda **Permissions**, impostare tutte le autorizzazioni su **Only authenticated users** e quindi fare clic su **Save**. In questo modo, tutte le operazioni eseguite sulla tabella **TodoItem** richiederanno un utente autenticato. Questa impostazione semplifica inoltre gli script dell'esercitazione successiva, in quanto non sarà necessario considerare la possibilità di accesso da parte di utenti anonimi.
    
	![][15]

3.  In Visual Studio 2012 Express per Windows Phone aprire il progetto creato dopo avere completato l'esercitazione [Introduzione a Servizi mobili](/en-us/develop/mobile/tutorials/get-started-wp8).

4.  Premere F5 per eseguire questa app basata sul progetto di guida introduttiva. Verificare che venga generata un'eccezione con codice di stato 401 (non autorizzato).
    
	L'eccezione viene generata perché l'app tenta di accedere a Servizi mobili come utente non autenticato, mentre la tabella _TodoItem_ richiede ora l'autenticazione.

A questo punto, si aggiornerà l'app in modo che autentichi gli utenti con Live Connect prima di richiedere risorse al servizio mobile.

<h2><a name="add-authentication"></a><span  class="short-header">Aggiunta dell'autenticazione</span>Aggiunta dell'autenticazione all'app</h2>


1.  Scaricare e installare [Live SDK per Windows e Windows Phone][1].

2.  Nel menu **Progetto** in Visual Studio fare clic su **Aggiungi riferimento**, espandere **Assembly**, fare clic su **Estensioni**, selezionare **Microsoft.Live**, quindi fare clic su **OK**.
    
	![][16]

 Verrà aggiunto un riferimento a Live SDK al progetto. 1.  Aprire il file del progetto mainpage.xaml.cs e aggiungere le istruzioni using seguenti:
    
         using Microsoft.Live;      

2.  Aggiungere il seguente frammento di codice alla classe MainPage:
    
        private LiveConnectSession session;
        private async System.Threading.Tasks.Task Authenticate()
        {
            LiveAuthClient liveIdClient = new LiveAuthClient("<< INSERT CLIENT ID HERE >>");

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
    
    Verranno creati una variabile membro per archiviare la sessione Live Connect corrente e un metodo per gestire il processo di
    autenticazione.

3.  Aggiornare la stringa *<< INSERT CLIENT ID HERE >>* del  passaggio precedente con il valore dell'ID client generato al momento della registrazione dell'app con Live Connect.
    


	**Nota**
	In un'app per Windows Phone 8 viene creata un'istanza della classe**LiveAuthClient** passando il valore
	dell'URI dell'ID client al costruttore della classe. In un'[app di Windows Store](/en-us/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/) viene creata un'istanza della stessa classe passando l'URI del dominio di reindirizzamento.





4.  Eliminare o rimuovere i simboli di commento dall'override del metodo **OnNavigatedTo** esistente e sostituirlo con il metodo seguente che  gestisce l'evento **Loaded** per la pagina.
    
        async void MainPage_Loaded(object sender, RoutedEventArgs e)
        {
            await Authenticate();
            RefreshTodoItems();
        }

5.  Sostituire il costruttore MainPage con il codice seguente:
    
        // Constructor
        public MainPage()
        {
            InitializeComponent();
            this.Loaded += MainPage_Loaded;
        }

6.  Premere F5 per eseguire l'app e accedere a Live Connect con l'account Microsoft.
    
	Dopo avere eseguito l'accesso, l'app dovrebbe funzionare senza errori e dovrebbe essere possibile eseguire query in Servizi mobili e aggiornare i dati.

## <a name="next-steps"> </a>Passaggi successivi

Nella prossima esercitazione, [Autorizzazione di utenti con script](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-wp8), il valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato verrà utilizzato per filtrare i dati restituiti da Servizi mobili. Per informazioni su come utilizzare altri provider di identità per l'autenticazione, vedere [Introduzione all'autenticazione](/en-us/develop/mobile/tutorials/get-started-with-users-wp8).

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]:#next-steps

<!-- Images. -->
[1]: ./media/mobile-services-windows-phone-single-sign-on/mobile-services-live-connect-add-app.png
[2]: ./media/mobile-services-windows-phone-single-sign-on/mobile-live-connect-app-api-settings-mobile.png
[4]: ./media/mobile-services-windows-phone-single-sign-on/mobile-services-selection.png
[5]: ./media/mobile-services-windows-phone-single-sign-on/mobile-service-uri.png





[13]: ./media/mobile-services-windows-phone-single-sign-on/mobile-identity-tab-ma-only.png
[14]: ./media/mobile-services-windows-phone-single-sign-on/mobile-portal-data-tables.png
[15]: ./media/mobile-services-windows-phone-single-sign-on/mobile-portal-change-table-perms.png
[16]: ./media/mobile-services-windows-phone-single-sign-on/mobile-add-reference-live-wp8.png

<!-- URLs. -->
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows and Windows Phone]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started-wp8
[Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-wp8
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-wp8
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-wp8
[Authorize users with scripts]: /en-us/develop/mobile/tutorials/authorize-users-in-scripts-wp8

[Azure Management Portal]: https://manage.windowsazure.com/



[1]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[2]: https://manage.windowsazure.com/
[3]: http://go.microsoft.com/fwlink/p/?LinkId=262039