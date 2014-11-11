<properties linkid="develop-mobile-tutorials-single-sign-on-windows-8-wp8" urlDisplayName="Authenticate with single sign-on" pageTitle="Authenticate your app with Live Connect (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to use Live Connect single sign-on in Azure Mobile Services from a Windows Phone application." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Authenticate your Windows Phone 8 app with Live Connect single sign-on" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Autenticazione dell'app per Windows Phone 8 con l'accesso Single Sign-On di Live Connect

<div class="dev-center-tutorial-selector sublanding">

[Windows Store C#][Windows Store C#][Windows Store JavaScript][Windows Store JavaScript][Windows Phone][Windows Phone]

</div>

In questo argomento viene illustrato come utilizzare l'accesso Single Sign-On di Live Connect per autenticare gli utenti in Servizi mobili di Azure da un'app per Windows Phone 8. Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite Live Connect. Dopo essere stato autenticato da Live Connect e avere eseguito l'accesso, l'utente visualizza una schermata di benvenuto in cui sono indicati il nome utente e il relativo ID.

<div class="dev-callout">

**Nota**
In questa esercitazione vengono descritti i vantaggi offerti dall'ambiente di accesso Single Sign-On per le app per Windows Phone, che semplifica l'autenticazione degli utenti che hanno già effettuato l'accesso al servizio mobile. Per un ambiente di autenticazione più generalizzato che supporta più provider di autenticazione, vedere l'argomento [Introduzione all'autenticazione][Introduzione all'autenticazione].

</div>

In questa esercitazione vengono descritte le operazioni di base per abilitare l'autenticazione Live Connect:

1.  [Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili][Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili]
2.  [Limitazione delle autorizzazioni per la tabella agli utenti autenticati][Limitazione delle autorizzazioni per la tabella agli utenti autenticati]
3.  [Aggiunta dell'autenticazione all'app][Aggiunta dell'autenticazione all'app]

Per completare questa esercitazione, è necessario disporre di:

-   [Live SDK per Windows e Windows Phone][Live SDK per Windows e Windows Phone]
-   Microsoft Visual Studio 2012 Express per Windows Phone

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. È inoltre necessario completare prima l'esercitazione [Introduzione a Servizi mobili][Introduzione a Servizi mobili].

## <a name="register"></a><span class="short-header">Registrazione dell'app</span>Registrazione dell'app con Live Connect

Per poter autenticare gli utenti, è necessario registrare l'app presso il Centro sviluppatori Live Connect. È quindi necessario registrare il segreto client per l'integrazione di Live Connect con Servizi mobili.

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure], fare clic su **Mobile Services** e quindi sul servizio mobile.

    ![][0]

2.  Fare clic sulla scheda **Dashboard** e prendere nota del valore in **Site URL**.

    ![][1]

    Questo valore verrà utilizzato per definire il dominio di reindirizzamento.

3.  Passare alla pagina [Applicazioni personali][Applicazioni personali] nel Centro sviluppatori Live Connect e, se necessario, accedere con il proprio account Microsoft.

4.  Fare clic su **Crea applicazione**, quindi digitare un nome in **Nome applicazione** e fare clic su **Accetto**.

    ![][2]

    L'applicazione verrà registrata con Live Connect.

5.  Fare clic sulla pagina **Impostazioni applicazione**, quindi su **Impostazioni API** e prendere nota dei valori di **ID client** e **Chiave privata client**.

    ![][3]

    <div class="dev-callout">

    **Nota sulla sicurezza**
    Il segreto client è un'importante credenziale di sicurezza. Non condividere questo valore con altri né distribuirlo con l'app.

    </div>

6.  In **Dominio di reindirizzamento** immettere l'URL del servizio mobile indicato nel passaggio 2, fare clic su **Sì** in **App client mobile** e quindi fare clic su **Salva**.

7.  Tornare al portale di gestione, fare clic sulla scheda **Identity**, immettere il segreto client ottenuto da Live Connect in **Client secret** e fare clic su **Save**.

    ![][4]

Il servizio mobile e l'app sono ora configurati per funzionare con Live Connect.

## <a name="permissions"></a><span class="short-header">Limitazione delle autorizzazioni</span>Limitazione delle autorizzazioni agli utenti autenticati

1.  Nel portale di gestione fare clic sulla scheda **Data** e quindi sulla tabella **TodoItem**.

    ![][5]

2.  Fare clic sulla scheda **Permissions**, impostare tutte le autorizzazioni su **Only authenticated users** e quindi fare clic su **Save**. In questo modo, tutte le operazioni eseguite sulla tabella **TodoItem** richiederanno un utente autenticato. Questa impostazione semplifica inoltre gli script dell'esercitazione successiva, in quanto non sarà necessario considerare la possibilità di accesso da parte di utenti anonimi.

    ![][6]

3.  In Visual Studio 2012 Express per Windows Phone aprire il progetto creato dopo avere completato l'esercitazione [Introduzione a Servizi mobili][Introduzione a Servizi mobili].

