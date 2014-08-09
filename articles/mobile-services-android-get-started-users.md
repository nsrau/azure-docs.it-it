<properties linkid="develop-mobile-tutorials-get-started-with-users-android" urlDisplayName="Get Started with Authentication" pageTitle="Get started with authentication (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Android app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="" solutions="" manager="" editor="" />

Introduzione all'autenticazione in Servizi mobili
=================================================

[Windows Store C#](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet "Windows Store C#")[Windows Store JavaScript](/en-us/develop/mobile/tutorials/get-started-with-users-js "Windows Store JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-users-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-users-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-users-android "Android")[HTML](/en-us/develop/mobile/tutorials/get-started-with-users-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-android "Xamarin.Android")

In questo argomento viene illustrato come autenticare gli utenti in Servizi mobili di Azure dalla propria app. Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite un provider di identità supportato da Servizi mobili. In seguito all'autenticazione e all'autorizzazione di Servizi mobili, viene visualizzato il valore dell'ID utente.

[guarda l'esercitazione](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services) [Riproduci video](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services)10:42

In questa esercitazione vengono descritte le operazioni di base per abilitare l'autenticazione in un'app:

1.  [Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili](#register)
2.  [Limitazione delle autorizzazioni per la tabella agli utenti autenticati](#permissions)
3.  [Aggiunta dell'autenticazione all'app](#add-authentication)

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. È inoltre necessario completare prima l'esercitazione [Introduzione a Servizi mobili](/en-us/develop/mobile/tutorials/get-started-android).

Per completare questa esercitazione, è necessario disporre di Eclipse e Android 4.2 o versione successiva.

Registrazione dell'appRegistrazione dell'app per l'autenticazione e configurazione di Servizi mobili
----------------------------------------------------------------------------------------------------

Per poter autenticare gli utenti, è necessario registrare la propria app presso un provider di identità. È quindi necessario registrare il segreto client generato dal provider con Servizi mobili.

1.  Accedere al [portale di gestione di Azure](https://manage.windowsazure.com/), fare clic su **Mobile Services** e quindi sul servizio mobile.

   	![](./media/mobile-services-android-get-started-users/mobile-services-selection.png)

2.  Fare clic sulla scheda **Dashboard** e prendere nota del valore in **Site URL**.

   	![](./media/mobile-services-android-get-started-users/mobile-service-uri.png)

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

   	![](./media/mobile-services-android-get-started-users/mobile-identity-tab.png)

Il servizio mobile e l'app sono ora configurati per funzionare con il provider di autenticazione scelto.

Limitazione delle autorizzazioniLimitazione delle autorizzazioni agli utenti autenticati
----------------------------------------------------------------------------------------

1.  Nel portale di gestione fare clic sulla scheda **Data** e quindi sulla tabella **TodoItem**.

   	![](./media/mobile-services-android-get-started-users/mobile-portal-data-tables.png)

2.  Fare clic sulla scheda **Permissions**, impostare tutte le autorizzazioni su **Only authenticated users** e quindi fare clic su **Save**. In questo modo, tutte le operazioni eseguite sulla tabella **TodoItem** richiederanno un utente autenticato. Questa impostazione semplifica inoltre gli script dell'esercitazione successiva, in quanto non sarà necessario considerare la possibilità di accesso da parte di utenti anonimi.

   	![](./media/mobile-services-android-get-started-users/mobile-portal-change-table-perms.png)	

3.  In Eclipse aprire il progetto creato dopo aver completato l'esercitazione [Introduzione a Servizi mobili](/en-us/develop/mobile/tutorials/get-started-android).

4.  Nel menu **Run** fare clic su **Run** per eseguire l'app e verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (Non autorizzato).

    L'eccezione non gestita viene generata perché l'app tenta di accedere a Servizi mobili come utente non autenticato, mentre la tabella *TodoItem* richiede ora l'autenticazione.

A questo punto, si aggiornerà l'app in modo che autentichi gli utenti prima di richiedere risorse al servizio mobile.

Aggiunta dell'autenticazioneAggiunta dell'autenticazione all'app
----------------------------------------------------------------

1.  In Package Explorer di Eclipse aprire il file ToDoActivity.java e aggiungere le istruzioni import seguenti.

         import com.microsoft.windowsazure.mobileservices.MobileServiceUser;
         import com.microsoft.windowsazure.mobileservices.MobileServiceAuthenticationProvider;
         import com.microsoft.windowsazure.mobileservices.UserAuthenticationCallback;

2.  Aggiungere il metodo seguente alla classe **ToDoActivity**:

         private void authenticate() {
            
             // Login using the Google provider.
             mClient.login(MobileServiceAuthenticationProvider.Google,
                     new UserAuthenticationCallback() {

                         @Override
                         public void onCompleted(MobileServiceUser user,
                                 Exception exception, ServiceFilterResponse response) {

                             if (exception == null) {
                                 createAndShowDialog(String.format(
                                                 "You are now logged in - %1$2s",
                                                 user.getUserId()), "Success");
                                 createTable();
                             } else {
                                 createAndShowDialog("You must log in. Login Required", "Error");
                             }
                         }
                     });
         }

    In questo modo viene creato un nuovo metodo per gestire il processo di autenticazione. L'utente viene autenticato tramite un account di accesso di Google. Viene visualizzata una finestra di dialogo che riporta l'ID dell'utente autenticato. Senza un'autenticazione positiva non è possibile procedere.

    **Nota**

    Se si utilizza un provider di identità diverso da Google, sostituire il valore passato al metodo di **accesso** riportato in precedenza con uno dei seguenti: *MicrosoftAccount*, *Facebook* o *Twitter*.

3.  Nel metodo **onCreate** aggiungere la riga di codice seguente dopo il codice che crea l'istanza dell'oggetto `MobileServiceClient`.

         authenticate();

    Questa chiamata avvia il processo di autenticazione.

4.  Spostare il codice rimanente dopo `authenticate();` nel metodo **onCreate** in un nuovo metodo **createTable**, simile al seguente:

         private void createTable() {

             // Get the Mobile Service Table instance to use
             mToDoTable = mClient.getTable(ToDoItem.class);

             mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);

             // Create an adapter to bind the items with the view
             mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
             ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
             listViewToDo.setAdapter(mAdapter);

             // Load the items from the Mobile Service
             refreshItemsFromTable();
         }

5.  Nel menu **Run** fare clic su **Run** per avviare l'app e accedere con il provider di identità scelto.

   	Dopo avere eseguito l'accesso, l'app dovrebbe funzionare senza errori e dovrebbe essere possibile eseguire query in Servizi mobili e aggiornare i dati.

Passaggi successivi
-------------------

Nella prossima esercitazione, [Autorizzazione di utenti con script](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-android), il valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato verrà utilizzato per filtrare i dati restituiti da Servizi mobili.

