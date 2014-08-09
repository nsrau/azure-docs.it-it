<properties linkid="develop-mobile-tutorials-get-started-with-users-xamarin-ios" urlDisplayName="Get Started with Authentication (Xamarin.iOS)" pageTitle="Get started with authentication (Xamarin.iOS) - Mobile Services" metaKeywords="Azure registering application, Azure authentication, application authenticate, authenticate mobile services, Mobile Services Xamarin.iOS" description="Learn how to use authentication in your Azure Mobile Services app for Xamarin.iOS." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" />

Introduzione all'autenticazione in Servizi mobili
=================================================

[Windows Store C\#](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet "Windows Store C#")[Windows Store JavaScript](/en-us/develop/mobile/tutorials/get-started-with-users-js "Windows Store JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-users-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-users-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-users-android "Android")[HTML](/en-us/develop/mobile/tutorials/get-started-with-users-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-android "Xamarin.Android")

In questo argomento viene illustrato come autenticare gli utenti in Servizi mobili di Azure dalla propria app. Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite un provider di identità supportato da Servizi mobili. In seguito all'autenticazione e all'autorizzazione di Servizi mobili, viene visualizzato il valore dell'ID utente.

In questa esercitazione vengono descritte le operazioni di base per abilitare l'autenticazione in un'app:

1.  [Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili](#register)
2.  [Limitazione delle autorizzazioni per la tabella agli utenti autenticati](#permissions)
3.  [Aggiunta dell'autenticazione all'app](#add-authentication)

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. È inoltre necessario completare prima l'esercitazione [Introduzione a Servizi mobili](/en-us/develop/mobile/tutorials/get-started-xamarin-ios).

Per completare questa esercitazione, è necessario disporre di [Xamarin.iOS], XCode 5.0 e iOS 5.0 o versioni successive.

Registrazione dell'appRegistrazione dell'app per l'autenticazione e configurazione di Servizi mobili
----------------------------------------------------------------------------------------------------

Per poter autenticare gli utenti, è necessario registrare la propria app presso un provider di identità. È quindi necessario registrare il segreto client generato dal provider con Servizi mobili.

1.  Accedere al [portale di gestione di Azure](https://manage.windowsazure.com/), fare clic su **Mobile Services** e quindi sul servizio mobile.

    ![][4]

2.  Fare clic sulla scheda **Dashboard** e prendere nota del valore in **Site URL**.

    ![][5]

    Potrebbe essere necessario fornire questo valore al provider di identità durante la registrazione dell'app.

3.  Scegliere un provider di identità supportato nell'elenco riportato di seguito e seguire la procedura per registrare l'app con tale provider:

-   [Account Microsoft](/en-us/develop/mobile/how-to-guides/register-for-microsoft-authentication/)
-   [Account di accesso di Facebook](/en-us/develop/mobile/how-to-guides/register-for-facebook-authentication/)
-   [Account di accesso di Twitter](/en-us/develop/mobile/how-to-guides/register-for-twitter-authentication/)
-   [Account di accesso di Google](/en-us/develop/mobile/how-to-guides/register-for-google-authentication/)
-   [Azure Active Directory](/it-it/documentation/articles/mobile-services-how-to-register-active-directory-authentication/)

    Non dimenticare di prendere nota dei valori dell'identità e del segreto client generati dal provider.

    **Nota sulla sicurezza**

    Il segreto generato dal provider è una credenziale di sicurezza importante. Non condividere questo valore con altri né distribuirlo con l'app.

1.  Tornare al portale di gestione, fare clic sulla scheda **Identity**, immettere l'identificatore dell'app e il segreto condiviso forniti dal provider di identità e fare clic su **Save**.

    ![][13]

Il servizio mobile e l'app sono ora configurati per funzionare con il provider di autenticazione scelto.

Limitazione delle autorizzazioniLimitazione delle autorizzazioni agli utenti autenticati
----------------------------------------------------------------------------------------

1.  Nel portale di gestione fare clic sulla scheda **Data** e quindi sulla tabella **TodoItem**.

    ![][14]

2.  Fare clic sulla scheda **Permissions**, impostare tutte le autorizzazioni su **Only authenticated users** e quindi fare clic su **Save**. In questo modo, tutte le operazioni eseguite sulla tabella **TodoItem** richiederanno un utente autenticato. Questa impostazione semplifica inoltre gli script dell'esercitazione successiva, in quanto non sarà necessario considerare la possibilità di accesso da parte di utenti anonimi.

    ![][15]

3.  In Xcode aprire il progetto creato dopo avere completato l'esercitazione [Introduzione a Servizi mobili](/en-us/develop/mobile/tutorials/get-started-xamarin-ios).

4.  Premere il pulsante **Run** per compilare il progetto e avviare l'app nell'emulatore iPhone e verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (Non autorizzato).

    L'eccezione non gestita viene generata perché l'app tenta di accedere a Servizi mobili come utente non autenticato, mentre la tabella _TodoItem_ richiede ora l'autenticazione.

A questo punto, si aggiornerà l'app in modo che autentichi gli utenti prima di richiedere risorse al servizio mobile.

Aggiunta dell'autenticazioneAggiunta dell'autenticazione all'app
----------------------------------------------------------------

1.  Aprire il file del progetto **TodoService** e aggiungere le variabili seguenti:

         // Mobile Service logged in user
         private MobileServiceUser user; 
         public MobileServiceUser User { get { return user; } }

2.  Aggiungere quindi a **TodoService** un nuovo metodo denominato **Authenticate** definito come segue:

         private async Task Authenticate(UIViewController view)
         {
             try
             {
                 user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.MicrosoftAccount);
             }
             catch (Exception ex)
             {
                 Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
             }
         }

    **Nota**

    Se si utilizza un provider di identità diverso da un account Microsoft, sostituire il valore passato al metodo **LoginAsync** riportato in precedenza con uno dei seguenti: *Facebook*, *Twitter* o *Google*.

3.  Spostare la richiesta per la tabella **TodoItem** dal costruttore **TodoService** a un nuovo metodo denominato **CreateTable**:

         private async Task CreateTable()
         {
             // Create an MSTable instance to allow us to work with the TodoItem table
             todoTable = client.GetTable<TodoItem>();
         }

4.  Creare un nuovo metodo pubblico asincrono denominato **LoginAndGetData** definito come segue:

         public async Task LoginAndGetData(UIViewController view)
         {
             await Authenticate(view);
             await CreateTable();
         }

5.  In **TodoListViewController** eseguire l'override del metodo **ViewDidAppear** e definirlo come illustrato di seguito. Questo consente l'accesso dell'utente se **TodoService** non dispone già di un handle per l'utente:

         public override async void ViewDidAppear(bool animated)
         {
             base.ViewDidAppear(animated);

             if (TodoService.DefaultService.User == null)
             {
                 await TodoService.DefaultService.LoginAndGetData(this);
             }

             if (TodoService.DefaultService.User == null)
             {
                 // TODO:: show error
                 return;
             } 
                    
             RefreshAsync();
         }

6.  Rimuovere la chiamata originale a **RefreshAsync** da **TodoListViewController.ViewDidLoad**.

7.  Premere il pulsante **Run** per creare il progetto, avviare l'app nell'emulatore iPhone e quindi accedere con il provider di identità scelto.

    Dopo avere eseguito l'accesso, l'app dovrebbe funzionare senza errori e dovrebbe essere possibile eseguire query in Servizi mobili e aggiornare i dati.

Download dell'esempio completato
--------------------------------

Scaricare il [progetto di esempio completato](http://go.microsoft.com/fwlink/p/?LinkId=331328). Assicurarsi di aggiornare le variabili **applicationURL** e **applicationKey** con le proprie impostazioni di Azure.

Passaggi successivi
-------------------

Nella prossima esercitazione, [Autorizzazione di utenti con script](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios), il valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato verrà utilizzato per filtrare i dati restituiti da Servizi mobili.



[4]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-services-selection.png
[5]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-service-uri.png
[13]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-identity-tab.png
[14]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-portal-data-tables.png
[15]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-portal-change-table-perms.png