4.  Premere F5 per eseguire questa app basata sul progetto di guida introduttiva. Verificare che venga generata un'eccezione con codice di stato 401 (non autorizzato).

    L'eccezione viene generata perché l'app tenta di accedere a Servizi mobili come utente non autenticato, mentre la tabella *TodoItem* richiede ora l'autenticazione.

A questo punto, si aggiornerà l'app in modo che autentichi gli utenti con Live Connect prima di richiedere risorse al servizio mobile.

## <a name="add-authentication"></a><span class="short-header">Aggiunta dell'autenticazione</span>Aggiunta dell'autenticazione all'app

1.  Scaricare e installare [Live SDK per Windows e Windows Phone][Live SDK per Windows e Windows Phone].

2.  Nel menu **Progetto** in Visual Studio fare clic su **Aggiungi riferimento**, espandere **Assembly**, fare clic su **Estensioni**, selezionare **Microsoft.Live**, quindi fare clic su **OK**.

    ![][7]

    Verrà aggiunto un riferimento a Live SDK al progetto.

3.  Aprire il file del progetto mainpage.xaml.cs e aggiungere le istruzioni using seguenti:

        using Microsoft.Live;      

4.  Aggiungere il seguente frammento di codice alla classe MainPage:

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

    Verranno creati una variabile membro per archiviare la sessione Live Connect corrente e un metodo per gestire il processo di autenticazione.

5.  Aggiornare la stringa *\<\< INSERT CLIENT ID HERE \>\>* del passaggio precedente con il valore dell'ID client generato al momento della registrazione dell'app con Live Connect.

    <div class="dev-callout">

    **Nota**
    In un'app per Windows Phone 8 viene creata un'istanza della classe **LiveAuthClient** passando il valore dell'URI dell'ID client al costruttore della classe. In un'[app di Windows Store][app di Windows Store] viene creata un'istanza della stessa classe passando l'URI del dominio di reindirizzamento.

    </div>

6.  Eliminare o rimuovere i simboli di commento dall'override del metodo **OnNavigatedTo** esistente e sostituirlo con il metodo seguente che gestisce l'evento **Loaded** per la pagina.

        async void MainPage_Loaded(object sender, RoutedEventArgs e)
        {
            await Authenticate();
            RefreshTodoItems();
        }

    Questo metodo chiama il nuovo metodo **Authenticate**.

7.  Sostituire il costruttore MainPage con il codice seguente:

        // Constructor
        public MainPage()
        {
            InitializeComponent();
            this.Loaded += MainPage_Loaded;
        }

    Questo costruttore registra inoltre il gestore per l'evento Loaded.

8.  Premere F5 per eseguire l'app e accedere a Live Connect con l'account Microsoft.

    Dopo avere eseguito l'accesso, l'app dovrebbe funzionare senza errori e dovrebbe essere possibile eseguire query in Servizi mobili e aggiornare i dati.

## <a name="next-steps"> </a>Passaggi successivi

Nella prossima esercitazione, [Autorizzazione di utenti con script][Autorizzazione di utenti con script], il valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato verrà utilizzato per filtrare i dati restituiti da Servizi mobili. Per informazioni su come utilizzare altri provider di identità per l'autenticazione, vedere [Introduzione all'autenticazione][8].



  [Windows Store C#]: /it-it/develop/mobile/tutorials/single-sign-on-windows-8-dotnet "Windows Store C#"
  [Windows Store JavaScript]: /it-it/develop/mobile/tutorials/single-sign-on-windows-8-js "Windows Store JavaScript"
  [Windows Phone]: /it-it/develop/mobile/tutorials/single-sign-on-wp8/ "Windows Phone"
  [Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-wp8/
  [Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili]: #register
  [Limitazione delle autorizzazioni per la tabella agli utenti autenticati]: #permissions
  [Aggiunta dell'autenticazione all'app]: #add-authentication
  [Live SDK per Windows e Windows Phone]: http://go.microsoft.com/fwlink/p/?LinkId=262253
  [Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started-wp8
  [portale di gestione di Azure]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-windows-phone-single-sign-on/mobile-services-selection.png
  [1]: ./media/mobile-services-windows-phone-single-sign-on/mobile-service-uri.png
  [Applicazioni personali]: http://go.microsoft.com/fwlink/p/?LinkId=262039
  [2]: ./media/mobile-services-windows-phone-single-sign-on/mobile-services-live-connect-add-app.png
  [3]: ./media/mobile-services-windows-phone-single-sign-on/mobile-live-connect-app-api-settings-mobile.png
  [4]: ./media/mobile-services-windows-phone-single-sign-on/mobile-identity-tab-ma-only.png
  [5]: ./media/mobile-services-windows-phone-single-sign-on/mobile-portal-data-tables.png
  [6]: ./media/mobile-services-windows-phone-single-sign-on/mobile-portal-change-table-perms.png
  [7]: ./media/mobile-services-windows-phone-single-sign-on/mobile-add-reference-live-wp8.png
  [app di Windows Store]: /it-it/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/
  [Autorizzazione di utenti con script]: /it-it/develop/mobile/tutorials/authorize-users-in-scripts-wp8
  [8]: /it-it/develop/mobile/tutorials/get-started-with-users-wp8
