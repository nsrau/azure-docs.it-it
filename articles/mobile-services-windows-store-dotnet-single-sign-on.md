<properties linkid="develop-mobile-tutorials-single-sign-on-windows-8-dotnet" urlDisplayName="Authenticate with single sign-on" pageTitle="Authenticate your Windows Store app with Live Connect" metaKeywords="Azure Live Connect, Azure SSO, SSO Live Connect, mobile services sso, Windows Store app sso" description="Learn how to use Live Connect single sign-on in Azure Mobile Services from a Windows Store application." metaCanonical="" services="" documentationCenter="" title="Authenticate your Windows Store app with Live Connect single sign-on" authors="glenga" solutions="" manager="" editor="" />

Autenticazione dell'app di Windows Store con l'accesso Single Sign-On di Live Connect
=====================================================================================

[Windows Store C\#](/en-us/develop/mobile/tutorials/single-sign-on-windows-8-dotnet "Windows Store C#")[Windows Store JavaScript](/en-us/develop/mobile/tutorials/single-sign-on-windows-8-js "Windows Store JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/single-sign-on-wp8 "Windows Phone")

In questo argomento viene illustrato come utilizzare l'accesso Single Sign-On di Live Connect per autenticare gli utenti in Servizi mobili di Azure da un'app di Windows Store. Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite Live Connect. Dopo essere stato autenticato da Live Connect e avere eseguito l'accesso, l'utente visualizza una schermata di benvenuto in cui sono indicati il nome utente e il relativo ID.

**Nota**

In questa esercitazione vengono descritti i vantaggi offerti dall'ambiente di accesso Single Sign-On per le app di Windows Store, che semplifica l'autenticazione degli utenti che hanno già effettuato l'accesso al servizio mobile. Per un ambiente di autenticazione più generalizzato che supporta più provider di autenticazione, vedere l'argomento [Introduzione all'autenticazione](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet/).

In questa esercitazione vengono descritte le operazioni di base per abilitare l'autenticazione Live Connect:

1.  [Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili](#register)
2.  [Limitazione delle autorizzazioni per la tabella agli utenti autenticati](#permissions)
3.  [Aggiunta dell'autenticazione all'app](#add-authentication)

Per completare questa esercitazione, è necessario disporre di:

-   [Live SDK per Windows](http://go.microsoft.com/fwlink/p/?LinkId=262253)
-   Microsoft Visual Studio 2012 Express per Windows 8 RC o versione successiva

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. È inoltre necessario completare prima l'esercitazione [Introduzione a Servizi mobili](/en-us/develop/mobile/tutorials/get-started).

Registrazione dell'appRegistrazione dell'app di Windows Store
-------------------------------------------------------------

Per poter autenticare gli utenti, è necessario inviare l'app a Windows Store. È quindi necessario registrare il segreto client per l'integrazione di Live Connect con Servizi mobili.

1.  Se l'app non è ancora stata registrata, passare alla pagina relativa all'[invio di app](http://go.microsoft.com/fwlink/p/?LinkID=266582) nel Centro per sviluppatori di app di Windows Store, accedere con il proprio account Microsoft e quindi fare clic su **App name**.

     ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-submit-win8-app.png)

2.  Immettere un nome per l'app in **Nome app**, fare clic su **Riserva nome applicazione** e quindi su **Salva**.

     ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-win8-app-name.png)

    Verrà creata una nuova registrazione a Windows Store per l'app.

3.  In Visual Studio 2012 Express per Windows 8 aprire il progetto creato dopo aver completato l'esercitazione [Introduzione a Servizi mobili](/en-us/develop/mobile/tutorials/get-started).

4.  In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto, scegliere **Store** e quindi fare clic su **Associa applicazione a Store**.

   ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-store-association.png) 

    Verrà visualizzata la procedura guidata **Associa l'applicazione con Windows Store**.

1.  Nella procedura guidata fare clic su **Accedi** e quindi accedere con il proprio account Microsoft.

2.  Selezionare l'app registrata nel passaggio 2, fare clic su **Avanti** e quindi su **Associa**.

     ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-select-app-name.png)

     Le necessarie informazioni di registrazione a Windows Store verranno aggiunte al manifesto dell'applicazione.    

3.  Accedere al [portale di gestione di Azure](https://manage.windowsazure.com/), fare clic su **Mobile Services** e quindi sul servizio mobile.

     ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-services-selection.png)

4.  Fare clic sulla scheda **Dashboard** e prendere nota del valore in **Site URL**.

     ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-service-uri.png)

    Questo valore verrà utilizzato per definire il dominio di reindirizzamento.

5.  Passare alla pagina [Applicazioni personali](http://go.microsoft.com/fwlink/p/?LinkId=262039) nel Centro sviluppatori Live Connect e fare clic sull'app nell'elenco **Applicazioni personali**.

     ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-live-connect-apps-list.png) 

6.  Fare clic sulla pagina **Edit settings**, quindi su **API Settings**e prendere nota dei valori di **Client ID** e **Client secret**.

   ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-live-connect-app-api-settings.png)

    <div class="dev-callout"><b>Nota sulla sicurezza</b>
    <p>Il segreto client è un'importante credenziale di sicurezza. Non condividere questo valore con altri né distribuirlo con l'app.</p>
    </div>

1.  In **Dominio di reindirizzamento** immettere l'URL del servizio mobile indicato nel passaggio 8 e quindi fare clic su **Salva**.

2.  Tornare al portale di gestione, fare clic sulla scheda **Identity**, immettere il segreto ottenuto da Windows Store in **Client secret** e fare clic su **Save**.

   ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-identity-tab-ma-only.png)

