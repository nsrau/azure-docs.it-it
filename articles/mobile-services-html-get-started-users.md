<properties linkid="develop-mobile-tutorials-get-started-with-users-html" urlDisplayName="Get Started with Authentication (HTML5)" pageTitle="Get started with authentication (HTML 5) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your HTML app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

Introduzione all'autenticazione in Servizi mobili
=================================================

[Windows Store C\#](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet "Windows Store C#")[Windows Store JavaScript](/en-us/develop/mobile/tutorials/get-started-with-users-js "Windows Store JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-users-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-users-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-users-android "Android")[HTML](/en-us/develop/mobile/tutorials/get-started-with-users-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-android "Xamarin.Android")

In questo argomento viene illustrato come autenticare gli utenti in Servizi mobili di Azure dalla propria app HTML. Nell'esercitazione verrà aggiunta l'autenticazione al progetto di guida introduttiva tramite un provider di identità supportato da Servizi mobili. In seguito all'autenticazione e all'autorizzazione di Servizi mobili, viene visualizzato il valore dell'ID utente.

In questa esercitazione vengono descritte le operazioni di base per abilitare l'autenticazione in un'app:

1.  [Registrazione dell'app per l'autenticazione e configurazione di Servizi mobili](#register)
2.  [Limitazione delle autorizzazioni per la tabella agli utenti autenticati](#permissions)
3.  [Aggiunta dell'autenticazione all'app](#add-authentication)

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. È inoltre necessario completare prima l'esercitazione [Introduzione a Servizi mobili](/en-us/develop/mobile/tutorials/get-started-html).

Registrazione dell'appRegistrazione dell'app per l'autenticazione e configurazione di Servizi mobili
----------------------------------------------------------------------------------------------------

Per poter autenticare gli utenti, è necessario registrare la propria app presso un provider di identità. È quindi necessario registrare il segreto client generato dal provider con Servizi mobili.

1.  Accedere al [portale di gestione di Azure](https://manage.windowsazure.com/), fare clic su **Mobile Services** e quindi sul servizio mobile.

     ![](./media/mobile-services-html-get-started-users/mobile-services-selection.png)

2.  Fare clic sulla scheda **Dashboard** e prendere nota del valore in **Mobile Service URL**.

     ![](./media/mobile-services-html-get-started-users/mobile-service-uri.png)

    Potrebbe essere necessario fornire questo valore al provider di identità durante la registrazione dell'app.

3.  Scegliere un provider di identità supportato nell'elenco riportato di seguito e seguire la procedura per registrare l'app con tale provider:

	-   [Account Microsoft](/en-us/develop/mobile/how-to-guides/register-for-microsoft-authentication/)
	-   [Account di accesso di Facebook](/en-us/develop/mobile/how-to-guides/register-for-facebook-authentication/)
	-   [Account di accesso di Twitter](/en-us/develop/mobile/how-to-guides/register-for-twitter-authentication/)
	-   [Account di accesso di Google](/en-us/develop/mobile/how-to-guides/register-for-google-authentication/)
	-   [Azure Active Directory](/en-us/documentation/articles/mobile-services-how-to-register-active-directory-authentication/)

    	Non dimenticare di prendere nota dei valori dell'identità e del segreto client generati dal provider.

    	**Nota sulla sicurezza**

    	Il segreto generato dal provider è una credenziale di sicurezza importante. Non condividere questo valore con altri né distribuirlo con l'app.

1.  Tornare al portale di gestione, fare clic sulla scheda **Identity**, immettere l'identificatore dell'app e il segreto condiviso forniti dal provider di identità e fare clic su **Save**.

     ![](./media/mobile-services-html-get-started-users/mobile-identity-tab.png)

Il servizio mobile e l'app sono ora configurati per funzionare con il provider di autenticazione scelto.

Limitazione delle autorizzazioniLimitazione delle autorizzazioni agli utenti autenticati
----------------------------------------------------------------------------------------

1.  Nel portale di gestione fare clic sulla scheda **Data** e quindi sulla tabella **TodoItem**.

     ![](./media/mobile-services-html-get-started-users/mobile-portal-data-tables.png)

2.  Fare clic sulla scheda **Permissions**, impostare tutte le autorizzazioni su **Only authenticated users** e quindi fare clic su **Save**. In questo modo, tutte le operazioni eseguite sulla tabella **TodoItem** richiederanno un utente autenticato. Questa impostazione semplifica inoltre gli script dell'esercitazione successiva, in quanto non sarà necessario considerare la possibilità di accesso da parte di utenti anonimi.

     ![](./media/mobile-services-html-get-started-users/mobile-portal-change-table-perms.png)

3.  Nella directory dell'app avviare uno dei file di comando seguenti dalla sottocartella **server**.

    -   **launch-windows** (computer Windows)
    -   **launch-mac.command** (computer Mac OS X)
    -   **launch-linux.sh** (computer Linux)

    **Nota**

    In un computer Windows, digitare 'R' quando PowerShell chiede di confermare che si desidera eseguire lo script. Il Web browser potrebbe visualizzare un avviso in cui si consiglia di non eseguire lo script in quanto scaricato da Internet. In questo caso, è necessario richiedere che il browser proceda nel caricamento dello script.

    Verrà quindi avviato un server Web nel computer locale per ospitare la nuova app.

4.  Aprire l'URL <http://localhost:8000/> in un Web browser per avviare l'app.

    Il caricamento dei dati ha esito negativo. L'eccezione non gestita viene generata perché l'app tenta di accedere a Servizi mobili come utente non autenticato, mentre la tabella *TodoItem* richiede ora l'autenticazione.

5.  (Facoltativo) Aprire il debugger di script per il Web browser in uso e ricaricare la pagina. Verificare che venga restituito un errore di accesso negato.

A questo punto, si aggiornerà l'app in modo da consentire l'autenticazione prima di richiedere risorse al servizio mobile.

Aggiunta dell'autenticazioneAggiunta dell'autenticazione all'app
----------------------------------------------------------------

**Nota**

Poiché l'accesso viene eseguito in una finestra popup, richiamare il metodo **login** da un evento clic del pulsante. In caso contrario, in molti browser non sarà visualizzata la finestra di accesso.

1.  Aprire il file di progetto, individuare l'elemento H1 e sotto di esso aggiungere il frammento di codice seguente:

         <div id="logged-in">
             You are logged in as <span id="login-name"></span>.
             <button id="log-out">Log out</button>
         </div>
         <div id="logged-out">
             You are not logged in.
             <button>Log in</button>
         </div>

    Sarà quindi possibile accedere a Servizi mobili dalla pagina.

2.  Nel file app.js individuare la riga di codice alla fine del file in cui viene chiamata la funzione refreshTodoItems e sostituirla con il codice seguente:

         function refreshAuthDisplay() {
             var isLoggedIn = client.currentUser !== null;
             $("#logged-in").toggle(isLoggedIn);
             $("#logged-out").toggle(!isLoggedIn);

             if (isLoggedIn) {
                 $("#login-name").text(client.currentUser.userId);
                 refreshTodoItems();
             }
         }

         function logIn() {
             client.login("facebook").then(refreshAuthDisplay, function(error){
                 alert(error);
             });
         }

         function logOut() {
             client.logout();
             refreshAuthDisplay();
             $('#summary').html('<strong>You must login to access data.</strong>');
         }

         // On page init, fetch the data and set up event handlers
         $(function () {
             refreshAuthDisplay();
             $('#summary').html('<strong>You must login to access data.</strong>');           
             $("#logged-out button").click(logIn);
             $("#logged-in button").click(logOut);
         });

    In questo modo viene creato set di funzioni per gestire il processo di autenticazione. L'utente viene autenticato tramite un account di accesso di Facebook.

    **Nota**

    Se si utilizza un provider di identità diverso da Facebook, sostituire il valore passato al metodo **login** riportato in precedenza con uno dei seguenti: *microsoftaccount*, *facebook*, *twitter* o *google*.

3.  Tornare al browser in cui è in esecuzione l'app e aggiornare la pagina.

    Dopo avere eseguito l'accesso, l'app dovrebbe funzionare senza errori e dovrebbe essere possibile eseguire query in Servizi mobili e aggiornare i dati.

    **Nota**

    Se si utilizza Internet Explorer, dopo l'accesso potrebbe essere visualizzato un errore che indica che `non è possibile accedere all'oggetto opener della finestra, in quanto potrebbe trovarsi in un'area di Internet Explorer diversa`. Questo errore si verifica perché la finestra popup viene eseguita in un'area di sicurezza (Internet) diversa da localhost (Intranet). Questo errore interessa le app solo durante lo sviluppo tramite localhost. Per risolvere il problema, in **Opzioni Internet** aprire la scheda **Sicurezza**, fare clic su **Intranet locale**, quindi su **Siti** e disabilitare **Rileva automaticamente rete Intranet**. Ricordare di tornare a modificare questa opzione al termine del test.

Passaggi successivi
-------------------

Nella prossima esercitazione, [Autorizzazione di utenti con script](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-html), il valore dell'ID utente fornito da Servizi mobili e basato su un utente autenticato verrà utilizzato per filtrare i dati restituiti da Servizi mobili. Per ulteriori informazioni su come utilizzare Servizi mobili con HTML/JavaScript, vedere [Riferimento per i concetti e le procedure di Servizi mobili con HTML/JavaScript](/en-us/develop/mobile/how-to-guides/work-with-html-js-client)