Il servizio mobile e l'app sono ora configurati per funzionare con Live Connect.

Limitazione delle autorizzazioniLimitazione delle autorizzazioni agli utenti autenticati
----------------------------------------------------------------------------------------

1.  Nel portale di gestione fare clic sulla scheda **Data** e quindi sulla tabella **TodoItem**.

     ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-portal-data-tables.png)

2.  Fare clic sulla scheda **Permissions**, impostare tutte le autorizzazioni su **Only authenticated users** e quindi fare clic su **Save**. In questo modo, tutte le operazioni eseguite sulla tabella **TodoItem** richiederanno un utente autenticato. Questa impostazione semplifica inoltre gli script dell'esercitazione successiva, in quanto non sarà necessario considerare la possibilità di accesso da parte di utenti anonimi.

     ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-portal-change-table-perms.png)

3.  In Visual Studio 2012 Express per Windows 8 aprire il progetto creato dopo aver completato l'esercitazione [Introduzione a Servizi mobili](/en-us/develop/mobile/tutorials/get-started).

4.  Premere F5 per eseguire questa app basata sul progetto di guida introduttiva. Verificare che venga generata un'eccezione con codice di stato 401 (non autorizzato).

     L'eccezione viene generata perché l'app tenta di accedere a Servizi mobili come utente non autenticato, mentre la tabella _TodoItem_ richiede ora l'autenticazione.

A questo punto, si aggiornerà l'app in modo che autentichi gli utenti con Live Connect prima di richiedere risorse al servizio mobile.

Aggiunta dell'autenticazioneAggiunta dell'autenticazione all'app
----------------------------------------------------------------

1.  Scaricare e installare [Live SDK per Windows](http://go.microsoft.com/fwlink/p/?LinkId=262253).

2.  Nel menu **Progetto** in Visual Studio fare clic su **Aggiungi riferimento**, espandere **Windows**, fare clic su **Estensioni**, selezionare **Live SDK**, quindi fare clic su **OK**.

   ![](./media/mobile-services-windows-store-dotnet-single-sign-on/mobile-add-reference-live-dotnet.png) 
   
   Verrà aggiunto un riferimento a Live SDK al progetto.

1.  Aprire il file del progetto mainpage.xaml.cs e aggiungere l'istruzione using seguente:

         using Microsoft.Live;        

2.  Aggiungere il seguente frammento di codice alla classe MainPage:

         private LiveConnectSession session;
         private async System.Threading.Tasks.Task Authenticate()
         {
             LiveAuthClient liveIdClient = new LiveAuthClient("<< INSERT REDIRECT DOMAIN HERE >>");

             while (session == null)
             {
                 // Force a logout to make it easier to test with multiple Microsoft Accounts
                 if (liveIdClient.CanLogout)
                     liveIdClient.Logout();

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

    **Nota**

    Questo codice forza una disconnessione, ove possibile, per garantire che all'utente vengano richieste le credenziali ogni volta che l'applicazione viene eseguita, al fine di semplificare il test dell'applicazione con account Microsoft diversi e verificare che l'autenticazione funzioni correttamente. Questo meccanismo funziona solo se l'utente che ha eseguito l'accesso non dispone di un account Microsoft connesso.

3.  Aggiornare la stringa *&lt;&lt; INSERT REDIRECT DOMAIN HERE \>\>* del passaggio precedente con il dominio di reindirizzamento specificato durante la configurazione dell'app in Live Connect, nel formato **https://*nome-servizio*.azure-mobile.net/**.

    **Nota**

    In un'app di Windows Store viene creata un'istanza della classe **LiveAuthClient** passando il valore dell'URI del dominio di reindirizzamento al costruttore della classe. In un'[app per Windows Phone 8](/en-us/develop/mobile/tutorials/single-sign-on-wp8/) viene creata un'istanza della stessa classe passando l'ID client.

4.  Sostituire il gestore di eventi **OnNavigatedTo** esistente con il gestore che chiama il nuovo metodo **Authenticate**:

         protected override async void OnNavigatedTo(NavigationEventArgs e)
         {
             await Authenticate();
             RefreshTodoItems();
         }

5.  Premere F5 per eseguire l'app e accedere a Live Connect con l'account Microsoft.

    Dopo avere eseguito l'accesso, l'app dovrebbe funzionare senza errori e dovrebbe essere possibile eseguire query in Servizi mobili e aggiornare i dati.

Passaggi successivi
-------------------

Nella prossima esercitazione, [Autorizzazione di utenti con script](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-dotnet), il valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato verrà utilizzato per filtrare i dati restituiti da Servizi mobili. Per informazioni su come utilizzare altri provider di identità per l'autenticazione, vedere [Introduzione all'autenticazione](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet). Per ulteriori informazioni su come utilizzare Servizi mobili con .NET, vedere [Riferimento per i concetti e le procedure di .NET per Servizi mobili](/en-us/develop/mobile/how-to-guides/work-with-net-client-library)

